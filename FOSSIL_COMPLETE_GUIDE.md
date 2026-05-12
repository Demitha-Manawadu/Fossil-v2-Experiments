# FOSSIL - Complete User Guide & Overview

## Table of Contents
1. [What is FOSSIL?](#what-is-fossil)
2. [Version Differences (v1 vs v2)](#version-differences)
3. [System Requirements](#system-requirements)
4. [Installation Guide](#installation-guide)
5. [How to Run FOSSIL](#how-to-run-fossil)
6. [Configuration Guide](#configuration-guide)
7. [Reproducing Paper Results](#reproducing-paper-results)
8. [Results & Outputs](#results--outputs)
9. [GPU Requirements](#gpu-requirements)
10. [Troubleshooting](#troubleshooting)

---

## What is FOSSIL?

**FOSSIL** stands for **Formal Synthesis of Lyapunov-like functions**. It is a sophisticated software tool for **sound and automated synthesis** of Lyapunov-like functions to verify the **stability and safety** of continuous-time and discrete-time dynamical systems.

### Key Capabilities:
- **Automated Certificate Synthesis**: Automatically generates Lyapunov functions and barrier certificates
- **Neural Network-Based**: Uses neural networks as function approximators for certificates
- **CEGIS-Based Architecture**: Employs Counterexample-Guided Inductive Synthesis for robust verification
- **Formal Verification**: Provides sound proofs that dynamical systems satisfy specified properties
- **Control Synthesis**: Can synthesize controllers to stabilize or control systems

### Real-World Applications:
- Autonomous vehicle safety verification
- Aircraft control system validation
- Robotics system stability proofs
- Network system verification
- Safety-critical system analysis

---

## Version Differences

### FOSSIL v1.0 (2021)
**Paper**: "FOSSIL: A Software Tool for the Formal Synthesis of Lyapunov Functions and Barrier Certificates using Neural Networks" (HSCC '21)

**Capabilities**:
- Lyapunov function synthesis for stability verification
- Barrier certificate synthesis for safety verification
- Continuous-time systems only
- Neural networks as candidates for verification

**Citation**:
```bibtex
@inproceedings{Abate_Ahmed_Edwards_Giacobbe_Peruffo_2021,
  title={FOSSIL: A Software Tool for the Formal Synthesis of Lyapunov Functions and Barrier Certificates using Neural Networks},
  booktitle={Proceedings of the 24th International Conference on Hybrid Systems: Computation and Control},
  publisher={ACM},
  author={Abate, Alessandro and Ahmed, Daniele and Edwards, Alec and Giacobbe, Mirco and Peruffo, Andrea},
  year={2021},
  month={May}
}
```

### FOSSIL v2.0 (2024)
**Paper**: "Fossil 2.0: Formal Certificate Synthesis for the Verification and Control of Dynamical Models" (HSCC '24)

**Enhancements over v1**:
- **Expanded Certificate Types**: Now supports 10+ certificate types:
  - Lyapunov (stability)
  - Barrier & BarrierAlt (safety)
  - Reach-While-Avoid (RWA)
  - Region of Attraction (ROA)
  - Reach-After-Reach (RAR)
  - And more...
- **Discrete-Time Support**: Extended to discrete-time systems
- **Control Synthesis**: Built-in controller synthesis
- **Multiple Interfaces**: CLI via YAML, Python API for custom workflows
- **Better Verification**: Improved dReal integration for stronger verification

**Citation**:
```bibtex
@inproceedings{Edwards_Peruffo_Abate_2024,
  series={HSCC '24},
  title={Fossil 2.0: Formal Certificate Synthesis for the Verification and Control of Dynamical Models},
  booktitle={Proceedings of the 27th International Conference on Hybrid Systems: Computation and Control},
  publisher={ACM},
  author={Edwards, Alec and Peruffo, Andrea and Abate, Alessandro},
  year={2024}
}
```

---

## System Requirements

### Hardware Requirements

**Processor**:
- ✅ **CPU-ONLY** (GPU not required or used)
- ❌ **NOT ARM-based**: Apple M1/M2/M3 Macs, Raspberry Pis not supported (hard dependency limitation)
- Recommended: Multi-core processor (6+ cores beneficial for parallel experiments)
- Tested on: Intel i5/i7 processors

**RAM**:
- **Minimum**: 8GB RAM
- **Recommended**: 16GB RAM
- The tool has been tested on machines with 8GB and 16GB; not tested with less

**Storage**:
- **Free Disk Space**: At least 5GB (includes Docker image ~2.2GB + dependencies)
- Good internet connection recommended for downloading dependencies

**Operating System**:
- ✅ **Linux**: Ubuntu 22.04 (primary, fully tested)
- ✅ **macOS**: Sonoma 14.1+ (tested, may have minor issues)
- ⚠️ **Windows**: Can use via Docker or WSL2 (Docker recommended)

### Software Prerequisites

1. **Python**: 3.9 or later
2. **Docker**: Recommended and easiest method
3. **dReal4** (SMT Solver): Needed for verification
4. **Additional Dependencies**: Z3, PyTorch, SymPy, etc.

---

## Installation Guide

### Method 1: Docker (Recommended - Easiest)

**Why Docker?**
- Pre-configured environment with all dependencies
- Works on Windows, Mac, and Linux
- No complex dependency installation
- Guaranteed to work as tested

**Steps**:

1. **Install Docker**:
   - Windows/Mac: [Download Docker Desktop](https://www.docker.com/products/docker-desktop)
   - Linux: `apt-get install docker.io`

2. **Pull the FOSSIL Docker Image**:
   ```bash
   docker pull aleccedwards/fossil:latest
   ```

3. **Verify Installation**:
   ```bash
   docker run --rm aleccedwards/fossil fossil -h
   ```
   You should see the help message.

4. **Create Results Directory** (for saving outputs):
   ```bash
   mkdir fossil_results
   ```

### Method 2: Native Installation (Advanced - Linux/Ubuntu 22.04)

**For Ubuntu 22.04**:

1. **Install dReal prerequisites**:
   ```bash
   curl -fsSL https://raw.githubusercontent.com/dreal/dreal4/master/setup/ubuntu/22.04/install_prereqs.sh | sudo bash
   ```

2. **Install dReal**:
   ```bash
   pip3 install dreal
   ```

3. **Clone/Navigate to FOSSIL**:
   ```bash
   cd /path/to/Fossil-v2-Experiments
   ```

4. **Install FOSSIL**:
   ```bash
   pip3 install .
   ```

5. **Verify**:
   ```bash
   fossil -h
   ```

**⚠️ Note**: dReal has complex build requirements. Docker is strongly recommended to avoid installation headaches.

### Method 3: Development Installation (Python API)

If you want to modify or extend FOSSIL code:

1. Install dependencies:
   ```bash
   pip3 install -r requirements.txt
   ```

2. Install in development mode:
   ```bash
   pip3 install -e .
   ```

---

## How to Run FOSSIL

### Method 1: Command-Line Interface (YAML Configuration)

**Simplest for standard benchmarks**

1. **Using Docker**:
   ```bash
   docker run -it --rm -v $(pwd)/fossil_results:/fossil/results \
     aleccedwards/fossil fossil lyap-test.yaml
   ```

2. **Native Installation**:
   ```bash
   fossil lyap-test.yaml
   ```

**What Happens**:
- FOSSIL reads the YAML configuration
- Synthesizes a certificate (Lyapunov, Barrier, etc.)
- Prints results to console
- May save plots/results depending on configuration

### Method 2: Python API (For Custom Workflows)

**Most flexible - full programmatic control**

Example (from `example.py`):

```python
import fossil
from fossil import plotting

# Define your system as a class
class MySystem(fossil.control.DynamicalModel):
    n_vars = 2  # Number of variables (x and y)
    
    def f_torch(self, v):
        """PyTorch version for neural network training"""
        x, y = v[:, 0], v[:, 1]
        return [y, -x - y + 1/3 * x**3]
    
    def f_smt(self, v):
        """SMT solver version for verification"""
        x, y = v
        return [y, -x - y + 1/3 * x**3]

# Define system domains
system = MySystem()
XD = fossil.domains.Rectangle([-3, -2], [2.5, 1])
XI = fossil.domains.Sphere([1.5, 0], 0.5)
XU = fossil.domains.Sphere([-1, -1], 0.4)

sets = {
    fossil.XD: XD,
    fossil.XI: XI,
    fossil.XU: XU,
}

data = {
    fossil.XD: XD._generate_data(1000),
    fossil.XI: XI._generate_data(400),
    fossil.XU: XU._generate_data(400),
}

# Configure synthesis options
opts = fossil.CegisConfig(
    N_VARS=2,
    SYSTEM=system,
    DOMAINS=sets,
    DATA=data,
    CERTIFICATE=fossil.CertificateType.BARRIER,
    TIME_DOMAIN=fossil.TimeDomain.CONTINUOUS,
    VERIFIER=fossil.VerifierType.DREAL,
    ACTIVATION=[fossil.ActivationType.SIGMOID, fossil.ActivationType.SIGMOID],
    N_HIDDEN_NEURONS=[10, 10],
    CEGIS_MAX_ITERS=25,
    SEED=167,
)

# Run synthesis
result = fossil.synthesise(opts)

# Check results
if result.successful:
    print("Certificate synthesis successful!")
else:
    print("Certificate synthesis failed")
```

**Run with**:
```bash
python3 example.py
```

### Method 3: Interactive Docker Session

**For exploration and testing**

```bash
# Enter interactive shell in Docker container
docker run -it --rm -v $(pwd)/fossil_results:/fossil/results \
  aleccedwards/fossil bash

# Inside container, run commands:
fossil lyap-test.yaml
python3 example.py
```

---

## Configuration Guide

### Basic YAML Configuration Structure

Here's a minimal example (from `lyap-test.yaml`):

```yaml
# System dynamics
SYSTEM: [x1, -x1 - x0]

# Certificate type to synthesize
CERTIFICATE: Lyapunov

# Domain definitions
DOMAINS:
  XD: Sphere([0,0], 1.0)  # Region of interest

# Time domain
TIME_DOMAIN: CONTINUOUS

# Verification engine
VERIFIER: Z3  # or DREAL

# Number of training data points
N_DATA:
  XD: 1000

# System dimensions
N_VARS: 2

# Neural network configuration for certificate
N_HIDDEN_NEURONS: [5]  # One hidden layer with 5 neurons
ACTIVATION: [SQUARE]   # Activation function: SQUARE, SIGMOID, TANH, etc.

# Optional: Random seed for reproducibility
SEED: 0
```

### More Complex Example (Barrier Certificate with Control)

From `barr-test.yaml`:

```yaml
SYSTEM: ["x1 + u0", "x0-x1"]  # u0, u1 are control inputs
CERTIFICATE: "Barrier"
DOMAINS:
  XD: Sphere([0,0], 1.1)       # Entire domain
  XI: Sphere([0,0], 0.1)       # Initial conditions
  XU: Sphere([0.8,0.8], 0.1)   # Unsafe region
TIME_DOMAIN: "CONTINUOUS"
VERIFIER: "DREAL"

# Data point counts
N_DATA:
  XD: 1000
  XI: 1000
  XU: 1000

# System and NN config
N_VARS: 2
N_HIDDEN_NEURONS: [5]
ACTIVATION: ["SQUARE"]

# Controller synthesis
CTRLAYER: [5, 1]              # Controller: hidden layer(5) -> output(1)
CTRLACTIVATION: ["LINEAR"]    # Linear activation for controller

SEED: 0
```

### Key Configuration Parameters

| Parameter | Values | Description |
|-----------|--------|-------------|
| **CERTIFICATE** | Lyapunov, Barrier, BarrierAlt, ROA, RWA, RAR, RSWA, RWS, RSWS, STABLESAFE | Property type to prove |
| **VERIFIER** | Z3, DREAL | SMT solver for verification (dReal stronger) |
| **TIME_DOMAIN** | CONTINUOUS, DISCRETE | System domain (continuous/discrete-time) |
| **N_HIDDEN_NEURONS** | [10, 5, 3] | Neural network architecture (list of layer sizes) |
| **ACTIVATION** | SQUARE, SIGMOID, TANH, RELU, POLY_2...8, EVEN_POLY_4...10 | Activation functions per layer |
| **CEGIS_MAX_ITERS** | Integer | Max iterations for CEGIS loop (default: 10) |
| **CEGIS_MAX_TIME_S** | Integer/Float | Max runtime in seconds (default: infinity) |
| **N_DATA** | Integer/Dict | Data points per domain for training |
| **SEED** | Integer | Random seed for reproducibility |

### Domain Types Available

```python
fossil.domains.Sphere([center_x, center_y], radius)
fossil.domains.Rectangle([min_x, min_y], [max_x, max_y])
fossil.domains.Torus([center_x, center_y], radius, thickness)
fossil.domains.Union(domain1, domain2)  # Combine domains
```

---

## Reproducing Paper Results

### Overview of Experiments

The paper "Fossil 2.0" includes 3 main reproducible experiments:

| Experiment | Table/Figure | Duration | What it Shows |
|------------|-------------|----------|---------------|
| Fossil v1 vs v2 Comparison | Table 2 | ~10 min | Performance comparison with v1 |
| Extended Benchmarks | Table 3 | ~1-2 hours | Results on 17 benchmark systems |
| Example Certificate | Figure 4 | ~1 min | Visualization of synthesized certificate |

### Experiment 1: Fossil v1 vs v2 Comparison (Table 2)

**Purpose**: Compare performance improvements from v1 to v2

**Runtime**: ~10 minutes

**Command**:
```bash
docker run -it --rm -v $(pwd)/fossil_results:/fossil/results \
  aleccedwards/fossil python3 -m experiments.fossil1-comparison
```

**Output Files**:
- `_raw_fossil1_comparison.csv`: Raw data (all runs)
- `fossil1_comparison.csv`: Averaged results (10 runs)
- `fossil1_comparison.tex`: LaTeX formatted table

**Example Output**:
Shows success rates and run times comparing v1 and v2 on various benchmarks.

### Experiment 2: Full Benchmark Suite (Table 3)

**Purpose**: Comprehensive evaluation on 17 diverse systems

**Runtime**: 1-2 hours (depends on CPU)

**Command**:
```bash
docker run -it --rm -v $(pwd)/fossil_results:/fossil/results \
  aleccedwards/fossil ./run_hscc_experiments.sh
```

**Output Files**:
- `_raw_tool_results.csv`: Complete raw data
- `tool_paper_tab.csv`: Averaged results
- `tool_paper_tab.tex`: LaTeX table for paper

**What Gets Tested**:
- Multiple certificate types (Lyapunov, Barrier, ROA, RWA, RAR, etc.)
- Continuous and discrete-time systems
- Various neural network configurations
- Different verification engines (Z3 vs dReal)

### Experiment 3: Certificate Visualization (Figure 4)

**Purpose**: Generate visual representation of synthesized certificate

**Runtime**: ~1 minute

**Command**:
```bash
docker run -it --rm -v $(pwd)/fossil_results:/fossil/results \
  aleccedwards/fossil fossil experiments/benchmarks/cli/ctrl_rar_sine.yaml --plot
```

**Output Files**:
- `*.pdf`: Two plots showing:
  1. Phase portrait with certificate zero contours (dashed lines)
  2. 3D surface plot of the certificate function

**What You'll See**:
- System dynamics visualized as vector field
- Certificate function shown as contour plot
- Controller behavior if synthesized

---

## Results & Outputs

### Where Results Are Saved

**In Docker**:
```
fossil_results/          # Mounted from host
  ├── _raw_fossil1_comparison.csv
  ├── fossil1_comparison.csv
  ├── fossil1_comparison.tex
  ├── _raw_tool_results.csv
  ├── tool_paper_tab.csv
  ├── tool_paper_tab.tex
  └── *.pdf              # Visualization files
```

### Understanding Results

#### CSV Format Example
```
benchmark_name,success,time_seconds,num_iterations,verifier_solver,certificate_type
linear_system,1,1.234,5,DREAL,Lyapunov
nonlinear_1,1,2.456,8,Z3,Barrier
```

#### Console Output Example
```
FOSSIL v2.0 - Synthesis Engine
================================
Loading configuration: lyap-test.yaml
System: x1, -x1 - x0
Certificate: Lyapunov
Domains: 
  - XD: Sphere([0,0], 1.0)
  
Starting CEGIS loop...
Iteration 1: Learning... Verifying... ✓
Iteration 2: Learning... Verifying... ✓
Iteration 3: Learning... Verifying... ✓

SUCCESS: Certificate synthesized!
Synthesis time: 1.234 seconds
Iterations: 3
Certificate type: Neural Network (2 hidden layers)
```

### Interpreting Results

- **Success**: Certificate found and formally verified
- **Failure**: No certificate found after max iterations
- **Time**: Total time including learning and verification
- **Iterations**: Number of CEGIS loops performed

---

## GPU Requirements

### ⚠️ GPU NOT Required

FOSSIL **does not use GPU acceleration**. Here's why:

1. **CPU-Only Design**: The tool was designed to run on CPU
2. **PyTorch CPU**: Dependency is `torch==2.0.0+cpu` (CPU-only)
3. **SMT Solvers**: dReal and Z3 optimized for single-core CPU
4. **Bottleneck**: Verification (SMT solving) is CPU-bound, not GPU-amenable

### Performance Notes

- Better CPU performance = Faster results
- Single-core performance more important than core count
- Typical run times: 1 minute to several hours (depending on problem complexity)
- Parallel benchmarks use multi-threading (set by `N_PROCS=4`)

### RAM is More Important Than GPU

Since no GPU is available, ensure you have adequate RAM:
- 8GB: Minimum (may be tight)
- 16GB: Comfortable (recommended)
- 32GB+: No additional benefit for typical problems

---

## Troubleshooting

### Common Issues

#### Issue 1: "dReal-Python API not found"

**Symptoms**: Import error when trying to use DREAL verifier

**Solution**:
```bash
# Try with Docker (easiest)
docker run -it aleccedwards/fossil bash

# Or reinstall dReal
pip3 install --upgrade dreal
```

#### Issue 2: "ARM processor not supported"

**Error**: Hard incompatibility with ARM processors

**Solution**:
- Use Docker on Intel/AMD machines
- Cannot run on M1/M2/M3 Macs natively
- Apple Silicon users: Use Docker or parallels VM with Ubuntu

#### Issue 3: Out of Memory

**Symptoms**: Process killed, "MemoryError"

**Solution**:
1. Reduce `N_DATA` in YAML (fewer training points)
2. Reduce neural network sizes (`N_HIDDEN_NEURONS`)
3. Add more RAM to machine
4. Consider breaking problem into smaller subsets

#### Issue 4: Z3 vs dReal results differ

**Expected**: Different SMT solvers may produce different verification results

**Note**: dReal is generally stronger but slower

#### Issue 5: Plots not displaying

**Symptoms**: PNG/PDF files created but can't view in Docker

**Solution**:
- Check output directory: `fossil_results/`
- The files are there, just need to view on host machine
- Use `--plot` flag if not generating plots

#### Issue 6: Very slow synthesis

**Symptoms**: Stuck at "Verifying..." for long time

**Causes**:
- Using DREAL with complex nonlinear systems (slow verification)
- Problem is genuinely hard
- Configuration not well-suited to problem

**Solutions**:
1. Set `CEGIS_MAX_TIME_S: 300` to timeout after 5 minutes
2. Try simpler network architecture
3. Use Z3 instead of DREAL (faster, less complete verification)
4. Reduce `N_DATA` for faster learning

### Debug Mode

Enable verbose output:

```yaml
VERBOSE: True  # or level: 1, 2, 3 for more detail
```

Or in Python API:
```python
opts = fossil.CegisConfig(
    ...
    VERBOSE=1,  # 0=silent, 1=normal, 2=detailed
    ...
)
```

### Getting Help

1. **Read the papers**: [papers/v2.pdf](papers/v2.pdf)
2. **Check user guide**: [user-guide.md](user-guide.md)
3. **Review examples**: [experiments/benchmarks/cli/](experiments/benchmarks/cli/)
4. **GitHub issues**: Report bugs on Oxford-OxCAV/fossil repository

---

## Quick Start Checklist

### For First-Time Users:

- [ ] Read this guide introduction
- [ ] Install Docker
- [ ] Pull FOSSIL image: `docker pull aleccedwards/fossil`
- [ ] Create results directory: `mkdir fossil_results`
- [ ] Run test: `docker run -it --rm aleccedwards/fossil fossil -h`
- [ ] Try quick demo: `docker run -it --rm aleccedwards/fossil fossil lyap-test.yaml`

### For Reproducing Paper Results:

- [ ] Complete first-time setup above
- [ ] Run Table 2 experiment (~10 min)
- [ ] Run Table 3 experiment (~1-2 hours)
- [ ] Generate Figure 4 (~1 min)
- [ ] Compare with paper in docs

### For Custom Problems:

- [ ] Define your system dynamics
- [ ] Choose certificate type
- [ ] Define state space domains
- [ ] Create YAML config file
- [ ] Test with simple network first
- [ ] Gradually increase complexity
- [ ] Use Python API for advanced control

---

## File Structure Overview

```
Fossil-v2-Experiments/
├── README.md                          # Main product documentation
├── user-guide.md                      # Detailed YAML parameter guide
├── set_guide.md                       # Domain/set construction guide
├── repeatability_instructions.md      # How to reproduce paper results
├── requirements.txt                   # Python dependencies
├── setup.py                          # Installation configuration
├── Dockerfile                        # Docker image specification
│
├── fossil/                           # Main source code
│   ├── main.py                       # CLI entry point
│   ├── cegis.py                      # Core CEGIS algorithm
│   ├── certificate.py                # Certificate definitions
│   ├── verifier.py                   # SMT solver interface
│   ├── domains.py                    # Domain definitions
│   ├── plotting.py                   # Visualization
│   └── ...
│
├── experiments/                       # Benchmarks and experiments
│   ├── benchmarks/
│   │   ├── cli/                      # YAML config examples
│   │   │   ├── lyap-test.yaml
│   │   │   ├── barr-test.yaml
│   │   │   └── ctrl_rar_sine.yaml
│   │   ├── models.py                 # Benchmark system definitions
│   │   └── ...
│   └── fossil1-comparison.py          # v1 vs v2 comparison script
│
├── papers/
│   ├── v1.pdf                        # FOSSIL v1.0 paper (HSCC '21)
│   └── v2.pdf                        # FOSSIL v2.0 paper (HSCC '24)
│
├── tst/                              # Unit tests
│   ├── test_cli.py
│   ├── test_learner.py
│   └── ...
│
└── results/                          # Output directory for experiments
    ├── fossil1_comparison.csv
    ├── fossil1_comparison.tex
    ├── tool_paper_tab.csv
    ├── tool_paper_tab.tex
    └── plots/
```

---

## Summary

**FOSSIL v2.0** is a powerful tool for verifying dynamical systems using AI-powered certificate synthesis. It:

✅ Requires only **CPU** (no GPU needed)  
✅ Needs **8GB RAM** minimum (16GB recommended)  
✅ Supports **multiple OS** (Linux, macOS, Windows via Docker)  
✅ Works via **multiple interfaces** (CLI, Python API)  
✅ Includes **comprehensive documentation** and examples  
✅ Is **fully reproducible** docker-based setup  

**Next Steps**:
1. Choose installation method (Docker recommended)
2. Run quick test to verify setup
3. Try example benchmarks
4. Customize for your own system
5. Read papers for theoretical background

Good luck with FOSSIL! 🦴

