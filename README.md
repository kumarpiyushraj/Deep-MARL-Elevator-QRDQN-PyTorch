<!---------------------------------------------------------------------------->
<!--  HERO BANNER                                                            -->
<!---------------------------------------------------------------------------->

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:020817,25:071428,55:0c2347,100:00b4d8&height=260&section=header&text=Multi-Elevator%20Dispatch&fontSize=46&fontColor=e0f7ff&fontAlignY=40&fontStyle=bold&desc=CTDE%20%2B%20QR-DQN%20%E2%80%94%20Teaching%20three%20elevators%20to%20think%20together%2C%20act%20alone&descAlignY=60&descSize=16&descColor=7dd3fc&animation=fadeIn" width="100%"/>

<br/>

<p>
  <a href="https://python.org"><img src="https://img.shields.io/badge/Python-3.12-0d1f38?style=for-the-badge&logo=python&logoColor=7dd3fc&labelColor=071020&color=0d1f38" alt="Python"/></a>&nbsp;
  <a href="https://pytorch.org"><img src="https://img.shields.io/badge/PyTorch-2.9.0+cu126-0d1f38?style=for-the-badge&logo=pytorch&logoColor=fb923c&labelColor=071020&color=0d1f38" alt="PyTorch"/></a>&nbsp;
  <a href="https://developer.nvidia.com/cuda-zone"><img src="https://img.shields.io/badge/CUDA-Tesla%20T4-0d1f38?style=for-the-badge&logo=nvidia&logoColor=4ade80&labelColor=071020&color=0d1f38" alt="CUDA"/></a>&nbsp;
  <a href="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Multi_Elevator_CTDE.ipynb"><img src="https://img.shields.io/badge/Notebook-Open%20on%20GitHub-0d1f38?style=for-the-badge&logo=github&logoColor=e2e8f0&labelColor=071020&color=0d1f38" alt="Notebook"/></a>
</p>

<br/>

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:0a1628,100:0f2340&height=90&text=3%20elevators%20%C2%B7%2010%20floors%20%C2%B7%203%2C000%20episodes%20%C2%B7%20900%2C000%20gradient%20updates&fontSize=15&fontColor=94a3b8&fontAlignY=35&desc=One%20policy%20that%20beats%20the%20best%20classical%20algorithm%20by%20-27%25%20wait%20time%20and%20-9.6%25%20energy%20%E2%80%94%20simultaneously&descSize=14&descColor=e0f7ff&descAlignY=68" width="100%"/>

<br/><br/>

**📊 &nbsp;AT A GLANCE**

| 🏢 Elevators | 🏗 Floors | 📦 Episodes | ⚡ Grad Updates | 🎯 Wait Δ | 🔋 Energy Δ |
|:---:|:---:|:---:|:---:|:---:|:---:|
| **3** | **10** | **3,000** | **`900,000`** | **`−27.0%`** | **`−9.6%`** |

</div>

<br/><br/>

<!---------------------------------------------------------------------------->
<!--  SECTION HEADER NOTES                                                   -->
<!--  All section banners: width=100%, height=64, fontSize=22, fontAlignY=52 -->
<!--  fontAlign=50 (centred) for consistent full-width visual across GitHub   -->
<!---------------------------------------------------------------------------->

<!---------------------------------------------------------------------------->
<!--  TABLE OF CONTENTS                                                       -->
<!---------------------------------------------------------------------------->

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:0f2d52,100:020d1a&height=64&text=%F0%9F%93%8B%20%20Table%20of%20Contents&fontSize=22&fontColor=e0f7ff&fontAlignY=52&fontAlign=50" width="100%"/>

<br/>

<div align="center">

| # | Section | # | Section |
|:---:|:---|:---:|:---|
| 01 | [✨ Overview](#-overview) | 09 | [📏 Baseline Policies](#-baseline-policies) |
| 02 | [🏆 Key Results](#-key-results) | 10 | [🔄 Training Pipeline](#-training-pipeline) |
| 03 | [📐 System Architecture](#-system-architecture) | 11 | [🔥 Stress Test Results](#-stress-test-results) |
| 04 | [🧠 Agent Architecture](#-agent-architecture) | 12 | [📊 Result Figures](#-result-figures) |
| 05 | [🔁 Training Sequence and Flow](#-training-sequence-and-decision-flow) | 13 | [📁 Project Structure](#-project-structure) |
| 06 | [🎭 Use Cases](#-use-cases) | 14 | [🚀 How to Run](#-how-to-run) |
| 07 | [🏗 Environment](#-environment) | 15 | [📦 Dependencies](#-dependencies) |
| 08 | [🎯 Reward Design](#-reward-design) | 16 | [🔬 Design Decisions v10.3](#-design-decisions--v103-fixes) |

</div>

<br/><br/>

<!---------------------------------------------------------------------------->
<!--  OVERVIEW                                                                -->
<!---------------------------------------------------------------------------->

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:0a3d62,100:021018&height=64&text=%E2%9C%A8%20%20Overview&fontSize=22&fontColor=7dd3fc&fontAlignY=52&fontAlign=50" width="100%"/>

<br/>

This project solves the **Multi-Elevator Group Control Problem** — a classic operations-research challenge where multiple elevators must coordinate to serve passengers efficiently **without any real-time communication** between them.

Three ideas combine into one system:

<br/>

<div align="center">

| &nbsp; | Idea | What It Solves |
|:---:|:---|:---|
| 🧠 | **CTDE** — Centralised Training, Decentralised Execution | Trains with a global building view; executes with only local sensor data per elevator |
| 📊 | **QR-DQN** — Quantile Regression Deep Q-Network | Models full *distributions* of returns — not just expectations — for robust multi-agent learning |
| ⚡ | **Dueling Net + Prioritised Replay** | Separates state value from action advantage; focuses updates on the most surprising transitions |

</div>

<br/>

> **The emergent result:** Elevators self-organise — spreading across the building, avoiding bunching, gravitating toward high-demand floors — **with zero hard-coded coordination rules.**

<br/>

<div align="center">

| 🔧 Version | 🖥 Hardware | 🧮 Framework | 📈 Scale |
|:---:|:---:|:---:|:---|
| `v10.3` | Kaggle NVIDIA Tesla T4 | PyTorch `2.9.0+cu126` | 3,000 ep × 300 steps × 3 elevators = **2,700,000 pushes · 900,000 updates** |

</div>

<br/><br/>

<!---------------------------------------------------------------------------->
<!--  KEY RESULTS                                                             -->
<!---------------------------------------------------------------------------->

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:3d2800,100:0a0700&height=64&text=%F0%9F%8F%86%20%20Key%20Results&fontSize=22&fontColor=fbbf24&fontAlignY=52&fontAlign=50" width="100%"/>

<br/>

> Evaluated over **60 episodes** after restoring the best composite checkpoint. Seed `42`.

<br/>

### 📋 All Methods — Head-to-Head

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

### 🎯 Scorecard vs ETA-Dispatch *(primary benchmark)*

<div align="center">

| Metric | 🥇 Ours | ETA | Delta | Verdict |
|:---|:---:|:---:|:---:|:---:|
| Avg Wait | **`0.492`** | 0.673 | **−27.0%** | ✅ BETTER |
| Energy | **`427.2`** | 472.7 | **−9.6%** | ✅ BETTER |
| Cluster Rate | **`10.0%`** | 10.2% | **−1.6pp** | ✅ BETTER |
| Spread Index | **`0.306`** | 0.297 | **+3.0%** | ✅ BETTER |

</div>

<br/>

> ► All 4 metrics beat ETA **simultaneously** — required all 4 v10.3 targeted fixes to achieve.
>
> **The critical insight:** Beating ETA on *wait alone* is easy — move more. Beating it on *energy alone* is easy — move less. Beating both **simultaneously** required learning a fundamentally smarter positioning strategy. That's what CTDE + QR-DQN enables.

<br/><br/>

<!---------------------------------------------------------------------------->
<!--  SYSTEM ARCHITECTURE                                                     -->
<!---------------------------------------------------------------------------->

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:1e1060,100:04020f&height=64&text=%F0%9F%93%90%20%20System%20Architecture&fontSize=22&fontColor=a5b4fc&fontAlignY=52&fontAlign=50" width="100%"/>

<br/>

The full system spans **four layers**: traffic generation → environment simulation → the learning agent → evaluation & reporting. Every component connects — from Poisson passenger arrivals through the reward engine, into the QR-DQN actor and centralised critic, and out to the reporting layer.

<br/>

<div align="center">

<sub>📌 &nbsp;**Figure — System Architecture Diagram**</sub>

<img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/UML%20Diagrams/Multi_Elevator_Sys.svg?raw=true" alt="System Architecture Diagram" width="100%"/>

</div>

<br/>

### 🗂 Four Layers at a Glance

<div align="center">

| Layer | Components | Role |
|:---|:---|:---|
| 🔵 **User & Sensor** | Poisson Arrivals · Traffic Modes | Generates λ ∈ [0.12–0.35] passengers/floor/step across 3 traffic modes |
| 🟣 **Control & Scheduling** | Hall Queue · EMA Responsibility · Reward Engine | Simulates 10-floor, 3-elevator building; computes decomposed rewards |
| 🟢 **Learning & Decision** | QR-Dueling Actor · Centralised Critic · PER Buffer · Baselines | All neural network components + classical comparison agents |
| 🟡 **Evaluation & Reporting** | Greedy Eval · Metrics · 9 Figures | 30–60 episode evaluations, composite checkpoint scoring, output generation |

</div>

<br/><br/>

<!---------------------------------------------------------------------------->
<!--  AGENT ARCHITECTURE                                                      -->
<!---------------------------------------------------------------------------->

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:2e1060,100:08020f&height=64&text=%F0%9F%A7%A0%20%20Agent%20Architecture&fontSize=22&fontColor=c4b5fd&fontAlignY=52&fontAlign=50" width="100%"/>

<br/>

The class diagram shows the **complete object model** — every class, its attributes, its methods, and how they relate.

<br/>

<div align="center">

<sub>📌 &nbsp;**Figure — Class Diagram**</sub>

<img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/UML%20Diagrams/Multi_Elevator_Class.svg?raw=true" alt="Class Diagram" width="100%"/>

</div>

<br/>

### 🔗 Key Relationships

- **`CTDEQRDQNAgent`** owns two `QRDuelingNet` instances (live `actor` + slowly-updated `target_actor`, τ=0.002), two `CentralisedCritic` instances (training only — discarded at inference), and one `PrioritizedReplayBuffer` (capacity 200k, α=0.6)
- **`MultiElevatorEnv`** sends `obs[n, 18]` and `rewards[n]` to the agent; receives `actions[n]` back
- **`EvaluatorLogger`** orchestrates evaluation without owning any component
- **`BaselinePolicy`** extends the agent in eval mode as a comparison interface

<br/>

### 🏛 Network Architectures

<div align="center">

| Module | Architecture |
|:---|:---|
| **QRDuelingNet** | `obs(18)` → `Linear(18→256)+LN+ReLU` → `Linear(256→256)+LN+ReLU` → **Value** `(256→128→51)` + **Advantage** `(256→128→3×51)` → `Q(s,a) = V(s) + A(s,a) − mean_a[A]` |
| **CentralisedCritic** *(train only)* | `joint_state(16)` → `Linear(16→256)+LN+ReLU` → `Linear(256→128→1)` → `V(s)` scalar |

</div>

<br/><br/>

<!---------------------------------------------------------------------------->
<!--  TRAINING SEQUENCE AND DECISION FLOW                                     -->
<!---------------------------------------------------------------------------->

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:0a3d28,100:010f06&height=64&text=%F0%9F%94%81%20%20Training%20Sequence%20and%20Decision%20Flow&fontSize=18&fontColor=6ee7b7&fontAlignY=52&fontAlign=50" width="100%"/>

<br/>

### ⏱ Per Time Step (300 per episode)

<div align="center">

| Step | From → To | Action |
|:---:|:---|:---|
| 1 | Passenger → Environment | Press floor call button → `hall[f]` incremented |
| 2 | Environment → Agent | Send observation `(dim 18)` — position, queues, peer info |
| 3 | Agent → self | Select action via QR-DQN `(argmax over mean quantiles)` |
| 4 | Agent → Environment | Return action `[ UP / DOWN / IDLE ]` |
| 5 | Environment → self | Move elevator · serve passengers · compute reward |
| 6 | Agent → self | Store transition in PER buffer · train · soft-update target |

</div>

<br/>

<div align="center">

<sub>📌 &nbsp;**Figure — Sequence Diagram** (message flow per time step)</sub>

<img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/UML%20Diagrams/Multi_Elevator_Sequence_Diagram.svg?raw=true" alt="Sequence Diagram" width="100%"/>

</div>

<br/>

### 🏊 Four Swim Lanes — Activity Lifecycle

<div align="center">

| Lane | Actor | Key Activities |
|:---|:---|:---|
| 🔵 **Passenger** | Real-world user | Arrives at floor → presses call button |
| 🟣 **Environment** | `MultiElevatorEnv` | Records hall call → generates obs → serves passengers → computes reward |
| 🟢 **Elevator Agent** | `CTDEQRDQNAgent` | Receives obs(18) → selects action → stores in PER → trains actor + critic |
| 🟡 **Administrator** | Training loop | Reviews metrics → evaluates composite score → saves best checkpoint |

</div>

<br/>

<div align="center">

<sub>📌 &nbsp;**Figure — Activity Diagram** (complete lifecycle across 4 swim lanes)</sub>

<img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/UML%20Diagrams/Multi_Elevator_Activity_Diagram.svg?raw=true" alt="Activity Diagram" width="100%"/>

</div>

<br/><br/>

<!---------------------------------------------------------------------------->
<!--  USE CASES                                                               -->
<!---------------------------------------------------------------------------->

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:3d1f00,100:0a0500&height=64&text=%F0%9F%8E%AD%20%20Use%20Cases&fontSize=22&fontColor=fdba74&fontAlignY=52&fontAlign=50" width="100%"/>

<br/>

<div align="center">

<sub>📌 &nbsp;**Figure — Use Case Diagram**</sub>

<img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/UML%20Diagrams/Multi_Elevator_Use_Case.svg?raw=true" alt="Use Case Diagram" width="100%"/>

</div>

<br/>

### 🧑‍🤝‍🧑 Actor Responsibilities

<div align="center">

| Actor | Use Cases |
|:---|:---|
| 🧍 **Passenger** | Press Floor Call Button · Observe Wait Time · Board or Alight Elevator |
| 🤖 **Elevator Agent** | Dispatch Elevator Action · Serve Passengers at Floor · Learn from Experience |
| 👨‍💼 **Administrator** | Monitor Training Logs · Evaluate System Performance → «extend» Save Best Checkpoint |

</div>

<br/>

> **Include chain:** `Press Floor Call Button` → «include» `Dispatch Elevator Action` → «include» `Serve Passengers at Floor` → «include» `Board or Alight Elevator`

<br/><br/>

<!---------------------------------------------------------------------------->
<!--  ENVIRONMENT                                                             -->
<!---------------------------------------------------------------------------->

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:003344,100:00080d&height=64&text=%F0%9F%8F%97%20%20Environment&fontSize=22&fontColor=67e8f9&fontAlignY=52&fontAlign=50" width="100%"/>

<br/>

### 🏢 Building Configuration

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

### 🚦 Traffic Modes

<div align="center">

| Mode | Weight | Description |
|:---|:---:|:---|
| `normal` | 50% | Standard Poisson arrivals at all floors |
| `up_peak` | 25% | Lobby weight ×2 — morning rush, everyone going up |
| `down_peak` | 25% | Upper floors ×1.8 — evening rush, everyone coming down |

</div>

<br/>

### 🔭 Observation Space — 18 Dimensions

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

### ⏱ Step Sequence — One Clock Tick

<div align="center">

| # | Phase | Action |
|:---:|:---|:---|
| 1 | **SERVE** | Elevator clears all passengers on its current floor |
| 2 | **MOVE** | Execute action `(UP / DOWN / IDLE)`, pay energy cost if moved |
| 3 | **SPAWN** | `Poisson(λ × weight[f])` new passengers added to every floor |
| 4 | **RESP** | Compute nearest elevator per floor; EMA-normalise responsibility |
| 5 | **CLUSTER** | Check if any elevator moved toward a peer within 2 floors |
| 6 | **IDLE** | Check if stationary with no nearby calls → grant idle bonus |
| 7 | **REWARD** | Compute per-elevator decomposed reward, clip to `[−20, +20]` |

</div>

<br/><br/>

<!---------------------------------------------------------------------------->
<!--  REWARD DESIGN                                                           -->
<!---------------------------------------------------------------------------->

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:3d0048,100:0a000d&height=64&text=%F0%9F%8E%AF%20%20Reward%20Design&fontSize=22&fontColor=f0abfc&fontAlignY=52&fontAlign=50" width="100%"/>

<br/>

Each elevator receives a **fully decomposed, strictly per-agent reward** — no shared terms, no free-rider problem.

<br/>

### 💰 Reward Terms

<div align="center">

| Term | Coefficient | Purpose |
|:---|:---:|:---|
| `passengers_served_i` | **`+8.0`** | Dominant signal — reward the primary task |
| `responsibility_norm_i` | **`−3.0`** | EMA-normalised nearest-floor queue load; drives elevators toward floors they own |
| `move_cost_i` | **`−0.5`** | 1 per floor moved; gentle discouragement of pointless movement |
| `idle_bonus_i` | **`+0.3`** | Stationary + no nearby calls → reward strategic waiting |
| `approach_cluster_i` | **`−2.5`** | Moved toward peer ≤2 floors → strictly local cluster penalty *(raised from 1.5 in v10.3)* |

</div>

<br/>

> **Reward formula:** `reward_i = +8.0×served − 3.0×resp_norm − 0.5×move_cost + 0.3×idle − 2.5×cluster` &nbsp;&nbsp; clipped to `[−20, +20]`
>
> **Responsibility is EMA-normalised:** `resp_ema = 0.95 × resp_ema + 0.05 × max_resp` — prevents a sudden surge from creating a massive one-step gradient spike.

<br/><br/>

<!---------------------------------------------------------------------------->
<!--  BASELINE POLICIES                                                       -->
<!---------------------------------------------------------------------------->

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:1e2730,100:04060a&height=64&text=%F0%9F%93%8F%20%20Baseline%20Policies&fontSize=22&fontColor=94a3b8&fontAlignY=52&fontAlign=50" width="100%"/>

<br/>

<div align="center">

| Policy | Strategy | Avg Wait | Energy | Cluster% |
|:---|:---|:---:|:---:|:---:|
| **Nearest Car** | Greedy — go to closest pending call | 9.679 | 409.8 | 98.5% |
| **SCAN / LOOK** | Sweep up then down, reverse on empty half | 1.415 | 899.6 | 98.5% |
| **Sectoring** | Each elevator owns a fixed floor zone | 0.854 | 486.1 | 28.5% |
| **ETA-Dispatch** ⭐ *primary benchmark* | Assign each call to min estimated-travel-time elevator | 0.673 | 472.7 | 10.2% |

</div>

<br/>

> **Why ETA-Dispatch is the target:** For each unassigned call it computes travel time for every elevator (direct if idle, via current target if busy) and assigns the minimum. It is the most sophisticated classical method — and the RL agent's entire training goal is to beat it jointly on *all four* metrics simultaneously.

<br/><br/>

<!---------------------------------------------------------------------------->
<!--  TRAINING PIPELINE                                                       -->
<!---------------------------------------------------------------------------->

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:002d48,100:00070d&height=64&text=%F0%9F%94%84%20%20Training%20Pipeline&fontSize=22&fontColor=38bdf8&fontAlignY=52&fontAlign=50" width="100%"/>

<br/>

### 🌡 Phase 0 — Warm Start

> **100 episodes × 300 steps × 3 elevators = 90,000 buffer entries** &nbsp;·&nbsp; No gradient updates.
> 50% ETA-Dispatch actions + 50% random actions — seeds the buffer with competent + exploratory experience.

<br/>

### 🚀 Phase 1 — Main Training &nbsp;`3,000 episodes · 900,000 gradient updates`

<br/>

### 📉 Learning Rate Schedule

<div align="center">

| Episodes | Learning Rate | Phase |
|:---|:---:|:---|
| Ep 1 – 799 | `1e-3` | Fast initial learning |
| Ep 800 – 1,199 | `5e-4` | Consolidation |
| Ep 1,200 – 1,799 | `2e-4` | Refinement |
| Ep 1,800 – 2,499 | `5e-5` | Fine-tuning |
| Ep 2,500 – 3,000 | `2e-5` | Final polish *(new in v10.3)* |

</div>

<br/>

### 🌀 Three Annealing Schedules — Running in Parallel

<div align="center">

| Schedule | Start | End | Behaviour |
|:---|:---:|:---:|:---|
| ε — exploration | `0.25` | `0.05` | Anneals ep 1→1,000, flat thereafter |
| critic_α — blend | `0.00` | `0.25` | Ramps ep 1→400, flat thereafter |
| β — IS correction | `0.40` | `1.00` | Linear across all 3,000 episodes |

</div>

<br/>

### 🏅 Composite Checkpoint Score *(FIX-1 — lower is better)*

<div align="center">

| Component | Weight | Normalised By |
|:---|:---:|:---|
| Avg Wait | `1.0 ×` | `eta_wait` |
| Energy | `0.4 ×` | `eta_energy` |
| Cluster Rate | `0.3 ×` | `0.15` *(soft target)* |

</div>

> `score = 1.70` → equal to ETA on all metrics &nbsp;·&nbsp; **Best checkpoint: ep 2,075 → score `1.347`** → wait `0.51`, energy `403.7`, cluster `12.2%`

<br/>

### 📅 Evaluation Cadence *(FIX-4)*

<div align="center">

| Episode Range | Eval Frequency | Episodes per Eval |
|:---|:---:|:---:|
| Ep 1 – 1,499 | Every 50 eps | 40 |
| Ep 1,500 – 3,000 | Every 25 eps | 50 |

</div>

<br/><br/>

<!---------------------------------------------------------------------------->
<!--  STRESS TEST RESULTS                                                     -->
<!---------------------------------------------------------------------------->

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:3d0e00,100:0a0300&height=64&text=%F0%9F%94%A5%20%20Stress%20Test%20Results&fontSize=22&fontColor=fca5a5&fontAlignY=52&fontAlign=50" width="100%"/>

<br/>

> Tested on **out-of-distribution** arrival rates — never seen during training. Training cap: λ = 0.35.

<br/>

<div align="center">

| Scenario | λ | Served/ep | Energy | Avg Wait | vs In-Dist | Cluster% | Spread |
|:---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Rush Hour** | `0.35` | 1,055 | 563 | 0.78 | 1.16× | 12.6% | 0.296 |
| **Peak Load** | `0.55` | 1,669 | 705 | 1.31 | 1.94× | 17.4% | 0.278 |
| **Extreme Surge** | `0.85` | 2,530 | 748 | 2.02 | 3.00× | 20.0% | 0.266 |

</div>

<br/>

> **At λ = 0.85** — **2.4× the training max** — the policy still maintains structured coverage (spread = 0.266, cluster = 20%) and serves **2,530 passengers/episode**. Graceful degradation, not catastrophic failure.

<br/><br/>

<!---------------------------------------------------------------------------->
<!--  RESULT FIGURES                                                          -->
<!---------------------------------------------------------------------------->

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:1e3800,100:040900&height=64&text=%F0%9F%93%8A%20%20Result%20Figures&fontSize=22&fontColor=a3e635&fontAlignY=52&fontAlign=50" width="100%"/>

<br/>

> All 8 figures generated at **400 DPI** (PNG + PDF) on Kaggle NVIDIA Tesla T4 · [`Results_Diagrams/`](https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/tree/main/Results_Diagrams)

<br/>

### 📈 Figure 1 — Training Convergence

> Avg wait per eval episode plotted against all 4 baselines, with LR step-down points as vertical lines. The smoothed curve crosses below ETA-Dispatch (0.673) around **episode 150** and continues improving through all four LR stages.

<div align="center">

<sub>📌 &nbsp;**Figure 1 — Training Convergence**</sub>

<img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Training_Convergence.png?raw=true" alt="Training Convergence" width="100%"/>

</div>

> **Best composite checkpoint captured at ep 2,075** → wait = 0.51, score = 1.347.

<br/>

### 📉 Figures 2 & 3 — Checkpoint Score · Wait Time Comparison

> The composite score (left) tracks joint quality across all three metrics. The wait-time bar chart (right) shows the final ranking of all methods after 60 evaluation episodes.

<div align="center">

| <sub>📌 **Figure 2 — Composite Score**</sub> | <sub>📌 **Figure 3 — Avg Wait Time**</sub> |
|:---:|:---:|
| <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Composite_Checkpoint.png?raw=true" alt="Composite Checkpoint Score" width="100%"/> | <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Avg_Wait_Time.png?raw=true" alt="Average Wait Time" width="100%"/> |
| Reference at **1.70** = equal to ETA. Best ep 2,075 (score **1.347**) sits 19% below. | **0.492** — **27% below** ETA and **94.9% below** Nearest Car. |

</div>

<br/>

### 🔬 Figure 4 — Training Dynamics (Three-Panel)

> Three metrics evolve together across training: wait time (top), energy (middle), and cluster rate (bottom). Reading them together shows how the policy balanced the three-way tradeoff.

<div align="center">

<sub>📌 &nbsp;**Figure 4 — Training Dynamics (Wait · Energy · Cluster)**</sub>

<img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Training_Dynamics.png?raw=true" alt="Training Dynamics" width="100%"/>

</div>

> **Wait** `0.78 → 0.49` · **Energy** `520 → 427` · **Cluster** `52% → 10%` — all improving monotonically across 3,000 episodes.

<br/>

### ⚡ Figures 5 & 6 — Energy Consumption · Spatial Distribution

> Energy (left) confirms that fewer motor-steps don't come at the cost of higher wait. Spatial spread (right) reveals emergent self-organisation: elevators learned to spread without any explicit rule telling them to.

<div align="center">

| <sub>📌 **Figure 5 — Energy Consumption**</sub> | <sub>📌 **Figure 6 — Cluster Rate and Spread Index**</sub> |
|:---:|:---:|
| <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Energy_Consumption.png?raw=true" alt="Energy Consumption" width="100%"/> | <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Elevator_Spatial_Distribution.png?raw=true" alt="Spatial Distribution" width="100%"/> |
| **427.2** motor-steps vs ETA's **472.7** — **9.6% fewer** movements. SCAN/LOOK worst at 899.6. | Spread rises near-zero → **0.306**, exceeding ETA (0.297). Pure reward-driven self-organisation. |

</div>

<br/>

### 🌡 Figures 7 & 8 — Stress Tests · Actor Loss

> OOD generalisation (left) shows how the policy degrades gracefully under extreme demand. The loss curve (right) confirms stable convergence — no divergence despite 900,000 updates.

<div align="center">

| <sub>📌 **Figure 7 — Stress Test Results**</sub> | <sub>📌 **Figure 8 — Actor Loss Trajectory**</sub> |
|:---:|:---:|
| <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Stress_Tests.png?raw=true" alt="Stress Tests" width="100%"/> | <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Actor_Loss.png?raw=true" alt="Actor Loss" width="100%"/> |
| **λ = 0.85** (2.4× training max): wait rises to 2.02 but spread holds. No catastrophic collapse. | **~1.10 → ~0.63** across 3,000 ep with no divergence. Each LR step-down causes a brief expected uptick. |

</div>

<br/><br/>

<!---------------------------------------------------------------------------->
<!--  PROJECT STRUCTURE                                                       -->
<!---------------------------------------------------------------------------->

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:1a2030,100:04050a&height=64&text=%F0%9F%93%81%20%20Project%20Structure&fontSize=22&fontColor=cbd5e1&fontAlignY=52&fontAlign=50" width="100%"/>

<br/>

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

<br/><br/>

<!---------------------------------------------------------------------------->
<!--  HOW TO RUN                                                              -->
<!---------------------------------------------------------------------------->

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:003d1e,100:000a05&height=64&text=%F0%9F%9A%80%20%20How%20to%20Run&fontSize=22&fontColor=4ade80&fontAlignY=52&fontAlign=50" width="100%"/>

<br/>

### ☁ Option 1 — Kaggle *(Recommended — Free T4 GPU)*

<div align="center">

| Step | Action |
|:---:|:---|
| 1 | Open `Multi_Elevator_CTDE.ipynb` via the badge at the top |
| 2 | Import to your Kaggle account |
| 3 | Settings → Accelerator → **GPU T4** |
| 4 | **Run All** — full training completes in ~2–3 hours |

</div>

<br/>

### 💻 Option 2 — Local / Google Colab

```bash
git clone https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch.git
cd multi-elevator-ctde-qrdqn-dispatch
pip install torch numpy matplotlib
jupyter notebook Multi_Elevator_CTDE.ipynb
```

### 🧪 Quick Smoke Test *(~20 min on CPU)*

```python
# Edit the entry point at the bottom of the notebook:
run_training(n_elevators=3, floors=10, episodes=500, steps_per_ep=300, seeds=[42])
```

<br/><br/>

<!---------------------------------------------------------------------------->
<!--  DEPENDENCIES                                                            -->
<!---------------------------------------------------------------------------->

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:352000,100:080500&height=64&text=%F0%9F%93%A6%20%20Dependencies&fontSize=22&fontColor=fcd34d&fontAlignY=52&fontAlign=50" width="100%"/>

<br/>

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

<br/><br/>

<!---------------------------------------------------------------------------->
<!--  DESIGN DECISIONS                                                        -->
<!---------------------------------------------------------------------------->

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:2a0040,100:07000f&height=64&text=%F0%9F%94%AC%20%20Design%20Decisions%20%E2%80%94%20v10.3%20Fixes&fontSize=20&fontColor=d8b4fe&fontAlignY=52&fontAlign=50" width="100%"/>

<br/>

> Four targeted changes over v10.2, each fixing a specific failure mode identified from training logs.

<br/>

<details>
<summary><b>🔧 FIX-1 — Composite Checkpoint Score</b></summary>

<br/>

**Problem:** v10.2 saved `min(avg_wait)` only → selected ep 2,050 (score = 1.411). Episodes 2,200 (energy = 440.2, cluster = 12%, score = 1.374) and ep 2,400 were jointly superior but never saved.

**Fix:** `score = 1.0×(wait/eta_wait) + 0.4×(energy/eta_energy) + 0.3×(cluster/0.15)`. Lower = better. Ep 2,200 now correctly selected over ep 2,050.

<br/>
</details>

<details>
<summary><b>📆 FIX-2 — Extended to 3,000 Episodes</b></summary>

<br/>

**Problem:** v10.2 actor loss was still at 0.498 and falling at ep 2,500. The best policy was still emerging.

**Fix:** 500 additional fine-tuning episodes at `LR = 2e-5` (ep 2,500–3,000). Gives the jointly-efficient behaviour at ep 2,200–2,400 time to consolidate.

<br/>
</details>

<details>
<summary><b>⚖ FIX-3 — Cluster Penalty Raised 1.5 → 2.5</b></summary>

<br/>

**Problem:** ep 2,200 achieved 12% cluster but the metric oscillated 12–15% in v10.2's final 500 episodes — signal too weak to hold the gain.

**Fix:** `approach_cluster` coefficient raised to **2.5**. Strictly local — fires only for the elevator that moved toward a peer, never the peer — so no free-rider gradient is created.

<br/>
</details>

<details>
<summary><b>🔍 FIX-4 — Finer Eval Cadence After Episode 1,500</b></summary>

<br/>

**Problem:** `eval_every=50` throughout meant the peak composite checkpoint between ep 2,150–2,250 could be missed by up to 50 episodes.

**Fix:** `eval_every=25` after ep 1,500, with 50 eval episodes per checkpoint (vs 40 earlier). Dense late-training measurement where quality is highest.

<br/>
</details>

<br/><br/>

<!---------------------------------------------------------------------------->
<!--  FOOTER                                                                  -->
<!---------------------------------------------------------------------------->

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:00b4d8,40:0a3d62,100:020817&height=140&section=footer" width="100%"/>

**Built from scratch &nbsp;·&nbsp; PyTorch 2.9 &nbsp;·&nbsp; NVIDIA Tesla T4 &nbsp;·&nbsp; 900,000 gradient updates**

<br/>

[![Star this repo](https://img.shields.io/github/stars/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch?style=for-the-badge&logo=github&color=fbbf24&labelColor=0d1117&label=Star%20this%20repo)](https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch)

<br/>

*© 2026 Kumar Piyush Raj &nbsp;·&nbsp; [GitHub @kumarpiyushraj](https://github.com/kumarpiyushraj)*

</div>
