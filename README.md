````markdown
# Replicating MC-DML: Memory-Guided MCTS for Text-Based Games

This repository contains the code and evaluation data for our replication of the paper  
**"Monte Carlo Planning with Large Language Model for Text-Based Game Agents" (arXiv:2504.16855)**.

We implemented both the **Baseline MCTS** approach and the proposed **MC-DML (Dynamic Memory-Guided)** approach from scratch to solve text-based interactive fiction games using the **Jericho benchmark**.

---

##  Project Objectives & Constraints

This project was built to satisfy specific architectural constraints:

1. **Strict Data Management**  
   No intermediate Python dictionaries or JSON files were used to manage the MCTS tree.  
   The entire search tree (nodes, parent-child relationships, visits, values) and the LLM’s cross-trial memory reflections are managed purely via an **SQLite relational database (`sqlite3`)**.

2. **Minimal Dependencies**  
   The environment relies only on:
   - `openai` → reasoning engine  
   - `jericho` → game environments  
   - `pandas` → data visualization  

   No reinforcement learning frameworks (e.g., Ray/RLlib) were used.

3. **Scaled Evaluation**  
   The replication was extended from the requested **9 games to a full 50-game batch evaluation**.

---

##  Architecture

- **Reasoning Engine:**  
  OpenAI API (`gpt-4o-mini`) acts as both:
  - Policy network → generates text actions  
  - Value network → evaluates state progression  

- **Tree Search:**  
  Uses the **UCB1 algorithm** to balance exploration and exploitation during selection.

- **Dynamic Memory (MC-DML):**  
  After trial failure, the LLM generates a reflection stored in the SQLite **`memory` table**.  
  During later planning phases, this memory is injected into prompts to prune the search tree and avoid repeated mistakes.

---

## 🛠 Installation & Setup

### 1️⃣ Clone the repository
```bash
git clone https://github.com/YOUR-USERNAME/MC-DML-Replication.git
cd MC-DML-Replication
````

### 2️⃣ Install dependencies

```bash
pip install openai jericho pandas
```

### 3️⃣ Set your OpenAI API key

```bash
export OPENAI_API_KEY="your-api-key-here"
```

### 4️⃣ Download the game ROMs

The scripts automatically pull the official Jericho Z-machine suite from GitHub.
No manual ROM downloads are required.

---

##  Summary of Results

Our replication successfully validated the core claims of the paper:

###  Baseline Vulnerability

Standard UCB1 exploration blindly exploited generic actions
(e.g., repeatedly trying to **"go north"** through a locked door) without semantic reasoning.

###  MC-DML Success

Cross-trial memory enabled the agent to dynamically adjust action generation
(e.g., proposing **"examine mailbox"** instead of **"go north"**), improving exploration efficiency.

###  Scale Findings

* Strong performance in semantically structured environments

  * **Ztuu:** 0.83
  * **Zork II:** 0.77

* Weak performance in highly partially observable environments

  * **LGOP:** 0.00

---

##  Repository Structure

```
├── src/
│   ├── mcts_baseline.py
│   ├── mc_dml_agent.py
│   ├── database.py
│   └── evaluation.py
├── data/
│   └── results.csv
├── notebooks/
│   └── analysis.ipynb
├── README.md
└── requirements.txt
```

---

