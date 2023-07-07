---
layout: default
title: Welcome to Portals
---

# Release Countdown: <span id="countdown"></span>

# Welcome to *Portals*

Find out more about what *Portals* is and how everything fits together with our [Talks & Papers](/talks-&-papers), or at the project's [GitHub repository](https://github.com/portals-project). Make sure to also try out our new [Playground](https://www.portals-project.org/playground/).

## News

* 4 July 2023 - **<span class="portals">Release countdown.</span>** We are planning to release a first version of *Portals* on **<span class="portals">21 August 2023</span>**. Stay tuned!
* 3 July 2023 - The **[Playground](https://www.portals-project.org/playground/)** is now live! Check out our JavaScript API with some examples in your browser.
* 5 June 2023 - Our demonstration paper "Portals: A Showcase of Multi-Dataflow Stateful Serverless" was accepted at **[VLDB 2023](https://vldb.org/2023)** in Vancouver, Canada, this August/September.
* 4 April 2023 - See you at Scala Days Madrid 2023! We will present our work on **[Crossing the Boundaries of Stateful Streaming and Actors Using Serverless Portals](https://scaladays.org/madrid-2023/crossing-the-boundaries-of-stateful-streaming-and-actors-using-serverless-portals)** at the **[Scala Days](https://scaladays.org/)** conference in Madrid, Spain, this September.
* 2 December 2022 - We presented our paper "Portals: An Extension of Dataflow Streaming for Stateful Serverless" at **[Onward! 2022](https://2022.splashcon.org/track/splash-2022-Onward-papers)** in Auckland, New Zealand. The paper is available on **[ACM DL](https://dl.acm.org/doi/abs/10.1145/3563835.3567664)**, and the presentation is now live on **[YouTube](https://www.youtube.com/watch?v=LyLNjtENti4)**. We also posted a blog post about the trip **[here](/blog/onward-splash-22)**.

## Cite Our Work

If you want to cite our work, please consider citing the following publication:

* Jonas Spenger, Paris Carbone, and Philipp Haller. 2022. Portals: An Extension of Dataflow Streaming for Stateful Serverless. In Proceedings of the 2022 ACM SIGPLAN International Symposium on New Ideas, New Paradigms, and Reflections on Programming and Software (Onward! ’22), December 8ś10, 2022, Auckland, New Zealand. ACM, New York, NY, USA, 19 pages. https://doi.org/10.1145/3563835.3567664


<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/10.7.2/styles/stackoverflow-light.min.css" integrity="sha512-cG1IdFxqipi3gqLmksLtuk13C+hBa57a6zpWxMeoY3Q9O6ooFxq50DayCdm0QrDgZjMUn23z/0PMZlgft7Yp5Q==" crossorigin="anonymous" />

<script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/10.7.1/highlight.min.js" integrity="sha512-d00ajEME7cZhepRqSIVsQVGDJBdZlfHyQLNC6tZXYKTG7iwcF8nhlFuppanz8hYgXr8VvlfKh4gLC25ud3c90A==" crossorigin="anonymous"></script>
<script>hljs.highlightAll();</script>

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