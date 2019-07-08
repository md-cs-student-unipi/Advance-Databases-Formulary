# Advance Databases
## Physical Operators Cost and Result Size

- **R** relation
- **I** index
- **O** operator
- **B** number of pages in buffer
- **A** attribute
- **N<sub>pag</sub>(R)** Number of pages of relation R
- **N<sub>rec</sub>(R)** Number of records of relation R
- **N<sub>leaf</sub>(I)** Number of leaf of index I
- **N<sub>key</sub>(I)** Number of key of index I
- **ψ<sub>J</sub>** join condition
- **S<sub>f</sub>(ψ<sub>J</sub>)** selectivity factor of ψ<sub>J</sub> 
- **C<sub>I</sub>** Cost of accessing the index pages
- **C<sub>D</sub>** Cost of accessing the data pages containing the records
- **Φ(k, n) ~ min(k, n)** Cardenas formula (estimate of the number of pages, in a file of n pages,
that contain at least one of the k records to be retrieved using a sorted rid-list)

Relation (R)
------------------

| Operator                             | Cost                   | Result Size                          |
|--------------------------------------|------------------------|--------------------------------------|
| **TableScan**(R)                     | C = N<sub>pag</sub>(R) | E<sub>rec</sub> = N<sub>rec</sub>(R) |
| **IndexScan**(R, I) _index clustered_ | C = N<sub>leaf</sub>(I) + N<sub>pag</sub>(R) | E<sub>rec</sub> = N<sub>rec</sub>(R) |
| **IndexScan**(R, I) _index on key_   | C = N<sub>leaf</sub>(I) + N<sub>rec</sub>(R) | E<sub>rec</sub> = N<sub>rec</sub>(R) |
| **IndexScan**(R, I) _otherwise_      | C = N<sub>leaf</sub>(I) + N<sub>key</sub>(R) * Φ(N<sub>rec</sub>(R)/N<sub>key</sub>(R), N<sub>pag</sub>(R)) | E<sub>rec</sub> = N<sub>rec</sub>(R) |
| **IndexSequentialScan**(R, I)        | C = N<sub>leaf</sub>(I) | E<sub>rec</sub> = N<sub>rec</sub>(R) |
| **SortScan**(R, I)                   | C = 3*N<sub>pag</sub>(R) | E<sub>rec</sub> = N<sub>rec</sub>(R) |

Projection (π)
------------------

| Operator                                 | Cost                     | Result Size                           |
|------------------------------------------|--------------------------|---------------------------------------|
| **Project**(O, {A<sub>i</sub>})          | C = C(O)                 | E<sub>rec</sub> = E<sub>rec</sub>(O)  |
| **IndexOnlyScan**(R, I, {A<sub>i</sub>}) | C = N<sub>leaf</sub>(I)  | E<sub>rec</sub> = N<sub>rec</sub>(R)  |

Duplicate elimination (δ)
------------------

| Operator                      | Cost                            | Result Size                           |
|-------------------------------|---------------------------------|---------------------------------------|
| **Distinct**(O, {A<sub>i</sub>})  | C = C(O) | E<sub>rec</sub> = min(E<sub>rec</sub>(O)/2, Π N<sub>key</sub>(A<sub>i</sub>))  |
| **HashDistinct**(O, {A<sub>i</sub>})  | C = C(O) + 2*N<sub>pag</sub>(O) | E<sub>rec</sub> = min(E<sub>rec</sub>(O)/2, Π N<sub>key</sub>(A<sub>i</sub>))  |

Sort (τ)
------------------

| Operator                      | Cost                            | Result Size                           |
|-------------------------------|---------------------------------|---------------------------------------|
| **Sort**(O, {A<sub>i</sub>})  | C = C(O) + 2*N<sub>pag</sub>(O) | E<sub>rec</sub> = E<sub>rec</sub>(O)  |


Selection (σ)
------------------

| Operator                                      | Cost                            | Result Size                           |
|-----------------------------------------------|---------------------------------|---------------------------------------|
| **Filter**(O, ψ)                              | C = C(O) | E<sub>rec</sub> = \|S<sub>f</sub>(ψ) * E<sub>rec</sub>(O)\|  |
| **IndexFilter**(R, I, ψ) _index clustered_    | C = \|S<sub>f</sub>(ψ) * (N<sub>leaf</sub>(I) + N<sub>pag</sub>(R))\| | E<sub>rec</sub> = \|S<sub>f</sub>(ψ) * N<sub>rec</sub>(R)\|  |
| **IndexFilter**(R, I, ψ) _index unclustered_  | C = \|S<sub>f</sub>(ψ) \| | E<sub>rec</sub> = \|S<sub>f</sub>(ψ) * N<sub>rec</sub>(R)\|  |
| **IndexFilter**(R, I, ψ) _index on key_       | C = \|S<sub>f</sub>(ψ) * (N<sub>leaf</sub>(I) + N<sub>rec</sub>(R))\| | E<sub>rec</sub> = \|S<sub>f</sub>(ψ) * N<sub>rec</sub>(R)\|  |
| **IndexSequentialFilter**(R, I, ψ)            | C = \|S<sub>f</sub>(ψ) * N<sub>leaf</sub>(I)\| | E<sub>rec</sub> = \|S<sub>f</sub>(ψ) * N<sub>rec</sub>(R)\|  |
| **IndexOnlyFilter**(R, I, {A<sub>i</sub>}, ψ) | C = \|S<sub>f</sub>(ψ) * N<sub>leaf</sub>(I)\| | E<sub>rec</sub> = \|S<sub>f</sub>(ψ) * N<sub>rec</sub>(R)\|  |
| **OrIndexFilter**(R, I, {A<sub>i</sub>}, ψ)   | C = \|Σ C<sub>I</sub><sup>K</sup> \| + \|Φ(E<sub>rec</sub>, N<sub>pag</sub>(R))\| | E<sub>rec</sub> = \|S<sub>f</sub>(ψ) * N<sub>rec</sub>(R)\|  |
| **AndIndexFilter**(R, I, {A<sub>i</sub>}, ψ)  | C = \|Σ C<sub>I</sub><sup>K</sup> \| + \|Φ(E<sub>rec</sub>, N<sub>pag</sub>(R))\| | E<sub>rec</sub> = \|S<sub>f</sub>(ψ) * N<sub>rec</sub>(R)\|  |


Grouping (γ)
------------------

| Operator                      | Cost                            | Result Size                           |
|-------------------------------|---------------------------------|---------------------------------------|
| **GroupBy**(O, {A<sub>i</sub>}, {f<sub>i</sub>})  | C = C(O) | E<sub>rec</sub> = min(E<sub>rec</sub>(O)/2, Π N<sub>key</sub>(A<sub>i</sub>))  |
| **HashGroupBy**(O, {A<sub>i</sub>}, {f<sub>i</sub>})  | C = C(O) + 2*N<sub>pag</sub>(O) | E<sub>rec</sub> = min(E<sub>rec</sub>(O)/2, Π N<sub>key</sub>(A<sub>i</sub>))  |


Join (⋈)
------------------


| Operator                      | Cost                            | Result Size                           |
|-------------------------------|---------------------------------|---------------------------------------|
| **NestedLoop**(O<sub>E</sub>, O<sub>I</sub>, ψ<sub>J</sub>) | C = C(O<sub>E</sub>) + E<sub>rec</sub>(O<sub>E</sub>) * C(O<sub>I</sub>) | E<sub>rec</sub> = \| S<sub>f</sub>(ψ<sub>J</sub>) * E<sub>rec</sub>(O<sub>E</sub>) * E<sub>rec</sub>(O<sub>I</sub>) \| |
| **PageNestedLoop**(O<sub>E</sub>, O<sub>I</sub>, ψ<sub>J</sub>) | C = C(O<sub>E</sub>) + N<sub>pag</sub>(O<sub>E</sub>) * C(O<sub>I</sub>) | E<sub>rec</sub> = \| S<sub>f</sub>(ψ<sub>J</sub>) * E<sub>rec</sub>(O<sub>E</sub>) * E<sub>rec</sub>(O<sub>I</sub>) \| |
| **BlockNestedLoop**(O<sub>E</sub>, O<sub>I</sub>, ψ<sub>J</sub>) | C = C(O<sub>E</sub>) + N<sub>pag</sub>(O<sub>E</sub>)/B * C(O<sub>I</sub>) | E<sub>rec</sub> = \| S<sub>f</sub>(ψ<sub>J</sub>) * E<sub>rec</sub>(O<sub>E</sub>) * E<sub>rec</sub>(O<sub>I</sub>) \| |
| **IndexNestedLoop**(O<sub>E</sub>, O<sub>I</sub>, ψ<sub>J</sub>) | C = C(O<sub>E</sub>) + E<sub>rec</sub>(O<sub>E</sub>) * (C<sub>I</sub> + C<sub>D</sub>) | E<sub>rec</sub> = \| S<sub>f</sub>(ψ<sub>J</sub>) * E<sub>rec</sub>(O<sub>E</sub>) * E<sub>rec</sub>(O<sub>I</sub>) \| |
| **MergeJoin**(O<sub>E</sub>, O<sub>I</sub>, ψ<sub>J</sub>) | C = C(O<sub>E</sub>) + C(O<sub>I</sub>) | E<sub>rec</sub> = \| S<sub>f</sub>(ψ<sub>J</sub>) * E<sub>rec</sub>(O<sub>E</sub>) * E<sub>rec</sub>(O<sub>I</sub>) \| |
| **HashJoin**(O<sub>E</sub>, O<sub>I</sub>, ψ<sub>J</sub>) | C = C(O<sub>E</sub>) + 2*(N<sub>pag</sub>(O<sub>E</sub>) + N<sub>pag</sub>(O<sub>I</sub>)) | E<sub>rec</sub> = \| S<sub>f</sub>(ψ<sub>J</sub>) * E<sub>rec</sub>(O<sub>E</sub>) * E<sub>rec</sub>(O<sub>I</sub>) \| |


