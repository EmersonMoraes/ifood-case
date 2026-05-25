# Case Técnico Data Science - iFood

Este projeto desenvolve uma solução baseada em dados para otimizar a distribuição de cupons e ofertas aos clientes do iFood.

A proposta é analisar dados históricos de clientes, ofertas e transações, construir uma base analítica consolidada e treinar um modelo preditivo para apoiar a decisão de qual oferta enviar para cada cliente.

---

## 1. Estrutura do projeto

A estrutura esperada do repositório é (ao final do processo por isso está com as bases):

    ifood-case/
    ├── data/
    │   ├── raw/
    │   │   ├── offers.json
    │   │   ├── customers.json
    │   │   └── transactions.json
    │   └── processed/
    │       └── customer_offer_dataset.parquet
    ├── notebooks/
    │   ├── 1_data_processing.ipynb
    │   └── 2_modeling.ipynb
    ├── presentation/
    ├── README.md
    └── requirements.txt

---

## 2. Dados de entrada

Os arquivos originais devem ser colocados na pasta:

    data/raw/

A pasta deve conter obrigatoriamente os três arquivos abaixo:

    data/raw/offers.json
    data/raw/customers.json
    data/raw/transactions.json

Descrição dos arquivos:

- `offers.json`: contém os dados das ofertas, como tipo, valor mínimo, duração, desconto e canais de envio.
- `customers.json`: contém os dados cadastrais dos clientes, como idade, gênero, data de cadastro e limite de cartão.
- `transactions.json`: contém o histórico de eventos, incluindo ofertas recebidas, ofertas visualizadas, ofertas concluídas e transações.

---

## 3. Ambiente de execução

O projeto possui duas etapas principais:

1. Processamento de dados com PySpark.
2. Análise, modelagem e simulação com Python.

A etapa de processamento foi desenvolvida para execução em ambiente com Spark, preferencialmente:

- Databricks Community Edition;
- Databricks Runtime;
- ambiente local com PySpark configurado.

A etapa de modelagem utiliza principalmente:

- pandas;
- numpy;
- plotly;
- scikit-learn;
- xgboost;
- optuna.

---

## 4. Instalação das dependências

Crie um ambiente virtual:

    python -m venv .venv

Ative o ambiente:

Windows:

    .venv\Scripts\activate

Linux/Mac:

    source .venv/bin/activate

Instale as dependências:

    pip install -r requirements.txt

Observação: em ambientes Databricks, o PySpark normalmente já vem instalado no runtime e não precisa ser instalado via `pip`.

---

## 5. Execução do processamento de dados

Execute primeiro o notebook:

    notebooks/1_data_processing.ipynb

Essa etapa realiza:

- validação dos arquivos de entrada;
- leitura dos arquivos JSON;
- normalização do campo `value` no arquivo `transactions.json`;
- padronização dos nomes das colunas;
- junção entre transações, clientes e ofertas;
- criação das variáveis indicadoras dos canais de comunicação;
- tratamento de dados cadastrais;
- construção da base em nível de oferta recebida por cliente;
- identificação de visualizações dentro da janela da oferta;
- identificação de conclusões dentro da janela da oferta;
- identificação de transações dentro da janela da oferta;
- criação de variáveis temporais;
- criação de variáveis financeiras;
- criação de flags comportamentais;
- validação de tipos;
- gravação da base final processada.

Ao final da execução do `1_data_processing`, será salvo o arquivo:

    data/processed/customer_offer_dataset.parquet

Esse parquet é a base consolidada usada na etapa de modelagem.

---

## 6. Ajuste de caminhos

Antes de executar os notebooks, valide se os caminhos estão corretos para o ambiente utilizado.

A lógica esperada do projeto é:

Entrada:

    data/raw/offers.json
    data/raw/customers.json
    data/raw/transactions.json

Saída:

    data/processed/customer_offer_dataset.parquet

Caso esteja rodando no Databricks, pode ser necessário ajustar os caminhos para o diretório do Workspace ou DBFS.

O ponto principal é manter a separação:

- `data/raw/`: dados brutos originais;
- `data/processed/`: dados tratados e prontos para modelagem.

---

## 7. Execução da modelagem

Depois que o arquivo parquet for gerado, execute o notebook:

    notebooks/2_modeling.ipynb


Antes da execução, confirme se o caminho de leitura do parquet aponta para:

    data/processed/customer_offer_dataset.parquet

Exemplo de leitura no notebook de modelagem:

    df = pd.read_parquet("data/processed/customer_offer_dataset.parquet")

A modelagem depende da execução prévia do processamento. Portanto, o arquivo `customer_offer_dataset.parquet` precisa existir antes de rodar o notebook de modelagem.

---

## 8. Ordem correta de execução

A ordem correta é:

1. Colocar os arquivos JSON em `data/raw/`.
2. Executar `1_data_processing.ipynb`.
3. Verificar se o arquivo `customer_offer_dataset.parquet` foi criado em `data/processed/`.
4. Executar `2_modeling.ipynb`.
5. Analisar os resultados da EDA, modelagem, simulações e impacto financeiro.

---

## 9. Base final de modelagem

A base final gerada pelo processamento possui a seguinte granularidade:

    1 linha = 1 oferta recebida por cliente em um determinado momento

O principal identificador da linha é:

    offer_instance_id

A base consolidada contém informações como:

- identificação do cliente;
- identificação da oferta;
- momento de recebimento da oferta;
- momento de expiração da oferta;
- atributos cadastrais do cliente;
- atributos da oferta;
- canais de envio;
- informação de visualização;
- informação de conclusão;
- transações durante a janela da oferta;
- valor bruto transacionado;
- recompensa concedida;
- valor líquido após recompensa;
- flags comportamentais;
- variáveis temporais.

---

## 10. Variável alvo

A variável alvo principal da modelagem é:

    completed

Ela indica se a oferta foi concluída dentro da janela de validade.

O modelo busca estimar a probabilidade de conclusão de uma oferta para cada cliente.

---

## 11. Etapa de modelagem

A etapa de modelagem contempla:

- análise exploratória da base consolidada;
- validação da estrutura e granularidade da base;
- análise de duplicidades e chaves;
- análise de valores ausentes;
- análise de atributos cadastrais dos clientes;
- análise de atributos das ofertas;
- análise de consistência temporal;
- análise da distribuição do target `completed`;
- avaliação de risco de vazamento de dados;
- separação temporal entre treino, validação e teste;
- treinamento de modelo XGBoost;
- otimização de hiperparâmetros com Optuna;
- avaliação do modelo com métricas de classificação;
- análise de lift;
- análise de calibração das probabilidades;
- simulação de política de recomendação;
- comparação entre cenário real observado e cenário recomendado;
- estimativa de impacto financeiro esperado.

---

## 12. Métricas avaliadas

As principais métricas utilizadas na modelagem são:

- ROC-AUC;
- PR-AUC;
- acurácia;
- precisão;
- recall;
- F1-score;
- log loss;
- Brier score;
- matriz de confusão;
- lift por decil;
- calibração das probabilidades.

Essas métricas ajudam a avaliar tanto a capacidade preditiva do modelo quanto a qualidade das probabilidades estimadas.

---

## 13. Simulação de negócio

Após o treinamento do modelo, a solução simula políticas de priorização de ofertas com base na probabilidade prevista de conclusão.

A ideia é comparar o comportamento histórico observado com uma estratégia orientada por modelo.

A simulação considera:

- quantidade de ofertas enviadas;
- quantidade esperada de ofertas concluídas;
- taxa esperada de conclusão;
- valor bruto estimado;
- recompensa estimada;
- valor líquido esperado;
- eficiência por envio;
- impacto financeiro potencial.

---

## 14. Sobre a solução proposta

O problema foi tratado como uma solução de propensão e priorização de ofertas.

Em vez de enviar ofertas de forma genérica, o modelo estima a chance de conclusão de uma oferta para cada cliente. Com isso, é possível priorizar clientes e ofertas com maior probabilidade de conversão e maior valor líquido esperado.

A solução considera:

- perfil do cliente;
- características da oferta;
- canais disponíveis;
- histórico de interação;
- comportamento transacional;
- valor financeiro esperado.

---

## 15. Objetivo de negócio

O objetivo de negócio é apoiar uma distribuição mais eficiente de cupons e ofertas.

A solução busca responder perguntas como:

- quais clientes têm maior probabilidade de concluir uma oferta;
- quais ofertas têm melhor desempenho;
- quais perfis respondem melhor a cada tipo de oferta;
- qual seria o impacto de priorizar recomendações pelo modelo;
- como a estratégia recomendada se compara ao cenário histórico;
- qual política gera maior valor líquido esperado.

---

## 16. Principais entregáveis

Os principais entregáveis do projeto são:

- notebook de processamento de dados em PySpark;
- base processada em formato parquet;
- notebook de análise e modelagem;
- métricas de avaliação do modelo;
- simulações de recomendação;
- análise de impacto financeiro;
- apresentação executiva para stakeholders de negócio.

---

## 17. Observações importantes

- Os arquivos JSON originais não devem ser alterados.
- A pasta `data/raw/` deve conter os dados brutos.
- A pasta `data/processed/` deve armazenar a base tratada.
- O notebook de modelagem depende da execução prévia do processamento.
- O arquivo `customer_offer_dataset.parquet` precisa existir antes da modelagem.
- Em caso de execução no Databricks, os caminhos devem ser validados antes de rodar os notebooks.
- O modelo não deve utilizar variáveis que só seriam conhecidas após o envio da oferta, para evitar vazamento de informação.

---

## 18. Resultado esperado

Ao final do projeto, espera-se ter uma solução capaz de estimar a propensão de conclusão de ofertas e simular uma política de recomendação mais eficiente.

Essa abordagem permite demonstrar o potencial impacto da solução no negócio, tanto em termos de conversão quanto em valor líquido esperado.# Case Téc­ni­co Data Sci­en­ce - iFo­od

