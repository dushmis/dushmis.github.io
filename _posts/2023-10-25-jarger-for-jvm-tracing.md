---
layout: post
title: "Maximizing JVM Performance Monitoring with Jarger and Flamegraph"
categories: java
author:
- Dushyant 
meta: "java jarger"
---


## WIP

I am currently in the process of developing an idea that involves implementing a Java agent capable of intercepting all methods and subsequently publishing traces to Jaeger. While this project is still a work in progress, it's worth noting that [Jaeger](https://www.jaegertracing.io/docs/1.50/) already provides comprehensive information about flamegraphs, spans, scopes, and other OpenTelemetry-related stuff.

One particular challenge I'm contemplating is whether we can treat each class as an independent application or server. Doing so could potentially offer advantages in terms of visualizing dependencies and obtaining more robust statistics.