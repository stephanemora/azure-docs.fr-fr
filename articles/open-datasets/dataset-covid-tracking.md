---
title: Projet de suivi du COVID
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données de suivi COVID-19 dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 55814a6b1b78673c20447d6129f609058d5c794f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453699"
---
# <a name="covid-tracking-project"></a>Projet de suivi COVID

Le jeu de données du projet COVID Tracking fournit les derniers chiffres sur les tests, les cas confirmés, les hospitalisations et l’état des patients pour chaque État américain et l’ensemble du territoire.

Pour plus d’informations sur ce dataset, consultez le [référentiel GitHub de projet](https://github.com/COVID19Tracking/covid-tracking-data).

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>Groupes de données

Des versions modifiées du jeu de données sont disponibles aux formats CSV, JSON, JSON-Lines et Parquet.
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.parquet

Toutes les versions modifiées du jeu de données se sont vu ajouter des codes de subdivision ISO 3166 et des temps de chargement, et utilisent des noms de colonnes en minuscules avec des séparateurs de soulignement.

Données brutes : « https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_tracking/latest/daily.json  »

Versions précédentes des données modifiées et brutes : https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/

https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_tracking/

## <a name="data-volume"></a>Volume de données
Tous les jeux de données sont mis à jour quotidiennement. Au 13 mai 2020, ils contenaient 4 100 lignes (CSV 574 Ko, JSON 1,8 Mo, JSONL 1,8 Mo, Parquet 334 Ko).

## <a name="data-source"></a>Paramètres

Ces données sont à l’origine publiées par le COVID Tracking Project at the Atlantic. Les données brutes sont ingérées à partir du référentiel GitHub du COVID à l’aide du [states_daily_4p_et.csv file](https://github.com/COVID19Tracking/covid-tracking-data/blob/master/data/states_daily_4pm_et.csv). Pour plus d’informations sur ce jeu de données, y compris son origine à partir de l’API de suivi COVID, consultez le [référentiel GitHub du projet](https://github.com/COVID19Tracking/covid-tracking-data).

## <a name="data-quality"></a>Qualité des données
Le projet COVID Tracking note la qualité des données pour chaque État et fournit d’autres informations sur l’évaluation de la qualité des données. Pour plus d’informations, consultez la [page de projet des données de suivi de COVID](https://covidtracking.com/data). Les données du dépôt GitHub peuvent avoir une heure de retard sur l’API ; l’utilisation de l’API est nécessaire pour accéder aux données les plus récentes.

## <a name="license-and-use-rights-attribution"></a>Attribution des licences et des droits d’utilisation

Ces données sont concédées sous licence selon les termes et conditions de la [licence Apache 2.0](https://github.com/COVID19Tracking/covid-tracking-data/blob/master/LICENSE).

Toute utilisation des données doit conserver tous les avis de droit d’auteur, de brevet, de marque et d’attribution.

## <a name="contact"></a>Contact

Pour toute question ou tout commentaire sur ce jeu de données ou sur d’autres jeux de données dans le lac de données COVID-19, contactez askcovid19dl@microsoft.com.

## <a name="columns"></a>Colonnes

| Nom                        | Type de données | Unique | Valeurs (exemple)                                                                   | Description                                                                                                                 |
|-----------------------------|-----------|--------|-----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|
| Date                        | Date      | 420    | 2020-11-10 2021-01-30                                                             | Date pour laquelle les totaux quotidiens ont été recueillis.                                                                             |
| date_checked                | string    | 9 487  | 2020-12-01T00:00:00Z 2020-09-01T00:00:00Z                                         | Déprécié                                                                                                                  |
| death                       | SMALLINT  | 7 327  | 2 5                                                                               | Nombre total de personnes décédées des suites de la COVID-19 à ce jour.                                                        |
| death_increase              | SMALLINT  | 429    | 1 2                                                                               | Déprécié                                                                                                                  |
| fips                        | SMALLINT  | 56     | 26 55                                                                             | Code FIPS de recensement de l'État.                                                                                             |
| fips_code                   | string    | 60     | 53 25                                                                             | Code FIPS de recensement de l'État.                                                                                             |
| Hachage                        | string    | 20 780 | 63df8cccd23a5476bab2d8111b138e4c9becd35e c606cd6990f16086b5382e12d84f6206172d493d | Code de hachage de cet enregistrement                                                                                                      |
| hospitalized                | int       | 7 641  | 89995 4                                                                           | Déprécié                                                                                                                  |
| hospitalized_cumulative     | int       | 7 641  | 89995 4                                                                           | Nombre total de personnes hospitalisées pour COVID-19 à ce jour (personnes guéries et décédées comprises). |
| hospitalized_currently      | SMALLINT  | 3 886  | 8 13                                                                              | Nombre de malades de la COVID-19 hospitalisés ce jour-là.                                                                      |
| hospitalized_increase       | SMALLINT  | 615    | 1 2                                                                               | Déprécié                                                                                                                  |
| in_icu_cumulative           | SMALLINT  | 2 295  | 990 220                                                                           | Nombre total de malades de la COVID-19 qui ont été hospitalisées en réanimation à ce jour (personnes guéries et décédées comprises).      |
| in_icu_currently            | SMALLINT  | 1 643  | 2 8                                                                               | Nombre total de malades de la COVID-19 hospitalisés en réanimation ce jour-là.                                                                 |
| iso_country                 | string    | 1      | US                                                                                | Code pays ou région ISO 3166                                                                                             |
| iso_subdivision             | string    | 57     | US-UM US-WA                                                                       | Code de sous-division ISO 3166                                                                                                   |
| last_update_et              | timestamp | 9 487  | 2020-12-01 00:00:00 2020-09-01 00:00:00                                           | Dernière mise à jour des données du jour                                                                                        |
| load_time                   | timestamp | 1      | 2021-04-26 00:06:49.883000                                                        | Date et heure auxquelles les données ont été chargées dans Azure à partir de la source                                                                  |
| négatif                    | int       | 10 864 | 305972 2140                                                                       | Nombre total de personnes ayant été testées négatives à la COVID-19 à ce jour.                                                        |
| negative_increase           | int       | 7 328  | 6 17                                                                              | Déprécié                                                                                                                  |
| on_ventilator_cumulative    | SMALLINT  | 677    | 411 412                                                                           | Nombre total de malades de la COVID-19 qui ont été placés sous respirateur à ce jour (personnes guéries et décédées comprises).    |
| on_ventilator_currently     | SMALLINT  | 837    | 4 10                                                                              | Nombre de malades de la COVID-19 placés sous respirateur ce jour-là.                                                               |
| en attente                     | SMALLINT  | 944    | 2 17                                                                              | Nombre de tests dont les résultats restent à déterminer.                                                                    |
| pos_neg                     | int       | 18 282 | 2140 2                                                                            | Déprécié                                                                                                                  |
| positif                    | int       | 16 837 | 2 1                                                                               | Nombre total de personnes ayant été testées positives à la COVID-19 à ce jour.                                                        |
| positive_increase           | SMALLINT  | 4 754  | 1 2                                                                               | Déprécié                                                                                                                  |
| recovered                   | int       | 8 286  | 29 19                                                                             | Nombre total de personnes qui se sont remises de la COVID-19 à ce jour.                                                             |
| state                       | string    | 56     | MI PA                                                                             | Code à deux lettres de l'État.                                                                                              |
| total                       | int       | 18 283 | 2140 2                                                                            | Déprécié                                                                                                                  |
| total_test_results          | int       | 18 648 | 2140 3                                                                            | Nombre total de résultats de test par État                                                                                    |
| total_test_results_increase | int       | 13 463 | 1 2                                                                               | Déprécié                                                                                                                  |

## <a name="preview"></a>Préversion

| Date       | state | positif | hospitalized_currently | hospitalized_cumulative | on_ventilator_currently | data_quality_grade | last_update_et        | hash                                     | date_checked          | death | hospitalized | total   | total_test_results | pos_neg | fips | death_increase | hospitalized_increase | negative_increase | positive_increase | total_test_results_increase | fips_code | iso_subdivision | load_time             | iso_country | négatif | in_icu_cumulative | on_ventilator_cumulative | recovered | in_icu_currently |
|------------|-------|----------|------------------------|-------------------------|-------------------------|--------------------|-----------------------|------------------------------------------|-----------------------|-------|--------------|---------|--------------------|---------|------|----------------|-----------------------|-------------------|-------------------|-----------------------------|-----------|-----------------|-----------------------|-------------|----------|-------------------|--------------------------|-----------|------------------|
| 2021-03-07 | AK    | 56886    | 33                     | 1293                    | 2                       | null               | 05/03/2021 3:59:00   | dc4bccd4bb885349d7e94d6fed058e285d4be164 | 05/03/2021 3:59:00   | 305   | 1293         | 56886   | 1731628            | 56886   | 2    | 0              | 0                     | 0                 | 0                 | 0                           | 2         | US-AK           | 26/04/2021 00:06:49 | US          |          |                   |                          |           |                  |
| 2021-03-07 | AL    | 499819   | 494                    | 45976                   |                         | null               | 07/03/2021 11:00:00  | 997207b430824ea40b8eb8506c19a93e07bc972e | 07/03/2021 11:00:00  | 10148 | 45976        | 2431530 | 2323788            | 2431530 | 1    | -1             | 0                     | 2087              | 408               | 2347                        | 1         | US-AL           | 26/04/2021 00:06:49 | US          | 1931711  | 2676              | 1515                     | 295690    |                  |
| 2021-03-07 | AR    | 324818   | 335                    | 14926                   | 65                      | null               | 07/03/2021 00:00:00  | 50921aeefba3e30d31623aa495b47fb2ecc72fae | 07/03/2021 00:00:00  | 5319  | 14926        | 2805534 | 2736442            | 2805534 | 5    | 22             | 11                    | 3267              | 165               | 3380                        | 5         | US-AR           | 26/04/2021 00:06:49 | US          | 2480716  |                   | 1533                     | 315517    | 141              |
| 2021-03-07 | AS    | 0        |                        |                         |                         | null               | 01/12/2020 00:00:00 | 96d23f888c995b9a7f3b4b864de6414f45c728ff | 01/12/2020 00:00:00 | 0     |              | 2140    | 2140               | 2140    | 60   | 0              | 0                     | 0                 | 0                 | 0                           | 60        | US-AS           | 26/04/2021 00:06:49 | US          | 2140     |                   |                          |           |                  |
| 2021-03-07 | AZ    | 826454   | 963                    | 57907                   | 143                     | null               | 07/03/2021 00:00:00  | 0437a7a96f4471666f775e63e86923eb5cbd8cdf | 07/03/2021 00:00:00  | 16328 | 57907        | 3899464 | 7908105            | 3899464 | 4    | 5              | 44                    | 13678             | 1335              | 45110                       | 4         | US-AZ           | 26/04/2021 00:06:49 | US          | 3073010  |                   |                          |           | 273              |
| 2021-03-07 | CA    | 3501394  | 4291                   |                         |                         | null               | 03/07/2021 2:59:00   | 63c5c0fd2daef2fb65150e9db486de98ed3f7b72 | 03/07/2021 2:59:00   |       |              | 3501394 | 49646014           | 3501394 | 6    | 258            | 0                     | 0                 | 3816              | 133186                      | 6         | US-CA           | 26/04/2021 00:06:49 | US          |          |                   |                          |           | 1159             |
| 2021-03-07 | CO    | 436602   | 326                    | 23904                   |                         | null               | 07/03/2021 1:59:00   | 444746cda3a596f183f3fa3269c8cab68704e819 | 07/03/2021 1:59:00   | 5989  | 23904        | 2636060 | 6415123            | 2636060 | 8    | 3              | 18                    | 0                 | 840               | 38163                       | 8         | US-CO           | 26/04/2021 00:06:49 | US          | 2199458  |                   |                          |           |                  |
| 2021-03-07 | CT    | 285330   | 428                    | 12257                   |                         | null               | 04/03/2021 23:59:00  | bcc0f7bc8c2bf77eec31b25f8b59d510f679d3e7 | 04/03/2021 23:59:00  | 7704  | 12257        | 285330  | 6520366            | 285330  | 9    | 0              | 0                     | 0                 | 0                 | 0                           | 9         | US-CT           | 26/04/2021 00:06:49 | US          |          |                   |                          |           |                  |
| 2021-03-07 | DC    | 41419    | 150                    |                         | 16                      | null               | 06/03/2021 00:00:00  | a3aa0d623d538807fb9577ad64354f48cf728cc8 | 06/03/2021 00:00:00  | 1030  |              | 41419   | 1261363            | 41419   | 11   | 0              | 0                     | 0                 | 146               | 5726                        | 11        | US-DC           | 26/04/2021 00:06:49 | US          |          |                   |                          | 29570     | 38               |
| 2021-03-07 | DE    | 88354    | 104                    |                         |                         | null               | 06/03/2021 18:00:00   | 059d870e689d5cc19c35f5eb398214d7d9856373 | 06/03/2021 18:00:00   | 1473  |              | 633424  | 1431942            | 633424  | 10   | 9              | 0                     | 917               | 215               | 5867                        | 10        | US-DE           | 26/04/2021 00:06:49 | US          | 545070   |                   |                          |           | 13               |

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

URL de différents formats de fichiers de jeux de données hébergés sur Stockage Blob Azure :

CSV : https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.csv

JSON : https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.json

JSONL : https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.jsonl

Parquet : https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.parquet

Téléchargez le fichier de jeu de données à l’aide de la capacité intégrée permettant de télécharger à partir d’une URL HTTP dans Pandas. Pandas comprend des visionneuses pour différents formats de fichiers :

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html

```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.parquet ")
df.head(10)

df.dtypes

df.groupby('state').first().filter(['date','positive', 'death'])

df.groupby(df.state).agg({'state': 'count','positive_increase': 'sum','death_increase': 'sum'})

df_NY=df[df['state'] == 'NY']
df_NY.plot(kind='line',x='date',y="positive",grid=True)
df_NY.plot(kind='line',x='date',y="positive_increase",grid=True)
df_NY.plot(kind='line',x='date',y="death",grid=True)
df_NY.plot(kind='line',x='date',y="death_increase",grid=True)

df_US=df.groupby(df.date).agg({'positive': 'sum','positive_increase': 'sum','death':'sum','death_increase': 'sum'}).reset_index()

df_US.plot(kind='line',x='date',y="positive",grid=True)
df_US.plot(kind='line',x='date',y="positive_increase",grid=True)
df_US.plot(kind='line',x='date',y="death",grid=True)
df_US.plot(kind='line',x='date',y="death_increase",grid=True)



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
blob_relative_path = "curated/covid-19/covid_tracking/latest/covid_tracking.parquet"
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
blob_relative_path = "curated/covid-19/covid_tracking/latest/covid_tracking.parquet"
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

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres jeux de données du [catalogue Open Datasets](dataset-catalog.md).
