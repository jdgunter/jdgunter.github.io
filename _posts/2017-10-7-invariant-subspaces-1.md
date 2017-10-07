---
layout: post
title: Computing Invariant Subspaces of Representations for Groups
---

In this post, I'm going to be going over the details of the GAP implementation of an algorithm to compute the invariant subspaces of a group, along with some background information on the mathematics behind it.

To start off, an **invariant subspace** of a representation \\(\rho : G \to GL_n(V)\\) is a non-empty subspace \\(W \subset V\\) such that \\(\rho(g)w \in W \forall w \in W\\). In other words, when acted upon by an element of \\(G\\), vectors in \\(W\\) stay within \\(W\\). For example, consider the canonical representation \\(\rho\\) of \\(S_2 = \lbrace (), (12) \rbrace \\), 
\\[ \rho(()) = 
\begin{bmatrix} 
1 & 0 \\\
0 & 1
\end{bmatrix},
\rho((12)) =
\begin{bmatrix} 
0 & 1 \\\
1 & 0
\end{bmatrix} 
\\]
which permutes the coordinates of the basis vectors. We can see that \\(span \lbrace (1,1)^T \rbrace \\) is an invariant subspace of \\(\rho\\), as permuting the coordinates has no effect on any of the vectors within it.

Any invariant subspace has an associated subrepresentation \\(\rho\vert_w : G -> GL_n(W)\\), consisting of the action of \\(\rho\\) restricted to \\(W\\). If a representation has no subrepresentations (i.e. the only subspace it acts invariantly on is the entire space), then we denote it as an **irreducible representation**. Any reducible representation can be decomposed into a direct sum of irreducible representations, which corresponds into a decomposition of the space \\(V\\) into a direct sum of invariant subspaces. Our goal with this algorithm will be to find a decomposition of \\(\mathbb R^n\\) into a direct sum of invariant subspaces for a representation of some group \\(G\\).

Now, given a representation \\(\rho : G \to GL_n(\mathbb C)\\), its character \\(\chi_\rho : G \to \mathbb C\\) is the trace of the matrix given by \\(\rho(g)\\) for all \\(g \in G\\). Many of our computations dealing with representations of groups can be re-expressed as computations involving their characters instead, which will greatly simplfy the complexity.

Before getting into the formulas and corresponding GAP code, I'm going to take a quick detour to discuss conjugacy classes, which we'll be making heavy use of. Two elements \\(a\\) and \\(b\\) of \\(G\\) are **conjugate** if \\(a = gbg^{-1}\\) for some \\(g \in G\\). This is an equivalence relation, and we can therefore partition \\(G\\) into disjoint sets of elements that conjugate with one another. In other words, \\(Cl(a) = Cl(b)\\) iff a and b conjugate, else \\(Cl(a) \cap CL(b) = \emptyset \\). An interesting fact involving characters and conjugacy classes is \\(\chi(a) = \chi(b)\\)  \\(\forall a \in Cl(b)\\), meaning characters are constant within a conjugacy class, so we only need to compute it once. This gives us a nice way of finding the characters of the canonical representation of a group in GAP:

```
RegularCharacters := function(G,dim)
    local cclasses, ch_list;
    cclasses := ConjugacyClasses(G);
    ch_list := List([1..NrConjugacyClasses(G)],
                    i -> TraceMat(PermutationMat(Representative(cclasses[i]),dim)));
    return ch_list;
end;
```

Another useful fact we'll need is \\(\chi(g^{-1}) = \chi(g)^\*\\), where \\(z^\*\\) is the complex conjugate of \\(z\\).

Now, just as a representation can be decomposed into irreducible representations, so can its character. Any character of a group \\(G\\) can be written as \\[ \chi = m_1\chi_1 + m_2\chi_2 + ... + m_k\chi_k \\] where \\(m_i \in \mathbb N\\) and the \\(\chi_i\\) are the characters of irreducible representations of \\(G\\). In GAP, we can use `Irr(CharacterTable(G))` to get a table of these irreducible characters, indexed by the conjugacy classes of \\(G\\).

We can compute each \\(m_i\\) as an inner product of characters by the formula 
\\[ m_i = \frac{1}{\vert G\vert} \sum_{g \in G}\chi_i(g)\chi(g^{-1}) \\]

Using the two facts just discussed, we can re-express this formula as
\\[ m_i = \frac{1}{\vert G \vert} \sum_{Cl(g) \subseteq G} \vert Cl(g) \vert \chi_i(g) \chi(g)^* \\]

Translated into GAP:
```
tbl := CharacterTable(G);;
irrs := Irr(tbl);;
reg := RegularCharacters(G,dim);;

m := List(irrs, i -> 0);;
for i in [1..Size(irrs)] do
    m[i] := Sum([1..Size(SizesConjugacyClasses(tbl))],
                j -> SizesConjugacyClasses(tbl)[j]*irrs[i][j]*ComplexConjugate(reg[j]) ) / Order(G);
od;
```

Here `irrs[i][j]` is the value of the \\(i\\)th irreducible character for the \\(j\\)th conjugacy class, and `SizesConjugacyClasses(tbl)` is a list of the cardinality of each conjugacy class.

From this decomposition of \\(\chi\\), we can find a related decomposition \\(\rho = \rho_1^{(m_1)} \oplus \rho_2^{(m_2)} \oplus ... \rho_k^{(m_k)} \\). We could further decompose each \\(\rho_i^{(m_i)}\\), but this will not be needed for our purposes. All we need to know is that with these \\(m_i\\)s, we can now solve the following formula for the projection of \\(\mathbb C^n\\) onto the invariant subspace \\(V_i\\) corresponding to the subrepresentation \\(\rho_i^{(m_i)}\\):

\\[ P_i = \frac{m_i \chi_i(1)}{\vert G \vert} \sum_{g \in G} \chi_i(g^{-1}) \rho(g) \\]

The column space of this matrix will form a basis for \\(V_i\\). Unfortunately, as we're summing over all the matrices representing group elements (which are definitely *not* constant in each conjugacy class), we cannot simplify this to the extent we previously could. On the bright side, this means the code is a more straightforward translation and easier to understand:

```
P := List(irrs, i -> NullMat(dim,dim));;
for i in [1..Size(irrs)] do
    if m[i] <> 0 then
        for g in G do
            irr_index := Position(cclasses, ConjugacyClass(G,g));
            reg_rep := PermutationMat(g,dim);
            P[i] := P[i] + ComplexConjugate(irrs[i][irr_index])*reg_rep;
        od;
        P[i] := m[i]*irrs[i][1]/Order(G) * P[i];
    fi;
od;
```
`Position(cclasses, ConjugacyClass(G,g))` finds the index of the conjugacy class that \\(g\\) lies in, which we can use to index our list of irreducible characters and find the character value for \\(g\\). Once again, we take the complex conjugate of this, since we're actually interested in the character of \\(g^{-1}\\).

At this point, we now have projection matrices for all of the invariant subspaces, so we just need to find bases for their column spaces. This turns out to be a trivial computation in GAP, as the command `BaseMat(A)` returns a basis for the row space of a matrix \\(A\\), so we can find a list of bases for the column spaces with `List(P, p -> BaseMat(TransposedMat(p)))`. Filtering out the 0-bases after this leaves us with a unique decomposition of \\(C^n\\) into \\(G\\)-invariant subspaces.
