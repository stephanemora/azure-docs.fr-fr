---
title: Jeu de données Microsoft News Recommandation
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données Microsoft News Recommandation dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: ecbad0a8c23bd2781373923ffa470a06509ee852
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038592"
---
# <a name="microsoft-news-recommendation"></a>Microsoft News Recommendation

**MI** crosoft **N** ews **D** ataset (MIND) est un jeu de données à grande échelle pour la recherche d’actualités suggérées. Il a été constitué à partir de journaux de comportement anonymisés du site web Microsoft News. La vocation de MIND est de faire office de jeu de données de référence pour les suggestions d’actualités, et de faciliter la recherche dans les systèmes de recommandation et les suggestions d’actualités.

MIND contient environ 160 000 articles d'actualités en anglais et plus de 15 millions de journaux d'impression générés par 1 million d'utilisateurs. Chaque article d’actualité comprend un contenu textuel riche composé d’un titre, d’un résumé, d’un corps, d’une catégorie et d’entités. Chaque journal d’impression contient les événements sur lesquels un utilisateur a cliqué, ceux sur lesquels il n’a pas cliqué et l’historique des comportements de clic sur des actualités de l’utilisateur avant l’impression. Afin de protéger la vie privée de l’utilisateur, celui-ci a été déconnecté du système de production après un hachage sécurisé dans un identifiant anonymisé. Pour des informations plus détaillées sur le jeu de données MIND, vous pouvez vous consulter le document [MIND : jeu de données à grande échelle pour des suggestions d’actualités](https://msnews.github.io/assets/doc/ACL2020_MIND.pdf).
 
## <a name="volume"></a>Volume

Les données relatives à l’apprentissage et à la validation figurent dans un dossier compressé au format zip, qui contient quatre fichiers différents :

| NOM DU FICHIER | Description |
|-|-|
| behaviors.tsv | Historique des clics et les journaux d'impression des utilisateurs |
| news.tsv | Informations des articles d’actualités |
| entity_embedding.vec | Incorporations d’entités dans les actualités extraites du graphe de connaissances |
| relation_embedding.vec | Incorporations des relations entre les entités extraites du graphe de connaissances |

### <a name="behaviorstsv"></a>behaviors.tsv

Le fichier behaviors.tsv contient les journaux d’impression et les historiques de clic sur des actualités des utilisateurs. Il comporte 5 colonnes séparées par le symbole de tabulation :

- ID d’impression. ID d’une impression.
- ID d'utilisateur. ID anonyme d’un utilisateur.
- Heure. Heure d’impression au format « MM/JJ/AAAA HH:MM:SS ».
- History. L'historique des clics sur des actualités (liste d’ID des actualités consultées) de cet utilisateur avant cette impression.
- Impressions. Liste des actualités affichées dans cette impression et des comportements de clic de l’utilisateur sur celles-ci (1 pour un clic, 0 pour zéro clic).

Un exemple est présenté dans le tableau ci-dessous :

| COLUMN | CONTENT |
|-|-|
| ID d’impression | 123 |
| ID d'utilisateur | U131 |
| Temps | 13/11/2019 8:36:57 |
| Historique | N11 N21 N103 |
| Impressions | N4-1 N34-1 N156-0 N207-0 N198-0 |

### <a name="newstsv"></a>news.tsv

Le fichier news.tsv contient les informations détaillées des articles d’actualités inclus dans le fichier behaviors.tsv. Il comporte 7 colonnes séparées par le symbole de tabulation :

- ID d’actualités
- Category
- Sous-catégorie
- Titre
- Résumé
- URL
- Entités de titre (entités contenues dans le titre de cette actualité)
- Entités de résumé (entités contenues dans le résumé de cette actualité)

Le corps du contenu complet des articles d’actualités de MSN n’est pas disponible pour téléchargement en raison de la structure des licences. Toutefois, pour votre commodité, nous avons fourni un [script utilitaire](https://github.com/msnews/MIND/tree/master/crawler) pour vous aider à analyser les pages web d'actualités des URL de MSN dans le jeu de données. En raison de la limitation dans le temps, certaines URL ont expiré et il n’est plus possible de les consulter. Actuellement, nous faisons de notre mieux pour résoudre ce problème.

Un exemple est présenté dans le tableau suivant :

| COLUMN | CONTENT |
|-|-|
| ID d’actualités | N37378 |
| Category | sports |
| SubCategory | golf |
| Titre | PGA Tour winners |
| Résumé | A gallery of recent winners on the PGA Tour. |
| URL | https://www.msn.com/en-us/sports/golf/pga-tour-winners/ss-AAjnQjj?ocid=chopendata |
| Entités de titre | [{“Label”: “PGA Tour”, “Type”: “O”, “WikidataId”: “Q910409”, “Confidence”: 1.0, “OccurrenceOffsets”: [0], “SurfaceForms”: [“PGA Tour”]}] |
| Entités de résumé | [{“Label”: “PGA Tour”, “Type”: “O”, “WikidataId”: “Q910409”, “Confidence”: 1.0, “OccurrenceOffsets”: [35], “SurfaceForms”: [“PGA Tour”]}] |

Les descriptions des clés du dictionnaire dans la colonne « Entités » sont répertoriées comme suit :

| CLÉS | Description |
|-|-|
| Étiquette | Nom de l’entité dans le graphe de connaissances de Wikidata |
| Type | Type de cette entité dans Wikidata |
| WikidataId | ID de l’entité dans Wikidata |
| Confiance | Confiance de la liaison d'entités |
| OccurrenceOffsets | Décalage de l'entité au niveau caractère dans le texte du titre ou du résumé |
| SurfaceForms | Noms des entités brutes dans le texte original |

### <a name="entity_embeddingvec--relation_embeddingvec"></a>entity_embedding.vec & relation_embedding.vec

Les fichiers entity_embedding.vec et relation_embedding.vec contiennent des incorporations à 100 dimensions des entités et des relations apprises du sous-graphe (du graphe de connaissances de WikiData) par la méthode TransE. Dans les deux fichiers, la première colonne est l'ID de l'entité ou de la relation, et les autres colonnes sont les valeurs des vecteurs d'incorporation. Nous espérons que ces données pourront faciliter la recherche de suggestions d’actualités tenant compte des connaissances. Un exemple se présente comme suit :

| ID | INCORPORATION DE VALEURS |
|-|-|
| Q42306013 | 0.014516 -0.106958 0.024590 … -0.080382 |

Pour certaines raisons, dans l’incorporation d’apprentissage à partir du sous-graphe, il se peut que quelques entités n’aient pas d’incorporations dans le fichier entity_embedding.vec.

## <a name="storage-location"></a>Emplacement de stockage

Les données sont stockées dans des blobs situés dans le centre de données USA Ouest/Est, dans le conteneur d'objets blob suivant : https://mind201910small.blob.core.windows.net/release/.

Au sein du conteneur, les jeux d’apprentissage et de validation sont comprimés respectivement dans les fichiers MINDlarge_train.zip and MINDlarge_dev.zip.

## <a name="additional-information"></a>Informations supplémentaires

Le jeu de données MIND est téléchargeable gratuitement à des fins de recherche sous [Termes du contrat de licence Microsoft Research](https://github.com/msnews/MIND/blob/master/MSR%20License_Data.pdf). Si vous avez des questions sur le jeu de données, contactez mind@microsoft.com.

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=microsoft-news-dataset -->

> [!TIP]
> **[Téléchargez le notebook à la place](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=microsoft-news-dataset)** .

## <a name="demo-notebook-for-accessing-mind-data-on-azure"></a>Notebook de démonstration pour l’accès aux données MIND sur Azure

Ce notebook fournit un exemple d’accès aux données MIND à partir d’un stockage d’objets blob sur Azure.

Les données MIND étant stockées dans le centre de données USA Ouest ou USA Est, ce notebook s’exécutera plus efficacement sur les services de calcul Azure situés dans les régions USA Ouest ou USA Est.

### <a name="imports-and-environment"></a>Importations et environnement

```python
import os
import tempfile
import shutil
import urllib
import zipfile
import pandas as pd

# Temporary folder for data we need during execution of this notebook (we'll clean up
# at the end, we promise)
temp_dir = os.path.join(tempfile.gettempdir(), 'mind')
os.makedirs(temp_dir, exist_ok=True)

# The dataset is split into training and validation set, each with a large and small version.
# The format of the four files are the same.
# For demonstration purpose, we will use small version validation set only.
base_url = 'https://mind201910small.blob.core.windows.net/release'
training_small_url = f'{base_url}/MINDsmall_train.zip'
validation_small_url = f'{base_url}/MINDsmall_dev.zip'
training_large_url = f'{base_url}/MINDlarge_train.zip'
validation_large_url = f'{base_url}/MINDlarge_dev.zip'
```

### <a name="functions"></a>Fonctions


```python
def download_url(url,
                 destination_filename=None,
                 progress_updater=None,
                 force_download=False,
                 verbose=True):
    """
    Download a URL to a temporary file
    """
    if not verbose:
        progress_updater = None
    # This is not intended to guarantee uniqueness, we just know it happens to guarantee
    # uniqueness for this application.
    if destination_filename is None:
        url_as_filename = url.replace('://', '_').replace('/', '_')
        destination_filename = \
            os.path.join(temp_dir,url_as_filename)
    if (not force_download) and (os.path.isfile(destination_filename)):
        if verbose:
            print('Bypassing download of already-downloaded file {}'.format(
                os.path.basename(url)))
        return destination_filename
    if verbose:
        print('Downloading file {} to {}'.format(os.path.basename(url),
                                                 destination_filename),
              end='')
    urllib.request.urlretrieve(url, destination_filename, progress_updater)
    assert (os.path.isfile(destination_filename))
    nBytes = os.path.getsize(destination_filename)
    if verbose:
        print('...done, {} bytes.'.format(nBytes))
    return destination_filename
```

### <a name="download-and-extract-the-files"></a>Télécharger et extraire les fichiers

```python
# For demonstration purpose, we will use small version validation set only.
# This file is about 30MB.
zip_path = download_url(validation_small_url, verbose=True)
with zipfile.ZipFile(zip_path, 'r') as zip_ref:
    zip_ref.extractall(temp_dir)

os.listdir(temp_dir)
```

### <a name="read-the-files-with-pandas"></a>Lire les fichiers avec Pandas

```python
# The behaviors.tsv file contains the impression logs and users' news click histories. 
# It has 5 columns divided by the tab symbol:
# - Impression ID. The ID of an impression.
# - User ID. The anonymous ID of a user.
# - Time. The impression time with format "MM/DD/YYYY HH:MM:SS AM/PM".
# - History. The news click history (ID list of clicked news) of this user before this impression.
# - Impressions. List of news displayed in this impression and user's click behaviors on them (1 for click and 0 for non-click).
behaviors_path = os.path.join(temp_dir, 'behaviors.tsv')
pd.read_table(
    behaviors_path,
    header=None,
    names=['impression_id', 'user_id', 'time', 'history', 'impressions'])
```

```python
# The news.tsv file contains the detailed information of news articles involved in the behaviors.tsv file.
# It has 7 columns, which are divided by the tab symbol:
# - News ID
# - Category
# - Subcategory
# - Title
# - Abstract
# - URL
# - Title Entities (entities contained in the title of this news)
# - Abstract Entities (entities contained in the abstract of this news)
news_path = os.path.join(temp_dir, 'news.tsv')
pd.read_table(news_path,
              header=None,
              names=[
                  'id', 'category', 'subcategory', 'title', 'abstract', 'url',
                  'title_entities', 'abstract_entities'
              ])
```

```python
# The entity_embedding.vec file contains the 100-dimensional embeddings
# of the entities learned from the subgraph by TransE method.
# The first column is the ID of entity, and the other columns are the embedding vector values.
entity_embedding_path = os.path.join(temp_dir, 'entity_embedding.vec')
entity_embedding = pd.read_table(entity_embedding_path, header=None)
entity_embedding['vector'] = entity_embedding.iloc[:, 1:101].values.tolist()
entity_embedding = entity_embedding[[0,
                                     'vector']].rename(columns={0: "entity"})
entity_embedding
```

```python
# The relation_embedding.vec file contains the 100-dimensional embeddings
# of the relations learned from the subgraph by TransE method.
# The first column is the ID of relation, and the other columns are the embedding vector values.
relation_embedding_path = os.path.join(temp_dir, 'relation_embedding.vec')
relation_embedding = pd.read_table(relation_embedding_path, header=None)
relation_embedding['vector'] = relation_embedding.iloc[:,
                                                       1:101].values.tolist()
relation_embedding = relation_embedding[[0, 'vector'
                                         ]].rename(columns={0: "relation"})
relation_embedding
```

### <a name="clean-up-temporary-files"></a>Nettoyer les fichiers temporaires

```python
shutil.rmtree(temp_dir)
```

<!-- nbend -->

---

## <a name="examples"></a>Exemples

Consultez les exemples suivants montrant comment utiliser le jeu de données Microsoft News Recommender :

- [MIND News Recommendation Challenge](https://www.microsoft.com/research/?post_type=msr-academic-program&p=676692&secret=hCWU3V)
- [Microsoft Recommenders Repository](https://github.com/microsoft/recommenders)
- [MIND: Microsoft News Dataset](https://msnews.github.io/index.html)


## <a name="next-steps"></a>Étapes suivantes

Découvrez plusieurs modèles de suggestion d’actualités de référence développés sur MIND dans [Microsoft Recommenders Repository](https://github.com/microsoft/recommenders)


Affichez les autres jeux de données dans le [catalogue Open Datasets](dataset-catalog.md).