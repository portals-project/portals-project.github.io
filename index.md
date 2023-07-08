---
layout: default
title: Welcome to Portals
---

### Release Countdown: <span id="countdown"></span>

# Welcome to *Portals*

Portals is a framework written in Scala under the **Apache 2.0 license** for stateful serverless applications. It provides a Scala and a JavaScript API, its source code is available on [GitHub](https://github.com/portals-project/portals). 

At its core, Portals unifies the Distributed Dataflow Streaming Model and the Actor Model, providing unparalleled flexibility and data-parallel processing capabilities with strong guarantees. The framework's programming model is tailored for edge stateful serverless processing, and provides the following key features:

1. **Multi-Dataflow Applications.** Multiple stateful dataflow streaming pipelines can dynamically be composed together on top of atomic streams, a transactional type of data streams.
2. **Inter-Dataflow Services.** The Portal abstraction binds dataflow pipelines together to create and expose reusable services. This enables a request-reply type of communication between pipelines by providing serverless access to remote operator states on top of a continuation-style execution.
3. **Decentralized Cloud and Local Execution.** The decentralized runtime can be executed on, and across, cloud and edge devices, whilst still providing end-to-end exactly-once processing guarantees.

Find out more about what *Portals* is and how everything fits together with our [Talks & Papers](/talks-&-papers), or at the project's [GitHub repository](https://github.com/portals-project). We also have an introductory [Tutorial](/tutorial) for the Portals framework. Make sure to also try out our new [Playground](https://www.portals-project.org/playground/) which runs in the browser. Find out more about the project's team [here](/team).

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

### News

* 4 July 2023 - We are planning to release a first version of *Portals* on **[21 August 2023](https://github.com/portals-project)**. Stay tuned!
* 3 July 2023 - The **[Playground](https://www.portals-project.org/playground/)** is now live! Check out our JavaScript API with some examples in your browser.
* 5 June 2023 - Our demonstration paper "Portals: A Showcase of Multi-Dataflow Stateful Serverless" was accepted at **[VLDB 2023](https://vldb.org/2023)** in Vancouver, Canada, this August/September.
* 4 April 2023 - See you at Scala Days Madrid 2023! We will present our work on **[Crossing the Boundaries of Stateful Streaming and Actors Using Serverless Portals](https://scaladays.org/madrid-2023/crossing-the-boundaries-of-stateful-streaming-and-actors-using-serverless-portals)** at the **[Scala Days](https://scaladays.org/)** conference in Madrid, Spain, this September.
* 2 December 2022 - We presented our paper "Portals: An Extension of Dataflow Streaming for Stateful Serverless" at **[Onward! 2022](https://2022.splashcon.org/track/splash-2022-Onward-papers)** in Auckland, New Zealand. The paper is available on **[ACM DL](https://dl.acm.org/doi/abs/10.1145/3563835.3567664)**, and the presentation is now live on **[YouTube](https://www.youtube.com/watch?v=LyLNjtENti4)**. We also posted a blog post about the trip **[here](/blog/onward-splash-22)**.

### Cite Our Work

If you want to cite our work, please consider citing the following publication:

* Jonas Spenger, Paris Carbone, and Philipp Haller. 2022. Portals: An Extension of Dataflow Streaming for Stateful Serverless. In Proceedings of the 2022 ACM SIGPLAN International Symposium on New Ideas, New Paradigms, and Reflections on Programming and Software (Onward! ’22), December 8-10, 2022, Auckland, New Zealand. ACM, New York, NY, USA, 19 pages. https://doi.org/10.1145/3563835.3567664

```bibtex
@inproceedings{SpengerCH22,
  author = {Jonas Spenger and Paris Carbone and Philipp Haller},
  title = {Portals: An Extension of Dataflow Streaming for Stateful Serverless},
  year = {2022},
  booktitle = {Proceedings of the 2022 {ACM} {SIGPLAN} International Symposium on New Ideas, New Paradigms, and Reflections on Programming and Software, Onward! 2022, Auckland, New Zealand, December 8-10, 2022},
  editor = {Christophe Scholliers and Jeremy Singer},
  pages = {153--171},
  publisher = {Association for Computing Machinery},
  address = {New York, NY, USA},
  url = {https://doi.org/10.1145/3563835.3567664},
  doi = {10.1145/3563835.3567664},
  isbn = {9781450399098},
  location = {Auckland, New Zealand},
  series = {Onward! 2022},
}
```

<!-- release countdown script -->
<script>
let date = new Date("Aug 21, 2023").getTime();
let x = setInterval(function() {
  let now = new Date().getTime();
  let diff = date - now;
  let days = Math.floor(diff / (1000 * 60 * 60 * 24));
  let hours = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
  let minutes = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60));
  let seconds = Math.floor((diff % (1000 * 60)) / 1000);
  document.getElementById("countdown").innerHTML = days + "d " + hours + "h "
  + minutes + "m " + seconds + "s ";
}, 1000);
</script>