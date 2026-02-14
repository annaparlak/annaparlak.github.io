# Veering Walkthrough

[Veering](https://github.com/henryseg/Veering/tree/master) is a Python code for working with transverse taut and veering ideal triangulations, written by Saul Schleimer, Henry Segerman, and Anna Parlak.

<!-- Almost all functions rely on [Regina](https://regina-normal.github.io/). Some functions rely on [Snappy](https://snappy.computop.org/) and/or [SageMath](https://www.sagemath.org/). -->

This walkthrough illustrates some of the capabilities of Veering when run inside [SageMath](https://www.sagemath.org/) from the command line. A complete list of available functions can be found by browsing the project’s [GitHub repository](https://github.com/henryseg/Veering/tree/master).

The [References](veering.md#ref) section at the end of this page provide sources for the definitions of the terms used in this walkthrough.

## Contents
- [Installation](veering.md#installation)
- [Testing](veering.md#testing)
- [Taut signatures](veering.md#signatures)
- [From a taut signature to a Regina triangulation](veering.md#regina)
- [Layered, measurable, and non-measurable triangulations](veering.md#type)
- [Carried surfaces](veering.md#carried)
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
### From a taut signature to a Regina triangulation, and vice versa <a id="regina"></a>

Veering’s functions operate on [Regina](https://regina-normal.github.io/) triangulations. To perform operations not yet implemented in Veering, one can convert a taut signature to a Regina triangulation as follows.

    sage: from veering import taut
    sage: tri, angle = taut.isosig_to_tri_angle(sig)
    sage: tri
    <regina.Triangulation3: Ideal orientable 3-D triangulation, f = ( 1 5 10 5 )>

The resulting Regina triangulation has a fixed numbering of its tetrahedra, faces, and edges, consistent with the numbering illustrated in the figures available in the [Veering Census](https://math.okstate.edu/people/segerman/veering.html). 

Information on how to work with Regina 3D-triangulations can be found here: [regina::Triangulation< 3 > Class Reference](https://regina-normal.github.io/engine-docs/classregina_1_1Triangulation_3_013_01_4.html).

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

Surfaces carried by a veering triangulation correspond to surfaces that are positively transverse to the underlying (drilled) flow. They are in one-to-one correspondence with non-negative integer solutions of the matching/edge equations of the triangulation. If a triangulation has $n$ tetrahedra, each carried surface is given by a vector $(w_i)_{i=0}^{2n-1}$, where $v_i \in \mathbb{Z} \cap \lbrack 0, +\infty)$. 

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
### References <a id="ref"></a>

__1. Taut structure__

M. Lackenby, [_Taut ideal triangulations of 3-manifolds_](https://msp.org/gt/2000/4-1/gt-v4-n1-p12-p.pdf), Definition on p. 370.

__2. Veering structure__

*  (original definition) I. Agol [_Taut triangulations of pseudo-Anosov mapping tori_](https://arxiv.org/pdf/1008.1606), Definition 4.1.
*  (now-standard definition) C.D. Hodgson, J.H. Rubinstein, H. Segerman, S. Tillmann [_Veering triangulations admit strict angle structures_], Definition 1.3.

__3. Matching/edge equations__








