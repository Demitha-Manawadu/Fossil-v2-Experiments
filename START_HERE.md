# FOSSIL v2.0 - README FOR USERS (Start Here!)

## Welcome to FOSSIL! 🦴

This repository contains **FOSSIL v2.0** - an automated tool for synthesizing certificates (Lyapunov functions, barrier certificates) to verify properties of dynamical systems.

---

## Quick Links

**New to FOSSIL?** Start here:
1. 📖 [QUICK_START.md](QUICK_START.md) - 5-minute overview
2. 🛠️ [INSTALLATION_GUIDE.md](INSTALLATION_GUIDE.md) - Setup instructions
3. 🎯 [Your First Run](#your-first-run-30-seconds)

**Want to understand how it works?**
4. 🧠 [ARCHITECTURE_EXPLAINED.md](ARCHITECTURE_EXPLAINED.md) - Deep dive into algorithms

**Complete documentation:**
5. 📚 [FOSSIL_COMPLETE_GUIDE.md](FOSSIL_COMPLETE_GUIDE.md) - Full reference

**Reproduce paper results:**
6. 🔬 [repeatability_instructions.md](repeatability_instructions.md) - Running experiments

---

## What is FOSSIL?

FOSSIL is a tool that **automatically proves your dynamical systems are stable or safe** by:

1. **Generating** candidate functions (neural networks)
2. **Verifying** them mathematically (SMT solvers)
3. **Iterating** until finding a valid proof

### The Problem It Solves

Traditional way to verify a system:
- 📚 Read papers on control theory
- 🤔 Manually design Lyapunov functions
- ✍️ Prove properties by hand
- ⏱️ Takes weeks or months

FOSSIL way:
- 📝 Write system dynamics in YAML
- ⚡ Run `fossil config.yaml`
- ✅ Get verified results in minutes!

### Real Applications

- 🚗 Autonomous vehicle safety
- 🛩️ Aircraft control systems  
- 🤖 Robotics stability
- 🔒 Safety-critical systems
- 🌐 Network control

---

## Key Facts

### Requirements

| What | Requirement |
|-----|------------|
| **CPU** | Intel/AMD (NOT ARM like M1 Mac) |
| **RAM** | 8GB minimum, 16GB recommended |
| **GPU** | ❌ Not needed (tool is CPU-only) |
| **Disk** | 5GB free space |
| **OS** | Windows/Mac/Linux (via Docker recommended) |
| **Python** | 3.9+ |

### What You Get

✅ Automated certificate synthesis  
✅ Formal mathematical proofs  
✅ Support for 10+ certificate types  
✅ Works on continuous and discrete-time systems  
✅ Can synthesize controllers too  
✅ Open-source and extensible  

### Version Information

- **Latest**: FOSSIL v2.0 (2024)
- **Previous**: FOSSIL v1.0 (2021)
- **Improvements in v2**: More certificates, discrete-time, control synthesis, better verification

---

## Your First Run (30 seconds)

### Using Docker (Easiest)

**Windows PowerShell**:
```powershell
cd fossil_results
docker run -it --rm -v ${pwd}/results:/fossil/results `
  aleccedwards/fossil fossil lyap-test.yaml
```

**Mac/Linux**:
```bash
cd fossil_results
docker run -it --rm -v $(pwd)/results:/fossil/results \
  aleccedwards/fossil fossil lyap-test.yaml
```

### Expected Output
```
SUCCESS: Lyapunov function synthesized!
Synthesis time: 1.234 seconds
Iterations: 3
```

✅ **Success!** Certificate proven!

---

## The Two Ways to Use FOSSIL

### #1: Command-Line Interface (Easiest)

Write a YAML config file describing your system, then run:

```bash
fossil my_system.yaml
```

**Example config**:
```yaml
SYSTEM: [x1, -x1 - x0]
CERTIFICATE: Lyapunov
DOMAINS:
  XD: Sphere([0,0], 1.0)
TIME_DOMAIN: CONTINUOUS
N_VARS: 2
N_HIDDEN_NEURONS: [5]
ACTIVATION: [SQUARE]
VERIFIER: Z3
```

👉 See [experiments/benchmarks/cli/](experiments/benchmarks/cli/) for more examples

### #2: Python API (Most Flexible)

Full control via Python code:

```python
import fossil

# Define system
class MySystem(fossil.control.DynamicalModel):
    n_vars = 2
    def f_torch(self, v):
        x, y = v[:, 0], v[:, 1]
        return [y, -x - y]
    def f_smt(self, v):
        x, y = v
        return [y, -x - y]

# Configure
opts = fossil.CegisConfig(
    N_VARS=2,
    SYSTEM=MySystem(),
    CERTIFICATE=fossil.CertificateType.LYAPUNOV,
    # ... more options ...
)

# Synthesize
result = fossil.synthesise(opts)
```

👉 See [example.py](example.py) for full example

---

## Document Roadmap

### Start Here (In Order)

1. **[QUICK_START.md](QUICK_START.md)** 
   - 5-minute overview
   - Basic usage examples
   - Minimal configuration
   - What you need to know immediately

2. **[INSTALLATION_GUIDE.md](INSTALLATION_GUIDE.md)**
   - Step-by-step installation
   - Docker vs native
   - Troubleshooting
   - Verification checklist

### Deep Dives

3. **[ARCHITECTURE_EXPLAINED.md](ARCHITECTURE_EXPLAINED.md)**
   - How CEGIS works
   - Learner vs Verifier
   - Neural network design  
   - Why it matters

4. **[FOSSIL_COMPLETE_GUIDE.md](FOSSIL_COMPLETE_GUIDE.md)**
   - YAML configuration reference
   - All parameters explained
   - Results interpretation
   - Advanced techniques

### Reference

5. **[user-guide.md](user-guide.md)**
   - Detailed YAML parameters
   - Default values
   - Configuration options

6. **[set_guide.md](set_guide.md)**
   - Domain construction guide
   - Set semantics
   - Best practices

7. **[repeatability_instructions.md](repeatability_instructions.md)**
   - Reproducing paper results
   - Running experiments
   - Benchmarks and tables

---

## FAQ

### Q: Do I need a GPU?
**A:** No! FOSSIL is CPU-only. GPU acceleration not available.

### Q: What system can I run this on?
**A:** Windows (Docker), Mac (Docker, but not M1/M2), Unix/Linux (Docker or native)

### Q: How long does it take?
**A:** Typically 30 seconds to 10 minutes per system. Complex problems may take longer.

### Q: Can I modify the code?
**A:** Yes! FOSSIL is written in clean Python and designed to be extended. See `fossil/` directory for source.

### Q: Do I need to know control theory?
**A:** Not really! FOSSIL abstracts away the math. Basic understanding helps but not required.

### Q: Is there a GUI?
**A:** No. YAML config files + command-line. Simple and scriptable!

### Q: Can I run multiple problems in parallel?
**A:** Yes! Each run is independent. Run multiple Docker containers or in different terminals.

### Q: Why does it sometimes fail?
**A:** Synthesis is hard! If it fails:
- Network too small → increase `N_HIDDEN_NEURONS`
- Insufficient data → increase `N_DATA`
- Problem hard → try `VERIFIER: DREAL` (stronger)
- Timeout → adjust `CEGIS_MAX_TIME_S`

---

## File Structure

```
Fossil-v2-Experiments/                 # You are here
│
├── 📖 Documentation (Read These!)
│   ├── README.md                      # Original README
│   ├── QUICK_START.md                 # ⭐ Start here!
│   ├── INSTALLATION_GUIDE.md          # How to install
│   ├── ARCHITECTURE_EXPLAINED.md      # How it works
│   ├── FOSSIL_COMPLETE_GUIDE.md       # Full reference
│   ├── user-guide.md                  # YAML parameters
│   ├── set_guide.md                   # Domain construction
│   └── repeatability_instructions.md  # Running experiments
│
├── 🔬 Source Code
│   └── fossil/                        # Main library
│       ├── main.py                    # CLI entry point
│       ├── cegis.py                   # CEGIS algorithm
│       ├── certificate.py             # Certificate types
│       ├── verifier.py                # SMT solvers
│       ├── learner.py                 # Neural networks
│       ├── domains.py                 # Geometric domains
│       └── ...
│
├── 🧪 Examples & Benchmarks
│   ├── example.py                     # Python API example
│   ├── experiments/                   # Experiment scripts
│   │   ├── benchmarks/cli/            # YAML configs
│   │   │   ├── lyap-test.yaml         # Simple Lyapunov
│   │   │   ├── barr-test.yaml         # Barrier example
│   │   │   └── ctrl_rar_sine.yaml     # Complex RAR
│   │   └── fossil1-comparison.py      # v1 vs v2
│   └── tst/                           # Unit tests
│
├── 📄 Configuration
│   ├── setup.py                       # Installation config
│   ├── requirements.txt               # Python packages
│   ├── Dockerfile                     # Docker image
│   └── config.yaml                    # (empty template)
│
├── 📚 Papers
│   └── papers/
│       ├── v1.pdf                     # FOSSIL v1.0 (HSCC '21)
│       └── v2.pdf                     # FOSSIL v2.0 (HSCC '24)
│
└── 📊 Results (Gets Created)
    └── results/                       # Experiment outputs
        ├── csv files
        ├── LaTeX tables
        └── plots/
```

---

## Installation Overview

### Fastest (Docker) - 10 minutes:
```bash
docker pull aleccedwards/fossil:latest
docker run -it aleccedwards/fossil fossil -h
```

### Native (Ubuntu) - 20 minutes:
```bash
curl -fsSL https://raw.githubusercontent.com/dreal/dreal4/master/setup/ubuntu/22.04/install_prereqs.sh | sudo bash
cd Fossil-v2-Experiments
pip3 install .
fossil -h
```

👉 Full instructions in [INSTALLATION_GUIDE.md](INSTALLATION_GUIDE.md)

---

## Getting Results

### Option 1: Quick Test (30 seconds)
```bash
fossil lyap-test.yaml
```

### Option 2: Custom System (5 minutes)
Create your config file and run:
```bash
fossil my_system.yaml
```

### Option 3: Reproduce Paper (10 minutes to 2 hours)
```bash
python3 -m experiments.fossil1-comparison    # 10 min
./run_hscc_experiments.sh                    # 1-2 hours
fossil experiments/benchmarks/cli/ctrl_rar_sine.yaml --plot  # 1 min
```

👉 See [FOSSIL_COMPLETE_GUIDE.md#Reproducing Paper Results](FOSSIL_COMPLETE_GUIDE.md) for details

---

## Next Steps

### 👶 Complete Beginner?
1. Read [QUICK_START.md](QUICK_START.md) (5 min)
2. Follow [INSTALLATION_GUIDE.md](INSTALLATION_GUIDE.md) (15 min)
3. Run first example (5 min)
4. Try your own system!

### 🧑‍💻 Programmer?
1. Skim [INSTALLATION_GUIDE.md](INSTALLATION_GUIDE.md)
2. Read [QUICK_START.md](QUICK_START.md) 
3. Check out [example.py](example.py)
4. Modify for your system

### 🔬 Researcher?
1. Read [ARCHITECTURE_EXPLAINED.md](ARCHITECTURE_EXPLAINED.md)
2. Read papers: [papers/v2.pdf](papers/v2.pdf)
3. Review [fossil/certificate.py](fossil/certificate.py)
4. Extend with custom certificates!

### 📊 Running Experiments?
1. Follow [repeatability_instructions.md](repeatability_instructions.md)
2. Run benchmark suite
3. Analyze results in CSV/LaTeX format

---

## Support & Resources

### Documentation
- 📖 This file (you are here!)
- 📚 [FOSSIL_COMPLETE_GUIDE.md](FOSSIL_COMPLETE_GUIDE.md) - Everything explained
- 🚀 [QUICK_START.md](QUICK_START.md) - Fast setup
- 🛠️ [INSTALLATION_GUIDE.md](INSTALLATION_GUIDE.md) - Detailed install

### Learning Resources
- 📕 [papers/v1.pdf](papers/v1.pdf) - Original publication (HSCC 2021)
- 📗 [papers/v2.pdf](papers/v2.pdf) - Latest (HSCC 2024)
- 💻 [example.py](example.py) - Working code example
- 📁 [experiments/benchmarks/cli/](experiments/benchmarks/cli/) - Config examples

### References  
- Theory: Read papers first
- Usage: Check YAML examples
- Troubleshooting: See [INSTALLATION_GUIDE.md](INSTALLATION_GUIDE.md#Troubleshooting)
- Advanced: Check [ARCHITECTURE_EXPLAINED.md](ARCHITECTURE_EXPLAINED.md)

---

## Summary

FOSSIL v2.0 is a **powerful, automated tool** for verifying dynamical systems:

✅ **Easy to use**: Simple YAML configuration  
✅ **Fast**: Results in minutes  
✅ **Guaranteed**: Formal mathematical proofs  
✅ **Flexible**: Multiple certificate types  
✅ **Extensible**: Modify and extend source code  
✅ **Well-documented**: Guides and examples included  

**Time to first result: 15 minutes** (installation + run)

---

## Citation

If you use FOSSIL, please cite:

**FOSSIL v2.0**:
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

**FOSSIL v1.0**:
```bibtex
@inproceedings{Abate_Ahmed_Edwards_Giacobbe_Peruffo_2021,
  title={FOSSIL: A Software Tool for the Formal Synthesis of Lyapunov Functions and Barrier Certificates using Neural Networks},
  booktitle={Proceedings of the 24th International Conference on Hybrid Systems: Computation and Control},
  publisher={ACM},
  author={Abate, Alessandro and Ahmed, Daniele and Edwards, Alec and Giacobbe, Mirco and Peruffo, Andrea},
  year={2021}
}
```

---

## License

BSD-3-Clause (see [LICENSE](LICENSE))

---

## Authors

**FOSSIL v2.0**:
- Alec Edwards (University of Oxford)
- Andrea Peruffo  
- Alessandro Abate

**FOSSIL v1.0**:
- Alessandro Abate
- Daniele Ahmed
- Alec Edwards
- Mirco Giacobbe
- Andrea Peruffo

---

## Ready to Get Started?

👉 **Next Step**: Go to [QUICK_START.md](QUICK_START.md)

*Happy synthesizing!* 🚀

---

## Quick Reference Commands

```bash
# Installation (Docker)
docker pull aleccedwards/fossil:latest

# Run YAML config
fossil my_system.yaml

# Run with visualization
fossil my_system.yaml --plot

# Python API
python3 my_script.py

# Quick experiment
python3 -m experiments.fossil1-comparison

# Full benchmark
./run_hscc_experiments.sh

# Help
fossil -h

# Enter interactive shell
docker run -it aleccedwards/fossil bash
```

---

*Created 2024 - FOSSIL v2.0 Documentation*
