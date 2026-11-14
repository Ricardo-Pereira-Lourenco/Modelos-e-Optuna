# Tiamat: Detecção de Fraudes em Cartões de Crédito

Este repositório contém um notebook Jupyter que compara diferentes algoritmos de aprendizado de máquina na detecção de fraudes em transações de cartão de crédito, com hiperparâmetros otimizados pelo Optuna e avaliação por validação cruzada.

## Contexto

Fraudes são uma fração minúscula das transações (cerca de 0,17%), então a acurácia é uma métrica enganosa: um modelo que classifica tudo como "normal" já acerta quase tudo sem detectar nenhuma fraude. Além disso, bloquear uma transação legítima (falso positivo) também é custoso. Por isso o trabalho usa a **PR-AUC** para selecionar modelos e o **F0,5** para ajustar o limiar de decisão, priorizando a precisão.

<img width="690" height="490" alt="0229e332-8123-4275-98df-7ad4c01053b0" src="https://github.com/user-attachments/assets/56f22e39-eccb-44fb-93b2-384c7067aa7e" />

## Conjunto de dados

[Credit Card Fraud Detection](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud) (`creditcard.csv`): 284.807 transações de clientes europeus, rotuladas como normais (`0`) ou fraudulentas (`1`). Os atributos `V1`–`V28` são componentes principais anonimizados (PCA), e o alvo é a coluna `Class`.

> O arquivo `creditcard.csv` não está incluído no repositório. Baixe-o no Kaggle e coloque-o na mesma pasta do notebook.

## Abordagem

- **Separação dos dados:** ~10% para teste com `StratifiedGroupKFold`. Linhas quase idênticas são agrupadas para não aparecerem ao mesmo tempo em treino e teste.
- **Baseline:** `DummyClassifier`.
- **Algoritmos:** Regressão Logística, KNN, Random Forest, Hist. Gradient Boosting e MLP.
- **Validação cruzada:** 5 folds estratificados e agrupados, idênticos para todos os modelos.
- **Otimização:** 100 tentativas do Optuna por algoritmo, ajustando os hiperparâmetros do modelo, o grau de subamostragem da classe majoritária e a redução de dimensionalidade (nenhuma, PCA ou `SelectKBest`).
- **Pipeline (sem vazamento):** padronização → subamostragem → redução de dimensionalidade → modelo.
- **Avaliação final:** cada melhor pipeline é avaliado uma única vez no teste. O limiar do melhor modelo é escolhido apenas com previsões fora da amostra da validação cruzada.


## Principais resultados

| Algoritmo | PR-AUC (CV) | PR-AUC (teste) |
|---|---|---|
| Baseline (Dummy) | 0,0018 | 0,0015 |
| Regressão Logística | 0,768 | 0,749 |
| KNN | 0,835 | 0,808 |
| Random Forest | 0,838 | 0,842 |
| **Hist. Gradient Boosting** | **0,844** | 0,840 |
| MLP | 0,841 | 0,786 |

<img width="1588" height="990" alt="b944c7ad-e9d8-418d-9a2c-536919daf68a" src="https://github.com/user-attachments/assets/2ff995f5-cae9-45ea-bb67-a01c37d4a2df" />

O **Hist. Gradient Boosting** foi escolhido pela PR-AUC na validação cruzada. Com o limiar ajustado pelo F0,5 (0,883), obteve no teste:

- Precisão: **0,971**
- Recall: **0,767**
- F0,5: **0,922**

<img width="566" height="455" alt="ffa3cd5f-ab6a-4352-a720-91fa03590a0e" src="https://github.com/user-attachments/assets/45ddc5d6-ce32-4880-b0ae-9a3eeb32f11c" />

## Principais aprendizados

- A subamostragem tratada como hiperparâmetro funcionou bem e acelerou o treino.
- PCA e seleção de atributos não trouxeram ganho consistente, já que `V1`–`V28` já são componentes principais.
- O teste tem poucas fraudes (43), então as métricas finais têm grande incerteza.

## Tecnologias utilizadas

- Python 3.13
- Jupyter Notebook
- Pandas e NumPy
- Matplotlib e Seaborn
- Scikit-learn
- Imbalanced-learn
- Optuna
- Joblib

## Como executar

1. Clone o repositório:
   ```bash
   git clone https://github.com/Ricardo-Pereira-Lourenco/Modelos-e-Optuna.git
   cd Modelos-e-Optuna
   ```
2. Instale as dependências:
   ```bash
   pip install pandas numpy matplotlib seaborn scikit-learn imbalanced-learn optuna joblib
   ```
3. Coloque o `creditcard.csv` na pasta do notebook.
4. Abra e execute o notebook:
   ```bash
   W_Tiamat.ipynb
   ```

> A otimização com 100 tentativas por algoritmo pode levar bastante tempo.

## Estrutura do notebook

1. Introdução
2. Conjunto de dados e análise exploratória
3. Métricas de desempenho (PR-AUC e F-beta)
4. Baseline
5. Comparação de algoritmos com Optuna
6. Avaliação no conjunto de teste e ajuste do limiar
7. Conclusão e aprendizados

| Categoria | Informações |
|-----------|-------------|
| **Instituição** | Ilum Escola de Ciência |
| **Aluno** | **Ricardo Pereira Lourenço de Carvalho**<br> GitHub: [@Ricardo-Pereira-Lourenco](https://github.com/Ricardo-Pereira-Lourenco) |
| **Docentes** | **Daniel Roberto Cassar**<br> GitHub: [@drcassar](https://github.com/drcassar)
