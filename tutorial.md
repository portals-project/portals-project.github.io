---
layout: default
title: Tutorial
---

# Tutorial

This is a short tutorial for the Portals framework based on examples. It is intended to be a quick introduction, and guide you through the basic concepts of the framework. The following topics are covered:

1. **Introduction.** A short introduction to the Portals framework.
1. **Project Setup.** How to setup a project using the Portals framework, and how to execute the examples.
1. **Hello World.** The canonical hello world example to get started.
1. **Workflows and Streams.** Introducing the main abstractions such as streams, workflows, generators, and sequencers.
1. **The Portal Service Abstraction.** The Portal service abstraction for exposing workflows as services.
1. **Portals Overview / Cheat Sheet.** A quick overview of the Portals API.
1. **Whats Next?** A list of resources for further reading.

## Introduction

Portals is a framework written in Scala under the **Apache 2.0 license** for stateful serverless applications. It provides a Scala and a JavaScript API, its source code is available on [GitHub](https://github.com/portals-project/portals).

At its core, Portals unifies the Distributed Dataflow Streaming Model and the Actor Model, providing unparalleled flexibility and data-parallel processing capabilities with strong guarantees. The framework's programming model is tailored for edge stateful serverless processing, and provides the following key features:

* *Multi-dataflow applications*: Dynamically connecting multiple dataflow streaming pipelines together for compositions of stateful serverless applications.
* *Inter-dataflow services*: The Portal abstraction provides a service interface for binding dataflow pipelines together.
* *Decentralized cloud and local execution*: The runtime can be executed on, and across, cloud and edge devices.
* *Atomic streams*: A transactional type of data stream which enables the end-to-end exactly-once processing guarantees for applications.

### Portals and PortalsJS

Portals comes with a Scala (portals core) and JavaScript (PortalsJS) API. The examples here are presented with the Scala API. The PortalsJS API is very similar with small exceptions due to the inherent differences between Scala and JavaScript. Many of the examples here exist also on the JavaScript based [Portals Playground](/playground/) where they can be executed in the browser.

## Project Setup

To use Portals in your project, add the following dependency to your `build.sbt` file:

```scala
libraryDependencies += "org.portals-project" %% "portals-core" % "0.1.0-RC1"
```

A project setup with instructions for executing the hello world example is available at [https://github.com/portals-project/Hello-World](https://github.com/portals-project/Hello-World).

## Hello World

```scala
import portals.api.dsl.DSL.*
import portals.system.Systems

object HelloWorld extends App:
  // A Portals Application with the name "HelloWorld"
  val app = PortalsApp("HelloWorld"):

    // A generator for generating a single event: "Hello, World!"
    val generator = Generators.fromList[String](List("Hello, World!"))
  
    // A workflow that processes the generated event stream
    val workflow = Workflows[String, String]()
      // Set the generated event stream as a source
      .source(generator.stream)
      // Map the events to upper case
      .map(_.toUpperCase)
      // Log the events
      .logger()
      // Put the events in the sink
      .sink()
      // Freeze and build the workflow
      .freeze()

  // Launch and run the application on the Portals test system
  val system = Systems.test()
  system.launch(app)
  system.stepUntilComplete()
  system.shutdown()
```

The example builds a Portals Application that logs a single event: "HELLO, WORLD!". The application consists of a single workflow that consumes a generator. The generator produces a single event ("Hello, World!"), and the workflow processes the event by converting it to upper case, logging it, and emitting it in the sink for others to consume. The application is launched on the Portals test system, and the system is stepped until the workflow is complete; a step on the test system is the processing of an atom by one of the processing units.

**Running the Example.**
To run the example, simply execute the `HelloWorld` application. The example is expected to output "HELLO WORLD!". See [https://github.com/portals-project/Hello-World](https://github.com/portals-project/Hello-World) if there are any issues. The Hello World example in PortalsJS can be found at this [shareable link](https://www.portals-project.org/playground/?code=let%20builder%20%3D%20PortalsJS.ApplicationBuilder(%22HelloWorld%22)%0Alet%20generator%20%3D%20builder.generators.fromArray(%5B%22Hello%2C%20World!%22%5D)%0Alet%20workflow%20%3D%20builder.workflows%0A%20%20.source(generator.stream)%0A%20%20.map(ctx%20%3D%3E%20x%20%3D%3E%20x.toUpperCase())%0A%20%20.logger()%0A%20%20.sink()%0A%20%20.freeze()%0Alet%20app%20%3D%20builder.build()%0Alet%20system%20%3D%20PortalsJS.System()%0Asystem.launch(app)%0Asystem.stepUntilComplete()%0Asystem.shutdown()).

## Workflows and Streams

Portals applications are built using Workflow and Stream abstractions (the Portal abstraction is introduced later). Using these abstractions, workflows can be composed together like microservices to build multi-workflow applications. The atomic streams provide the glue which connect all applications together. The following example shows building a simple multi-workflow application.

### Generators and Multi-Workflow Applications

```scala
import portals.api.dsl.DSL.*
import portals.system.Systems

object MultiWorkflow extends App:
  // A Portals application as a composition of multiple workflows:
  // generator => workflow1 =>|=> workflow2
  //                          |=> workflow3
  val app = PortalsApp("MultiWorkflow"):
    // A generator that produces a stream of integers from 0 to 128 grouped into
    // atoms of size 8
    val generator = Generators.fromRange(0, 128, 8)

    // A workflow which multiplies each number by 2
    val workflow1 = Workflows[Int, Int]()
      .source(generator.stream)
      .map(x => x * 2)
      .logger("workflow 1: ")
      .sink()
      .freeze()

    // A workflow which multiplies each number by itself
    val workflow2 = Workflows[Int, Int]()
      // consume the stream from workflow1
      .source(workflow1.stream)
      .map(x => x * x)
      .logger("workflow2: ")
      .sink()
      .freeze()

    // A workflow which multiplies each number by 10
    val workflow3 = Workflows[Int, Int]()
      // consume the stream from workflow1
      .source(workflow1.stream)
      .map(x => x * 10)
      .logger("workflow3: ")
      .sink()
      .freeze()

  val system = Systems.test()
  system.launch(app)
  system.stepUntilComplete()
  system.shutdown()
```

Although the example is simple, it shows how we can compose together multiple workflows, an important pattern for building stateful serverless applications as microservices. 

**Composition of Workflows.**
The core idea of composition is that all processing units consume and produce atomic streams. These streams can be used to connect the processing units together. In this example, the generator produces a stream of integers, which is consumed by workflow1. The output of workflow1 is consumed by both workflow2 and workflow3. A stream of a processing unit is usually accessed as a field as here `workflow.stream`.

### Sequencers and Cycles

A more complicated application uses also the **sequencer** abstraction, which allows sequencing a set of atomic streams into a single atomic stream. With the sequencer, we can dynamically form interesting structures such as cyclic graphs across the workflows.

```scala
import portals.api.dsl.DSL.*
import portals.system.Systems

object CyclicWorkflow extends App:
  // A Portals application with a cyclic workflow:
  // generator |=> sequencer => workflow =>|
  //           |<<=======================<<|
  val app = PortalsApp("CyclicWorkflow"):
    val generator = Generators.signal(8)

    // A sequencer that sequences atomic streams of type Int
    val sequencer = Sequencers.random[Int]()

    // A connection from the generator to the sequencer
    val _ = Connections.connect(generator.stream, sequencer)

    // A workflow which consumes the sequencer's stream
    val workflow = Workflows[Int, Int]()
      .source(sequencer.stream)
      .flatMap { x =>
        if x > 0 then List(x - 1)
        else List()
      }
      .logger()
      .sink()
      .freeze()

    // A connection from the workflow back to the sequencer, closing the cycle
    val _ = Connections.connect(workflow.stream, sequencer)

  val system = Systems.test()
  system.launch(app)
  system.stepUntilComplete()
  system.shutdown()
```

The example shows how we can connect a workflow dynamically to consume its own output, forming a cycle. To note is that within a workflow we cannot have any cycles, the workflow itself is an acyclic graph (DAG). In the example, the workflow consumes the number 8, and decrements it by one until the number reaches zero, and consumes the output again connecting its output to the sequencer which it consumes. In this case, the workflow loops 8 times until the number reaches zero.

### Workflows as DAGs of Tasks

So far we have seen only simple workflows as sequences of tasks. Workflows, however, can form complicated DAG structures for processing data. The following example shows how the flow in a workflow can be forked and joined together again.

```scala
import portals.api.dsl.DSL.*
import portals.system.Systems

object DAGWorkflow extends App:
  val app = PortalsApp("DAGWorkflow"):
    val generator = Generators.fromRange(0, 128, 8)

    // A workflow which forms a DAG with two branches:
    // src =>|=> flow1 =>|=> flow3 => logger => sink
    //       |=> flow2 =>|
    val src = Workflows[Int, Int]()
      .source(generator.stream)
    val flow1 = src
      .map(x => x * 2)
    val flow2 = src
      .map(x => -(x * 2))
    val flow3 = flow1
      .union(flow2)
      .logger()
    val workflow = flow3
      .sink()
      .freeze()

  val system = Systems.test()
  system.launch(app)
  system.stepUntilComplete()
  system.shutdown()
```

The workflow DAG is built simply by building flows which can be `union`ed together. Here, two flows, `flow1` and `flow2`, are built from the same source flow `src`, and later merged together with the `union` operator. This is the core way in which DAG structures can be built.

Besides simple `map`, `flatMap` transformations, there are many other transformations for transforming flows. These can be found in the FlowBuilder API and include: `processor`, `filter`, `init`, `identity`, etc. Additional useful transformations are the ones prefixed by `with`: `withName`, `withOnNext`, `withOnAtomComplete`, `withLoop`. etc. These allow modifying the latest specified task in the flow, for example, giving the task a name `withName`, or modifying its onAtomComplete handler `withOnAtomComplete`.

### Stateful, Data-Parallel Workflows

The processing in workflows is stateful and data-parallel, similar to other stateful dataflow streaming systems. In this example we will explore these notions and how they surface in the programming model.

```scala
import portals.api.dsl.DSL.*
import portals.system.Systems
import portals.application.task.PerKeyState

object StatefulWorkflow extends App:
  val app = PortalsApp("StatefulWorkflow"):
    val generator = Generators.fromRange(0, 128, 8)
    val workflow = Workflows[Int, Int]()
      .source(generator.stream)
      // Key the stream into two partitions: even and odd numbers
      .key(x => x % 2)
      .map(x => if x % 2 == 0 then x else -x)
      .processor[Int] { x =>
        // A task which maintains a state of type Int locally for each key
        val state = PerKeyState[Int]("state", 0)
        // Increment the state by `x`
        state.set(state.get() + x)
        // Emit the state, consumed by downstream tasks
        emit(state.get())
      }
      .logger()
      .sink()
      .freeze()

  val system = Systems.test()
  system.launch(app)
  system.stepUntilComplete()
  system.shutdown()
```

This examples shows how we can perform stateful computations on keyed/partitioned streams. Atomic streams and flows are always partitioned, or keyed, that is, they are physically partitioned into disjoint streams over some key space. If the key is not made explicit, then the system will compute the key by a default method. In this example, we explicitly set the key using the `key` method to assign two partitions: one for even numbers and one for odd numbers. In a physical execution, the partitions are executed in parallel (hence *data-parallelism*). The `processor` task is a task which has access to the processor `context` (more on context later). Using this context, the processor can create a `PerKeyState` with the name `"state"` and initial value 0 for integers. The PerKeyState is disjoint over the key space, each unique key has its own unique PerKeyState. The processor increments the state by the incoming numbers, and emits the latest state downstream. The output that we expect from the logger is the sum of these numbers for each partition, indeed the last outputs are "4032" and "-4096", if we add them together we get the sum from 0 to 127.

### Task Contexts and the Task Builder

Thanks to Scala's contextual function types (givens, implicits, etc.), the API hides the notion of Task Contexts from the user. However, it is useful to have seen the contexts, as common errors will involve the context. This example expores contexts and the task builder.

In the previous example we created a processor task which had access to some stateful context, and emitting context. The processor task can also be created using the `TaskBuilder`. 

```scala
import portals.api.builder.TaskBuilder
import portals.api.dsl.DSL.*
import portals.system.Systems

object TaskContextExample extends App:
  val processor1 = TaskBuilder.processor[Int, String] { ctx ?=> x =>
    if x % 2 == 0 then //
      ctx.emit("even")
    else //
      ctx.emit("odd")
  }

  val processor2 = TaskBuilder.processor[Int, String] { x =>
    if x % 2 == 0 then //
      emit("even")
    else //
      emit("odd")
  }

  val mapper = TaskBuilder.map[Int, String] { ctx ?=> x =>
    if x % 2 == 0 then //
      "event"
    else //
      "odd"
  }

  val app = PortalsApp("TaskContextExample"):
    val generators = Generators.fromRange(0, 128, 8)
    val workflow = Workflows[Int, String]()
      .source(generators.stream)
      .task(processor1)
      // .task(processor2)
      // .task(mapper)
      .logger()
      .sink()
      .freeze()

  val system = Systems.test()
  system.launch(app)
  system.stepUntilComplete()
  system.shutdown()
```

The example shows three different task definitions using the `TaskBuilder`. All tasks do the same: they transform incoming integers and output the strings "odd" or "even", if the number is odd or even, respectively. The first, `processor1`, does this through explicitly accessing the tasks context, it calls the `ctx.emit` method. The second, `processor2`, does this implicitly, it calls the `emit` method directly, for which the `emit` method uses an contextual `ctx` object. The third, `mapper`, also has access to a context, however, the `MapTaskContext` does not have an `emit` method. Instead, the `mapper` task returns the string directly, which is then emitted. It is important to understand the various contexts, and their interaction with the DSL. The provided `DSL` removes much of the context boilerplate, however, it is useful to understand the contexts for debugging purposes.

### Splitting Streams

Splittings streams into disjoint sets of streams is a common operation. This example shows how to split atomic streams through predicates.

```scala
import portals.api.dsl.DSL.*
import portals.system.Systems

object SplittingStreams extends App:
  val app = PortalsApp("SplittingStreams"):
    val generator = Generators.fromRange(0, 128, 8)
    // A splitter that consumes the generator which initially is empty
    val splitter = Splitters.empty(generator.stream)
    // Split the stream into even numbers
    val evenStream = Splits.split(splitter, x => x % 2 == 0)
    // And, split the stream into odd numbers
    val oddStream = Splits.split(splitter, x => x % 2 == 1)

    val wfEven = Workflows[Int, Int]()
      .source(evenStream)
      .logger("even: ")
      .sink()
      .freeze()

    val wfOdd = Workflows[Int, Int]()
      .source(oddStream)
      .logger("odd: ")
      .sink()
      .freeze()

  val system = Systems.test()
  system.launch(app)
  system.stepUntilComplete()
  system.shutdown()
```

At this point the example should be self-explanatory: it splits the generators stream into two disjoint streams, one for even numbers and one for odd numbers. The `Splitters` API provides many useful methods for splitting streams. To note is that splits for disjoint predicates can be added dynamically over time, even after the application has been launched. The same is true for sequencers, which is something we will explore in the next example.

### The Portals Registry and Dynamic Applications Runtime

The Portals Registry is an important part of the Portals API. It can be used for connecting new applications with previous applications, through looking up the previous applications in the Registry. 

```scala
import portals.api.dsl.DSL.*
import portals.application.ASTPrinter
import portals.system.Systems

object RegistryApp extends App:
  // An application with a single generator and its stream
  val app1 = PortalsApp("app1"):
    // A generator within "app1" with name "generatorName"
    val generator = Generators("generatorName").fromRange(0, 128, 8)

  // An application which consumes and logs an external stream from "app1"
  val app2 = PortalsApp("app2"):
    // An external stream from "app1" by a generator "generatorName", paths
    // follow the same pattern for all applications
    val externalStream =
      Registry.streams.get[Int]("/app1/generators/generatorName/stream")
    // Log the external stream
    val workflow = Workflows[Int, Int]("workflowName")
      .source(externalStream)
      .logger("app2: ")
      .sink()
      .freeze()

  // An application with a sequencer and a workflow that logs the sequencer
  val app3 = PortalsApp("app3"):
    // A sequencer that sequences atomic streams of type Int
    val sequencer = Sequencers("sequencerName").random[Int]()
    // A workflow which logs the sequencer's stream
    val workflow = Workflows[Int, Int]()
      .source(sequencer.stream)
      .logger("app3: ")
      .sink()
      .freeze()

  // An application that connects the output from the workflow in "app2" to the
  // sequencer in "app3"
  val app4 = PortalsApp("app4"):
    // The external workflow from "app2"
    val externalWorkflow =
      Registry.streams.get[Int]("/app2/workflows/workflowName/stream")
    // The sequencer from "app3"
    val sequencer =
      Registry.sequencers.get[Int]("/app3/sequencers/sequencerName")
    // Connect the workflow to the sequencer
    val _ = Connections.connect(externalWorkflow, sequencer)

  // Print the AST of the applications to find out about the path names
  ASTPrinter.println(app1)
  ASTPrinter.println(app2)
  ASTPrinter.println(app3)
  ASTPrinter.println(app4)

  val system = Systems.test()
  // Launch all applications
  system.launch(app1)
  system.launch(app2)
  system.launch(app3)
  system.launch(app4)
  system.stepUntilComplete()
  system.shutdown()
```

The example shows how various applications can be connected together using the `Registry`. Here, the `Registry` method is the entry-point, from which one can get either streams, sequencers, splitters, or portals. The access to the registry is checked during application submission (`system.launch`), to check that any external references are valid. With the registry, larger ecosystems of microservices can be built over time, such that new microservices can use and connect to the existing ones. What is new in this example is the `ASTPrinter`, which allows us to print the built AST, this can be useful for debugging.

### Remote Applications: Connecting Decentralized Runtimes

TODO.

## The Portal Service Abstraction

The Portal service abstraction enables exposing workflows and tasks as services. With this, portal tasks can communicate directly, similarly to actors, and perform arbitrary stateful computations as do regular processing tasks. The key idea is how Portal Tasks are integrated into workflows as normal tasks, with the difference that Portals Tasks bind to Portals. There are three types of Portal Tasks: Requesting Portal Tasks, Responding Portal Tasks, and Portal Tasks (which is both a requesting and responding portal task). These tasks connect to Portal Services, each Portal Service must have exactly one responding portal task. However, there can be many requesting portal tasks for a portal service. The tasks are integrated into workflows through consuming the regular task input, thus enabling the unification of dataflow with actor-like communication patterns.

```scala
import portals.api.dsl.DSL.*
import portals.api.dsl.ExperimentalDSL.*
import portals.system.Systems

object PortalApp extends App:
  val app = PortalsApp("PortalApp"):
    // A portal with name "portalName" with request type Int and response type
    // String with a key function that extracts requests into even and odd
    // partitions
    val portal = Portal[Int, String]("portalName", x => x % 2)

    // The responding workflow for the portal, the workflow has no input or
    // output
    val respondingWorkflow = Workflows[Nothing, Nothing]()
      // Consumes nothing, empty generator is imported from the experimental DSL
      .source(Generators.empty.stream)
      // A portal responder that replies to requests of type Int with a String,
      // it consumes a flow stream of type Nothing and produces Nothing
      .responder[Nothing, Int, String](
        // The portal which this responder binds to
        portal
      ) {
        // Nothing to handle, as the replier consumes no events
        _ => ???
      } {
        // Handle requests by aggregating and responding
        req =>
          val state = PerKeyState[Int]("state", 0)
          state.set(state.get() + req)
          reply(state.get().toString())
      }
      .sink()
      .freeze()

    // A requesting workflow
    val requestingWorkflow = Workflows[Int, String]()
      .source(Generators.fromRange(0, 128, 8).stream)
      // The requesting portal task, which consumes a flow stream of type Int
      // and produces a flow stream of type String, its request type is Int and
      // the expected response type is String
      .requester[String, Int, String](
        // The portal which this requester binds to
        portal
      ) {
        // Handle flow stream events
        x =>
          // Send a request to the portal, returns a future
          val future = ask(portal)(x)
          // Wait for the future to complete and return the result
          await(future) {
            // Continuation which is called when the future has completed
            future.value match
              case Some(v) =>
                // Emit the result, printed by the consuming logger task
                emit(v)
              case None =>
                ???
          }
      }
      .logger()
      .sink()
      .freeze()

  val system = Systems.test()
  system.launch(app)
  system.stepUntilComplete()
  system.shutdown()
```

The example shows how to create a requesting and a responding portal task, and how to integrate it into the workflows. Writing these programs requires some experience on the best practices, for this we recommend looking at the examples in the [portals-examples](https://github.com/portals-project/portals/tree/main/portals-examples/src/main/scala/portals/examples) directory. Here, we have shown how we can create a stateful responding portal task, which does not consume anything from its regular flow inputs. The requesting portal task, in contrast, consumes a stream of numbers within the workflow, and generates requests for each number. The requesting portal aggregates these in a per-key context, and responds with the latest aggregate value. The example finishes by printing 4032, and 4096, which summed up, gives the sum of the numbers from 0 to 127.

The next example also shows how we can create recursive portal tasks, in this self-recursive example, which keeps requesting the portal service from the portal service itself, until it has reached some final value (0), at which point the replies are unrolled and the final result (0) is printed.

```scala
import portals.api.dsl.DSL.*
import portals.system.Systems

object RecursivePortal extends App:
  val app = PortalsApp("RecursivePortal"):
    val portal = Portal[Int, Int]("portalName")
    val generator = Generators.signal(8)
    val workflow = Workflows[Int, Int]()
      .source(generator.stream)
      .portalTask(
        // The requesting portal connection
        portal
      )(
        // The responding portal connection, same as the requesting one
        portal
      ) {
        // Handle flow stream events
        x =>
          val future = ask(portal)(x)
          await(future) {
            emit(future.value.get)
          }
      } {
        // Handle requests by recursively sending requests to the portal
        req =>
          if req > 0 then
            log.info("requesting: " + req)
            val f = ask(portal)(req - 1)
            await(f) {
              log.info("responding: " + f.value.get)
              reply(f.value.get)
            }
          else reply(req)
      }
      .logger()
      .sink()
      .freeze()

  val system = Systems.test()
  system.launch(app)
  system.stepUntilComplete()
  system.shutdown()
```

The example shows how we can create a recursive portal task, the task both requests to the portal and responds to the same portal. The portal task keeps sending requests to the portal, each time decrementing the number, until the number reaches 0, at which point the replies are unrolled and the final result (0) is emitted and logged. This shows a general pattern for creating recursive communication patterns, for example used for transactional patterns, locking, and many other use cases. Again, we would like to stress, that at first glance this may seem rather messy, however with the right best practices for writing such programs, we can extract the corresponding logic into better reusable structures, as shown in the [portals-examples](https://github.com/portals-project/portals/tree/main/portals-examples/src/main/scala/portals/examples).

To note is that a portal task may connect to multiple different portals, and it may also be stateful, such that the state between the flow event handler and the portal request event handler are shared on a per-key context.

## Portals Overview / Cheat Sheet

**Imports.**
In most examples, we have imported the following:
```scala
// Import useful DSL API syntax
import portals.api.dsl.DSL.*
// Import experimental, but useful, API syntax
import portals.api.dsl.Experimental.*
// Import the systems API, for running the application
import portals.system.Systems
```

**Atomic Streams.**
Atomic streams transport atoms of events. An atom is a transactional group of events: 1) either all events in an atom are processed, or none; 2) atoms are processed exactly-once; and 3) atoms are processed in a serializable schedule (this can be thought of as a single-threaded execution order). Atomic streams form the glue which binds together the portals applications. Atomic streams can be accessed from other abstractions through the `stream` field: `generator.stream`, `sequencer.stream`, `workflow.stream`, etc.

**Generators.**
Atomic streams are generated by generators. The generators provide many useful methods for generating atomic streams. Commonly used ones are: `fromList`, `signal`, `fromListOfLists`, etc. The whole list of generators can be found in the API documentation.

**Sequencers and Connections.**
Sequencers are used to sequence atomic streams, that is, they combine a set of atomic streams into a single atomic stream. A stream can be connected to a sequencer through forming a **Connection**.

**Splitters and Splits.**
Splitters are used to split atomic streams, that is, they split a single atomic stream into a set of atomic streams through provided predicates. A **Splitter** consumes a stream, and new **Split**s can be added to the splitter through the `split` method.

**Workflows and Tasks.**
Workflows are the processing units in Portals. They consist of a DAG of tasks specified when building the workflow. A workflow has an input type and an output type, and starts by consuming from a source (a workflow must consume exactly once source). Tasks can be built either using the methods of the `FlowBuilder`, or by using the `TaskBuilder`/`Tasks`.

**Registry.**
The Registry is used to find workflows, streams, sequencers, splitters, portals, etc. from other applications that are currently launched on the runtime. Using the `Registry`, one can get an external reference, and use it within the application.

**Portals.**
Portals are a service abstraction. A Portal is a service abstraction with a service request type and response type. A portal task binds to a portal either for sending requests to the portal (requesting task), or responding to the portal (responding task), or both (portal task). A portal task also consumes a stream of events from the flow of the workflow, which enables the portal task to unify the request/response communication with the stream transformations in the workflow.

**Portals Applications.**
A Portals Application is a grouping of streams, generators, workflows, etc., and is created by the `PortalsApp` method. Note that only within the scope of a Portals Application can workflows and generators be built using the DSL API. 

**Portals System.**
A Portals System is the runtime for executing Portals Applications. There exists a selection of systems, commonly used ones are `Systems.test()` and `Systems.default()`. Launching events is achieved by calling `system.launch(app)` method. The system can be shutdown by calling `system.shutdown()`.

## What's Next?

This concludes the tutorial. The presented tutorial did not touch on many important concepts for building Portals applications. Importantly, this tutorial has left out many of the details for building larger applications. This will be covered in future tutorials. Here are our suggestions for what to look at next:

* For the eager reader we recommend looking at the [portals-examples](https://github.com/portals-project/portals/tree/main/portals-examples/src/main/scala/portals/examples) in the GitHub repository, which show the current best practices for building Portals applications. The Shopping Cart example is a good starting point.
* The [Portals API](https://portals-project.org/api/) documentation provides a detailed description of the API.
* The [blog posts](https://portals-project.org/blog/) provide more in-depth reviews of examples.
