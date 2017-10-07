---
layout: post
title: Computing Invariant Subspaces of Irreducible Representations
---

In this post, I'm going to be going over the details of an algorithm, implemented in GAP, to compute the invariant subspaces of a group, along with some background information on the mathematics behind it. Some definitions to start off with:

Given a group \\(G\\) and a vector space \\(V\\), a representation of \\(G\\) is a mapping \\(\rho : G \to GL(V)\\). If there's a subspace \\(W \subset V\\), \\(W \neq \emptyset\\), such that \\(\forall g \in G, w \in W, \rho(g)w \in W\\), i.e. \\(w\\) stays within \\(W\\) when acted upon by an element of \\(G\\), then we say that \\(\rho\\) is reducible and \\(W\\) is an **invariant subspace** for \\(\rho\\). A subrepresentation \\(\rho|_w : G -> GL(W)\\) can be obtained by restricting the action of \\(\rho\)) to \\(W\\) (?). If \\(\rho\)) has no such invariant subspaces, then \\(\rho\)) is **irreducible**.

