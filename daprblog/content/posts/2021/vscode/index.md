---
date: "2021-07-30T07:00:00-07:00"
title: "A better developer experience with the Dapr Visual Studio Code extension"
linkTitle: "VS Code extension update"
description: A new version of the Dapr extension brings new features and improvements to VS Code developers
author: "[Shruthi Kumar](https://github.com/sk593) & [Jason Viviano](https://github.com/jasonviviano)"
type: blog
---

## Introduction
Since Dapr first launched almost two years ago, the developer experience has been a key area of focus for the project. Our goal is to help developers write Dapr applications, debug them, and easily access relevant information about the Dapr sidecar in developers' local environments and using their tools of choice. Seeing as Visual Studio Code (VS Code) is one of the most commonly used IDEs, we introduced the [VS Code Dapr extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-dapr) - a plug-in designed to help VS Code developers build Dapr-enabled applications. Launched in early 2020, the extension provides a way to invoke Dapr application methods and scaffold debugging configuration files and component files, culminating in a facilitated local debugging experience for Dapr applications.

As more and more developers started using Dapr, we saw an opportunity to expand the extension with features that would improve the experience for developers. To determine what can be improved, we reached out to members of the community and conducted interviews with regular users of the extension about what features would help better the developer experience. Some of the things we heard included a request for more UI interaction and visual feedback that would make the extension easier to navigate. Also, you told us that the extension should aim to target beginner and intermediate Dapr users, while still providing flexibility to accommodate advanced users.

This feedback helped shape the recent v0.5.0 release of the extension. This release aims to make the Dapr development experience more native to VS Code, meaning that users can develop applications within the scope of VS Code (i.e. there’s no need to switch between the IDE and the terminal). Additionally, users can now visualize Dapr attributes that a developer might need (for example, port numbers, app IDs, etc).

## What’s new in v0.5.0?
To see a detailed description of the changes, fixes, and new features, check out the [extension release notes](TBD). The main new features and improvements included in v0.5.0 of the extension are:

### UI improvements
An updated application view and an added details view that allows users to view the components and metadata associated with their applications. Application metadata includes the app port, HTTP port, process IDs, etc while component metadata includes the name, type, and version of the component.


{{< imgproc apps.png Resize "500x" >}} Dapr application view {{< /imgproc >}}

{{< imgproc app-detailed.png Resize "500x" >}} Application details {{< /imgproc >}}

{{< imgproc comp-details.png Resize "500x" >}} Components details {{< /imgproc >}}

### Stop a Dapr application
Support for stopping applications, allowing users to easily kill running applications or sidecars. 

{{< imgproc stop.png Resize "500x" >}} New menu option to stop a Dapr application {{< /imgproc >}}
### Open the Dapr dashboard
Ability to open the Dapr dashboard from within VS Code to see any metadata associated with running applications.

{{< imgproc dashboard.png Resize "500x" >}} Open the Dapr dashboard {{< /imgproc >}}
### Other
- A [bug fix](https://github.com/microsoft/vscode-dapr/issues/169) for locating Dapr applications on Mac/Unix machines.

## Looking ahead
As the Dapr extension continues to evolve to improve the developer experience, here are some additional areas we intend to focus on:

- More component customization support in the form of templates for common component files.
- Viewing the configuration files found in Dapr instances identified in the application tree view.
- VS Code specific Dapr sidecar to handle all Dapr related interactions on VS Code itself.

## How to Contribute
As a developer, you are best positioned to help us identify the challenges faced in everyday Dapr development and as such, we invite you to share your feature ideas or open issues on the official [GitHub repository for the extension](https://github.com/microsoft/vscode-dapr). We also encourage you to attend our recurring [Dapr community calls](https://github.com/dapr/community#community-meetings) to see demos and discuss new feature proposals with the rest of the Dapr community!

We hope you find the latest release of the extension useful and are excited to see how it can help you build your next Dapr application!
## Resources
- Visual Studio Marketplace page for the [Dapr VS Code extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-dapr)
- [Getting started with Dapr](https://docs.dapr.io/getting-started/)
- Dapr docs section about the [VS Code extension](https://docs.dapr.io/developing-applications/ides/vscode/)
- Presentation of the new features in the Dapr [community call](https://youtu.be/QADHQ5v-gww?t=1602)



