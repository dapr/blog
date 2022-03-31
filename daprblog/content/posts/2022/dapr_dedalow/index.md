---
date: "2022-04-01T08:00:00-07:00"
title: "How Dapr helped Dedalow accelerate development on AWS and Azure"
linkTitle: "How Dapr helped Dedalow accelerate development on AWS & Azure"
author:  "Javier Vela"
type: blog
---

[Dedalow](https://dedalow.com/) is a low-code / no-code solution developed by [NTT DATA](https://www.nttdata.com/), offering an end-to-end service for you to model, generate, and deploy your applications in different technologies based on your needs. Dedalow brings functionalities to the table, including testing, task automation, and code discovery services for existing applications. [View Dedalow in action](https://www.dedalow.com/wp-content/uploads/2021/03/Dedalow_Screen.mov).

Dedalow is a containerized application composed of more than 30 services, developed in different technologies, including: .NET, Python, Node.js or Java. Due to containerization and Helm charts, you can deploy Dedalow on a Kubernetes clusters and today, the service is available on Azure Kubernetes Service (AKS) or Amazon Elastic Kubernetes Service (EKS).

Dedalow is in constant evolution, and needed to integrate three new features:  

- **Asynchronous messaging**, allowing services to send information about the status of asynchronous requests to other services; for example, information about a pipeline status or execution status.
- **Storage** to persist test execution results.
- **Email notifications** from a Shell Script.

In all three cases, we wanted to take advantage of the services offered by the public cloud providers Azure and AWS:

| Feature | Azure | AWS |
| ------- | ----- | --- |
| Asynchronous messaging | Service bus | SNS/SQS1 |
| Storage | Azure storage | S3 |
| Email notifications | SendGrid2 | SES3 |

Previously, one handicap in implementing these functionalities was the time available to develop the features. Initially, we thought of integrating the different SDKs provided by the cloud providers, but this solution did not quite fit, due to:

- The integration time.
- The complexity of selecting and integrating libraries to be used, depending on:
  - The cloud provider, or
  - The type of cluster (Development / Production) where the application was deployed.

We then began evaluating Distributed Application Runtime (Dapr). Dapr is an open-source runtime hosted in the Cloud Native Computing Foundation (CNCF), with open governance by Microsoft, Alibaba, Intel, and Diagrid. Dapr offers different APIs to solve the complexity of developing distributed applications:

- Service invocation
- Publish & subscribe
- Secrets management
- Bindings (input/ output)
- State management
- Actors  

For Dedalow, we decided to use these APIs for the feature development:  

- Publish & subscribe
- Bindings (input / output)

Integration with Dapr has been very easy, as Dapr allows us to develop an agnostic implementation and apply configuration changes, without needing to recompile our applications or use external libraries. Dapr provides portability of code across the different clouds.

To use Dapr in any of the Dedalow core services, you only need to add the required annotations in Helm charts. Once added, the Dapr Kubernetes Operator injects a container into the pod of the Dedalow service. Dapr and Dedalow then work together through the sidecar pattern, with the communication between both containers via HTTP.

The diagram below shows the Dedalow integration with Dapr:

{{< imgproc dedalow_integration_dapr.png  Resize "1600x" />}}

Dapr is being used in production environments: AKS in Azure, EKS in AWS. Currently, not all of Dapr's components being used are in a stable status. However, the benefits of using them greatly outweighs the potential drawbacks, and so far we have not seen any issues. Dedalow intends to increase our usage of Dapr in the future.  
