+++
title = 'TIL: Creating Periodic Streams with Mutiny in Quarkus'
date = 2026-01-01T10:45:40+01:00
draft = false
+++
First of all : I wrote this article for myself so that I can refer back to it when I need to

For a demo application, I needed to generate a large number of Kafka messages every second. Instead of using the standard Kafka client,
The goal was simple: produce faked messages in continuous batches, without manually managing loops.

In Quarkus, the recommended way to produce messages to Kafka is through Reactive Messaging. According to the documentation, you define a producer method annotated with `@Outgoing`. This method does not return a single message, but a `Multi<T>` object, which represents a stream of messages

While exploring the documentation,I discovered that streams can be **merged**, which makes it possible to create a stream creating every second a batch stream items that generates multiple messages

Here’s the example I used:
```
ti.createFrom().ticks().every(Duration.ofSeconds(1))
       .onItem()
       .transformToMultiAndMerge(tick ->
               Multi.createFrom().range(0, 100)
                    .map(e -> AircraftMetrics.generate(generateAircraftId()))
       );

```

In this snippet, `ticks().every(Duration.ofSeconds(1))` produces an item every second. Each tick triggers `transformToMultiAndMerge`, which generates 100 new items via `Multi.createFrom().range(0, 100)`.

Each item is then transformed into a simulated AircraftMetrics object. 

The resulting stream emits a batch of 100 messages every second.
