---
date: "2021-09-02T07:00:00-07:00"
title: "How Dapr helped AutoNavi build a new serverless solution"
linkTitle: "Dapr case study: AutoNavi"
author: "Xuexiang Deng, Staff Engineer, AutoNavi Information Ltd."
type: blog
---

{{< imgproc autonavi-logo.png  Resize "200x" />}}

AutoNavi is a leading provider of digital map and navigation services in China with over 100 million daily active users. AutoNavi started it's Serverless/FaaS (Function as a service) project on April 2020 and after just one year, our solution already exceeds 100,000 queries per second (QPS). In this blog I will share how we at AutoNavi use Dapr to implement our serverless solution.

Below are a few of the business use cases that our solution addresses:
- **Weather in a long route -** When the route distance exceeds a threshold, for example 100kms, weather information is provided along the route.
- **Search along route -** Search for vehicle services such as gas station, electric vehicle charge-station etc. along the route.
- **Route tips -** Show driver relevant route information such as warnings that large vehicles are ahead, narrow roads etc.
- **Scenic spots information -** Show ticket prices, opening hours, contact information and brief descriptions for scenic spots and points of interest.

{{< imgproc autonavi-usercase.png Resize "1500x" >}}{{< /imgproc >}}

## Why we chose Dapr

While building the new service, we encountered several challenges including two that we felt Dapr could help us address: Connecting existing backend services using a lightweight solution and our requirement for a runtime which supports multiple languages.

### Connecting existing backend services

An important requirement we have is that our FaaS must have the ability to invoke existing backend micro-services which were developed on top of our RPC framework. A direct approach would be to use our RPC framework SDKs in our FaaS applications, but another requirement we have is keeping the FaaS runtime lightweight as to meet our fast start-up and scaling needs. We also wanted to avoid having the application become bloated due to a large number of SDKs that were integrated into the code base. 

This is where Dapr's lightweight footprint was very helpful. In addition, leveraging Dapr's APIs helped us avoid using any SDK libraries in our code. 

### Multi-language runtime

At AutoNavi, we mainly use C++ and Node.js on the client side. Base navigation functions such as mapping display, route display, sign guidance, spoken guidance etc. need to run both on mobile and vehicle embedded navigation systems. These functions are written in C++ to leverage its strengths as a cross platform language. Other functions such as recommended points of interest after route completion are written in Node.js. In addition, on the server side, Java, Go and C++ are used.

We had designed a FaaS runtime component in our serverless solution so developers just need to write function code which will be downloaded, loaded to the runtime and finally run inside our FaaS runtime. To achieve this, we have developed different FaaS runtimes for each language such as C++, Go, Rust etc. Functions in each language need to connect to backend services or to infrastructure services such as Redis, MySQL, MQ and so on, so we needed a multi-language solution to help us achieve this if we wanted to avoid using class libraries for each of the different languages.

In the past our solution to this challenge was using a [RSocket](https://rsocket.io/) broker: The FaaS runtime uses a lightweight multi-language RSocket SDK to connect to the RSocket broker, the RSocket broker in turn forwards the request to our RPC framework proxy to invoke the backend micro-services and forwards the response back to the FaaS runtime. This indeed solved both challenges but introduced a new challenge - the RSocket broker and the RPC framework proxy are centralized and that breaks the decentralized architecture we aimed to have for our serverless system.

As we looked into using Dapr, we found that Dapr offers an optimal alternative solution. We use the Dapr sidecar to support multiple languages and keeping applications lightweight, replacing the need for client SDKs. Meanwhile, the sidecar pattern keeps our architecture decentralized without the need for a centralized broker like RSocket.

## How Dapr is used in the FaaS runtime today

{{< imgproc autonavi-arch-diagram.png Resize "1500x" >}}{{< /imgproc >}}

In our Dapr sidecar, we have developed our custom components to support our RPC framework and other infrastructure such as our own KV-Store, config server. The multi-language (C++/Node.js/Go/Java) FaaS runtime uses Dapr SDKs to make requests to the Dapr sidecar via gRPC and the Dapr sidecars make requests to our backend services or to infrastructures such as Redis, MySQL, MQ. and sends a callback to the FaaS runtime when a response is returned.

In this new serverless solution, the Dapr sidecar is injected automatically by our Kubernetes service when a new FaaS runtime pod created. We have integrated this in our CI/CD pipelines, the user function code and user configurations are the same on different environments because running in a dev environment and production is consistent thanks to Dapr APIs.

In practice we are still using Dapr in an experimental way. Currently RSocket broker serves as a fallback in case of failures with Dapr. We feel that having a fallback is always a best practice when adopting a new technology. 

We are now working to verify Dapr in various scenarios by implementing Dapr in several business applications. After the verification, we see more and more parts of our solution migrating to use Dapr. Finally, we plan to remove the RSocket broker and fully rely on Dapr for our needs.

## Summary

The solution above has now been running in AutoNavi's production environment for over a month without any issues - the experiment is going well. By using Dapr, we solve the problems of invoking existing backend services in a lightweight model and supporting multiple languages in our serverless runtime without breaking the decentralized architecture. Dapr is really a perfect solution for invoking backend services in our multi-language serverless runtime.