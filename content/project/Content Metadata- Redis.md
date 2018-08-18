---
title: "Content Metadata - Redis"
description: "Storing and Retrieving Content Metadata Using Redis"
tags: ["redis", "publish/subscribe", "messaging" ]
weight: 4
draft: false
---

Description
-----

> - This system was designed and developed to maintain a key/value store for faster
metadata retrieval of content.
> - This system was also developed to establish a Publish/Subscribe messaging paradigm for
the synonyms feature.
> - This system was developed to store crucial information related to different types of stores
such as groups and zones.

Responsibilities
-----

> - Implemented storing of content related data in redis using StackExchange.Redis client for
.NET.
> - Implemented storing of group/zone related information, mostly useful in the specification
section of the product page.
> - Implemented a Publish/Subscribe mechanism for communicating between the web server
and the ElasticSearch node to achieve synonyms update process.