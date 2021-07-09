---
title: US Producer Price Index industry (Indice des prix à la production aux États-Unis)
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données US Producer Price Index industry dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: cd0662fc4990e6a2baf0a29ba2e96cec50509a05
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038601"
---
# <a name="us-consumer-price-index-industry"></a>US Consumer Price Index industry (Indice des prix à la consommation aux États-Unis)

L’indice des prix à la production (IPP) est une mesure de la variation moyenne dans le temps des prix de vente perçus par les producteurs nationaux pour leur production. Les prix inclus dans l’IPP sont ceux de la première transaction commerciale pour les produits et services couverts.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

Les indices de la série actuelle de la révision de l’Indice des prix à la production reflètent les mouvements de prix de la production nette des producteurs organisés selon le système NAICS (North American Industry Classification System). Le jeu de données PC est compatible avec un vaste éventail de séries chronologiques économiques basées sur le NAICS, notamment : productivité, production, emploi, salaires et gains.

L’Indice des prix à la production aux États-Unis est constitué par la production de toutes les industries des secteurs producteurs de biens de l’économie américaine (industries extractives, industries manufacturières, agriculture, pêche et foresterie) ainsi que le gaz naturel, l’électricité, la construction et les biens concurrents avec ceux fabriqués dans les secteurs de production, tels que les déchets et les mises au rebut. En outre, à compter de janvier 2011, l’Indice des prix à la production couvrait plus des trois quarts de la production du secteur des services, en publiant des données pour certaines industries des secteurs suivants : commerce de gros et de détail ; transport et entreposage ; information ; finances et assurances ; courtage immobilier, location et crédit-bail ; services professionnels, scientifiques et techniques ; services administratifs, de soutien et de gestion des déchets ; soins de santé et assistance sociale ; hébergement.

Le fichier [README](https://download.bls.gov/pub/time.series/wp/wp.txt) contenant des informations détaillées sur ce jeu de données est disponible à l’[emplacement d’origine du jeu de données](https://download.bls.gov/pub/time.series/wp/). Des informations supplémentaires sont disponibles dans les [FAQ](https://www.bls.gov/ppi/ppifaq.htm).

Ce jeu de données est produit à partir des [données d’indice des prix à la production](https://www.bls.gov/ppi/) publiées par l’[US Bureau of Labor Statistics (BLS)](https://www.bls.gov/). Consultez les [informations de liaison et de copyright](https://www.bls.gov/bls/linksite.htm) et les [avis importants de site web](https://www.bls.gov/bls/website-policies.htm) pour connaître les conditions générales relatives à l’utilisation de ce jeu de données.

## <a name="storage-location"></a>Emplacement de stockage

Ce jeu de données est stocké dans la région Azure USA Est. L’allocation de ressources de calcul dans la région USA Est est recommandée à des fins d’affinité.

## <a name="related-datasets"></a>Jeux de données associés

- [Indice américain des prix à la consommation](dataset-us-consumer-price-index.md)
- [Indice des prix à la production aux États-Unis - Marchandises](dataset-us-producer-price-index-commodities.md)

## <a name="columns"></a>Colonnes

| Nom | Type de données | Unique | Valeurs (exemple) | Description |
|-|-|-|-|-|
| footnote_codes | string | 3 | nan P | Identifie les notes de bas de page pour la série de données. La plupart des valeurs sont Null. Consultez https://download.bls.gov/pub/time.series/pc/pc.footnote. |
| industry_code | string | 1,064 | 221122 325412 | Code NAICS pour le secteur. Voir https://download.bls.gov/pub/time.series/pc/pc.industry pour les codes et les noms. |
| industry_name | string | 842 | Distribution d’énergie électrique Préparation pharmaceutique Fabrication | Nom correspondant au code de l’industrie. Voir https://download.bls.gov/pub/time.series/pc/pc.industry pour les codes et les noms. |
| period | string | 13 | M06 M07 | Identifie la période pendant laquelle les données sont observées. Voir https://download.bls.gov/pub/time.series/pc/pc.period pour la liste complète. |
| product_code | string | 4,822 | 324121P 316--- | Code identifiant le produit auquel l’observation des données fait référence. Voir https://download.bls.gov/pub/time.series/pc/pc.product pour le mappage des codes d’activité, codes de produits et noms de produits. |
| product_name | string | 3,313 | Produits principaux Produits secondaires | Nom du produit auquel l’observation des données fait référence. Voir https://download.bls.gov/pub/time.series/pc/pc.product pour le mappage des codes d’activité, codes de produits et noms de produits. |
| saisonnier | string | 1 | U | Code identifiant si les données sont corrigées en fonction des variations saisonnières. S=Corrigées en fonction des variations saisonnières ; U=Non corrigées |
| series_id | string | 4,822 | PCU332323332323M PCU333415333415C | Code identifiant les séries spécifiques. Une série chronologique fait référence à un jeu contenant des données observées sur une période étendue sur des intervalles cohérents. Voir https://download.bls.gov/pub/time.series/pc/pc.series pour des informations sur les séries telles que le code, le nom, l’année de début et de fin, etc. |
| series_title | string | 4,588 | Données sectorielles d’indice des prix à la production pour la distribution d’énergie électrique dans la région Centre sud-ouest, non ajustées de façon saisonnière, Données sectorielles d’indice des prix à la production pour la distribution d’énergie électrique dans la région Pacifique, non ajustées de façon saisonnière |  |
| valeur | float | 7,658 | 100.0 100.4000015258789 | Indice des prix pour l’élément. |
| year | int | 22 | 2015 2017 | Identifie l’année d’observation. |

## <a name="preview"></a>Préversion

| product_code | industry_code | series_id | year | period | valeur | footnote_codes | saisonnier | series_title | industry_name | product_name |
|-|-|-|-|-|-|-|-|-|-|-|
| 2123240 | 212324 | PCU2123242123240 | 1998 | M01 | 117 | NaN | U | Données sectorielles d’indice des prix à la production pour l’extraction de kaolin et d’argile plastique, non ajustées de façon saisonnière | Extraction de kaolin et d’argile plastique | Kaolin et argile plastique |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M02 | 116.9 | NaN | U | Données sectorielles d’indice des prix à la production pour l’extraction de kaolin et d’argile plastique, non ajustées de façon saisonnière | Extraction de kaolin et d’argile plastique | Kaolin et argile plastique |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M03 | 116.3 | NaN | U | Données sectorielles d’indice des prix à la production pour l’extraction de kaolin et d’argile plastique, non ajustées de façon saisonnière | Extraction de kaolin et d’argile plastique | Kaolin et argile plastique |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M04 | 116 | NaN | U | Données sectorielles d’indice des prix à la production pour l’extraction de kaolin et d’argile plastique, non ajustées de façon saisonnière | Extraction de kaolin et d’argile plastique | Kaolin et argile plastique |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M05 | 116.2 | NaN | U | Données sectorielles d’indice des prix à la production pour l’extraction de kaolin et d’argile plastique, non ajustées de façon saisonnière | Extraction de kaolin et d’argile plastique | Kaolin et argile plastique |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M06 | 116.3 | NaN | U | Données sectorielles d’indice des prix à la production pour l’extraction de kaolin et d’argile plastique, non ajustées de façon saisonnière | Extraction de kaolin et d’argile plastique | Kaolin et argile plastique |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M07 | 116.6 | NaN | U | Données sectorielles d’indice des prix à la production pour l’extraction de kaolin et d’argile plastique, non ajustées de façon saisonnière | Extraction de kaolin et d’argile plastique | Kaolin et argile plastique |

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[Téléchargez le notebook à la place](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-producer-price-index-industry)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborPPIIndustry

labor = UsLaborPPIIndustry()
labor_df = labor.to_pandas_dataframe()
```

```python
labor_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[Téléchargez le notebook à la place](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-producer-price-index-industry)** .

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
folder_name = "ppi_industry/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[Téléchargez le notebook à la place](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-producer-price-index-industry)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborPPIIndustry

labor = UsLaborPPIIndustry()
labor_df = labor.to_spark_dataframe()
```

```python
display(labor_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[Téléchargez le notebook à la place](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-producer-price-index-industry)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ppi_industry/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[Téléchargez le notebook à la place](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-producer-price-index-industry)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ppi_industry/"
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