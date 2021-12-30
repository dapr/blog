---
date: "2021-12-26T07:00:00-07:00"
title: "Enable Dapr with Apache APISIX Ingress Controller"
linkTitle: "Dapr case study: Apache APISIX"
author: "Shanyou Zhang, Yilin Zeng"
type: blog
---

## About Apache APISIX Ingress Controller

Ingress is a resource that represents the entry point for traffic in a Kubernetes environment. In order to make Ingress effective, we need to configure an Ingress Controller to listen to the Ingress resources in Kubernetes, parse the rules for those resources, and carry the traffic. Although Kubernetes Ingress Nginx is most commonly used, there are other options to consider for implementing ingress, such as APISIX Ingress.

APISIX Ingress is an implementation of the Ingress Controller and the main difference from Kubernetes Ingress Nginx is that APISIX Ingress uses Apache APISIX as the data plane for carrying business traffic. As shown in the figure below, when a user requests a specific service, API or web page, the entire business traffic/user request is transferred to the Kubernetes cluster through an external proxy and then is processed by APISIX Ingress.

{{< imgproc apache_apisix_explanation.png  Resize "1200x" />}}

APISIX Ingress is divided into two parts. The first is the APISIX Ingress Controller, which serves as the control plane for configuration management and distribution. The second is the APISIX Proxy Pod, which is responsible for carrying business traffic and is implemented through CRDs (Custom Resource Definitions). Apache APISIX Ingress supports not only custom resources but also native Kubernetes Ingress resources.

## Why integrate Dapr and Apache APISIX Ingress Controller

Today, we are experiencing a wave of cloud applications and microservices. Although developers are familiar with 'web + database' application architectures (e.g., classic 3-tier designs), some developers are not familiar with a microservice application architecture. This could be a headache when integrating multiple microservices when building a cloud application. Developers' goal is to focus on business logic, while the platform is responsible for adding scalability, elasticity, maintainability, resiliency, and other attributes of cloud-native architectures to the application. This is where Dapr and Apache APISIX Ingress Controller provide value.

Dapr supports multiple programming languages and platforms. It codifies best practices for building microservice applications into open, independent building blocks, which allows users to build portable, resilient and secure applications using the language and framework of their choice. Since each building block is completely independent, developers can decide to use one building block or multiple building blocks in an application. Imagine that building a cloud-native application is similar to building Legos - you decide how and what you are going build, and each block fits perfectly and hassle-free. In addition, Dapr supports multiple cloud providers and is not tied to any specific vendor. This means users can run applications in any Kubernetes cluster and other hosting environments that are integrated with Dapr.

In order to maximize the advantages of Dapr, the best choice is to integrate it with something that is also highly customizable, high-performance, and hassle-free. After some research, we find Apache APISIX Ingress Controller. 

Apache APISIX Ingress Controller uses APISIX as its data plane, which has been proven to have better performance than Kong and Nginx. Additionally, APISIX also has an active community for problem-solving, bi-weekly webinar, and new version releases each month with enhancements and bug-fixes. Apache APISIX Ingress Controller also supports multiple programming languages in the data plane and is not vendor specific. In addition, Apache APISIX Ingress supports not only custom resources but also native Kubernetes Ingress resources. In this article, we will discuss the benefits of using Apache APISIX Ingress Controller with Dapr and how you can apply this to your applications today. 

## How to integrate Dapr with Apache APISIX Ingress Controller

### Overview

[weyhd](https://www.weyhd.com/) is a solution provider focused on cloud-native application development and services. We believe that in today's world, any company will eventually become a software company. Software makes our lives easier. Today, the process of building software has become complex and unwieldy, and the day-to-day work of most software development teams is inefficient. We at wehyd provide .NET and cloud-native technologies to help build modern software with high productivity.

In the project using Apache APISIX and Dapr, we migrate the Container Terminal Operating System(CTOS) of [China Merchants International Technology Co., Ltd.(CMIT)](https://www.cmit1872.com/en-us/about-us/company-profile-2) to the cloud.

CMIT was established in 2001 and is a high-tech enterprise specializing in digital construction of maritime logistics.The company is headquartered in Shenzhen, with subsidiaries in Dalian and Yingkou, and is the construction and operation enterprise of the "Traffic Electronic Port" sub-center of the Ministry of Communications and Dalian Port Public Information Platform. After more than 20 years of development and accumulation, the company's business has now spread to Hong Kong, Shenzhen, Ningbo, Qingdao, Dalian, Yingkou, Zhangzhou, Zhanjiang-Shantou and other coastal hub ports and regional ports and terminals in the Pearl River Delta, Bohai Bay and Yangtze River coast, and has successfully laid out the Belt and Road ports and South Asia, Africa, Europe Mediterranean and South America.

We integrate Apache APISIX Ingress Controller and Dapr in our production environment. It enables Dapr applications in the Kubernetes cluster. 
Thanks to the cloud architecture, we are able to decouple the whole CTOS  into different functional modules. By doing so, we increase the efficiency of the system and decrease the difficulty of maintaining the system.

Dapr is a core part of our microservice-based CTOS Platform, hosted on Rancher Kubernetes Service with a RabbitMQ Cluster for Pub/Sub. An aggregator acts as an intermediary/orchestrator between the microservices. All services sit behind an APISIX API Gateway which itself is a part of the Daprd service enabling us to use TLS or mTLS to the edge and mTLS throughout the Kubernetes cluster.

We have integrated Apache APISIX Ingress Controller and Dapr in our production environment. It enables the use of Dapr applications in the Kubernetes cluster. The following diagram shows the architectural flow of the actual project:

{{< imgproc apache_apisix_gateway_controller.png  Resize "1200x" />}}

You may notice the architecture is a bit complex because we are connecting with various external services to achieve different goals, such as authentication, observability, logging, and dashboard for monitoring. Let's take a request for example: 

When a request comes in, it first goes through the load balancer. It then enters Apache APISIX Ingress Controller, which decides how to send the request to a service efficiently. Apache APISIX Ingress Controller uses the same standard Dapr annotations to inject DAPRD sidecar for each service in the cluster. Once the decision is made, the request is sent to a service, and the sidecar deployed by Dapr handles the communication with external services. By exposing the sidecars, external services such as Minio, Kibana, and elasticsearch, are able to communicate with services inside the cluster. Thanks to the sidecar, we can have resource isolation and increased security level inside services and clusters. 

Let's move to the next steps and get hands-on experience of building this integration!

### Environment preparation

Kubernetes 1.19+ cluster with Dapr already configured 
Helm CLI 3x installed
Kubectl CLI installed and configured to access the cluster
Optional: OpenSSL for creating self-signed certificates
The Helm Chart version for Apache APISIX is 0.7.2+. Refer to: https://github.com/apache/apisix-helm-chart/issues/167 for the specific reason


#### Step 1: Apache APISIX Helm Configuration

Add the latest helm chart repo for the Apache APISIX Ingress Controller by running the following command:

```bash
$ helm repo add apisix https://charts.apiseven.com
$ helm repo update
```

#### Step 2: Create the Apache APISIX Ingerss namespace

Ensure that the current kubectl context points to the correct Kubernetes cluster, and then run the following command to create the ingres-apisix namespace:

```bash
kubectl create namespace ingress-apisix
```

#### Step 3: Install the APISIX Controller with Dapr Support

Use the following YAML to create a file called dapr-annotations.yaml, which will apply the annotations on the Apache APISIX Proxy Pod.

```yaml
apisix:
 podAnnotations:
   dapr.io/enabled: "true"
   dapr.io/app-id: " apisix-gateway"
dapr.io/app-port: "9080"
dapr.io/enable-metrics: "true"
dapr.io/metrics-port: "9099"
dapr.io/sidecar-listen-addresses: 0.0.0.0
dapr.io/config: ingress-apisix-config
```

> Note: The app-port above is telling the daprd sidecar proxy which port it is listening on. For a full list of supported annotations, see the [Dapr Kubernetes pod annotation](https://docs.dapr.io/reference/arguments-annotations-overview/) specification.

Below is a sample dapr-annotations.yaml from an example installation on AKS.

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

HTTPBin is a tool written in Python + Flask that covers various HTTP scenarios and returns to each interface. Next, we'll use kennethreitz/httpbin as a sample project for demonstration purposes.

```bash
kubectl apply -f 01.namespace.yaml
kubectl apply -f 02.deployment.yaml
kubectl apply -f 03.svc.yaml
```

{{< imgproc deploy_sample.png  Resize "810x" />}}

The image above shows a hypothetical microservice running with the Dapr app-id `kennethreitz-httpbin`.

#### Path Matching Rewrites
We will add some settings related to path matching. For example, if the request gateway is `/httpbin/`, the backend receive path should be `/`, with `httpbin` acting as a service name identifier.

{{< imgproc configuration_required_fields.png  Resize "810x" />}}

On hosted platforms that support namespaces, the Dapr application ID is in a valid FQDN format, which includes the target namespace. For example, the following string contains the application ID (`svc-kennethreitz-httpbin`) and the namespace the application is running in (`kind-test`).

Finally, you can see if the proxy was successful by visiting: [http://20.195.90.43/httpbin/get](http://20.195.90.43/httpbin/get).

{{< imgproc successful_proxy.png  Resize "1280x" />}}

#### Additional Notes

Of course, you can also deploy Apache APISIX and APISIX Ingress Controller directly in Kubernetes using the official Apache APISIX Helm repository, which allows you to directly use Apache APISIX as a gateway to the APISIX Ingress Controller data plane to carry business traffic. This allows you to directly use Apache APISIX as a gateway to carry business traffic on the data plane of the APISIX Ingress Controller.

Finally, Dapr is injected into the Apache APISIX Proxy Pod via Sidecar annotations, and the microservices in the cluster are invoked through the service invocation module to achieve complete process deployment.

#### Deleting Apache APISIX Ingress Controller

If you want to delete the Apache APISIX Ingress Controller at the end of the project, you can follow the command below (remember to delete the namespace `ingress-apisix` created before).

```bash
helm delete apisix -n ingress-apisix
```

### Summary

Infrastructure is an important part of cloud services. When building a cloud application, the most crucial factor is flexibility for application migration from one cloud vendor to another.

Since Dapr and Apache APISIX Ingress Controller are designed to support multiple cloud infrastructures and neither are vendor-locked, they are a natural fit for integration. In addition, the active communities of both Dapr and Apache APISIX Ingress Controller give us confidence to use it in our production environment. We are delighted to find such matching products in the open-source market, and we will not only use them, but also consider contributing to both projects in the future. Open source makes the world better!

### Reference
- https://apisix.apache.org/blog/2021/11/17/dapr-with-apisix/, published on 11/17/2021
- https://mp.weixin.qq.com/s/c3SokOw-n4l-Q_kOiYykSw, published on 11/16/2021
