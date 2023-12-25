# Trip chain optimization for vehicle sharing company: Facing Reserved order and Real time order

## Table of Contents
- [Background and Problem Statement](#background-and-problem-statement)
- [Methodology](#methodology)
  - [Phase one model](#phase-one-model)
  - [Phase two greedy algorithm](#phase-two-greedy-algorithm)
- [Example and Applications](#example-and-applications)
- [Comments](#comments)
- [Started](#started)
- [Reference](#reference)

## Background and Problem Statement
In this project, we aim to establish an scheduling system for an autonomous vehicle(refered as AV) sharing company. 
The system will receive orders from customers. And in each order, we will receive information regarding the pickup location, delivery location, pickup time and delivery time.
Based on these information, our system will optimally determine the number of AVs we should dispatched and the trip chain for each AV.

In our system, each AV is dispatched from the depot at the beginning of the day and returns to the depot at the end of the day for maintenance. 
We may receive orders both before and after dispatching the vehicles.
Therefore, We divide our relocation plan problem into two phases to simplify the problem-solving process.

A reserved order is one received before dispatching vehicles, while a real-time order is received after dispatching the vehicles.
In phase one, we handle reserved orders(orders received before dispatching) using operations research methods for optimization.
In phase two, we address real-time orders(orders received after dispatching) using a greedy algorithm.
The reason we don't rerun the optimization in the second phase is that, in real-time scenarios, customers want to know immediately whether we can dispatch an AV to pick them up. 
Therefore, we need a method that allows us to promptly determine whether to accept or decline that order.
In the following section, we will introduce how we find the optimal trip chain in both phase one and phase two. 

## Methodology

In this section, we will introduce our model using in phase one and phase two

### Phase one model

In phase model, we use the model in ##### 引用 to find the optimal trip chain based on reserved input

#### Input data
Our input data comes from received orders, providing information about pickup location, delivery location, pickup time, and delivery time.

#### Set and indices 

- $I$: set of the trip demands
- $i^-$: pickup location for trip $i\in I$
- $i^+$: delivery location for trip $i\in I$
- $t_{i^-}$: start time for trip $i\in I$
- $t_{i^+}$: end time for trip $i\in I$
- $o$: source node
- $d$: sink node
- $A_r = {(i,j)}$
- $A_1 = {(i^-,i^+)|\forall i \in I}$: $A_1$ is a set contains all sevice link $(i^-,i^+)$
- $A_2 = {(i^+,j^-)|\forall (i,j) \in I}$: $A_2$ is a set contains all sevice link $(i^-,i^+)$
- 
