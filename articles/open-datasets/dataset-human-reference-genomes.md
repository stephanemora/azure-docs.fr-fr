---
title: Génomes humains de référence
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données Génomes humains de référence dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 2e8afede19a948af1863b6b24f277ab934861f22
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038620"
---
# <a name="human-reference-genomes"></a>Génomes humains de référence

Ce jeu de données comprend deux références de génome humain assemblées par le consortium [Genome Reference Consortium](https://www.ncbi.nlm.nih.gov/grc) : Hg19 et Hg38.

Pour en savoir plus sur les données de Hg19 (GRCh37), consultez le [rapport GRCh37 sur NCBI](https://www.ncbi.nlm.nih.gov/assembly/GCF_000001405.13/).

Pour en savoir plus sur les données de Hg38, consultez le [rapport GRCh38 sur NCBI](http://www.ncbi.nlm.nih.gov/assembly/GCF_000001405.26/).

Plus de détails sur les données sont disponibles sur le site [NCBI RefSeq](https://www.ncbi.nlm.nih.gov/refseq/).

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>Source de données

Ce jeu de données provient de deux emplacements FTP :

ftp://ftp.ncbi.nih.gov/genomes/refseq/vertebrate_mammalian/Homo_sapiens/all_assembly_versions/GCF_000001405.25_GRCh37.p13/

ftp://ftp.ncbi.nih.gov/genomes/refseq/vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/

Les noms des blobs sont précédés du segment « vertebrate_mammalian » de l’URI.

## <a name="data-volumes-and-update-frequency"></a>Volumes de données et fréquence de mise à jour

Ce jeu de données contient approximativement 10 Go de données et est mis à jour quotidiennement.

## <a name="storage-location"></a>Emplacement de stockage

Ce jeu de données est stocké dans les régions Azure USA Ouest 2 et USA Centre-Ouest. L’allocation de ressources de calcul dans la région USA Ouest 2 ou USA Centre-Ouest est recommandée à des fins d’affinité.

## <a name="data-access"></a>Accès aux données

USA Ouest 2 : https://datasetreferencegenomes.blob.core.windows.net/dataset

USA Centre-Ouest : https://datasetreferencegenomes-secondary.blob.core.windows.net/dataset

[Jeton SAS](/azure/storage/common/storage-sas-overview) : sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=JtQoPFqiC24GiEB7v9zHLi4RrA2Kd1r%2F3iFt2l9%2FlV8%3D

## <a name="use-terms"></a>Conditions d’utilisation

Les données sont disponibles sans restrictions. Pour plus d’informations et pour obtenir des détails sur les citations, consultez le [site NCBI Reference Sequence Database](https://www.ncbi.nlm.nih.gov/refseq/).

## <a name="contact"></a>Contact

Si vous avez des questions ou des commentaires sur ce jeu de données, contactez le consortium [Genome Reference Consortium](https://www.ncbi.nlm.nih.gov/grc/contact-us).

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-reference-genomes -->

> [!TIP]
> **[Téléchargez le notebook à la place](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-reference-genomes)** .

## <a name="getting-the-reference-genomes-from-azure-open-datasets"></a>Obtention des génomes de référence à partir d’Azure Open Datasets

Plusieurs données génomiques publiques ont été chargées comme Azure Open Dataset [ici](https://azure.microsoft.com/services/open-datasets/catalog/). Nous créons un service blob lié à ce jeu de données ouvert. Vous trouverez des exemples de procédure d’appel de données à partir d’Azure Open Datasets pour le jeu de données `Reference Genomes` dans ce qui suit :

Les utilisateurs peuvent appeler et télécharger le chemin suivant avec ce notebook : https://datasetreferencegenomes.blob.core.windows.net/dataset/vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/GCF_000001405.39_GRCh38.p13_assembly_structure/genomic_regions_definitions.txt

**Remarque importante :** Les utilisateurs doivent se connecter à leur compte Azure via Azure CLI pour afficher les données avec le SDK Azure ML. En revanche, il n’est pas nécessaire d’effectuer des actions pour télécharger les données.

[Installer l’interface de ligne de commande Microsoft Azure](/cli/azure/install-azure-cli).

### <a name="calling-the-data-from--reference-genome-datasets"></a>Appel des données à partir des « jeux de données Génome de référence »

```python
import azureml.core
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

```python
from azureml.core import  Dataset
reference_dataset = Dataset.File.from_files('https://datasetreferencegenomes.blob.core.windows.net/dataset')
mount = reference_dataset.mount()
```

```python
import os

REF_DIR = '/vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/GCF_000001405.39_GRCh38.p13_assembly_structure'
path = mount.mount_point + REF_DIR

with mount:
    print(os.listdir(path))
```

```python
import pandas as pd

# create mount context
mount.start()

# specify path to genomic_regions_definitions.txt file
REF_DIR = 'vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/GCF_000001405.39_GRCh38.p13_assembly_structure'
metadata_filename = '{}/{}/{}'.format(mount.mount_point, REF_DIR, 'genomic_regions_definitions.txt')

# read genomic_regions_definitions.txt file
metadata = pd.read_table(metadata_filename)
metadata
```

### <a name="download-the-specific-file"></a>Télécharger le fichier spécifique

```python
import os
import uuid
import sys
from azure.storage.blob import BlockBlobService, PublicAccess

blob_service_client = BlockBlobService(account_name='datasetreferencegenomes',sas_token='sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=JtQoPFqiC24GiEB7v9zHLi4RrA2Kd1r%2F3iFt2l9%2FlV8%3D')     
blob_service_client.get_blob_to_path('dataset/vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/GCF_000001405.39_GRCh38.p13_assembly_structure', 'genomic_regions_definitions.txt', './genomic_regions_definitions.txt')
```

<!-- nbend -->

---

## <a name="next-steps"></a>Étapes suivantes

Affichez les autres jeux de données dans le [catalogue Open Datasets](dataset-catalog.md).