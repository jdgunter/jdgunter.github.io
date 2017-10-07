---
layout: post
title: Computing Invariant Subspaces of Irreducible Representations
---

In this post, I'm going to be going over the details of an algorithm, implemented in GAP, to compute the invariant subspaces of a group, along with some background information on the mathematics behind it.

Before getting into the computational aspects, I'm going to take a quick detour to discuss conjugacy classes. Two elements \\(a\\) and \\(b\\) of \\(G\\) are **conjugate** if \\(a = gbg^{-1}\\) for some \\(g \in G\\). This is an equivalence relation, and we can therefore partition \\(G\\) into disjoint sets of elements that conjugate with one another (i.e. \\(Cl(a) = Cl(b)\\) iff a and b conjugate, else \\(Cl(a) \bigcap CL(b) = \emptyset \\). 

To start off, given a representation \\(\rho : G \to GL_n(\mathbb C)\\), its character \\(\chi_\rho : G \to \mathbb C\\) is the trace of the matrix given by \\(\rho(g)\\) for all \\(g \in G\\). Many computations dealing with representations of groups can be re-expressed as computations involving their characters instead, greatly simplfying the complexity.

Just as a representation can be decomposed into irreducible representations, so can its character. Any character of a group \\(G\\) can be written as \\[ \chi = m_1\chi_1 + m_2\chi_2 + ... + m_k\chi_k \\] where \\(m_i \in \mathbb N\\) and the \\(\chi_i\\) are the characters of irreducible representations of \\(G\\). We can compute each \\(m_i\\) as an inner product of characters by the formula \\[ m_i = \frac{1}{\vert G\vert} \sum_{g \in G}\chi_i(g)\chi(g^{-1}) \\]

To compute this efficiently in GAP, we can use the formula 
