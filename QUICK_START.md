# FOSSIL v2.0 - Quick Start Guide

## 60-Second Overview

**FOSSIL** = A tool that automatically proves your dynamical system is stable/safe using AI-generated Lyapunov functions and barrier certificates.

**In 3 simple steps**:
1. Write system dynamics in YAML
2. Run `fossil config.yaml`
3. Get formal proof your system works!

---

## Installation (Choose One)

### Option A: Docker (Recommended - 2 minutes)
```powershell
# Windows PowerShell
docker pull aleccedwards/fossil:latest
docker run --rm aleccedwards/fossil fossil -h
```

### Option B: Native Linux (Ubuntu 22.04 - 15 minutes)
```bash
curl -fsSL https://raw.githubusercontent.com/dreal/dreal4/master/setup/ubuntu/22.04/install_prereqs.sh | sudo bash
cd /path/to/Fossil-v2-Experiments
pip3 install .
fossil -h
```

---

## Your First Run (30 seconds)

### Using Docker:
```powershell
mkdir fossil_results
docker run -it --rm -v ${pwd}/fossil_results:/fossil/results `
  aleccedwards/fossil fossil lyap-test.yaml
```

### Native Installation:
```bash
fossil lyap-test.yaml
```

**Expected Output**:
```
SUCCESS: Lyapunov function synthesized!
Time: 1.234 seconds
Iterations: 3
```

---

## Understanding YAML Config

Create `my_system.yaml`:

```yaml
# Your system dynamics (dx/dt = f(x, u))
SYSTEM: [x1, -x1 - x0]

# What you want to prove
CERTIFICATE: Lyapunov        # or Barrier, ROA, RWA, etc.

# Where the system operates
DOMAINS:
  XD: Sphere([0,0], 1.0)     # Origin-centered sphere, radius 1.0

# Type of system
TIME_DOMAIN: CONTINUOUS       # CONTINUOUS or DISCRETE

# How to verify
VERIFIER: Z3                  # Z3 (fast) or DREAL (stronger)

# Training data points
N_DATA:
  XD: 1000                    # Generate 1000 samples

# System size
N_VARS: 2                     # 2 state variables: x0, x1

# Neural network for certificate
N_HIDDEN_NEURONS: [5]         # One hidden layer, 5 neurons
ACTIVATION: [SQUARE]          # SQUARE, SIGMOID, TANH, RELU, etc.

# Random seed (for reproducibility)
SEED: 0
```

### Run it:
```bash
fossil my_system.yaml
```

---

## Adding a Controller

Want to synthesize a controller too?

```yaml
SYSTEM: ["x1 + u0", "x0-x1"]  # u0 is control input

# ... rest of config ...

# Controller synthesis
CTRLAYER: [5, 1]              # Hidden layer: 5 neurons, Output: 1
CTRLACTIVATION: ["LINEAR"]    # Linear output
```

---

## Advanced: Python API

For more control or custom problems:

```python
import fossil

# Define system as Python class
class MySystem(fossil.control.DynamicalModel):
    n_vars = 2
    
    def f_torch(self, v):
        x, y = v[:, 0], v[:, 1]
        return [y, -x - y + 1/3*x**3]
    
    def f_smt(self, v):
        x, y = v
        return [y, -x - y + 1/3*x**3]

# Define domains
system = MySystem()
XD = fossil.domains.Rectangle([-3, -2], [2.5, 1])
XI = fossil.domains.Sphere([1.5, 0], 0.5)
XU = fossil.domains.Sphere([-1, -1], 0.4)

# Configure
opts = fossil.CegisConfig(
    N_VARS=2,
    SYSTEM=system,
    DOMAINS={fossil.XD: XD, fossil.XI: XI, fossil.XU: XU},
    DATA={
        fossil.XD: XD._generate_data(1000),
        fossil.XI: XI._generate_data(400),
        fossil.XU: XU._generate_data(400),
    },
    CERTIFICATE=fossil.CertificateType.BARRIER,
    TIME_DOMAIN=fossil.TimeDomain.CONTINUOUS,
    VERIFIER=fossil.VerifierType.DREAL,
    ACTIVATION=[fossil.ActivationType.SIGMOID] * 2,
    N_HIDDEN_NEURONS=[10] * 2,
    CEGIS_MAX_ITERS=25,
    SEED=167,
)

# Synthesize!
result = fossil.synthesise(opts)
if result.successful:
    print("Success!")
```

Run:
```bash
python3 my_script.py
```

---

## Understanding Results

### Success Output:
```
SUCCESS: Barrier certificate synthesized!
Synthesis Time: 2.456 seconds
CEGIS Iterations: 5
```

**What this means**:
- ✅ A neural network was found that provably works as a Barrier
- ✅ dReal/Z3 formally verified it meets all mathematical conditions
- ✅ Your system is SAFE in the specified domain

### Failure Output:
```
FAILURE: Could not synthesize certificate after 10 iterations
Best loss achieved: 0.234
```

**What this means**:
- ❌ No valid certificate found within iteration limit
- Try: larger network, more training data, simpler problem, DREAL verifier

---

## Available Certificate Types

| Certificate | Purpose | For Systems |
|------------|---------|------------|
| **Lyapunov** | Prove stability (all trajectories → origin) | Continuous & Discrete |
| **Barrier** | Prove safety (avoid bad regions) | Continuous & Discrete |
| **ROA** | Find region of attraction around stable point | Continuous |
| **RWA** | Reach goal while avoiding bad region | Continuous |
| **RAR** | Reach goal after reaching intermediate point | Continuous |
| **RWS** | Reach goal while staying safe | Continuous |
| **STABLESAFE** | Stability + Safety combined | Continuous |

---

## Reproducing Paper Results

### Quick Experiments:

**Table 2 (v1 vs v2 comparison) - 10 min**:
```bash
docker run -it --rm -v ${pwd}/fossil_results:/fossil/results `
  aleccedwards/fossil python3 -m experiments.fossil1-comparison
```

**Table 3 (Full benchmark) - 1-2 hours**:
```bash
docker run -it --rm -v ${pwd}/fossil_results:/fossil/results `
  aleccedwards/fossil ./run_hscc_experiments.sh
```

**Figure 4 (Visualization) - 1 min**:
```bash
docker run -it --rm -v ${pwd}/fossil_results:/fossil/results `
  aleccedwards/fossil fossil experiments/benchmarks/cli/ctrl_rar_sine.yaml --plot
```

---

## System Requirements

### Hardware
| Component | Requirement |
|-----------|------------|
| CPU | Intel/AMD only (NOT ARM like M1/M2 Mac) |
| RAM | 8GB minimum, 16GB recommended |
| GPU | None (CPU-only tool) |
| Disk | 5GB free space |

### Software
- **Python**: 3.9+
- **OS**: Ubuntu 22.04, macOS, Windows (via Docker)
- **Docker** (recommended): Latest Docker Desktop

---

## Troubleshooting

**Q: "dReal not found"**  
A: Use Docker, or: `pip3 install dreal`

**Q: "ARM processor not supported"**  
A: Can't run on M1/M2 Macs natively. Use Docker with x86_64 emulation.

**Q: Out of memory**  
A: Reduce `N_DATA` or `N_HIDDEN_NEURONS` in config

**Q: Synthesis is very slow**  
A: Set `CEGIS_MAX_TIME_S: 300` to timeout after 5 min. Try Z3 instead of DREAL.

**Q: Do I need GPU?**  
A: No. FOSSIL doesn't use GPU at all.

---

## Next Steps

1. ✅ Install Docker or native environment
2. ✅ Run test: `fossil lyap-test.yaml`
3. ✅ Create your own `my_system.yaml`
4. ✅ Try different certificate types
5. ✅ Read detailed guide: `FOSSIL_COMPLETE_GUIDE.md`
6. ✅ Check papers in `papers/` folder

---

## File Examples

### Simple Lyapunov (Stability):
See: `experiments/benchmarks/cli/lyap-test.yaml`

### Barrier (Safety):
See: `experiments/benchmarks/cli/barr-test.yaml`

### With Controller:
See: `experiments/benchmarks/cli/ctrl_rar_sine.yaml`

---

## Key Commands Reference

```bash
# Run existing config
fossil config.yaml

# Run with plotting
fossil config.yaml --plot

# Help
fossil -h

# Enter Docker shell
docker run -it aleccedwards/fossil bash

# Interactive Python in Docker
docker run -it aleccedwards/fossil python3
```

---

## Papers & References

- **FOSSIL v2.0 (2024)**: `papers/v2.pdf` - HSCC '24
- **FOSSIL v1.0 (2021)**: `papers/v1.pdf` - HSCC '21

---

## Support

- 📖 Full guide: `FOSSIL_COMPLETE_GUIDE.md`
- 📋 YAML reference: `user-guide.md`
- 🎯 Domain guide: `set_guide.md`
- 🔬 Reproducibility: `repeatability_instructions.md`

Enjoy synthesizing certificates! 🚀
