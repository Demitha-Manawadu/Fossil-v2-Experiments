# Understanding FOSSIL's Architecture & Algorithm

## How FOSSIL Works (The "Why" Behind the Tool)

### The Problem FOSSIL Solves

**Classic Control Theory Question**: 
"Given a dynamical system `dx/dt = f(x)`, can you prove it's stable or safe?"

**Traditional Approach**: 
- Manually find Lyapunov functions (very hard!)
- Requires deep mathematical expertise
- No guarantees you'll succeed
- Takes weeks or months per system

**FOSSIL's Approach**:
- **Automated**: Searches for certificates automatically
- **Neural Networks**: Uses neural networks as function approximators
- **Sound**: Formally verifies results with SMT solvers
- **Fast**: Minutes instead of months!

---

## CEGIS: The Core Algorithm

### What is CEGIS?

**CEGIS** = **Counterexample-Guided Inductive Synthesis**

Think of it as a conversation between two agents:

```
Learner (Neural Network): "Here's my Lyapunov function!"
                          [Shows candidate function]

Verifier (SMT Solver):   "Nope, I found a counterexample!"
                         [Points to state where function breaks]

Learner:                 "Okay, I'll adjust and try again..."
                         [Retrains network with counterexample]

(repeat until Verifier says: "Yes, that works!")
```

### CEGIS Loop (Detailed)

```
┌─────────────────────────────────────┐
│   Initialize Neural Network N       │
│   with random weights               │
└────────────┬────────────────────────┘
             │
             ▼
┌─────────────────────────────────────┐
│   LEARNING PHASE                    │
│   Train N on data to satisfy         │
│   certificate conditions             │
│   (minimize loss function L)         │
└────────────┬────────────────────────┘
             │
             ▼
┌─────────────────────────────────────┐
│   VERIFICATION PHASE                │
│   Use SMT solver (Z3/dReal)          │
│   to verify N satisfies ALL          │
│   conditions everywhere              │
└────────────┬────────────────────────┘
             │
      ┌──────┴──────┐
      │             │
  SUCCESS      COUNTEREXAMPLE
      │             │
      │             ▼
      │   ┌──────────────────────────┐
      │   │ Add counterexamples to   │
      │   │ training data            │
      │   │ Go to LEARNING PHASE     │
      │   └──────────────────────────┘
      │
      ▼
┌─────────────────────────────────────┐
│   RETURN SUCCESSFUL CERTIFICATE     │
│   (Formal proof of correctness!)    │
└─────────────────────────────────────┘
```

### Concrete Example: Finding a Lyapunov Function

**System**: `dx/dt = -x - y`, `dy/dt = -x`

**Goal**: Find function V(x,y) such that:
1. V(0,0) = 0 (zero at origin)
2. V(x,y) > 0 for all (x,y) ≠ 0 (positive elsewhere)
3. dV/dt < 0 (decreases along trajectories)

**FOSSIL's Process**:

**Iteration 1:**
- Neural network suggests: V(x,y) = x² + y²
- Verifier checks condition 3 everywhere
- **Counterexample found**: At some point, dV/dt ≈ 0 (not negative!)
- Add that point to training data

**Iteration 2:**
- Network retrained with new data point
- Suggests: V(x,y) = x² + 1.5y² + 0.3xy
- Verifier checks again
- **Counterexample found**: Another point where condition fails
- Add to training data

**Iteration 3:**
- Network retrained
- Suggests: V(x,y) = x² + 2y² + 0.5xy
- Verifier checks thoroughly
- ✅ **SUCCESS**: All conditions satisfied everywhere!

---

## The Two Components: Learner & Verifier

### Component 1: The Learner (Neural Network)

**What it does**: Trains a neural network to approximate a valid certificate

**Architecture**:
```
Input layer:  x₀, x₁, ..., xₙ (state variables)
         │
Hidden layer: [N neurons with activation]
         │
Hidden layer: [M neurons with activation]
         │
Output layer: Single scalar (the certificate value)
```

**Example**:
```
For 2D system (x, y):
Input → [5 neurons, SIGMOID] → [3 neurons, SQUARE] → Output (scalar)
         
Represents: V(x,y) = f_network(x, y)
```

**Loss Function**:
The network is trained to minimize a loss L that encourages:
- For Lyapunov: V > 0, V(0)=0, dV/dt < 0 on training data
- For Barrier: B ≥ 0 in safe region, B ≤ 0 in unsafe region
- (Different for each certificate type)

**Optimization**: PyTorch with SGD/Adam optimizer

### Component 2: The Verifier (SMT Solver)

**What it does**: Proves (or disproves) that the network-generated certificate works everywhere

**Two Options**:

**Z3 (Fast, Less Complete)**:
- Polynomial arithmetic
- Good for bilinear/quadratic systems
- ~1 second per verification
- May miss some valid certificates

**dReal (Slow, More Complete)**:
- Nonlinear arithmetic over bounds
- Handles transcendental functions
- ~10-100 seconds per verification
- More likely to find valid certificates

**Verification Process**:
```
Verifier asks SMT solver:
"Does there exist a point (x,y) where the certificate fails?"

If solver says NO:
  ✅ Certificate is valid (proven!)
  
If solver says YES:
  ❌ Here's a counterexample point
  (Add to training data for next iteration)
```

---

## Neural Network Architectures

### Available Activations

| Activation | Formula | When to Use |
|-----------|---------|-----------|
| **SQUARE** | f(x) = x² | Simple systems, Lyapunov |
| **SIGMOID** | f(x) = 1/(1+e^(-x)) | Smooth approximations |
| **TANH** | f(x) = tanh(x) | Smoother version of sigmoid |
| **RELU** | f(x) = max(0, x) | Discrete-time systems |
| **POLY_2...8** | Polynomial of order N | Higher expressivity |
| **COSH** | f(x) = cosh(x) | Exponential growth |

### Architecture Selection Guide

**Simple Linear/Quadratic Systems**:
```yaml
N_HIDDEN_NEURONS: [5]
ACTIVATION: [SQUARE]
# Small network, sufficient for polynomial systems
```

**Nonlinear Systems**:
```yaml
N_HIDDEN_NEURONS: [10, 10]
ACTIVATION: [SIGMOID, SQUARE]
# Deeper network for complex functions
```

**Complex Space Partitioning**:
```yaml
N_HIDDEN_NEURONS: [20, 15, 10]
ACTIVATION: [SIGMOID, SIGMOID, SQUARE]
# Larger network for intricate behavior
```

---

## Data Sampling

### Why Training Data Matters

The neural network must see representative data to learn the certificate pattern.

### Sampling Strategy

**Data Points**: Distributed across the domain using:
- **Uniform sampling**: Regular grid
- **Random sampling**: Uniform random distribution
- **Rejection sampling**: Biased toward interesting regions

**Example**:
```yaml
DOMAINS:
  XD: Sphere([0,0], 1.0)  # Domain to cover

N_DATA:
  XD: 1000  # Generate 1000 samples uniformly in sphere
```

### Data Quality Impact

**Too Few Points** (< 100):
- Network may overfit to noise
- Verification fails
- Many iterations needed

**Optimal** (500-2000):
- Good coverage
- Fast learning
- Reasonable verification

**Too Many** (> 5000):
- Slower training
- No benefit
- Wastes computation

---

## Certificate Types (Deep Dive)

### 1. Lyapunov Function

**Proves**: Global stability

**For continuous systems**: 
```
dx/dt = f(x)

Conditions:
- V(0) = 0
- V(x) > 0 for all x ≠ 0
- ∇V · f(x) < 0 for all x
```

**Translation**: There exists an energy-like function that:
- is zero only at origin
- always decreases along system trajectories
- guarantees convergence to origin

### 2. Barrier Certificate

**Proves**: Safety (unsafe regions never reached)

**For continuous systems**:
```
Conditions:
- B(x) ≤ 0 in initial region XI
- B(x) > 0 in unsafe region XU
- ∇B · f(x) ≤ 0 in domain XD
```

**Translation**: Barrier function prevents trajectories from reaching unsafe region

### 3. Region of Attraction (ROA)

**Proves**: Size of stability basin

**Conditions**:
- Lyapunov for equilibrium point
- Find exact region where trajectories converge

**Output**: Level set of Lyapunov function representing ROA

### 4. Reach-While-Avoid (RWA)

**Proves**: Can reach goal without entering unsafe region

**Conditions**:
- Start in initial region XI
- Reach goal region XG
- Never enter unsafe region XU
- Stay within domain XD

**Example**: Robot navigates around obstacles to target

### 5. Reach-After-Reach (RAR)

**Proves**: Can reach intermediate waypoint, then final goal

**Conditions**:
- Reach intermediate region XF first
- Then reach final goal XG
- Full control over trajectory

**Example**: Multi-stage mission planning

---

## Performance Bottlenecks

### Where Time Goes

```
Total Time = Learning Time + Verification Time
           = (Neural Network Training) + (SMT Solver Solving)
           
Learning:      20-50% (SGD iterations on data)
Verification:  50-80% (SMT solver reasoning)
```

### Factors Affecting Speed

| Factor | Impact on Speed |
|--------|-----------------|
| System complexity | Larger coefficients → slower verification |
| Network size | Larger networks → slower training |
| Data points | More data → slower training |
| Verifier choice | dReal 10x slower than Z3, but stronger |
| Problem difficulty | Hard problems may take many iterations |
| Domain size | Larger domains → harder verification |

---

## Why This Matters: Comparison to Manual Methods

### Without FOSSIL (Manual Approach):

```
Expert Controls Engineer:
Week 1:  Read papers on Lyapunov theory
Weeks 2-3: Brainstorm candidate functions
Weeks 4-5: Test candidates, verify properties
         (May fail completely!)
Result:   If lucky: one function for one system
         Time: 1-2 months per system
```

### With FOSSIL:

```
FOSSIL User:
1 minute:  Write YAML config
30 seconds: Run fossil config.yaml
Result:   Automatic certificate found & formally verified
         Time: ~1 minute per system
```

### The Power:

- **Automated**: No need for expert knowledge
- **Reproducible**: Same config = same result
- **Scalable**: Run on 100s of systems
- **Verifiable**: Formal proof, not just heuristic

---

## Extension Points: Customizing FOSSIL

### For Advanced Users: Custom Certificates

FOSSIL is designed to be extensible. You can define custom certificates:

```python
# custom_cert.py
from fossil.certificate import Certificate

class CustomLyapunov(Certificate):
    def compute_loss(self, data, V):
        # Define custom loss function
        # This is minimized during learning
        pass
    
    def get_constraints(self, V, x, eps):
        # Define SMT constraints
        # This is checked during verification
        pass
    
    def learn(self, data, config):
        # Custom learning procedure
        pass
```

Then use it:
```python
opts = fossil.CegisConfig(
    CERTIFICATE=fossil.CertificateType.CUSTOM,
    CUSTOM_CERTIFICATE=CustomLyapunov(),
    ...
)
```

---

## Key Insights

### Why Neural Networks?

1. **Expressivity**: Can approximate any continuous function
2. **Efficiency**: Fast evaluation (one forward pass)
3. **Composability**: Layers can be stacked for complexity
4. **Differentiability**: Enables gradient-based learning

### Why SMT Solvers?

1. **Completeness**: Decide satisfiability (yes/no/unknown)
2. **Soundness**: Results are mathematically correct
3. **Counterexamples**: Guide learning when verification fails
4. **No Approximation**: Exact logic, not heuristic

### Why CEGIS?

1. **Guided Search**: Counterexamples focus learning on hard regions
2. **Convergence**: Iterative refinement guarantees finding valid certificate if exists
3. **Efficiency**: Don't waste time on obviously wrong candidates
4. **Adaptivity**: Learning focuses on falsification regions

---

## Research Innovations

### FOSSIL v1 (2021):
- First tool for automated Lyapunov/Barrier synthesis
- Demonstrated feasibility
- CEGIS architecture with neural networks

### FOSSIL v2 (2024):  
- Expanded certificate types (10+ types)
- Discrete-time support
- Control synthesis
- Improved scalability
- Better neural network architectures
- Pythonic API for researchers

---

## Mathematical Foundations

### Dynamical Systems

```
Continuous-time ODE:
dx/dt = f(x, u)
x ∈ ℝⁿ (state)
u ∈ ℝᵐ (control input)

Discrete-time:
x[k+1] = f(x[k], u[k])
```

### Lyapunov Stability (Continuous)

For system `dx/dt = f(x)`:

A function V: ℝⁿ → ℝ is a Lyapunov function if:
1. V(0) = 0 (zero at equilibrium)
2. V(x) > 0 for x ≠ 0 (positive definite)
3. dV/dt = ∇V·f(x) < 0 (decreases along trajectories)

→ **Implies**: x(t) → 0 as t → ∞ (stability)

### Barrier Certificates (Safety)

For system `dx/dt = f(x)`:

A function B: ℝⁿ → ℝ is a barrier certificate if:
1. B(x) ≤ 0 for x ∈ XI (non-positive in initial set)
2. B(x) > 0 for x ∈ XU (positive in unsafe set)
3. dB/dt = ∇B·f(x) ≤ 0 for x ∈ XD (non-increasing in domain)

→ **Implies**: Trajectories starting in XI never reach XU

---

## Conclusion

FOSSIL combines:
- **System Theory** (Lyapunov, barrier certificates)
- **Machine Learning** (neural networks)
- **Formal Methods** (SMT solvers)
- **Control Theory** (control synthesis)

Into a tool that automates what experts spent weeks doing, with formal guarantees!

The CEGIS loop is elegant: keep refining until you get it right. 🎯
