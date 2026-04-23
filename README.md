# AI Agents in Spades

A research project implementing and benchmarking multiple AI agent strategies for the card game **Spades** — a 4-player trick-taking game with incomplete information, cooperative bidding, and adversarial play. Built as part of an undergraduate AI research course at Southwestern University.

The game engine is designed to be **game-agnostic**: the `Rules` interface in `util.py` can be implemented for other card games with minimal changes.

## Agents Implemented

| Agent | File | Algorithm |
|-------|------|-----------|
| Simple heuristic | `players.py` | Rule-based bidding and play |
| Expectiminimax | `expectiminimax.py` | Expectiminimax search with probability estimation |
| Monte Carlo Tree Search | `MCTS.py` | MCTS with UCB1 selection |
| Reinforcement Learning | `RL.py` + `SIMPLE/` | Self-play RL via the SIMPLE framework (Docker) |

Probability estimates for unseen cards are precomputed and stored in `probabilities.json` via `probabilities.py`.

## Results

Win rates and score distributions across 1,000+ simulated games comparing all agent types:

![Win Rates](SIMPLE/app/output/winrates.png)
![Scores](SIMPLE/app/output/scores.png)

Full result data available in [`/SIMPLE/app/output/`](SIMPLE/app/output/).

## Setup

### Requirements

- Python 3.8+
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)

### Install & Run (RL Agent)

```bash
cd SIMPLE
docker-compose up -d
bash scripts/install_env.sh spades
```

**Train** the RL agent (N = number of parallel sessions):

```bash
docker-compose exec app mpirun -np N python3 train.py -e spades
```

**Play** a set of games between trained models:

```bash
# 4-player game
docker-compose exec app python3 play.py -a [model1] [model2] [model3] [model4] -g [NumGames]

# 2-player variant
docker-compose exec app python3 play.py -a [model1] [model2] -g [NumGames]
```

`-g` is optional and defaults to 1 game.

### Run Without Docker (Heuristic / MCTS / Expectiminimax)

```bash
python play.py -a heuristic mcts -g 100
```

## Project Structure

```
├── util.py              # Game engine: Game class, Rules interface, Card/Player abstractions
├── spades.py            # Spades-specific Rules implementation
├── players.py           # Heuristic agent (basePlayer)
├── expectiminimax.py    # Expectiminimax agent
├── MCTS.py              # Monte Carlo Tree Search agent
├── RL.py                # RL agent wrapper
├── probabilities.py     # Card probability estimator
├── probabilities.json   # Precomputed probability tables
├── play.py              # Game runner / evaluation harness
├── plotGenerator.py     # Results visualization
└── SIMPLE/              # RL training environment (Docker)
```

## Key Design Decisions

- **Imperfect information handling**: Expectiminimax and MCTS both use a probability model over unseen cards rather than assuming perfect information, making them applicable to real incomplete-information play.
- **Extensibility**: The `Rules` interface abstracts game-specific logic so the same agents can be tested on other trick-taking games (Hearts, Euchre, etc.) by implementing a new `Rules` subclass.
- **Benchmarking harness**: `play.py` supports arbitrary agent combinations and game counts, enabling systematic comparison across all agent types.

## References

- [Bidding Algorithm (ECAI 2020)](https://ecai2020.eu/papers/235_paper.pdf)
- [Monte Carlo Tree Search overview](https://ai-boson.github.io/mcts/)
- [RL on Spades (CS229)](https://cs229.stanford.edu/proj2021spr/report2/81999416.pdf)
- [SIMPLE RL Framework](https://github.com/davidADSP/SIMPLE)
- [RL Agent for Hearts (reference)](https://fse.studenttheses.ub.rug.nl/15440/1/Bachelor_Thesis_-_Maxiem_Wagen_1.pdf)

## Skills Demonstrated

- Adversarial search under uncertainty (expectiminimax, MCTS)
- Reinforcement learning with self-play (SIMPLE / MPI-based training)
- Probability estimation and Bayesian inference for game state
- Modular, interface-driven software design in Python
- Quantitative evaluation and visualization of AI agent performance

## Authors

Built collaboratively as part of an undergraduate AI research project at Southwestern University (Spring 2024).
