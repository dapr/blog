---
date: "2022-08-16T07:00:00-07:00"
title: "Pubsub for actors is arriving to Dapr with its first alpha version"
linkTitle: "Pubsub for Actors"
author:  "Diego Juarez"
type: blog
---

## Introduction
[Dapr virtual actors](https://docs.dapr.io/developing-applications/building-blocks/actors/actors-overview/) offer a lightweight, efficient, and scalable implementation of a self-contained unit of computation that acts based on the messages that it receives. Actors can be used in different scenarios where they can only communicate by sending and receiving messages from other independent objects. With the adoption of distributed applications in more real-world scenarios, the need for "acting objects" based on events happening in the environment made us create another way to interact with Dapr Virtual Actors.

["Pubsub for Actors”](https://docs.dapr.io/developing-applications/building-blocks/actors/pubsub-actors/) will help the user utilize Dapr Actors based on a subscription event sent from a publisher, maintaining the distribution and isolation of both parts. Pubsub for Actors will bring a new way to communicate with Dapr Actors from an event perspective.  
The next blog entry will explain how the first iteration of Pubsub for Actors works. This is a functionality that is in Alpha version, with time, it will be a fully supported functionality.

### Virtual Actors Overview

[Actors](https://docs.dapr.io/developing-applications/building-blocks/actors/actors-overview/) are the smallest unit of computation and helps distribute the workflows of an application. Actors are stateful, lightweight, and single-threaded with previously declared configurations and methods they can execute.
Dapr utilizes the Virtual Actors pattern, which builds a service that allocates an infinite number of actors. They are identified by their Actor Type and Actor ID, and found by the [Placement service](https://docs.dapr.io/developing-applications/building-blocks/actors/actors-overview/#distribution-and-failover) that works alongside the Dapr service. The Placement service’s work is to distribute and tell the location of all the Actors, how to find them and distribution of different ids within multiple sidecars. The placement service sidecar creates a hash table with all the actor’s information and will forward it to all the Dapr sidecars in the environment. This helps the Dapr sidecars know which actor types are hosted and where to allocate them.

### Pubsub Overview

[Pubsub](https://docs.dapr.io/developing-applications/building-blocks/pubsub/pubsub-overview/) is a pattern that helps the user send and receive messages with the Publisher and Subscriber model. The Pubsub building block lets the publisher share events with any subscriber listening without any interaction of both parties. Dapr Pubsub helps the user interact with the publish and subscribe APIs to use Pubsub architecture flexibly and effortlessly. The publisher sends data to the [intermediary message broker](https://docs.dapr.io/developing-applications/building-blocks/pubsub/pubsub-overview/#pubsub-api-in-dapr) and never knows if a subscriber received it. The message broker will forward the event as a Cloud event to any subscriber and guarantees a one-time delivery policy. The user can configure policies to the message so it can handle failed messages or specific message routing.

Pubsub works based on the [consumer groups model](https://docs.dapr.io/developing-applications/building-blocks/pubsub/pubsub-overview/#consumer-groups-and-competing-consumers-pattern). Services attached to different consumer groups will receive the message separately, but if the services are attached to the same consumer group, they will compete to receive the event.  
Since Dapr is agnostic of the message broker that the user wants to use, the user application needs to have a component file where it declares which Pubsub service it will use.

### Proposed Solution
The solution that the Dapr team developed describes the following: 
- A new API endpoint to publish messages specifying the Actor to invoke. 
- Handling of Cloud Events to call a specific Actor’s method.   
- A new field in the Actor Runtime Configuration that enables declaration of subscriptions for specific Actor Types. 

{{< imgproc actors-pubsub.png  Resize "1600x" Alt "Diagram of how Pubsub for Actors works, from publishing to receiving the message">}} Pubsub for Actors Diagram {{< /imgproc >}}

### Why make Actors Pubsub
Pubsub for actors was developed to let users interact and incorporate Actors to their applications based on Pubsub architectures. At this point, Actors can only be activated and invoked with the API endpoint. Additionally, Pubsub only supports service subscriptions. The new functionality is an increment to helping the user build applications with actors and call them based on published events.  
The proposed functionality was requested from Dapr users to extend Actors usability with other building blocks.

## Implementation
### Publish for Actors
To start using Pubsub for Actors, the user will need to have a publisher service or actor, and at least one Actor type as a subscriber. 

Use the HTTP/gRPC endpoint to create a Publish Actor Event:
```bash
POST http://localhost:3500/v1.0-alpha1/actors/<actorType>/<actorId>/publish/<PubsubName>/<Topic>
```
> Alternatively use the Python SDK event implementation
```python
with DaprClient() as d:
resp = d.publish_actor_event(
            pubsub_name='mypubsub',
            topic_name='mytopic',
            actor_id= '1000',
            actor_type='DemoActor',
            data=json.dumps(req_data),
            data_content_type='application/json',
        )
```
This request will be published in the specified topic as a Cloud Event that stores the declared Actor Type and Actor Id. 

### Subscribe for Actors
To subscribe an Actor to a topic, it is required to include a Pubsub configuration in the Actor Runtime Configuration. Include the "Pubsub name", "topic", "Actor type", "method", and optionally include an "actorIdDataAttribute" to use an Actor’s id from the sent message.
> Go
```go
var daprConfigResponse = daprConfig{
    Entities:                 []string{"DemoActor"}
    ActorIdleTimeout:         "1h",
    ActorScanInterval:        "30s",
    DrainOngoingCallTimeout:  "30s",
    DrainRebalancedActors:    True,
    Pubsub: []config.PubSubConfig{ // Subscription
        {
            PubSubName:  "myPubsub",
            Topic:       "myTopic",
            ActorType:   "DemoActor",
            Method:      "mymethod1",
            actorIdDataAttribute: "id",
        },
    },
    EntitiesConfig: []config.EntityConfig{
        {
            Entities: []string{"DemoActor"},
            Reentrancy: config.ReentrancyConfig{
                Enabled:       true,
                MaxStackDepth: 5,
            },
        },
    },
}
```
> With Python SDK
```python
# Actor Runtime Configuration with pubsub subscriptions
config = ActorRuntimeConfig(pubsub=[PubsubConfig(
    pubsubName="mypubsub",
    topic="mytopic",
    actorType="DemoActor", # Actor must exist in the Actor Runtime
    method="mymethod1", # Actor must have this method
    actorIdDataAttribute= "id"
    ), PubsubConfig(
        pubsubName="pubsub",
        topic="mytopic",
        actorType="AnotherActor",
        method="mymethod2"
    )])
```

The Dapr runtime will automatically check if there exists a Pubsub subscription for Actors in the Actor Runtime Configuration. When an event is published, Dapr will match the Actor Type and Id, then it will call the declared method. This logic follows the Pubsub consumer group principles. Multiple sidecars with the same consumer and actor types will compete for the event. If the sidecars have different consumer groups, they will all receive the message.    

## Functionality
### Actor Pubsub Features
Some of the Pubsub for Actor features are:  
- Activate actors based on events sent through a pubsub component without interacting with the Actors API.  
- The user is not required to include the Actor type and Actor Id in the published event (This scenario will require an actorIdDataAttribute in the subscription configuration).
- Integration with existing Pubsub architecture. 
- Pubsub component will handle failure when sending events.  

### Python SDK
Pubsub for Actors is currently in development. If you want to start using the functionality, you can use the Dapr endpoints or the Python SDK integration.

### Limitations
Due to functionality being in alpha version, there are some limitations:
- Implementation doesn’t guarantee in-order processing. Other Sidecars won’t wait until another sidecar has finished processing an event to continue receiving more events.
- The publish message must be of type Cloud Event.
- Publish Actor fields are optional, but the Actor Runtime Configuration must have the matching attributes to find the correct Actor. 
- Dead Letter topics are not supported yet.

## Looking ahead
The next steps for “Pubsub for Actors” will be targeted at optimizing the way subscriptions are handled. We want to make the process more efficient when searching for a subscription in a big number of Actor subscriptions.

Additionally, incorporate the new endpoint and configurations to all the SDKs supported by Dapr. SDK implementations will make the functionality agnostic of the programming language used to build the application and reach more users.

## Give us feedback and contribute
We are excited to bring this next step for Actors and Pubsub to our Dapr community. We are constantly searching for ways to help our users incorporate any Dapr Building Block into their applications. You can share your feedback or comments about [“Pubsub for Actors”](https://github.com/dapr/dapr/issues/501) on the proposal page. We care about your opinion or comments to improve.
You can open a new Dapr proposal on the [Dapr GitHub page](https://github.com/dapr/dapr).

Also, feel free to reach out to the Dapr official [Twitter account](https://twitter.com/daprdev) and [Discord server](https://discord.com/invite/sexsjFsSKW).
Additionally, you can contribute to the Dapr OSS project by collaborating in any of [ Dapr's GitHub repositories](https://github.com/dapr). Finally, you can actively participate in our [Community Calls](https://github.com/dapr/community) where Dapr’s new features new are shown and discussed.
