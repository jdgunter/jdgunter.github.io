---
layout: post
title: Constructing Integer Programs from Core Points
---

In order to test different strategies for solving integer programming problems, we require some example programs that we can test methods on. In this post, I'll be examining a method we can use to generate infeasible symmetric integer programming problems, as well as working through an example computation. First, I'll go over some definitions we'll need to know before tackling the method itself.

## Definitions

__Core Point:__ Let \\(G \leq S_n\\) be a permutation group. A point \\( z \in \mathbb Z\\) is a core point for \\(G\\) if and only if the orbit polytope conv(\\(Gz\\)) is lattice-free. That is, conv(\\(Gz\\))\\(\cap \mathbb Z = \\)conv(\\(Gz\\)).
