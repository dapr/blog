---
date: "2021-03-19T07:00:00-07:00"
title: "How Alibaba is using Dapr"
linkTitle: "Dapr case study: Alibaba"
author: "[Sky Ao](https://github.com/skyao), Staff Engineer, Alibaba Cloud"
type: blog
---

{{< imgproc alibaba-logo.png  Resize "200x" />}}

The **D**istributed **Ap**plication **R**untime ([Dapr](https://dapr.io/)) is an open-source, portable, and event-driven runtime. It enables developers to build elastic, stateless/stateful applications running on cloud platforms and edge devices. Dapr can lower the threshold for building modern cloud-native applications based on the microservice architecture.

## Why we chose Dapr

At Alibaba, Java is widely used for business applications, various middleware servers, and basic capabilities. Over the past decade, Alibaba has built a solid Java based technology stack in various of battle tested scenarios.

However, as Alibaba's business growing rapidly and its adoption of cloud-native technology, **multi-language** requirement, including Node.js, Golang, C, C++, and Rust, etc soon becomes a need. As a result, an efficient solution to develop microservices without language restriction is highly demanded.

Today, more of our business applications, especially frontend services, have begun to adopt FaaS and serverless as application hosting and resource scheduling solutions. However, FaaS and serverless scenarios require a more lightweight solution to meet fast start-up and scaling needs. In the conventional class library mode, a business application becomes bloated because a large number of SDKs must be integrated. It is more inconsistent for applications in the function form. Take Node.js as an example - Hundreds of lines of Node.js function code still need to depend on tens of MB of node modules. FaaS and serverless also pose higher requirements for multi-language support. Therefore, in FaaS and serverless scenarios, it is necessary to provide a more lightweight and multi-language solution different from the conventional class library approach.

Finally, Alibaba chose using a solution with multiple runtimes running as sidecars. A similar approach was also mentioned in "[Multi-Runtime Microservices Architecture by Bilgin Ibryam](https://www.infoq.com/articles/multi-runtime-microservice-architecture/).":

{{< imgproc Multi-Runtime-Microservices-Architecture.jpg Resize "1500x" >}}
{{< /imgproc >}}

Dapr is the first open-source project to practice the multiple runtime concept. Alibaba has paid close attention to Dapr since its release because it had the potential to help solve some of the challenges we've encountered. Namely, the sidecar pattern supports multiple languages and so making applications more lightweight with the Dapr runtime replacing the need for client SDKs. This is especially helpful when migrating a product to different environments or deploying it across environments - a process that can be very painful. In this context, the function-oriented programming concept, portable and scalable standard APIs, and platform-neutral and vendor-free design of Dapr makes a lot sense.

Li Xiang, Senior Staff Engineer at Alibaba Cloud, said, *"At Alibaba Cloud, we believe that Dapr will lead the way in microservice development. By adopting Dapr, our customers can build portable and robust distributed systems faster."*

In the middle of 2020, Alibaba conducted an internal small-scale pilot using Dapr to explore and verify the Dapr's validity in a real-world implementation. We've also been actively participating in the Dapr community and have submitted a large number of improvement suggestions, feedback and code.

In the next section, this article takes the real-world implementation of Dapr in Alibaba as an example to describe how Dapr helps solve the problems described above.

## Dapr use cases at Alibaba

### Overview

Dapr is still in the experimental stage in Alibaba. Currently, our primary goal is to develop Dapr components for internal middleware. With Dapr components, business applications can be decoupled from this middleware and the Java and Java Client SDK that implements them. Additionally, we are working to verify Dapr in various scenarios by implementing Dapr in several business applications. After the verification, business applications will be deployed on a large scale.

As of March 2021, Dapr has been implemented in two scenarios in Alibaba: multi-language support and cloud-to-cloud migration.

### Multi-language support

#### FaaS and serverless scenarios

**The challenge:** Alibaba's e-commerce system involves a large number of requirements for activities and shopping guides.

These requirements are short duration, stable traffic, and fast response. They require rapid development and iteration and have a relatively short life cycle. Therefore, FaaS is suitable for such requirements.

FaaS has strong demands for multi-language support and is not limited to Java. However, most of the applications in Alibaba use the Java system and have adequate multi-language support, especially for emerging languages (such as Dart) or niche languages (such as Rust).

FaaS applications also need to communicate with on-premises services, various middleware, and infrastructures. Therefore, multi-language support is critical for FaaS.

With Dapr, Alibaba has solved the multi-language problem of FaaS and helped customers improve their development efficiency through FaaS.

#### Multi-language application integration

**The challenge:** Alibaba acquires a large number of companies, each using different solutions and technology stacks.

The companies acquired by Alibaba have a large number of applications, many of which do not use the Java system. These applications have clear requirements for multi-language support during integration into the Alibaba technical system. For example, Node.js and Golang are required for some applications, while Dart and C++ are required for other applications.

However, the ecosystem of these languages is not as complete as Java. In particular, some middleware and infrastructures are very mature and only need maintenance while others are not. In reality, it is unpractical to redevelop clients in all languages because due to cost and time constraints.

Alibaba can provide multi-language solutions for these applications through Dapr.

#### Complex Java based legacy systems

**The challenge:** Complex systems designed based on Java ClassLoader

Alibaba designed complex systems based on ClassLoader for the Java system to solve the class conflict problem and isolate different service modules. The design of these systems is often complex, and applications are bloated.

In addition, some business teams maintain a set of multi-language middleware SDKs to interconnect with existing middleware. These SDKs should be maintained and simultaneously updated by the middleware team. Aside from the effort it requires, this also brings hidden stability risks.

Therefore, Alibaba expects to migrate these legacy systems to Dapr to maintain and update the middleware SDKs uniformly. In addition, we aim to remove the need for business development teams to make code changes to reduce the impact on business applications during migration. When migrating these legacy systems to Dapr, we design a Java adaptation layer, which adapts original Java calls to the Dapr client API.

The implementation of multi-language support in the three scenarios above is illustrated below:

{{< imgproc multiple-langurage.jpg Resize "1500x" >}}
{{< /imgproc >}}

### Cloud-to-cloud migration

**The challenge:** Cross-platform requirements exist when providing business applications externally.

Some Alibaba services, such as DingTalk Document, were originally provided for internal and external Alibaba users. Users only need to deploy DingTalk Document in the internal business cluster of Alibaba, and then they can directly access the Alibaba ecosystem.

However, as the SaaS business develops, the demand for data security from users sensitive to information security also increases. Thus, users want to deploy DingTalk Document in VPCs or the public cloud.

Therefore, the DingTalk Document system need to be migrated to the public cloud. The underlying technologies of DingTalk Document are based on the internal technical system of Alibaba. Now, they need to be migrated to commercial products based on open-source technologies or Alibaba Cloud technologies.

With the standard API and scalable components of Dapr, users can directly shield the underlying middleware through the Dapr runtime without any code modifications. If business systems are deployed on different platforms, consistent capabilities are provided by activating different components in Dapr.

Take message communication as an example. When an application needs to access the message system, users can:

- **Inside Alibaba:** Activate RocketMQ components through Rocketmq.yaml.
- **On the Public Cloud:** Activate Kafka components through Kafka.yaml.

The portability of Dapr decouples the upper-layer applications of DingTalk Document from underlying infrastructures, such as the message system. Thus, DingTalk Document achieves smooth migration between different cloud platforms.

And so, Dapr helps the business team deploy DingTalk anywhere.


{{< imgproc cloud-migration.jpg Resize "1500x" >}}
{{< /imgproc >}}

## Future plans for Dapr at Alibaba

In the future, we will continue to verify Dapr through pilot applications in the following aspects:

- Applicable scenarios
- Performance
- Stability
- Portability

We are developing Dapr components to integrate with more middleware and infrastructures, including internal products and commercial products supported on Alibaba Cloud. After passing the verification, Alibaba will contribute the integration code of commercial products on Alibaba Cloud to the Dapr project. By doing so, we can provide support for Dapr, including:

- RPC support for Apache Dubbo
- Messaging support for Apache RocketMQ
- Dynamic configuration support for Nacos
- MySQL support for Alibaba Cloud RDS
- Redis support for Alibaba Cloud Open Cache Service (OCS)

Alibaba is a pioneer of the multiple runtime architecture and an early adopter of Dapr. We are working with the Dapr community to improve the functions, performance, and stability of Dapr. We are looking forward to continue building the cloud-native **D**istributed **Ap**plication **R**untime together with the community.