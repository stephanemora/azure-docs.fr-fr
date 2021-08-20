---
title: Données de sûreté de San Francisco
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données de sûreté San Francisco Safety dataset dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 4502d51054f43b7b1d876443c12f67418ba84060
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982453"
---
# <a name="san-francisco-safety-data"></a>Données de sûreté de San Francisco

Appel au pompiers et incidents 311 à San Francisco.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

Les appels aux pompiers incluent toutes les réponses des unités de pompiers aux appels. Chaque enregistrement inclut le numéro d’appel, le numéro d’incident, l’adresse, l’identifiant de l’unité, le type d’appel et la disposition. Tous les intervalles de temps pertinents sont également inclus. Étant donné que ce jeu de données est basé sur les réponses et que la plupart des appels impliquent plusieurs unités, il existe plusieurs enregistrements pour chaque numéro d’appel. Les adresses sont associées à un numéro de bloc, à une intersection ou à une cabine téléphonique, et non à une adresse spécifique.

Le jeu « 311 Cases » comprend des cas associés à un endroit ou à une chose (par exemple parcs, rues et bâtiments) et créés après le 1er juillet 2008. Les cas qui sont enregistrés par un utilisateur concernant leurs propres besoins sont exclus. Par exemple, les questions de propriété ou d’impôt professionnel, les demandes d’autorisation de stationnement, etc. Pour plus d’informations, consultez [Program Link](https://support.datasf.org/help/311-case-data-faq).
 
## <a name="volume-and-retention"></a>Volume et conservation

Ce jeu de données est stocké au format Parquet. Il est mis à jour quotidiennement avec environ 6 millions de lignes (400 Mo) en 2019.

Ce jeu de données contient les enregistrements historiques accumulés de 2015 à aujourd’hui. Vous pouvez utiliser les paramètres de paramétrage de notre SDK pour récupérer les données dans un intervalle de temps spécifique.

## <a name="storage-location"></a>Emplacement de stockage

Ce jeu de données est stocké dans la région Azure USA Est. L’allocation de ressources de calcul dans la région USA Est est recommandée à des fins d’affinité.

## <a name="related-datasets"></a>Jeux de données associés

- [Appels au service des pompiers](https://data.sfgov.org/Public-Safety/Fire-Department-Calls-for-Service/nuek-vuh3)
- [311 Cases](https://data.sfgov.org/City-Infrastructure/311-Cases/vw6y-z8j6)

## <a name="additional-information"></a>Informations supplémentaires

Ce jeu de données provient de la ville de San Francisco. Consultez les [conditions de l’utilisation de ce jeu de données](https://datasf.org/opendata/terms-of-use/).

## <a name="columns"></a>Colonnes

| Nom | Type de données | Unique | Valeurs (exemple) | Description |
|-|-|-|-|-|
| address | string | 280,652 | Non associé à une adresse spécifique « 0 Block of 6TH ST » | Adresse de l’incident (remarque : adresse et emplacement généralisés au milieu de la rue, à l’intersection ou à la cabine téléphonique la plus proche pour protéger la confidentialité de l’appelant). |
| catégorie | string | 108 | Nettoyage de la rue et du trottoir Risque vital potentiel | Nom lisible par l’homme du type de demande de service 311 ou du groupe de types d’appel pour les appels 911 pour signaler un incendie. |
| dataSubtype | string | 2 | 911_Fire 311_All | « 911_Fire » ou « 311_All ». |
| dataType | string | 1 | Sûreté | Sécurité |
| dateTime | timestamp | 6,496,563 | 2020-10-19 12:28:08 2020-07-28 06:40:26 | Date et heure auxquelles la demande de service a été effectué ou l’appel signalant un incendie a été reçu. |
| latitude | double | 1,615,369 | 37.777624238929 37.786117211838 | Latitude du lieu, à l’aide de la projection WGS84. |
| longitude | double | 1,554,612 | -122.39998111124 -122.419854245692 | Longitude du lieu, à l’aide de la projection WGS84. |
| source | string | 9 | Téléphone portable/Open311 | Mécanisme ou chemin de réception de la demande de service ; généralement « Téléphone », « Message/SMS », « Site web », « Appli mobile », « Twitter », etc. mais les termes peuvent varier selon le système. |
| status | string | 3 | Fermé Ouvert | Indicateur en un seul mot de l’état actuel de la demande de service. (Remarque : GeoReport v2 autorise uniquement les états « open » et « closed ») |
| subcategory | string | 1 270 | Medical Incident Bulky Items | Nom lisible par l’homme du sous-type de demande de service pour les incidents 311 ou type d’appel pour les appels au 911 pour signaler un incendie. |

## <a name="preview"></a>Préversion

| dataType | dataSubtype | dateTime | catégorie | subcategory | status | address | latitude | longitude | source | extendedProperties |
|-|-|-|-|-|-|-|-|-|-|-|
| Sûreté | 911_Fire | 26/04/2021 2:56:13 AM | Pas de risque vital | Incident médical | null | 700 Block of GEARY ST | 37.7863607914647 | -122.415616900246 | null |  |
| Sûreté | 911_Fire | 26/04/2021 2:56:13 AM | Pas de risque vital | Incident médical | null | 700 Block of GEARY ST | 37.7863607914647 | -122.415616900246 | null |  |
| Sûreté | 911_Fire | 26/04/2021 2:54:03 AM | Pas de risque vital | Incident médical | null | 0 Block of ESSEX ST | 37.7860048266229 | -122.395077258809 | null |  |
| Sûreté | 911_Fire | 26/04/2021 2:54:03 AM | Pas de risque vital | Incident médical | null | 0 Block of ESSEX ST | 37.7860048266229 | -122.395077258809 | null |  |
| Sûreté | 911_Fire | 26/04/2021 2:52:17 AM | Pas de risque vital | Incident médical | null | 700 Block of 29TH AVE | 37.7751770865322 | -122.488604397217 | null |  |
| Sûreté | 911_Fire | 26/04/2021 2:50:28 AM | Pronostic vital engagé | Incident médical | null | 1000 Block of GEARY ST | 37.7857350982044 | -122.420555240691 | null |  |
| Sûreté | 911_Fire | 26/04/2021 2:50:28 AM | Pronostic vital engagé | Incident médical | null | 1000 Block of GEARY ST | 37.7857350982044 | -122.420555240691 | null |  |
| Sûreté | 911_Fire | 26/04/2021 2:33:52 AM | Pas de risque vital | Incident médical | null | 100 Block of BELVEDERE ST | 37.767791696654 | -122.449332294394 | null |  |
| Sûreté | 911_Fire | 26/04/2021 2:33:52 AM | Pas de risque vital | Incident médical | null | 100 Block of BELVEDERE ST | 37.767791696654 | -122.449332294394 | null |  |
| Sûreté | 911_Fire | 26/04/2021 2:33:51 AM | Pronostic vital engagé | Incident médical | null | 100 Block of 6TH ST | 37.7807920802756 | -122.408385745499 | null |  |

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_newyork -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_newyork)** .

```
# This is a package in preview.
from azureml.opendatasets import NycSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = NycSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_newyork -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_newyork)** .

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
folder_name = "Safety/Release/city=NewYorkCity"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_newyork -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_newyork)** .

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import NycSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = NycSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_newyork -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_newyork)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=NewYorkCity"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_newyork -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_newyork)** .

```python
# This is a package in preview.
from azureml.opendatasets import NycSafety

from datetime import datetime
from dateutil import parser

end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = NycSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```python
# Display top 5 rows
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_newyork -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_newyork)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=NewYorkCity"
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
