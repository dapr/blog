# How AutoNavi is using Dapr

> By Xuexiang Deng, Staff Engineer, AutoNavi Information Ltd. 

AutoNavi is a leading provider of digital map in China with over 100 million DAU. AutoNavi stated it's Serverless/Faas project on Apr 2020, After one year, AutoNavi's Faas QPS has been exceed 100 thousands. We use Dapr in AutoNavi Serverless/Faas project, let me explain why and how we use Dapr in our Serverless/Faas project.

Some business examples written by Faas:

![image.png](./images/autonavi.png)

## Why We Use Dapr

During the conducting of AutoNavi Faas project, We got many challenges, two of the challenges has something to do with Dapr, one challenge is how to connect existing backend services in a lightweight mode. Another challenge is the multi-language runtime demand.

### How to connect existing backend services in a lightweight mode

To leverage existing backend services, Faas must have the capability to invoke existing services which were built base on Alibaba middleware. of course we can also use middlerware SDKs in our Faas applications, but as we know FaaS and serverless scenarios require a more lightweight solution to meet fast start-up and scaling needs. In the conventional class library mode, a business application becomes bloated because a large number of SDKs must be integrated.

### Multi-language runtime

In AutoNavi, the major language used in the client side are C++ and Node.js. base navigation functions such as mapping display, route display, sign guidance, spoken guidance and etc need to be run both at mobile and vehicle embedded navigation. these functions are written in C++ to leverage it's cross platform feature. Functions such as before the route, recommanded POIs after the route are written in Node.js. The major language used in the server side are Java, Go and C++. 

Therefore, in AutoNavi's FaaS and serverless scenarios, it is necessary to provide a more lightweight and multi-language solution different from the conventional class library approach.

We use RSocket broker solution to solve theses two challenges in the past. Faas runtime use a lightweight multi-language RSocket SDK to connect to RSocket broker, Rsocket broker forward the request to middleware proxy to invoke the existing service exposed by Alibaba middleware and forward the response back to Faas runtime. These approach can solve the two challenges properly, but involves a new challenge at the same time, the RSocket broker and the middleware proxies are centralized architecture which against the serverless architecture.

Dapr is an optimized sulotion to these chanllenges. We use Dapr sidecar to support multiple languages and make applications more lightweight with the Dapr lightweight SDK replacing the need for client SDKs, and Dapr does not have the centralized architecture problem.

## We Use Dapr Sidecar In Faas Runtime

How we use Dapr in Faas runtime is illustrated below:

![image.png](./images/faas-runtime.png)

C++/Node.js/Go/Java Faas runtime use Dapr sdk to make middleware related request to Dapr sidecar by GRPC, these Dapr sidecars has Alibaba middleware components built in it. Dapr sidecar make request to Alibaba middleware services and callback Faas runtime when got response from Alibaba middleware services.

Dapr is still in the experimental stage in AutoNavi's Faas scenarios. In the beginning we fallback to RSocket broker in case of dapr invoking failed. As you know, has a fallback is always the best practice to adopt a new  technology.

Now we are working to verify Dapr in various scenarios by implementing Dapr in several business applications. After the verification, more and more Faas Runtime will be migrate to the Dapr sidecar approach. Finally we will remove the RSocket broker approach and use Dapr only in our Faas runtime.

