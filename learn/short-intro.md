---
layout: default
title: A Short Introduction to Portals
---

# A Short Introduction to Portals

Portals is a framework written in Scala under the **Apache 2.0 license** for stateful serverless applications. It provides a Scala and a JavaScript API, its source code is available on [GitHub](https://github.com/portals-project/portals). 

At its core, Portals unifies the Distributed Dataflow Streaming Model and the Actor Model, providing unparalleled flexibility and data-parallel processing capabilities with strong guarantees. The framework's programming model is tailored for edge stateful serverless processing, and provides the following key features:

1. **Multi-Dataflow Applications.** Multiple stateful dataflow streaming pipelines can dynamically be composed together on top of atomic streams, a transactional type of data streams.
2. **Inter-Dataflow Services.** The Portal abstraction binds dataflow pipelines together to create and expose reusable services. This enables a request-reply type of communication between pipelines by providing serverless access to remote operator states on top of a continuation-style execution.
3. **Decentralized Cloud and Local Execution.** The decentralized runtime can be executed on, and across, cloud and edge devices, whilst still providing end-to-end exactly-once processing guarantees.

### Guarantees

* *End-to-End Exactly-Once Processing*: Portals provides end-to-end exactly-once processing guarantees for applications. This guarantee holds for arbitrary failures.
* *Transactional Processing*: Atoms are processed transactionally with ACID guarantees in a serializable schedule.
* *Static Compile-Time Correctness*: The Portals compiler statically checks the correctness of applications at compile-time for most common application bugs.

### Features

* *Real-Time Data Processing*: Events are processed at real-time low-latency speeds.
* *Parallelism*: Portals provides data-parallelism, pipeline-parallelism and task-parallelism for applications through stateful per-key execution semantics and pipelined execution.
* *Elastic Scalability, Serverless Execution*: Applications are automatically, elastically scaled up and down with the data demand, fully managed by the runtime.
* *Dynamic Service Composition*: Services can be composed together dynamically at runtime, forming dynamic connections between applications.
* *Decentralized Service Composition*: The Portals API and runtime support composing applications across decentralized runtimes.
* *Cyclic Composition of Applications*: Applications can be composed together as cyclic microservices.
* *Dynamic Actor-Like Communication:* The Portals service abstraction enables dynamic communication between applications, reminiscent of the actor model, integrated with futures.
