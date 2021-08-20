---
title: Indice des prix à la production aux États-Unis - Marchandises
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données US Producer Price Index – Commodities dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 224a5d0278237d5a7d87ee8e3c9adaedb71f193a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038675"
---
# <a name="us-producer-price-index---commodities"></a>Indice des prix à la production aux États-Unis - Marchandises

L’indice des prix à la production (IPP) est une mesure de la variation moyenne dans le temps des prix de vente perçus par les producteurs nationaux pour leur production. Les prix inclus dans l’IPP sont ceux de la première transaction commerciale pour les produits et services couverts.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

Environ 10 000 indices des prix à la production pour des produits individuels et des groupes de produits sont publiés chaque mois. Les indices des prix à la production sont disponibles pour presque toutes les industries des secteurs producteurs de biens de l’économie américaine (industrie minière, industrie manufacturière, agriculture, pêche et foresterie) ainsi que pour le gaz naturel, l’électricité, la construction et les biens en concurrence avec ceux fabriqués dans les secteurs de production, tels que les déchets et les mises au rebut. Le programme d’indices des prix à la production couvre environ 72 % de la production du secteur des services, telle que mesurée par les recettes déclarées lors du recensement économique de 2007. Les données incluent les industries des secteurs suivants : commerce de gros et de détail ; transport et entreposage; information; finances et assurances ; courtage immobilier, location et crédit-bail ; services professionnels, scientifiques et techniques ; services administratifs, de soutien et de gestion des déchets ; soins de santé et assistance sociale ; et hébergement.

Le fichier [README](https://download.bls.gov/pub/time.series/wp/wp.txt) contenant des informations détaillées sur ce jeu de données est disponible à l’[emplacement d’origine du jeu de données](https://download.bls.gov/pub/time.series/wp/). Des informations supplémentaires sont disponibles dans les [FAQ](https://www.bls.gov/ppi/ppifaq.htm).

Ce jeu de données est produit à partir des [données d’indice des prix à la production](https://www.bls.gov/ppi/) publiées par l’[US Bureau of Labor Statistics (BLS)](https://www.bls.gov/). Consultez les [informations de liaison et de copyright](https://www.bls.gov/bls/linksite.htm) et les [avis importants de site web](https://www.bls.gov/bls/website-policies.htm) pour connaître les conditions générales relatives à l’utilisation de ce jeu de données.

## <a name="storage-location"></a>Emplacement de stockage

Ce jeu de données est stocké dans la région Azure USA Est. L’allocation de ressources de calcul dans la région USA Est est recommandée à des fins d’affinité.

## <a name="related-datasets"></a>Jeux de données associés

- [Indice américain des prix à la consommation](dataset-us-consumer-price-index.md)
- [Indice des prix producteur aux États-Unis - Industrie](dataset-us-producer-price-index-industry.md)

## <a name="columns"></a>Colonnes

| Nom | Type de données | Unique | Valeurs (exemple) | Description |
|-|-|-|-|-|
| footnote_codes | string | 3 | nan P | Identifie les notes de bas de page pour la série de données. La plupart des valeurs sont Null. Consultez https://download.bls.gov/pub/time.series/wp/wp.footnote. |
| group_code | string | 56 | 02 01 | Code identifiant le principal groupe de produits couvert par l’indice. Voir https://download.bls.gov/pub/time.series/wp/wp.group pour les codes et noms de groupes. |
| group_name | string | 56 | Denrées alimentaires et aliments pour animaux transformés Produits agricoles | Nom du principal groupe de produits couvert par l’indice. Voir https://download.bls.gov/pub/time.series/wp/wp.group pour les codes et noms de groupes. |
| item_code | string | 2 949 | 1 11 | Identifie l’élément auquel les observations de données se rapportent. Voir https://download.bls.gov/pub/time.series/wp/wp.item pour les codes et noms d’éléments. |
| item_name | string | 3 410 | Entreposage, stockage et services connexes Location de voitures particulières | Noms complets des éléments. Voir https://download.bls.gov/pub/time.series/wp/wp.item pour les codes et noms d’éléments. |
| period | string | 13 | M06 M07 | Identifie la période pendant laquelle les données sont observées. Voir https://download.bls.gov/pub/time.series/wp/wp.period pour la liste des valeurs de périodes. |
| saisonnier | string | 2 | U S | Code identifiant si les données sont corrigées en fonction des variations saisonnières. S=Corrigées en fonction des variations saisonnières ; U=Non corrigées |
| series_id | string | 5 458 | WPU101 WPU091 | Code identifiant les séries spécifiques. Une série chronologique fait référence à un jeu contenant des données observées sur une période étendue sur des intervalles cohérents. Voir https://download.bls.gov/pub/time.series/wp/wp.series pour des informations sur les séries telles que le code, le nom, l’année de début et de fin, etc. |
| series_title | string | 4 379 | Données sur les marchandises IPP pour les services d’extraction minière, sans correction des variations saisonnières Données sur les marchandises IPP pour les services de traitement des métaux, sans correction des variations saisonnières | Titre des séries spécifiques. Une série chronologique fait référence à un jeu contenant des données observées sur une période étendue sur des intervalles cohérents. Voir https://download.bls.gov/pub/time.series/wp/wp.series pour des informations sur les séries telles que l’ID, le nom, l’année de début et de fin, etc. |
| valeur | float | 6 788 | 100 99,0999984741211 | Indice des prix pour l’élément. |
| year | int | 26 | 2018 2017 | Identifie l’année d’observation. |

## <a name="preview"></a>Préversion

| item_code | group_code | series_id | year | period | valeur | footnote_codes | saisonnier | series_title | group_name | item_name |
|-|-|-|-|-|-|-|-|-|-|-|
| 120922 | 05 | WPU05120922 | 2008 | M06 | 100 | NaN | U | Données sur les marchandises IPP pour les combustibles et produits connexes et l’électricité : mines souterraines de charbon bitumineux préparé, concassé, criblé et calibré mécaniquement uniquement, sans correction des variations saisonnières | Combustibles et produits connexes et électricité | Mines souterraines de charbon bitumineux préparé, concassé, criblé et calibré mécaniquement uniquement |
| 120922 | 05 | WPU05120922 | 2008 | M07 | 104,6 | NaN | U | Données sur les marchandises IPP pour les combustibles et produits connexes et l’électricité : mines souterraines de charbon bitumineux préparé, concassé, criblé et calibré mécaniquement uniquement, sans correction des variations saisonnières | Combustibles et produits connexes et électricité | Mines souterraines de charbon bitumineux préparé, concassé, criblé et calibré mécaniquement uniquement |
| 120922 | 05 | WPU05120922 | 2008 | M08 | 104,4 | NaN | U | Données sur les marchandises IPP pour les combustibles et produits connexes et l’électricité : mines souterraines de charbon bitumineux préparé, concassé, criblé et calibré mécaniquement uniquement, sans correction des variations saisonnières | Combustibles et produits connexes et électricité | Mines souterraines de charbon bitumineux préparé, concassé, criblé et calibré mécaniquement uniquement |
| 120922 | 05 | WPU05120922 | 2008 | M09 | 98,3 | NaN | U | Données sur les marchandises IPP pour les combustibles et produits connexes et l’électricité : mines souterraines de charbon bitumineux préparé, concassé, criblé et calibré mécaniquement uniquement, sans correction des variations saisonnières | Combustibles et produits connexes et électricité | Mines souterraines de charbon bitumineux préparé, concassé, criblé et calibré mécaniquement uniquement |
| 120922 | 05 | WPU05120922 | 2008 | M10 | 101,5 | NaN | U | Données sur les marchandises IPP pour les combustibles et produits connexes et l’électricité : mines souterraines de charbon bitumineux préparé, concassé, criblé et calibré mécaniquement uniquement, sans correction des variations saisonnières | Combustibles et produits connexes et électricité | Mines souterraines de charbon bitumineux préparé, concassé, criblé et calibré mécaniquement uniquement |
| 120922 | 05 | WPU05120922 | 2008 | M11 | 95,2 | NaN | U | Données sur les marchandises IPP pour les combustibles et produits connexes et l’électricité : mines souterraines de charbon bitumineux préparé, concassé, criblé et calibré mécaniquement uniquement, sans correction des variations saisonnières | Combustibles et produits connexes et électricité | Mines souterraines de charbon bitumineux préparé, concassé, criblé et calibré mécaniquement uniquement |

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborPPICommodity

labor = UsLaborPPICommodity()
labor_df = labor.to_pandas_dataframe()
```

```python
labor_df.info()
```

<!-- nbend -->


# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-producer-price-index-commodities)** .

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
folder_name = "ppi_commodity/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborPPICommodity

labor = UsLaborPPICommodity()
labor_df = labor.to_spark_dataframe()
```

```python
display(labor_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-producer-price-index-commodities)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ppi_commodity/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-producer-price-index-commodities)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ppi_commodity/"
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