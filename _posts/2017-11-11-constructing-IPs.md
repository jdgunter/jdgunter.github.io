---
layout: post
title: Constructing Integer Programs from Core Points
---

In order to test different strategies for solving integer programming problems, we require some example programs that we can test methods on. In this post, I'll be examining a method we can use to generate infinitely many infeasible symmetric integer programming problems, as well as working through an example computation. These constructions will depend primarily on finding core points of groups. I will first discuss what a core point is and how we can find them, before moving on to the construction of the programs.

__Definition:__ A __core point__ of a permutation group \\(G\\) is a point \((z \in \mathbb Z^n\\) such that the orbit polytope \\(\textrm{conv}(Gz)\\) contains no interior integer points, i.e. \\(\textrm{conv}(Gz) \cap \mathbb Z^n = \textrm{conv}(Gz)\\).

To construct an infeasible integer programming problem with high symmetry the orbit polytope of a core point gets us most of the way there. By definition it will contain no integer points aside from the polytope's vertices, and since the vertices are constructed from a single point permuted by a group it will have the symmetry of the group. Unfortunately, computing core points is a non-trivial task - luckily, we can find some tools for this in Thomas Rehn's thesis Computing Core Points for Fun and Profit [1]. I won't go over the proofs of these statements, as they require too much background information to state succinctly. Before the statements, we need to define a term:

__Definition:__ A point \\(z \in \mathbb Z^n\\) has __globally minimal projection__ with respect to \\(V \subseteq \mathbb R^n \\) if \\(\\|z\|_V\\| \leq \\|z'\|_V\\|\\) for all \\(z' \in \textrm{aff}(Gz) \cap \mathbb Z^n\\).

Next, we have a lemma providing the backbone of our core point constructions, along with a corollary and some experimental results that will allow us to make use of the lemma.

__Lemma 5.19:__ Let \\(G \leq S_n\\), and \\(\mathbb R^n = \textrm{span}\textbf{1} \oplus V \oplus W\\) be a decomposition of \\(\mathbb R^n\\) into invariant subspaces. Let \\(z^{(0)} \in \mathbb Z^n\\) have globally minimal projection with respect to \\(V\\) and be a core point for \\(\textrm{Stab$\_G$}(z^{(0)}\|_V)\\). Let \\(w \in W \cap \mathbb Z^n\\) such that \\(\textrm{Stab$\_G$}(z^{(0)}\|_V) \\) \\(\leq \textrm{Stab$\_G$}(w)\\). Then for all \\(m \in \mathbb Z\\), the polytope \\(P_m := \textrm{conv}G(z^{(0)} + mw)\\) is lattice-free, and \\(z^{(0)} + mw\\) is a core point for \\(G\\).

What this lemma provides us is a way to compute an infinite series of core points, given a number of conditions. We need to able to find a decomposition of \\(R^n\\) into at least three invariant subspaces, and then find an integer point with globally minimal projection on \\(V\\). After that, we need to find a vector \\(w \in W\\) where the stabilizer subgroup of \\(G\\) with respect to \\(z^{(0)}\|_V\\) is a subgroup of the stabilizer subgroup with respect to \\(w\\). However, all of these work is non-trivial, and we want to be able to find a more straightforward method, which brings us to the next result.

__Corollary 5.36:__ Let \\(G\\) be a primitive group and \\(V\\) a rational invariant subspace. It holds that \\(\textrm{Stab$_G$}(e^{(1)}) = \textrm{Stab$\_G$}(z^{(0)}\|_V)\\).

Combining these, we get a proposition giving us an easy method to find our core points:

__Proposition 5.37:__ Let \\(G \leq S_n\\) be a primitive permutation group, and let \\(V \subset \mathbb R^n\\) be a rational invariant subspace. If \\(e^{(1)}\\) has globally minimal projection onto \\(V\\), then there are infinitely many core points in layer one. The corresponding orbit polytopes are simplices.

__Proof Sketch:__ Let \\(G \leq S_n\\) be primitive, \\(\mathbb R^n = \textrm{span}\textbf{1}\oplus V \oplus W\\). By Corollary 5.36, \\(\textrm{Stab$\_G$}(e^{(1)}\|_V) = \textrm{Stab$_G$}(e^{(1)}) = \textrm{Stab$\_G$}(e^{(1)}\|_W)\\), and so \\(\textrm{Stab$\_G$}(e^{(1)}\|_V) \leq \textrm{Stab$\_G$}(e^{(1)}\|_W)\\). \\(G\\) being primitive implies that \\(e^{(1)}\|_W \neq 0\\), so we can find a \\(w := ke^{(1)}\|_W\\) such that \\(w \in W \cap \mathbb Z^n\\). Then by Lemma 5.19, for all \\(m \in \mathbb Z\\), \\(e^{(1)} + mw\\) is a core point.

It has been computationally verified that for all primitive groups of degree \\(n \leq 127\\), \\(e^{(1)}\\) is globally minimally for at least one invariant subspace, and for most it is minimal for all. Therefore this gives us an easy method of computing core points for any such group. Before looking at an example, I'll first discuss how we can use one of these core points to make an integer program.

Let \\(z \in \mathbb Z\\) be one such core point constructed using this method, and let \\(A\\) be the matrix with the vertices of \\(T := \textrm{conv}(Gz)\\) as columns. Then an inequality description for \\(T\\) is:

\\[ T = \lbrace x \in \mathbb{R}^n : A^{-1}x \geq 0, \sum_{i = 1}^{n} x_i = \sum_{i=1}^n z_i \rbrace \\]

Due to our method of construction, \\(z\\) will have exactly one coordinate with maximal value. Denote this by \\(M\\). Then we can get an inequality description for the integer points inside \\(T\\), minus the vertices, with:

\\[ T' := \lbrace x \in \mathbb{Z}^n : A^{-1}x \geq 0, \sum_{i = 1}^{n} x_i = \sum_{i=1}^n z_i, x_i \leq M - 1 \rbrace \\]

This integer programming problem will be infeasible, with an underlying symmetry group of \\(G\\), and is therefore a good candidate to test the effectiveness of integer programming algorithms that make use of information about the problem's symmetry.
