# 📚 FOSSIL Documentation Map

I've created a comprehensive guide package for you! Here's your roadmap:

---

## 🚀 START HERE (Everyone starts here)

### **[START_HERE.md](START_HERE.md)**
- Overview of what FOSSIL is
- File structure of the project  
- FAQ section
- Quick reference commands
- Links to all other guides
- **Read Time: 10 minutes**

---

## 📖 Main Learning Path (Choose Your Level)

### Level 1: Get Running Fast ⚡
**[QUICK_START.md](QUICK_START.md)**
- 60-second overview
- Installation options
- Your first run (30 seconds!)
- YAML config examples
- Running benchmarks
- Troubleshooting basics
- **Perfect for**: I just want to run it!
- **Read Time: 5 minutes**

### Level 2: Proper Installation 🛠️
**[INSTALLATION_GUIDE.md](INSTALLATION_GUIDE.md)**
- Step-by-step for 3 paths (Docker, Native, Virtual Env)
- Detailed Docker setup
- dReal prerequisites
- Verification checklist
- Installation troubleshooting
- Configuration adjustments
- **Perfect for**: I want to install it correctly
- **Read Time: 15 minutes (depending on path)**

### Level 3: Understand How It Works 🧠
**[ARCHITECTURE_EXPLAINED.md](ARCHITECTURE_EXPLAINED.md)**
- What FOSSIL solves (the why)
- CEGIS algorithm explained
- Learner vs Verifier components
- Neural network design choices
- Certificate types deep dive
- Mathematical foundations
- **Perfect for**: I want to understand the theory
- **Read Time: 30 minutes**

### Level 4: Complete Reference 📚
**[FOSSIL_COMPLETE_GUIDE.md](FOSSIL_COMPLETE_GUIDE.md)**
- Everything in one document
- Version history (v1 vs v2)
- System requirements breakdown
- All configuration parameters
- Certificate types explained
- Reproducing paper results
- Results interpretation
- **Perfect for**: I need complete documentation
- **Read Time: 1 hour (reference document)**

---

## 🔍 Specialized Guides (For Specific Tasks)

### Configuring YAML Files
**[user-guide.md](user-guide.md)** (in repo)
- All YAML parameters
- Default values  
- Parameter descriptions
- **Perfect for**: Tweaking configurations

### Domain Construction
**[set_guide.md](set_guide.md)** (in repo)
- Understanding domains and sets
- Set semantics
- Domain construction best practices
- **Perfect for**: Defining state spaces

### Reproducing the Paper
**[repeatability_instructions.md](repeatability_instructions.md)** (in repo)
- Running paper experiments
- Hardware requirements  
- Docker usage details
- Interpreting results
- **Perfect for**: Validation & reproducibility

---

## 📊 Quick Decision Trees

### "Which guide should I read?"

```
START_HERE.md
    │
    ├─ "I just want to run it NOW"
    │   └─ QUICK_START.md (5 min)
    │
    ├─ "I need to install it properly"
    │   ├─ INSTALLATION_GUIDE.md - Docker path (10 min)
    │   ├─ INSTALLATION_GUIDE.md - Native path (20 min)
    │   └─ INSTALLATION_GUIDE.md - Virtual env (15 min)
    │
    ├─ "I want to understand the algorithm"
    │   └─ ARCHITECTURE_EXPLAINED.md (30 min)
    │
    ├─ "I need complete reference"
    │   └─ FOSSIL_COMPLETE_GUIDE.md (1 hour)
    │
    ├─ "I'm configuring YAML"
    │   └─ user-guide.md + set_guide.md
    │
    └─ "I'm running experiments"
        └─ repeatability_instructions.md
```

### "What's my timeline?"

```
⚡ 5 minutes:
   └─ Install Docker + Run QUICK_START.md = Working!

🚀 15 minutes:
   └─ INSTALLATION_GUIDE.md (Docker) + first run

📚 1 hour:
   ├─ QUICK_START.md
   ├─ INSTALLATION_GUIDE.md
   └─ ARCHITECTURE_EXPLAINED.md

🔬 2-3 hours:
   └─ Read all guides + papers + run experiments
```

---

## 📋 Document Purposes Summary

| Document | Purpose | Length | Complexity |
|----------|---------|--------|-----------|
| **START_HERE.md** | Overview & navigation | 10 min | Low |
| **QUICK_START.md** | Fast setup & first run | 5 min | Low |
| **INSTALLATION_GUIDE.md** | Installation methods | 10-30 min | Low-Medium |
| **ARCHITECTURE_EXPLAINED.md** | How CEGIS works | 30 min | Medium-High |
| **FOSSIL_COMPLETE_GUIDE.md** | Full reference | 1 hour | Medium |
| **user-guide.md** | YAML parameters | 15 min | Low |
| **set_guide.md** | Domain construction | 10 min | Medium |
| **repeatability_instructions.md** | Paper experiments | 20 min | Low |

---

## 🎯 Common Use Cases

### Use Case 1: "I Want to Verify My System in 10 Minutes"

1. Read: [QUICK_START.md](QUICK_START.md) (5 min)
2. Follow: Docker installation steps (3 min)
3. Create: Your YAML config (1 min)
4. Run: `fossil my_system.yaml` (1 min)

**Total: 10 minutes** ✅

---

### Use Case 2: "I Want to Understand Everything Before Using"

1. Read: [START_HERE.md](START_HERE.md) (10 min)
2. Read: [ARCHITECTURE_EXPLAINED.md](ARCHITECTURE_EXPLAINED.md) (30 min)
3. Read: [papers/v2.pdf](papers/v2.pdf) (45 min)
4. Follow: [INSTALLATION_GUIDE.md](INSTALLATION_GUIDE.md) (15 min)
5. Run: Examples (15 min)

**Total: 2 hours** (well-informed!)

---

### Use Case 3: "I Need to Run Reproducibility Experiments"

1. Read: [repeatability_instructions.md](repeatability_instructions.md) (10 min)
2. Install: Docker if needed (10 min)
3. Run: `./run_hscc_experiments.sh` (1-2 hours)
4. Check: Results in results/ folder

**Total: 1.5-2.5 hours**

---

### Use Case 4: "I Want to Extend/Modify FOSSIL"

1. Read: [ARCHITECTURE_EXPLAINED.md](ARCHITECTURE_EXPLAINED.md) (30 min)
2. Study: [papers/v2.pdf](papers/v2.pdf) (1 hour)
3. Explore: `fossil/certificate.py` (30 min)
4. Check: `custom_cert.py` example (15 min)
5. Implement: Your custom certificate

**Total: 2+ hours**

---

## 🔧 How to Use These Guides

### Method 1: Linear Reading (Beginner)

```
START_HERE.md
    ↓
QUICK_START.md  
    ↓
INSTALLATION_GUIDE.md
    ↓
Try examples
    ↓
ARCHITECTURE_EXPLAINED.md (if interested)
    ↓
FOSSIL_COMPLETE_GUIDE.md (for reference)
```

### Method 2: Jump to Relevant Section (Experienced)

- New to FOSSIL? → QUICK_START.md
- Having issues? → INSTALLATION_GUIDE.md
- Want to understand? → ARCHITECTURE_EXPLAINED.md  
- Need details? → FOSSIL_COMPLETE_GUIDE.md
- Configuring YAML? → user-guide.md
- Running experiments? → repeatability_instructions.md

### Method 3: Reference Lookup

Use Ctrl+F (Cmd+F) to search through:
- FOSSIL_COMPLETE_GUIDE.md - Most comprehensive
- user-guide.md - YAML parameters
- INSTALLATION_GUIDE.md - Setup issues

---

## 📞 Finding Answers to Common Questions

### "How do I install FOSSIL?"
→ [INSTALLATION_GUIDE.md](INSTALLATION_GUIDE.md)

### "How do I run my first example?"
→ [QUICK_START.md](QUICK_START.md)

### "What configuration parameters are available?"
→ [user-guide.md](user-guide.md) or [FOSSIL_COMPLETE_GUIDE.md](FOSSIL_COMPLETE_GUIDE.md)

### "How does the algorithm work?"
→ [ARCHITECTURE_EXPLAINED.md](ARCHITECTURE_EXPLAINED.md)

### "Do I need a GPU?"
→ [FOSSIL_COMPLETE_GUIDE.md](FOSSIL_COMPLETE_GUIDE.md#gpu-requirements) or [QUICK_START.md](QUICK_START.md)

### "How do I reproduce the paper results?"
→ [repeatability_instructions.md](repeatability_instructions.md)

### "How do I define domains/sets for my system?"
→ [set_guide.md](set_guide.md)

### "What are the system requirements?"
→ [FOSSIL_COMPLETE_GUIDE.md](FOSSIL_COMPLETE_GUIDE.md#system-requirements) or [QUICK_START.md](QUICK_START.md#system-requirements)

### "How do I troubleshoot installation?"
→ [INSTALLATION_GUIDE.md](INSTALLATION_GUIDE.md#troubleshooting-installation)

### "Can I extend/modify FOSSIL?"
→ [ARCHITECTURE_EXPLAINED.md](ARCHITECTURE_EXPLAINED.md#extension-points-customizing-fossil)

---

## 🎓 Learning Paths by Background

### For Control Theory Students

1. Read papers: [papers/v1.pdf](papers/v1.pdf) and [papers/v2.pdf](papers/v2.pdf)
2. Understand theory: [ARCHITECTURE_EXPLAINED.md](ARCHITECTURE_EXPLAINED.md)
3. Run experiments: [QUICK_START.md](QUICK_START.md)
4. Deep dive: [FOSSIL_COMPLETE_GUIDE.md](FOSSIL_COMPLETE_GUIDE.md)

### For Software Engineers

1. Quick setup: [QUICK_START.md](QUICK_START.md)
2. Install: [INSTALLATION_GUIDE.md](INSTALLATION_GUIDE.md)
3. Try: examples in [experiments/benchmarks/cli/](experiments/benchmarks/cli/)
4. Extend: Check [fossil/](fossil/) source code

### For Machine Learning Researchers

1. Overview: [ARCHITECTURE_EXPLAINED.md](ARCHITECTURE_EXPLAINED.md)
2. Theory: [papers/v2.pdf](papers/v2.pdf)
3. Understanding: How neural networks are trained for certificates
4. Extending: Create custom certificates in [fossil/certificate.py](fossil/certificate.py)

### For DevOps/Systems People

1. Docker setup: [INSTALLATION_GUIDE.md](INSTALLATION_GUIDE.md) - Path A
2. Scaling: Running multiple instances in parallel
3. Results: Collecting and analyzing outputs
4. Automation: See [run_hscc_experiments.sh](run_hscc_experiments.sh) for example

---

## 📈 Progression Path

```
Day 1:
  ├─ Install FOSSIL (15 min)
  └─ Run quick example (5 min)
  
Day 2:
  ├─ Read QUICK_START (5 min)
  ├─ Try own system (15 min)
  └─ Adjust configurations (20 min)

Day 3:
  ├─ Read ARCHITECTURE (30 min)  
  └─ Run benchmarks (1 hour)

Day 4+:
  ├─ Read papers (2 hours)
  ├─ Understand CEGIS deeply
  └─ Consider customizations
```

---

## ✅ Checklist: Getting Started

- [ ] Read [START_HERE.md](START_HERE.md)
- [ ] Choose your level above
- [ ] Follow the appropriate guide
- [ ] Install using [INSTALLATION_GUIDE.md](INSTALLATION_GUIDE.md)
- [ ] Run first example
- [ ] Create your own system
- [ ] Explore configuration options
- [ ] Read papers if interested

---

## 🎉 You're All Set!

All the information you need is in these guides. 

**Next Step:** Open [START_HERE.md](START_HERE.md) and follow from there!

---

## 📋 File Inventory

I've created these 4 new documentation files for you:

```
✅ START_HERE.md                  - You are reading a summary of this!
✅ QUICK_START.md                 - Fast setup and usage guide
✅ INSTALLATION_GUIDE.md          - Detailed installation instructions
✅ ARCHITECTURE_EXPLAINED.md      - How the algorithm works
✅ FOSSIL_COMPLETE_GUIDE.md       - Complete reference manual
```

Plus existing guides:
```
📖 README.md                      - Original readme
📖 user-guide.md                  - YAML parameter reference
📖 set_guide.md                   - Domain construction guide
📖 repeatability_instructions.md  - Paper reproduction
📖 example.py                     - Working code example
📖 papers/v1.pdf & v2.pdf         - Academic papers
```

---

## 🚀 TL;DR - Just Get Started

1. **Read**: [QUICK_START.md](QUICK_START.md) (5 min)
2. **Install**: Docker (10 min) or Native (20 min) 
3. **Run**: `fossil lyap-test.yaml` (1 min)
4. **Done**: You have a working installation!

Then explore from there. Good luck! 🦴

---

*Documentation created May 2026 for FOSSIL v2.0*
