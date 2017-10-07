---
layout: post
title: Computing Invariant Subspaces of Irreducible Representations
---

In this post, I'm going to be going over the details of an algorithm, implemented in GAP, to compute the invariant subspaces of a group, along with some background information on the mathematics behind it. 

To start off, given a representation \\(\rho : G \to GL_n(\mathbb C)\\), the character of \\(\rho\\) \\(\chi_\rho : \G \to \mathbb C\\) is the trace of the matrix given by \\(\rho(g)\\) for all \\(g \in G\\).
