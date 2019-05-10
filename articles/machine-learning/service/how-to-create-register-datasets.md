---
title: Créer et enregistrer des jeux de données dans votre espace de travail
titleSuffix: Azure Machine Learning service
description: Apprenez à créer des jeux de données à partir de différentes sources et enregistrer des jeux de données avec votre espace de travail
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/19
ms.openlocfilehash: 938f13524b22f34f4becc936885d1611cb854df1
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510507"
---
# <a name="create-and-register-azure-machine-learning-datasets-preview"></a>Créer et enregistrer des jeux de données Azure Machine Learning (version préliminaire)

Dans cet article, vous allez les flux de travail Azure Machine Learning pour créer et enregistrer des jeux de données et comment y accéder pour réutilisation sur les expériences locales et distantes.

Azure Machine Learning jeux de données (version préliminaire) facilitent l’accéder et travailler avec vos données. Jeux de données gérer les données dans divers scénarios tels que l’apprentissage du modèle et de la création du pipeline. À l’aide de la [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), vous pouvez travailler avec des données dans des formats courants, accéder au stockage sous-jacent, Explorer et préparer des données, gérer le cycle de vie des définitions de jeu de données différents et comparer entre les jeux de données utilisés dans formation et de production.

## <a name="prerequisites"></a>Conditions préalables

Pour créer et enregistrer des jeux de données, vous devez :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree) dès aujourd’hui.

* Un espace de travail de service Microsoft Azure Machine Learning. Consultez [créer un espace de travail du service Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* Azure Machine Learning SDK pour Python. Pour installer ou mettre à jour vers la dernière version du SDK, consultez [installer ou mettre à jour le SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

## <a name="create-datasets-from-local-files"></a>Créer des jeux de données à partir de fichiers locaux

Charger des fichiers à partir de votre ordinateur local en spécifiant le chemin d’accès du fichier ou dossier avec le [ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) méthode à partir de la `Dataset` classe.  Cette méthode effectue les étapes suivantes sans avoir à vous permettent de spécifier le type de fichier ou de l’analyse des arguments :

* Déduction et en définissant le délimiteur.
* Ignore les enregistrements vides en haut du fichier.
* Déduction et la définition de la ligne d’en-tête.
* Déduction et la conversion des types de données de colonne.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Les fonctions spécifiques aux fichiers permet également de contrôler explicitement l’analyse de votre fichier. Actuellement, prend en charge le SDK Datasets [délimitée par des](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-allfileshavesameheaders--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-norows--0---comment-none--include-path-false--archive-options-none-), [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-), [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-), [binaire](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-), et [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-) formats de fichier.

## <a name="create-datasets-from-azure-datastores"></a>Créer des jeux de données à partir de magasins de données Azure

Pour créer des jeux de données à partir d’un magasin de données Azure, veillez à :

* Vérifiez que contributeur ou propriétaire de l’accès à la banque de données Azure inscrit.

* Importer le [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) et [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#definition) et `Dataset` packages à partir du SDK.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
```

 Le `get()` méthode récupère un magasin de données existant dans l’espace de travail.

```
dstore = Datastore.get(workspace, datastore_name)
```

Utilisez le `from_delimited_files()` méthode lire dans les fichiers délimités, et créer un jeu de données non enregistré.

```Python
# create an in-memory Dataset on your local machine
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

||ID|Case Number|Date|Block|IUCR|Primary Type|Description |Location Description|Arrest|Domestic|...|Ward|Community Area|FBI Code|X Coordinate|Y Coordinate|Year|Updated On|Latitude|Longitude|Lieu|
|--|--|---|---|---|---|----|------|-------|------|-----|---|----|----|-----|-----|------|----|-----|----|----|-----
|0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|PRATIQUE FRAUDULEUSE|USURPATION D’IDENTITÉ FINANCIÈRE SUR 300 DOLLARS|OTHER|FALSE|FALSE|...|9|50|11|1183356|1831503|2016|5/11/2016 15:48|41.69283384|-87.60431945|(41.692833841, -87.60431945)|
1|10516598|HZ258664|4/15/2016 17:00|ENREGISTRER LES MARSHFIELD 082XX S|890|THEFT| À PARTIR DE LA GÉNÉRATION|LIEU DE RÉSIDENCE|FALSE|FALSE|...|21|71|6.|1166776|1850053|2016|5/12/2016 15:48|41.74410697|-87.66449429|(41.744106973, -87.664494285)
2|10519196|HZ261252|4/15/2016 10:00|ENREGISTRER LES SACRAMENTO 104XX S|1154|PRATIQUE FRAUDULEUSE|USURPATION D’IDENTITÉ FINANCIÈRE 300 DOLLARS ET, SOUS|LIEU DE RÉSIDENCE|FALSE|FALSE|...|19|74|11|||2016|5/12/2016 15:50
3|10519591|HZ261534|4/15/2016 9:00|113XX NREGISTRER PRAIRIE|1120|PRATIQUE FRAUDULEUSE|CONTREFAÇON|LIEU DE RÉSIDENCE|FALSE|FALSE|...|9|49|10|||2016|5/13/2016 15:51
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|THEFT|À PARTIR DE LA GÉNÉRATION|SCHOOL, PUBLIC, CRÉATION|FALSE|FALSE|...|40|13|6.|||2016|5/25/2016 15:59|

## <a name="register-your-datasets-with-workspace"></a>Inscrire vos jeux de données avec l’espace de travail

Utilisez le [ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) méthode pour inscrire des jeux de données à votre espace de travail pour le partage et la réutiliser dans votre organisation et entre les différentes expériences.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> La valeur du paramètre par défaut pour `register()` est `exist_ok = False`. Si vous essayez d’inscrire un jeu de données portant le même nom sans modifier ce paramètre, qu'une erreur se produit.

Le `register()` méthode retourne le jeu de données déjà inscrite avec la valeur du paramètre, `exist_ok = True`.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = True
                           )
```

Utilisez `list()` pour voir tous les jeux de données inscrits dans votre espace de travail.

```Python
Dataset.list(workspace_name)
```

Le code précédent génère les éléments suivants :

```Python
[Dataset(Name: dataset_crime,
         Workspace: workspace_name)]
```

## <a name="access-datasets-in-workspace"></a>Jeux de données Access dans l’espace de travail

Jeux de données inscrits est accessibles et consommables localement, à distance et sur des clusters de calcul tels que les ressources de calcul Azure Machine Learning. Pour réutiliser votre jeu de données des expériences et environnements de calcul, utilisez le code suivant pour récupérer votre espace de travail et le jeu de données inscrit par nom.

```Python
workspace = Workspace.from_config()

dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>Étapes suivantes

* [Explorer et de préparer des jeux de données](how-to-explore-prepare-data.md).
* [Gérer le cycle de vie des définitions de jeu de données](how-to-manage-dataset-definitions.md).
* Pour obtenir un exemple d’utilisation de jeux de données, consultez le [exemples de blocs-notes](https://aka.ms/dataset-tutorial).
