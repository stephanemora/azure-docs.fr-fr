---
title: Annotations ClinVar
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données d’annotations ClinVar dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 384eb565882cf5f62cec1c6d38769647a82ec712
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038644"
---
# <a name="clinvar-annotations"></a>Annotations ClinVar

[ClinVar](https://www.ncbi.nlm.nih.gov/clinvar/) est une archive publique de rapports sur les relations entre les variations et les phénotypes humains, en libre accès et avec des éléments de preuve à l’appui. Cette archive facilite la communication et l’accès aux relations affirmées entre variation humaine et état d’intégrité observé, ainsi que l’historique de cette interprétation. Elle fournit un ensemble d’interprétations cliniques plus important qui peut être incorporé aux workflows et aux applications de génomique.

Pour plus d’informations sur les données, consultez le [Dictionnaire de données](https://www.ncbi.nlm.nih.gov/projects/clinvar/ClinVarDataDictionary.pdf) et la [FAQ](https://www.ncbi.nlm.nih.gov/clinvar/docs/faq/).

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>Source de données

Ce jeu de données reflète ftp://ftp.ncbi.nlm.nih.gov/pub/clinvar/xml/

## <a name="data-volumes-and-update-frequency"></a>Volumes de données et fréquence de mise à jour

Ce jeu de données contient approximativement 56 Go de données et est mis à jour quotidiennement.

## <a name="storage-location"></a>Emplacement de stockage

Ce jeu de données est stocké dans les régions Azure USA Ouest 2 et USA Centre-Ouest. L’allocation de ressources de calcul dans la région USA Ouest 2 ou USA Centre-Ouest est recommandée à des fins d’affinité.

## <a name="data-access"></a>Accès aux données

USA Ouest 2 : https://datasetclinvar.blob.core.windows.net/dataset

USA Centre-Ouest : https://datasetclinvar-secondary.blob.core.windows.net/dataset

[Jeton SAS](../storage/common/storage-sas-overview.md) : sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=qFPPwPba1RmBvaffkzkLuzabYU5dZstSTgMwxuLNME8%3D

## <a name="use-terms"></a>Conditions d’utilisation
Les données sont disponibles sans restrictions. Plus d’informations, notamment sur les citations, consultez [Consultation et utilisation des données dans ClinVar](https://www.ncbi.nlm.nih.gov/clinvar/docs/maintenance_use/).

## <a name="contact"></a>Contact

Si vous avez des questions ou des commentaires sur ce jeu de données, contactez clinvar@ncbi.nlm.nih.gov.

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-clinvar -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-clinvar)** .

## <a name="getting-the-clinvar-data-from-azure-open-dataset"></a>Récupération des données ClinVar à partir d’un jeu de données Azure Open Datasets

Différentes données publiques de génomique ont été chargées dans un [jeu de données Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/). Nous créons un service blob lié à ce jeu de données ouvert. Vous trouverez ici des exemples de procédure d’appel de données à partir d’Azure Open Datasets pour le jeu de données `ClinVar` :

Les utilisateurs peuvent appeler et télécharger le chemin suivant avec ce notebook : https://datasetclinvar.blob.core.windows.net/dataset/ClinVarFullRelease_00-latest.xml.gz.md5.

> [!NOTE]
> Les utilisateurs doivent se connecter à leur compte Azure avec Azure CLI pour afficher les données avec le kit SDK Azure ML. En revanche, ils n’ont aucune action à effectuer pour télécharger les données.

Pour plus d’informations sur l’installation d’Azure CLI, consultez [Installation d’Azure CLI](/cli/azure/install-azure-cli).

### <a name="calling-the-data-from--clinvar-data-set"></a>Appel des données à partir de « ClinVar Data Set »

```python
import azureml.core
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

```python
from azureml.core import  Dataset
reference_dataset = Dataset.File.from_files('https://datasetclinvar.blob.core.windows.net/dataset')
mount = reference_dataset.mount()
```

```python
import os

REF_DIR = '/dataset'
path = mount.mount_point + REF_DIR

with mount:
    print(os.listdir(path))
```

```python
import pandas as pd

# create mount context
mount.start()

# specify path to README file
REF_DIR = '/dataset'
metadata_filename = '{}/{}/{}'.format(mount.mount_point, REF_DIR, '_README')

# read README file
metadata = pd.read_table(metadata_filename)
metadata
```

### <a name="download-the-specific-file"></a>Téléchargement du fichier proprement dit

```python
import os
import uuid
import sys
from azure.storage.blob import BlockBlobService, PublicAccess

blob_service_client = BlockBlobService(account_name='datasetclinvar', sas_token='sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=qFPPwPba1RmBvaffkzkLuzabYU5dZstSTgMwxuLNME8%3D')     
blob_service_client.get_blob_to_path('dataset', 'ClinVarFullRelease_00-latest.xml.gz.md5', './ClinVarFullRelease_00-latest.xml.gz.md5')
```

<!-- nbend -->

---

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres jeux de données du [catalogue Open Datasets](dataset-catalog.md).