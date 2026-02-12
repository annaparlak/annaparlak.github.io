# Veering Walkthrough

Veering is a Python code for working with transverse taut and veering ideal triangulations.

Almost all functions rely on [Regina](https://regina-normal.github.io/). Some functions rely on [Snappy](https://snappy.computop.org/) and/or [SageMath](https://www.sagemath.org/).

This walkthrough illustrates some of the capabilities of Veering when run inside Sage from the command line. 

## Contents
- [Installation](veering.md#installation)
- [Testing](veering.md#testing)
- [Taut signatures](veering.md#signatures)
- [Layered, measurable, and non-measurable triangulations](veering.md#type)

---

### Installation <a id="installation"></a>

To install (or uninstall) veering inside Sage via the command line using pip type:

    sage -pip install veering

or

    sage -pip uninstall veering

### Testing <a id="testing"></a>

After installation start a sage session and run the following:

    sage: import veering
    sage: from veering import test_suite
    sage: test_suite.run_tests()

### Taut signatures <a id="signatures"></a>

We refer to veering triangulations using _taut signatures_. Each taut signature identifies a veering triangulation up to combinatorial isomorphism and reversing its (transverse) taut structure.  

One can load taut signatures of all veering triangulations from the [Veering Census](https://math.okstate.edu/people/segerman/veering.html) as follows:

    sage: census = veering.veering_census()

Using this, one can easily perform computations on all veering triangulations in the census. In this walkthrough, however, we focus on a single triangulation at a time. For example:
    
    sage: sig = census[17]
    sage: sig
    'fLAMcaccdeejsnaxk_20010'

The alphabetic prefix encodes the isomorphism type of the triangulation, the numerical suffix -- (transverse) taut structure. The triangulation encoded by this string comes with a fixed numbering of its tetrahedra, faces, and edges. In particular, it makes sense to refer to the $i$-th tetrahedron, face, or edge of the triangulation. If there are $n$ tetrahedra, then there are $2n$ faces and $n$ edges.


### Layered, measurable, and non-measurable triangulations <a id="type"></a>

One of the simplest classifications of veering triangulations is into those that are _layered_ (the underlying flow on the drilled manifold is a suspension flow), _measurable_ (the underlying flow admits a positively transverse surface, but no transverse fibration), and _non-measurable_ (the underlying flow admits no positively transverse surfaces). 

To check the type of the triangulation, use

        sage: from veering import taut_polytope
        sage: taut_polytope.LMN_tri_angle(sig)
        'N'

This output means that the triangulation is non-measurable. For the other two types, one gets 'L' (layered) or 'M' (measurable).

One can also find the primitive vectors on the extremal rays spanning the cone of non-negative solutions to the matching/edge equations (if it is at least 1-dimensional). Non-negative integral solutions corresponds to _carried surfaces_ --- that is, those that are transverse to the underlying (drilled) flow. 

        sage: taut_polytope.taut_rays(census[29])
        [(0, 0, 0, 1, 0, 1, 0, 1, 1, 0),
         (0, 1, 1, 0, 1, 0, 1, 0, 0, 0),
         (0, 0, 1, 1, 0, 1, 1, 0, 0, 0),
         (0, 1, 1, 0, 0, 1, 0, 0, 0, 1),
         (0, 1, 0, 0, 1, 0, 0, 1, 1, 0),
         (1, 0, 0, 1, 0, 1, 0, 0, 0, 1),
         (1, 1, 0, 0, 1, 0, 0, 0, 0, 1),
         (1, 0, 0, 1, 1, 0, 1, 0, 0, 0)]

The carried surface corresponding to a vector $(v_i)_{i=0}^{2n-1}$, where $n$ is the number of tetrahedra and $v_i \in \mathbb{Z} \cap \lbrack 0, +\infty)$, is built from $v_i$ copies of the face $f_i$.




