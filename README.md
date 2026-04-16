# Meander Matrix and Arch Diagram Toolkit

This project provides tools for generating **Dyck words**, **arch diagrams**, and **meanders**, along with functionality for constructing **meander matrices**, computing **orbits under symmetry**, and analyzing structural patterns.

The implementation relies heavily on the **SageMath** ecosystem, which provides built-in support for combinatorial structures such as Dyck words, permutations, and polynomial rings.

---

# Overview

The core steps taking place are:

1. Generate Dyck words.
2. Build meanders from pairs of arch diagrams.
3. Construct the meander matrix.
4. Analyze meander matrix data, including orbits, distribution of entries, and more.
5. Visualize diagrams and matrix structure.

---

# Requirements

- **SageMath**
- **Python**
- **Matplotlib**
- NumPy (implicitly used through Sage/Matplotlib)

---

# Dyck Words

Dyck words are generated recursively using:

```python
dyck_words(n, sorting="lex")
```

## Method

Dyck words are generated via recursion:

- At each step:
  - Add an open bracket `"("`
  - Add a close bracket `")"`
- Track counts of open and close brackets.
- Valid Dyck words of length `2n` are collected at recursion leaves.

## Sorting Options

Dyck words can be ordered using:

| Sorting | Description |
|--------|-------------|
| `"lex"` | Lexicographic ordering (default) |
| `"dom"` | Dominance (Stanley lattice) ordering |
| `"tam"` | Tamari lattice ordering |

### Dominance Ordering

Defined as:

```
a > b if h(a) > h(b) at every point
```

Tie-breaking rules:

1. Compare maximum height.
2. If equal, compare leftmost first peak.

---

# Arch Diagrams

Arch diagrams are constructed from Dyck words using the SageMath:

```python
tunnels()
```

function.

This returns pairs:

```python
(a_i, b_i + 1)
```

Each arch diagram is stored as:

```python
[(a1, b1), (a2, b2), ..., (an, bn)]
```

These are used to build permutations representing arch structures.

---

# Meanders

A **meander** is created from two Dyck words:

- One representing the **top arches**
- One representing the **bottom arches**

Each arch diagram is also converted to and stored as a permutation.

The meander permutation is computed by:

```python
top_perm * bottom_perm
```

The number of connected components is extracted from the resulting permutation.

---

# Meander Matrix

Constructed using:

```python
meander_matrix(n, sorting="lex")
```

## Definition

Entry:

```
M[i,j] = r
```

where `r` is the number of components of the meander formed by arch diagrams `i` and `j`.

Alternatively:

```
M[i,j] = q^r
```

when working in a polynomial ring.

This enables manual computation of the determinant.

---

# Determinant Verification

Di Francesco's determinant formula is implemented using Sage polynomial rings.

A helper function computes required constants, and numerical tests verify that the computed determinant matches the theoretical formula.

---

# Symmetry Actions

## Rotation

Rotation of Dyck words is implemented via:

```python
rotate_dyck(x)
```

Each step of the Dyck path is shifted right, wrapping cyclically.

Example:

```python
x = DyckWord("()()()()")
y = rotate_dyck(x)
print(y)

>>> (()()())
```

---

# Orbit Computation

Several functions analyze symmetry orbits.

## Core Orbit Functions

```python
orbit_indices(arches, index_dict, i, j=None)

meander_orbits(mM, arches)

arch_orbits(arches)

orbit_label_matrix(mM, arches)

get_reps(arches, orbits)
```

---

## orbit_indices

Computes the orbit under rotation.

Inputs:

- List of arch diagrams
- Dictionary mapping Dyck words to indices
- One or two indices

Behavior:

- One index → arch orbit
- Two indices → meander orbit

Rotation continues until returning to the original element.

---

## arch_orbits

Enumerates all arch orbits:

- Avoids double counting by tracking visited elements

---

## meander_orbits

Same as `arch_orbits`, but applied to meanders.
- Has options for dihedral and symmetric actions. 

---

## orbit_label_matrix

Produces:

- Matrix with orbits highlighted different colors
- Orbit counts
- List of orbits

Each orbit is assigned a unique color label.

Example:

```python
M, arches, heights = meander_matrix(4)

labels, num_orbits, orbits = orbit_label_matrix(M, arches)

plt.imshow(labels, cmap='tab20')
plt.colorbar()
plt.show()
```

---

## get_reps

Returns orbit representatives.

Behavior differs for different objects:

- **Meanders:** arbitrary representative
- **Arch diagrams:** maximal height element

---

# Component Sum Polynomials

Implemented using:

```python
component_sum_polynomial(n, x)

arch_orbit_polynomials(n)

arch_polynomial_graphs(n, x1, x2)
```

## component_sum_polynomial(n, x)

Computes the component sum polynomial of a single arch diagram as defined in the paper.

## arch_orbit_polynomials

Computes component sum polynomials of arch diagram orbit representatives.

## arch_polynomial_graphs(n, x_1, x_2)

Computes and graphs the component sum polynomials of arch diagram orbit representatives. x_1 and x_2 dictate the x values represented in the graph.

---

# Matrix Analysis

```python
matrix_data(M, heights, should_print=True)
```

Computes various meander matrix data.

---

## Returns

- Row sums
- Minimum row index
- Maximum row index
- Height-ordered indices
- Correlation between:
  - Height vs average components
  - Representative height vs average components

---

## Generated Plots

- Row sums vs index
- Row sum distribution
- Matrix entry distribution
- Heatmap visualization
- Linear regression fit

---

# Visualization Tools

## Arch Diagram Drawing

```python
draw_arch_diagram(dw)
```

Draws arch diagram using Matplotlib.

---

## Meander Drawing

```python
draw_meander(top_dw, bot_dw)
```

Visualizes full meander structure.

---

# Example Workflow

```python
# Generate lexicographically ordered meander matrix
M, arches, heights = meander_matrix(4)

# Compute symmetry orbits
labels, num_orbits, orbits = orbit_label_matrix(M, arches)

# Plot orbit structure
plt.imshow(labels, cmap='tab20')
plt.colorbar()
plt.show()
```

---

# Design Notes

## Choice of Ordering

Lexicographic ordering is used by default because:

- It is easy to generate recursively
- It is a refinement of the Tamari lattice
- It produces visually interesting matrices (heuristic).

Other orderings remain available for experimentation.

---

