---
layout: post
title: Constructing Integer Programs from Core Points
---

In order to test different strategies for solving integer programming problems, we require some example programs that we can test methods on. In this post, I'll be examining a method we can use to generate infinitely many infeasible symmetric integer programming problems, as well as working through an example computation. These constructions will depend primarily on finding core points of groups. I will first discuss what a core point is and how we can find them, before moving on to the construction of the programs.

__Definition:__ A __core point__ of a permutation group \\(G\\) is a point \((z \in \mathbb Z^n\\) such that the orbit polytope \\(\textrm{conv}(Gz)\\) contains no interior integer points, i.e. \\(\textrm{conv}(Gz) \cap \mathbb Z^n = \textrm{conv}(Gz)\\).

To construct an infeasible integer programming problem with high symmetry the orbit polytope of a core point gets us most of the way there. By definition it will contain no integer points aside from the polytope's vertices, and since the vertices are constructed from a single point permuted by a group it will have the symmetry of the group. Unfortunately, computing core points is a non-trivial task - luckily, we can find some tools for this in Thomas Rehn's thesis Computing Core Points for Fun and Profit [1]. I won't go over the proofs of these statements, as they require too much background information to state succinctly. Before the statements, we need to define a term:

__Definition:__ A point \\(z \in \mathbb Z^n\\) has __globally minimal projection__ with respect to \\(V \subseteq \mathbb R^n \\) if \\(\\|z\mid_V\\| \leq \\|z'\mid_V\\|\\) for all \\(z' \in \textrm{aff}(Gz) \cap \mathbb Z^n\\).

Next, we have a lemma providing the backbone of our core point constructions.

__Lemma 5.19:__ Let \\(G \leq S_n\\), and \\(\mathbb R^n = span\mathbb 1 \oplus V \oplus W\\) be a decomposition of \\(\mathbb R^n\\) into invariant subspaces. Let \\(z^{(0)} \in \mathbb Z^n\\) have globally minimal projection with regard to \\(V\\) and be a core point for \\(\textrm{Stab$\_G$}(z^{(0)}\mid_V)\\). Let \\(w \in W \cap \mathbb Z^n\\) such that \\(\textrm{Stab_$G$}(z^{(0)}\mid_V) \leq \textrm{Stab$\_G$}(w)\\). Then for all \\(m \in \mathbb Z\\), the polytope \\(P_m := \textrm{conv}G(z^{(0)} + mw)\\) is lattice-free.

__Proposition 5.37:__ Let \\(G \leq S_n\\) be a primitive permutation group, and let \\(V \subset \mathbb R^n\\) be a rational invariant subspace. If \\(e^{(1)}\\) has globally minimal projection onto \\(V\\), then there are infinitely many core points in layer one. The corresponding orbit polytopes are simplices.


