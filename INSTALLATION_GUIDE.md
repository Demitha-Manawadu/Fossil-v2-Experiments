# FOSSIL v2.0 - Complete Installation & Setup Guide

## Step-by-Step Setup (Choose Your Path)

---

## PATH A: Docker Setup (Windows/Mac/Linux) ⭐ RECOMMENDED

### Why Docker?
✅ Works on Windows, Mac, Linux  
✅ No complex dependency issues  
✅ Guaranteed to work  
✅ Easy to remove (just uninstall Docker)  
✅ Can run multiple isolated instances  

### Prerequisites
- 5GB free disk space
- Internet connection (for pulling image)
- Admin access to install Docker

### Installation Steps

#### Step 1: Install Docker

**Windows**:
1. Download [Docker Desktop for Windows](https://www.docker.com/products/docker-desktop)
2. Run installer, restart computer
3. Open PowerShell, verify: `docker --version`

**Mac**:
1. Download [Docker Desktop for Mac](https://www.docker.com/products/docker-desktop)
2. Install and startup Docker
3. Open Terminal, verify: `docker --version`

**Linux (Ubuntu)**:
```bash
sudo apt-get update
sudo apt-get install -y docker.io
sudo usermod -aG docker $USER
# Log out and back in
docker --version
```

#### Step 2: Pull FOSSIL Image

```bash
# Takes ~5-10 minutes (downloads ~2.2GB)
docker pull aleccedwards/fossil:latest
```

Verify:
```bash
docker run --rm aleccedwards/fossil fossil -h
```

Expected output: Help message for `fossil` command

#### Step 3: Create Results Directory

**Windows PowerShell**:
```powershell
mkdir fossil_results
cd fossil_results
```

**Mac/Linux**:
```bash
mkdir fossil_results
cd fossil_results
```

This directory will store all outputs.

#### Step 4: Test Installation

**Windows PowerShell**:
```powershell
docker run -it --rm -v ${pwd}/results:/fossil/results `
  aleccedwards/fossil fossil lyap-test.yaml
```

**Mac/Linux**:
```bash
docker run -it --rm -v $(pwd)/results:/fossil/results \
  aleccedwards/fossil fossil lyap-test.yaml
```

Expected output:
```
SUCCESS: Lyapunov function synthesized!
Synthesis time: 1.234 seconds
Iterations: 3
```

✅ **Installation Complete!** Skip to "Your First Run"

---

## PATH B: Native Linux Installation (Ubuntu 22.04)

### Why Native?
✅ Direct access to system  
✅ No Docker overhead  
❌ Complex dependency installation  
❌ Only works on Linux  

### Prerequisites
- Ubuntu 22.04 LTS (other Ubuntu versions may have issues)
- 8GB RAM minimum
- 5GB free disk
- Internet connection

### Installation Steps

#### Step 1: Install dReal Prerequisites

```bash
# Copy-paste this entire block:
curl -fsSL https://raw.githubusercontent.com/dreal/dreal4/master/setup/ubuntu/22.04/install_prereqs.sh | sudo bash
```

This installs:
- Bazel (build system)
- C++ compiler
- Required libraries

**Time**: ~10-15 minutes  
**Note**: Script may take a while - be patient!

#### Step 2: Install dReal

```bash
pip3 install dreal
```

**Verify**:
```bash
python3 -c "import dreal; print(dreal.__version__)"
```

You should see a version number.

#### Step 3: Clone FOSSIL Repository

```bash
# Navigate to workspace
cd /path/to/Fossil-v2-Experiments

# Or clone if not already present
git clone https://github.com/oxford-oxcav/fossil.git
cd fossil
```

#### Step 4: Install FOSSIL

```bash
# Install in standard mode
pip3 install .

# Or development mode (if you plan to modify code)
pip3 install -e .
```

**Verify**:
```bash
fossil -h
```

You should see help message.

#### Step 5: Install Additional Dependencies

Some experiments need extra packages:

```bash
pip3 install jupyter pandas tabulate scipy aenum tqdm
```

#### Step 6: Test Installation

```bash
# Create test directory
mkdir fossil_test
cd fossil_test

# Copy test config
cp ../lyap-test.yaml .

# Run test
fossil lyap-test.yaml
```

✅ **Native installation complete!**

---

## PATH C: Manual Setup with Virtual Environment (Advanced)

### For Users Who Want Isolation

#### Step 1: Create Virtual Environment

**Linux/Mac**:
```bash
python3 -m venv fossil_env
source fossil_env/bin/activate
```

**Windows PowerShell**:
```powershell
python -m venv fossil_env
.\fossil_env\Scripts\Activate.ps1
```

#### Step 2: Upgrade pip

```bash
pip install --upgrade pip setuptools wheel
```

#### Step 3: Install Requirements

```bash
pip install -r requirements.txt
```

This installs:
- PyTorch (CPU version)
- NumPy, SciPy
- SymPy
- Z3
- Matplotlib
- And others

#### Step 4: Install FOSSIL

```bash
cd /path/to/Fossil-v2-Experiments
pip install -e .
```

#### Step 5: Test

```bash
fossil lyap-test.yaml
```

---

## Troubleshooting Installation

### Problem 1: Docker Command Not Found

**Windows**:
- Restart computer after Docker install
- Ensure Docker Desktop is running (check system tray)
- Use PowerShell (not Command Prompt)

**Mac**:
- Check Applications folder for Docker
- May need to grant permissions

**Linux**:
- Check: `sudo usermod -aG docker $USER`
- May need to restart or `newgrp docker`

**Solution**:
```bash
docker --version  # Should work now
```

### Problem 2: dReal Installation Fails

**Symptoms**: `pip install dreal` fails with build errors

**Causes**:
- Not Ubuntu 22.04 (try different version)
- Missing build tools
- Outdated pip

**Solutions**:
1. Update pip: `pip3 install --upgrade pip`
2. Use Docker instead (easiest)
3. Check dReal GitHub issues

### Problem 3: "ARM Processor Not Supported"

**Symptoms**: Installation fails on Apple M1/M2/M3 Mac

**Why**: dReal doesn't support ARM architecture

**Solutions**:
- Use Docker (will auto-emulate)
- Use Parallels VM with Ubuntu
- Cannot run natively on M-series Macs

### Problem 4: Permission Denied (Linux)

**Symptom**: `sudo: command not found` or permission errors

**Solution**:
```bash
# You might not have sudo on WSL2
apt-get install sudo
# Or run commands without sudo if logged as root
```

### Problem 5: Out of Disk Space

**Symptom**: "No space left on device"

**Solutions**:
- Need at least 5GB total free
- Docker image: ~2.2GB
- Temporary files: ~1-2GB
- Results: ~1GB

Check:
```bash
df -h  # Linux/Mac
Get-Volume  # Windows PowerShell
```

---

## Verification Checklist

### After Installation, Verify Everything Works:

- [ ] Docker running (or native Python installed)
- [ ] Can run: `fossil -h` (shows help)
- [ ] Can run: `fossil lyap-test.yaml` (quick test, <30 sec)
- [ ] Results directory created
- [ ] Can see output files

### If All Pass: ✅ Ready to Use!

---

## Your First Run

### Option 1: Docker (Recommended)

**Windows PowerShell**:
```powershell
cd fossil_results
docker run -it --rm -v ${pwd}/results:/fossil/results `
  aleccedwards/fossil fossil lyap-test.yaml
```

**Mac/Linux Bash**:
```bash
cd fossil_results
docker run -it --rm -v $(pwd)/results:/fossil/results \
  aleccedwards/fossil fossil lyap-test.yaml
```

### Option 2: Native Installation

```bash
fossil lyap-test.yaml
```

### Expected Output:

```
FOSSIL v2.0
============
Configuration: lyap-test.yaml

System: x1, -x1 - x0
Certificate: Lyapunov
Domains: Sphere([0,0], 1.0)

Starting CEGIS synthesis...

Iteration 1: Learning... ✓ Verifying... ✓
Iteration 2: Learning... ✓ Verifying... ✓  
Iteration 3: Learning... ✓ Verifying... ✓

SUCCESS!
========
Certificate type: Lyapunov
Synthesis time: 1.234 seconds
CEGIS iterations: 3
```

---

## Running Your First Custom Example

### Step 1: Create Config File

Create `my_first_system.yaml`:

```yaml
# Van der Pol oscillator - stability analysis
SYSTEM: [x1, -x0 + 0.1*(1 - x0**2)*x1]

CERTIFICATE: Lyapunov
TIME_DOMAIN: CONTINUOUS
VERIFIER: Z3

DOMAINS:
  XD: Sphere([0,0], 2.0)

N_DATA:
  XD: 1000

N_VARS: 2
N_HIDDEN_NEURONS: [10]
ACTIVATION: [SQUARE]

SEED: 0
```

### Step 2: Run It

**Docker**:
```bash
docker run -it --rm -v $(pwd)/results:/fossil/results \
  aleccedwards/fossil fossil my_first_system.yaml
```

**Native**:
```bash
fossil my_first_system.yaml
```

### Step 3: Interpret Results

- **SUCCESS**: Found valid certificate! ✅
- **FAILURE**: Couldn't find certificate (try: larger network, more data)

---

## Next: Reproducing Paper Results

Once installation complete, two options:

### Quick Test (10 minutes):
```bash
# Table 2: v1 vs v2 comparison
docker run -it --rm -v $(pwd)/results:/fossil/results \
  aleccedwards/fossil python3 -m experiments.fossil1-comparison
```

### Full Experiments (1-2 hours):
```bash
# Table 3: Complete benchmark suite
docker run -it --rm -v $(pwd)/results:/fossil/results \
  aleccedwards/fossil ./run_hscc_experiments.sh
```

### Visualization (1 minute):
```bash
# Figure 4: Certificate visualization
docker run -it --rm -v $(pwd)/results:/fossil/results \
  aleccedwards/fossil fossil experiments/benchmarks/cli/ctrl_rar_sine.yaml --plot
```

---

## System Specifications Reference

### Minimum Requirements
```
CPU:     2+ cores
RAM:     8GB
Disk:    5GB free
OS:      Windows/Mac/Linux
Network: Broadband (for pulling Docker)
```

### Recommended Requirements
```
CPU:     4+ cores (6+ Intel better)
RAM:     16GB
Disk:    10GB free
OS:      Ubuntu 22.04 (native) or any (Docker)
Network: Fast internet
```

### What You DON'T Need
```
❌ GPU (tool is CPU-only)
❌ CUDA (no GPU acceleration)
❌ Tensor cores
❌ Special hardware
✅ Just a regular computer!
```

---

## Common Configuration Adjustments

### Problem: "Synthesis taking too long"

**Try shorter timeout**:
```yaml
CEGIS_MAX_TIME_S: 300  # 5 minute limit
```

**Try simpler network**:
```yaml
N_HIDDEN_NEURONS: [5]        # Smaller
ACTIVATION: [SQUARE]          # Simpler activation
```

**Try faster verifier**:
```yaml
VERIFIER: Z3  # Faster than DREAL
```

### Problem: "Out of memory"

**Reduce data points**:
```yaml
N_DATA:
  XD: 500  # From 1000
```

**Reduce network size**:
```yaml
N_HIDDEN_NEURONS: [5]  # Smaller network
```

### Problem: "Verification always fails"

**Try stronger verifier**:
```yaml
VERIFIER: DREAL  # More powerful than Z3
```

**Try larger network**:
```yaml
N_HIDDEN_NEURONS: [15, 10]  # Deeper/wider
```

**Try more data**:
```yaml
N_DATA:
  XD: 2000  # More samples
```

---

## File Organization Best Practice

```
fossil_workspace/
├── experiments/              # Your configs
│   ├── my_system1.yaml
│   ├── my_system2.yaml
│   └── results/             # Store output here
│
└── fossil_results/          # Docker mounted dir
    ├── csv files
    ├── plots
    └── logs
```

**Usage**:
```bash
cd fossil_workspace
docker run -it --rm -v $(pwd)/fossil_results:/fossil/results \
  aleccedwards/fossil fossil experiments/my_system1.yaml
```

---

## Getting Help

### Documentation Files
- **Quick start**: `QUICK_START.md`
- **Complete guide**: `FOSSIL_COMPLETE_GUIDE.md`
- **Architecture**: `ARCHITECTURE_EXPLAINED.md`
- **YAML reference**: `user-guide.md`
- **Domain guide**: `set_guide.md`
- **Reproducibility**: `repeatability_instructions.md`

### Running Help Command
```bash
fossil -h      # Command-line help
fossil --help  # Same thing
```

### Example Files
```
experiments/benchmarks/cli/
├── lyap-test.yaml      # Simple Lyapunov example
├── barr-test.yaml      # Barrier example
└── ctrl_rar_sine.yaml  # Complex RAR with control
```

### Papers
```
papers/
├── v1.pdf  # Original 2021 HSCC paper
└── v2.pdf  # Latest 2024 improvements
```

---

## Summary Table: Installation Methods

| Method | Time | Difficulty | OS | Pros | Cons |
|--------|------|-----------|----|----|-----|
| **Docker** | 10 min | Easy | All | Works everywhere, easy cleanup | One abstraction layer |
| **Native Linux** | 20-30 min | Hard | Ubuntu 22.04 only | Direct control, no overhead | Complex setup, Linux-only |
| **Virtual Env** | 15 min | Medium | All | Isolated Python env | Need to handle dependencies |

---

## Ready to Go! 🚀

Pick your path above and follow the steps. You should be running FOSSIL within 30 minutes!

Once installed, see `QUICK_START.md` for next steps.

Happy synthesizing! 🔬
