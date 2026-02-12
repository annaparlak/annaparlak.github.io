# Veering Walkthrough

Veering is a Python code for working with transverse taut and veering ideal triangulations.

Almost all functions rely on [Regina](https://regina-normal.github.io/). Some functions rely on [Snappy](https://snappy.computop.org/) and/or [SageMath](https://www.sagemath.org/).

This walkthrough illustrates some of the capabilities of Veering when run inside Sage from the command line. 

## Contents
- [Installation](veering.md#installation)
- [Testing](veering.md#testing)
- [Taut signatures](veering.md#signatures)

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

We refer to veering triangulations using taut signatures. Each taut signature identifies a veering triangulation up to combinatorial isomorphism and reversing its (transverse) taut structure.  

One can load taut signatures of all veering triangulations from the [Veering Census](https://math.okstate.edu/people/segerman/veering.html) as follows:

    sage: census = veering.veering_census()

Using this, one can easily perform computations on all veering triangulations in the census. In this walkthrough, however, we focus on a single triangulation at a time. For example:
    
    sage: sig = census[17]
    sage: sig
    'fLAMcaccdeejsnaxk_20010'



