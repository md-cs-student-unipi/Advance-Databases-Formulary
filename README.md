# Advance-Databases-Formulary

Physical Operators Cost and Result Size
===========


Relation (R)
------------------

| Operator             | Cost                       | Result Size                                      |
|----------------------|----------------------------|--------------------------------------------------|
| **TableScan**(R)         | C = N<sub>pag</sub>(R)     | E<sub>rec</sub> = N<sub>rec</sub>(R)             |

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
| **Distinct**(O, {A<sub>i</sub>})  | C = C(O) | E<sub>rec</sub> = min(E<sub>rec</sub>(O), Π N<sub>key</sub>(A<sub>i</sub>))  |
| **HashDistinct**(O, {A<sub>i</sub>})  | C = C(O) + 2*N<sub>pag</sub>(O) | E<sub>rec</sub> = min(E<sub>rec</sub>(O), Π N<sub>key</sub>(A<sub>i</sub>))  |

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
| **GroupBy**(O, {A<sub>i</sub>}, {f<sub>i</sub>})  | C = C(O) | E<sub>rec</sub> = min(E<sub>rec</sub>(O), Π N<sub>key</sub>(A<sub>i</sub>))  |
| **HashGroupBy**(O, {A<sub>i</sub>}, {f<sub>i</sub>})  | C = C(O) + 2*N<sub>pag</sub>(O) | E<sub>rec</sub> = min(E<sub>rec</sub>(O), Π N<sub>key</sub>(A<sub>i</sub>))  |


Join (⋈)
------------------
