# CyberLeaks DW

**CyberLeaks DW** é um projeto de Data Warehouse desenvolvido para analisar vazamentos de dados públicos, estruturando as informações em um modelo estrela e permitindo consultas otimizadas com Spark SQL.

Este projeto foi realizado por **Andre Luiz Marques Serrano** como parte da disciplina de Banco de Dados na **PUC-Rio**.

---

## Objetivo

- Estruturar dados sobre vazamentos de segurança.
- Permitir análises por setor, método de ataque, e entidade afetada.
- Facilitar a visualização de tendências ao longo do tempo.

---

## Fonte dos Dados

- **Dataset**: [Data Breaches - A Comprehensive List](https://www.kaggle.com/datasets/thedevastator/data-breaches-a-comprehensive-list)
- **Origem**: Kaggle
- **Formato**: CSV

---

## Tecnologias Utilizadas

- Apache Spark / PySpark  
- Databricks  
- SQL (Spark SQL)  
- Python  
- Parquet  

---

## Estrutura do Modelo

- **Tabela fato**: `fatos_ocorrencias`
- **Tabelas dimensão**:  
  - `dim_entidade`  
  - `dim_organizacao`  
  - `dim_metodo`  
  - `dim_fonte`

---

## Etapas do Projeto

1. **Coleta dos dados** a partir de arquivo CSV.
2. **Transformação**: limpeza, tipagem e modelagem.
3. **Carga**: criação de tabelas Parquet e views SQL.
4. **Análise**: consultas SQL para geração de insights.

---

## Autor

Andre Luiz Marques Serrano  
Projeto acadêmico - PUC-Rio
