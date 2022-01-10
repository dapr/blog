---
date: "2021-12-26T07:00:00-07:00"
title: "Enable Dapr with Apache APISIX Ingress Controller"
linkTitle: "Dapr case study: Apache APISIX"
author: "Shanyou Zhang, Yilin Zeng"
type: blog
---

## Using Apache APISIX Ingress Controller with Dapr

In this article, we will discuss the benefits of using [Apache APISIX Ingress Controller](https://github.com/apache/apisix-ingress-controller) with Dapr and how you can apply this to your applications running in a Kubernetes cluster. 

Ingress is a resource that represents the entry point for traffic in a Kubernetes environment. In order to make ingress effective, you configure an ingress controller to listen to the ingress resources in Kubernetes, parse the rules for those resources, and carry the traffic to the application. Nginx is the most commonly used ingress controller, however there are other options to consider for ingress implementation, such as APISIX Ingress. It is worth noting at this point, that Dapr is not designed to be a publicly facing ingress and should always be used behind an ingress controller or API gateway.

The APISIX Kubernetes Ingress is an implementation of an ingress controller. The main difference from Nginx Kubernetes Ingress is that APISIX ingress uses Apache APISIX as the data plane for carrying business traffic. As shown in the diagram below, when a user requests a specific service, API or web page, the business traffic/user request is transferred to the Kubernetes cluster through an external proxy and then is processed by APISIX Ingress.

{{< imgproc apache_apisix_explanation.png  Resize "1200x" />}}

The APISIX Kubernetes Ingress is divided into two parts. The first is the APISIX Ingress Controller, which serves as the control plane for configuration management and distribution. The second is the APISIX Proxy Pod, which is responsible for carrying business traffic and is implemented as a Kubernetes CRD (Custom Resource Definitions). Apache APISIX Ingress supports not only custom resources but also native Kubernetes Ingress resources.

### Why integrate Dapr and Apache APISIX Ingress Controller

Today, we are experiencing a wave of development of cloud applications and microservices. Although developers are familiar with 'web + database' application architectures (i.e. classic 3-tier designs), some developers are not familiar with microservice application architectures. This is a pain point when integrating multiple microservices to build a cloud application. The developers' goal is to focus on business logic, while the platform is responsible for adding scalability, elasticity, maintainability, resiliency, and other attributes of cloud-native architectures to the application.This is where Dapr and Apache APISIX Ingress Controller provide value. 

Dapr supports multiple programming languages and platforms. It codifies best practices for building microservice applications into open, independent building blocks, which allows users to build portable, resilient and secure applications using the language and framework of their choice. Since each building block is completely independent, developers can decide to use one or multiple building blocks in an application. Imagine that building a cloud-native application is similar to building Lego, you decide how and what you are going to build and each block fits perfectly and hassle-free. In addition, Dapr supports multiple cloud providers and it is not tied to any specific vendor. This means users can run applications in any Kubernetes cluster and other hosting environments that are integrated with Dapr.

In order to maximize the advantages of Dapr, the best choice is to integrate it with an ingress that is highly customizable, high-performance, and hassle-free. After some research at Weyhd, we landed on the Apache APISIX Ingress Controller.

The Apache APISIX Ingress Controller uses APISIX as its data plane, and has been proven to have better performance than both Kong and Nginx. APISIX has an active community for problem-solving, bi-weekly webinars, and new version releases each month with enhancements and bug-fixes. The Apache APISIX Ingress Controller also supports multiple programming languages in the data plane, and it is not vendor-specific. Lastly the Apache APISIX Ingress supports not only custom resources but also native Kubernetes Ingress resources.

## Project using Apache APISIX Ingress Controller with Dapr

### Project Overview

[Weyhd](https://www.weyhd.com/) is a solution provider focused on cloud-native application development and services. Weyhd provides .NET and cloud-native technologies to build modern software with high productivity. We believe that in today's world, all companies will eventually be a software company as software makes our lives easier.

Weyhd worked on a project with China Merchants International Technology Co., Ltd. (CMIT) to migrate their Container Terminal Operating System (CTOS) to the cloud using Apache APISIX Ingress Controller combined with Dapr.

CMIT was established in 2001 and is a high-tech enterprise specializing in digital construction for maritime logistics.The company is headquartered in Shenzhen, with subsidiaries in Dalian and Yingkou, and is the construction and operation enterprise of the "Traffic Electronic Port" sub-center of the Ministry of Communications and Dalian Port Public Information Platform. After more than 20 years of development and accumulation, the company's business has now spread to Hong Kong, Shenzhen, Ningbo, Qingdao, Dalian, Yingkou, Zhangzhou, Zhanjiang-Shantou and other coastal hub ports and regional ports and terminals in the Pearl River Delta, Bohai Bay and Yangtze River coast. 

We decided to integrate Apache APISIX Ingress Controller and Dapr in the CMIT production environment running on a Kubernetes cluster. Due to the cloud architecture, we are able to decouple the CTOS application into different functional modules. By doing so, we increased the efficiency of the system and decreased the difficulty of maintaining the system.

Dapr is a core part of the microservice-based CTOS Platform, hosted on Rancher Kubernetes Service with a RabbitMQ Cluster for Pub/Sub. An aggregator acts as an intermediary/orchestrator between the microservices. All services sit behind an APISIX API Gateway which itself is a part of the Daprd service enabling us to use TLS or mTLS to the edge and mTLS throughout the Kubernetes cluster.

The following diagram shows the architectural flow of the actual project:

{{< imgproc apache_apisix_gateway_controller.png  Resize "1200x" />}}

You may notice the architecture is a bit complex, because we are connecting with various external services to achieve different goals such as authentication, observability, logging, and dashboard for monitoring. Let's take a request example: 

When a request comes in, it first goes through the load balancer. It then enters the Apache APISIX Ingress Controller, which decides how to send the request to a service efficiently. Apache APISIX Ingress Controller configures the same standard Dapr annotations to inject DAPRD sidecar for each service in the cluster. Once the decision is made, the request is sent to a service, and the sidecar deployed by Dapr handles communication with external services. By exposing the sidecars, external services such as Minio, Kibana, and elasticsearch, are able to communicate with services inside the cluster. Thanks to the sidecar, we can have resource isolation and increased security level inside services and clusters. 

Let's move to the next steps to get some hands-on experience of building this integration!

## How to integrate Dapr with Apache APISIX Ingress Controller
Now let’s look at how to use Apache APISIX and Dapr together.

### Environment prerequisite

- Kubernetes 1.19+ cluster with Dapr already configured on the cluster
- Helm CLI 3x installed
- Kubect CLI installed and configured to access the cluster
- Optional: OpenSSL for creating self-signed certificates
- The Helm Chart version for Apache APISIX is 0.7.2+. Refer to: https://github.com/apache/apisix-helm-chart/issues/167 for the specific reason

#### Step 1: Apache APISIX Helm Configuration

Add the latest helm chart repo for the Apache APISIX Ingress Controller by running the following command.

```bash
helm repo add apisix https://charts.apiseven.com
helm repo update
```

#### Step 2: Create the Apache APISIX Ingress namespace

Ensure that the current kubectl context points to the correct Kubernetes cluster, and then run the following command.

```bash
kubectl create namespace ingress-apisix
```

#### Step 3: Install the APISIX Controller with Dapr Support

Use the following to create a file called dapr-annotations.yaml to set up annotations on the Apache APISIX Proxy Pod.

```yaml
apisix:
  podAnnotations:
    dapr.io/enabled: 'true'
    dapr.io/app-id: ' apisix-gateway'
dapr.io/app-port: '9080'
dapr.io/enable-metrics: 'true'
dapr.io/metrics-port: '9099'
dapr.io/sidecar-listen-addresses: 0.0.0.0
dapr.io/config: ingress-apisix-config
```

> Note: The app-port above is telling the daprd sidecar Proxy which port it is listening on. For a full list of supported annotations, see the [Dapr Kubernetes pod annotation](https://docs.dapr.io/reference/arguments-annotations-overview/) specification.

Here is a sample `dapr-annotations.yaml` from an example installation on AKS.

```yaml
apisix:
  podAnnotations:
    dapr.io/app-id: apisix-gateway
    dapr.io/app-port: '9080'
    dapr.io/enable-metrics: 'true'
    dapr.io/enabled: 'true'
    dapr.io/metrics-port: '9099'
dapr.io/sidecar-listen-addresses: 0.0.0.0
dapr.io/config: ingress-apisix-config
gateway:
  type: LoadBalancer
ingress-controller:
  enabled: true
dashboard:
  enabled: true
```

Next, run the following command (referencing the above file).

```bash
helm install apisix apisix/apisix -f dapr-annotations.yaml -n ingress-apisix
```

#### Step 4: Create the Dapr Sidecar resource for Apache APISIX

First, configure Apache APISIX upstream-apisix-dapr.

{{< imgproc create_dapr_sidecar_resource.png  Resize "1600x" />}}


Fill in the hostname `apisix-gateway-dapr` and the port number `3500`.

```json
{
 "nodes": [
   {
     "host": "apisix-gateway-dapr",
     "port": 3500,
     "weight": 1
   }
 ],
 "retries": 1,
 "timeout": {
   "connect": 6,
   "read": 6,
   "send": 6
 },
 "type": "roundrobin",
 "scheme": "http",
 "pass_host": "pass",
 "name": "apisix-dapr"
}
```

Then configure the Apache APISIX service `apisix-gateway-dapr` and select `apisix-dapr` for the upstream service.

{{< imgproc configure_apache_apisix.png  Resize "1600x" />}}

```json
{
 "name": "apisix-gateway-dapr",
 "upstream_id": "376187148778341098"
}
```

#### Step 5: Deploy the test sample project

[HTTPBin](https://httpbin.org/)  is a tool written in Python+Flask that covers various HTTP scenarios and returns to each interface. Next, we'll use kennethreitz/httpbin as a sample project for demonstration purposes.

```bash
kubectl apply -f 01.namespace.yaml
kubectl apply -f 02.deployment.yaml
kubectl apply -f 03.svc.yaml
```

{{< imgproc deploy_sample.png  Resize "810x" />}}

The image above shows a hypothetical microservice running with the Dapr app-id `kennethreitz-httpbin`.

#### Path Matching Rewrites
Here we add some settings related to path matching. For example, if the request gateway is `/httpbin/`, the backend receive path should be `/`, with `httpbin` acting as a service name identifier.

{{< imgproc configuration_required_fields.png  Resize "810x" />}}

On hosted platforms that support namespaces, the Dapr application ID is in a valid FQDN format, which includes the target namespace. For example, the following string contains the application ID (`svc-kennethreitz-httpbin`) and the namespace the application is running in (`kind-test`).

Finally, you can see if the proxy was successful by visiting: [http://20.195.90.43/httpbin/get](http://20.195.90.43/httpbin/get).

{{< imgproc successful_proxy.png  Resize "1280x" />}}

#### Additional Notes

Of course, you can also deploy Apache APISIX and APISIX Ingress Controller directly in Kubernetes using the official Apache APISIX Helm repository, which allows you to directly use Apache APISIX as a gateway to the APISIX Ingress Controller data plane to carry business traffic.

Finally, Dapr is injected into the Apache APISIX Proxy Pod via Sidecar annotations, and the microservices in the cluster are invoked through the service invocation module to achieve complete process deployment.

#### Deleting Apache APISIX Ingress Controller

If you want to delete the Apache APISIX Ingress Controller at the end of the project, you can follow the command below (remember to delete the ingress-apisix namespace as well).

```bash
helm delete apisix -n ingress-apisix
```

### Summary

Infrastructure is an important part of cloud services. When building a cloud application, the most crucial factor is the flexibilities of application migration from one cloud vendor to another.

Since Dapr and Apache APISIX Ingress Controller are designed to support multiple cloud infrastructures, and they are both not vendor-specific, they are suitable for an integration naturally. In addition, the active communities of both Dapr and Apache APISIX Ingress Controller also give us confidence to use them in production. While other sorts of integration is a '1+1=2' type of thing, the integration of Dapr and Apache APISIX Ingress Controller is definitely a '1+1 >> 2' type of thing. We are delighted to find such matching products in the open-source market, and we will not only use them, but also consider contributing to both projects in the future. Open source makes the world better!

### Reference
- https://apisix.apache.org/blog/2021/11/17/dapr-with-apisix/ published on 11/17/2021
- https://mp.weixin.qq.com/s/c3SokOw-n4l-Q_kOiYykSw published on 11/16/2021
