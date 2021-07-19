---
date: "2021-06-30T07:00:00-07:00"
title: "How Dapr helped AutoNavi build a new serverless solution"
linkTitle: "Autonavi's Dapr case study"
author: "Xuexiang Deng, Staff Engineer, AutoNavi Information Ltd."
type: blog
---

{{< imgproc autonavi-logo.png  Resize "200x" />}}

AutoNavi is a leading provider of digital map and navigation services in China with over 100 million daily active users. AutoNavi started its Serverless/FaaS (Function as a service) project on April 2020 and after just one year, AutoNavi's solution already exceeds 100,000 queries per second (QPS). In this blog I will share how we at Autonavi use Dapr to implement our serverless solution.

Below are a few of the business use cases that our solution addresses:

{{< imgproc autonavi-usercase.png Resize "1500x" >}}{{< /imgproc >}}

- Weather in a long route: When the route distance exceed a threshold, for example 100kms, users may want to know what is the weather like during the route.
- Search along route: Search vehicle infrastructure like gas station, electric vehicle charge-station，petrol station, vehicle maintain station in the route.
- Route tips: Show tips like width limiting pier, many large vehicle along the route.
- Scenic spots information: Show ticket price, opening hours, telephone and brief introduction of a scenic spot.

## Why we chose Dapr

While building the new service, we encountered several challenges. Among them, two that we felt Dapr could help us address: Connecting existing backend services using a lightweight solution and our requirement for a runtime which supports multiple languages.

### Connecting existing backend services

To leverage existing backend services which generally are Micro-service, our FaaS must have the capability to invoke these Micro-services which developed base on our RPC framework. A direct approach would be to use our RPC framework SDKs in our FaaS applications, but a hard requirement for our FaaS and serverless scenarios is keeping it lightweight as to meet our fast start-up and scaling needs. We also wanted to avoid having the application become bloated due to a large number of SDKs that were integrated into the code base. 

This is where Dapr's lightweight footprint was very helpful. In addition, leveraging Dapr's APIs helped us avoid using any SDK libraries in our code. 

### Multi-language runtime

At AutoNavi, we mainly use C++ and Node.js on the client side. Base navigation functions such as mapping display, route display, sign guidance, spoken guidance etc. need to run both on mobile and vehicle embedded navigation systems. These functions are written in C++ to leverage its strengths as a cross platform language. Other functions such recommended points of interest after route completion are written in Node.js. In addition, on the server side, Java, Go and C++ are used.

We had designed a Faas(function as a service) runtime component in our serverless solution. Developer just need to write function code, the function code will be downloaded, loaded and finally run inside our Faas runtime. We have developed different Faas runtime for each of the language, include C++ Faas runtime, Go Faas runtime, rust Faas runtime and so on. Functions in each language need to connect to backend services or connect to infrastructures such as redis, mysql, MQ and so on, so we needed a multi-language solution to help us achieve this if we wanted to avoid using class libraries for each of the different languages.

In the past our solution to the above challenges was using a [RSocket](https://rsocket.io/) broker: The FaaS runtime uses a lightweight multi-language RSocket SDK to connect to the RSocket broker, the RSocket broker in turn forwards the request to our RPC framework proxy to invoke the backend Micro-services and forwards the response back to the FaaS runtime. This indeed solved both challenges but introduced a new challenge - the RSocket broker and the RPC framework proxy are centralized and that breaks the decentralized architecture we aimed to have for our serverless system.

As we looked into using Dapr - we found that Dapr offers an optimal alternative solution. We use the Dapr sidecar to support multiple languages and keeping applications lightweight, replacing the need for client SDKs. Meanwhile, the sidecar pattern keeps our architecture decentralized without the need for a centralized broker like RSocket.

## How Dapr is used in the FaaS runtime today

{{< imgproc autonavi-faas-runtime-v3.png Resize "1500x" >}}{{< /imgproc >}}

In our Dapr sidecar, we have developed our custom components to support our RPC framework and other infrastructure such as our own KV-Store, config server. The multi-language (C++/Node.js/Go/Java) FaaS runtime uses Dapr SDKs to make requests to the Dapr sidecar via gRPC, the Dapr sidecars make requests to our backend services or make requests to infrastructures such as redis, mysql, MQ. and sends a callback to the FaaS runtime when a response is returned.

In this new serverless solution, the Dapr sidecar will be injected automatically by our kubernetes service when a new Faas runtime pod created. We have integrated this in our CI/CD pipelines, the user function code and user configurations is the same on different environments because running in a dev environment and production is consistent thanks to Dapr APIs.

In practice we are still using Dapr in an experimental way. Currently RSocket broker serves as a fallback in case failures with Dapr. Having a fallback is always a best practice when adopting a new technology. 

We are now working to verify Dapr in various scenarios by implementing Dapr in several business applications. After the verification, we see more and more parts of our solution migrating to use Dapr. Finally we plan to remove the RSocket broker and fully rely on Dapr for our needs.

## Summary

The solution above has now been running in Autonavi production environment for the last one month without any issue, the experiment goes well. By using Dapr, we solve the problems of invoking existing backend services in a lightweight model and supporting multiple languages in our serverless runtime without breaking the decentralized architecture. Dapr is really a perfect solution for invoking backend services in our multi-language serverless runtime.