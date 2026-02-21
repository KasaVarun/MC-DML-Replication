DELIVERABLE 3: GITHUB README.MD
Markdown
# Replicating MC-DML: Memory-Guided MCTS for Text-Based Games

This repository contains the code and evaluation data for our replication of the paper **"Monte Carlo Planning with Large Language Model for Text-Based Game Agents"** (arXiv:2504.16855). 

We implemented both the Baseline MCTS approach and the proposed MC-DML (Dynamic Memory-Guided) approach from scratch to solve text-based interactive fiction games using the Jericho benchmark.

## 🎯 Project Objectives & Constraints
This project was built to satisfy specific architectural constraints:
1. **Strict Data Management:** No intermediate Python dictionaries or JSON files were used to manage the MCTS tree. The entire search tree (nodes, parent-child relationships, visits, values) and the LLM's cross-trial memory reflections are managed purely via an **SQLite relational database** (`sqlite3`).
2. **Minimal Dependencies:** The environment relies solely on `openai` (for the reasoning engine), `jericho` (for the game environments), and `pandas` (for data visualization). No bloated reinforcement learning frameworks (like Ray/RLlib) were used.
3. **Scaled Evaluation:** We scaled the replication from the requested 9 games to a full **50-game batch evaluation**.

## ⚙️ Architecture
* **Reasoning Engine:** OpenAI API (`gpt-4o-mini`) acts as both the policy network (generating text-based actions) and the value network (evaluating state progression).
* **Tree Search:** Utilizes the **UCB1 algorithm** to balance exploration and exploitation during the selection phase.
* **Dynamic Memory (MC-DML):** Upon trial failure, the LLM generates a reflection which is saved to the SQLite `memory` table. During subsequent MCTS planning phases, this memory is injected into the LLM's prompt to dynamically prune the search tree and prevent repetitive action loops.

---

##  Installation & Setup

**1. Clone the repository**
```bash
git clone [https://github.com/YOUR-USERNAME/MC-DML-Replication.git](https://github.com/YOUR-USERNAME/MC-DML-Replication.git)
cd MC-DML-Replication
2. Install dependencies

Bash
pip install openai jericho pandas
3. Set your OpenAI API Key
Ensure your environment has access to your API key before running the scripts:

Bash
export OPENAI_API_KEY="your-api-key-here"
4. Download the Game ROMs
The scripts automatically pull the official Jericho Z-machine suite from GitHub. No manual ROM downloads are required.

 Summary of Results
Our replication successfully validated the core claims of the paper:

Baseline Vulnerability: Standard UCB1 exploration blindly exploited generic actions (e.g., repeatedly trying to "go north" through a locked door) without semantic understanding.

MC-DML Success: Cross-trial memory allowed the agent to dynamically adjust its action generation (e.g., proposing "examine mailbox" instead of "go north"), fundamentally improving exploration during the planning phase.

Scale: The agent performed exceptionally well in environments with clear semantic logic (e.g., Ztuu: 0.83, Zork II: 0.77) but struggled in environments with severe partial observability (e.g., LGOP: 0.00).
