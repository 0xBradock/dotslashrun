---
title: The Economics of Software Design (J.B. Rainsberger)
slug: the-economics-of-software-design-rainsberger-2019
draft: false
date: 2023-01-07
tags: ["design", "agile", "devternity", "conference"]
---

{{< katex >}}
{{< youtube TQ9rng6YFeY >}}

In this talk Rainsberger talks about 3 values of software that orbits around _business value_:

- Features \\(S\\): Is the primary source of _business value_
- Design \\(\dfrac{dS}{dt}\\): _Refactoring_ that improves the ability to plan. The goal is to __Minimize volatility in marginal cost of features__
- Feedback \\(\dfrac{d^2S}{dt^2}\\): 

## Design

Rainsberger suggests that no matter the effort put in **design** the complexity to add features in the future has a compouding effect.
However, not spending enough time in the begining and stepping over good design practices will lead to a steeper curve.

Good design requires more time in the begining to have the same set of features, but ensures the cost of adding features will remain more controlled.

So, the only reason to not spend enough time on **design** is with a throw away project.

## Feedback

The speaker also demonstrates how **faster feedback** from queueing theory could transform waterfall into agile sooner.

Waterfall steps: R(requirements) => D(design) => C(code) => T(test) => M(maintenance)

- T => C: Throughput is increased when working in small cycles and testing is done before coding (Test First Program)
- D => T => C: Another increase is to add design on the cycle (TDD)
- R => D => T => C: Keeping the customer in the loop prevents working a long time in unwanted product behaviour. Adding the customer in the cycle (BDD)
- BDD followed by maintenance in small cycles (CD)

## Questions

Why TDD is not used enough?

> The payback of new learned skills are unknown, but is usually worthwhile to take it personally

## Thoughts

- The mathematical model he proposed for the values presented makes sense
- It is very interesting the parallel between queues and steadily delivering working value
