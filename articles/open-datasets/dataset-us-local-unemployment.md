---
title: US Local Area Unemployment Statistics (Statistiques sur le chômage local aux États-Unis)
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données des statistiques de chômage du réseau local des États-Unis dans les jeux de données Azure Open.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 13af967282d1f9be8f289612936ab7d33a9fce11
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038604"
---
# <a name="us-local-area-unemployment-statistics"></a>US Local Area Unemployment Statistics (Statistiques sur le chômage local aux États-Unis)

Le programme LAUS (de statistiques sur le chômage au niveau local) produit des données mensuelles et annuelles sur l’emploi, le chômage et la population active pour les régions et divisions de recensement, les États, les comtés, les régions métropolitaines et de nombreuses villes des États-Unis.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

Le fichier [README](https://download.bls.gov/pub/time.series/la/la.txt) contenant des informations détaillées sur ce jeu de données est disponible à l’[emplacement d’origine du jeu de données](https://download.bls.gov/pub/time.series/la/).

Ce jeu de données provient des [données Local Area Unemployment Statistics](https://www.bls.gov/lau/) publiées par le [Bureau of Labor Statistics (BLS) aux États-Unis](https://www.bls.gov/). Consultez les [informations de liaison et de copyright](https://www.bls.gov/bls/linksite.htm) et les [avis importants de site web](https://www.bls.gov/bls/website-policies.htm) pour connaître les conditions générales relatives à l’utilisation de ce jeu de données.

## <a name="storage-location"></a>Emplacement de stockage

Ce jeu de données est stocké dans la région Azure USA Est. L’allocation de ressources de calcul dans la région USA Est est recommandée à des fins d’affinité.

## <a name="related-datasets"></a>Jeux de données associés

- [US National Employment Hours and Earnings (Heures d’emploi et revenus nationaux aux États-Unis)](dataset-us-national-employment-earnings.md)
- [US National Employment Hours and Earnings (Heures d’emploi et revenus au niveau des États aux États-Unis)](dataset-us-state-employment-earnings.md)
- [US Labor Force Statistics (Statistiques de la population active américaine)](dataset-us-labor-force.md)

## <a name="columns"></a>Colonnes

| Nom | Type de données | Unique | Valeurs (échantillon) | Description |
|-|-|-|-|-|
| area_code | string | 8 290 | ST3400000000000 RD8200000000000 | Code identifiant la zone géographique. Consultez https://download.bls.gov/pub/time.series/la/la.area. |
| area_text | string | 8 238 | Oregon, District de Columbia | Nom de la zone géographique. Consultez https://download.bls.gov/pub/time.series/la/la.area |
| area_type_code | string | 14 | F G | Code unique définissant le type de zone. Consultez https://download.bls.gov/pub/time.series/la/la.area_type |
| areatype_text | string | 14 | Comtés et villes équivalentes au-dessus de la population 25 000 | Nom du type de zone. |
| footnote_codes | string | 5 | nan P |  |
| measure_code | string | 4 | 5 4 | Code identifiant l’élément mesuré. 03 : taux de chômage, 04 : chômage, 05 : emploi, 06 : main-d’œuvre. Consultez https://download.bls.gov/pub/time.series/la/la.measure. |
| measure_text | string | 4 | emploi chômage | Nom de l’élément mesuré. Consultez https://download.bls.gov/pub/time.series/la/la.measure |
| period | string | 13 | M07 M05 | Identifie la période, généralement le mois. Consultez https://download.bls.gov/pub/time.series/la/la.period |
| saisonnier | string | 2 | U S |  |
| series_id | string | 33 476 | LASST160000000000006 LASST200000000000006 | Code identifiant la série. Voir https://download.bls.gov/pub/time.series/la/la.series pour la liste complète de séries. |
| series_title | string | 33 268 | Emploi : Philadelphie comté/ville, PA (U) Labor Force: Manassas city, VA (U) | Titre identifiant la série. Voir https://download.bls.gov/pub/time.series/la/la.series pour la liste complète de séries. |
| srd_code | string | 53 | 48 23 | Code de division, État ou région. |
| srd_text | string | 53 | Texas Maine |  |
| valeur | float | 600 099 | 4.0 5.0 | Valeur de la mesure spécifique. |
| year | int | 44 | 2009 2008 |  |

## <a name="preview"></a>Préversion

| area_code | area_type_code | srd_code | measure_code | series_id | year | period | valeur | footnote_codes | saisonnier | series_title | measure_text | srd_text | areatype_text | area_text |
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M01 | 4,7 | NaN | U | Taux de chômage : Syracuse-Auburn, NY Combined Statistical Area (U) | taux de chômage | New York | Combined areas | Syracuse-Auburn, NY Combined Statistical Area |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M02 | 4,7 | NaN | U | Taux de chômage : Syracuse-Auburn, NY Combined Statistical Area (U) | taux de chômage | New York | Combined areas | Syracuse-Auburn, NY Combined Statistical Area |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M03 | 4,2 | NaN | U | Taux de chômage : Syracuse-Auburn, NY Combined Statistical Area (U) | taux de chômage | New York | Combined areas | Syracuse-Auburn, NY Combined Statistical Area |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M04 | 3.6 | NaN | U | Taux de chômage : Syracuse-Auburn, NY Combined Statistical Area (U) | taux de chômage | New York | Combined areas | Syracuse-Auburn, NY Combined Statistical Area |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M05 | 3.6 | NaN | U | Taux de chômage : Syracuse-Auburn, NY Combined Statistical Area (U) | taux de chômage | New York | Combined areas | Syracuse-Auburn, NY Combined Statistical Area |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M06 | 3.6 | NaN | U | Taux de chômage : Syracuse-Auburn, NY Combined Statistical Area (U) | taux de chômage | New York | Combined areas | Syracuse-Auburn, NY Combined Statistical Area |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M07 | 3.6 | NaN | U | Taux de chômage : Syracuse-Auburn, NY Combined Statistical Area (U) | taux de chômage | New York | Combined areas | Syracuse-Auburn, NY Combined Statistical Area |

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[Téléchargez le notebook à la place](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-local-area-unemployment-statistics)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborLAUS

usLaborLAUS = UsLaborLAUS()
usLaborLAUS_df = usLaborLAUS.to_pandas_dataframe()
```

```python
usLaborLAUS_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[Téléchargez le notebook à la place](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-local-area-unemployment-statistics)** .

```python
# Pip install packages
import os, sys

!{sys.executable} -m pip install azure-storage-blob
!{sys.executable} -m pip install pyarrow
!{sys.executable} -m pip install pandas
```

```python
# Azure storage access info
azure_storage_account_name = "azureopendatastorage"
azure_storage_sas_token = r""
container_name = "laborstatisticscontainer"
folder_name = "laus/"
```

```python
from azure.storage.blob import BlockBlobServicefrom azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

if azure_storage_account_name is None or azure_storage_sas_token is None:
    raise Exception(
        "Provide your specific name and key for your Azure Storage account--see the Prerequisites section earlier.")

print('Looking for the first parquet under the folder ' +
      folder_name + ' in container "' + container_name + '"...')
container_url = f"https://{azure_storage_account_name}.blob.core.windows.net/"
blob_service_client = BlobServiceClient(
    container_url, azure_storage_sas_token if azure_storage_sas_token else None)

container_client = blob_service_client.get_container_client(container_name)
blobs = container_client.list_blobs(folder_name)
sorted_blobs = sorted(list(blobs), key=lambda e: e.name, reverse=True)
targetBlobName = ''
for blob in sorted_blobs:
    if blob.name.startswith(folder_name) and blob.name.endswith('.parquet'):
        targetBlobName = blob.name
        break

print('Target blob to download: ' + targetBlobName)
_, filename = os.path.split(targetBlobName)
blob_client = container_client.get_blob_client(targetBlobName)
with open(filename, 'wb') as local_file:
    blob_client.download_blob().download_to_stream(local_file)
```

```python
# Read the parquet file into Pandas data frame
import pandas as pd

print('Reading the parquet file into Pandas data frame')
df = pd.read_parquet(filename)
```

```python
# you can add your filter at below
print('Loaded as a Pandas data frame: ')
df
```

<!-- nbend -->

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

Échantillon non disponible pour cette combinaison plateforme/package.

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[Téléchargez le notebook à la place](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-local-area-unemployment-statistics)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborLAUS

usLaborLAUS = UsLaborLAUS()
usLaborLAUS_df = usLaborLAUS.to_spark_dataframe()
```

```python
display(usLaborLAUS_df.limit(5))
```

<!-- nbend -->

 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Échantillon non disponible pour cette combinaison plateforme/package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[Téléchargez le notebook à la place](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-local-area-unemployment-statistics)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "laus/"
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

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

Échantillon non disponible pour cette combinaison plateforme/package.

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Échantillon non disponible pour cette combinaison plateforme/package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[Téléchargez le notebook à la place](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-local-area-unemployment-statistics)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "laus/"
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

Affichez les autres jeux de données dans le [catalogue Open Datasets](dataset-catalog.md).