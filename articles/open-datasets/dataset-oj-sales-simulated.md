---
title: OJ Sales Simulated
titleSuffix: Azure Open Datasets
description: Découvrez le fonctionnement du jeu de données OJ Sales Simulated dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 8f835e12bbfd17474fd2b928809394c93c19eae5
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038616"
---
# <a name="oj-sales-simulated"></a>OJ Sales Simulated 

Ce jeu de données est dérivé du jeu de données OJ de Dominick et inclut notamment des données simulées supplémentaires dans le but de fournir un jeu de données qui facilite la formation simultanée de milliers de modèles sur Azure Machine Learning.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

Les données portent sur les ventes hebdomadaires de jus d’orange réfrigéré sur 121 semaines. 3 991 magasins sont inclus avec trois marques de jus d’orange par magasin, de sorte que 11 973 modèles peuvent être formés.

[Affichez la description du jeu de données d’origine](http://www.cs.unitn.it/~taufer/QMMA/L10-OJ-Data.html) ou [téléchargez le jeu de données](http://www.cs.unitn.it/~taufer/Data/oj.csv).

## <a name="columns"></a>Colonnes

| Nom | Type de données | Unique | Valeurs (échantillon) | Description |
|-|-|-|-|-|
| Publication | int |  | 1 | Valeur indiquant s’il existe des publications pour ce jus orange au cours de la semaine 0 : Aucune publication 1 : Publications |
| Marque | string |  | dominicks tropicana | Marque de jus d’orange |
| Price | double |  | 2,6 2,09 | Prix du jus d’orange (en USD) |
| Quantité | int |  | 10 939 11 638 | Quantité de jus d’orange vendue pendant cette semaine |
| Chiffre d’affaires | double |  | 38 438,4 36 036,0 | Revenus des ventes de jus d’orange pendant cette semaine (en USD) |
| Magasin | int |  | 2 658 1 396 | Nombre de magasins où le jus d’orange a été vendu |
| WeekStarting | timestamp |  | 09-08-1990 00:00:00 20-02-1992 00:00:00 | Date indiquant à quelle semaine les ventes sont liées |

## <a name="preview"></a>Préversion

| WeekStarting | Magasin | Marque | Quantité | Publication | Price | Chiffre d’affaires |
|-|-|-|-|-|-|-|
| 01/10/1992 12:00:00 AM | 3 571 | minute.maid | 13 247 | 1 | 2,42 | 32 057,74 |
| 01/10/1992 12:00:00 AM | 2999 | minute.maid | 18461 | 1 | 2.69 | 49 660,09 |
| 01/10/1992 12:00:00 AM | 1198 | minute.maid | 13222 | 1 | 2.64 | 34 906,08 |
| 01/10/1992 12:00:00 AM | 3916 | minute.maid | 12 923 | 1 | 2.45 | 31 661,35 |
| 01/10/1992 12:00:00 AM | 1688 | minute.maid | 9 380 | 1 | 2.46 | 23 074,8 |
| 01/10/1992 12:00:00 AM | 1040 | minute.maid | 18 841 | 1 | 2,31 | 43 522,71 |
| 01/10/1992 12:00:00 AM | 1938 | minute.maid | 14202 | 1 | 2.19 | 31 102,38 |
| 01/10/1992 12:00:00 AM | 2 405 | minute.maid | 16 326 | 1 | 2.05 | 33 468,3 |
| 01/10/1992 12:00:00 AM | 1972 | minute.maid | 16 380 | 1 | 2.12 | 34 725,6 |

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=sample-oj-sales-simulated -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=sample-oj-sales-simulated)** .

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
datastore = ws.get_default_config()
```

```python
from azureml.opendatasets import OjSalesSimulated
```

### <a name="read-data-from-azure-open-datasets"></a>Lire les données à partir d’Azure Open Datasets

```python
# Create a Data Directory in local path
import os

oj_sales_path = "oj_sales_data"

if not os.path.exists(oj_sales_path):
    os.mkdir(oj_sales_path)
```

```python
# Pull all of the data
oj_sales_files = OjSalesSimulated.get_file_dataset()

# or pull a subset of the data
oj_sales_files = OjSalesSimulated.get_file_dataset(num_files=10)
```

```python
oj_sales_files.download(oj_sales_path, overwrite=True)
```

### <a name="upload-the-individual-datasets-to-blob-storage"></a>Charger les jeux de données individuels vers Stockage Blob
Nous chargeons les données vers Blob et allons créer le FileDataset à partir de ce dossier de fichiers CSV.  

```python
target_path = 'oj_sales_data'

datastore.upload(src_dir = oj_sales_path,
                target_path = target_path,
                overwrite = True, 
                show_progress = True)
```

### <a name="create-the-file-dataset"></a>Créer le jeu de données du fichier 
Nous devons définir le chemin d’accès des données pour créer le [FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset). 


```python
from azureml.core.dataset import Dataset

ds_name = 'oj_data'
path_on_datastore = datastore.path(target_path + '/')

input_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
```

### <a name="register-the-file-dataset-to-the-workspace"></a>Inscrire le jeu de données du fichier dans l’espace de travail 
Nous souhaitons inscrire le jeu de données dans notre espace de travail pour pouvoir l’appeler en tant qu’entrée dans notre pipeline pour prévision. 


```python
registered_ds = input_ds.register(ws, ds_name, create_new_version=True)
named_ds = registered_ds.as_named_input(ds_name)
```

<!-- nbend -->

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=sample-oj-sales-simulated -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=sample-oj-sales-simulated)** .

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
# Download or mount OJ Sales raw files Azure Machine Learning file datasets.
# This works only for Linux based compute. See https://docs.microsoft.com/en-us/azure/machine-learning/service/how-to-create-register-datasets to learn more about datasets.

from azureml.opendatasets import OjSalesSimulated

ojss_file = OjSalesSimulated.get_file_dataset()
ojss_file
```

```
ojss_file.to_path()
```

```
# Download files to local storage
import os
import tempfile

mount_point = tempfile.mkdtemp()
ojss_file.download(mount_point, overwrite=True)
```

```
# Mount files. Useful when training job will run on a remote compute.
import gzip
import struct
import pandas as pd
import numpy as np

# load compressed OJ Sales Simulated gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        gz.read(4)
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return pd.DataFrame(res)
```

```
import sys
mount_point = tempfile.mkdtemp()
print(mount_point)
print(os.path.exists(mount_point))
print(os.listdir(mount_point))

if sys.platform == 'linux':
  print("start mounting....")
  with ojss_file.mount(mount_point):
    print(os.listdir(mount_point))  
    train_images_df = load_data(os.path.join(mount_point, 'train-tabular-oj-ubyte.gz'))
    print(train_images_df.info())
```

<!-- nbend -->

---

## <a name="next-steps"></a>Étapes suivantes

Affichez les autres jeux de données dans le [catalogue Open Datasets](dataset-catalog.md).