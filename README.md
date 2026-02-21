```markdown
#  Replicating MC-DML: Memory-Guided MCTS for Text-Based Games

This repository contains the replication of **"Monte Carlo Planning with Large Language Model for Text-Based Game Agents"** (arXiv:2504.16855). 

We implemented both the Baseline MCTS approach and the proposed MC-DML (Dynamic Memory-Guided) approach from scratch using a local SQLite database for all state management.

##  Project Highlights
* **Strict Data Management:** No intermediate Python dictionaries or JSON files. The entire MCTS tree and LLM reflections are managed purely via an **SQLite relational database** (`sqlite3`).
* **Minimal Dependencies:** Built using only `openai`, `jericho`, and `pandas`.
* **Scaled Evaluation:** Expanded the replication from the requested 9 games to a full **50-game batch evaluation**.

##  Architecture

The MC-DML algorithm addresses the fact that traditional MCTS lacks language understanding. 
1. **Selection:** Uses the **UCB1 algorithm** to balance exploration and exploitation.
2. **Expansion:** LLM (`gpt-4o-mini`) generates potential actions based on the current state.
3. **Dynamic Memory:** Upon failure, the LLM reflects on the trajectory. This reflection is saved to SQLite and injected into future planning phases to prune the search tree.

---

##  Getting Started

### 1. Installation
```bash
pip install -r requirements.txt

```

### 2. Set API Key

```bash
export OPENAI_API_KEY='your-api-key-here'

```

### 3. Usage

If using the provided `.ipynb` file in Google Colab:

1. Upload the notebook to Colab.
2. Add your OpenAI API key to the **Secrets** (key icon) tab with the name `openai` and enable **Notebook access**.
3. Run all cells.

##  Results Summary

Our replication confirmed that while the baseline gets stuck in repetitive loops, the **MC-DML approach** uses cross-trial reflections to pivot toward investigative actions.

* **Highest Avg Value:** *Ztuu* (0.83), *Zork II* (0.77).
* **Baseline Vulnerability:** 100% of visits frequently funneled into a single non-productive action without memory.
