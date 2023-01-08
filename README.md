# Reinforcement Learning progress and results
## Comparison table
| Model | Acc | Rec (Macro-avg) | Prec (Macro-avg) | F1 (Macro-avg) | Details |
| - | - | - | - | - | - |
| Seq (reference) | 81.7% | 48.3% | 76.4% | 49.1% | 5 hidden dense layers, vanilla |
| Seq (reference) | 67.8% | 59.7% | 47.9% | 43.1% | 5 hidden dense layers, with class weighted training |
| PPO | 73.1% | 52.8% | 45.3% | 45.7% | 29.8M steps, biased reward to minorities |
| DQN | 77.0% | 57.5% | 52.9% | 52.6% | 32M steps, biased rewards to very reduced minorities |
| PPO (Multiprocessing) | -% | -% | -% | -% | Work in progress. 8 parallel environments, aprox. x3 steps per second than normal PPO |
