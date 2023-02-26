# Contenidos
## Comparación
| Model | Acc | Rec (Macro-avg) | Prec (Macro-avg) | F1 (Macro-avg) | Details |
| - | - | - | - | - | - |
| Seq (reference) | 81.7% | 48.3% | 76.4% | 49.1% | 5 hidden dense layers, vanilla |
| Seq (reference) | 67.8% | 59.7% | 47.9% | 43.1% | 5 hidden dense layers, with class weighted training |
| PPO | 73.1% | 52.8% | 45.3% | 45.7% | 29.8M steps, biased reward to minorities |
| DQN | 77.0% | 57.5% | 52.9% | 52.6% | 32M steps, biased rewards to very reduced minorities |
| Multiprocess DQN with custom policy network - WIP| 75.7% | -% | -% | 51.1% | 2.7M steps, 8 parallel environments, superb sample efficiency and wall clock time |

## Proyecto y objetivo

Diseño de un modelo de IA explicativa basado en aprendizaje por refuerzo aplicado al dataset UNSW-NB15

El trabajo tiene como objetivo el diseño del modelo indicado en el título a un nivel básico, sin realizar un estudio exhaustivo sobre los parámetros implicados que
podrían mejorar los resultados del modelo y bajo un enfoque de clasificación binaria. Se indicará el procedimiento completo realizado, así como las decisiones tomadas y los resultados obtenidos. Por último, se realizará una comparación respecto a otros modelos bajo circunstancias similares (modificación mínima o nula de parámetros) en cuanto a métricas obtenidas, así como
un análisis comparativo sobre la naturaleza explicativa implícita a cada modelo. También se incluirá en la comparativa trabajos relacionados al respecto.

Los modelos que se han trabajado son los siguientes, a los cuales se ha aplicado un factor de pesos de clases basado en el desequilibrio de estos en el split de entrenamiento:

| Familia/metodología del modelo | Modelo | Framework | Características |
| - | - | - | - |
| Basado en árboles | Decision Tree | Scikit-learn | Por defecto |
| Ensamblado | Random Forest | Scikit-learn | Por defecto |
| Boosting de ensamblado de árboles | XGBoost | XGBoost | Por defecto |
| Perceptrón multicapa | Sequential Dense Layers | Keras (Tensorflow) | Doble capa de 64 unidades |
| Aprendizaje por refuerzo | Proximal Policy Optimization | Stable-Baselines3 (PyTorch) | Doble capa de 64 unidades para actor y crítico |

## Comparación de métricas obtenidas de los modelos
Los resultados se han obtenido utilizando ```average='Binary'```.

| Model | Accuracy | Recall | Precision | F1 |
| - | - | - | - | - | - |
| Decision Tree | 97.71% | 95.99% | 92.11% | 94.01% |
| Random Forest | 98.30% | 98.07% | 96.41% | 97.23% |
| XGBoost | 98.73% | 98.26% | 95.16% | 96.68% |
| Sequential Dense Layers | 98.17% | 99.98% | 91.43% | 95.51% |
| Proximal Policy Optimization | 97.79% | 99.96% | 89.84% | 94.63% |

![](./Images/DT.png)

![](./Images/RF.png)

![](./Images/XGB.png)

![](./Images/MLP.png)

![](./Images/PPO.png)












