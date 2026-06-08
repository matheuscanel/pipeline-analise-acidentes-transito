# Pipeline de Análise de Acidentes de Trânsito em Rodovias Federais

Este projeto foi desenvolvido na disciplina de Fundamentos de Big Data, da CESAR School, com base em dados públicos da Polícia Rodoviária Federal (PRF). O objetivo é construir um pipeline analítico em arquitetura Medallion para identificar padrões e fatores de risco associados a acidentes graves ou fatais em rodovias federais brasileiras.

## Objetivo

Responder à seguinte pergunta de negócio:

**Quais fatores estão mais associados a acidentes graves ou fatais?**

Para isso, o projeto organiza o dado bruto em camadas progressivas de tratamento até chegar a uma base analítica pronta para exploração, visualização e geração de insights.

## Fonte de Dados

- Dataset: `DATATRAN 2025 - Dados de Acidentes em Rodovias Federais`
- Origem: Polícia Rodoviária Federal (PRF) - Dados Abertos
- Formato: CSV (UTF-8, separado por `;`)
- Volume aproximado: `~75.000` registros
- Fluxo de ingestão: `Google Sheets -> Pandas -> Apache Spark`

### Principais colunas

- `data_inversa`
- `horario`
- `uf`
- `br`
- `km`
- `tipo_acidente`
- `classificacao_acidente`
- `condicao_metereologica`
- `tipo_pista`
- `mortos`
- `feridos_graves`
- `feridos_leves`

## Arquitetura do Pipeline

O pipeline segue a arquitetura Medallion, com as camadas `Landing -> Bronze -> Silver -> Gold`, executadas no Databricks Community Edition.

### Landing

Camada de entrada dos dados brutos, sem transformações.

- Leitura do CSV via URL do Google Sheets com `pandas.read_csv()`
- Conversão para Spark DataFrame
- Persistência inicial como Delta Table em `acidentes.default.datatran_2025`

### Bronze

Camada de padronização mínima e rastreabilidade da ingestão.

- Normalização dos nomes das colunas
- Adição de metadados de governança:
  - `ingestion_timestamp`
  - `ingestion_date`
  - `ingestion_id`
  - `source_table`
- Registro de log de ingestão em `acidentes.bronze.ingestion_log`
- Persistência da tabela `acidentes.bronze.acidentes_2025`

### Silver

Camada de limpeza, padronização e enriquecimento dos dados.

- Remoção de duplicatas
- Tratamento de nulos e valores inválidos
- Correção de `classificacao_acidente` com base em mortos e feridos
- Conversão de tipos como data, horário e `km`
- Remoção de colunas irrelevantes para análise
- Padronização de texto em colunas categóricas
- Criação de colunas derivadas, como:
  - `ano`
  - `mes`
  - `dia_do_mes`
  - `hora`
  - `periodo_dia`
  - `qtd_vitimas`
  - `flag_fatal`
  - `flag_com_feridos`
  - `indice_gravidade`
- Particionamento físico por `ano` e `mes`
- Persistência em `acidentes.silver.acidentes_2025`
- Registro de log em `acidentes.silver.ingestion_log`

Observação: a documentação atualizada indica que `dia_semana` não é materializada na Silver, e que o equivalente funcional de `turno` nessa camada é `periodo_dia`.

### Gold

Camada analítica, modelada para consumo por notebooks, dashboards e ferramentas externas.

- Modelo dimensional em estrela (`Star Schema`)
- Tabela fato: `fato_acidentes`
- Dimensões:
  - `dim_tempo`
  - `dim_localizacao`
  - `dim_condicoes_via`
  - `dim_ambiente`
  - `dim_tipo_acidente`
- Exportação dos dados Gold em `CSV` e `Parquet`
- Consumo previsto em Power BI, Tableau, Python/R e cenários de Machine Learning

### Análises entregues na Gold

- Top 20 rodovias federais por número absoluto de mortes em acidentes fatais
- Heatmap de mortes por dia da semana x hora do dia
- Comparativo de frequência e taxa de letalidade por condição meteorológica
- Top 15 causas de acidente por volume de mortes e por taxa de letalidade
- Top 15 tipos de acidente por taxa de letalidade
- Associação entre tipo de acidente e causa nas combinações mais letais
- Sazonalidade mensal de mortes, acidentes fatais e taxa de mortalidade
- Resumo executivo com KPIs, rodovias, horários e dias mais perigosos

## Tecnologias Utilizadas

- Python 3
- Pandas
- Apache Spark (PySpark)
- Delta Lake
- Databricks Community Edition
- Google Sheets
- GitHub
- Jupyter Notebooks
- Matplotlib
- Seaborn

## Tecnologias Pagas para Refinamento

Em um cenário de produção, a documentação considera as seguintes evoluções:

- Databricks: clusters maiores e jobs agendados
- Azure Data Lake Storage: armazenamento distribuído e persistente em nuvem
- Apache Kafka: ingestão contínua em tempo real
- dbt: transformações SQL versionadas e maior governança
- Power BI: dashboards interativos para usuários não técnicos

## Estrutura do Repositório

```text
pipeline-analise-acidentes-transito/
├── data/
│   └── datatran-2025.csv
├── docs/
│   └── arquitetura.pdf
├── src/
│   ├── 01_landing_to_bronze.ipynb
│   ├── 02_bronze_to_silver.ipynb
│   └── 03_silver_to_gold.ipynb
└── README.md
```

O arquivo [docs/arquitetura.pdf](/Users/gabrielmoc/Downloads/pipeline-analise-acidentes-transito/docs/arquitetura.pdf) contém o detalhamento da arquitetura, da modelagem analítica, das tecnologias adotadas e da divisão das etapas do pipeline.

## Checklist

- Ingestão: Finalizado
- Armazenamento: Finalizado
- Transformação: Finalizado

## Equipe

- Arthur Reis (`aars@cesar.school`)
- Diego Escorel (`dfe@cesar.school`)
- Edmar Alencar (`era@cesar.school`)
- Gabriel Moura (`gmoc@cesar.school`)
- Luiz Felipe Soriano (`lfsbs@cesar.school`)
- Matheus Canel (`mxgtc@cesar.school`)

## Referências

- PRF - Dados Abertos: https://www.gov.br/prf/pt-br/acesso-a-informacao/dados-abertos
- Delta Lake: https://delta.io
- Arquitetura Medallion: https://www.databricks.com/glossary/medallion-architecture
