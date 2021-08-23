---
title: Bing COVID-19
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données Bing COVID-19 dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 7d797e169a0f1fbeeceaf377e731a051112c68e3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452296"
---
# <a name="bing-covid-19"></a>Bing COVID-19

Les données COVID-19 de Bing incluent les cas confirmés, décédés et guéris de toutes les régions, mis à jour quotidiennement.
Ces données sont reflétées dans le suivi [Bing COVID-19 Tracker](https://bing.com/covid).

Bing collecte des données à partir de plusieurs sources fiables et approuvées, notamment l’[Organisation mondiale de la santé (OMS)](https://www.who.int/emergencies/diseases/novel-coronavirus-2019), les [centres CDC (Centers for Disease Control and Prevention)](https://www.cdc.gov/coronavirus/2019-ncov/index.html), les services de santé publique nationaux et d’état, [BNO News](https://bnonews.com/index.php/2020/04/the-latest-coronavirus-cases/), [24/7 Wall Street](https://247wallst.com/) et [Wikipedia](https://en.wikipedia.org/wiki/2019%E2%80%9320_coronavirus_pandemic).

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>Groupes de données
Les jeux de données modifiés sont disponibles aux formats CSV, JSON, JSON-Lines et Parquet.

- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet

Tous les jeux de données modifiés se sont vu ajouter des codes de subdivision ISO 3166 et des temps de chargement, et utilisent des noms de colonnes en minuscules avec des séparateurs de soulignement.

Données brutes : https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/bing_covid-19_data/latest/Bing-COVID19-Data.csv

Versions précédentes des données modifiées et brutes : https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/

## <a name="data-volume"></a>Volume de données
Tous les jeux de données sont mis à jour quotidiennement. Au 11 mai 2020, ils contenaient 125 576 lignes (CSV 1,1 Mo, JSON 40 Mo, JSONL 39,60 Mo, Parquet 1,1 Mo).

## <a name="license-and-use-rights-attribution"></a>Attribution des licences et des droits d’utilisation
Ces données sont disponibles exclusivement à des fins éducatives et académiques, telles que la recherche médicale, les organismes gouvernementaux et les établissements scolaires, dans le cadre des [conditions générales](https://github.com/microsoft/Bing-COVID-19-Data/blob/master/LICENSE.txt).

Les données utilisées ou citées dans des publications doivent inclure une attribution à « Bing COVID-19 Tracker » avec un lien vers www.bing.com/covid.

## <a name="contact"></a>Contact
Pour toute question ou commentaire sur ce jeu de données ou d’autres jeux de données dans le Data Lake COVID-19, veuillez contacter askcovid19dl@microsoft.com.

## <a name="columns"></a>Colonnes

| Nom             | Type de données | Unique    | Valeurs (exemple)                    | Description                                                          |
|------------------|-----------|-----------|------------------------------------|----------------------------------------------------------------------|
| admin_region_1   | string    | 864       | Texas Géorgie                      | Région dans country_region                                         |
| admin_region_2   | string    | 3,143     | Comté de Washington Comté de Jefferson | Région dans admin_region_1                                         |
| confirmed        | int       | 120 692   | 1 2                                | Nombre de cas confirmés pour la région                                  |
| confirmed_change | int       | 12 120    | 1 2                                | Différence de cas confirmés par rapport au jour précédent                 |
| country_region   | string    | 237       | États-Unis Inde                | Pays/région                                                       |
| deaths           | int       | 20 616    | 1 2                                | Nombre de décès pour la région                                      |
| deaths_change    | SMALLINT  | 1 981     | 1 2                                | Différence de nombre de décès par rapport au jour précédent                          |
| id               | int       | 1 783 534 | 742546 69019298                    | Identificateur unique                                                    |
| iso_subdivision  | string    | 484       | US-TX US-GA                        | Codes de subdivision ISO en deux parties                                        |
| iso2             | string    | 226       | US-IN                              | Code pays à 2 lettres                                     |
| iso3             | string    | 226       | USA IND                            | Code pays à 3 lettres                                     |
| latitude         | double    | 5 675     | 42.28708 19.59852                  | Latitude du centroïde de la région                               |
| load_time        | timestamp | 1         | 2021-04-26 00:06:34.719000         | Date et heure du chargement du fichier à partir de la source Bing sur GitHub |
| longitude        | double    | 5 693     | -2,5396 -155,5186                  | Longitude du centroïde de la région                              |
| recovered        | int       | 73 287    | 1 2                                | Nombre de personnes guéries pour la région                                       |
| recovered_change | int       | 10 441    | 1 2                                | Différence de nombre de personnes guéries par rapport au jour précédent                 |
| date de mise à jour          | Date      | 457       | 2021-04-23 2021-04-22              | Date de l’enregistrement                                        |

## <a name="preview"></a>Préversion

| id     | date de mise à jour    | confirmed | deaths | iso2 | iso3 | country_region | admin_region_1 | iso_subdivision | admin_region_2 | load_time             | confirmed_change | deaths_change |
|--------|------------|-----------|--------|------|------|----------------|----------------|-----------------|----------------|-----------------------|------------------|---------------|
| 338995 | 2020-01-21 | 262       | 0      | null | null | Monde entier      | null           | null            | null           | 26/04/2021 00:06:34 |                  |               |
| 338996 | 2020-01-22 | 313       | 0      | null | null | Monde entier      | null           | null            | null           | 26/04/2021 00:06:34 | 51               | 0             |
| 338997 | 23-01-2020 | 578       | 0      | null | null | Monde entier      | null           | null            | null           | 26/04/2021 00:06:34 | 265              | 0             |
| 338998 | 2020-01-24 | 841       | 0      | null | null | Monde entier      | null           | null            | null           | 26/04/2021 00:06:34 | 263              | 0             |
| 338999 | 2020-01-25 | 1320      | 0      | null | null | Monde entier      | null           | null            | null           | 26/04/2021 00:06:34 | 479              | 0             |
| 339000 | 2020-01-26 | 2014      | 0      | null | null | Monde entier      | null           | null            | null           | 26/04/2021 00:06:34 | 694              | 0             |
| 339001 | 2020-01-27 | 2798      | 0      | null | null | Monde entier      | null           | null            | null           | 26/04/2021 00:06:34 | 784              | 0             |
| 339002 | 2020-01-28 | 4593      | 0      | null | null | Monde entier      | null           | null            | null           | 26/04/2021 00:06:34 | 1795             | 0             |
| 339003 | 2020-01-29 | 6065      | 0      | null | null | Monde entier      | null           | null            | null           | 26/04/2021 00:06:34 | 1472             | 0             |
| 339004 | 30-01-2020 | 7818      | 0      | null | null | Monde entier      | null           | null            | null           | 26/04/2021 00:06:34 | 1753             | 0             |

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=bing-covid-19-data -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=bing-covid-19-data)** .

#### <a name="this-notebook-documents-the-urls-and-sample-code-to-access-the-bing-covid-19-dataset"></a>Ce bloc-notes documente les URL et les exemples de code pour accéder au [jeu de données Bing COVID-19](https://github.com/microsoft/Bing-COVID-19-Data)

Utilisez les URL suivantes pour récupérer des formats de fichier spécifiques hébergés sur des objets Blob Azure Stockage :

CSV : https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.csv

JSON : https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.json

JSONL : https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.jsonl

Parquet : https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet

Téléchargez le fichier de jeu de données à l’aide de la capacité intégrée permettant de télécharger à partir d’une URL HTTP dans Pandas. Pandas comprend des visionneuses pour différents formats de fichiers :

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html


```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet")
df.head(10)
```

Vérifions les types de données des différents champs et vérifions que la colonne mise à jour est au format DateHeure

```python
df.dtypes
```

Nous allons maintenant examiner les données mondiales et tracer des graphiques simples pour visualiser les données

```python
df_Worldwide=df[df['country_region']=='Worldwide']
```

```python
df_Worldwide_pivot=df_Worldwide.pivot_table(df_Worldwide, index=['country_region','updated'])

df_Worldwide_pivot
```

```python
df_Worldwide.plot(kind='line',x='updated',y="confirmed",grid=True)
df_Worldwide.plot(kind='line',x='updated',y="deaths",grid=True)
df_Worldwide.plot(kind='line',x='updated',y="confirmed_change",grid=True)
df_Worldwide.plot(kind='line',x='updated',y="deaths_change",grid=True)
```

<!-- nbend -->

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

Exemple non disponible pour cette combinaison de plateforme et de package.

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=bing-covid-19-data -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=bing-covid-19-data)** .

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet"
blob_sas_token = r""
```

```python
# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)
```

```python
# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')
```

```python
# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

<!-- nbend -->

---

### <a name="azure-synapse"></a>Azure Synapse

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=bing-covid-19-data -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=bing-covid-19-data)** .

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet"
blob_sas_token = r""
```

```python
# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)
```

```python
# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')
```

```python
# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

<!-- nbend -->

---

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres jeux de données du [catalogue Open Datasets](dataset-catalog.md).
