# Reinforcement Learning progress and results
## Comparison table
| Model | Acc | Rec (Macro-avg) | Prec (Macro-avg) | F1 (Macro-avg) | Details |
| - | - | - | - | - | - |
| Seq (ANN) | 81.7% | 48.3% | 76.4% | 49.1% | 5 hidden dense layers, vanilla |
| Seq (Ann) | 67.8% | 59.7% | 47.9% | 43.1% | 5 hidden dense layers, with class weighted training |
| PPO (RL) | 72.8% | 52.2% | 44.8% | 44.7% | 14M steps, biased reward to minorities |
| PPO (RL) | 73.1% | 52.8% | 45.3% | 45.7% | 29.8M steps, biased reward to minorities |
| DQN | 77.0% | 57.5% | 52.9% | 52.6% | Work in progress. 32M steps, biased rewards to very reduced minorities |

