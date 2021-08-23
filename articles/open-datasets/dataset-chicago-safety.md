---
title: Données de sûreté de Chicago
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données Chigago Safety Data dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: d39c89308f0c33f98f1c5d074746a2008317472a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038647"
---
# <a name="chicago-safety-data"></a>Données de sûreté de Chicago

Demandes de service 311 de la ville de Chicago, notamment des plaintes concernant le code d’assainissement, des nids-de-poule signalés et des problèmes d’éclairage public

Toutes les plaintes relatives au code d’assainissement ouvert ont été adressées au service 311 et toutes les demandes traitées depuis le 1er janvier 2011. Le Département Streets and Sanitation examine et corrige les violations du code de l’assainissement de Chicago. Les résidents peuvent effectuer une demande de service pour des infractions telles que des bennes à ordures qui débordent et des déchets dans la rue. Le 311 reçoit parfois des plaintes en double relatives au code d’assainissement. Les demandes étiquetées comme des doublons se trouvent dans la même zone géographique qu’une demande précédente et ont été entrées dans le système de demande de service clientèle (RSC) du service 311 à peu près au même moment. Les plaintes en double sont identifiées comme telles dans le champ d’état, sous la forme « Open-Dup » ou « Completed-Dup ».

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

Le département des transports de Chicago (CDOT) supervise la réparation des nids-de-poule sur plus de 4 000 miles de grandes artères et de rues résidentielles à Chicago. CDOT reçoit des rapports de nids-de-poule via le centre d’appels 311. CDOT utilise un système de mappage et de suivi pour identifier les emplacements des nids-de-poule et programmer les équipes de réparation.

Un appel au 311 peut générer plusieurs réparations de nids-de-poule. Lorsqu’une équipe arrive pour réparer un nid-de-poule signalé au 311, elle s’occupe de tous les autres nids-de-poule du bloc. Les réparations des nids-de-poule sont terminées dans les 7 jours qui suivent le premier signalement d’un nid-de-poule au service 311. Les conditions météorologiques, les températures glaciales et les précipitations, ont une influence sur la durée des réparations. Les jours où le temps est clément et où il n’y a pas de précipitations, les équipes peuvent réparer plusieurs milliers de nids-de-poule. 

Si une demande précédente est déjà ouverte pour un groupe de 4 adresses, elle reçoit le statut « Doublon (Ouvert) ». Par exemple, s’il existe une demande pour 6535 N Western et qu’une nouvelle demande est reçue pour 6531 N Western (qui se trouve à moins de quatre adresses de la demande d’origine), la nouvelle demande reçoit le statut « Doublon (Ouvert) ». Une fois la rue réparée, le statut de la demande indique « Terminée » pour la demande d’origine et « Doublon (Fermé) » pour toutes les demandes en double. Une demande de service reçoit également le statut « Completed » lorsque l’adresse déclarée est inspectée mais qu’aucun nid-de-poule n’est trouvé ou que le nid-de-poule a déjà été comblé. Si un autre problème est détecté dans la rue, tel qu’un « effondrement » ou une « coupure de service », il est alors dirigé vers le service ou l’entrepreneur approprié.

Tous les rapports ouverts de « Lampadaires - Tout est éteint » (une panne de trois lumières ou plus) faits au 311 et toutes les demandes complétées depuis le 1er janvier 2011. Le Chicago Department of Transportation (CDOT) supervise environ 250 000 lampadaires qui éclairent les artères et les rues résidentielles de Chicago. La CDOT effectue les réparations et le remplacement des ampoules en réponse aux rapports des résidents sur les pannes d’éclairage. À chaque fois que le CDOT reçoit un rapport indiquant que tous les éclairages sont hors service (« Tout est éteint »), l’électricien chargé de la réparation examine tous les éclairages de ce circuit (chaque circuit comporte 8 à 16 éclairages) pour s’assurer qu’ils fonctionnent tous correctement. Si une deuxième demande relative à un éclairage en panne dans le même circuit est soumise dans les quatre jours calendaires suivant la demande initiale, la demande la plus récente reçoit automatiquement le statut « Duplicate (Open) ». Étant donné que l’électricien du CDOT examine tous les éclairages d’un circuit pour vérifier qu’ils fonctionnent tous, toute adresse de type « Doublon (Ouverte) » fera automatiquement l’objet d’une observation et d’une réparation. Une fois les éclairages réparés, le statut de la demande indique « Completed » pour la demande d’origine et « Duplicate (Closed) » pour toutes les demandes en double. Une demande de service reçoit également le statut « Completed » lorsque les éclairages signalés sont inspectés mais s’avèrent en bon état de fonctionnement et de réparation. Lorsque la demande de service concerne une adresse inexistante ; ou lorsque les éclairages sont entretenus par une société privée. Les données sont mises à jour quotidiennement.

## <a name="volume-and-retention"></a>Volume et conservation

Ce jeu de données est stocké au format Parquet. Il est mis à jour quotidiennement et contient environ 1 million de lignes (80 Mo) au total à partir de 2018.

Ce jeu de données contient les enregistrements historiques accumulés de 2011 à 2018. Vous pouvez utiliser les paramètres de paramétrage de notre SDK pour récupérer les données dans un intervalle de temps spécifique.

## <a name="storage-location"></a>Emplacement de stockage

Ce jeu de données est stocké dans la région Azure USA Est. L’allocation de ressources de calcul dans la région USA Est est recommandée à des fins d’affinité.

## <a name="related-datasets"></a>Jeux de données associés

- [Assainissement de Chicago](https://data.cityofchicago.org/Service-Requests/311-Service-Requests-Sanitation-Code-Complaints-Hi/me59-5fac)
- [Nids-de-poule à Chicago](https://data.cityofchicago.org/Service-Requests/311-Service-Requests-Pot-Holes-Reported-Historical/7as2-ds3y)
- [Lampadaires de Chicago](https://data.cityofchicago.org/Service-Requests/311-Service-Requests-Street-Lights-All-Out-Histori/zuxi-7xem)

## <a name="additional-information"></a>Informations supplémentaires

Ce jeu de données est fourni par les services publiques de la ville de Chicago.

Cliquez ici pour les termes utilisés dans ce jeu de données. Envoyez un e-mail à dataportal@cityofchicago.org si vous avez des questions sur la source de données.

## <a name="columns"></a>Colonnes

| Nom | Type de données | Unique | Valeurs (exemple) | Description |
|-|-|-|-|-|
| address | string | 140 612 | \" \" 1 City Hall Plz Boston MA 02108 | Lieu. |
| catégorie | string | 54 | Nettoyage des rues Assainissement | Motif de la demande de service. |
| dataSubtype | string | 1 | 311_All | “311_All” |
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
| Sûreté | 311_All | 25/04/2021 23:55:04 | Réclamation relative aux lampadaires | null | Ouvrir | 4800 W WASHINGTON BLVD | 41,882148426 | -87,74556256 | null |  |
| Sûreté | 311_All | 25/04/2021 23:54:31 | 311 INFORMATIONS UNIQUEMENT | null | Effectué | 2111 W Lexington ST |  |  | null |  |
| Sûreté | 311_All | 25/04/2021 23:52:11 | 311 INFORMATIONS UNIQUEMENT | null | Effectué | 2111 W Lexington ST |  |  | null |  |
| Sûreté | 311_All | 25/04/2021 23:49:56 | 311 INFORMATIONS UNIQUEMENT | null | Effectué | 2111 W Lexington ST |  |  | null |  |
| Sûreté | 311_All | 25/04/2021 23:48:53 | Maintenance des conteneurs à poubelle | null | Ouvrir | 3409 E 106TH ST | 41,702545562 | -87,540917602 | null |  |
| Sûreté | 311_All | 25/04/2021 23:46:01 | 311 INFORMATIONS UNIQUEMENT | null | Effectué | 2111 W Lexington ST |  |  | null |  |
| Sûreté | 311_All | 25/04/2021 23:45:46 | Réclamation sur le bruit des avions | null | Effectué | 10510 W ZEMKE RD |  |  | null |  |
| Sûreté | 311_All | 25/04/2021 23:45:02 | 311 INFORMATIONS UNIQUEMENT | null | Effectué | 2111 W Lexington ST |  |  | null |  |
| Sûreté | 311_All | 25/04/2021 23:44:24 | Demande d’inspection des égouts souterrains | null | Ouvrir | 7246 W THORNDALE AVE | 41,987984339 | -87,808702917 | null |  |

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_chicago -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_chicago)** .

```
# This is a package in preview.
from azureml.opendatasets import ChicagoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = ChicagoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_chicago -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_chicago)** .

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
folder_name = "Safety/Release/city=Chicago"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_chicago -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_chicago)** .

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import ChicagoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = ChicagoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_chicago -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_chicago)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Chicago"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_chicago -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_chicago)** .

```python
# This is a package in preview.
from azureml.opendatasets import ChicagoSafety

from datetime import datetime
from dateutil import parser

end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = ChicagoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```python
# Display top 5 rows
display(safety.limit(5))
```

```python
# Display data statistic information
display(safety, summary = True)
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_chicago -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_chicago)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Chicago"
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