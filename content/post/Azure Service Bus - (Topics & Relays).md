---
title: "Azure Service Bus - (Topics & Relays)"
date: 2018-07-16
tags: ["azure", "cloud", "development", "topic", "relays", "service bus"]
draft: false
---

Topics
----
Topics are completely in contrast to Queues, in which each message is received by a single consumer, but Topics provide a publisher/subscriber mechanism where we can have more than 1 consumer/receiver. It plays well when we have requirements that cater to a good number of subscribers for the messages. `Filters can be added to the subscribers/receivers so that they only receive messages of specific kind.`

Messages are sent to the Topic in the same as they are sent to the Queue. But, they are received in a different manner through subscriptions. From the individual subscriptions,messages are received in the same as they are in the Queue. 

Configuration of the Topic almost remains the same as the Queue with the following properties:

>  - Partitioning
>  - Size
>  - Message TimeToLive
>  - Duplicate Detection

These properties are elaborated in my previous article on Queues. As you can see, some properties are missing in Topics. And, they are Lock Duration, Dead Lettering and Sessions.

You can configure the `read/write access to the Queue/Topic through setting up Shared Access Policy rules in the Azure portal of the Queue/Topic section.`

Relays
----
Relays set up a platform/medium to expose services that rest in a company's private environment to the public cloud without the need of configuring any firewall  or any network changes. They support the typical one-way communication of request/response and bi-directional socket communication as well.

Relayed data transfer works in a way that, a service hosted in a company's environment connects to relay service through a outbound port and messages are transmitted across to an address generated at the relay service end through `bi-directional socket` communication mechanism. And, this would in turn lead the client to communicate with the private hosted service by sending data to the relay service which gets relayed to the private service as the bi-directional communication socket is in place between relay service and private service.

Azure Relay has two ways of handling data transfers:

> - Hybrid Connections
> - WCF Relay

Hybrid Connections - Azure Hybrid Connections can be implemented on any platform and can relay both sockets and http(s) requests/responses.

WCF Relays - This approach is more of a traditional one as WCF is still being used for remote procedure calls. Connection between the private service and relay are established using relay bindings much on the lines of the existing architecture of WCF model.