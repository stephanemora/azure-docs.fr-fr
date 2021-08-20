---
title: Centre européen pour la prévention et le contrôle des maladies (CEPCM) - Cas de COVID-19
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données « Centre européen pour la prévention et le contrôle des maladies (CEPCM) - Cas de COVID-19 » dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 31868ee27e531e70df4c89944f0baf888527a190
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447597"
---
# <a name="european-centre-for-disease-prevention-and-control-ecdc-covid-19-cases"></a>Centre européen pour la prévention et le contrôle des maladies (CEPCM) - Cas de COVID-19

[Dernières données publiques disponibles](https://www.ecdc.europa.eu/en/publications-data/download-todays-data-geographic-distribution-covid-19-cases-worldwide) sur la répartition géographique des cas de COVID-19 dans le monde, fournies par le Centre européen de prévention et de contrôle des maladies (ECDC). Chaque ligne/entrée contient le nombre de nouveaux cas signalés par jour et par pays ou région.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>Groupes de données

Des versions modifiées du jeu de données sont disponibles aux formats CSV, JSON, JSON-Lines et Parquet ; celles-ci sont mises à jour quotidiennement :
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet

Des codes iso_pays_région et des temps de chargement ont été ajoutés à toutes les versions modifiées. En outre, celles-ci utilisent des noms de colonnes en minuscules et des tirets bas en guise de séparateurs.

Données brutes : https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/ecdc_cases/latest/ECDCCases.csv

Versions précédentes des données modifiées et brutes : https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/ https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/ecdc_cases/

## <a name="data-volume"></a>Volume de données
Au 28 mai 2020, on comptait 19 876 lignes (CSV 1,5 Mo, JSON 4,9 Mo, JSONL 4,9 Mo, Parquet 54,1 Ko).

## <a name="data-source"></a>Paramètres

Les données brutes sont ingérées quotidiennement à partir du [fichier .csv ECDC](https://opendata.ecdc.europa.eu/covid19/casedistribution/csv). Pour plus d’informations sur ce jeu de données, y compris sur ses origines, consultez la [page consacrée à la collection de données ECDC](https://www.ecdc.europa.eu/en/covid-19/data-collection).

## <a name="data-quality"></a>Qualité des données
L'ECDC ne garantit pas l'exactitude ou la pertinence des données. [Lire la clause d’exclusion de responsabilité](https://www.ecdc.europa.eu/en/covid-19/data-collection).

## <a name="license-and-use-rights-attribution"></a>Attribution des licences et des droits d’utilisation

Ces données sont mises à disposition et peuvent être utilisées conformément à la politique ECDC sur les droits d’auteur disponible ici. Pour tout document dont le droit d’auteur appartient à un tiers, l’autorisation de reproduction doit être obtenue auprès du détenteur du droit d’auteur.

L’ECDC doit toujours être reconnu comme la source d’origine de ces données. Cette reconnaissance doit être incluse dans chaque copie du matériel.

## <a name="contact"></a>Contact

Pour toute question ou commentaire sur ce jeu de données ou d’autres jeux de données dans le Data Lake COVID-19, veuillez contacter askcovid19dl@microsoft.com.

## <a name="columns"></a>Colonnes

| Nom                      | Type de données | Unique | Valeurs (exemple)            | Description                            |
|---------------------------|-----------|--------|----------------------------|----------------------------------------|
| cas                     | SMALLINT  | 5 515  | 1 2                        | Nombre de cas signalés               |
| continent_exp             | string    | 6      | Europe Afrique              | Nom du continent                         |
| countries_and_territories | string    | 214    | Canada Belgique             | Nom du pays ou du territoire              |
| country_territory_code    | string    | 213    | KOR ISL                    | Code à trois lettres du pays ou du territoire |
| date_rep                  | Date      | 350    | 11-12-2020 22-11-2020      | Date du signalement                     |
| day                       | SMALLINT  | 31     | 14 13                      | Jour du mois                           |
| décès                    | SMALLINT  | 1 049  | 1 2                        | Nombre de décès signalés              |
| geo_id                    | string    | 214    | CA SE                      | Identificateur géographique                         |
| iso_country               | string    | 214    | SE US                      | Code pays ou région ISO 3166        |
| load_date                 | timestamp | 1      | 26-04-2021 00:06:22.123000 | Date de chargement des données dans Azure      |
| month                     | SMALLINT  | 12     | 10 8                       | Numéro du mois                           |
| year                      | SMALLINT  | 2      | 2020 2019                  | Year                                   |

## <a name="preview"></a>Préversion

| date_rep   | day | month | year | cas | décès | countries_and_territories | geo_id | country_territory_code | continent_exp | load_date             | iso_country |
|------------|-----|-------|------|-------|--------|---------------------------|--------|------------------------|---------------|-----------------------|-------------|
| 14-12-2020 | 14  | 12    | 2020 | 746   | 6      | Afghanistan               | AF     | AFG                    | Asie          | 26/04/2021 12:06:22 | AF          |
| 13-12-2020 | 13  | 12    | 2020 | 298   | 9      | Afghanistan               | AF     | AFG                    | Asie          | 26/04/2021 12:06:22 | AF          |
| 12-12-2020 | 12  | 12    | 2020 | 113   | 11     | Afghanistan               | AF     | AFG                    | Asie          | 26/04/2021 12:06:22 | AF          |
| 11-12-2020 | 11  | 12    | 2020 | 63    | 10     | Afghanistan               | AF     | AFG                    | Asie          | 26/04/2021 12:06:22 | AF          |
| 10-12-2020 | 10  | 12    | 2020 | 202   | 16     | Afghanistan               | AF     | AFG                    | Asie          | 26/04/2021 12:06:22 | AF          |
| 09-12-2020 | 9   | 12    | 2020 | 135   | 13     | Afghanistan               | AF     | AFG                    | Asie          | 26/04/2021 12:06:22 | AF          |
| 08-12-2020 | 8   | 12    | 2020 | 200   | 6      | Afghanistan               | AF     | AFG                    | Asie          | 26/04/2021 12:06:22 | AF          |
| 2020-12-07 | 7   | 12    | 2020 | 210   | 26     | Afghanistan               | AF     | AFG                    | Asie          | 26/04/2021 12:06:22 | AF          |
| 06-12-2020 | 6   | 12    | 2020 | 234   | 10     | Afghanistan               | AF     | AFG                    | Asie          | 26/04/2021 12:06:22 | AF          |
| 05-12-2020 | 5   | 12    | 2020 | 235   | 18     | Afghanistan               | AF     | AFG                    | Asie          | 26/04/2021 12:06:22 | AF          |

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ce notebook documente les URL et les exemples de code pour l’accès aux URL du jeu de données « Centre européen pour la prévention et le contrôle des maladies (CEPCM) - Cas de COVID-19 » qui se présentent sous différents formats hébergés dans le Stockage Blob Azure : CSV https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv

JSON : https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json

JSONL : https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl

Parquet : https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet

Téléchargez le fichier de jeu de données à l’aide de la fonctionnalité intégrée permettant de télécharger à partir d’une URL HTTP dans Pandas. Pandas comprend des visionneuses pour différents formats de fichiers :

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html


```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet")
df.head(10)

df.dtypes

df.groupby('countries_and_territories').first().filter(['continent_exp','cases', 'deaths','date_rep'])

df.groupby('continent_exp').agg({'countries_and_territories': 'count','cases': 'count','deaths': 'count'})

import plotly.graph_objects as go
import plotly.express as px
import matplotlib.pyplot as plt

df.loc[: , ['countries_and_territories', 'cases', 'deaths']].groupby(['countries_and_territories'
         ]).max().sort_values(by='cases',ascending=False).reset_index()[:15].style.background_gradient(cmap='rainbow')

df_Worldwide=df[df['countries_and_territories']=='United_States_of_America']

df.plot(kind='line',x='date_rep',y="cases",grid=True)
df.plot(kind='line',x='date_rep',y="deaths",grid=True)
#df_Worldwide.plot(kind='line',x='date_rep',y="confirmed_change",grid=True)
#df_Worldwide.plot(kind='line',x='date_rep',y="deaths_change",grid=True)

```

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

Exemple non disponible pour cette combinaison de plateforme et de package.

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet"
blob_sas_token = r""

# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)

# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')

# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

---

### <a name="azure-synapse"></a>Azure Synapse

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet"
blob_sas_token = r""

# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)

# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')

# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

---

## <a name="examples"></a>Exemples

Voici des exemples d’utilisation pour ce jeu de données :

- [Analyser les données COVID avec un point de terminaison serverless Synapse SQL](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/how-azure-synapse-analytics-helps-you-analyze-covid-data-with/ba-p/1457836)
- [Analyse de la régression linéaire des données COVID à l’aide d’un point de terminaison SQL dans Azure Synapse Analytics](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/linear-regression-analysis-on-covid-data-using-sql-endpoint-in/ba-p/1468697)

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres jeux de données du [catalogue Open Datasets](dataset-catalog.md).
