# Advance Databases
## Physical Operators Cost and Result Size

Relation (R)
------------------

| Operator                             | Cost                   | Result Size                          |
|--------------------------------------|------------------------|--------------------------------------|
| **TableScan**(R)                     | C = N<sub>pag</sub>(R) | E<sub>rec</sub> = N<sub>rec</sub>(R) |
| **IndexScan**(R, I) _indexclustered_ | C = N<sub>leaf</sub>(I) + N<sub>pag</sub>(R) | E<sub>rec</sub> = N<sub>rec</sub>(R) |
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


