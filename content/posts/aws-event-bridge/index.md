---
title: "AWS Single-Bus Multi-Account Event Bus with CDK"
slug: "aws-event-bridge-eda-cdk"
date: 2023-01-14
draft: true
editURL: https://github.com/Kmelow/dotslashrun/issues
tags: ["AWS", "event", "EDA", "CDK"]
---

> How to create a single-bus multi-account AWS Event Bridge service for an Event Driven Application using CDK

I will start by explaining the choices used for this project in the [Context](#context) section.

## Context

### EDA (Event Driven Application)

There are different ways of architecting an application:

- EDA
- Monolith

### Bus Tool

It is possible to implement the communication bus using different methods:

- [AWS EventBridge](https://aws.amazon.com/eventbridge/) is a serverless resource and it is the go for tool when relying on AWS resources
- [NATS](https://nats.io/) is the go for tool when working with a Kubernetes cluster
- [Kafka](https://kafka.apache.org/) is a very popular tool, but known for its complexity

In this project I will be using Serverless technologies (AWS EventBridge) because I believe it has the lowest entry barrier
and this resource is quite powerfull while having a reasonable cost.

### Application / Bus Architecture

There are different ways of architecting an event bus within an EDA application, the most commons are:

- Single-bus single-account
- Single-bus multi-account
- Multi-bus multi-account

Generally, I think the single-bus multi-account offers the best separation of concerns between application and event-bus,
it also offers a single point of management for the bus and eliminates some issues with resource limits (when using AWS).
This is why I will be illustrating that configuration for this example.

### IaC

I am interested not only on the bus itself, but also how I can deploy it to different environments, like development, test and production.
So, this requires using a IaC (Infrastructure as Code).
There are different IaC tools available when working with AWS, but I believe CDK is the best option.

I will try and be very explicit when talking about Bus and when talking about CDK

### Project

## CDK Setup

## EventBridge

### Concepts

## References

