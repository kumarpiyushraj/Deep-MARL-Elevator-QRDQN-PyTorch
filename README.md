<div align="center">

<!-- ████████████████████  HERO BANNER  ████████████████████ -->

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:020817,30:0a1628,60:0f2d52,100:00b4d8&height=240&section=header&text=Multi-Elevator%20Dispatch&fontSize=42&fontColor=e0f7ff&fontAlignY=42&fontStyle=bold&desc=CTDE%20%2B%20QR-DQN%20%E2%80%94%20Teaching%20three%20elevators%20to%20think%20together%2C%20act%20alone&descAlignY=63&descSize=15&descColor=7dd3fc&animation=fadeIn" width="100%"/>

<br/>

<!-- ─────────────  BADGES  ───────────── -->

<p>
  <a href="https://python.org"><img src="https://img.shields.io/badge/Python-3.12-0d1b2a?style=for-the-badge&logo=python&logoColor=7dd3fc&labelColor=0a1628&color=0d2137" alt="Python"/></a>&nbsp;
  <a href="https://pytorch.org"><img src="https://img.shields.io/badge/PyTorch-2.9.0+cu126-0d1b2a?style=for-the-badge&logo=pytorch&logoColor=f87171&labelColor=0a1628&color=0d2137" alt="PyTorch"/></a>&nbsp;
  <a href="https://developer.nvidia.com/cuda-zone"><img src="https://img.shields.io/badge/GPU-Tesla%20T4-0d1b2a?style=for-the-badge&logo=nvidia&logoColor=4ade80&labelColor=0a1628&color=0d2137" alt="CUDA"/></a>&nbsp;
  <a href="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Multi_Elevator_CTDE.ipynb"><img src="https://img.shields.io/badge/📓_Notebook-Open%20on%20GitHub-0d1b2a?style=for-the-badge&logo=github&logoColor=e2e8f0&labelColor=0a1628&color=0d2137" alt="Notebook"/></a>
</p>

<br/>

<!-- ─────────────  HERO QUOTE  ───────────── -->

<table>
<tr>
<td align="center" width="100%">

```
  3 elevators · 10 floors · 3,500 episodes · 1,050,000 gradient updates

  One policy that beats the best classical algorithm
  by  -27% wait time  and  -9.6% energy  — simultaneously.
```

</td>
</tr>
</table>

<br/>

<!-- ─────────────  HERO STATS  ───────────── -->

| 🏢 Elevators | 🏗️ Floors | 📦 Episodes | ⚡ Grad Updates | 🎯 vs ETA Wait | 🔋 vs ETA Energy |
|:---:|:---:|:---:|:---:|:---:|:---:|
| **3** | **10** | **3,500** | **1,050,000** | **`-27.0%`** | **`-9.6%`** |

</div>

<br/>

---

<br/>

<!-- ████████████████████  TABLE OF CONTENTS  ████████████████████ -->

## 📋 &nbsp; Table of Contents

> Quick-jump to any section

| # | Section |
|:---:|:---|
| 01 | [✨ Overview — Three ideas, one system](#-overview) |
| 02 | [🏆 Key Results — Head-to-head vs all baselines](#-key-results) |
| 03 | [📐 System Architecture — UML diagrams](#-system-architecture) |
| 04 | [🧠 Agent Architecture — Class diagram & network layout](#-agent-architecture) |
| 05 | [🔁 Training Sequence & Decision Flow](#-training-sequence--decision-flow) |
| 06 | [🎭 Use Cases — Actor responsibilities](#-use-cases) |
| 07 | [🏗️ Environment — Building config & observation space](#-environment) |
| 08 | [🎯 Reward Design — Decomposed per-agent rewards](#-reward-design) |
| 09 | [📏 Baseline Policies](#-baseline-policies) |
| 10 | [🔄 Training Pipeline — LR schedule & composite scoring](#-training-pipeline) |
| 11 | [🔥 Stress Test Results — OOD generalisation](#-stress-test-results) |
| 12 | [📊 Result Figures — All 8 plots explained](#-result-figures) |
| 13 | [📁 Project Structure](#-project-structure) |
| 14 | [🚀 How to Run](#-how-to-run) |
| 15 | [📦 Dependencies](#-dependencies) |
| 16 | [🔬 Design Decisions — v10.3 Fixes](#-design-decisions--v103-fixes) |

<br/>

---

<br/>

<!-- ████████████████████  OVERVIEW  ████████████████████ -->

## <img src="https://img.shields.io/badge/%E2%80%8B-✨ Overview-00b4d8?style=flat-square&labelColor=020817&color=020817" height="28"/> &nbsp; ✨ Overview

<table>
<tr>
<td>

> **The Problem:** The **Multi-Elevator Group Control Problem** — a classic operations-research challenge where multiple elevators must coordinate to serve passengers efficiently **with zero real-time communication** between them.

</td>
</tr>
</table>

<br/>

**Three ideas. One system. Emergent coordination.**

<br/>

<div align="center">

| &nbsp; | Idea | What It Solves |
|:---:|:---|:---|
| 🧠 | **CTDE** — Centralised Training, Decentralised Execution | Trains with a global building view; runs with only local sensor data per elevator |
| 📊 | **QR-DQN** — Quantile Regression Deep Q-Network | Models full *distributions* of returns (not just expectations) for robust learning under multi-agent uncertainty |
| ⚡ | **Dueling Net + Prioritised Replay** | Separates state value from action advantage; focuses gradient updates on the most surprising transitions |

</div>

<br/>

> **The emergent result:** Elevators self-organise — spreading across the building, avoiding bunching, gravitating toward high-demand floors — **with zero hard-coded coordination rules.**

<br/>

<div align="center">

| 🔧 Version | 🖥️ Hardware | 🧮 Framework | 📈 Scale |
|:---:|:---:|:---:|:---|
| `v10.3` | Kaggle NVIDIA Tesla T4 | PyTorch `2.9.0+cu126` | 3,500 ep × 300 steps × 3 elevators = **3,150,000 pushes · 1,050,000 updates** |

</div>

<br/>

---

<br/>

<!-- ████████████████████  KEY RESULTS  ████████████████████ -->

## <img src="https://img.shields.io/badge/%E2%80%8B-🏆 Key Results-fbbf24?style=flat-square&labelColor=020817&color=020817" height="28"/> &nbsp; 🏆 Key Results

<table>
<tr>
<td>

> Evaluated over **60 episodes** after restoring the best composite checkpoint. &nbsp;Seed `42`.

</td>
</tr>
</table>

<br/>

### 📊 &nbsp; Full Comparison — All Methods

<div align="center">

| Method | Avg Wait ↓ | Energy ↓ | Cluster Rate ↓ | Spread ↑ | Served/ep |
|:---|:---:|:---:|:---:|:---:|:---:|
| Nearest Car | 9.679 | 409.8 | 98.5% | 0.005 | 580.2 |
| SCAN / LOOK | 1.415 | 899.6 | 98.5% | 0.015 | 670.7 |
| Sectoring | 0.854 | 486.1 | 28.5% | 0.234 | 747.1 |
| ETA-Dispatch *(best classical)* | 0.673 | 472.7 | 10.2% | 0.297 | 748.8 |
| 🥇 **QR-DQN CTDE (ours)** | **`0.492`** | **`427.2`** | **`10.0%`** | **`0.306`** | 667.2 |

</div>

<br/>

### 🎯 &nbsp; Head-to-Head: Our Method vs ETA-Dispatch

<div align="center">

| Metric | 🥇 Ours | vs | ETA | Δ Delta | Verdict |
|:---|:---:|:---:|:---:|:---:|:---:|
| Avg Wait | **`0.492`** | vs | 0.673 | **−27.0%** | ✅ BETTER |
| Energy | **`427.2`** | vs | 472.7 | **−9.6%** | ✅ BETTER |
| Cluster Rate | **`10.0%`** | vs | 10.2% | **−1.6pp** | ✅ BETTER |
| Spread Index | **`0.306`** | vs | 0.297 | **+3.0%** | ✅ BETTER |

</div>

<br/>

> ► All 4 metrics beat ETA **simultaneously** — required all 4 v10.3 targeted fixes to achieve.

<br/>

> **The critical insight:** Beating ETA on *wait alone* is easy — move more. Beating it on *energy alone* is easy — move less. Beating it on **both simultaneously** required learning a fundamentally smarter positioning strategy. That's what CTDE + QR-DQN enables.

<br/>

---

<br/>

<!-- ████████████████████  SYSTEM ARCHITECTURE  ████████████████████ -->

## <img src="https://img.shields.io/badge/%E2%80%8B-📐 System Architecture-818cf8?style=flat-square&labelColor=020817&color=020817" height="28"/> &nbsp; 📐 System Architecture

<table>
<tr>
<td>

> The full system spans **four layers**: traffic generation → environment simulation → the learning agent → evaluation & reporting. The diagram shows how all components connect — from Poisson passenger arrivals through the reward engine, into the QR-DQN actor and centralised critic, and out to the reporting layer.

</td>
</tr>
</table>

<br/>

<div align="center">

---

**`SYSTEM DIAGRAM`**

<img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/UML%20Diagrams/Multi_Elevator_Sys.svg?raw=true" alt="System Architecture Diagram" width="96%" style="border-radius:12px;"/>

---

</div>

<br/>

### 🗂️ &nbsp; Four Layers at a Glance

<div align="center">

| Layer | Components | Role |
|:---|:---|:---|
| 🔵 **User & Sensor** | Poisson Arrivals · Traffic Modes | Generates λ ∈ [0.12–0.35] passengers/floor/step across 3 traffic modes |
| 🟣 **Control & Scheduling** | Hall Queue · EMA Responsibility · Reward Engine | Simulates 10-floor, 3-elevator building; computes decomposed rewards |
| 🟢 **Learning & Decision** | QR-Dueling Actor · Centralised Critic · PER Buffer · Baselines | All neural network components + classical comparison agents |
| 🟡 **Evaluation & Reporting** | Greedy Eval · Metrics · 9 Figures | 30–60 episode evaluations, composite checkpoint scoring, output generation |

</div>

<br/>

---

<br/>

<!-- ████████████████████  AGENT ARCHITECTURE  ████████████████████ -->

## <img src="https://img.shields.io/badge/%E2%80%8B-🧠 Agent Architecture-a78bfa?style=flat-square&labelColor=020817&color=020817" height="28"/> &nbsp; 🧠 Agent Architecture

<table>
<tr>
<td>

> The class diagram shows the **complete object model** — every class, its attributes, its methods, and how they relate.

</td>
</tr>
</table>

<br/>

<div align="center">

---

**`CLASS DIAGRAM`**

<img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/UML%20Diagrams/Multi_Elevator_Class.svg?raw=true" alt="Class Diagram" width="96%" style="border-radius:12px;"/>

---

</div>

<br/>

### 🔗 &nbsp; Key Relationships

- **`CTDEQRDQNAgent`** owns two `QRDuelingNet` instances (live `actor` + slowly-updated `target_actor`, τ=0.002), two `CentralisedCritic` instances (training only — discarded at inference), and one `PrioritizedReplayBuffer` (capacity 200k, α=0.6)
- **`MultiElevatorEnv`** sends `obs[n, 18]` and `rewards[n]` to the agent, receives `actions[n]` back
- **`EvaluatorLogger`** orchestrates evaluation without owning any component
- **`BaselinePolicy`** extends the agent in eval mode as a comparison interface

<br/>

### 🏛️ &nbsp; Network Architectures

```
QRDuelingNet
────────────────────────────────────────────────────────────────────────
  obs(18) → Linear(18→256)+LN+ReLU → Linear(256→256)+LN+ReLU
                  ↙                              ↘
         Value(256→128→51)           Advantage(256→128→3×51)
                  ↘                              ↙
            Q(s,a) = V(s) + A(s,a) − mean_a[A]
            Output: [batch × 3 actions × 51 quantiles]

CentralisedCritic  (training only — discarded at execution)
────────────────────────────────────────────────────────────────────────
  joint_state(16) → Linear(16→256)+LN+ReLU → Linear(256→128→1)
  Output: V(s) — single scalar
```

<br/>

---

<br/>

<!-- ████████████████████  TRAINING SEQUENCE  ████████████████████ -->

## <img src="https://img.shields.io/badge/%E2%80%8B-🔁 Training Sequence %26 Decision Flow-34d399?style=flat-square&labelColor=020817&color=020817" height="28"/> &nbsp; 🔁 Training Sequence & Decision Flow

<br/>

<div align="center">

---

**`SEQUENCE DIAGRAM`** — *Message flow between Passenger, Environment, Elevator Agent, and Administrator per time step*

<img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/UML%20Diagrams/Multi_Elevator_Sequence_Diagram.svg?raw=true" alt="Sequence Diagram" width="88%" style="border-radius:12px;"/>

---

</div>

<br/>

### ⏱️ &nbsp; Per Time Step (300 per episode)

```
  1. Passenger  → Environment    : Press floor call button → hall[f] incremented
  2. Environment → Agent         : Send observation (dim 18) — pos, queues, peer info
  3. Agent       → self          : Select action via QR-DQN (argmax over mean quantiles)
  4. Agent       → Environment   : Return action  [ UP | DOWN | IDLE ]
  5. Environment → self          : Move elevator · serve passengers · compute reward
  6. Agent       → self          : Store transition in PER buffer · train · soft update
```

<br/>

<div align="center">

---

**`ACTIVITY DIAGRAM`** — *Complete lifecycle across 4 swim lanes: Passenger · Environment · Elevator Agent · Administrator*

<img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/UML%20Diagrams/Multi_Elevator_Activity_Diagram.svg?raw=true" alt="Activity Diagram" width="88%" style="border-radius:12px;"/>

---

</div>

<br/>

### 🏊 &nbsp; Four Swim Lanes

<div align="center">

| Lane | Actor | Key Activities |
|:---|:---|:---|
| 🔵 **Passenger** | Real-world user | Arrives at floor → presses call button |
| 🟣 **Environment** | `MultiElevatorEnv` | Records hall call → generates obs → serves passengers → computes reward |
| 🟢 **Elevator Agent** | `CTDEQRDQNAgent` | Receives obs(18) → selects action → stores in PER → trains actor + critic |
| 🟡 **Administrator** | Training loop | Reviews metrics → evaluates composite score → saves best checkpoint → generates figures |

</div>

<br/>

---

<br/>

<!-- ████████████████████  USE CASES  ████████████████████ -->

## <img src="https://img.shields.io/badge/%E2%80%8B-🎭 Use Cases-fb923c?style=flat-square&labelColor=020817&color=020817" height="28"/> &nbsp; 🎭 Use Cases

<br/>

<div align="center">

---

**`USE CASE DIAGRAM`**

<img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/UML%20Diagrams/Multi_Elevator_Use_Case.svg?raw=true" alt="Use Case Diagram" width="78%" style="border-radius:12px;"/>

---

</div>

<br/>

<div align="center">

| Actor | Use Cases |
|:---|:---|
| 🧍 **Passenger** | Press Floor Call Button · Observe Wait Time · Board or Alight Elevator |
| 🤖 **Elevator Agent** | Dispatch Elevator Action · Serve Passengers at Floor · Learn from Experience |
| 👨‍💼 **Administrator** | Monitor Training Logs · Evaluate System Performance → «extend» Save Best Checkpoint |

</div>

<br/>

> **Include chain:** `Press Floor Call Button` → «include» `Dispatch Elevator Action` → «include» `Serve Passengers at Floor` → «include» `Board or Alight Elevator`

<br/>

---

<br/>

<!-- ████████████████████  ENVIRONMENT  ████████████████████ -->

## <img src="https://img.shields.io/badge/%E2%80%8B-🏗️ Environment-22d3ee?style=flat-square&labelColor=020817&color=020817" height="28"/> &nbsp; 🏗️ Environment

### 🏢 &nbsp; Building Configuration

<div align="center">

| Parameter | Value | Notes |
|:---|:---:|:---|
| Elevators | **3** | E1, E2, E3 — all share actor weights |
| Floors | **10** | Floor 0 = lobby · Floor 9 = top |
| Arrival rate λ | **[0.12, 0.35]** | New random value drawn each episode |
| Lobby boost | **5×** | Ground floor gets 5× more passengers |
| Top floor boost | **1.5×** | Top floor gets 1.5× more passengers |
| Steps / episode | **300** | Fixed length, no terminal condition |
| Training episodes | **3,000** | + 100 warm-up episodes before gradients |

</div>

<br/>

### 🚦 &nbsp; Traffic Modes

<div align="center">

| Mode | Weight | Description |
|:---|:---:|:---|
| `normal` | 50% | Standard Poisson arrivals at all floors |
| `up_peak` | 25% | Lobby weight ×2 — morning rush, everyone going up |
| `down_peak` | 25% | Upper floors ×1.8 — evening rush, everyone coming down |

</div>

<br/>

### 🔭 &nbsp; Observation Space — 18 Dimensions

<div align="center">

| Index | Dim | Content |
|:---|:---:|:---|
| `[0]` | 1 | Own floor position (normalised: `floor / 9`) |
| `[1–10]` | 10 | `hall[0] … hall[9] / 5.0` — queue depth, clipped 0–1 |
| `[11–12]` | 2 | Peer elevator positions (normalised) |
| `[13–14]` | 2 | Peer elevator targets (normalised; `−0.1` = idle) |
| `[15–17]` | 3 | One-hot elevator identity `[1,0,0]` · `[0,1,0]` · `[0,0,1]` |

</div>

<br/>

### ⏰ &nbsp; Step Sequence — One Clock Tick

```
  1. SERVE   → elevator clears all passengers on its current floor
  2. MOVE    → execute action (UP / DOWN / IDLE), pay energy cost if moved
  3. SPAWN   → Poisson(λ × weight[f]) new passengers added to every floor
  4. RESP    → compute nearest elevator per floor; EMA-normalise responsibility
  5. CLUSTER → check if any elevator moved toward a peer within 2 floors
  6. IDLE    → check if stationary with no nearby calls → grant idle bonus
  7. REWARD  → compute per-elevator decomposed reward, clip to [−20, +20]
```

<br/>

---

<br/>

<!-- ████████████████████  REWARD DESIGN  ████████████████████ -->

## <img src="https://img.shields.io/badge/%E2%80%8B-🎯 Reward Design-f472b6?style=flat-square&labelColor=020817&color=020817" height="28"/> &nbsp; 🎯 Reward Design

<table>
<tr>
<td>

> Each elevator receives a **fully decomposed, strictly per-agent reward** — no shared terms, no free-rider problem.

</td>
</tr>
</table>

<br/>

```
  reward_i  =  +8.0  ×  passengers_served_i
               −3.0  ×  responsibility_norm_i      ← EMA-normalised nearest-floor queue load
               −0.5  ×  move_cost_i                ← 1 per floor moved (energy)
               +0.3  ×  idle_bonus_i               ← bonus: stationary + no nearby calls
               −2.5  ×  approach_cluster_i         ← penalty: moved toward peer ≤2 floors

  Clipped to [−20, +20]
```

<br/>

<div align="center">

| Term | Weight | Purpose |
|:---|:---:|:---|
| Serve reward | `+8.0` | Dominant signal for the primary task |
| Responsibility penalty | `−3.0` | Drives elevators toward floors they're nearest to |
| Energy penalty | `−0.5` | Gentle discouragement of pointless movement |
| Idle bonus | `+0.3` | Rewards strategic waiting at uncrowded positions |
| Cluster penalty | `−2.5` | Discourages bunching — strictly local, no free-rider gradient *(raised from 1.5 in v10.3)* |

</div>

<br/>

> **Responsibility is EMA-normalised:** &nbsp;`resp_ema = 0.95 × resp_ema + 0.05 × max_resp` — prevents a sudden surge from creating a massive one-step gradient spike.

<br/>

---

<br/>

<!-- ████████████████████  BASELINES  ████████████████████ -->

## <img src="https://img.shields.io/badge/%E2%80%8B-📏 Baseline Policies-64748b?style=flat-square&labelColor=020817&color=020817" height="28"/> &nbsp; 📏 Baseline Policies

<div align="center">

| Policy | Strategy | Avg Wait | Energy | Cluster% |
|:---|:---|:---:|:---:|:---:|
| **Nearest Car** | Greedy — go to closest pending call | 9.679 | 409.8 | 98.5% |
| **SCAN / LOOK** | Sweep up then down, reverse on empty half | 1.415 | 899.6 | 98.5% |
| **Sectoring** | Each elevator owns a fixed floor zone | 0.854 | 486.1 | 28.5% |
| **ETA-Dispatch** | Assign each call to min estimated-travel-time elevator | 0.673 | 472.7 | 10.2% |

</div>

<br/>

> **ETA-Dispatch** is the primary benchmark. For each unassigned call it computes travel time for every elevator (direct if idle, via current target if busy) and assigns the minimum. Most sophisticated classical method — the RL agent's entire training goal is to beat it jointly on *all* metrics.

<br/>

---

<br/>

<!-- ████████████████████  TRAINING PIPELINE  ████████████████████ -->

## <img src="https://img.shields.io/badge/%E2%80%8B-🔄 Training Pipeline-38bdf8?style=flat-square&labelColor=020817&color=020817" height="28"/> &nbsp; 🔄 Training Pipeline

### 🌡️ &nbsp; Phase 0 — Warm Start (100 episodes, no gradient updates)

```
  100 episodes × 300 steps × 3 elevators = 90,000 buffer entries

  50% ETA-Dispatch actions  +  50% random actions
  Purpose: seed buffer with competent + exploratory experience
```

<br/>

### 🚀 &nbsp; Phase 1 — Main Training (3,500 episodes)

**Total gradient updates: 3,500 × 300 = `1,050,000`**

<br/>

#### 📉 &nbsp; Learning Rate Schedule

<div align="center">

| Episodes | Learning Rate | Purpose |
|:---|:---:|:---|
| Ep 1 – 799 | `1e-3` | Fast initial learning |
| Ep 800 – 1199 | `5e-4` | Consolidation |
| Ep 1200 – 1799 | `2e-4` | Refinement |
| Ep 1800 – 2499 | `5e-5` | Fine-tuning |
| Ep 2500 – 3500 | `2e-5` | Final polish *(new in v10.3)* |

</div>

<br/>

#### 🌀 &nbsp; Three Annealing Schedules Running in Parallel

<div align="center">

| Schedule | Start | End | Notes |
|:---|:---:|:---:|:---|
| ε (exploration) | `0.25` | `0.05` | Anneals ep 1→1000, then flat |
| critic_α (blend) | `0.00` | `0.25` | Ramps ep 1→400, then flat |
| β (IS correction) | `0.40` | `1.00` | Linear across all 3,500 episodes |

</div>

<br/>

#### 🏅 &nbsp; Composite Checkpoint Score *(FIX-1)*

```python
score = 1.0 × (avg_wait    / eta_wait)      # wait   — highest priority
      + 0.4 × (energy      / eta_energy)    # energy — second
      + 0.3 × (cluster_rate / 0.15)         # cluster — third (soft target 15%)

# Lower = better.
# score = 1.70  →  equal to ETA on all three metrics simultaneously.
# Best checkpoint: ep 2075  →  score 1.347  →  wait 0.51, energy 403.7, cluster 12.2%
```

<br/>

#### 📅 &nbsp; Evaluation Cadence *(FIX-4)*

<div align="center">

| Episode Range | Frequency | Episodes per Eval |
|:---|:---:|:---:|
| Ep 1 – 1499 | Every 50 eps | 40 |
| Ep 1500 – 3500 | Every 25 eps | 50 |

</div>

<br/>

---

<br/>

<!-- ████████████████████  STRESS TESTS  ████████████████████ -->

## <img src="https://img.shields.io/badge/%E2%80%8B-🔥 Stress Test Results-ef4444?style=flat-square&labelColor=020817&color=020817" height="28"/> &nbsp; 🔥 Stress Test Results

<table>
<tr>
<td>

> Tested on **out-of-distribution** arrival rates — never seen during training (λ capped at 0.35 during training).

</td>
</tr>
</table>

<br/>

<div align="center">

| Scenario | λ | Served | Energy | Avg Wait | vs In-Dist | Cluster% | Spread |
|:---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Rush Hour** | `0.35` | 1,055 | 563 | 0.78 | 1.16× | 12.6% | 0.296 |
| **Peak Load** | `0.55` | 1,669 | 705 | 1.31 | 1.94× | 17.4% | 0.278 |
| **Extreme Surge** | `0.85` | 2,530 | 748 | 2.02 | 3.00× | 20.0% | 0.266 |

</div>

<br/>

> **At λ = 0.85** — 2.4× the training max — the policy still maintains structured coverage (spread = 0.266, cluster = 20%) and serves **2,530 passengers/episode**. Graceful degradation, not catastrophic failure.

<br/>

---

<br/>

<!-- ████████████████████  RESULT FIGURES  ████████████████████ -->

## <img src="https://img.shields.io/badge/%E2%80%8B-📊 Result Figures-a3e635?style=flat-square&labelColor=020817&color=020817" height="28"/> &nbsp; 📊 Result Figures

<table>
<tr>
<td>

> All 8 figures generated at **400 DPI** (PNG + PDF) on Kaggle NVIDIA Tesla T4.
> Full set: [`Results_Diagrams/`](https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/tree/main/Results_Diagrams)

</td>
</tr>
</table>

<br/>

---

### 📈 &nbsp; Figure 1 — Training Convergence

<div align="center">

<img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Training_Convergence.png?raw=true" alt="Training Convergence" width="92%"/>

</div>

> Avg wait per eval episode vs all 4 baselines. LR step-down points shown as vertical lines. The smoothed curve crosses below ETA-Dispatch (0.673) around **episode 150** and continues improving through all four LR stages. Best composite checkpoint captured at **ep 2,075** (wait = 0.51, score = 1.347).

<br/>

---

### 📉 &nbsp; Figures 2 & 3 — Composite Score · Wait Time Comparison

<div align="center">

| Composite Checkpoint Score | Avg Wait Time |
|:---:|:---:|
| <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Composite_Checkpoint.png?raw=true" alt="Composite Checkpoint Score" width="100%"/> | <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Avg_Wait_Time.png?raw=true" alt="Average Wait Time" width="100%"/> |
| Reference line at **1.70** = all metrics equal to ETA. Best checkpoint (ep 2,075, score = **1.347**) sits 19% below. | QR-DQN CTDE: **0.492** — **27% below** ETA-Dispatch and **94.9% below** Nearest Car. |

</div>

<br/>

---

### 🔬 &nbsp; Figure 4 — Training Dynamics (Three-Panel)

<div align="center">

<img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Training_Dynamics.png?raw=true" alt="Training Dynamics" width="92%"/>

</div>

> **Panel 1 (Wait):** 0.78 (ep 50) → below ETA by ep 150 → 0.49 at best checkpoint.
> **Panel 2 (Energy):** Gradually falls 520 → 427 as the policy learns smarter positioning.
> **Panel 3 (Cluster):** Drops sharply 52% → ~10% by ep 1,500. Stronger penalty (FIX-3: 1.5→2.5) prevents the v10.2 oscillation in the final 500 episodes.

<br/>

---

### ⚡ &nbsp; Figures 5 & 6 — Energy Consumption · Spatial Distribution

<div align="center">

| Energy Consumption | Cluster Rate & Spread Index |
|:---:|:---:|
| <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Energy_Consumption.png?raw=true" alt="Energy Consumption" width="100%"/> | <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Elevator_Spatial_Distribution.png?raw=true" alt="Spatial Distribution" width="100%"/> |
| QR-DQN uses **427.2** motor-steps vs ETA's **472.7** — **9.6% fewer movements**. SCAN/LOOK worst at 899.6. | Spread rises from near-zero to **0.306** — exceeding ETA (0.297). Emergent self-organisation from reward alone. |

</div>

<br/>

---

### 🌡️ &nbsp; Figures 7 & 8 — Stress Tests · Actor Loss

<div align="center">

| Stress Test Generalisation | Actor Loss Trajectory |
|:---:|:---:|
| <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Stress_Tests.png?raw=true" alt="Stress Tests" width="100%"/> | <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Actor_Loss.png?raw=true" alt="Actor Loss" width="100%"/> |
| At **λ = 0.85** (2.4× training max), wait rises to 2.02 but structure holds. No catastrophic collapse. | Falls from **~1.10** (ep 50) to **~0.63** (ep 3,500) with no divergence. Each LR step causes a brief expected uptick. |

</div>

<br/>

---

<br/>

<!-- ████████████████████  PROJECT STRUCTURE  ████████████████████ -->

## <img src="https://img.shields.io/badge/%E2%80%8B-📁 Project Structure-94a3b8?style=flat-square&labelColor=020817&color=020817" height="28"/> &nbsp; 📁 Project Structure

```
multi-elevator-ctde-qrdqn-dispatch/
│
├── 📓 Multi_Elevator_CTDE.ipynb              ← Full notebook (code + all outputs inline)
├── 📄 README.md
│
├── 📂 Results_Diagrams/                      ← 8 publication-quality figures (PNG + PDF, 400 DPI)
│   ├── Training_Convergence.png
│   ├── Composite_Checkpoint.png
│   ├── Avg_Wait_Time.png
│   ├── Energy_Consumption.png
│   ├── Training_Dynamics.png
│   ├── Elevator_Spatial_Distribution.png
│   ├── Stress_Tests.png
│   └── Actor_Loss.png
│
└── 📂 UML Diagrams/                          ← Complete system design documentation
    ├── Multi_Elevator_Sys.svg                ← Full 4-layer system architecture
    ├── Multi_Elevator_Class.svg              ← Class diagram — all objects & relationships
    ├── Multi_Elevator_Sequence_Diagram.svg   ← Message flow across training episode
    ├── Multi_Elevator_Activity_Diagram.svg   ← Decision & learning flow (4 swim lanes)
    └── Multi_Elevator_Use_Case.svg           ← Actor use cases & include/extend chains
```

<br/>

---

<br/>

<!-- ████████████████████  HOW TO RUN  ████████████████████ -->

## <img src="https://img.shields.io/badge/%E2%80%8B-🚀 How to Run-4ade80?style=flat-square&labelColor=020817&color=020817" height="28"/> &nbsp; 🚀 How to Run

### ☁️ &nbsp; Option 1 — Kaggle *(Recommended — Free T4 GPU)*

```
1. Open: Multi_Elevator_CTDE.ipynb  (link in badge above)
2. Import to your Kaggle account
3. Settings → Accelerator → GPU T4
4. Run All — full training completes in approximately 2–3 hours
```

<br/>

### 💻 &nbsp; Option 2 — Local / Google Colab

```bash
git clone https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch.git
cd multi-elevator-ctde-qrdqn-dispatch
pip install torch numpy matplotlib
jupyter notebook Multi_Elevator_CTDE.ipynb
```

<br/>

### 🧪 &nbsp; Quick Smoke Test (~20 minutes on CPU)

```python
# Edit the entry point at the bottom of the notebook:
run_training(n_elevators=3, floors=10, episodes=500, steps_per_ep=300, seeds=[42])
```

<br/>

---

<br/>

<!-- ████████████████████  DEPENDENCIES  ████████████████████ -->

## <img src="https://img.shields.io/badge/%E2%80%8B-📦 Dependencies-fbbf24?style=flat-square&labelColor=020817&color=020817" height="28"/> &nbsp; 📦 Dependencies

<div align="center">

| Package | Version | Role |
|:---|:---:|:---|
| **Python** | `3.12` | Runtime |
| **PyTorch** | `2.9.0+cu126` | Neural networks, GPU acceleration |
| **NumPy** | latest | Environment simulation, array ops |
| **Matplotlib** | latest | 8 publication figures + MP4 animation |

</div>

<br/>

> **Zero external RL frameworks.** QR-DQN, CTDE, dueling architecture, and prioritised replay are all implemented from scratch in pure PyTorch — no Stable-Baselines3, RLlib, or similar.

<br/>

---

<br/>

<!-- ████████████████████  DESIGN DECISIONS  ████████████████████ -->

## <img src="https://img.shields.io/badge/%E2%80%8B-🔬 Design Decisions — v10.3 Fixes-e879f9?style=flat-square&labelColor=020817&color=020817" height="28"/> &nbsp; 🔬 Design Decisions — v10.3 Fixes

> Four targeted changes over v10.2, each fixing a specific failure mode identified from training logs.

<br/>

<details>
<summary><b>🔧 FIX-1 — Composite Checkpoint Score</b></summary>

<br/>

**Problem:** v10.2 saved `min(avg_wait)` only → selected ep 2050 (score = 1.411). Episodes 2200 (energy = 440.2, cluster = 12%, score = 1.374) and ep 2400 were jointly superior but never saved.

**Fix:** `score = 1.0×(wait/eta_wait) + 0.4×(energy/eta_energy) + 0.3×(cluster/0.15)`. Lower = better. Ep 2200 now correctly selected over ep 2050.

<br/>

</details>

<details>
<summary><b>📆 FIX-2 — Extended to 3,500 Episodes</b></summary>

<br/>

**Problem:** v10.2 actor loss was still at 0.498 and falling at ep 3000. Best policy was still emerging.

**Fix:** 500 additional episodes at `LR = 2e-5`. Gives the jointly-efficient behaviour at ep 2200–2400 time to consolidate.

<br/>

</details>

<details>
<summary><b>⚖️ FIX-3 — Cluster Penalty Raised from 1.5 → 2.5</b></summary>

<br/>

**Problem:** ep 2200 achieved 12% cluster but the metric oscillated 12–15% in v10.2's final 500 episodes — signal too weak to hold the gain.

**Fix:** `approach_cluster` coefficient raised to **2.5**. Strictly local — fires only for the elevator that moved toward a peer, never the peer — so no free-rider gradient is created.

<br/>

</details>

<details>
<summary><b>🔍 FIX-4 — Finer Eval Cadence After Episode 1,500</b></summary>

<br/>

**Problem:** `eval_every=50` throughout meant the peak composite checkpoint between ep 2150–2250 could be missed by up to 50 episodes.

**Fix:** `eval_every=25` after ep 1500, with 50 eval episodes per checkpoint (vs 40 earlier). Dense late-training measurement where quality is highest.

<br/>

</details>

<br/>

---

<br/>

<!-- ████████████████████  FOOTER  ████████████████████ -->

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:00b4d8,40:0a3d62,100:020817&height=130&section=footer" width="100%"/>

<br/>

**Built from scratch &nbsp;·&nbsp; PyTorch 2.9 &nbsp;·&nbsp; NVIDIA Tesla T4 &nbsp;·&nbsp; 1,050,000 gradient updates**

<br/>

[![Star this repo](https://img.shields.io/github/stars/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch?style=for-the-badge&logo=github&color=fbbf24&labelColor=0d1117&label=⭐%20Star%20this%20repo)](https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch)

<br/>

*© 2026 Kumar Piyush Raj &nbsp;·&nbsp; [GitHub @kumarpiyushraj](https://github.com/kumarpiyushraj)*

</div>
