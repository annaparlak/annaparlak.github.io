# Veering Walkthrough

[Veering](https://github.com/henryseg/Veering/tree/master) is a Python code for working with transverse taut and veering ideal triangulations, written by Saul Schleimer, Henry Segerman, and Anna Parlak.

<!-- Almost all functions rely on [Regina](https://regina-normal.github.io/). Some functions rely on [Snappy](https://snappy.computop.org/) and/or [SageMath](https://www.sagemath.org/). -->

This walkthrough illustrates some of the capabilities of Veering when run inside [SageMath](https://www.sagemath.org/) from the command line. A complete list of available functions can be found by browsing the project’s [GitHub repository](https://github.com/henryseg/Veering/tree/master).

The [References](veering.md#ref) section at the end of this page provide sources for the definitions of the terms used in this walkthrough.

## Contents
- [Installation](veering.md#installation)
- [Testing](veering.md#testing)
- [Taut signatures](veering.md#signatures)
- [Edge-orientability](veering.md#EO)
- [Layered, measurable, and non-measurable triangulations](veering.md#type)
- [Carried surfaces](veering.md#carried)
- [Topological information about a carried surface](veering.md#stratum)
- [Veering mutations](veering.md#mutations)
- [Polynomial invariants](veering.md#polynomials)
- [Flow cycles](veering.md#cycles)
- [From a taut signature to a Regina triangulation](veering.md#regina)
- [References](veering.md#ref)

---

### Installation <a id="installation"></a>

To install (or uninstall) veering inside Sage via the command line using pip type:

    sage -pip install veering

or

    sage -pip uninstall veering

---

### Testing <a id="testing"></a>

After installation start a sage session and run the following:

    sage: import veering
    sage: from veering import test_suite
    sage: test_suite.run_tests()

---

### Taut signatures <a id="signatures"></a>

Every veering triangulation can be assigned a unique `name' called a _taut signature_. A taut signature consists of an alphabetical string that identifies the triangulation up to combinatorial isomorphism, and a numerical string that identifies its taut structure up to reversing the coorientation on all faces of the triangulation.

Information on all veering triangulation with at most 16 tetrahedra can be found in the [Veering Census](https://math.okstate.edu/people/segerman/veering.html). One can load the taut signatures of all veering triangulations in the census, or a specific veering triangulation from the census, as follows:

    sage: census = veering.veering_census()
    sage: sig = census[17]
    sage: sig
    'fLAMcaccdeejsnaxk_20010'

---

### Edge-orientability <a id="EO"></a>

Each veering triangulation is either _edge-orientable_ or not. This corresponds to whether the stable lamination of the underlying drilled pseudo-Anosov flow is transversely orientable or not.

        sage: sig = census[17]
        sage: edge_orientability.is_edge_orientable(sig)
        False

---


### Layered, measurable, and non-measurable triangulations <a id="type"></a>

One of the simplest classifications of veering triangulations is into those that are _layered_ (the underlying flow on the drilled manifold is a suspension flow), _measurable_ (the underlying flow admits a positively transverse surface, but no transverse fibration), and _non-measurable_ (the underlying flow admits no positively transverse surfaces). 

To check the type of the triangulation, use

        sage: from veering import taut_polytope
        sage: taut_polytope.LMN_tri_angle(sig)
        'N'

This output means that the triangulation is non-measurable. For the other two types, one gets 'L' (layered) or 'M' (measurable).

---

### Carried surfaces <a id="carried"></a>

Every surface that is _carried by_ a veering triangulation determines a properly embedded surface that is positively transverse to the underlying (drilled) flow. Carried surfaces correspond to non-negative integer solutions of the matching/edge equations of the triangulation. If a triangulation has $n$ tetrahedra, each carried surface is given by a vector $(w_i)_{i=0}^{2n-1}$, where $v_i \in \mathbb{Z} \cap \lbrack 0, +\infty)$. 

The following function finds the primitive vectors on the extremal rays spanning the convex cone of non-negative solutions to the matching/edge equations:

        sage: sig = census[29]
        sage: from veering import taut_polytope
        sage: rays = taut_polytope.taut_rays(sig)
        sage: rays
        [(0, 0, 0, 1, 0, 1, 0, 1, 1, 0),
         (0, 1, 1, 0, 1, 0, 1, 0, 0, 0),
         (0, 0, 1, 1, 0, 1, 1, 0, 0, 0),
         (0, 1, 1, 0, 0, 1, 0, 0, 0, 1),
         (0, 1, 0, 0, 1, 0, 0, 1, 1, 0),
         (1, 0, 0, 1, 0, 1, 0, 0, 0, 1),
         (1, 1, 0, 0, 1, 0, 0, 0, 0, 1),
         (1, 0, 0, 1, 1, 0, 1, 0, 0, 0)]

---
### Topological information about a carried surface <a id="stratum"></a>

        sage: weights = rays[-2] + rays[-1]
        sage: weights
        (2, 1, 0, 1, 2, 0, 1, 0, 0, 1)
        sage: from veering import carried_surface
        sage: carried_surface.stratum(sig, weights)
        [[1, [2, 2]], [1, [2, 2]]]

The output is a list of lists of the form [g, [k_1, ..., k_r]].

The number of such lists is the number of connected components of the surface.

The number g is the genus of the connected component, and r is the number of its punctures.

Intersecting the surface with the stable lamination of the underlying flow gives a lamination in the surface whose complementary regions are once-punctured ideal polygons. Each number k_i is the number of sides of one such ideal polygon (or the number of prongs that we would get after capping off each puncture and collapsing the lamination into a (potentially singular) foliation on the closed surface).

So the output [[1, [2, 2]], [1, [2, 2]]] means two connected components, each of which is a twice punctured torus, and each puncture is inside a bigon complementary region of the induced lamination.

The first Betti number of the manifold underlying census[29] is equal to 1, so we won't get any more interesting surfaces there. Here is the first veering triangulation whose underlying manifold has a higher betti number:

        sage: sig = census[8]
        sage: rays = taut_polytope.taut_rays(sig)
        sage: for ray in rays[:3]:
        ....:     carried_surface.stratum(sig, ray)
        ....: 
        [[1, [1, 3]]]
        [[2, [2, 6]]]
        [[0, [1, 1, 1, 1]]]

---

### Veering mutations <a id="mutations"></a>

A _veering mutation_ is an operation that takes as input 
- a veering triangulation V,
- a surface S carried by V,
- a combinatorial isomorphism of the induced ideal triangulation of S that preserves the stable train track,

and produces another veering triangulation, typically on a different manifold.

Given V and S we can perform all possible veering mutations of V along S using perform_all_mutations():

        sage: from veering import mutation
        sage: sig = census[939]
        sage: weights = (0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 1, 1, 1, 1, 1, 0, 0, 1, 0, 0) ### this is some surface that is carried by census[939]
        sage: mutation.perform_all_mutations(sig, weights)
        stratum: [[2, [4, 4]]]
        triangulation isosig: kLLLQzMkcegfeehijjjhqhaxhhhhha
        taut: True
        taut isosig: kLLLQzMkcegfeehijjjhqhaxhhhhha_1222011221
        transverse taut: True
        veering: True
        layered: True
        edge-orientable: True
        triangulation isosig: kLLvQLQkbefhgijjijihhxaxaxxaaa
        taut: True
        taut isosig: kLLvQLQkbefhgijjijihhxaxaxxaaa_1200111100
        transverse taut: True
        veering: True
        layered: True
        edge-orientable: True
        triangulation isosig: kLLLQLMkbeefeghijjjhhhhhhaahah
        taut: True
        taut isosig: kLLLQLMkbeefeghijjjhhhhhhaahah_1200111001
        transverse taut: True
        veering: True
        layered: True
        edge-orientable: False

If no veering mutation is possible for a given (V, S) we would see:

        sage: sig = census[45]
        sage: weights = taut_polytope.taut_rays(sig)[0]
        sage: mutation.perform_all_mutations(sig, weights)
        stratum: [[2, [6]]]
        surface has no veering symmetries

---
### Polynomial invariants <a id="polynomials"></a>

Landry-Minsky-Taylor introduced two polynomial invariants of veering triangulation: the _taut polynomial_ and the _veering polynomial_.
Veering offers two different ways of computing the taut polynomial: taut_polynomial_via_tree and taut_polynomial_via_fox_calculus. The latter is much faster. The outputs should be the same up to the change of basis of $H_1(M;\mathbb{Z})/$torsion.

        sage: sig = census[8]
        sage: from veering import taut_polynomial
        sage: taut_polynomial.taut_polynomial_via_tree(sig)
        a^2*b - a*b^2 - a*b - a + b
        sage: taut_polynomial.taut_polynomial_via_fox_calculus(sig)
        a^2*b - a*b^2 - a*b - a + b

        sage: veering_polynomial.veering_polynomial(sig)
        a^6*b - a^5*b^2 - 2*a^5*b + a^4*b^2 - a^5 + 2*a^4*b + a^4 - 2*a^3*b + a^2*b^2 + 2*a^2*b - a*b^2 + a^2 - 2*a*b - a + b
        sage: veering_polynomial.veering_polynomial(sig).factor()
        (-1) * (a - 1)^2 * (a^2 + a + 1) * (-a^2*b + a*b^2 + a*b + a - b)

--- 
### Flow cycles <a id="cycles"></a>

Landry-Minsky-Taylor introduced _flow cycles_ of a veering triangulation and showed that each of them encodes a periodic orbit of the underlying pseudo-Anosov flow.

Each flow cycle of a veering triangulation V can be represented by a tuple of pairs ((a_i, b_i)), where a_i is the index of a tetrahedron of V, and b_i is the index of the edge of tetrahedron a_i that is the bottom diagonal of the tetrahedron of index a_{i+1}, where i is taken modulo the length of the tuple.

        sage: sig = census[8]
        sage: from veering import flow_cycles
        sage: cycles = flow_cycles.generate_flow_cycles(sig, max_length = 3)
        sage: cycles
        [((0, 0),),
         ((1, 0),),
         ((2, 3),),
         ((3, 4),),
         ((0, 3), (1, 1)),
         ((0, 5), (3, 1)),
         ((1, 5), (2, 2)),
         ((2, 5), (3, 0)),
         ((0, 0), (0, 3), (1, 1)),
         ((0, 0), (0, 5), (3, 1)),
         ((0, 3), (1, 0), (1, 1)),
         ((0, 5), (3, 4), (3, 1)),
         ((1, 0), (1, 5), (2, 2)),
         ((1, 5), (2, 3), (2, 2)),
         ((2, 3), (2, 5), (3, 0)),
         ((2, 5), (3, 4), (3, 0))]

Given a flow cycle, we can test whether the encoded orbit is twisted (meaning that the leaf of the stable foliation of the flow that contains the orbit is homeomorphic to an open Möbius band) or not. Twisted flow cycles appear only for veering triangulations that are not edge-orientable.




---
### From a taut signature to a Regina triangulation, and vice versa <a id="regina"></a>

Veering’s functions operate on [Regina](https://regina-normal.github.io/) triangulations. To perform operations not yet implemented in Veering, one can convert a taut signature to a Regina triangulation as follows.

    sage: from veering import taut
    sage: tri, angle = taut.isosig_to_tri_angle(sig)
    sage: tri
    <regina.Triangulation3: Ideal orientable 3-D triangulation, f = ( 1 5 10 5 )>

The resulting Regina triangulation has a fixed numbering of its tetrahedra, faces, and edges, consistent with the numbering illustrated in the figures available in the [Veering Census](https://math.okstate.edu/people/segerman/veering.html). 

Information on how to work with Regina 3D-triangulations can be found at: [regina::Triangulation< 3 > Class Reference](https://regina-normal.github.io/engine-docs/classregina_1_1Triangulation_3_013_01_4.html).



---
### References <a id="ref"></a>

The references provided below are __not__ necessarily the original sources in which the definitions first appeared. I included the original source when it was easy to identify (to the best of my knowledge).

__1. Taut structure__

M. Lackenby, [_Taut ideal triangulations of 3-manifolds_](https://msp.org/gt/2000/4-1/gt-v4-n1-p12-p.pdf), Definition on p. 370.

__2. Veering structure__

*  (original definition) I. Agol [_Taut triangulations of pseudo-Anosov mapping tori_](https://arxiv.org/pdf/1008.1606), Definition 4.1.
*  (now-standard definition) C.D. Hodgson, J.H. Rubinstein, H. Segerman, S. Tillmann [_Veering triangulations admit strict angle structures_](https://msp.org/gt/2011/15-4/gt-v15-n4-p06-p.pdf), Definition 1.3.

__3. Edge-orientability__

A. Parlak, [The taut polynomial and the Alexander polynomial](https://londmathsoc.onlinelibrary.wiley.com/doi/epdf/10.1112/topo.12302), Section 3.3.

__4. Matching/edge/branch equations__

A. Parlak, [Computation of the Taut, the Veering, and the Teichmüller Polynomials](https://www.tandfonline.com/doi/epdf/10.1080/10586458.2021.1985656), Section 3.1. 

__5. Polynomial invariants__

* (definition) M. Landry, Y.N. Minsky, S.J. Taylor [A polynomial invariant for veering triangulations](https://ems.press/journals/jems/articles/11806809), Section 3.

* (algorithm for the computation) A. Parlak, [Computation of the Taut, the Veering, and the Teichmüller Polynomials](https://www.tandfonline.com/doi/epdf/10.1080/10586458.2021.1985656), Sections 4 - 6. 

* (computation of the taut polynomial via Fox calculus) A. Parlak, [Arbitrarily large veering triangulations with a vanishing taut polynomial](https://ems.press/journals/ggd/articles/14298509), Section 2.4.2.

__6. Veering mutations__

A. Parlak, [Mutations and faces of the Thurston norm ball dynamically represented by multiple different flows](https://msp.org/gt/2025/29-4/gt-v29-n4-p07-s.pdf), Section 3.

__7. Flow cycles__

M. Landry, Y.N. Minsky, S.J. Taylor [Flows, growth rates, and the veering polyomial](https://www.cambridge.org/core/journals/ergodic-theory-and-dynamical-systems/article/flows-growth-rates-and-the-veering-polynomial/B79BE9FBDBE54CDE8C9D8A5285F4E7BF), Sections 2 (definition) and 6 (proof that flow cycles encode orbits of the flow).





