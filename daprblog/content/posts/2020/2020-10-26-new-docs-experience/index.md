---
date: "2020-10-26T09:00:00-07:00"
title: "A new docs experience for Dapr"
linkTitle: "A new docs experience"
author: "[Ori Zohar](https://github.com/orizohar) & [Aaron Crawfis](https://github.com/AaronCrawfis)"
description: "Introducing an easier way to explore the Dapr documentation"
type: blog
---

#### **tl;dr -** [**docs.dapr.io**](https://docs.dapr.io) is now live!

Since Dapr was launched a year ago, it has grown at a remarkable pace. With new capabilities, features and enhancements growing, so does the documentation that helps developers and operators use Dapr to build their next distributed application. This includes introductory docs about Dapr concepts, "How-To" guides for achieving specific tasks and API reference documentation that are all a critical part of giving developers the tools they need to take advantage of everything Dapr has to offer. 

As Dapr is on the [path to stable v1.0 release]({{<ref 2020-10-20-path-to-production-release>}}), and seeing as the documentation is growing to a point where navigating the GitHub repo is becoming harder, we decided to streamline the docs experience and launch [docs.dapr.io](https://docs.dapr.io), as a better way to explore the Dapr documentation.

Informative and detailed documentation has been a primary goal for the Dapr project from the start and our approach has been that the documentation of any feature is an integral part of delivering it along with the code. The Dapr community has played a significant role in improving the docs, from simple fixes to typos and broken links, to comprehensive additions, to guides, the docs repo has seen the most community contributions of all the Dapr repositories and for that we want to give a big thank you!

Here are things to look for in the new docs experience and what it means for the docs GitHub repository and future contributions:

### Better navigation
The Dapr docs website now includes several elements to help with page navigation and content discovery 

* **Contextual navigation bar** - When exploring any of the pages in the documentation, you always have access to the left panel to a navigation bar showing all of the docs sections. The nav bar is contextual, so the current location is expanded and other sections remain collapsed.
* **Breadcrumbs trail** - At the top of the page you can always find where in the docs hierarchy you are using a breadcrumbs trail.
* **Table of contents** - Each documentation page has a table of contents on the right panel, making it easy to jump to specific sections in the page.
* **Section directories** - Each section includes a list of all docs in that section for easy discovery.

{{< imgproc navigation Resize "2496x" >}}
Navigation elements in the new Dapr docs
{{< /imgproc >}}

### Search
Powered by [aloglia](https://www.algolia.com/ref/docsearch/), we have integrated search to easily find content across the different sections. In the example below, a search for the term *service invocation* brings up articles relating to service invocation including overview, API reference, How-To guides and more.  

{{< imgproc search Resize "600x" >}}
Searching the docs
{{< /imgproc >}}

### Versioning
We have always aligned the docs with the Dapr runtime release versions using [version tags](https://github.com/dapr/docs/releases). With the docs website, you can to review docs by version. If you are looking for documentation aligned to the latest release, the default docs view is where you need to go. To explore previous versions, click the drop down at the top right and choose the version. For now, previous versions have not been migrated to the new experience and so these will land you on GitHub. Going forward from the current v0.11 release, new versions will be available on the website and remain there as a reference.

{{< imgproc versions Resize "400x" >}}
Version drop down
{{< /imgproc >}}

### Embedded content
With the ability to embed HTML in the documentation, we'll be adding relevant content into articles including videos such as demos from the [Dapr community calls](https://www.youtube.com/playlist?list=PLcip_LgkYwzuF-OV6zKRADoiBvUvGhkao). You can see an example in [this article](https://docs.dapr.io/developing-applications/building-blocks/service-invocation/service-invocation-overview/#namespaces-scoping).

### Changes in the repo structure 
The Dapr docs continue to be managed through GitHub via the [dapr/docs](https://github.com/dapr/docs) repository. In general, you shouldn't need to go to GitHub if all you want to do is read the docs, but if you have visited the repository in the past and curious to know how the repo has changed, you might notice some changes introduced as part of this addition of the docs website:

* **Hugo integration** - First we are leveraging the popular [Hugo](https://gohugo.io/) framework with the [Docsy](https://www.docsy.dev/) theme which allows us to easily build a static website out of the markdown files in the repo. This means there are Hugo related files in the repo and content has moved for the repository root to *docs/content*. All hierarchy is reflected in the directory structure under that path.
* **How-To guides** - You may notice that we removed the "How-To" directory and moved all guides that were available in that location to the relevant places across different sections. We did this because the number of guides has grown so much, that finding what you are looking for in that directory was becoming a tedious task. Now, for example, if you are looking for guides around Pub/Sub you can find them under the Pub/Sub section. The search feature is also a great way to look for guides about specific features and related to specific topics.
* **Separating developer and operator concerns** - Another change we made was splitting content according to the persona, mainly to a ["Developing Applications" section](https://docs.dapr.io/developing-applications/) for developers looking for guidance on how to leverage Dapr to build distributed applications and to an ["Operations" section](https://docs.dapr.io/operations/) aimed at operators looking for guidance on configuring, deploying and monitoring these applications.  
* **Reference docs** - The ["Reference" section](https://docs.dapr.io/reference/) now includes both reference documentation for the Dapr API as well as for the Dapr CLI.

### Looking ahead
Dapr docs continue to evolve as Dapr grows, especially in the near future as the project matures into a stable version 1.0. Some additional areas we intend to focus on include:
* Improving the getting started experience.
* More code snippets for different programing languages in existing docs (using tabs).
* Bringing in the documentation under [dapr/dapr](https://github.com/dapr/dapr/tree/master/docs) which provide guidance for developers looking to modify and build Dapr ruintime code.
* As always, new documentation for new features and improving existing documentation.

### Contributing to the Dapr docs
One thing that has not changed is our appreciation of community contributions to Dapr docs. Overall, docs are still essentially markdown files so if you have contributed in the past or looking to do so for the first time, it is straight forward. With the integration of the Hugo framework and changes to the structure and build process of the blog, there a few things to be aware of and we've added a detailed ["Contributing" section](https://docs.dapr.io/contributing/) that guides you in opening issues and pull requests to the docs.

With [Dapr's transition to open governance]({{< ref 2020-09-30-open-governance>}}) we invite you to become a community member and advance to the role of approver and maintainer of the docs. More information on community membership can be found in the [Dapr community repository](https://github.com/dapr/community/blob/master/community-membership.md).

### Give us feedback
We are excited to share the new docs website with you and hope you find it useful. As always, we'd love to know how you feel about the new docs and how we continue to improve them. Feel free to reach out over [Twitter](https://twitter.com/daprdev) or [Gitter](https://gitter.im/Dapr/) as well as open issues on [GitHub](https://github.com/dapr/docs).
