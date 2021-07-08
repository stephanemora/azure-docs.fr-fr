---
title: Indice américain des prix à la consommation
titleSuffix: Azure Open Datasets
description: Découvrez l’utilisation du jeu de données de l’Indice américain des prix à la consommation dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: fffe6b1521f3f9ab4973a64f03ef52e4a9019658
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038606"
---
# <a name="us-consumer-price-index"></a>Indice américain des prix à la consommation

L’indice des prix à la consommation (IPC) est une mesure de la variation moyenne dans le temps des prix payés par les consommateurs urbains pour un panier de biens et services à la consommation.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

Le fichier [README](https://download.bls.gov/pub/time.series/cu/cu.txt) contenant des informations détaillées sur ce jeu de données est disponible à l’[emplacement d’origine du jeu de données](https://download.bls.gov/pub/time.series/cu/).

Ce jeu de données est produit à partir des [données de l’Indice des prix à la consommation](https://www.bls.gov/cpi/) publiées par le [Bureau of Labor Statistics (BLS) des États-Unis](https://www.bls.gov/). Consultez [Informations relatives aux liaisons et aux droits d’auteur](https://www.bls.gov/bls/linksite.htm) et [Remarques importantes relatives au site web](https://www.bls.gov/bls/website-policies.htm) pour connaître les conditions générales.

## <a name="storage-location"></a>Emplacement de stockage

Ce jeu de données est stocké dans la région Azure USA Est. Nous vous recommandons de placer les ressources de calcul dans la région USA Est à des fins d’affinité.

## <a name="related-datasets"></a>Jeux de données associés

- [Indice des prix producteur aux États-Unis - Industrie](dataset-us-producer-price-index-industry.md)
- [Indice des prix à la production aux États-Unis - Marchandises](dataset-us-producer-price-index-commodities.md)

## <a name="columns"></a>Colonnes

| Nom | Type de données | Unique | Valeurs (échantillon) | Description |
|-|-|-|-|-|
| area_code | string | 70 | 0000 0300 | Code unique pour identifier une zone géographique spécifique. Les codes de zone complets se trouvent ici : http://download.bls.gov/pub/time.series/cu/cu.area |
| area_name | string | 69 | Moyenne des villes américaines du Sud | Nom de la zone géographique spécifique. Voir https://download.bls.gov/pub/time.series/cu/cu.area pour tous les noms et codes de zones. |
| footnote_codes | string | 3 | nan U | Identifie la note de bas de page pour la série de données. La plupart des valeurs sont Null. |
| item_code | string | 515 | SA0E SAF11 | Identifie l’élément auquel les observations de données se rapportent. Voir https://download.bls.gov/pub/time.series/cu/cu.item pour tous les noms et codes d’éléments. |
| item_name | string | 515 | Energy Food at home | Noms complets des éléments. Voir https://download.bls.gov/pub/time.series/cu/cu.txt pour les noms et codes d’éléments. |
| period | string | 16 | S01 S02 | Identifie la période pendant laquelle les données sont observées. Format : M01-M13 ou S01-S03 (M=Mensuel, M13=Moy. annuelle, S=Semi-annuel). Exemple : M06=June. Consultez https://download.bls.gov/pub/time.series/cu/cu.period pour les noms et codes de période. |
| periodicity_code | string | 3 | R S | Fréquence d’observation des données. S=Semi-annuelle; R=Régulière. |
| saisonnier | string | 1 043 | U S | Code identifiant si les données sont corrigées en fonction des variations saisonnières. S=Corrigées en fonction des variations saisonnières ; U=Non corrigées |
| series_id | string | 16 683 | CWURS400SA0E CWUR0100SA0E | Code identifiant les séries spécifiques. Une série chronologique fait référence à un jeu contenant des données observées sur une période de temps étendue avec des intervalles cohérents (par exemple mensuel, trimestriel, semi-annuel ou annuel). Les données de série chronologique BLS sont généralement produites à intervalles mensuels et représentent des données allant d’un produit de consommation spécifique dans une zone géographique spécifique dont le prix est collecté mensuellement à une catégorie de travailleurs d’un secteur spécifique dont le taux d’emploi est enregistré mensuellement, etc. Pour plus d'informations, consultez https://download.bls.gov/pub/time.series/cu/cu.txt |
| series_title | string | 8 336 | Boissons alcoolisées dans la moyenne des villes aux États-Unis, tous les consommateurs urbains, pas de transport ajusté par saison à Los Angeles-Long Beach-Anaheim, Californie, tous les consommateurs urbains, pas ajusté par saison | Nom de série du series_id correspondant. Voir https://download.bls.gov/pub/time.series/cu/cu.series pour les ID et noms de série. |
| valeur | float | 310 603 | 100,0 101,0999984741211 | Indice des prix pour l’élément. |
| year | int | 25 | 2018 2017 | Identifie l’année d’observation. |

## <a name="preview"></a>Préversion

| area_code | item_code | series_id | year | period | valeur | footnote_codes | saisonnier | periodicity_code | series_title | item_name | area_name |
|-|-|-|-|-|-|-|-|-|-|-|-|
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279,974 | NaN | U | R | Électricité à San Diego-Carlsbad, Californie, tous les consommateurs urbains, non ajustés par saison | Électricité | San Diego-Carlsbad, Californie |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279,974 | NaN | U | R | Électricité à San Diego-Carlsbad, Californie, tous les consommateurs urbains, non ajustés par saison | Électricité | San Diego-Carlsbad, Californie |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279,974 | NaN | U | R | Électricité à San Diego-Carlsbad, Californie, tous les consommateurs urbains, non ajustés par saison | Électricité | San Diego-Carlsbad, Californie |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279,974 | NaN | U | R | Électricité à San Diego-Carlsbad, Californie, tous les consommateurs urbains, non ajustés par saison | Électricité | San Diego-Carlsbad, Californie |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279,974 | NaN | U | R | Électricité à San Diego-Carlsbad, Californie, tous les consommateurs urbains, non ajustés par saison | Électricité | San Diego-Carlsbad, Californie |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279,974 | NaN | U | R | Électricité à San Diego-Carlsbad, Californie, tous les consommateurs urbains, non ajustés par saison | Électricité | San Diego-Carlsbad, Californie |

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-consumer-price-index -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-consumer-price-index)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborCPI

usLaborCPI = UsLaborCPI()
usLaborCPI_df = usLaborCPI.to_pandas_dataframe()
```

```python
usLaborCPI_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-consumer-price-index -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-consumer-price-index)** .

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
folder_name = "cpi/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-consumer-price-index -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-consumer-price-index)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborCPI

usLaborCPI = UsLaborCPI()
usLaborCPI_df = usLaborCPI.to_spark_dataframe()
```

```python
display(usLaborCPI_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-consumer-price-index -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-consumer-price-index)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "cpi/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-consumer-price-index -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-consumer-price-index)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "cpi/"
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