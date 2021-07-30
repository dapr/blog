---
date: "2021-07-30T07:00:00-07:00"
title: "A better developer experience with the Dapr Visual Studio Code extension"
linkTitle: "VS Code extension update"
author: "[Shruthi Kumar](https://github.com/sk593) & [Jason Viviano](https://github.com/jasonviviano)"
type: blog
---

## Introduction
Since the launch of Dapr in 2019, we’ve continued to see the framework grow at a high pace. As new features and capabilities are continually added, it is important for developers to be able to easily use Dapr in their own environment. Users should be able to write Dapr applications, debug them, and see information about their apps and sidecars within VS Code. Enter the [Visual Studio Code Dapr extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-dapr), a plug-in designed to help developers build Dapr-enabled applications in Visual Studio Code. Launched in early 2020, the extension provides a way to invoke Dapr application methods and scaffold debugging configuration files and component files, culminating in a facilitated local debugging experience of Dapr applications.

As more and more developers started using Dapr, we saw this as an opportunity to expand the extension with features that would improve the experience for developers. We discussed with members of the community and conducted interviews with frequent users of the extension about what features would help better the developer’s experience with the Dapr extension. We were told that more UI interaction and visual feedback would make the extension easier to navigate. Also, the extension should aim to target beginner and intermediate users of Dapr, while still providing flexibility to accommodate advanced users. Our goal with the VS Code Dapr updates is to allow for an easier debugging process and provide more visual control and feedback using UI components.

This feedback shaped the upcoming v0.5.0 release of the extension. The release aims to make the Dapr development experience more native to VS Code, meaning that users can develop applications within the scope of VS Code (i.e. there’s no need to switch between the IDE and the terminal). Additionally, users can now visualize Dapr attributes that a developer might need (for example, port numbers, app IDs, etc).

## What’s new in v0.5.0?  
Below is a summary of the new features and improvements included in v0.5.0 of the extension: 

- An updated application view and an added Details view that allows users to view the components and metadata associated with their applications. Application metadata includes the app port, HTTP port, process IDs, etc while component metadata includes the name, type, and version of the component. 
- Support for stopping applications, allowing users to easily kill running applications or sidecars. 
- Ability to open the Dapr dashboard from within VS Code to see any metadata associated with running applications.
- A [bug fix](https://github.com/microsoft/vscode-dapr/issues/169) for locating Dapr applications on Mac/Unix machines.

To see a detailed description of the changes, fixes, and new features, check out the [extension release notes](TBD).

## Looking ahead
As the Dapr extension continues to evolve to improve the developer experience, here are some additional areas we intend to focus on:

- More component customization support in the form of templating common component files.
- Viewing the configuration files found in Dapr instances identified in the application tree view.
- VS Code specific Dapr sidecar to handle all Dapr related interactions on VS Code itself.

## How to Contribute
As a developer, you are best positioned to help us identify the challenges faced in everyday Dapr development and as such, we invite you to share your feature ideas or open issues on the official [GitHub repository for the extension](https://github.com/microsoft/vscode-dapr). We also encourage you to attend our recurring [Dapr community calls](https://github.com/dapr/community#community-meetings) to see demos and discuss new feature proposals with the rest of the Dapr community!

## Resources
- Visual Studio Marketplace page for the [Dapr VS Code extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-dapr)
- [Getting started with Dapr](https://docs.dapr.io/getting-started/)
- Dapr docs section about the [VS Code extension](https://docs.dapr.io/developing-applications/ides/vscode/)
- Presentation of the new features in the Dapr [community call](https://youtu.be/QADHQ5v-gww?t=1602)



