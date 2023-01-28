# Contenidos
## Comparación
| Model | Acc | Rec (Macro-avg) | Prec (Macro-avg) | F1 (Macro-avg) | Details |
| - | - | - | - | - | - |
| Seq (reference) | 81.7% | 48.3% | 76.4% | 49.1% | 5 hidden dense layers, vanilla |
| Seq (reference) | 67.8% | 59.7% | 47.9% | 43.1% | 5 hidden dense layers, with class weighted training |
| PPO | 73.1% | 52.8% | 45.3% | 45.7% | 29.8M steps, biased reward to minorities |
| DQN | 77.0% | 57.5% | 52.9% | 52.6% | 32M steps, biased rewards to very reduced minorities |
| Multiprocess DQN with custom policy network - WIP| 75.7% | -% | -% | 51.1% | 2.7M steps, 8 parallel environments, superb sample efficiency and wall clock time |

## Proyecto/objetivo

Diseño de un sistema de inteligencia artificial explicativa para una Plataforma de Conciencia Cibersituacional basado en modelos de aprendizaje automático por refuerzo como: Explainable Reinforcement Learning.

## Framework y dataset
El framework utilizado para elegir y diseñar modelos de Reinforcement Learning (RL), es [Stable-Baselines3](https://stable-baselines3.readthedocs.io/en/master/), que ofrece implementaciones mejoradas de algoritmos de RL basados en la librería original de OpenAI, Baselines, usando PyTorch.

El dataset elegido es UNSW_NB15.

## Primera aproximación
En una primera aproximación al problema se han usado los subdatasets `UNSW_NB15_training-set.csv` y `UNSW_NB15_training-set.csv`, que incluyen una colección de registros de los dataset originales con modificaciones (eliminación de puertos origen/destino, direcciones IP origen/destino, eliminación de NaN, etc). Esto resulta en un dataset de forma (257_673, 43)

Se ha elegido usar como target el atributo `attack_cat`, convirtiendo el problema en una clasificación categórica con 10 clases, con las siguientes proporciones al concatenar ambos subsets:

| Nombre | Proporcion |
| - | - |
| Normal | 36.09 % |
| Generic | 22.85% |
| Exploits | 17.28% | 
| Fuzzers | 9.41% |
| DoS | 6.45% |
| Reconnaisasance | 5.43% |
| Analysis | 1.04% |
| Backdoor | 0.9% |
| Shellcode | 0.59% |
| Worms | 0.07% |

Para el preproecesamiento de datos se ha utilizado una adaptación multiclase realizada por [Carl Kirstein](https://www.kaggle.com/code/carlkirstein/unsw-nb15-modelling-97-7) en este mismo dataset.

En cuanto a RL, primero se hubo de definir el entorno. Las características técnicas de este serán:
- `action_space = Discrete(10)` Dado que se eligen 10 clases
- `observation_space = Box(shape=43, )` Puesto que se observa un único registro por paso

Se adapta el algoritmo propuesto por [Enlu Lin et. al](https://arxiv.org/abs/1901.01379), donde se ataca tanto el uso de RL para labores clasificación, como para utilizar el sistema de recompensas para paliar el desequilibrio del dataset en función de su proporción. Omitiendo detalles, la clave del trabajo es que se usa Deep Q Network y los episodios de entrenamiento son variables dado que funcionan de la siguiente manera:
- Se recorre el dataset de forma ordenada
- A menos que se falle la categoría de un registro que pertenezca a una clase minoritaria, el episodio continúa
- Se asigna X(i) de recompensa al agente al acertar y -X(i) al fallar, siendo X(i) el valor de recompensa correspondiente a la clase calculado con anterioridad

Los algoritmos de Stable-Baselines3 que admiten las características de espacio descritas son: DQN, A2C y PPO. Dado que PPO considera aspectos de A2C, se descarta la prueba de A2C. Los otros dos muestran resultados similares al entrenar en un split 3:1 tras más de 25M de pasos, sin alcanzar valores que los modelos de referencia conseguían (DNN con Keras y RFC).

Un cambio en el algoritmo, por el cual se establece que el episodio tiene una duración fija de 1, es decir, el episodio termina cuando se hace una evaluación, ya sea correcta o incorrecta, resulta en una mejora general en las métricas, además de un mejor tiempo de reloj y eficiencia por muestra. Sin embargo, se ha observado que esto causa que las recompensas carezcan de relevancia en el entrenamiento, dando resultados similares a los que daría una DNN (sin tener en cuenta pesos de clase). Esto implica que las clases mayoritarias consiguen considerables mejoras en métricas que las minoritarias.

Si por el contrario se toma el extremo contrario y se establece una duración de episodio fija del tamaño del dataset, se consiguen mejores resultados en métricas como F1, recall y precision a escala macro durante el entrenamiento (respecto al caso anterior), pero se sacrifica accuracy. En este caso, además, el entrenamiento es considerablemente más lento (en cuanto a tiempo de reloj y eficiencia por muestra). Utilizando entornos paralelizados (multiprocesamiento) mejora ambas características al utilizar el entorno descrito.

Una prueba donde inicializó cada entorno en una posición distinta del dataset o a barajar el dataset para cada entorno demostró resultados mucho peores tras el mismo número de pasos.

Tras una mayor indagación en el funcionamiento de los algoritmos de RL, determiné que la causa de este comportamiento era el dataset. Dado que los algoritmos de RL están diseñados para trabajar en entornos modelados como procesos de Markov y estableciendo relaciones entre transiciones y estados (en nuestro caso cada registro), usar registros puntuales en desorden del dataset original era una aproximación incorrecta.

## Segunda aproximación
En este caso, siguiendo el razonamiento que hace [SubrataMaji](https://github.com/SubrataMaji/IDS-UNSW-NB15/blob/master/1.%20Dataset/About%20Dataset%20and%20It's%20Cleaning.ipynb), se trabaja sobre los datos en crudo con los datasets `UNSWNB15_i.csv`, con aproximadamente 2.5M de registros ordenados temporalmente, realizando también el respectivo preprocesamiento.

Si bien esta aproximación, en la que se interpreta el conjunto como una serie temporal, resulta correcta en teoría, la realidad es que trabajar sobre estos datos supone una carga que ni entornos como Google colab pueden soportar (además del tamaño inmenso de los datos, algoritmos como DQN utilizan un buffer de replay y la propia Q-table). Es posible reducir esta carga al sustituir el problema por uno de clasificación binaria, utilizando 1 de los 5 datasets completos y reduciendo el número de atributos usando por ejemplo SelectKBest. Aun así, sigue siendo un proceso notablemente lento y peligra que en los splits de test haya clases no presentes.

En un escenario ideal se utilizaría este dataset completo junto a un algoritmo como RecurrentPPO, de la sublibrería de sb3-contrib, que permite el uso de políticas recurrentes como LSTM, que es ideal para este tipo de tratamiento.









