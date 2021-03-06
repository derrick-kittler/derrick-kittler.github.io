---
layout: post
title:  "Microservices are great for all-the-things but what about the data"
date:   2018-03-23 10:16:01 -0600
comments: true
categories: jekyll update
---

If you’ve been following the trends around modern application development, you’ve likely been inundated with reasons why you need to adopt microservices. You know about domain-driven design, the perks of 2-pizza teams, and you’re familiar with consumer-driven contracts. What’s not discussed as often is how to best manage application state and user data within a scaled microservice architecture.

In part one of a two-part series, I’m going explore some of the historical challenges and define several approaches for a more thoughtful distributed data architecture.

Let’s take a moment to reflect on how sophisticated and robust modern software has become. Big steps have been made in tooling for code craftsmanship, quality analysis, and automated tests. We now have the ability to use machine intelligence to improve code performance and rectify security vulnerabilities before a production go-live! Data access is easier thanks to today’s frameworks, libraries, and integrated runtimes, which also allow just-in-time data model creation with full create-read-update-delete semantics.

Writing high-quality functioning code is easier today than ever before; what’s hard is managing the state of the application. For example, in a distributed architecture, how do we ensure consistency of user experience, data reporting, data analytics? How do we ensure transaction consistency, write and read performance under load, etc.? And how do we do it all within an ephemeral cloud-based infrastructure?

### The red headed stepchild

In traditional java-based development, data can easily become the red-headed stepchild. That’s because traditional Java-based development has you model your domain objects into POJOs (plain-old-Java-object) which are then mapped to an underlying data schema; typically your data access layer. The data schema is created and maintained via ORM (object-relational-mapping) tools. This programing model makes it easy to build the data access layer, however the resulting data model and physical schema is often anemic and not representative of the overall enterprise data architecture.

Writing software in this manner places the application as the source of essential complexity, resulting in accidental complexity of the associated data model, a common side effect of software development. Moreover, software development heavily influences physical data structure, storage requirements, consistency rules, access patterns etc — and it's all done accidentally, hence data becoming the red-headed stepchild.


### The impedance problem

More recent application development approaches focus on modeling the business domain into business-specific functionality aka “rules of the business.” Development using this rules-first approach is commonly defined as domain driven design (DDD). First documented by Eric Evans, DDD is a mature approach for defining and architecting systems around like business concepts (bounded contexts) and requires a strong collaboration between engineering and business owners. However, when it comes to modeling the supporting data architecture, we still rely on ORM mapping techniques with little to no improvement over creating a POJO architecture.

When you consider the change friction at the schema level versus the change friction at the application level, it’s easy to rationalize that all changes directly to the data schema are painful. On the other hand, when you properly abstract the schema from the application (e.g. data interface), then friction becomes close to non existent; schema modifications are painful whereas application changes are much easier.

### Microservices solve all-the-things

When we apply sound DDD principles to a distributed service-based architecture we are immediately confronted with the data problem. Microservice purists espouse the need to manage data as part of the overall service lifecycle and often times this translates into  creating and managing copies of data. If we consider that each bounded context has its own data model, we move into this uneasy place of forced data duplication.

What happens when the teams pick differing persistent stores? Combinations of relational databases? NoSQL structures? And distributed caches? This lack of data strategy fosters database sprawl, increased cost, needless operational overhead and data consistency issues. Data purists shrill at this approach and have worked to define other techniques and architectures that not only support a microservice architecture but are also better custodians of enterprise data management.

### A few approaches

One strategy that can significantly help the database-per-microservice dogma is to architect with data virtualization. Data virtualization can provide an in-memory bounded context-specific data model per microservice as an abstraction above the physical data schema. With data virtualization, data is never isolated, never replicated to physical data schemas, and correlations across data sources are permitted. Virtual databases provide data agility, fit-for-purpose data models, and fit-for-service data security (e.g. RBAC, attribute masking, etc. … ).

Another data strategy for microservices is to leverage in-memory distributed caching architectures. Adding caching strategies such as replication and partitioning directly to the cache instance can assist the architecture depending on use case. Replication is concerned with sharing memory across nodes to allow for storing critical data such as user session data. This session data needs to be consistent across nodes, durable for fault tolerance and elastically scalable. Whereas partitioning allows for co-locating data alongside the actual microservice in a ‘sidecar’ approach. Partitioning supports data sharding at a per-service level providing greater security than straight replication and it also supports a near/far cache architecture with a remote distributed data grid.

Given the two approaches outlined, we have yet to consider several core data management concerns including data consistency and a core fallacy of distributed computing. Recall Eric Brewer’s CAP theorem which states that given a network partition (distributed computing fallacy #1 is the network is reliable) you need to choose between consistency or availability. When solving for consistency, change data capture (CDC) strategies can be your friend. A refinement to CDC is with the addition of event sourcing and state change propagation which provides the feel of a highly-consistent data architecture while allowing for asynchronous performance and elasticity benefits.

### Everything is an event

An architectural approach that can solve many of the data problems inherent with a distributed architecture is event sourcing. Event sourcing, as defined by Martin Fowler, is a process by which you “capture all changes to an application state as a sequence of events.” In the data context, microservices publish and subscribe to data events and persist state change to a replayable append-only log. Event sourcing provides eventual consistency of data across the network and also eliminates the need for data management within the lifecycle of the microservice. Streaming technologies such as Apache Kafka are often used to manage the event messaging fabric and have been designed to excel in distributed environments where eventual consistency and asynchronous communication are defaults.

In order to evolve into a fully distributed architecture that exploits microservices, we need to consider the impact on data and not treat it as accidental complexity. Microservice development using traditional object mapping tools that build bespoke data snowflakes do not take into account data replication, consistency, and the larger problem of enterprise data management. When working to ensure the health of data within a microservices architecture, there are a number of classic approaches that can work including data virtualization and caching. Taking the architecture one step further, we can all but reduce the need for data gravity at the microservice and only concern ourselves with data in motion by building reactive event driven services that operate on streams of data. More to come on this topic in part two.
