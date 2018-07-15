---
title: "Azure Service Bus - (Queues)"
date: 2018-07-14
tags: ["azure", "service", "bus", "cloud", "queue", "messaging"]
draft: false
---

Azure Service Bus is a robust cloud-based scalable messaging system provided by Microsoft. This system offers three different ways to securely transmit messages between disconnected devices. And, they are:

> - Queues
> - Topics
> - Relays

Queues
-------------
A message queue provides an asynchronous mechanism to exchange messages between services. It implies that the producer of the message and the consumer of the message can send messages asynchronously without them having to be online at the same time. Messages can be sent from multiple sources based on the needs of the service. As it's a robust system, you don't have to worry about the message getting lost in any situation. Messages stay safe in the system until the receivers have processed the same. In case of the Queue, there's only one receiver at the other end. 

Let's look at an example where this would come handy. Imagine a situation where we are storing events/exceptions/activities in our database. And, the system follows a micro-service architecture. In this scenario, we can make use of a Queue to log all our events initially to it and later we could have another service monitoring this Queue to pass it on to the database. We would have all the services of the system to be the producers and one or two services specifically monitoring the Queue and playing the role of consumer(s).

Before creating a Queue on Azure, you would require a Namespace to be setup in your resource group and then we can move on to setting up a Queue. Namespace is a logical container of sorts, we can have multiple Queues under one Namespace.

Some important parameters to be considered at the time of Queue setup:

> - Partitioning
> - Size
> - Message TimeToLive
> - Lock Duration
> - Duplicate Detection
> - Dead Lettering
> - Sessions

Enable Partitioning:
----
Most Queues in the market employ the design of 1 Broker & 1 Message Store. And, this design can cause inefficiencies at the time of high loads or any downtime. Service Bus Queues provide this parameter to combat this very issue by enabling partitioning. It enables messaging entities to partition across multiple brokers and message stores. This would significantly improve the throughput and a temporary outage would not cause the Message Queue to be completely down as it's spread across other partitions. `And, this feature can only be enabled at the creation of the Queue, it cannot be changed or enabled later.`

Queue Size:
----
In their standard offering, you get 1 GB as the default size of the Queue. If partitioning is enabled, Queue would be stretched up to 16 MAX. For every additional GB that you choose, the max size gets multiplied by 16. So, if you choose a Queue size of 5 GB, you will get a maximum limit of 80 GB. `For each GB(1-5) that you specify, it creates 16 partitions. That's why you have a multiplier of 16 against the size you select, thereby increasing the max limit of the Queue.`

Message TimeToLive:
----
This parameter comes into the picture when the messages aren't being consumed or processed. It specifies an Expiration time that would allow the message to stay in the Queue for that specific time. Post which, the messages may be moved across to the Dead Letter Queue or deleted/dropped from the Queue. It can be specified based on the number of Days, Hours, Mins and Seconds. By default, it's set at 14 days. It can also be set on an individual message basis or just one interval for all the messages.

Lock Duration:
---
This feature is handy when there are multiple consumers monitoring or looking to process the messages in the same Queue. `In that case, a particular consumer can hold a lock on a certain message and other consumers won't be able to process them.` They move on to processing other messages in the Queue and pick the locked message in case of any failure or if the lock gets released by the previous consumer. There can also be instances where, consumers are releasing a particular message multiple times due to some failure in processing. After a few retries, that message would be moved to the Dead Letter Queue. **MaxDeliveryCount** is the parameter that will be responsible for setting the retry attempts for a message.

Duplicate Detection:
----
There can be situations in which the messages are being duplicated in the Queue, in order to avoid that, this parameter can be enabled at a certain cost of the throughput of the Queue. `It does hamper the performance of the Queue as and when the Queue size starts to increase with time. There's a certain overhead associated with this feature.`

Dead Lettering:
----
`This parameter can be enabled if you wish to keep track of the messages that have not been delivered to the consumer or have failed in the process.` Although, you don't have to maintain or create anything special for this, it's like a secondary queue for storing messages that failed for some reason. There's no `TimeToLive` duration being observed for these messages, they stay in the Dead Letter Queue for as long as you don't delete them from it explicitly. You will also find all the expired messages and messages that have exceeded the MaxDeliveryCount in this Queue.

Session:
----
`Service bus sessions help to determine a sequence of messages that are part of a certain batch in the Queue.` We can enable this parameter at the time of Queue creation or even at a later point in time. Programmatically, we will be able to set a SessionId for a certain set of messages and can be handled in a different way at the consumer end. This also helps the consumer to hold an exclusive lock on all the messages that pertain to this SessionId and these aren't tied to any session expiration.


`Next up, we will look into Topics & Relays.`


