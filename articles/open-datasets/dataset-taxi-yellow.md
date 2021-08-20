---
title: Jeu de données des taxis et limousines jaunes de la ville de New York
titleSuffix: Azure Open Datasets
description: Découvrez l’utilisation du jeu de données des taxis et limousines jaunes de la ville de New York dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 5bf2a3b363c7d8a1cd0b10b1c958c4cfbb567deb
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038680"
---
# <a name="nyc-taxi--limousine-commission---yellow-taxi-trip-records"></a>Commission des taxis et limousines de la ville de New York : enregistrements de trajets en taxi jaune

Les enregistrements de trajets en taxi jaune incluent des champs indiquant les dates et heures de début et fin de trajet, les lieux de départ et d’arrivée, la distance des trajets, les tarifs détaillés, les types de tarifs, les types de paiement et le nombre de passagers signalé par le conducteur.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>Volume et conservation

Ce jeu de données est stocké au format Parquet. Il y a environ 1,5 milliard de lignes (50 Go) au total en 2018.

Ce jeu de données contient les enregistrements historiques accumulés de 2009 à 2018. Vous pouvez utiliser les paramètres de paramétrage de notre SDK pour récupérer les données dans un intervalle de temps spécifique.

## <a name="storage-location"></a>Emplacement de stockage

Ce jeu de données est stocké dans la région Azure USA Est. L’allocation de ressources de calcul dans la région USA Est est recommandée à des fins d’affinité.

## <a name="additional-information"></a>Informations supplémentaires

Commission des services de taxis et de limousines de la ville de New York (en anglais)

Les données ont été collectées et fournies à la Commission des services de taxis et de limousines de la ville de New York (TLC) par des fournisseurs de technologie agréés dans le cadre du programme d’amélioration du trafic passagers et taxis (TPEP/LPEP). Les données sur les trajets n’ont pas été créées par la TLC et celle-ci ne fait aucune déclaration quant à l’exactitude de ces données.

Affichez l’[emplacement du jeu de données d’origine](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page) et les [conditions d’utilisation d’origine](https://www1.nyc.gov/home/terms-of-use.page).

## <a name="columns"></a>Colonnes
| Nom                 | Type de données | Unique      | Valeurs (exemple)                         | Description                                                                                                                                                                                                                                            |
|----------------------|-----------|-------------|-----------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| doLocationId         | string    | 265         | 161 236                                 | Zone de taxi TLC où le compteur a été arrêté.                                                                                                                                                                                                   |
| endLat               | double    | 961 994     | 41,366138 40,75                         |                                                                                                                                                                                                                                                        |
| endLon               | double    | 1 144 935   | -73,137393 -73,9824                     |                                                                                                                                                                                                                                                        |
| extra                | double    | 877         | 0,5 1,0                                 | Frais et suppléments divers. Actuellement, cela n’inclut que les frais de 0,50 $ et de 1 $ pour les heure de pointe et de nuit.                                                                                                                                   |
| fareAmount           | double    | 18 935      | 6,5 4,5                                 | Tarif temps-distance calculé par le compteur.                                                                                                                                                                                                    |
| improvementSurcharge | string    | 60          | 0,3 0                                   | Supplément pour l’amélioration de 0,30 $ évalué sur les trajets hélés à une station. Le supplément pour amélioration a commencé à être perçu en 2015.                                                                                                                                     |
| mtaTax               | double    | 360         | 0,5 -0,5                                | Taxe de 0,50 USD MTA déclenchée automatiquement en fonction du tarif affiché au compteur.                                                                                                                                                                        |
| passengerCount       | int       | 64          | 1 2                                     | Nombre de passagers dans le véhicule. Cette valeur est entrée par le conducteur.                                                                                                                                                                               |
| paymentType          | string    | 6 282       | CSH CRD                                 | Code numérique indiquant comment le passager a payé le trajet. 1 = Carte de crédit ; 2 = Argent liquide ; 3 = Aucuns frais ; 4 = Litige ; 5 = Inconnu ; 6 = Voyage annulé.                                                                                                          |
| puLocationId         | string    | 266         | 237 161                                 | Zone de taxi TLC où le compteur a été lancé.                                                                                                                                                                                                      |
| puMonth              | int       | 12          | 3 5                                     |                                                                                                                                                                                                                                                        |
| puYear               | int       | 29          | 2012 2011                               |                                                                                                                                                                                                                                                        |
| rateCodeId           | int       | 56          | 1 2                                     | Code tarifaire final en vigueur à la fin du trajet. 1 = Taux standard ; 2 = JFK ; 3 = Newark ; 4 = Nassau ou Westchester ; 5 = Prix négocié ; 6 = Voyage en groupe.                                                                                                |
| startLat             | double    | 833 016     | 41,366138 40,7741                       |                                                                                                                                                                                                                                                        |
| startLon             | double    | 957 428     | -73,137393 -73,9821                     |                                                                                                                                                                                                                                                        |
| storeAndFwdFlag      | string    | 8           | N 0                                     | Cet indicateur indique si l’enregistrement de trajet a été conservé dans la mémoire du véhicule avant son envoi au fournisseur (ce qui s’appelle « stockage et transmission »), car le véhicule n’était pas connecté au serveur. Y = Trajet stockage et transmission ; N = Pas un trajet stockage et transmission. |
| tipAmount            | double    | 12 121      | 1,0 2,0                                 | Ce champ est automatiquement renseigné pour les pourboires en carte bancaire. Les pourboires en espèces ne sont pas inclus.                                                                                                                                                                |
| tollsAmount          | double    | 6 634       | 5,33 4,8                                | Montant total de tous les péages payés lors du trajet.                                                                                                                                                                                                                |
| totalAmount          | double    | 39 707      | 7 7,8                                 | Montant total facturé aux passagers. N’inclut pas les pourboires.                                                                                                                                                                                    |
| tpepDropoffDateTime  | timestamp | 290 185 010 | 2010-11-07 01:29:00 2013-11-03 01:22:00 | Date et heure auxquelles le compteur a été arrêté.                                                                                                                                                                                                       |
| tpepPickupDateTime   | timestamp | 289 948 585 | 2010-11-07 01:00:00 2009-11-01 01:05:00 | Date et heure auxquelles le compteur a été lancé.                                                                                                                                                                                                          |
| tripDistance         | double    | 14 003      | 1 0,9                                 | Distance parcourue en miles indiquée par le compteur.                                                                                                                                                                                          |
| vendorID             | string    | 7           | VTS CMT                                 | Code indiquant le fournisseur TPEP qui a fourni l’enregistrement. 1 = Creative Mobile Technologies, LLC ; 2 = VeriFone Inc.                                                                                                                                   |
| vendorID             | int       | 2           | 2 1                                     | Code indiquant le fournisseur LPEP qui a fourni l’enregistrement. 1 = Creative Mobile Technologies, LLC ; 2 = VeriFone Inc.                                                                                                                                   |

## <a name="preview"></a>Préversion

| vendorID | tpepPickupDateTime    | tpepDropoffDateTime   | passengerCount | tripDistance | puLocationId | doLocationId | rateCodeId | storeAndFwdFlag | paymentType | fareAmount | extra | mtaTax | improvementSurcharge | tipAmount | tollsAmount | totalAmount | puYear | puMonth |
|----------|-----------------------|-----------------------|----------------|--------------|--------------|--------------|------------|-----------------|-------------|------------|-------|--------|----------------------|-----------|-------------|-------------|--------|---------|
| 2        | 1/24/2088 12:25:39 AM | 1/24/2088 7:28:25 AM  | 1              | 4,05         | 24           | 162          | 1          | N               | 2           | 14.5       | 0     | 0,5    | 0.3                  | 0         | 0           | 15,3        | 2088   | 1       |
| 2        | 1/24/2088 12:15:42 AM | 1/24/2088 12:19:46 AM | 1              | 0,63         | 41           | 166          | 1          | N               | 2           | 4.5        | 0     | 0,5    | 0.3                  | 0         | 0           | 5.3         | 2088   | 1       |
| 2        | 11/4/2084 12:32:24 PM | 11/4/2084 12:47:41 PM | 1              | 1.34         | 238          | 236          | 1          | N               | 2           | 10         | 0     | 0,5    | 0.3                  | 0         | 0           | 10.8        | 2084   | 11      |
| 2        | 11/4/2084 12:25:53 PM | 11/4/2084 12:29:00 PM | 1              | 0.32         | 238          | 238          | 1          | N               | 2           | 4          | 0     | 0,5    | 0.3                  | 0         | 0           | 4.8         | 2084   | 11      |
| 2        | 11/4/2084 12:08:33 PM | 11/4/2084 12:22:24 PM | 1              | 1.85         | 236          | 238          | 1          | N               | 2           | 10         | 0     | 0,5    | 0.3                  | 0         | 0           | 10.8        | 2084   | 11      |
| 2        | 11/4/2084 11:41:35 AM | 11/4/2084 11:59:41 AM | 1              | 1,65         | 68           | 237          | 1          | N               | 2           | 12.5       | 0     | 0,5    | 0.3                  | 0         | 0           | 13.3        | 2084   | 11      |
| 2        | 11/4/2084 11:27:28 AM | 11/4/2084 11:39:52 AM | 1              | 1.07         | 170          | 68           | 1          | N               | 2           | 9          | 0     | 0,5    | 0.3                  | 0         | 0           | 9.8         | 2084   | 11      |
| 2        | 11/4/2084 11:19:06 AM | 11/4/2084 11:26:44 AM | 1              | 1.3          | 107          | 170          | 1          | N               | 2           | 7.5        | 0     | 0,5    | 0.3                  | 0         | 0           | 8.3         | 2084   | 11      |
| 2        | 11/4/2084 11:02:59 AM | 11/4/2084 11:15:51 AM | 1              | 1.85         | 113          | 137          | 1          | N               | 2           | 10         | 0     | 0,5    | 0.3                  | 0         | 0           | 10.8        | 2084   | 11      |
| 2        | 11/4/2084 10:46:05 AM | 11/4/2084 10:50:09 AM | 1              | 0,62         | 231          | 231          | 1          | N               | 2           | 4.5        | 0     | 0,5    | 0.3                  | 0         | 0           | 5.3         | 2084   | 11      |

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
from azureml.opendatasets import NycTlcYellow

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
nyc_tlc_df = nyc_tlc.to_pandas_dataframe()

nyc_tlc_df.info()
```

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

```python
# Pip install packages
import os, sys

!{sys.executable} -m pip install azure-storage-blob
!{sys.executable} -m pip install pyarrow
!{sys.executable} -m pip install pandas

# Azure storage access info
azure_storage_account_name = "azureopendatastorage"
azure_storage_sas_token = r""
container_name = "nyctlc"
folder_name = "yellow"

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

# Read the parquet file into Pandas data frame
import pandas as pd

print('Reading the parquet file into Pandas data frame')
df = pd.read_parquet(filename)

# you can add your filter at below
print('Loaded as a Pandas data frame: ')
df
```

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

Exemple non disponible pour cette combinaison de plateforme et de package.

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import NycTlcYellow

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
nyc_tlc_df = nyc_tlc.to_spark_dataframe()

display(nyc_tlc_df.limit(5))
```

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "nyctlc"
blob_relative_path = "yellow"
blob_sas_token = r"

# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)

# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')

# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

---

### <a name="azure-synapse"></a>Azure Synapse

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
from azureml.opendatasets import NycTlcYellow

from datetime import datetime
from dateutil import parser

end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
nyc_tlc_df = nyc_tlc.to_spark_dataframe()

# Display top 5 rows
display(nyc_tlc_df.limit(5))
```

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "nyctlc"
blob_relative_path = "yellow"
blob_sas_token = r""

# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)

# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')

# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

---

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres jeux de données du [catalogue Open Datasets](dataset-catalog.md).