---
layout: post
title: Computing Invariant Subspaces of Irreducible Representations
---

In this post, I'm going to be going over the details of the GAP implementation of an algorithm to compute the invariant subspaces of a group, along with some background information on the mathematics behind it.

To start off, an **invariant subspace** of a representation \\(\rho : G \to GL_n(V)\\) is a non-empty subspace \\(W \subset V\\) such that \\(\rho(g)w \in W \forall w \in W\\). In other words, when acted upon by an element of \\(G\\), vectors in \\(W\\) stay within \\(W\\). For example, consider the canonical representation \\(\rho\\) of \\(S_2 = \lbrace (), (12) \rbrace \\), 
\\[ \rho(()) = 
\begin{bmatrix} 
1 & 0 \\\
0 & 1
\end{bmatrix} \\]
\\( \rho((12)) = \\)
\\[
\begin{bmatrix} 
0 & 1 \\\
1 & 0
\end{bmatrix} 
\\]
which permutes the coordinates of the basis vectors. \\(span \lbrace (1,1)^T \rbrace \\) is an invariant subspace of \\(\rho\\), as permuting the coordinates has no effect on any of the vectors within it.

Any invariant subspace has an associated subrepresentation \\(\rho\vert_w : G -> GL_n(W)\\), consisting of the action of \\(\rho\\) restricted to \\(W\\). If a representation has no subrepresentations (i.e. the only subspace it acts invariantly on is the entire space), then we denote it as an **irreducible representation**. Any reducible representation can be decomposed into a direct sum of irreducible representations, which corresponds into a decomposition of the space \\(V\\) into a direct sum of invariant subspaces. Our goal with this algorithm will be to find a decomposition of \\(\mathbb R^n\\) into a direct sum of invariant subspaces for a representation of some group \\(G\\).

Now, given a representation \\(\rho : G \to GL_n(\mathbb C)\\), its character \\(\chi_\rho : G \to \mathbb C\\) is the trace of the matrix given by \\(\rho(g)\\) for all \\(g \in G\\). Many of our computations dealing with representations of groups can be re-expressed as computations involving their characters instead, which will greatly simplfy the complexity.

Before getting into the formulas and corresponding GAP code, I'm going to take a quick detour to discuss conjugacy classes, which we'll be making heavy use of. Two elements \\(a\\) and \\(b\\) of \\(G\\) are **conjugate** if \\(a = gbg^{-1}\\) for some \\(g \in G\\). This is an equivalence relation, and we can therefore partition \\(G\\) into disjoint sets of elements that conjugate with one another. In other words, \\(Cl(a) = Cl(b)\\) iff a and b conjugate, else \\(Cl(a) \bigcap CL(b) = \emptyset \\). An interesting fact involving characters and conjugacy classes is \\(\chi(a) = \chi(b)\\)  \\(\forall a \in Cl(b)\\), meaning characters are constant within a conjugacy class, so we only need to compute it once. This gives us a nice way of finding the characters of the canonical representation of a group in GAP:

`
RegularCharacters := function(G,dim)
    local cclasses, ch_list;
    cclasses := ConjugacyClasses(G);
    ch_list := List([1..NrConjugacyClasses(G)],
                    i -> TraceMat(PermutationMat(Representative(cclasses[i]),dim)));
    return ch_list;
end;
`

Now, just as a representation can be decomposed into irreducible representations, so can its character. Any character of a group \\(G\\) can be written as \\[ \chi = m_1\chi_1 + m_2\chi_2 + ... + m_k\chi_k \\] where \\(m_i \in \mathbb N\\) and the \\(\chi_i\\) are the characters of irreducible representations of \\(G\\). In GAP, we can use `Irr(CharacterTable(G))` to get a table of these irreducible characters, indexed by the conjugacy classes of \\(G\\).

We can compute each \\(m_i\\) as an inner product of characters by the formula 
\\[ m_i = \frac{1}{\vert G\vert} \sum_{g \in G}\chi_i(g)\chi(g^{-1}) \\]

Using the two facts just discussed, we can re-express this formula as
\\[ m_i = \frac{1}{\vert G \vert} \sum {Cl(g) \subseteq G} \vert Cl(g) \vert \chi_i(g) \chi(g)* \\]

Translated into GAP:


