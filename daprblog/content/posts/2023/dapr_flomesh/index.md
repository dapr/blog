---
date: "2023-02-23T04:00:00+08:00"
title: "Achieving Multi-cluster Dapr support with Flomesh Service Mesh"
linkTitle: "Achieving Multi-cluster Dapr support with Flomesh Service Mesh"
author:  "Addo Zhang - Flomesh"
type: blog
---

How combining Dapr with [Flomesh](https://flomesh.io) Service Mesh brings Dapr functionality to multi-cluster services. 

## Background

The continuous evolution of technology and architecture is trending towards multiple runtimes. The basic capabilities of modern applications are consistently separated from the application as independent runtimes. Among them are distributed application runtimes and service meshes. This article introduces the integration of [Dapr](https://dapr.io/) and [Flomesh](https://flomesh.io) Service Mesh to achieve "true" cross-cluster interconnectivity for service invocation.

### Multi-Cluster

Kubernetes adheres to a design philosophy of loose coupling and scalability, which has brought about the flourishing development of the Kubernetes ecosystem. However, most of these are limited to a single cluster, and more and more clusters are being created within enterprises for various reasons and purposes, such as single-cluster failures, regulatory requirements, cross-data center disaster recovery for multiple regions, hybrid clouds for agility and cost reduction, multi-cloud deployment, limited capacity of a single cluster, coexistence of multiple versions of Kubernetes clusters, etc.


### Dapr

[Dapr](https://dapr.io) is a distributed application toolkit that decouples applications and peripheral functional components by providing simple and stable APIs. This enables developers to focus on business functionality development. By decoupling from peripheral components, applications become more portable and cloud-native. Enterprises can easily migrate applications to different environments at a low cost.

Dapr provides rich features such as service invocation, resilience policies, state storage, publish/subscribe, binding, distributed locking, name resolution, etc. However, it does not support advanced service governance functions, such as canary release and cross-cluster service invocation.

### Flomesh Service Mesh

With the rise of microservices architecture, as the scale grows larger, the difficulty and fragmentation of service governance has significantly increased. The emergence of service meshes solves these problems. A service mesh is a dedicated infrastructure layer for handling communication between services. Through it, functionality such as observability, traffic management, and security can be added transparently without adding it to your code.

[Flomesh](https://flomesh.io) service mesh uses the programmable proxy [Pipy](https://github.com/flomesh-io/pipy) as the core to provide east-west and north-south traffic management. Through L7-based traffic management capabilities, it breaks through the network isolation between computing environments, proposes a virtual flat network, and enables communication between applications in different computing environments. Flomesh service mesh can be imagined as a "big mesh" that covers multiple clusters.

{{< imgproc flomesh_dapr_blog_arch.png  Resize "800x" />}}

## Demo 

The diagram below demonstrates how Dapr and Flomesh service mesh can be integrated to enable cross-cluster service communication. The example consists of two components: a server-side NodeApp and a client-side curl.

The server-side NodeApp is a Dapr application, modified from the NodeApp in the [Dapr hello-kubernetes](https://github.com/dapr/quickstarts/tree/master/tutorials/hello-kubernetes) tutorial example. When it returns a response, it displays the name of the current cluster.

The client-side curl is used to send requests to the NodeApp, but is not declared as a Dapr application. This demonstrates how Flomesh service mesh can enable communication between a Dapr application and a non-Dapr application, facilitating cross-cluster communication.


{{< imgproc flomesh_dapr_blog_demo_arch.png  Resize "1200x" />}}


The NodeApp in the example has three endpoints:

- `GET /ports`: returns the ports that the application can be accessed on
- `POST /neworder`: creates a new order
- `GET /order`: queries an order
  
Follow along with the demo to:
1. Create multiple clusters. 
1. Configure the environment.
1. Install and configure various components.
1. Deploy the application. 

### Before you begin


#### One-click installation script

For this demo, we provide a quick, one-click installation script to avoid the tedious configuration of the environment and components. [Get the script content from GitHub](https://github.com/addozhang/flomesh-dapr-demo).

Before using the one-click script, install `Docker` and `kubectl`. The script will check during runtime and install tools such as `k3d`, `helm`, `jq`, `pv`, etc. Commands include:

- `flomesh.sh` - provides no parameters, the script will create 4 clusters, complete the environment installation and configuration, and run the demo
- `flomesh.sh -h` - prints help information
- `flomesh.sh -i` - creates 4 clusters, completes environment installation and configuration
- `flomesh.sh -d` - runs the demo
- `flomesh.sh -r` - cleans up demo-related resources
- `flomesh.sh -u` - deletes all clusters
- 
After downloading the script, execute the following command to install and configure the environment and run the demo.

```shell
curl -sL https://raw.githubusercontent.com/addozhang/flomesh-dapr-demo/main/flomesh.sh | bash -
```

#### Prerequisites

To perform the demonstration, you need the following tools:

- Docker
- Kubectl
- K3d
- Helm
- kubectx


### Create multiple clusters

Obtain the IP address of the local machine as the communication address between clusters.

```shell
export HOST_IP=10.0.0.13
```

To create four clusters named `control-plane`, `cluster-1`, `cluster-2`, and `cluster-3`, execute the following command:

```shell
API_PORT=6444 #6444 6445 6446 6447
PORT=80 #81 82 83
for CLUSTER_NAME in control-plane cluster-1 cluster-2 cluster-3
do
  k3d cluster create ${CLUSTER_NAME} \
    --image docker.io/rancher/k3s:v1.23.8-k3s2 \
    --api-port "${HOST_IP}:${API_PORT}" \
    --port "${PORT}:80@server:0" \
    --servers-memory 4g \
    --k3s-arg "--disable=traefik@server:0" \
    --network multi-clusters \
    --timeout 120s \
    --wait
    ((API_PORT=API_PORT+1))
    ((PORT=PORT+1))
done
```

The above command:
- Creates four `k3d` clusters 
- Installs and configures the necessary components for the Flomesh service mesh on each cluster

### Install Flomesh service mesh

Install Flomesh with the following command:

```shell
helm repo add fsm https://charts.flomesh.io
helm repo update
export FSM_NAMESPACE=flomesh
export FSM_VERSION=0.2.1-alpha.3
for CLUSTER_NAME in control-plane cluster-1 cluster-2 cluster-3
do 
  kubectx k3d-${CLUSTER_NAME}
  sleep 1
  helm install --namespace ${FSM_NAMESPACE} --create-namespace --version=${FSM_VERSION} --set fsm.logLevel=5 fsm fsm/fsm
  sleep 1
  kubectl wait --for=condition=ready pod --all -n $FSM_NAMESPACE --timeout=120s
done
```

To join the clusters `cluster-1`, `cluster-2`, and `cluster-3` to the `control-plane` cluster, run the following command:


```shell
kubectx k3d-control-plane
sleep 1
PORT=81
for CLUSTER_NAME in cluster-1 cluster-2 cluster-3
do
  kubectl apply -f - <<EOF
apiVersion: flomesh.io/v1alpha1
kind: Cluster
metadata:
  name: ${CLUSTER_NAME}
spec:
  gatewayHost: ${HOST_IP}
  gatewayPort: ${PORT}
  kubeconfig: |+
`k3d kubeconfig get ${CLUSTER_NAME} | sed 's|^|    |g' | sed "s|0.0.0.0|$HOST_IP|g"`
EOF
((PORT=PORT+1))
done
```

### Install `osm-edge`

Download [osm-edge](https://github.com/flomesh-io/osm-edge) Service Mesh CLI by running below command:

```shell
system=$(uname -s | tr [:upper:] [:lower:])
arch=$(dpkg --print-architecture)
release=v1.3.1
curl -L https://github.com/flomesh-io/osm-edge/releases/download/$release/osm-edge-$release-$system-$arch.tar.gz | tar -vxzf -
./${system}-${arch}/osm version
cp ./${system}-${arch}/osm /usr/local/bin/
```

To install the service mesh osm-edge on the clusters `cluster-1`, `cluster-2`, and `cluster-3`, run the following command for each cluster:

```shell
export OSM_NAMESPACE=osm-system
export OSM_MESH_NAME=osm
for CLUSTER_NAME in cluster-1 cluster-2 cluster-3
do
  kubectx k3d-${CLUSTER_NAME}
  DNS_SVC_IP="$(kubectl get svc -n kube-system -l k8s-app=kube-dns -o jsonpath='{.items[0].spec.clusterIP}')"
osm install \
    --mesh-name "$OSM_MESH_NAME" \
    --osm-namespace "$OSM_NAMESPACE" \
    --set=osm.certificateProvider.kind=tresor \
    --set=osm.image.pullPolicy=Always \
    --set=osm.sidecarLogLevel=error \
    --set=osm.controllerLogLevel=warn \
    --timeout=900s \
    --set=osm.localDNSProxy.enable=true \
    --set=osm.localDNSProxy.primaryUpstreamDNSServerIPAddr="${DNS_SVC_IP}"
done
```

```shell
kubectl get svc -n default
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   43h
```

To allow the pods to access the apiserver directly without going through the sidecar:

```shell
for CLUSTER_NAME in cluster-1 cluster-2 cluster-3
do
  kubectx k3d-${CLUSTER_NAME}
  kubectl patch meshconfig osm-mesh-config -n $OSM_NAMESPACE -p '{"spec":{"traffic":{"outboundIPRangeExclusionList":["10.43.0.1/32"]}}}'  --type=merge
done
```

### Install Dapr

Invoke below command to install Dapr on `cluster-1`, `cluster-2`, and `cluster-3` :

```shell
for CLUSTER_NAME in cluster-1 cluster-2 cluster-3
do
  kubectx k3d-${CLUSTER_NAME}
  dapr init --kubernetes \
  --enable-mtls=false \
  --wait
done
```

Check the status of the components:

```shell
dapr status -k
  NAME                   NAMESPACE    HEALTHY  STATUS   REPLICAS  VERSION  AGE  CREATED
  dapr-placement-server  dapr-system  True     Running  1         1.9.6    2m   2023-02-09 10:36.51
  dapr-operator          dapr-system  True     Running  1         1.9.6    2m   2023-02-09 10:36.51
  dapr-dashboard         dapr-system  True     Running  1         0.11.0   2m   2023-02-09 10:36.51
  dapr-sentry            dapr-system  True     Running  1         1.9.6    2m   2023-02-09 10:36.51
  dapr-sidecar-injector  dapr-system  True     Running  1         1.9.6    2m   2023-02-09 10:36.51
```

Check all services and their respective ports:

```shell
kubectl get svc -n dapr-system
NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)              AGE
dapr-placement-server   ClusterIP   None            <none>        50005/TCP,8201/TCP   5h50m
dapr-sidecar-injector   ClusterIP   10.43.12.213    <none>        443/TCP              5h50m
dapr-webhook            ClusterIP   10.43.103.31    <none>        443/TCP              5h50m
dapr-dashboard          ClusterIP   10.43.172.156   <none>        8080/TCP             5h50m
dapr-api                ClusterIP   10.43.126.14    <none>        80/TCP               5h50m
dapr-sentry             ClusterIP   10.43.41.10     <none>        80/TCP               5h50m
```

To allow traffic to flow through the Dapr components and Redis ports, add the necessary traffic rules to the OSM data plane.

```shell
for CLUSTER_NAME in cluster-1 cluster-2 cluster-3
do
  kubectx k3d-${CLUSTER_NAME}
  kubectl patch meshconfig osm-mesh-config -n $OSM_NAMESPACE -p '{"spec":{"traffic":{"outboundPortExclusionList":[50005,8201,6379]}}}'  --type=merge
done
```

### Install Redis
Install Redis with the following YAML:

```yaml
version: '3'
services:
  redis:
    image: redis:latest
    container_name: redis
    ports:
      - 6379:6379
    volumes:
      - ./data:/data
    command: redis-server --appendonly yes --requirepass changeme
```

### Create namespace

Create and add a namespace to be monitored by osm-edge Service Mesh

```shell
export NAMESPACE=dapr-test
for CLUSTER_NAME in cluster-1 cluster-2 cluster-3
do
  kubectx k3d-${CLUSTER_NAME}
  kubectl create namespace $NAMESPACE
  osm namespace add $NAMESPACE
done
```

### Register Redis state store component

Run the following command to register the Redis state store component:

```shell
export NAMESPACE=dapr-test
for CLUSTER_NAME in cluster-1 cluster-2 cluster-3
do
  kubectx k3d-${CLUSTER_NAME}
  kubectl create secret generic redis -n $NAMESPACE --from-literal=redis-password=changeme
  kubectl apply -n $NAMESPACE -f - <<EOF
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: statestore
spec:
  type: state.redis
  version: v1
  metadata:
  - name: redisHost
    value: 10.0.0.13:6379
  - name: redisPassword
    secretKeyRef:
      name: redis
      key: redis-password
auth:
  secretStore: kubernetes
EOF
done
```

### Deploy the applications

Deploy the NodeApp application in the `httpbin` namespace (managed by the service mesh and injected with a Dapr sidecar) in `cluster-1` and `cluster-3`.

```shell
export NAMESPACE=dapr-test
for CLUSTER_NAME in cluster-1 cluster-2  cluster-3
do
  kubectx k3d-${CLUSTER_NAME}
  kubectl apply -n $NAMESPACE -f - <<EOF
kind: Service
apiVersion: v1
metadata:
  name: nodeapp
  labels:
    app: node
spec:
  selector:
    app: node
  ports:
  - protocol: TCP
    port: 3000
    targetPort: 3000

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nodeapp
  labels:
    app: node
spec:
  replicas: 1
  selector:
    matchLabels:
      app: node
  template:
    metadata:
      labels:
        app: node
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "nodeapp"
        dapr.io/app-port: "3000"
        dapr.io/enable-api-logging: "true"
    spec:
      containers:
      - name: node
        image: addozhang/dapr-nodeapp
        env:
        - name: APP_PORT
          value: "3000"
        - name: CLUSTER_NAME
          value: ${CLUSTER_NAME}
        ports:
        - containerPort: 3000
        imagePullPolicy: Always
EOF
done
```

Deploy the curl application in the `curl` namespace of the `cluster-2` cluster. This namespace is managed by the service mesh and the injected Dapr sidecar, allowing for seamless cross-cluster traffic routing.

```shell
export NAMESPACE=curl
kubectx k3d-cluster-2
kubectl create namespace ${NAMESPACE}
osm namespace add ${NAMESPACE}
kubectl apply -n ${NAMESPACE} -f - <<EOF
apiVersion: v1
kind: ServiceAccount
metadata:
  name: curl
---
apiVersion: v1
kind: Service
metadata:
  name: curl
  labels:
    app: curl
    service: curl
spec:
  ports:
    - name: http
      port: 80
  selector:
    app: curl
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: curl
spec:
  replicas: 1
  selector:
    matchLabels:
      app: curl
  template:
    metadata:
      labels:
        app: curl
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "curl"
        dapr.io/enable-api-logging: "true"
        dapr.io/log-level: "debug"
    spec:
      serviceAccountName: curl
      containers:
      - image: curlimages/curl
        imagePullPolicy: IfNotPresent
        name: curl
        command: ["sleep", "365d"]
EOF

sleep 3
kubectl wait --for=condition=ready pod -n ${NAMESPACE} --all --timeout=60s
```

### Exporting Services

```shell
export NAMESPACE=dapr-test
for CLUSTER_NAME in cluster-1 cluster-3
do
  kubectx k3d-${CLUSTER_NAME}
  kubectl apply -n $NAMESPACE -f - <<EOF
apiVersion: flomesh.io/v1alpha1
kind: ServiceExport
metadata:
  name: nodeapp
spec:
  serviceAccountName: '*'
  pathRewrite:
    from: '^/nodeapp/?'
    to: '/'
  rules:
    - portNumber: 3000
      path: '/nodeapp'
      pathType: Prefix
EOF
sleep 1
done
```

After exporting a service, FSM will automatically create Ingress rules for it. With these rules, the services can be accessed through the Ingress.

```shell
for CLUSTER_NAME_INDEX in 1 3
do
  CLUSTER_NAME=cluster-${CLUSTER_NAME_INDEX}
  ((PORT=80+CLUSTER_NAME_INDEX))
  kubectx k3d-${CLUSTER_NAME}
  echo "Getting service exported in cluster ${CLUSTER_NAME}"
  echo '-----------------------------------'
  kubectl get serviceexports.flomesh.io -A
  echo '-----------------------------------'
  curl -s "http://${HOST_IP}:${PORT}/ports"
  echo '-----------------------------------'
done
```

## Test

Switch to cluster `cluster-2` and make a request from the `curl` pod to test.

```shell
kubectx k3d-cluster-2
curl_client="$(kubectl get pod -n curl -l app=curl -o jsonpath='{.items[0].metadata.name}')"
```

An error occurred when sending a request to access the nodeapp because the nodeapp application was not deployed in `cluster-2`. By default, when no cross-cluster traffic policy is specified, it will only attempt to call local services and will not schedule traffic to other clusters.

```shell
kubectl exec "${curl_client}" -n curl -c curl -- curl -s http://nodeapp.dapr-test:3000/ports
command terminated with exit code 7
```

### Setting Traffic Policies

There are three types of traffic policies for cross-cluster traffic management:

- `Locality`: Only use local services, which is the default type. This is why accessing the nodeapp application fails when we don't provide any global policies, because the service is not available in the `cluster-2`.
- `FailOver`: Only proxy to other clusters when the local cluster fails. Similar to the concept of active-passive failover.
- `ActiveActive`: Proxy to other clusters normally, even when the local cluster is up and running. Similar to active-active.


Currently, `cluster-2` defaults to the `Locality` traffic policy type. Create and apply an `ActiveActive` policy in the `cluster-2` cluster:

```shell
kubectl apply -n dapr-test -f - <<EOF
apiVersion: flomesh.io/v1alpha1
kind: GlobalTrafficPolicy
metadata:
  name: nodeapp
spec:
  lbType: ActiveActive
  targets:
    - clusterKey: default/default/default/cluster-1
      weight: 100
    - clusterKey: default/default/default/cluster-3
      weight: 100
EOF
```

### Test again

Sending the request again now results in a successful response:

```shell
kubectl exec "${curl_client}" -n curl -c curl -- curl -s http://nodeapp.dapr-test:3000/ports
{"DAPR_HTTP_PORT":"3500","DAPR_GRPC_PORT":"50001"}, from cluster: cluster-3
```

This time, try to send a request to `/neworder` to attempt to write order data.

```shell
kubectl exec "${curl_client}" -n curl -c curl -- curl -si --request POST --data '{"data":{"orderId":"42"}}' --header Content-Type:application/json --header dapr-app-id:nodeapp http://nodeapp.dapr-test:3000/neworder
created order via cluster: cluster-1
```

Multiple requests to `/order` can be made, and it can be observed that the requests are forwarded to different clusters for processing.

```shell
kubectl exec "${curl_client}" -n curl -c curl -- curl -s http://nodeapp.dapr-test:3000/order
{"orderId":"42"}, from cluster: cluster-3
kubectl exec "${curl_client}" -n curl -c curl -- curl -s http://nodeapp.dapr-test:3000/order
{"orderId":"42"}, from cluster: cluster-1
```





