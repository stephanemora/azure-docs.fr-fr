---
title: US National Employment Hours and Earnings (Heures d’emploi et revenus au niveau des États aux États-Unis)
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données US State Employment Hours and Earnings dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: a1cb79e704eeae13e8794cad7409e0b945889d22
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038672"
---
# <a name="us-state-employment-hours-and-earnings"></a>US National Employment Hours and Earnings (Heures d’emploi et revenus au niveau des États aux États-Unis)

Le programme Current Employment Statistics (CES) produit des estimations détaillées de l’emploi non agricole, des heures de travail et des revenus des travailleurs salariés aux États-Unis.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

Ce jeu de données est produit à partir des données [State and Metro Area Employment](https://www.bls.gov/sae/), [Hours and Earnings](https://www.bls.gov/sae/) publiées par l’[US Bureau of Labor Statistics (BLS)](https://www.bls.gov/). Consultez les [informations de liaison et de copyright](https://www.bls.gov/bls/linksite.htm) et les [avis importants de site web](https://www.bls.gov/bls/website-policies.htm) pour connaître les conditions générales relatives à l’utilisation de ce jeu de données.

## <a name="storage-location"></a>Emplacement de stockage

Ce jeu de données est stocké dans la région Azure USA Est. L’allocation de ressources de calcul dans la région USA Est est recommandée à des fins d’affinité.

## <a name="related-datasets"></a>Jeux de données associés

- [US National Employment Hours and Earnings (Heures d’emploi et revenus nationaux aux États-Unis)](dataset-us-national-employment-earnings.md)
- [US Local Area Unemployment Statistics (Statistiques sur le chômage local aux États-Unis)](dataset-us-local-unemployment.md)
- [US Labor Force Statistics (Statistiques de la population active américaine)](dataset-us-labor-force.md)

## <a name="columns"></a>Colonnes

| Nom | Type de données | Unique | Valeurs (exemple) |
|-|-|-|-|
| area_code | string | 446 | 0 31084 |
| area_name | string | 446 | À l’échelle de l’état Los Angeles-Long Beach-Glendale, division métropolitaine de Californie |
| data_type_code | string | 9 | 1 3 |
| data_type_text | string | 9 | Tous les employés, en milliers d’heures hebdomadaires moyennes de tous les employés |
| footnote_codes | string | 3 | nan P |
| industry_code | string | 343 | 0 5000000 |
| industry_name | string | 343 | Total non agricole Total privé |
| period | string | 13 | M04 M05 |
| saisonnier | string | 2 | U S |
| series_id | string | 23 853 | SMU12000000000000001 SMU36000000000000001 |
| state_code | string | 53 | 6 48 |
| state_name | string | 53 | Californie Texas |
| supersector_code | string | 22 | 90 60 |
| supersector_name | string | 22 | Services professionnels et commerciaux du gouvernement |
| valeur | float | 132 565 | 0,30000001192092896 0,10000000149011612 |
| year | int | 81 | 2014 2018 |

## <a name="preview"></a>Préversion

| area_code | state_code | data_type_code | industry_code | supersector_code | series_id | year | period | valeur | footnote_codes | saisonnier | supersector_name | industry_name | data_type_text | state_name | area_name |
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M04 | 0.2 | NaN | S | Total non agricole | Total non agricole | Tous les employés, variation moyenne sur trois mois, en milliers, avec correction des variations saisonnières | Oregon | Bend-Redmond, OR |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M05 | 0.2 | NaN | S | Total non agricole | Total non agricole | Tous les employés, variation moyenne sur trois mois, en milliers, avec correction des variations saisonnières | Oregon | Bend-Redmond, OR |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M06 | 0.1 | NaN | S | Total non agricole | Total non agricole | Tous les employés, variation moyenne sur trois mois, en milliers, avec correction des variations saisonnières | Oregon | Bend-Redmond, OR |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M07 | 0.1 | NaN | S | Total non agricole | Total non agricole | Tous les employés, variation moyenne sur trois mois, en milliers, avec correction des variations saisonnières | Oregon | Bend-Redmond, OR |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M08 | 0.2 | NaN | S | Total non agricole | Total non agricole | Tous les employés, variation moyenne sur trois mois, en milliers, avec correction des variations saisonnières | Oregon | Bend-Redmond, OR |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M09 | 0.2 | NaN | S | Total non agricole | Total non agricole | Tous les employés, variation moyenne sur trois mois, en milliers, avec correction des variations saisonnières | Oregon | Bend-Redmond, OR |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M10 | 0.1 | NaN | S | Total non agricole | Total non agricole | Tous les employés, variation moyenne sur trois mois, en milliers, avec correction des variations saisonnières | Oregon | Bend-Redmond, OR |

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-state)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborEHEState

usLaborEHEState = UsLaborEHEState()
usLaborEHEState_df = usLaborEHEState.to_pandas_dataframe()
```

```python
usLaborEHEState_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-employment-hours-earnings-state)** .

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
folder_name = "ehe_state/"
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

Exemple non disponible pour cette combinaison de plateforme et de package.

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-state)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborEHEState

usLaborEHEState = UsLaborEHEState()
usLaborEHEState_df = usLaborEHEState.to_spark_dataframe()
```

```python
display(usLaborEHEState_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-employment-hours-earnings-state)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ehe_state/"
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

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-employment-hours-earnings-state)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ehe_state/"
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