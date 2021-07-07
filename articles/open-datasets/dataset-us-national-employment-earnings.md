---
title: US National Employment Hours and Earnings (Heures d’emploi et revenus nationaux aux États-Unis)
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données US National Employment Hours and Earnings dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: ffbd29a3aa19b999c9202a0cea36cafee6a4c5f4
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038603"
---
# <a name="us-national-employment-hours-and-earnings"></a>US National Employment Hours and Earnings (Heures d’emploi et revenus nationaux aux États-Unis)

Le programme Current Employment Statistics (CES) produit des estimations détaillées de l’emploi non agricole, des heures de travail et des revenus des travailleurs salariés aux États-Unis.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

Un fichier [LISEZ-MOI](https://download.bls.gov/pub/time.series/ce/ce.txt) contenant des informations détaillées sur ce jeu de données est disponible à l’[emplacement du jeu de données d’origine](https://download.bls.gov/pub/time.series/ce/).

Ce jeu de données est produit à partir des [données Current Employment Statistics - CES](https://www.bls.gov/ces/) publiées par le [US Bureau of Labor Statistics (BLS)](https://www.bls.gov/). Consultez les [informations de liaison et de copyright](https://www.bls.gov/bls/linksite.htm) et les [avis importants de site web](https://www.bls.gov/bls/website-policies.htm) pour connaître les conditions générales relatives à l’utilisation de ce jeu de données.

## <a name="storage-location"></a>Emplacement de stockage

Ce jeu de données est stocké dans la région Azure USA Est. L’allocation de ressources de calcul dans la région USA Est est recommandée à des fins d’affinité.

## <a name="related-datasets"></a>Jeux de données associés

- [US National Employment Hours and Earnings (Heures d’emploi et revenus au niveau des États aux États-Unis)](dataset-us-state-employment-earnings.md)
- [US Local Area Unemployment Statistics (Statistiques sur le chômage local aux États-Unis)](dataset-us-local-unemployment.md)
- [US Labor Force Statistics (Statistiques de la population active américaine)](dataset-us-labor-force.md)

## <a name="columns"></a>Colonnes

| Nom | Type de données | Unique | Valeurs (exemple) | Description |
|-|-|-|-|-|
| data_type_code | string | 37 | 1 10 | Voir https://download.bls.gov/pub/time.series/ce/ce.datatype |
| data_type_text | string | 37 | TOUS LES EMPLOYÉS, MILLIERS DE FEMMES EMPLOYÉS, MILLIERS | Voir https://download.bls.gov/pub/time.series/ce/ce.datatype |
| footnote_codes | string | 2 | nan P |  |
| industry_code | string | 902 | $30000000 32000000 | Différents secteurs couverts. Consultez https://download.bls.gov/pub/time.series/ce/ce.industry |
| industry_name | string | 895 | Biens non durables biens durables | Différents secteurs couverts. Consultez https://download.bls.gov/pub/time.series/ce/ce.industry |
| period | string | 13 | M03 M06 | Voir https://download.bls.gov/pub/time.series/ce/ce.period |
| saisonnier | string | 2 | U S |  |
| series_id | string | 26 021 | CEU3100000008 CEU9091912001 | Différents types de séries de données disponibles dans le jeu de données. Consultez https://download.bls.gov/pub/time.series/ce/ce.series |
| series_title | string | 25 685 | Tous les employés, milliers, biens durables, Tous les employés non ajusté de façon saisonnière, milliers, biens non durables, non ajusté de façon saisonnière | Titre des différents types de séries de données disponibles dans le jeu de données. Consultez https://download.bls.gov/pub/time.series/ce/ce.series |
| supersector_code | string | 22 | 31 60 | Classification d’industrie ou de secteur de niveau supérieur. Voir https://download.bls.gov/pub/time.series/ce/ce.supersector |
| supersector_name | string | 22 | Services professionnels et commerciaux de biens durables | Classification d’industrie ou de secteur de niveau supérieur. Voir https://download.bls.gov/pub/time.series/ce/ce.supersector |
| valeur | float | 572 372 | 38,5 38,400001525878906 |  |
| year | int | 81 | 2017 2012 |  |

## <a name="preview"></a>Préversion

| data_type_code | industry_code | supersector_code | series_id | year | period | valeur | footnote_codes | saisonnier | series_title | supersector_name | industry_name | data_type_text |
|-|-|-|-|-|-|-|-|-|-|-|-|-|
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M04 | 52 | NaN | S | Tous les employés, modification moyenne sur 3 mois, ajusté de façon saisonnière, milliers, total privé, ajusté de façon saisonnière | Total privé | Total privé | TOUS LES EMPLOYÉS, MODIFICATION MOYENNE SUR 3 MOIS, AJUSTÉ DE FAÇON SAISONNIÈRE, MILLIERS |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M05 | 65 | NaN | S | Tous les employés, modification moyenne sur 3 mois, ajusté de façon saisonnière, milliers, total privé, ajusté de façon saisonnière | Total privé | Total privé | TOUS LES EMPLOYÉS, MODIFICATION MOYENNE SUR 3 MOIS, AJUSTÉ DE FAÇON SAISONNIÈRE, MILLIERS |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M06 | 74 | NaN | S | Tous les employés, modification moyenne sur 3 mois, ajusté de façon saisonnière, milliers, total privé, ajusté de façon saisonnière | Total privé | Total privé | TOUS LES EMPLOYÉS, MODIFICATION MOYENNE SUR 3 MOIS, AJUSTÉ DE FAÇON SAISONNIÈRE, MILLIERS |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M07 | 103 | NaN | S | Tous les employés, modification moyenne sur 3 mois, ajusté de façon saisonnière, milliers, total privé, ajusté de façon saisonnière | Total privé | Total privé | TOUS LES EMPLOYÉS, MODIFICATION MOYENNE SUR 3 MOIS, AJUSTÉ DE FAÇON SAISONNIÈRE, MILLIERS |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M08 | 108 | NaN | S | Tous les employés, modification moyenne sur 3 mois, ajusté de façon saisonnière, milliers, total privé, ajusté de façon saisonnière | Total privé | Total privé | TOUS LES EMPLOYÉS, MODIFICATION MOYENNE SUR 3 MOIS, AJUSTÉ DE FAÇON SAISONNIÈRE, MILLIERS |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M09 | 152 | NaN | S | Tous les employés, modification moyenne sur 3 mois, ajusté de façon saisonnière, milliers, total privé, ajusté de façon saisonnière | Total privé | Total privé | TOUS LES EMPLOYÉS, MODIFICATION MOYENNE SUR 3 MOIS, AJUSTÉ DE FAÇON SAISONNIÈRE, MILLIERS |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M10 | 307 | NaN | S | Tous les employés, modification moyenne sur 3 mois, ajusté de façon saisonnière, milliers, total privé, ajusté de façon saisonnière | Total privé | Total privé | TOUS LES EMPLOYÉS, MODIFICATION MOYENNE SUR 3 MOIS, AJUSTÉ DE FAÇON SAISONNIÈRE, MILLIERS |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M11 | 248 | NaN | S | Tous les employés, modification moyenne sur 3 mois, ajusté de façon saisonnière, milliers, total privé, ajusté de façon saisonnière | Total privé | Total privé | TOUS LES EMPLOYÉS, MODIFICATION MOYENNE SUR 3 MOIS, AJUSTÉ DE FAÇON SAISONNIÈRE, MILLIERS |

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[Téléchargez le notebook à la place](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-national)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborEHENational

usLaborEHENational = UsLaborEHENational()
usLaborEHENational_df = usLaborEHENational.to_pandas_dataframe()
```

```python
usLaborEHENational_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[Téléchargez le notebook à la place](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-employment-hours-earnings-national)** .

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
folder_name = "ehe_national/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[Téléchargez le notebook à la place](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-national)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborEHENational

usLaborEHENational = UsLaborEHENational()
usLaborEHENational_df = usLaborEHENational.to_spark_dataframe()
```

```python
display(usLaborEHENational_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[Téléchargez le notebook à la place](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-employment-hours-earnings-national)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ehe_national/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[Téléchargez le notebook à la place](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-employment-hours-earnings-national)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ehe_national/"
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