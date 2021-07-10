---
title: Jeu de données des taxis et limousines verts de la ville de New York
titleSuffix: Azure Open Datasets
description: Découvrez l’utilisation du jeu de données des taxis et limousines verts de la ville de New York dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 058969168b09c1eb394b6fc7a9f06c401c7f9763
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038610"
---
# <a name="nyc-taxi--limousine-commission---green-taxi-trip-records"></a>Commission des services de taxis et de limousines de la ville de New York - enregistrements de trajets en taxi vert

Les enregistrements de trajets en taxi vert incluent des champs indiquant les dates et heures de début et fin de trajet, les lieux de départ et d’arrivée, la distance des trajets, les tarifs détaillés, les types de tarifs, les types de paiement et le nombre de passagers signalé par le conducteur.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>Volume et conservation des données

Ce jeu de données est stocké au format Parquet. Il y a environ 80 millions d’enregistrements (2 Go) en tout depuis 2018.

Ce jeu de données contient les enregistrements historiques accumulés de 2009 à 2018. Vous pouvez utiliser les paramètres de paramétrage de notre SDK pour récupérer les données dans un intervalle de temps spécifique.

## <a name="storage-location"></a>Emplacement de stockage

Ce jeu de données est stocké dans la région Azure USA Est. L’allocation de ressources de calcul dans la région USA Est est recommandée à des fins d’affinité.

## <a name="additional-information"></a>Informations supplémentaires

Commission des services de taxis et de limousines de la ville de New York (en anglais)

Les données ont été collectées et fournies à la Commission des services de taxis et de limousines de la ville de New York (TLC) par des fournisseurs de technologie agréés dans le cadre du programme d’amélioration du trafic passagers et taxis (TPEP/LPEP). Les données sur les trajets n’ont pas été créées par la TLC et celle-ci ne fait aucune déclaration quant à l’exactitude de ces données.

Affichez l’[emplacement du jeu de données d’origine](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page) et les [conditions d’utilisation d’origine](https://www1.nyc.gov/home/terms-of-use.page).

## <a name="columns"></a>Colonnes

| Nom                 | Type de données | Unique     | Valeurs (échantillon)                         | Description                                                                                                                                                                                                                                          |
|----------------------|-----------|------------|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| doLocationId         | string    | 264        | 74 42                                   | Zone de taxi TLC DOLocationID où le compteur a été arrêté.                                                                                                                                                                                    |
| dropoffLatitude      | double    | 109 721    | 40,7743034362793 40,77431869506836      | Déconseillé depuis juillet 2016                                                                                                                                                                                                                       |
| dropoffLongitude     | double    | 75 502     | -73,95272827148438 -73,95274353027344   | Déconseillé depuis juillet 2016                                                                                                                                                                                                                       |
| extra                | double    | 202        | 0,5 1,0                                 | Frais et suppléments divers. Actuellement, cela n’inclut que les frais de 0,50 $ et de 1 $ pour les heure de pointe et de nuit.                                                                                                                                 |
| fareAmount           | double    | 10 367     | 6,0 5,5                                 | Tarif temps-distance calculé par le compteur.                                                                                                                                                                                                  |
| improvementSurcharge | string    | 92         | 0,3 0                                   | Supplément pour l’amélioration de 0,30 $ évalué sur les trajets hélés à une station. Le supplément pour amélioration a commencé à être perçu en 2015.                                                                                                                         |
| lpepDropoffDatetime  | timestamp | 58 100 713 | 22-05-2016 00:00:00 09-05-2016 00:00:00 | Date et heure auxquelles le compteur a été arrêté.                                                                                                                                                                                                     |
| lpepPickupDatetime   | timestamp | 58 157 349 | 22-10-2013 12:40:36 09-08-2014 15:54:25 | Date et heure auxquelles le compteur a été lancé.                                                                                                                                                                                                        |
| mtaTax               | double    | 34         | 0,5 -0,5                                | Taxe de 0,50 USD MTA déclenchée automatiquement en fonction du tarif affiché au compteur.                                                                                                                                                                      |
| passengerCount       | int       | 10         | 1 2                                     | Nombre de passagers dans le véhicule. Cette valeur est entrée par le conducteur.                                                                                                                                                                             |
| paymentType          | int       | 5          | 2 1                                     | Code numérique indiquant comment le passager a payé le trajet. 1 = Carte de crédit 2 = Argent liquide 3 = Aucuns frais 4 = Litige 5 = Inconnu 6 = Voyage annulé                                                                                                              |
| pickupLatitude       | double    | 95 110     | 40,721351623535156 40,721336364746094   | Déconseillé depuis juillet 2016                                                                                                                                                                                                                       |
| pickupLongitude      | double    | 55 722     | -73,84429931640625 -73,84429168701172   | Déconseillé depuis juillet 2016                                                                                                                                                                                                                       |
| puLocationId         | string    | 264        | 74 41                                   | Zone de taxi TLC où le compteur a été lancé.                                                                                                                                                                                                    |
| puMonth              | int       | 12         | 3 5                                     |                                                                                                                                                                                                                                                      |
| puYear               | int       | 14         | 2015 2016                               |                                                                                                                                                                                                                                                      |
| rateCodeID           | int       | 7          | 1 5                                     | Code tarifaire final en vigueur à la fin du trajet. 1 = Taux standard 2 = JFK 3 = Newark 4 = Nassau ou Westchester 5 = Prix négocié 6 = Voyage en groupe                                                                                                    |
| storeAndFwdFlag      | string    | 2          | N Y                                     | Cet indicateur indique si l’enregistrement de trajet a été conservé dans la mémoire du véhicule avant son envoi au fournisseur (ce qui s’appelle « stockage et transmission »), car le véhicule n’était pas connecté au serveur. Y = Trajet stockage et transmission N = Pas un trajet stockage et transmission |
| tipAmount            | double    | 6 206      | 1,0 2,0                                 | Montant du pourboire - Ce champ est automatiquement renseigné pour les pourboires en carte bancaire. Les pourboires en espèces ne sont pas inclus.                                                                                                                                                 |
| tollsAmount          | double    | 2 150      | 5,54 5,76                               | Montant total de tous les péages payés lors du trajet.                                                                                                                                                                                                              |
| totalAmount          | double    | 20 188     | 7,8 6,8                                 | Montant total facturé aux passagers. N’inclut pas les pourboires.                                                                                                                                                                                  |
| tripDistance         | double    | 7 060      | 0,9 1,0                                 | Distance parcourue en miles indiquée par le compteur.                                                                                                                                                                                        |
| tripType             | int       | 3          | 1 2                                     | Code indiquant si le trajet a été hélé dans la rue ou s’il s’agit d’une commande automatiquement attribuée en fonction du tarif mesuré utilisé, mais pouvant être modifié par le conducteur. 1 = Héler en rue 2 = Envoyer                                                      |
| vendorID             | int       | 2          | 2 1                                     | Code indiquant le fournisseur LPEP qui a fourni l’enregistrement. 1 = Creative Mobile Technologies, LLC ; 2 = VeriFone Inc.                                                                                                                                 |

## <a name="preview"></a>Préversion

| vendorID | lpepPickupDatetime     | lpepDropoffDatetime    | passengerCount | tripDistance | puLocationId | doLocationId | rateCodeID | storeAndFwdFlag | paymentType | fareAmount | extra | mtaTax | improvementSurcharge | tipAmount | tollsAmount | totalAmount | tripType | puYear | puMonth |
|----------|------------------------|------------------------|----------------|--------------|--------------|--------------|------------|-----------------|-------------|------------|-------|--------|----------------------|-----------|-------------|-------------|----------|--------|---------|
| 2        | 24/06/2081 5:40:37 PM   | 24/06/2081 6:42:47 PM   | 1              | 16,95        | 93           | 117          | 1          | N               | 1           | 52         | 1     | 0,5    | 0.3                  | 0         | 2.16        | 55,96       | 1        | 2081   | 6       |
| 2        | 28/11/2030 12:19:29 AM | 28/11/2030 12:25:37 AM | 1              | 1.08         | 42           | 247          | 1          | N               | 2           | 6.5        | 0     | 0,5    | 0.3                  | 0         | 0           | 7.3         | 1        | 2030   | 11      |
| 2        | 28/11/2030 12:14:50 AM | 28/11/2030 12:14:54 AM | 1              | 0,03         | 42           | 42           | 5          | N               | 2           | 5          | 0     | 0      | 0                    | 0         | 0           | 5           | 2        | 2030   | 11      |
| 2        | 14/11/2020 11:38:07 AM | 14/11/2020 11:42:22 AM | 1              | 0,63         | 129          | 129          | 1          | N               | 2           | 4.5        | 1     | 0,5    | 0.3                  | 0         | 0           | 6.3         | 1        | 2020   | 11      |
| 2        | 14/11/2020 9:55:36 AM  | 14/11/2020 10:04:54 AM | 1              | 3.8          | 82           | 138          | 1          | N               | 2           | 12.5       | 1     | 0,5    | 0.3                  | 0         | 0           | 14.3        | 1        | 2020   | 11      |
| 2        | 26/08/2019 4:18:37 PM   | 26/08/2019 4:19:35 PM   | 1              | 0            | 264          | 264          | 1          | N               | 2           | 1          | 0     | 0,5    | 0.3                  | 0         | 0           | 1.8         | 1        | 2019   | 8       |
| 2        | 01/07/2019 8:28:33 AM    | 01/07/2019 8:32:33 AM    | 1              | 0.71         | 7            | 7            | 1          | N               | 1           | 5          | 0     | 0,5    | 0.3                  | 1,74      | 0           | 7,54        | 1        | 2019   | 7       |
| 2        | 01/07/2019 12:04:53 AM   | 01/07/2019 12:21:56 AM   | 1              | 2.71         | 223          | 145          | 1          | N               | 2           | 13         | 0,5   | 0,5    | 0.3                  | 0         | 0           | 14.3        | 1        | 2019   | 7       |
| 2        | 01/07/2019 12:04:11 AM   | 01/07/2019 12:21:15 AM   | 1              | 3,14         | 166          | 142          | 1          | N               | 2           | 14.5       | 0,5   | 0,5    | 0.3                  | 0         | 0           | 18,55       | 1        | 2019   | 7       |
| 2        | 01/07/2019 12:03:37 AM   | 01/07/2019 12:09:27 AM   | 1              | 0,78         | 74           | 74           | 1          | N               | 1           | 6          | 0,5   | 0,5    | 0.3                  | 1,46      | 0           | 8,76        | 1        | 2019   | 7       |

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
from azureml.opendatasets import NycTlcGreen

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcGreen(start_date=start_date, end_date=end_date)
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
folder_name = "green"

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
from azureml.opendatasets import NycTlcGreen

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcGreen(start_date=start_date, end_date=end_date)
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
blob_relative_path = "green"
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

### <a name="azure-synapse"></a>Azure Synapse

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
from azureml.opendatasets import NycTlcGreen

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcGreen(start_date=start_date, end_date=end_date)
nyc_tlc_df = nyc_tlc.to_spark_dataframe()

# Display top 5 rows
display(nyc_tlc_df.limit(5))

# Display data statistic information
display(nyc_tlc_df, summary = True)
```

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Exemple non disponible pour cette combinaison de plateforme et de package.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "nyctlc"
blob_relative_path = "green"
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

Affichez les autres jeux de données dans le [catalogue Open Datasets](dataset-catalog.md).