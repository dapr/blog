---
date: "2020-12-22T07:00:00-07:00"
title: "Looking back at 2020"
linkTitle: "Looking back at 2020"
description: "A growth year for the Dapr project"
author: "[Ori Zohar](https://github.com/orizohar)"
type: blog
---

As the year 2020 is coming to a close, I thought it is worthwhile to take a moment and look back at how much the Dapr project has achieved and how much the community has grown over the past 12 months. I believe these achievements are remarkable, especially considering how challenging and disruptive this year has been.

Here are a few key areas worth mentioning:

## Dapr releases

During 2020, Dapr saw 8 preview [releases](https://github.com/dapr/dapr/releases/) - starting with v0.4 in February, up to the last preview release, v0.11 in September. This release cadence has allowed the project to remain agile when addressing issues, feature requests, and gaps raised by the community.

Additionally, in the past two months, Dapr has started moving towards [a production ready v1.0 release]({{<ref 2020-10-20-path-to-production-release>}}) with two published release candidates. The latest, [v1.0-rc.2]({{<ref 2020-12-18-v1.0.0-rc.2-release.md>}}), released just this past week.

## More integrations

Dapr’s pluggable [component](https://docs.dapr.io/concepts/components-concept/) architecture allows developers to integrate a variety of technologies when building distributed applications. Growing this ecosystem of components is one of the main ways the Dapr community is contributing to the project. This year the number of supported components has more than tripled, growing from 20 supported components in the beginning of the year to now over 70! For example Dapr added [supported state stores](https://docs.dapr.io/operations/components/setup-state-store/supported-state-stores/) such as PostgreSQL, [supported bindings](https://docs.dapr.io/operations/components/setup-bindings/supported-bindings/) such as SendGrid and [supported Pub/Sub brokers](https://docs.dapr.io/operations/components/setup-pubsub/supported-pubsub/) such as Google Cloud Pub/Sub.

As Dapr grew, developers looked more and more to the benefits that it can provide for their use cases and scenarios. We have seen some integrations with developer runtimes, such as [cloud-native workflows using Dapr and Logic Apps](https://cloudblogs.microsoft.com/opensource/2020/05/26/announcing-cloud-native-workflows-dapr-logic-apps/), an [Azure Functions extension for Dapr](https://cloudblogs.microsoft.com/opensource/2020/07/01/announcing-azure-functions-extension-for-dapr/), and the [Dapr integration with Azure API Management Service](https://cloudblogs.microsoft.com/opensource/2020/09/22/announcing-dapr-integration-azure-api-management-service-apim/).

Language support and developer SDKs also continued to grow with integration of Spring Boot by the [Java SDK](https://github.com/dapr/java-sdk), the addition of an actor framework for the [Python SDK](https://github.com/dapr/python-sdk) and the integration of ASP.NET Core to the [.NET SDK](https://github.com/dapr/dotnet-sdk/tree/master/samples). Just this week a Dapr [PHP SDK](https://github.com/dapr/php-sdk) was added, including support for actors.

## A growing, engaged community

Openness has been a key part of the philosophy behind Dapr. All this progress cannot have been achieved without an engaged open-source community. This commitment to openness has been further manifested with the [transition to an open governance model]({{<ref 2020-09-30-open-governance>}}) for the Dapr project.

Throughout the year, Dapr has seen a consistent, healthy growth in new contributors who have made contributions to the various project repositories in the form of pull requests, issues, comments and reviews (we follow the [definition of contribution](https://github.com/cncf/devstats/blob/master/README_K8s.md) established for CNCF, used for projects such as Envoy and Kubernetes). During 2020, 386 new contributors joined the Dapr community growing the total number of contributors to 639.
 
{{< imgproc contributors-chart Resize "750x" >}}
Dapr project unique contributors to-date (across all repos) by month
{{< /imgproc >}}

Overall, more than 2500 new pull requests were opened since January 1st, 2020 across all Dapr repos.

Additionally, the community has come together to discuss the Dapr project and present demos and proposals in 24 [community calls](https://github.com/dapr/community#community-meetings) throughout the year (Watch [recordings](https://www.youtube.com/playlist?list=PLcip_LgkYwzuF-OV6zKRADoiBvUvGhkao) of these calls in the Dapr YouTube channel) and last month we also launched a new [Discord server](https://aks.ms/dapr-discord) for community members to discuss various areas of the Dapr project, ask questions and share ideas.

## A new docs website

In October we [launched a new website]({{<ref 2020-10-26-new-docs-experience>}}) for the Dapr docs - [docs.dapr.io](https://docs.dapr.io). This website allows for easier navigation, search, and discovery of Dapr documentation. Since its launch over 100,000 unique visitors have visited the Dapr docs website.
The Dapr docs repo remains one of the leading areas where the community is contributing, with over 460 pull requests opened during 2020 to improve and extend the Dapr documentation.

## Looking forward to 2021

Looking ahead to next year, there is plenty to look forward to: In the first part of 2021 we’ll see additional release candidates leading to an official production ready v1.0 release. With the community growing and more developers than ever discovering Dapr, we will continue to see more components added, integration to existing developer frameworks, and innovative ways to use Dapr. 

As we get closer to the v1.0 release, we will publish a roadmap looking past v1.0 at some potential areas to add new feature capabilities to, in order to get community feedback on where to go next. 

## Thank you

All these achievements would not be possible without the continued contributions, input, engagement, and excitement from you, the Dapr community. Thank you all for your continued support and contributions throughout this challenging year. Here’s to the next year of Dapr and building distributed applications together!
