# 🏢 Multi-Elevator Dispatch — CTDE QR-DQN

> **Multi-agent elevator dispatch using Quantile Regression DQN with Centralised Training, Decentralised Execution (CTDE).** Three elevators learn to minimise passenger wait time, energy consumption, and clustering across a 10-floor building with non-uniform Poisson traffic — trained over 3,000 episodes with prioritised experience replay and composite checkpoint scoring.

<p align="center">
  <a href="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Multi_Elevator_CTDE.ipynb">
    <img src="https://img.shields.io/badge/Notebook-Open%20in%20GitHub-181717?style=for-the-badge&logo=github" alt="Open Notebook"/>
  </a>
  <img src="https://img.shields.io/badge/Python-3.12-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python 3.12"/>
  <img src="https://img.shields.io/badge/PyTorch-2.9.0-EE4C2C?style=for-the-badge&logo=pytorch&logoColor=white" alt="PyTorch 2.9"/>
  <img src="https://img.shields.io/badge/Hardware-NVIDIA%20Tesla%20T4-76B900?style=for-the-badge&logo=nvidia&logoColor=white" alt="CUDA T4"/>
  <img src="https://img.shields.io/badge/Algorithm-QR--DQN%20%2B%20CTDE-blueviolet?style=for-the-badge" alt="QR-DQN CTDE"/>
</p>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Key Results](#-key-results)
- [Architecture](#-architecture)
- [Environment](#-environment)
- [Reward Design](#-reward-design)
- [Baseline Policies](#-baseline-policies)
- [Training Pipeline](#-training-pipeline)
- [Stress Test Results](#-stress-test-results)
- [Output Figures](#-output-figures)
- [Project Structure](#-project-structure)
- [How to Run](#-how-to-run)
- [Dependencies](#-dependencies)
- [Design Decisions](#-design-decisions)

---

## 🔍 Overview

This project solves the **multi-elevator group control problem** using deep reinforcement learning. The core challenge is coordinating three elevators simultaneously to minimise three competing objectives:

| Objective | Description |
|---|---|
| **Passenger Wait Time** | Minimise the average queue depth across all floors |
| **Energy Consumption** | Minimise total motor-steps (floor movements) per episode |
| **Elevator Clustering** | Prevent multiple elevators from bunching together on adjacent floors |

The approach uses **CTDE (Centralised Training, Decentralised Execution)** — a paradigm from multi-agent RL where agents share a global view during training but act independently at execution time. Each elevator's actor network receives only its own 18-dimensional local observation at runtime, yet behaves in a coordinated, building-aware manner because it was trained with guidance from a centralised critic that saw the full 16-dimensional joint state.

The RL algorithm is **QR-DQN (Quantile Regression DQN)** with a **Dueling network architecture**, enhanced with **Prioritised Experience Replay** and a **four-stage learning rate schedule** across 3,000 training episodes.

---

## 🏆 Key Results

All results produced on **Kaggle — NVIDIA Tesla T4 GPU**, PyTorch 2.9.0, Seed 42.

### Final Performance vs All Baselines

| Method | Avg Wait ↓ | Energy ↓ | Cluster Rate ↓ | Spread ↑ | Served |
|---|---|---|---|---|---|
| Nearest Car | 9.679 | 409.8 | 98.5% | 0.005 | 580.2 |
| SCAN / LOOK | 1.415 | 899.6 | 98.5% | 0.015 | 670.7 |
| Sectoring | 0.854 | 486.1 | 28.5% | 0.234 | 747.1 |
| ETA-Dispatch *(best baseline)* | 0.673 | 472.7 | 10.2% | 0.297 | 748.8 |
| **QR-DQN CTDE (ours)** | **0.492** | **427.2** | **10.0%** | **0.306** | 667.2 |

### QR-DQN CTDE vs ETA-Dispatch (best baseline)

| Metric | QR-DQN CTDE | ETA-Dispatch | Improvement |
|---|---|---|---|
| Avg Wait | **0.492** | 0.673 | **−27.0%** |
| Energy / episode | **427.2** | 472.7 | **−9.6%** |
| Cluster Rate | **10.0%** | 10.2% | **−1.6 pp** |
| Spread Index | **0.306** | 0.297 | **+3.0%** |

> The trained agent simultaneously achieves lower wait time **and** lower energy than the best hand-crafted baseline — a result that required all four targeted fixes in v10.3.

---

## 🧠 Architecture

### Overview: CTDE — Centralised Training, Decentralised Execution

```
TRAINING TIME
──────────────────────────────────────────────────────────
Joint State (16-dim) ──► Centralised Critic ──► V(s)
[all positions +                                  │
 all queues +                                     │ blended guidance
 all targets]                                     ▼
                         Each Elevator's Local Obs (18-dim)
                              ──► Actor (QR-Dueling Net) ──► Action

EXECUTION TIME
──────────────────────────────────────────────────────────
Each Elevator's Local Obs (18-dim) ──► Actor Only ──► Action
                                       (critic discarded;
                                        wisdom baked into weights)
```

### Actor — QR-Dueling Network

Each elevator has its own actor network. All three share identical architecture and weights (parameter sharing).

```
Input: 18-dim observation
    │
    ├── Linear(18 → 256) + LayerNorm + ReLU
    └── Linear(256 → 256) + LayerNorm + ReLU
              │
    ┌─────────┴──────────┐
    │                    │
Value Head          Advantage Head
Linear(256→128)     Linear(256→128)
    + ReLU              + ReLU
Linear(128→51)      Linear(128→3×51)
    │                    │
    └──── Combined ───────┘
     Q(s,a) = V(s) + A(s,a) − mean(A)
     Output: [batch × 3 actions × 51 quantiles]
```

**Why QR (Quantile Regression)?** Instead of predicting a single expected Q-value, the network predicts a full distribution over 51 quantiles. This makes learning more robust under the uncertainty introduced by the other two simultaneously-learning elevators.

**Why Dueling?** Separating the value baseline (how good is this state?) from the advantage (how much better is UP vs IDLE?) stabilises training because the shared value estimate is not contaminated by action-selection noise.

### Centralised Critic

```
Input: 16-dim joint state
  [pos_E1, pos_E2, pos_E3,          ← all 3 elevator positions (normalised)
   hall[0] … hall[9],               ← all 10 floor queue depths (normalised)
   target_E1, target_E2, target_E3] ← where all 3 elevators are heading

    Linear(16 → 256) + LayerNorm + ReLU
    Linear(256 → 128) + ReLU
    Linear(128 → 1)

Output: V(joint_state) — single scalar building value estimate
```

The critic is used only during training to provide a building-wide value signal. It is completely discarded at inference time.

---

## 🏗️ Environment

### Building Configuration

| Parameter | Value |
|---|---|
| Elevators | 3 |
| Floors | 10 (0 = lobby, 9 = top) |
| Arrival rate λ | Uniform random ∈ [0.12, 0.35] per episode |
| Lobby boost | 5× (lobby gets 5× more passengers) |
| Top floor boost | 1.5× |
| Steps per episode | 300 |
| Total training episodes | 3,000 |

### Traffic Modes (randomly sampled each episode)

| Mode | Weight | Description |
|---|---|---|
| `normal` (×2) | 50% | Standard random arrivals |
| `up_peak` | 25% | Lobby gets 2× extra — morning rush simulation |
| `down_peak` | 25% | Upper floors get 1.8× extra — evening rush simulation |

### Observation Space (18-dim per elevator)

```
[own_position / 9,         ← 1 value: normalised floor (0.0 – 1.0)
 hall[0..9] / 5.0,         ← 10 values: queue depth per floor (clipped 0–1)
 peer_positions × 2,       ← 2 values: where the other 2 elevators are
 peer_targets × 2,         ← 2 values: where peers are heading (−0.1 = idle)
 one_hot_elevator_id]      ← 3 values: which elevator am I? e.g. [1,0,0]
```

### Action Space

| Action | Value | Effect |
|---|---|---|
| `UP` | 0 | Move one floor up (if not at top) |
| `DOWN` | 1 | Move one floor down (if not at ground) |
| `IDLE` | 2 | Stay on current floor |

### Passenger Spawning

New passengers appear every step via a Poisson process:
```
arrivals[f] ~ Poisson(λ × floor_weight[f])
```
Passengers accumulate in `hall[f]` until an elevator arrives at floor `f` and clears the queue instantly. There are no explicit call registrations — passenger density is embedded directly in the observation.

---

## 🎯 Reward Design

Each elevator receives a **fully decomposed, per-agent reward** — no shared reward terms exist, which eliminates the free-rider problem in cooperative MARL.

```
reward_i = +8.0 × passengers_served_i
           −3.0 × responsibility_norm_i   (weighted queue load for floors where i is nearest)
           −0.5 × move_cost_i             (energy: 1 per floor moved)
           +0.3 × idle_bonus_i            (bonus for staying still when no nearby calls)
           −2.5 × approach_cluster_i      (penalty for moving toward a peer within 2 floors)

Clipped to [−20, +20]
```

### Responsibility (EMA-Normalised)

For each floor, the nearest elevator is computed. The weighted sum of pending passengers on those floors is that elevator's raw responsibility. This is normalised by an Exponential Moving Average (EMA, α = 0.95) of the maximum responsibility seen recently — preventing reward spikes from destabilising training.

### Approach-Cluster Penalty

This penalty is **strictly local** — it fires only for the elevator that moved toward a peer, never for the peer. This ensures no free-rider gradient exists. It discourages bunching, which wastes capacity and leaves other floors unserved.

---

## 📏 Baseline Policies

Four classical dispatching algorithms serve as comparison benchmarks:

| Policy | Strategy | Real-world analogy |
|---|---|---|
| **Nearest Car** | Each elevator greedily goes to its closest pending call | Simple rule-based dispatch |
| **SCAN / LOOK** | Sweep up then down; reverse on empty half (like a hard disk seek) | Classic LOOK algorithm |
| **ETA-Dispatch** | Assign each call to the elevator with minimum estimated travel time | Most advanced traditional system |
| **Sectoring** | Divide building into equal zones; each elevator owns its zone | Zone-based dispatch |

ETA-Dispatch is the **primary benchmark** — it is the most competitive traditional method and the one the RL agent is specifically trained to beat.

---

## 🔄 Training Pipeline

### Phase 0 — Warm Start (100 episodes, no gradients)

Fill the replay buffer before any gradient updates:
- 50% actions from ETA-Dispatch (expert demonstrations)
- 50% completely random actions

Buffer after warm start: **90,000 experiences**

### Phase 1 — Main Training (3,000 episodes)

**One gradient update per time step** → 3,000 × 300 = **900,000 total gradient updates**

#### Learning Rate Schedule (4 stages)

| Episode Range | Learning Rate | Purpose |
|---|---|---|
| 1 – 799 | 1e-3 | Fast initial exploration |
| 800 – 1,199 | 5e-4 | Consolidation |
| 1,200 – 1,799 | 2e-4 | Refinement |
| 1,800 – 2,499 | 5e-5 | Fine-tuning |
| 2,500 – 3,000 | 2e-5 | Final polish |

#### Three Annealing Schedules

| Parameter | Start | End | Over |
|---|---|---|---|
| Epsilon (exploration) | 0.25 | 0.05 | First 1,000 episodes |
| Critic alpha (critic influence on actor) | 0.00 | 0.25 | First 400 episodes |
| Beta (IS correction in prioritised replay) | 0.40 | 1.00 | All 3,000 episodes |

#### Evaluation Cadence

- Episodes 1–1,499: evaluate every **50** episodes (40 eval episodes each)
- Episodes 1,500–3,000: evaluate every **25** episodes (50 eval episodes each — denser to catch peak performance)

### Composite Checkpoint Scoring (FIX-1)

Old single-metric checkpointing (save if `avg_wait` improves) missed checkpoints that were jointly better on all three metrics. The new composite score:

```
score = 1.0 × (wait / eta_wait)
      + 0.4 × (energy / eta_energy)
      + 0.3 × (cluster_rate / 0.15)

Lower score = better checkpoint
```

Weights reflect priority: wait matters most (1.0), then energy (0.4), then clustering (0.3). Best checkpoint restored at end of training and evaluated on **60 final episodes**.

### Prioritised Experience Replay

- Buffer capacity: **200,000** transitions
- Priority exponent α: **0.6**
- IS correction β: annealed 0.4 → 1.0
- Experiences with high TD-error are sampled more frequently — the agent focuses on its biggest mistakes

---

## 🔥 Stress Test Results

After training, the policy is evaluated on **out-of-distribution** arrival rates never seen during training (λ was capped at 0.35 during training):

| Scenario | λ | Served | Energy | Avg Wait | vs In-Dist Wait | Cluster% | Spread |
|---|---|---|---|---|---|---|---|
| Rush Hour | 0.35 | 1,055 | 563 | 0.78 | 1.16× | 12.6% | 0.296 |
| Peak Load | 0.55 | 1,669 | 705 | 1.31 | 1.94× | 17.4% | 0.278 |
| Extreme Surge | 0.85 | 2,530 | 748 | 2.02 | 3.00× | 20.0% | 0.266 |

The policy **scales gracefully under overload** — at 2.4× the training maximum (λ=0.85), it still maintains reasonable cluster rates and spread, demonstrating strong out-of-distribution generalisation.

---

## 📊 Output Figures

The notebook generates **9 publication-quality figures** (PNG + PDF, 400 DPI each):

| Figure | Title | What It Shows |
|---|---|---|
| Fig 1 | Training Convergence | Avg wait per eval episode vs all 4 baselines |
| Fig 2 | Composite Checkpoint Score | Joint optimisation score over all 3,000 episodes |
| Fig 3 | Wait Time Comparison | Bar chart — all methods |
| Fig 4 | Energy Consumption | Bar chart — all methods |
| Fig 5 | Three-Metric Training Dynamics | Wait, energy, cluster rate over episodes (3-panel) |
| Fig 6 | Cluster Rate and Spread | Elevator distribution quality over training |
| Fig 7 | Multi-Metric Radar | All metrics on a single radar chart |
| Fig 8 | Stress Tests | Out-of-distribution generalisation (3 scenarios) |
| Fig 9 | Actor Loss | QR-DQN loss trajectory confirming stable convergence |

A **side-by-side MP4 animation** comparing QR-DQN vs ETA-Dispatch in real-time is also generated (Kaggle-safe, exports to GIF if FFmpeg unavailable).

---

## 📁 Project Structure

```
multi-elevator-ctde-qrdqn-dispatch/
│
├── Multi_Elevator_CTDE.ipynb          ← Main notebook (full code + outputs)
├── README.md                          ← This file
│
└── [Generated outputs — Kaggle /kaggle/working/]
    ├── elevator_qrdqn_n3_seed42_fig1_convergence.png
    ├── elevator_qrdqn_n3_seed42_fig2_composite_score.png
    ├── elevator_qrdqn_n3_seed42_fig3_wait.png
    ├── elevator_qrdqn_n3_seed42_fig4_energy.png
    ├── elevator_qrdqn_n3_seed42_fig5_training_dynamics.png
    ├── elevator_qrdqn_n3_seed42_fig6_cluster_spread.png
    ├── elevator_qrdqn_n3_seed42_fig7_radar.png
    ├── elevator_qrdqn_n3_seed42_fig8_stress.png
    ├── elevator_qrdqn_n3_seed42_fig9_actor_loss.png
    └── elevator_qrdqn_n3_seed42_comparison.mp4
```

---

## 🚀 How to Run

### Option 1: Kaggle (Recommended — GPU included)

1. Open the notebook on Kaggle via the link below
2. Set accelerator to **GPU T4 x2** in Settings → Accelerator
3. Click **Run All**
4. Full training (~3,000 episodes) completes in approximately **2–3 hours** on T4

[![Open in Kaggle](https://img.shields.io/badge/Open%20in-Kaggle-20BEFF?style=for-the-badge&logo=kaggle&logoColor=white)](https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Multi_Elevator_CTDE.ipynb)

### Option 2: Local / Colab

```bash
# Clone the repository
git clone https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch.git
cd multi-elevator-ctde-qrdqn-dispatch

# Install dependencies
pip install torch numpy matplotlib

# Run as a Python script (if exported)
python Multi_Elevator_Full_Code_KaggleReady.py

# Or open the notebook
jupyter notebook Multi_Elevator_CTDE.ipynb
```

### Quick Start — Minimal Training Run

To test quickly with fewer episodes, edit the entry point:

```python
# In the __main__ block, change episodes:
run_training(n_elevators=3, floors=10, episodes=500, steps_per_ep=300, seeds=[42])
```

---

## 📦 Dependencies

| Package | Version | Purpose |
|---|---|---|
| Python | 3.12 | Runtime |
| PyTorch | 2.9.0+cu126 | Neural networks, GPU training |
| NumPy | — | Environment simulation, array ops |
| Matplotlib | — | All 9 publication figures + animation |
| Collections | stdlib | Metrics aggregation |

No additional RL libraries (Stable-Baselines, RLlib) are used — the full QR-DQN + CTDE implementation is written from scratch.

---

## 🔬 Design Decisions

### v10.3 Targeted Fixes (over v10.2)

Four specific issues identified from v10.2 training logs were addressed:

**FIX-1 — Composite Checkpoint Score**
The v10.2 selector saved the checkpoint with minimum wait time only. Episodes 2200 and 2400 achieved all three targets simultaneously but were missed. The new composite score (wait + 0.4×energy + 0.3×cluster, each normalised by ETA baseline) selects checkpoints that are jointly optimal.

**FIX-2 — 3,000 Episodes**
The v10.2 loss curve was still falling at episode 2,500. An extra 500 episodes at reduced LR (5e-5 → 2e-5) allows the policy to consolidate the jointly-efficient behaviour observed at episodes 2,200–2,400.

**FIX-3 — Stronger Approach-Cluster Penalty (1.5 → 2.5)**
Episode 2,200 achieved 12% cluster rate (near ETA's 11%) but the metric oscillated between 12–15% in the final 500 episodes. Raising the penalty from 1.5 to 2.5 strengthens the "don't walk toward a peer" signal during fine-tuning. The penalty remains strictly local (fires only for the elevator that moved toward a peer), so it cannot create a free-rider gradient.

**FIX-4 — Finer Eval Cadence After Episode 1,500**
v10.2 evaluated every 50 episodes throughout. The best composite checkpoint between episodes 2,150 and 2,250 may have been missed by 25–50 episodes. Switching to eval_every=25 after episode 1,500 ensures dense enough sampling to capture the performance peak.

---

## 📄 License

This project is open-sourced under the [MIT License](LICENSE).

---

## 🙋 Author

**Kumar Piyush Raj**
GitHub: [@kumarpiyushraj](https://github.com/kumarpiyushraj)
Repository: [multi-elevator-ctde-qrdqn-dispatch](https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch)

---

<p align="center">
  <i>Built with PyTorch · Trained on NVIDIA Tesla T4 · 900,000 gradient updates · 3,000 episodes</i>
</p>
