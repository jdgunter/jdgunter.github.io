---
layout: post
title: Constructing Integer Programs from Core Points
---

In order to test different strategies for solving integer programming problems, we require some example programs that we can test methods on. In this post, I'll be examining a method we can use to generate infeasible symmetric integer programming problems, as well as working through an example computation. First, I'll go over some definitions we'll need to know before tackling the method itself.

## Definitions

__Orbit Polytope:__ Let \\(G \leq S_n\\) be a permutation group. The orbit polytope of \\(G\\) on a point \\(z \in \mathbb Z^n\\) is the convex hull of \\( {gz : g \in G } \\). Here the group action is on position, meaning it permutes the coordinates of the vector. For example, given \\(g = (1,2), z = (1,2,3,4)\\), \\(gz = (2,1,3,4)\\).

__Core Point:__ Let \\(G \leq S_n\\). A point \\( z \in \mathbb Z^n\\) is a core point for \\(G\\) if and only if the orbit polytope conv(\\(Gz\\)) is lattice-free. That is, conv(\\(Gz\\))\\(\cap \mathbb Z^n = \\)conv(\\(Gz\\)).
