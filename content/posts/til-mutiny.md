+++
title = 'TIL: Generating Kafka Message Batches Every Second Using Mutiny'
date = 2026-01-01T10:45:40+01:00
draft = false
+++
First of all : I wrote this article for myself so that I can refer back to it when I need to


For a demo application, I needed to generate a large number of Kafka messages every second. Instead of using the standard Kafka client,
I chose the recommended Quarkus approach: Reactive Messaging Kafka with Mutiny.

The goal was simple: produce faked messages in continuous batches, without manually managing loops. While exploring Mutiny,
I discovered that streams can be merged, which makes it possible to create exactly the behavior I wanted: a “tick” stream triggering 
every second a batch stream that generates multiple messages.

Here’s the example I used:
```
ti.createFrom().ticks().every(Duration.ofSeconds(1))
       .onItem()
       .transformToMultiAndMerge(tick ->
               Multi.createFrom().range(0, 100)
                    .map(e -> AircraftMetrics.generate(generateAircraftId()))
       );

In```
 this snippet, ticks().every(Duration.ofSeconds(1)) produces an item every second. Each tick triggers transformToMultiAndMerge, which generates 100 new items via Multi.createFrom().range(0, 100). Each item is then transformed into a simulated AircraftMetrics object. 
The resulting stream emits a batch of 100 messages every second.
