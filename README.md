# LifeHash: Cellular Automata-Based Deterministic Hashing

A custom deterministic hashing algorithm based on Conway's Game of Life cellular automata. Processes password data through bitwise shuffling and iterative simulation to generate irreversible hashes.



## I. The Transformation Pipeline: Mathematical Foundations

The architecture of **LifeHash** is designed to maximize **Diffusion** and **Confusion**. By mapping linear data onto a two-dimensional toroidal field, we introduce spatial complexity that traditional bitwise hashing often lacks.

### 1. Spatial Projection & ASCII Mapping
Input strings are vectorized into their binary representation and projected onto a configurable $N \times M$ grid. This converts a one-dimensional sequence into a topological initial state, facilitating neighborly interactions across the grid.

### 2. Parity-Based Shuffling (Initial Entropy Injection)
To ensure that even minute differences in input (low Hamming distance) result in vastly different initial configurations, the board undergoes a dual-axis roll using `numpy.roll`:
* **Axis Displacement**: Rows and columns are shifted based on the parity of their index ($i \pmod 2$).
* **The Logic**: This ensures that data is uniformly distributed across the grid before the simulation begins, preventing "localized" pattern matching.

### 3. The Simulation Engine: Iterative Evolution
The core hash is generated through $T$ iterations of a cellular automaton. Unlike standard Game of Life rules which frequently collapse into "static life" or "extinction," the rules in this engine are tuned for persistence.
* **Multi-Generational Evolution**: The system allows for an arbitrary number of simulation cycles. Each generation further diffuses the initial input data, increasing the cryptographic complexity.
* **Non-Injective Mapping**: Reversing the state of a CA grid is computationally infeasible. Multiple distinct parent configurations can lead to a single child state, creating the "information loss" necessary for a one-way function.



---

## II. Empirical Analysis & Data Science

The algorithm's performance is validated using a comprehensive password dataset sourced from Kaggle, which provides both raw password strings and pre-computed complexity statistics.

### 1. Dataset Integration
This project utilizes a Kaggle dataset containing:
* **Raw Passwords**: The primary input for the LifeHash pipeline.
* **Pre-calculated Metadata**: Statistics including character length, uppercase/lowercase distribution, and special character density are used to evaluate how input complexity correlates with hash entropy.

### 2. Digital Ontogeny Visualization
The project utilizes `matplotlib` to render the transition of a hash through three distinct epistemological phases:
* **Raw State**: The initial, structured ASCII projection.
* **Start State**: The grid post-shuffling, showing increased entropy.
* **End State**: The final chaotic-yet-deterministic output after the specified number of generations.

---

## III. Technical Specifications

* **Vectorization**: `NumPy` for high-performance grid manipulation and axis-rolling.
* **Data Management**: `Pandas` for processing the Kaggle dataset and managing password metadata.
* **Visualization**: `Matplotlib` for rendering state-space transitions and bit-density histograms.

## IV. Usage

The following snippet demonstrates the iterative hashing pipeline. The simulation can be run for a desired amount of time before the final decoding occurs.

```python
# Define the input payload
password = "your_secure_password"

# 1. Encoding: Map the string to its numerical ASCII representation
encoded = encode(password)

# 2. Setup & Shuffling: Project onto an [N, M] grid and apply parity-based axis rolling
board = shuffle(setup(encoded, [7, 23]))

# 3. Iterative Evolution: Run the CA simulation for N generations. 
# The board evolves according to deterministic, local "physics" rules.
# You can specify the desired amount of simulation cycles here.
updated = update(board, iterations=50)

# 4. Finalization: Flatten the last generation and decode into a consistent hash
final_hash = decode(updated)

print(final_hash)
