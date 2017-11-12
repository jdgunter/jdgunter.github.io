---
layout: post
title: Constructing Integer Programs from Core Points
---

In order to test different strategies for solving integer programming problems, we require some example programs that we can test methods on. In this post, I'll be examining a method we can use to generate infinitely many infeasible symmetric integer programming problems, as well as working through an example computation. These constructions will depend primarily on finding core points of groups. I will first discuss what a core point is and how we can find them, before moving on to the construction of the programs.

__Definition:__ A __core point__ of a permutation group \\(G\\) is a point \((z \in \mathbb Z^n\\) such that the orbit polytope \\(\textrm{conv}(Gz)\\) contains no interior integer points, i.e. \\(\textrm{conv}(Gz) \cap \mathbb Z^n = \textrm{conv}(Gz)\\).

To construct an infeasible integer programming problem with high symmetry the orbit polytope of a core point gets us most of the way there. By definition it will contain no integer points aside from the polytope's vertices, and since the vertices are constructed from a single point permuted by a group it will have the symmetry of the group. The main tool we'll be using to find these core points is __Proposition 5.37__, from Thomas Rehn's thesis Exploring Core Points for Fun and Profit [1].

__Proposition 5.37:__ Let \\(G \leq S_n\\) be a primitive permutation group, and let \\(V \subset \mathbb R^n\\) be a rational invariant subspace. If \\(e^(1)\\) has globally minimal projection onto \\(V\\), then there are infinitely many core points in layer one. The corresponding orbit polytopes are simplices.


