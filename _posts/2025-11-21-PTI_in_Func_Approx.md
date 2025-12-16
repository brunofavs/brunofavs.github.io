---
layout: post
title: Why does the Policy Improvement Theorem not hold true in function approximation
date: 2025-11-21 11:12:00-0400
description: 
tags: "theory" 
images:
  compare: true
  slider: true
---
*Page 254  Sutton*

	"*The root cause of the difficulties with the discounted control setting is that with
	function approximation we have lost the policy improvement theorem (Section 4.2). It is
	no longer true that if we change the policy to improve the discounted value of one state
	then we are guaranteed to have improved the overall policy in any useful sense. That
	guarantee was key to the theory of our reinforcement learning control methods. With
	function approximation we have lost it!*"

[Stack Exchange Useful Link](https://ai.stackexchange.com/questions/45071/why-policy-improvement-theorem-cant-be-applied-in-case-of-function-approximatio)

Equation (4.7) from chapter *4.2* states that given $\pi$ and $\pi'$  as any pair of deterministic policies such that, for all $s \in \mathcal{S}$ :

$$ q_{\pi}(s,\pi'(s)) \ge v_{\pi}(s)$$

Must mean that $\pi'$ is a strict improvement over $\pi$. A particular case occurs when all state values are equal but one. This was the basis of many tabular control methods. Tabular controls methods can improve individual states, so they allow *PIT* to be applied.

### When using function approximation...

... any update in the weights modifies all states. Which means some get better, others get worse. It is a trade-off. Hence, the name function **approximation**. If we cannot assure that all state values are *at least* equal, the policy improvement theorem does not apply. Note that *PIT* also requires policies to be deterministic. Policies learned by Function approximation can both be deterministic or stochastic, so this particular condition can still be applied.

# Solution

Later in the same page...

	*In Chapter 13 we introduce an
	alternative class of reinforcement learning algorithms based on parameterized policies,
	and there we have a theoretical guarantee called the “policy-gradient theorem” which
	plays a similar role as the policy improvement theorem.*

This is the class of algorithms I'm eager to learn about. These include all the Actor-Critic methods such as **SAC**. SAC is a state of the art algorithm used extensively in Robotics-RL research!
