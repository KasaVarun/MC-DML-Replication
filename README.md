# Replicating MC-DML: Memory-Guided MCTS for Text-Based Games

This repository contains the code and evaluation data for our replication of the paper
"Monte Carlo Planning with Large Language Model for Text-Based Game Agents" (arXiv:2504.16855).

We implemented both the Baseline MCTS approach and the proposed MC-DML (Dynamic Memory-Guided) approach from scratch to solve text-based interactive fiction games using the Jericho benchmark.

---

## Project Objectives and Constraints

This project was built to satisfy specific architectural constraints:

### 1. Strict Data Management
No intermediate Python dictionaries or JSON files were used to manage the MCTS tree.
The entire search tree (nodes, parent-child relationships, visits, values) and the LLM’s cross-trial memory reflections are managed purely via an SQLite relational database (sqlite3).

### 2. Minimal Dependencies
The environment relies only on:

- openai for the reasoning engine
- jericho for the game environments
- pandas for data visualization

No reinforcement learning frameworks (e.g., Ray or RLlib) were used.

### 3. Scaled Evaluation
The replication was extended from the requested 9 games to a full 50-game batch evaluation.

---

## Architecture

### Reasoning Engine
OpenAI API (gpt-4o-mini) acts as both:
- Policy network that generates text actions
- Value network that evaluates state progression

### Tree Search
Uses the UCB1 algorithm to balance exploration and exploitation during selection.

### Dynamic Memory (MC-DML)
After trial failure, the LLM generates a reflection stored in the SQLite memory table.
During later planning phases, this memory is injected into prompts to prune the search tree and avoid repeated mistakes.

---

## Installation and Setup

### 1. Clone the repository
git clone https://github.com/KasaVarun/MC-DML-Replication.git
cd MC-DML-Replication

### 2. Install dependencies
pip install openai jericho pandas

### 3. Set your OpenAI API key

Mac or Linux:
export OPENAI_API_KEY="your-api-key-here"

Windows PowerShell:
setx OPENAI_API_KEY "your-api-key-here"

### 4. Download the game ROMs
The scripts automatically pull the official Jericho Z-machine suite from GitHub.
No manual ROM downloads are required.

---

## Summary of Results

Our replication successfully validated the core claims of the paper.

Baseline behavior:
Standard UCB1 exploration repeatedly exploited generic actions such as trying to go north through a locked door without semantic reasoning.

MC-DML behavior:
Cross-trial memory enabled the agent to dynamically adjust action generation, for example proposing examine mailbox instead of go north, improving exploration efficiency.

Scale findings:
Strong performance in semantically structured environments such as Ztuu (0.83) and Zork II (0.77).
Weak performance in highly partially observable environments such as LGOP (0.00).

---

## Repository Structure

src/
  mcts_baseline.py
  mc_dml_agent.py
  database.py
  evaluation.py

data/
  results.csv

notebooks/
  analysis.ipynb

README.md
requirements.txt

---

## Future Improvements

- Add retrieval-augmented memory instead of static reflections
- Experiment with stronger reasoning models
- Parallelize MCTS rollouts for faster planning
- Add visualization dashboard for tree exploration
