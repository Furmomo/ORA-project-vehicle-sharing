# Trip chain optimization for vehicle sharing company: Facing Reserved order and Real time order

## Table of Contents
- [Background and Problem Statement](#background-and-problem-statement)
- [Methodology](#methodology)
  - [Phase one model](#phase-one-model)
  - [Phase two greedy algorithm](#phase-two-greedy-algorithm)
- [Example](#example)
- [Numerical Study](#numerical_study)
- [Conclusion](#conclusion)
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

- $I$: set of the orders
- $i^-$: pickup location for order $i\in I$
- $i^+$: delivery location for order $i\in I$
- $t_{i^-}$: start time for order $i\in I$
- $t_{i^+}$: end time for order $i\in I$
- $o$: source node
- $d$: sink node
- $A_r = \lbrace (i,j)| i,j \in I, i \  can \ reach \ in \ j\rbrace$: $A_r$ is a set contains all reachable link
- Service link $(i^-,i^+)$: Each order $i$ is served by service link
- Relocation link $(i^+,j^-)$: after deliver at delivery node $i^+$, AV needs to be relocated to pickup node $j^-$ for next order
- $A_1 = \lbrace (i^-,i^+)|\forall i \in I \rbrace$: $A_1$ is a set that contains all sevice link $(i^-,i^+)$
- $A_2 = \lbrace (i^+,j^-)|\forall (i,j) \in I \rbrace$: $A_2$ is a set that contains all reachable relocation link $(i^+,i^-)$
- $A= A_1 \cup A_2 \cup \lbrace (o,i^-) \rbrace _{i\in I} \cup \lbrace (i^+,d) \rbrace _{i\in I} \cup \lbrace (o,d) \rbrace $, A is a set that conatins all possible links in our relocation problem

#### Parameter

- $F$: Maximum number of AV we can dispatch
- $f$: Fleet cost per vehicle includes expenses related to AV maintenance and refueling
- $d_i$: The dispatch cost refers to the expense associated with initially dispatch an AV to $i^-$ or collect an AV at the end from $i^+$
- $d_{i,j}$: The relocation cost represents the driving expense from $i^+$ to $j^-$ where $(i^+,j^-)\in A_2$. And it contains fuel usage or toll fees
- $l_i$: The cost of losing order $i$
- $c_{ij}$: total cost from location $i$ to location $j$
- $m_{ij}$: link capacity for link $(i,j)$.  $m_{ij}=1$ if $(i,j)\in A \backslash \lbrace (o,d) \rbrace$ and $m_{ij}=F$ if $(i,j)\in (o,d)$

#### Decision variable

- $y_{ij}$ is a binary variable and $y_{i,j}=1$ if link $(i,j)$ is used by an AV; otherwise, $y_{ij}$ is zero

#### Objective

Our objective is minimize the total cost of our system, and the objective function is as following equation

- $min_{y_{ij}}\sum_{((i,j)\in A)}c_{ij}y_{ij}$
- $c_{ij}=f+d_i$ if $\lbrace i=o, j\neq d \rbrace \ or\ \lbrace i\neq o, j=d \rbrace$
- $c_{ij}=d_{i,j}$ if $((i,j) \in A_2)$
- $c_{ij}=d_{i,j}-l_i$ if $((i,j)\in A_1)$

#### Assumption

- Travel time is deterministic and we don't need to refuel our AV between the trip

#### Constraint

1. $t_{j^-}-t_{i^+}\ge$ relocation_time $(i,j)$
2. $\sum_{j}y_{ij}\leq \sum_{j}m_{ij}$
3. $\sum_{j\in I\backslash \lbrace o \rbrace} y_{ij}=F$, $i=o$
4. $\sum_{j\in I\backslash \lbrace d \rbrace} y_{ji}=F$, $i=d$

- In constraint 1, we want to let our AV arrive before trip's pickup time given that the relocation time is deterministic. Therefore, our relocation time in $(i^+,j^-)$ must less than the delivery time of order j minus pickup time of order i
- In constraint 2, we constraint link capacity. For each link, we only allow one AV use it because we only need to serve each demand once.
- Constraint 3 and constraint are balance constraint, we use these two constraints to make sure that number of AVs we collect at the end is same as number of AVs we dispatch at the beginning

After solving above model, we can optimally determined number of AV we should dispatch and the trip chain for each AV based on reserved order.

### Phase two greedy algorithm

- In phase two, as we deal with real-time orders, efficiency becomes crucial. Therefore, we employ a greedy algorithm to address the problem. While this approach may not yield an optimal trip chain, it allows us to quickly obtain a suboptimal trip chain.
- Our greedy algorithm is straightforward: we identify all feasible solutions based on the current trip chain and then insert the new order into the trip chain in a way that minimally increases the cost
- In the later empirical results, we will demonstrate the efficiency of our algorithm and illustrate that the optimality gap between our algorithm and the optimization is not substantial

## Example

In this section, we will present a simple example to facilitate a better understanding of our entire problem and methodology

- In our example, we have five reserved orders, and the information is presented in the following figure. We then utilize the information from these five orders and execute the phase one model, resulting in the following optimal trip chains:
  - Car 1’s trip chain: start --- order 2 --- order 5 --- order 1 --- end
  - Car 2’s trip chain: start --- order 3 --- order 4 --- end
 
- Subsequently, in real-time, we receive order 6, and we identify two possible ways to insert this order:
  - Car 1’s trip chain: start --- order 2 --- order 6 --- order 5 --- order 1 --- end
  - Car 2’s trip chain: start --- order 3 --- order 6 --- order 4 --- end
- Finanly, we will choose the trip chain that minimally increases the cost




