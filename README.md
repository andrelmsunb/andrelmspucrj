# andrelmspucrj
CyberLeaks DW (MVP)
https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/1823891642227113/1691741848228488/5301276124242215/latest.html

#######################################
CyberLeaks DW (MVP)
Autor: André Luiz Marques Serrano

1. Descrição
O CyberLeaks DW é uma solução de Data Warehouse desenvolvida para analisar, monitorar e detectar padrões em vazamentos de dados. Utilizando um modelo estrela, a plataforma estrutura informações sobre violações de segurança, permitindo consultas avançadas e insights estratégicos para mitigar riscos cibernéticos.

2. Objetivo
O CyberLeaks DW resolve esses desafios ao estruturar os dados sobre vazamentos em um Data Warehouse, facilitando a análise por meio de consultas SQL otimizadas e integração com ferramentas de visualização de dados. A solução inclui:

Coleta e armazenamento de dados do Kaggle.
Modelo estrela para organizar as informações e permitir consultas eficientes.
Pipeline ETL para extração, transformação e carga dos dados.
Análises e insights sobre frequência, setores afetados e métodos de ataque.
3. Implementação
A implementação visa criar uma estrutura robusta para análise de vazamentos de dados, utilizando um Data Warehouse baseado no modelo estrela. Esse processo permitirá consultas otimizadas, análises preditivas e identificação de padrões de ataques cibernéticos.

A seguir mostraremos a implementação e no final apresentaremos respostas de algumas perguntas interessantes para temática.

3.1. Base de dados (Kaggle)
A base de dados utilizada foi obtida no Kaggle, a partir do dataset "Data Breaches - A Comprehensive List", disponível em:

Kaggle: Data Breaches - A Comprehensive List

Esse conjunto de dados fornece um histórico detalhado sobre vazamentos de dados, abrangendo diversas organizações e setores, permitindo análises sobre a frequência, magnitude e causas desses incidentes ao longo dos anos.

O dataset contém informações estruturadas sobre vazamentos de dados ocorridos desde o ano 2004, abrangendo os seguintes atributos:

Atributo	Descrição
Entity	Nome da organização que sofreu o vazamento de dados.
Year	Ano em que o vazamento ocorreu.
Records	Número de registros comprometidos.
Organization type	Setor da organização (ex: Tecnologia, Saúde, Governo, Educação).
Method	Método pelo qual os dados foram vazados (ex: hacking, erro humano, malware).
Sources	Fonte da informação sobre o vazamento (artigos, relatórios, investigações públicas).
6
# Instalando os pacote kaggle para baixar o dataset pela API
%pip install kaggle -q
Python interpreter will be restarted.
Python interpreter will be restarted.
7
import json
import os

# Função de autenticação na plataforma Kaggle
def kaggle_auth_setup(kaggle_auth):
    try:
        # Cria o diretório "~/.kaggle"
        os.makedirs(os.path.expanduser("~/.kaggle"), exist_ok=True)
        output_file = os.path.expanduser("~/.kaggle/kaggle.json")

        # Cria o arquivo "kaggle.json" com as credenciais
        with open(output_file, "w") as file:
            json.dump(json.loads(kaggle_auth), file, indent=4)
        
        os.chmod(output_file, 0o600)  # Define permissões seguras
        print("Autenticação Kaggle configurada com sucesso!")
    
    except Exception as e:
        print(f"Erro na autenticação: {e}")
8
# Realiza a autenticação com as credenciais informadas (Token API)
kaggle_auth_setup('{"username":"andreserrano","key":"XXXXXXX"}')
Autenticação Kaggle configurada com sucesso!
9
# Visualizando o json criado
!cat ~/.kaggle/kaggle.json
{
    "username": "andreserrano",
    "key": "XXXXXXX"
}
10
# Realizando o download do dataset data-breaches-a-comprehensive-list
!kaggle datasets download -d thedevastator/data-breaches-a-comprehensive-list
Dataset URL: https://www.kaggle.com/datasets/thedevastator/data-breaches-a-comprehensive-list
License(s): CC0-1.0
data-breaches-a-comprehensive-list.zip: Skipping, found more recently modified local copy (use --force to force download)
11
# Listando os arquivos para verificar se o download foi realizado com sucesso (data-breaches-a-comprehensive-list.zip)
dbutils.fs.ls('file:/databricks/driver')
Out[5]: [FileInfo(path='file:/databricks/driver/azure/', name='azure/', size=4096, modificationTime=1742397655324),
 FileInfo(path='file:/databricks/driver/preload_class.lst', name='preload_class.lst', size=1306936, modificationTime=1742397655325),
 FileInfo(path='file:/databricks/driver/conf/', name='conf/', size=4096, modificationTime=1742397654539),
 FileInfo(path='file:/databricks/driver/hadoop_accessed_config.lst', name='hadoop_accessed_config.lst', size=2755, modificationTime=1742397655325),
 FileInfo(path='file:/databricks/driver/data-breaches-a-comprehensive-list.zip', name='data-breaches-a-comprehensive-list.zip', size=9067, modificationTime=1668096970000),
 FileInfo(path='file:/databricks/driver/eventlogs/', name='eventlogs/', size=4096, modificationTime=1742408572078),
 FileInfo(path='file:/databricks/driver/metastore_db/', name='metastore_db/', size=4096, modificationTime=1742408632694),
 FileInfo(path='file:/databricks/driver/logs/', name='logs/', size=4096, modificationTime=1742410840440),
 FileInfo(path='file:/databricks/driver/ganglia/', name='ganglia/', size=4096, modificationTime=1742413501895)]
12
# Descompactando o arquivo data-breaches-a-comprehensive-list.zip
!unzip /databricks/driver/data-breaches-a-comprehensive-list.zip
Archive:  /databricks/driver/data-breaches-a-comprehensive-list.zip
  inflating: df_1.csv                
13

# Listando os arquivos
dbutils.fs.ls('file:/databricks/driver')
Out[7]: [FileInfo(path='file:/databricks/driver/azure/', name='azure/', size=4096, modificationTime=1742397655324),
 FileInfo(path='file:/databricks/driver/preload_class.lst', name='preload_class.lst', size=1306936, modificationTime=1742397655325),
 FileInfo(path='file:/databricks/driver/conf/', name='conf/', size=4096, modificationTime=1742397654539),
 FileInfo(path='file:/databricks/driver/hadoop_accessed_config.lst', name='hadoop_accessed_config.lst', size=2755, modificationTime=1742397655325),
 FileInfo(path='file:/databricks/driver/data-breaches-a-comprehensive-list.zip', name='data-breaches-a-comprehensive-list.zip', size=9067, modificationTime=1668096970000),
 FileInfo(path='file:/databricks/driver/eventlogs/', name='eventlogs/', size=4096, modificationTime=1742408572078),
 FileInfo(path='file:/databricks/driver/df_1.csv', name='df_1.csv', size=23269, modificationTime=1668096968000),
 FileInfo(path='file:/databricks/driver/metastore_db/', name='metastore_db/', size=4096, modificationTime=1742408632694),
 FileInfo(path='file:/databricks/driver/logs/', name='logs/', size=4096, modificationTime=1742410840440),
 FileInfo(path='file:/databricks/driver/ganglia/', name='ganglia/', size=4096, modificationTime=1742413501895)]
14
# Renomea e move o dataset df_1.csv para data-breaches-a-comprehensive-list.csv
dbutils.fs.mv('file:/databricks/driver/df_1.csv', 'dbfs:/Filestore/data-breaches-a-comprehensive-list.csv', recurse=True)
Out[8]: True
15
# Lista o arquivo para verificar se foi renomeado o CSV
dbutils.fs.ls('dbfs:/Filestore')
Out[9]: [FileInfo(path='dbfs:/Filestore/data-breaches-a-comprehensive-list.csv', name='data-breaches-a-comprehensive-list.csv', size=23269, modificationTime=1742414052000)]
3.2. Coleta
A coleta de dados foi realizada a partir do dataset "Data Breaches - A Comprehensive List", disponível no Kaggle. Esse conjunto de dados foi baixado e armazenado na nuvem, garantindo acessibilidade para processamento e análise. Durante essa etapa, os dados passaram por um processo de verificação para assegurar sua integridade, incluindo a remoção de duplicatas, a conversão de tipos de dados e o tratamento de valores ausentes. Caso necessário, futuras expansões poderão incluir a extração de novas fontes de dados, como relatórios de segurança pública e bases de dados governamentais sobre incidentes de vazamento.

17
from pyspark.sql import SparkSession
from pyspark.sql.functions import col

# Criar sessão Spark
spark = SparkSession.builder.appName("CyberLeaks_Coleta").getOrCreate()

# oletar os dados do arquivo CSV
df_spark = spark.read.csv(
    'dbfs:/Filestore/data-breaches-a-comprehensive-list.csv', 
    header=True, 

df_spark:pyspark.sql.dataframe.DataFrame = [_c0: integer, Entity: string ... 5 more fields]
root
 |-- _c0: integer (nullable = true)
 |-- Entity: string (nullable = true)
 |-- Year: integer (nullable = true)
 |-- Records: long (nullable = true)
 |-- Organization_Type: string (nullable = true)
 |-- Method_Name: string (nullable = true)
 |-- Source_Details: string (nullable = true)

+---+--------------------+----+---------+-----------------+-------------------+--------------+
|_c0|              Entity|Year|  Records|Organization_Type|        Method_Name|Source_Details|
+---+--------------------+----+---------+-----------------+-------------------+--------------+
|  5|Advocate Medical ...|2017|  4000000|       healthcare|lost / stolen media|      [13][14]|
|  4|          Adobe Inc.|2019|  7500000|             tech|      poor security|      [11][12]|
|  3|Adobe Systems Inc...|2013|152000000|             tech|             hacked|          [10]|
|  1|               500px|2020| 14870304|social networking|             hacked|           [7]|
|  0|21st Century Onco...|2016|  2200000|       healthcare|             hacked|        [5][6]|
+---+--------------------+----+---------+-----------------+-------------------+--------------+
only showing top 5 rows

3.3. Modelagem
A etapa de Modelagem foi estruturada utilizando um modelo estrela, garantindo um armazenamento eficiente e otimizado para consultas analíticas. A modelagem foi definida com uma tabela fato (fatos_ocorrencias), que centraliza os registros de vazamentos de dados, e quatro tabelas dimensão (dim_entidade, dim_organizacao, dim_metodo, dim_fonte), que armazenam informações categóricas essenciais para análise. Essa abordagem facilita a realização de consultas rápidas e permite explorar as relações entre empresas afetadas, setores vulneráveis, métodos de ataque e fontes de informação. A definição dos atributos e relacionamentos foi documentada no Dicionário de Dados, garantindo padronização e transparência na estrutura do Data Warehouse.

Diagrama DW

Tabela Fato: fatos_ocorrencias
Atributo	Tipo	Descrição
Entity_ID	INT	Identificador da entidade afetada pelo vazamento (FK).
Year	INT	Ano em que ocorreu o vazamento.
Records	BIGINT	Número de registros comprometidos.
Method_ID	INT	Identificador do método utilizado no vazamento (FK).
Organization_ID	INT	Identificador do setor da organização afetada (FK).
Source_ID	INT	Identificador da fonte da informação sobre o vazamento (FK).
Tabela Dimensão: dim_entidade
Atributo	Tipo	Descrição
Entity_ID	INT	Identificador único da entidade (PK).
Entity_Name	STRING	Nome da organização afetada pelo vazamento.
Tabela Dimensão: dim_organizacao
Atributo	Tipo	Descrição
Organization_ID	INT	Identificador único do setor (PK).
Organization_Type	STRING	Tipo de organização (ex: Governo, Saúde, Tecnologia).
Tabela Dimensão: dim_metodo
Atributo	Tipo	Descrição
Method_ID	INT	Identificador único do método de ataque (PK).
Method_Name	STRING	Nome do método de ataque (ex: Hacking, Erro Humano).
Tabela Dimensão: dim_fonte
Atributo	Tipo	Descrição
Source_ID	INT	Identificador único da fonte (PK).
Source_Details	STRING	Descrição da fonte da informação sobre o vazamento.
3.4. ETL (Extração, Transformação e Carga)
A etapa de ETL (Extração, Transformação e Carga) tem como objetivo estruturar os dados coletados e prepará-los para análises eficientes dentro do Data Warehouse. Inicialmente, os dados passam por uma fase de transformação, onde são aplicadas conversões de tipos para garantir consistência e integridade. Em seguida, as tabelas dimensão são criadas, removendo duplicatas e gerando identificadores únicos para cada entidade, organização, método de ataque e fonte de informação. A tabela fato é então construída, associando os registros de vazamentos de dados com as tabelas dimensão, permitindo a realização de consultas otimizadas. Por fim, as tabelas processadas são armazenadas no formato Parquet, garantindo um acesso rápido e eficiente dentro do ambiente Databricks.

20
from pyspark.sql.functions import monotonically_increasing_id

# Criar sessão Spark para o processo ETL
spark = SparkSession.builder.appName("CyberLeaks_ETL").getOrCreate()

# Corrigir tipos de dados
df_spark = df_spark.withColumn("Year", col("Year").cast("int"))
df_spark = df_spark.withColumn("Records", col("Records").cast("bigint"))

# Criar tabelas dimensão (removendo duplicatas e gerando IDs únicos)

df_spark:pyspark.sql.dataframe.DataFrame = [_c0: integer, Entity: string ... 5 more fields]
dim_entidade:pyspark.sql.dataframe.DataFrame = [Entity_Name: string, Entity_ID: long]
dim_fonte:pyspark.sql.dataframe.DataFrame = [Source_Details: string, Source_ID: long]
dim_metodo:pyspark.sql.dataframe.DataFrame = [Method_Name: string, Method_ID: long]
dim_organizacao:pyspark.sql.dataframe.DataFrame = [Organization_Type: string, Organization_ID: long]
fatos_ocorrencias:pyspark.sql.dataframe.DataFrame = [Entity_ID: long, Year: integer ... 4 more fields]
ETL finalizado com sucesso!
3.5. Análise
A etapa de Análise consiste na execução de consultas SQL para extrair insights estratégicos sobre vazamentos de dados. As consultas são formuladas para responder a perguntas-chave, como quais setores são mais vulneráveis, quais métodos de ataque são mais frequentes, quais empresas tiveram os maiores vazamentos e como esses incidentes evoluíram ao longo do tempo. Utilizando a estrutura do modelo estrela, é possível explorar os dados de maneira eficiente, combinando a tabela fato (fatos_ocorrencias) com as tabelas dimensão (dim_entidade, dim_organizacao, dim_metodo e dim_fonte). Os resultados dessas análises permitem identificar padrões, avaliar riscos e contribuir para estratégias de mitigação de vazamentos de dados.

22
%sql
-- 1. Qual foi o ano com o maior número de registros vazados?
SELECT Year, SUM(Records) AS total_records
FROM fatos_ocorrencias
GROUP BY Year
ORDER BY total_records DESC
LIMIT 1;
_sqldf:pyspark.sql.dataframe.DataFrame = [Year: integer, total_records: long]
Table
1 row
23
%sql
-- 2. Qual a tendência de vazamentos ao longo dos anos?
SELECT Year, SUM(Records) AS total_records
FROM fatos_ocorrencias
GROUP BY Year
ORDER BY Year ASC;

_sqldf:pyspark.sql.dataframe.DataFrame = [Year: integer, total_records: long]
Table
20 rows
24
%sql
-- 3. Quais são as 5 empresas mais afetadas por vazamentos de dados?
SELECT e.Entity_Name, SUM(f.Records) AS total_records
FROM fatos_ocorrencias f
JOIN dim_entidade e ON f.Entity_ID = e.Entity_ID
GROUP BY e.Entity_Name
ORDER BY total_records DESC
LIMIT 5;

_sqldf:pyspark.sql.dataframe.DataFrame = [Entity_Name: string, total_records: long]
Table
5 rows
25
%sql
-- 4. Quais setores tiveram mais vazamentos de dados?
SELECT o.Organization_Type, SUM(f.Records) AS total_records
FROM fatos_ocorrencias f
JOIN dim_organizacao o ON f.Organization_ID = o.Organization_ID
GROUP BY o.Organization_Type
ORDER BY total_records DESC;

_sqldf:pyspark.sql.dataframe.DataFrame = [Organization_Type: string, total_records: long]
Table
70 rows
26
%sql
-- 5. Qual setor foi mais afetado a cada ano?
SELECT Year, o.Organization_Type, SUM(Records) AS total_records
FROM fatos_ocorrencias f
JOIN dim_organizacao o ON f.Organization_ID = o.Organization_ID
GROUP BY Year, o.Organization_Type
ORDER BY Year, total_records DESC;

_sqldf:pyspark.sql.dataframe.DataFrame = [Year: integer, Organization_Type: string ... 1 more field]
Table
184 rows
27
%sql
-- 6. Quais são os métodos de ataque mais comuns?
SELECT m.Method_Name, COUNT(*) AS ocorrencias, SUM(f.Records) AS total_records
FROM fatos_ocorrencias f
JOIN dim_metodo m ON f.Method_ID = m.Method_ID
GROUP BY m.Method_Name
ORDER BY total_records DESC;

_sqldf:pyspark.sql.dataframe.DataFrame = [Method_Name: string, ocorrencias: long ... 1 more field]
Table
25 rows
28
%sql
-- 7. Como os métodos de ataque evoluíram ao longo do tempo?
SELECT Year, m.Method_Name, COUNT(*) AS ocorrencias, SUM(f.Records) AS total_records
FROM fatos_ocorrencias f
JOIN dim_metodo m ON f.Method_ID = m.Method_ID
GROUP BY Year, m.Method_Name
ORDER BY Year ASC, total_records DESC;

_sqldf:pyspark.sql.dataframe.DataFrame = [Year: integer, Method_Name: string ... 2 more fields]
Table
97 rows
29
%sql
-- 8. Quais métodos de ataque afetam mais cada setor?
SELECT o.Organization_Type, m.Method_Name, COUNT(*) AS ocorrencias, SUM(f.Records) AS total_records
FROM fatos_ocorrencias f
JOIN dim_organizacao o ON f.Organization_ID = o.Organization_ID
JOIN dim_metodo m ON f.Method_ID = m.Method_ID
GROUP BY o.Organization_Type, m.Method_Name
ORDER BY o.Organization_Type, total_records DESC;

_sqldf:pyspark.sql.dataframe.DataFrame = [Organization_Type: string, Method_Name: string ... 2 more fields]
Table
127 rows
30
%sql
-- 9. Quais são as principais fontes de vazamento de dados?
SELECT s.Source_Details, COUNT(*) AS ocorrencias, SUM(f.Records) AS total_records
FROM fatos_ocorrencias f
JOIN dim_fonte s ON f.Source_ID = s.Source_ID
GROUP BY s.Source_Details
ORDER BY total_records DESC;

_sqldf:pyspark.sql.dataframe.DataFrame = [Source_Details: string, ocorrencias: long ... 1 more field]
Table
312 rows
31
%sql
-- 10. Fontes de vazamento mais relevantes por setor
SELECT o.Organization_Type, s.Source_Details, COUNT(*) AS ocorrencias, SUM(f.Records) AS total_records
FROM fatos_ocorrencias f
JOIN dim_organizacao o ON f.Organization_ID = o.Organization_ID
JOIN dim_fonte s ON f.Source_ID = s.Source_ID
GROUP BY o.Organization_Type, s.Source_Details
ORDER BY o.Organization_Type, total_records DESC;

_sqldf:pyspark.sql.dataframe.DataFrame = [Organization_Type: string, Source_Details: string ... 2 more fields]
Table
328 rows
32
%sql
-- 11. Quais métodos de ataque estão crescendo mais rápido?
WITH metodo_crescimento AS (
    SELECT Year, m.Method_Name, SUM(f.Records) AS total_records,
           LAG(SUM(f.Records)) OVER (PARTITION BY m.Method_Name ORDER BY Year) AS prev_year_records
    FROM fatos_ocorrencias f
    JOIN dim_metodo m ON f.Method_ID = m.Method_ID
    GROUP BY Year, m.Method_Name
)
SELECT Year, Method_Name, total_records, (total_records - prev_year_records) AS crescimento

_sqldf:pyspark.sql.dataframe.DataFrame = [Year: integer, Method_Name: string ... 2 more fields]
Table
68 rows
This result is stored as _sqldf and can be used in other Python cells.
;
