---
title: Données de sûreté de Boston
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données Boston Safety Data dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 1c9a4917f1b5d2b719a247d111b62897f8063bc9
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038652"
---
# <a name="boston-safety-data"></a>Données de sûreté de Boston

Appels 311 signalés à la ville de Boston.

Suivez ce lien pour en savoir plus sur [BOS:311](https://www.cityofboston.gov/311/).

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>Volume et conservation

Ce jeu de données est stocké au format Parquet. Il est mis à jour quotidiennement et contient environ 100 000 lignes (10 Mo) au total à partir de 2019.

Ce jeu de données contient les enregistrements historiques accumulés de 2011 à aujourd’hui. Vous pouvez utiliser les paramètres de paramétrage de notre SDK pour récupérer les données dans un intervalle de temps spécifique.

## <a name="storage-location"></a>Emplacement de stockage

Ce jeu de données est stocké dans la région Azure USA Est. L’allocation de ressources de calcul dans la région USA Est est recommandée à des fins d’affinité.

## <a name="additional-information"></a>Informations supplémentaires

Ce jeu de données est fourni par la ville de Boston. Pour plus d’informations, consultez le [site de jeux de données de Boston](https://data.boston.gov/dataset/311-service-requests). Pour la licence du jeu de données, consultez la documentation [Open Data Commons Public Domain Dedication and License (ODC PDDL)](http://opendefinition.org/licenses/odc-pddl/).

## <a name="columns"></a>Colonnes

| Nom | Type de données | Unique | Valeurs (exemple) | Description |
|-|-|-|-|-|
| address | string | 140 612 | \" \" 1 City Hall Plz Boston MA 02108 | Lieu. |
| catégorie | string | 54 | Nettoyage des rues Assainissement | Motif de la demande de service. |
| dataSubtype | string | 1 | 311_All | « 311_All » |
| dataType | string | 1 | Sûreté | Sécurité |
| dateTime | timestamp | 1 529 075 | 2015-07-23 10:51:00 2015-07-23 10:47:00 | Date et heure d’ouverture de la demande de service. |
| latitude | double | 1 622 | 42,3594 42,3603 | Il s’agit de la valeur de latitude. Les lignes de latitude sont parallèles à l’équateur. |
| longitude | double | 1 806 | -71,0587 -71,0583 | Il s’agit de la valeur de longitude. Les lignes de longitude sont perpendiculaires aux lignes de latitude et passent par les deux pôles. |
| source | string | 7 | Appel aux électeurs Application Citizens Connect | Source d’origine de l’incident. |
| status | string | 2 | Fermé Ouvert | Statut de l’incident. |
| subcategory | string | 209 | Demandes de contrôle du stationnement pour le nettoyage des rues | Type de la demande de service. |

## <a name="preview"></a>Préversion

| dataType | dataSubtype | dateTime | catégorie | subcategory | status | address | latitude | longitude | source | extendedProperties |
|-|-|-|-|-|-|-|-|-|-|-|
| Sûreté | 311_All | 4/27/2021 11:45:49 PM | Contrôle et véhicules abandonnés | Contrôle du stationnement | Ouvrir | 51 Gardner St Allston MA 02134 | 42,3535 | -71,1285 | Application Citizens Connect |  |
| Sûreté | 311_All | 4/27/2021 11:43:43 PM | Assainissement | Poubelle/recyclage/déchets de jardin/articles en vrac manqués | Ouvrir | 4 Putnam Pl Roxbury MA 02119 | 42,3298 | -71,0883 | Libre-service |  |
| Sûreté | 311_All | 4/27/2021 11:37:19 PM | Contrôle et véhicules abandonnés | Contrôle du stationnement | Ouvrir | 36 Raven St Dorchester MA 02125 | 42,3177 | -71,0546 | Application Citizens Connect |  |
| Sûreté | 311_All | 4/27/2021 11:30:00 PM | Assainissement | Poubelle/recyclage/déchets de jardin/articles en vrac manqués | Ouvrir | 58 Bicknell St Dorchester MA 02121 | 42,2984 | -71,0834 | Appel aux électeurs |  |
| Sûreté | 311_All | 4/27/2021 11:10:20 PM | Contrôle et véhicules abandonnés | Contrôle du stationnement | Ouvrir | 2000 Commonwealth Ave Brighton MA 02135 | 42,3394 | -71,1585 | Application Citizens Connect |  |
| Sûreté | 311_All | 4/27/2021 11:06:00 PM | Nuisances sonores | Fêtes/musique/personnes bruyantes | Ouvrir | INTERSECTION de Lewis St et Nord St Boston MA | 42,3594 | -71,0587 | Appel aux électeurs |  |
| Sûreté | 311_All | 4/27/2021 11:05:00 PM | Contrôle et véhicules abandonnés | Contrôle du stationnement | Ouvrir | 1 Nassau St Boston MA 02111 | 42,3486 | -71,0629 | Appel aux électeurs |  |
| Sûreté | 311_All | 4/27/2021 11:00:55 PM | Autorités chargées de l’application des lois | Mauvais état des biens | Ouvrir | 17 Mercer St South Boston MA 02127 | 42,3332 | -71,0492 | Application Citizens Connect |  |

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_boston -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_boston)** .

```
# This is a package in preview.
from azureml.opendatasets import BostonSafety

from datetime import datetime
from dateutil import parser

end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = BostonSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->


# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_boston -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_boston)** .

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
container_name = "citydatacontainer"
folder_name = "Safety/Release/city=Boston"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_boston -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_boston)** .

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import BostonSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = BostonSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety)
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_boston -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_boston)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Boston"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_boston -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_boston)** .

```python
from azureml.opendatasets import BostonSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = BostonSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```python
display(safety)
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_boston -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_boston)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Boston"
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

## <a name="examples"></a>Exemples

- Consultez l’exemple [City Safety Analytics](https://github.com/scottcounts/CitySafety) sur GitHub.


## <a name="next-steps"></a>Étapes suivantes

Consultez les autres jeux de données du [catalogue Open Datasets](dataset-catalog.md).