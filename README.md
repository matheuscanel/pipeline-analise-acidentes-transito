# Pipeline de Análise de Acidentes de Trânsito em Rodovias Federais

Este projeto foi desenvolvido para a disciplina de Big Data, com base em dados públicos da Polícia Rodoviária Federal (PRF). A proposta é construir um pipeline de dados baseado na arquitetura Medallion (Landing, Bronze, Silver e Gold), identificando padrões e fatores de risco associados à gravidade dos acidentes.

O foco principal é entender melhor o comportamento dos acidentes e identificar padrões que possam estar relacionados à gravidade das ocorrências.

## Objetivo

Analisar padrões de acidentes de trânsito nas rodovias federais brasileiras para identificar fatores de risco, como horário, localização e condições da via, e entender quais situações estão mais associadas a acidentes graves.

## Fonte de Dados

- Dataset: DATATRAN 2025 – Dados de Acidentes em Rodovias Federais  
- Origem: Polícia Rodoviária Federal (PRF) – Dados Abertos  
- Formato: CSV (UTF-8)  
- Volume: aproximadamente 75.000 registros

### Principais colunas

- data_inversa  
- horario  
- uf  
- br  
- km  
- tipo_acidente  
- classificacao_acidente  
- condicao_meteorologica  
- tipo_pista  
- mortos  
- feridos_graves  
- feridos_leves  

## Arquitetura do Pipeline

O pipeline foi construído utilizando a arquitetura Medallion, que organiza os dados em camadas de acordo com o nível de tratamento.

### Landing

Camada responsável pela ingestão dos dados brutos, sem qualquer tipo de transformação. Os dados são lidos a partir do CSV disponibilizado e convertidos para um DataFrame do Spark.

### Bronze

Nesta etapa ocorre uma padronização inicial dos dados. São aplicadas mudanças simples, como normalização dos nomes das colunas, além da adição de metadados para controle da ingestão.

Também é feito o registro de logs para rastrear o processo de carregamento dos dados.

### Silver

Camada onde os dados passam por limpeza e tratamento. Nessa etapa são removidos registros duplicados, tratados valores nulos e corrigidas inconsistências.

Também são realizadas transformações como conversão de tipos e criação de novas colunas derivadas, como dia da semana, mês e turno.

### Gold

Camada voltada para análise. Os dados são preparados para geração de insights, com agregações e estrutura adequada para visualização.

Essa etapa está em desenvolvimento, mas já foram definidos alguns tipos de análise, como distribuição de acidentes por horário e taxa de mortalidade por estado.

## Tecnologias Utilizadas

- Python 3  
- Pandas  
- Apache Spark (PySpark)  
- Delta Lake  
- Databricks Community Edition  
- Google Sheets  
- GitHub  
- Jupyter Notebooks  
- Matplotlib e Seaborn  

## Evolução do Projeto

Pensando em um cenário de produção, o pipeline pode ser expandido com outras tecnologias que permitem maior escala e automação.

- Databricks (versão paga) para execução automatizada  
- Azure Data Lake Storage para armazenamento distribuído  
- Apache Kafka para ingestão de dados em tempo real  
- dbt para organização das transformações  
- Power BI para criação de dashboards  

## Estrutura do Repositório

```
pipeline-analise-acidentes-transito/
├── data/
│   └── datatran-2025.csv
├── src/
│   ├── 01_landing_to_bronze.ipynb
│   └── 02_bronze_to_silver.ipynb
├── documentacao/
│   ├── arquitetura.md
│   └── checklist.md
└── README.md
```

## Equipe

- Arthur Reis (aars@cesar.school)
- Diego Escorel (dfe@cesar.school)
- Edmar Alencar  (era@cesar.school)
- Gabriel Moura (gmoc@cesar.school)
- Luiz Felipe Soriano (lfsbs@cesar.school)
- Matheus Canel (mxgtc@cesar.school)

## Referências

- https://www.gov.br/prf/pt-br/acesso-a-informacao/dados-abertos  
- https://delta.io  
- https://www.databricks.com/glossary/medallion-architecture  
