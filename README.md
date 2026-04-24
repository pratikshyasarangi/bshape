# Advanced Order-Independent Methodologies for Multi-Variable Model Attribution

## 1. Introduction to System Variance Dynamics and Attribution Mathematics

In sophisticated analytical modeling, the accurate quantification and attribution of output variance across different reporting states is a paramount mathematical directive. The evolution of multi-variable measurement frameworks relies on the precise, granular calibration of underlying structural components. These frameworks demand an explicit understanding of how individual parameters interact to generate the aggregate system output. 

When analyzing system dynamics longitudinally or subjecting the model to varying external stress scenarios, practitioners must perform a comprehensive attribution analysis. The primary objective is to decompose the total variance in the system output (the delta) into the discrete, independent contributions of underlying structural changes and external scenario shifts. 

Attributing this delta within a multiplicative model characterized by complex, nested interdependencies introduces profound mathematical challenges. Because input variables interact both multiplicatively and non-linearly, traditional sequential decomposition methods suffer from severe, inherent order-dependency. The calculated impact of an isolated parameter change varies dramatically depending on its position within the evaluation sequence, fundamentally compromising the analytical integrity of the attribution.

This document establishes an exhaustive, mathematically rigorous, and fully order-independent framework for isolating the impacts of structural parameter changes. It evaluates methodologies such as the Logarithmic Mean Divisia Index (LMDI) and Aumann-Shapley continuous allocations, ultimately establishing the Baseline Shapley (B-Shap) value framework as the optimal paradigm for handling discontinuous, step-function variables and nested dependencies.

---

## 2. Mathematical Formalization of the Target Architecture

To architect a rigorous attribution engine, the core model must be formalized as a multidimensional scalar function of its underlying covariates. The system output ($V_{out}$) for a single entity is governed by the master identity:

$$V_{out} = X_{mag} \times f_{prob} \times f_{sev} \times f_{ratio}$$

Within this structural equation, the parameters are dynamic, nested functions of underlying node attributes and exogenous global scenarios.

### 2.1 The Probability Function ($f_{prob}$)
This component is a composite metric derived from foundational state mappings and adjusted by multiple contextual scalars. It is mathematically represented as the product of four sub-functions:

$$f_{prob} = f_{matrix}(C_{state}) \times f_{stress}(S_{global}) \times f_{shift}(S_{global}) \times f_{size}(C_{state}, X_{mag})$$

* $C_{state}$ represents the categorical state or grade of the entity.
* $f_{matrix}(C_{state})$ is the base probability extracted from a static transition matrix.
* $f_{stress}(S_{global})$ is a systemic stress scalar parameterized by the global scenario severity.
* $f_{shift}(S_{global})$ is a specific non-linear clustering scalar active during extreme tail events.
* $f_{size}(C_{state}, X_{mag})$ establishes a direct dependency between the absolute magnitude ($X_{mag}$) and the categorical state, reflecting varying elasticities based on size.

### 2.2 The Severity Function ($f_{sev}$)
This function measures the proportional severity of the outcome, driven by segment assignments and scaled by external factors:

$$f_{sev} = g_{base}(C_{seg}) \times g_{stress}(S_{global})$$

* $C_{seg}$ denotes the structural segment mapping of the entity.
* $g_{base}(C_{seg})$ is the baseline severity percentage assigned to that specific segment.
* $g_{stress}(S_{global})$ is a scenario-dependent scalar applied to capture external environmental dynamics.

### 2.3 The Ratio Function ($f_{ratio}$)
This component calculates a projected ratio, incorporating current activation levels and unactivated thresholds. The formulation relies heavily on a logical flag ($F$):

$$f_{ratio} = \begin{cases} \frac{U + c_{1}(S_{global}) \times Un}{X_{mag}} & \text{if } F = \text{Type A} \\ \frac{U + c_{2}(S_{global}) \times Un}{X_{mag}} & \text{if } F = \text{Type B} \\ 1.0 & \text{if } F \notin \{\text{Type A, Type B}\} \end{cases}$$

* $U$ is the currently active/utilized amount.
* $Un$ is the inactive/unutilized threshold.
* $X_{mag}$ is the total maximum magnitude ($X_{mag} = U + Un$).
* $c_{1}$ and $c_{2}$ are conversion scalars dictating the proportion of the inactive threshold expected to activate.

### 2.4 Scenario Selection and Maximization
The system evaluates the environment under multiple external scenarios simultaneously ($\mathbb{S}$). The finalized output is a maximization function:

$$V_{final} = \max_{s \in \mathbb{S}} \left( V_{out}(\mathbf{X}_{system}, s) \right)$$

---

## 3. The Mathematical Limitations of Sequential Decomposition

Standard sequential attribution fails critically when applied to multiplicative models with nested dependencies. Consider a simplified multiplicative function of two fundamental variables: $V = x \times y$. The total change from a baseline state $(x_0, y_0)$ to a target state $(x_1, y_1)$ is:

$$\Delta V = x_1 y_1 - x_0 y_0$$

Expressed algebraically by separating the deltas:

$$\Delta V = (x_0 + \Delta x)(y_0 + \Delta y) - x_0 y_0$$
$$\Delta V = y_0 \Delta x + x_0 \Delta y + \Delta x \Delta y$$

The variance partitions into three components:
* $y_0 \Delta x$: Pure impact of $x$.
* $x_0 \Delta y$: Pure impact of $y$.
* $\Delta x \Delta y$: The interaction effect (synergism).

In sequential decomposition, the interaction effect is arbitrarily allocated to whichever variable is evaluated last.

| Decomposition Order | Impact Allocated to $x$ | Impact Allocated to $y$ | Treatment of Interaction ($\Delta x \Delta y$) |
| :--- | :--- | :--- | :--- |
| **Sequence 1: $x$ then $y$** | $y_0 \Delta x$ | $x_0 \Delta y + \Delta x \Delta y$ | Absorbed entirely by $y$ |
| **Sequence 2: $y$ then $x$** | $y_0 \Delta x + \Delta x \Delta y$ | $x_0 \Delta y$ | Absorbed entirely by $x$ |
| **Ideal Order-Independent** | $y_0 \Delta x + \frac{1}{2}\Delta x \Delta y$ | $x_0 \Delta y + \frac{1}{2}\Delta x \Delta y$ | Split symmetrically |

With six interdependent dimensions in the target model, sequential evaluation generates arbitrary residuals, obscuring the true foundational drivers of system variance.

---

## 4. Evaluating Index Decomposition Analysis: The LMDI Framework

The Logarithmic Mean Divisia Index (LMDI) is designed to achieve perfect decomposition in purely multiplicative models without leaving unexplained residuals.

### 4.1 Theoretical Foundation
For an aggregate variable $V = \prod_{i=1}^{n} X_i$, the logarithmic mean of two non-equal numbers $a$ and $b$ maps multiplicative changes into an additive domain:

$$L(a, b) = \frac{a - b}{\ln(a) - \ln(b)}$$$$L(a, a) = a$$

The additive impact of factor $X_i$ on the total change is:

$$\Delta V_{X_i} = L(V_1, V_0) \times \ln\left(\frac{X_{i,1}}{X_{i,0}}\right)$$

### 4.2 Application Challenges
Despite its elegance, LMDI suffers from two critical mathematical limitations in nested architectures:
1.  **Zero-Value Intolerance:** The logarithmic mean relies on natural logarithms, undefined at zero. If initial or final states equal exactly zero (e.g., node creation or termination), approximation errors are introduced.
2.  **Inability to Handle Nested Additive Dependencies:** The formula assumes pure, flat multiplication. It lacks the dimensionality to disentangle variables nested within additive or piecewise components (e.g., the threshold logic within $f_{ratio}$).

---

## 5. Cooperative Game Theory and the Axiomatic Foundation

The most rigorous, strictly order-independent method relies on the Shapley value. Formulated in cooperative game theory, it allocates variance based on the marginal contribution of each variable across all possible structural permutations.

### 5.1 Axioms of Fair Attribution
The Shapley value is mathematically unique in satisfying four fundamental axioms:
* **Efficiency:** The sum of attributed impacts exactly equals the total variance. $\sum \phi_i = \Delta V$.
* **Symmetry:** Variables with identical marginal impacts across all configurations receive identical attribution.
* **Dummy Player:** A variable with zero mathematical impact across all configurations receives an attribution of exactly zero.
* **Additivity:** Total attribution across an aggregated system perfectly equals the sum of granular node-level attributions.

### 5.2 Mathematical Formulation
For a subset of features $S \subseteq N$, let $v(S)$ be a characteristic function computing the output by substituting Target state values for factors in $S$, holding others at Baseline values. The Shapley attribution $\phi_k$ for factor $k$ is:

$$\phi_k = \sum_{S \subseteq N \setminus \{k\}} \frac{|S|!(|N| - |S| - 1)!}{|N|!} \left[ v(S \cup \{k\}) - v(S) \right]$$

This exhaustive combinatoric averaging perfectly symmetrically distributes all higher-order interaction terms.

---

## 6. Continuous vs. Discrete Frameworks

### 6.1 The Aumann-Shapley Value
For models continuously differentiable with respect to all inputs, Aumann-Shapley integrates the partial derivative along a straight-line diagonal path from Baseline to Target state:

$$a_i = (x_i^{(1)} - x_i^{(0)}) \int_0^1 \frac{\partial f}{\partial x_i} \left( \mathbf{x}^{(0)} + t(\mathbf{x}^{(1)} - \mathbf{x}^{(0)}) \right) dt$$

### 6.2 The Baseline Shapley (B-Shap) Necessity
Because the target model contains discrete lookups, logical flags, and step-functions, partial derivatives ($\frac{\partial f}{\partial x_i}$) mathematically do not exist at segment boundaries. B-Shap treats the problem strictly as a discrete cooperative game, executing the evaluation function $2^K$ times (representing every binary combination of inputs). 

| Methodology | Handling of Multiplicative Interdependencies | Zero-Value Tolerance | Compatibility with Discrete Variables | Computational Complexity |
| :--- | :--- | :--- | :--- | :--- |
| **Sequential** | Poor (Arbitrary Order Bias) | High | High | Very Low |
| **LMDI** | Perfect for strict multiplication | Very Low | High | Very Low |
| **Aumann-Shapley** | Perfect (Symmetric Allocation) | High | Very Low | High |
| **B-Shap** | Perfect (Symmetric Allocation) | High | High | Moderate ($2^K$ runs per node) |

---

## 7. Operationalizing B-Shap for Nested Interdependencies

To operationalize B-Shap for $n=6$ structural vectors, the algorithm identifies active deltas, generates up to $2^6 = 64$ hybrid data states per node, processes each through the master output function, and calculates marginal contributions.

Crucially, this seamlessly deconstructs nested dependencies without double-counting:

### 7.1 Decomposing Magnitude Impacts
The magnitude variable ($X_{mag}$) drives the base multiplier, the nested size factor $f_{size}$, and the denominator in $f_{ratio}$. When the B-Shap algorithm evaluates a coalition updating $X_{mag}$, it injects the new value into every step of the algorithmic chain simultaneously. Testing this against all permutations of other variables natively isolates the true structural impact of scaling.

### 7.2 Decomposing Categorical State Migrations
Updating a categorical state ($C_{state}$) alters base probability matrices, size factors, and potentially scenario stress groupings. Evaluating the "State Player" tests these nested updates concurrently, decoupling intrinsic entity deterioration from systemic scenario shifts.

### 7.3 Resolving Scenario Shifts and Peak-Maximization
When the systemic $\max$ function selects a new dominant scenario, it alters all fundamental multipliers. By defining the "Scenario Player" as the transition from Baseline stress rules to Target stress rules, B-Shap quantifies precisely the variance driven strictly by the changing external environment, isolated from internal composition changes.
