# atmos-adf

Repositório de gravação de aula do projeto **atmos**.

## Descrição

Este repositório contém os pipelines do Azure Data Factory (ADF) do resource group `rg-atmos-dev-westus2-001`, desenvolvidos durante as aulas do projeto atmos.

## Estrutura

- **Pipelines**: Pipelines de ingestão e transformação de dados no ADF
- **Datasets**: Definições de datasets utilizados nos pipelines

## Arquitetura

![Arquitetura ADF](utils\image.png)

## Convenções de Nomenclatura

| Componente | Padrão |
|---|---|
| LinkedService | `<servico>_<tipo-conector>_<fonte>_<ambiente>` |
| Dataset | `ds_<formato>_<fonte>_<papel>_<ambiente>` |
| Pipeline | `pl_<ação>_<fonte>_<camada>_<ambiente>` |

## Datasets

| Dataset | Fonte | Formato | Destino |
|---|---|---|---|
| `ds_bigquey_inmet_src_dev` | Google BigQuery (`basedosdados`) | BigQuery Table | Origem dos dados do INMET |
| `ds_csv_inmet_landing_dev` | ADLS Gen2 | CSV | `landing/inmet/microdados/{id_estacao}/ano={ano}/` |
| `ds_json_visualcrossing_src_dev` | [API VisualCrossing](https://www.visualcrossing.com/weather-api) | JSON | Origem dos dados meteorológicos por cidade/data |
| `ds_json_visualcrossing_landing_dev` | ADLS Gen2 | JSON | `landing/visualcrossing/{city}/year=/month=/day=/` |

## Pipelines

### `pl_ingest_inmet_landing_dev`
Ingestão histórica do INMET. Itera sobre um intervalo de anos (`startYear` → `endYear`) em paralelo (até 5 por vez), copiando dados do BigQuery (`basedosdados.br_inmet_bdmep.microdados`) filtrados por `id_estacao` e salvando como CSV no ADLS.

### `pl_ingest_visualcrossing_landing_dev_daily`
Ingestão diária do [VisualCrossing](https://www.visualcrossing.com/weather-api). Busca a API key no Azure Key Vault, define a data atual e copia os dados meteorológicos da cidade parametrizada via API REST, salvando como JSON no ADLS.

### `pl_ingest_visualcrossing_landing_dev_history`
Ingestão histórica do [VisualCrossing](https://www.visualcrossing.com/weather-api). Gera uma lista de datas entre `startDate` e `endDate` e itera sobre cada dia em paralelo (até 10 por vez), copiando os dados da API para o ADLS.

## Tecnologias

- Azure Data Factory (ADF)
- Azure Data Lake Storage
- Google BigQuery (Base dos Dados)
- [VisualCrossing Weather API](https://www.visualcrossing.com/weather-api)
- Azure Key Vault
