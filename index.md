---
layout: default
title: Welcome to Portals
---

# Welcome to *Portals*

Find out more about what *Portals* is and how everything fits together with our [Talks & Papers](/talks-&-papers), or at the project's [GitHub repository](https://github.com/portals-project). Make sure to also try out our new [Playground](https://www.portals-project.org/playground/).

## News

* 4 July 2023 - **Release countdown**. We are planning to release a first version of *Portals* on **21 August 2023**. Stay tuned!
* 3 July 2023 - The **[Playground](https://www.portals-project.org/playground/)** is now live! Check out our JavaScript API with some examples in your browser.
* 5 June 2023 - Our demonstration paper "Portals: A Showcase of Multi-Dataflow Stateful Serverless" was accepted at **[VLDB 2023](https://vldb.org/2023)** in Vancouver, Canada, this August/September.
* 4 April 2023 - See you at Scala Days Madrid 2023! We will present our work on **[Crossing the Boundaries of Stateful Streaming and Actors Using Serverless Portals](https://scaladays.org/madrid-2023/crossing-the-boundaries-of-stateful-streaming-and-actors-using-serverless-portals)** at the [Scala Days](https://scaladays.org/) conference in Madrid, Spain, this September.

<h2>Release Countdown: <span class="portals" id="countdown"></span></h2>

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