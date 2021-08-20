---
title: COVID-19 Open Research Dataset
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données COVID-19 Open Research Dataset dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 1225cc9f88fb07766f8f6e89e5a2502bdb83653a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038700"
---
# <a name="covid-19-open-research-dataset"></a>COVID-19 Open Research Dataset

Jeu de données de texte intégral et de métadonnées d’articles de recherche liés au COVID-19 et aux coronavirus, optimisé pour la lisibilité par une machine et mis à disposition par la communauté mondiale de chercheurs.

Pour répondre à la pandémie du COVID-19, l’institut [Allen Institute for AI](https://allenai.org/) s’est associé à des groupes de recherche dans le but de préparer et de distribuer le jeu de données COVID-19 Open Research Dataset (CORD-19). Ce jeu de données est constitué de plus de 47 000 articles universitaires (dont 36 000 peuvent être lus dans leur intégralité) ayant pour sujet le COVID-19 et la famille des coronavirus. Il s’agit d’une ressource gratuite destinée aux chercheurs du monde entier.

Ce jeu de données mobilise les chercheurs pour qu’ils appliquent les avancées récentes du traitement du langage naturel afin de générer de nouvelles connaissances dans la lutte contre cette maladie infectieuse.

Le corpus fera l’objet de mises à jour lorsque de nouvelles recherches seront publiées dans des documents évalués par des pairs et des services d’archivage tels que [bioRxiv](https://www.biorxiv.org/), [medRxiv](https://www.medrxiv.org/) et d’autres.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="license-terms"></a>Termes du contrat de licence

Ce jeu de données est mis à disposition par l’institut Allen Institute of AI par le biais du projet [Semantic Scholar](https://pages.semanticscholar.org/coronavirus-research). En parcourant, en téléchargeant ou en utilisant de quelque manière que ce soit le contenu fourni dans le jeu de données CORD-19, vous acceptez la [licence associée au jeu de données](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/COVID.DATA.LIC.AGMT.pdf) et à son utilisation. Des informations de licence propres à chaque article du jeu de données sont disponibles dans le fichier de métadonnées. Pour plus d’informations sur les licences, consultez les sites web [PMC](https://www.ncbi.nlm.nih.gov/pmc/tools/openftlist/), [medRxiv](https://www.medrxiv.org/submit-a-manuscript) et [bioRxiv](https://www.biorxiv.org/about-biorxiv).

## <a name="volume-and-retention"></a>Volume et conservation

Ce jeu de données est stocké au format JSON et la dernière version contient plus de 36 000 articles en texte intégral. Chaque article est représenté comme un seul objet JSON. [Afficher le schéma](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/json_schema.txt).

## <a name="storage-location"></a>Emplacement de stockage
Ce jeu de données est stocké dans la région Azure USA Est. Il est recommandé de placer les ressources de calcul dans la région USA Est pour des raisons d’affinité.

## <a name="citation"></a>Citation

Lorsque vous incluez des données CORD-19 dans une publication ou une redistribution, vous devez citer le jeu de données de la façon suivante :

Dans la bibliographie :

COVID-19 Open Research Dataset (CORD-19). 2020. Version YYYY-MM-DD. Récupéré à partir de [COVID-19 Open Research Dataset (CORD-19)](https://pages.semanticscholar.org/coronavirus-research). Accès AAA-MM-JJ. doi:10.5281/zenodo.3715505

Dans le texte : (CORD-19, 2020)

## <a name="contact"></a>Contact

Pour toute question sur ce jeu de données, contactez partnerships@allenai.org.

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=covid-19-open-research -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=covid-19-open-research)** .

## <a name="the-cord-19-dataset"></a>Jeu de données CORD-19

CORD-19 est un ensemble de plus de 50 000 articles universitaires (dont plus de 40 000 en texte intégral) portant sur le COVID-19, le SARS-CoV-2 et autres coronavirus. Ce jeu de données est disponible gratuitement dans le but d’aider les communautés chercheurs à combattre la pandémie du COVID-19.

L’objectif de ce notebook est double :

1. Montrer comment accéder au jeu de données CORD-19 dans Azure - Nous nous connectons au compte de stockage Blob Azure qui héberge le jeu de données CORD-19.
2. Parcourir la structure du jeu de données - Les articles du jeu de données sont stockés sous forme de fichiers JSON. Nous fournissons des exemples qui montrent comment :

  - Trouver les articles (en parcourant le conteneur)
  - Lire les articles (en parcourant le schéma JSON)

Dépendances - Ce notebook nécessite les bibliothèques suivantes :

- Stockage Azure (par exemple, `pip install azure-storage`)
- NLTK ([docs](https://www.nltk.org/install.html))
- Pandas (par exemple, `pip install pandas`)

### <a name="getting-the-cord-19-data-from-azure"></a>Obtention des données CORD-19 à partir d’Azure

Les données CORD-19 ont été chargées en tant que jeu de données ouvert Azure [disponible ici](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). Nous créons un service blob lié au jeu de données ouvert CORD-19.


```python
from azure.storage.blob import BlockBlobService

# storage account details
azure_storage_account_name = "azureopendatastorage"
azure_storage_sas_token = "sv=2019-02-02&ss=bfqt&srt=sco&sp=rlcup&se=2025-04-14T00:21:16Z&st=2020-04-13T16:21:16Z&spr=https&sig=JgwLYbdGruHxRYTpr5dxfJqobKbhGap8WUtKFadcivQ%3D"

# create a blob service
blob_service = BlockBlobService(
    account_name=azure_storage_account_name,
    sas_token=azure_storage_sas_token,
)
```

Nous pouvons utiliser ce service blob comme handle pour ces données. Nous pouvons accéder au jeu de données à l’aide des API `BlockBlobService`. Pour plus d’informations, consultez ce qui suit :

* [Concepts de service Blob](/rest/api/storageservices/blob-service-concepts)
* [Opérations sur les conteneurs](/rest/api/storageservices/operations-on-containers)

Les données CORD-19 sont stockées dans le conteneur `covid19temp`. Voici la structure de fichiers située dans le conteneur, avec un exemple de fichier.

```
metadata.csv
custom_license/
    pdf_json/
        0001418189999fea7f7cbe3e82703d71c85a6fe5.json        # filename is sha-hash
        ...
    pmc_json/
        PMC1065028.xml.json                                  # filename is the PMC ID
        ...
noncomm_use_subset/
    pdf_json/
        0036b28fddf7e93da0970303672934ea2f9944e7.json
        ...
    pmc_json/
        PMC1616946.xml.json
        ...
comm_use_subset/
    pdf_json/
        000b7d1517ceebb34e1e3e817695b6de03e2fa78.json
        ...
    pmc_json/
        PMC1054884.xml.json
        ...
biorxiv_medrxiv/                                             # note: there is no pmc_json subdir
    pdf_json/
        0015023cc06b5362d332b3baf348d11567ca2fbb.json
        ...
```

Chaque fichier .json correspond à un article du jeu de données. C’est ici que sont stockés le titre, les auteurs, le résumé et le texte intégral (le cas échéant).

### <a name="using-metadatacsv"></a>Utilisation de metadata.csv
Le jeu de données CORD-19 est fourni avec `metadata.csv`. Ce fichier contient les informations de base sur tous les articles disponibles dans le jeu de données CORD-19. C’est un bon point de départ pour l’exploration !


```python
# container housing CORD-19 data
container_name = "covid19temp"

# download metadata.csv
metadata_filename = 'metadata.csv'
blob_service.get_blob_to_path(
    container_name=container_name,
    blob_name=metadata_filename,
    file_path=metadata_filename
)
```

```python
import pandas as pd

# read metadata.csv into a dataframe
metadata_filename = 'metadata.csv'
metadata = pd.read_csv(metadata_filename)
```

```python
metadata.head(3)
```

Il y a beaucoup de choses à intégrer à première vue, il faut donc être le plus clair possible.

```python
simple_schema = ['cord_uid', 'source_x', 'title', 'abstract', 'authors', 'full_text_file', 'url']

def make_clickable(address):
    '''Make the url clickable'''
    return '<a href="{0}">{0}</a>'.format(address)

def preview(text):
    '''Show only a preview of the text data.'''
    return text[:30] + '...'

format_ = {'title': preview, 'abstract': preview, 'authors': preview, 'url': make_clickable}

metadata[simple_schema].head().style.format(format_)
```

```python
# let's take a quick look around
num_entries = len(metadata)
print("There are {} many entries in this dataset:".format(num_entries))

metadata_with_text = metadata[metadata['full_text_file'].isna() == False]
with_full_text = len(metadata_with_text)
print("-- {} have full text entries".format(with_full_text))

with_doi = metadata['doi'].count()
print("-- {} have DOIs".format(with_doi))

with_pmcid = metadata['pmcid'].count()
print("-- {} have PubMed Central (PMC) ids".format(with_pmcid))

with_microsoft_id = metadata['Microsoft Academic Paper ID'].count()
print("-- {} have Microsoft Academic paper ids".format(with_microsoft_id))
```

```
There are 51078 many entries in this dataset:
-- 42511 have full text entries
-- 47741 have DOIs
-- 41082 have PubMed Central (PMC) ids
-- 964 have Microsoft Academic paper ids
```

## <a name="example-read-full-text"></a>Exemple : Lire le texte intégral

`metadata.csv` ne contient pas le texte intégral. Voyons un exemple pour comprendre comment le lire. Accédez au code JSON qui comprend le texte intégral, décompressez-le puis convertissez-le en une liste de phrases.

```python
# choose a random example with pdf parse available
metadata_with_pdf_parse = metadata[metadata['has_pdf_parse']]
example_entry = metadata_with_pdf_parse.iloc[42]

# construct path to blob containing full text
blob_name = '{0}/pdf_json/{1}.json'.format(example_entry['full_text_file'], example_entry['sha'])  # note the repetition in the path
print("Full text blob for this entry:")
print(blob_name)
```

Nous pouvons maintenant lire le contenu JSON associé à cet objet Blob en procédant de la façon suivante :

```python
import json
blob_as_json_string = blob_service.get_blob_to_text(container_name=container_name, blob_name=blob_name)
data = json.loads(blob_as_json_string.content)

# in addition to the body text, the metadata is also stored within the individual json files
print("Keys within data:", ', '.join(data.keys()))
```

Dans cet exemple, nous nous intéresserons à `body_text`, qui stocke les données texte de la façon suivante :

```json
"body_text": [                      # list of paragraphs in full body
    {
        "text": <str>,
        "cite_spans": [             # list of character indices of inline citations
                                    # e.g. citation "[7]" occurs at positions 151-154 in "text"
                                    #      linked to bibliography entry BIBREF3
            {
                "start": 151,
                "end": 154,
                "text": "[7]",
                "ref_id": "BIBREF3"
            },
            ...
        ],
        "ref_spans": <list of dicts similar to cite_spans>,     # e.g. inline reference to "Table 1"
        "section": "Abstract"
    },
    ...
]
```

Le schéma json complet est [disponible ici](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/json_schema.txt).


```python
from nltk.tokenize import sent_tokenize

# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```    

### <a name="pdf-vs-pmc-xml-parse"></a>Analyse XML PDF et PMC

Dans l’exemple ci-dessus, nous avons vu un scénario qui utilisait `has_pdf_parse == True`. Le chemin du fichier Blob se présentait ainsi :

```
'<full_text_file>/pdf_json/<sha>.json'
```

Pour les scénarios utilisant `has_pmc_xml_parse == True`, vous pouvez également utiliser le format suivant :

```
'<full_text_file>/pmc_json/<pmcid>.xml.json'
```

Par exemple :


```python
# choose a random example with pmc parse available
metadata_with_pmc_parse = metadata[metadata['has_pmc_xml_parse']]
example_entry = metadata_with_pmc_parse.iloc[42]

# construct path to blob containing full text
blob_name = '{0}/pmc_json/{1}.xml.json'.format(example_entry['full_text_file'], example_entry['pmcid'])  # note the repetition in the path
print("Full text blob for this entry:")
print(blob_name)

blob_as_json_string = blob_service.get_blob_to_text(container_name=container_name, blob_name=blob_name)
data = json.loads(blob_as_json_string.content)

# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```
```
Full text blob for this entry:
custom_license/pmc_json/PMC546170.xml.json
An example sentence: Double-stranded small interfering RNA (siRNA) molecules have drawn much attention since it was unambiguously shown that they mediate potent gene knock-down in a variety of mammalian cells (1).
```    

## <a name="iterate-through-blobs-directly"></a>Effectuer une itération directement dans les objets blob

Dans les exemples ci-dessus, nous avons utilisé le fichier `metadata.csv` pour parcourir les données, construire le chemin du fichier d’objet blob et lire les données de l’objet blob. Une autre possibilité consiste à effectuer une itération dans les objets blob.


```python
# get and sort list of available blobs
blobs = blob_service.list_blobs(container_name)
sorted_blobs = sorted(list(blobs), key=lambda e: e.name, reverse=True)
```

À présent, nous pouvons effectuer une itération directement dans les objets blob. Par exemple, nous allons compter le nombre de fichiers JSON disponibles.


```python
# we can now iterate directly though the blobs
count = 0
for blob in sorted_blobs:
    if blob.name[-5:] == ".json":
        count += 1
print("There are {} many json files".format(count))
```

```
There are 59784 many json files
```  

## <a name="appendix"></a>Annexe

### <a name="data-quality-issues"></a>Problèmes de qualité des données

Il s’agit d’un jeu de données volumineux qui, pour des raisons évidentes, a été créé dans la précipitation ! Voici quelques problèmes de qualité des données que nous avons détectés.

#### <a name="multiple-shas"></a>SHA multiples

Nous observons que dans certains cas, il existe plusieurs SHA pour une même entrée.

```python
metadata_multiple_shas = metadata[metadata['sha'].str.len() > 40]

print("There are {} many entries with multiple shas".format(len(metadata_multiple_shas)))

metadata_multiple_shas.head(3)
```
```
There are 1999 many entries with multiple shas
```

## <a name="layout-of-the-container"></a>Disposition du conteneur

Ici, nous utilisons une expression régulière simple pour explorer la structure des fichiers du conteneur au cas où celle-ci serait mise à jour.

```python
container_name = "covid19temp"
blobs = blob_service.list_blobs(container_name)
sorted_blobs = sorted(list(blobs), key=lambda e: e.name, reverse=True)
```

```python
import re
dirs = {}

pattern = '([\w]+)\/([\w]+)\/([\w.]+).json'
for blob in sorted_blobs:
    
    m = re.match(pattern, blob.name)
    
    if m:
        dir_ = m[1] + '/' + m[2]
        
        if dir_ in dirs:
            dirs[dir_] += 1
        else:
            dirs[dir_] = 1
        
dirs
```

<!-- nbend -->

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=covid-19-open-research -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=covid-19-open-research)** .

## <a name="the-cord-19-dataset"></a>Jeu de données CORD-19

CORD-19 est un ensemble de plus de 50 000 articles universitaires (dont plus de 40 000 en texte intégral) portant sur le COVID-19, le SARS-CoV-2 et autres coronavirus. Ce jeu de données est disponible gratuitement dans le but d’aider les communautés chercheurs à combattre la pandémie du COVID-19.

L’objectif de ce notebook est double :

1. Montrer comment accéder au jeu de données CORD-19 dans Azure : nous utilisons le jeu de données AzureML pour fournir un contexte aux données CORD-19.
2. Parcourir la structure du jeu de données - Les articles du jeu de données sont stockés sous forme de fichiers JSON. Nous fournissons des exemples qui montrent comment :

  - Trouver les articles (en parcourant la structure de répertoires)
  - Lire les articles (en parcourant le schéma JSON)

Dépendances - Ce notebook nécessite les bibliothèques suivantes :

- SDK Python AzureML (par exemple, `pip install --upgrade azureml-sdk`)
- Pandas (par exemple, `pip install pandas`)
- NLTK ([docs](https://www.nltk.org/install.html)) (par exemple, `pip install nltk`)

Si votre NLTK ne comprend pas le package `punkt`, vous devrez exécuter ce qui suit :

```
import nltk
nltk.download('punkt')
```

### <a name="getting-the-cord-19-data-from-azure"></a>Obtention des données CORD-19 à partir d’Azure

Les données CORD-19 ont été chargées en tant que jeu de données ouvert Azure [disponible ici](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). Dans ce notebook, nous utilisons un [jeu de données](/python/api/azureml-core/azureml.core.dataset.dataset) AzureML pour référencer le jeu de données ouvert CORD-19.

```python
import azureml.core
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

```python
from azureml.core import  Dataset
cord19_dataset = Dataset.File.from_files('https://azureopendatastorage.blob.core.windows.net/covid19temp')
mount = cord19_dataset.mount()
```

La méthode `mount()` crée un gestionnaire de contexte qui permet de monter des flux de système de fichiers définis par le jeu de données comme des fichiers locaux.

Utilisez `mount.start()` et `mount.stop()`, ou utilisez `with mount():` pour gérer le contexte.

Le montage est pris en charge uniquement sur les systèmes d’exploitation UNIX ou similaires. En outre, libfusible doit être présent. Si les exécutions ont lieu à l’intérieur d’un conteneur Docker, celui-ci doit être démarré à l’aide de l’indicateur `--privileged` ou à l’aide de `--cap-add SYS_ADMIN --device /dev/fuse`. Pour plus d’informations, consultez la [documentation](/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py#mount-mount-point-none----kwargs-&preserve-view=true).


```python
import os

COVID_DIR = '/covid19temp'
path = mount.mount_point + COVID_DIR

with mount:
    print(os.listdir(path))
```
```
['antiviral_with_properties_compressed.sdf', 'biorxiv_medrxiv', 'biorxiv_medrxiv_compressed.tar.gz', 'comm_use_subset', 'comm_use_subset_compressed.tar.gz', 'custom_license', 'custom_license_compressed.tar.gz', 'metadata.csv', 'noncomm_use_subset', 'noncomm_use_subset_compressed.tar.gz']
```

Voici la structure de fichiers située dans le jeu de données CORD-19, avec un exemple de fichier.

```
metadata.csv
custom_license/
    pdf_json/
        0001418189999fea7f7cbe3e82703d71c85a6fe5.json        # filename is sha-hash
        ...
    pmc_json/
        PMC1065028.xml.json                                  # filename is the PMC ID
        ...
noncomm_use_subset/
    pdf_json/
        0036b28fddf7e93da0970303672934ea2f9944e7.json
        ...
    pmc_json/
        PMC1616946.xml.json
        ...
comm_use_subset/
    pdf_json/
        000b7d1517ceebb34e1e3e817695b6de03e2fa78.json
        ...
    pmc_json/
        PMC1054884.xml.json
        ...
biorxiv_medrxiv/                                             # note: there is no pmc_json subdir
    pdf_json/
        0015023cc06b5362d332b3baf348d11567ca2fbb.json
        ...
```

Chaque fichier .json correspond à un article du jeu de données. C’est ici que sont stockés le titre, les auteurs, le résumé et le texte intégral (le cas échéant).

## <a name="using-metadatacsv"></a>Utilisation de metadata.csv

Le jeu de données CORD-19 est fourni avec `metadata.csv`. Ce fichier contient les informations de base sur tous les articles disponibles dans le jeu de données CORD-19. C’est un bon point de départ pour l’exploration !


```python
import pandas as pd

# create mount context
mount.start()

# specify path to metadata.csv
COVID_DIR = 'covid19temp'
metadata_filename = '{}/{}/{}'.format(mount.mount_point, COVID_DIR, 'metadata.csv')

# read metadata
metadata = pd.read_csv(metadata_filename)
metadata.head(3)
```

```python
simple_schema = ['cord_uid', 'source_x', 'title', 'abstract', 'authors', 'full_text_file', 'url']

def make_clickable(address):
    '''Make the url clickable'''
    return '<a href="{0}">{0}</a>'.format(address)

def preview(text):
    '''Show only a preview of the text data.'''
    return text[:30] + '...'

format_ = {'title': preview, 'abstract': preview, 'authors': preview, 'url': make_clickable}

metadata[simple_schema].head().style.format(format_)
```

```python
# let's take a quick look around
num_entries = len(metadata)
print("There are {} many entries in this dataset:".format(num_entries))

metadata_with_text = metadata[metadata['full_text_file'].isna() == False]
with_full_text = len(metadata_with_text)
print("-- {} have full text entries".format(with_full_text))

with_doi = metadata['doi'].count()
print("-- {} have DOIs".format(with_doi))

with_pmcid = metadata['pmcid'].count()
print("-- {} have PubMed Central (PMC) ids".format(with_pmcid))

with_microsoft_id = metadata['Microsoft Academic Paper ID'].count()
print("-- {} have Microsoft Academic paper ids".format(with_microsoft_id))
```    

### <a name="example-read-full-text"></a>Exemple : Lire le texte intégral

`metadata.csv` ne contient pas le texte intégral. Voyons un exemple pour comprendre comment le lire. Accédez au code JSON qui comprend le texte intégral, décompressez-le puis convertissez-le en une liste de phrases.


```python
# choose a random example with pdf parse available
metadata_with_pdf_parse = metadata[metadata['has_pdf_parse']]
example_entry = metadata_with_pdf_parse.iloc[42]

# construct path to blob containing full text
filepath = '{0}/{1}/pdf_json/{2}.json'.format(path, example_entry['full_text_file'], example_entry['sha'])
print("Full text filepath:")
print(filepath)
```    

Nous pouvons maintenant lire le contenu JSON associé à ce fichier en procédant de la façon suivante :

```python
import json

try:
    with open(filepath, 'r') as f:
        data = json.load(f)
except FileNotFoundError as e:
    # in case the mount context has been closed
    mount.start()
    with open(filepath, 'r') as f:
        data = json.load(f)
        
# in addition to the body text, the metadata is also stored within the individual json files
print("Keys within data:", ', '.join(data.keys()))
```

```
Keys within data: paper_id, metadata, abstract, body_text, bib_entries, ref_entries, back_matter
```

Dans cet exemple, nous nous intéresserons à `body_text`, qui stocke les données texte de la façon suivante :

```json
"body_text": [                      # list of paragraphs in full body
    {
        "text": <str>,
        "cite_spans": [             # list of character indices of inline citations
                                    # e.g. citation "[7]" occurs at positions 151-154 in "text"
                                    #      linked to bibliography entry BIBREF3
            {
                "start": 151,
                "end": 154,
                "text": "[7]",
                "ref_id": "BIBREF3"
            },
            ...
        ],
        "ref_spans": <list of dicts similar to cite_spans>,     # e.g. inline reference to "Table 1"
        "section": "Abstract"
    },
    ...
]
```

Afficher le [schéma JSON complet](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/json_schema.txt).


```python
from nltk.tokenize import sent_tokenize
# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```
    

#### <a name="pdf-vs-pmc-xml-parse"></a>Analyse XML PDF et PMC

Dans l’exemple ci-dessus, nous avons vu un scénario qui utilisait `has_pdf_parse == True`. Dans ce scénario, le chemin du fichier se présentait ainsi :

```
'<full_text_file>/pdf_json/<sha>.json'
```

Pour les scénarios utilisant `has_pmc_xml_parse == True`, vous pouvez également utiliser le format suivant :

```
'<full_text_file>/pmc_json/<pmcid>.xml.json'
```

Par exemple :


```python
# choose a random example with pmc parse available
metadata_with_pmc_parse = metadata[metadata['has_pmc_xml_parse']]
example_entry = metadata_with_pmc_parse.iloc[42]

# construct path to blob containing full text
filename = '{0}/pmc_json/{1}.xml.json'.format(example_entry['full_text_file'], example_entry['pmcid'])  # note the repetition in the path
print("Path to file: {}\n".format(filename))

with open(mount.mount_point + '/' + COVID_DIR + '/' + filename, 'r') as f:
    data = json.load(f)

# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```

## <a name="appendix"></a>Annexe

### <a name="data-quality-issues"></a>Problèmes de qualité des données

Il s’agit d’un jeu de données volumineux qui, pour des raisons évidentes, a été créé dans la précipitation ! Voici quelques problèmes de qualité des données que nous avons détectés.


```python
metadata_multiple_shas = metadata[metadata['sha'].str.len() > 40]

print("There are {} many entries with multiple shas".format(len(metadata_multiple_shas)))

metadata_multiple_shas.head(3)
```

<!-- nbend -->

---

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres jeux de données du [catalogue Open Datasets](dataset-catalog.md).