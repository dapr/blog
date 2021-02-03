---
date: "2021-02-03T07:00:00-07:00"
title: "Dapr v1.0.0-rc.3 is now available"
linkTitle: "Dapr v1.0.0-rc.3 is now available"
description: "Announcing the third release candidate for Dapr v1.0"
author: Dapr project maintainers
type: blog
---

We are excited to announce v1.0.0-rc.3, the third release candidate for the official production ready v1.0 of Dapr!

The improvements in this release came out of issues opened by the community which has helped test the previous release candidate as well as contributed code changes to address these issues. Once again, we encourage the Dapr community to upgrade to the this release candidate and identify problems and open issues.

We expect this release candidate to be last one and have an official v1.0 release in the next few weeks.

## Acknowledgements

Thanks to everyone who made this release possible!

@1046102779, @AaronCrawfis, @Betula-L, @DarqueWarrior, @Yggdrasill-7C9, @alpha-baby, @alydersen, @arghya88, @artursouza, @beiwei30, @cmendible, @darren-wang, @davidkarlsen, @dylandee, @eibrunorodrigues, @fjvela, @gaoxinge, @georgestevens99, @halspang, @hepin1989, @IroNEDR, @jigargandhi, @juazasan, @karishma-chawla, @karoldeland, @kevgo, @kzmake, @mchmarny, @mrchypark, @msfussell, @mukundansundar, @newbe36524, @pkedy, @rrylee, @rynowak, @skyao, @soiboxauxi, @tcnghia, @TenSt, @tomkerkhove, @trondhindenes, @vinayada1, @wcs1only, @withinboredom, @wjayesh, @XavierGeerinck, @xiazuojie, @yaron2, @youngbupark, @zzxwill

## Installing and using v1.0.0-rc.3

As with the previous release candidate, an explicit opt-in to download and install is required. The instructions to upgrade to v1.0.0-rc.3 can be found in the [release notes](https://github.com/dapr/dapr/blob/release-1.0/docs/release_notes/v1.0.0-rc.3.md#upgrading-to-dapr-100-rc3), as well as in the [v1.0.0-rc.3 docs](https://v1-rc3.docs.dapr.io/getting-started).

## Release highlights

These are some highlights for this release:

* Pub/Sub [per message time-to-live](https://v1-rc3.docs.dapr.io/developing-applications/building-blocks/pubsub/pubsub-message-ttl/)
Dapr enables per-message time-to-live (TTL) for all pub/sub components. Applications can set time-to-live per message, and subscribers will not receive those messages after expiration.
* Sidecar to app authentication
* [MySQL output binding](https://v1-rc3.docs.dapr.io/operations/components/setup-bindings/supported-bindings/mysql/)
* [MySQL state store](https://v1-rc3.docs.dapr.io/operations/components/setup-state-store/supported-state-stores/setup-mysql/)
* Component metadata's random value via `{uuid}` marker
* [Upgrade CLI command to upgrade the Dapr control plane](https://github.com/dapr/cli#upgrade-dapr-on-kubernetes)
* Runs as non-root by defaut on Kubernetes
* Numerous improvements to the [Dapr Dashboard](https://github.com/dapr/dashboard)
* Added [dapr-distributed-calendar sample](https://github.com/dapr/samples/tree/master/dapr-distributed-calendar)
* [Java SDK](https://github.com/dapr/java-sdk) fixed to perform async API calls to Dapr sidecar
* [Improvements to actor registration, exception handling](https://github.com/dapr/dotnet-sdk/issues/569) in the .NET SDK 
* Service invocation in the SDK now uses HTTP as the underlying protocol so deeper integration with [.NET SDK](https://github.com/dapr/dotnet-sdk) API surface for HTTP is possible, which makes service invocation much more flexible. See [Service Invocation section](https://github.com/dapr/dotnet-sdk/issues/569) for more details

## Release notes

See a detailed description of changes, fixes and new features in the [Dapr v1.0.0-rc.3 release notes](https://github.com/dapr/dapr/blob/release-1.0/docs/release_notes/v1.0.0-rc.3.md). As always, it is recommended to pay careful attention to the [breaking changes section](https://github.com/dapr/dapr/blob/release-1.0/docs/release_notes/v1.0.0-rc.3.md#breaking-changes) before upgrading.

## Resources

- [Getting started with Dapr](https://v1-rc3.docs.dapr.io/getting-started/)
- [Documentation](https://v1-rc3.docs.dapr.io/)
- [Quickstarts](https://github.com/dapr/quickstarts/tree/v1.0.0-rc.3) and [samples](https://github.com/dapr/samples)

We hope you try out this release candidate and raise issues on [GitHub](https://github.com/dapr) as well as reach out to us to ask questions on [Discord](https://aka.ms/dapr-discord) or via [Twitter](https://twitter.com/daprdev) - just give us your thoughts, we’d love to hear from you!
