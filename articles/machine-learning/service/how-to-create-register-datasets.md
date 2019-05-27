---
title: Créer des jeux de données pour accéder aux données avec des groupes de données azureml
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
ms.date: 05/21/2019
ms.openlocfilehash: 949468dfe26b076b5c5cf5cab8bbdc2038c7bd2a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165894"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Créer et accéder aux jeux de données (version préliminaire) dans Azure Machine Learning

Dans cet article, vous allez apprendre à créer des jeux de données Azure Machine Learning (version préliminaire) et comment accéder aux données à partir d’expériences locaux et distants.

Avec les jeux de données managé, vous pouvez : 
* **Accéder facilement aux données pendant l’apprentissage du modèle** sans rétablir la connexion à des bases de données sous-jacentes

* **Garantir la cohérence des données et reproductibilité** à l’aide de la même pointeur entre les expériences : ordinateurs portables, ml automatisé, des pipelines, interface visuelle

* **Partager des données et de collaborer en** avec d’autres utilisateurs

* **Explorer les données** & gérer le cycle de vie des instantanés de données de publication et versions

* **Comparer les données** à une formation pour la production


## <a name="prerequisites"></a>Conditions préalables

Pour créer et utiliser des jeux de données, vous devez :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree) dès aujourd’hui.

* Un [espace de travail de service Azure Machine Learning service espace de travail Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)

* Le [Azure Machine Learning SDK pour Python installé](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), qui inclut le package azureml-jeux de données.

> [!Note]
> Certaines classes de jeu de données (version préliminaire) ont des dépendances sur le [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) package (GA). Pour les utilisateurs de Linux, ces classes sont prises en charge uniquement sur les distributions suivantes :  Red Hat Enterprise Linux, Ubuntu, Fedora et CentOS.

## <a name="data-formats"></a>Formats de données

Vous pouvez créer un jeu de données Azure Machine Learning à partir des données suivantes :
+ [delimited](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-allfileshavesameheaders--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-norows--0---comment-none--include-path-false--archive-options-none-)
+ [binary](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)
+ [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-)
+ [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-)
+ [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-)
+ [Base de données SQL Azure](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [Génération d’Azure Data Lake. 1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)

## <a name="create-datasets"></a>Créez les jeux de données 

Vous pouvez interagir avec vos jeux de données avec le package azureml-jeux de données dans le [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) et spécifiquement [le `Dataset` classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py).

### <a name="create-from-local-files"></a>Créer à partir de fichiers locaux

Charger des fichiers à partir de votre ordinateur local en spécifiant le chemin d’accès du fichier ou dossier avec le [ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) méthode à partir de la `Dataset` classe.  Cette méthode effectue les étapes suivantes sans avoir à vous permettent de spécifier le type de fichier ou de l’analyse des arguments :

* Déduction et en définissant le délimiteur.
* Ignore les enregistrements vides en haut du fichier.
* Déduction et la définition de la ligne d’en-tête.
* Déduction et la conversion des types de données de colonne.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Les fonctions spécifiques aux fichiers permet également de contrôler explicitement l’analyse de votre fichier. 


### <a name="create-from-azure-datastores"></a>Créer à partir de magasins de données Azure

Pour créer des jeux de données à partir d’un magasin de données Azure :

* Vérifiez que vous avez `contributor` ou `owner` accès à la banque de données Azure inscrit.

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

## <a name="register-datasets"></a>Inscrire des jeux de données

Pour terminer le processus de création, enregistrez vos jeux de données avec l’espace de travail :

Utilisez le [ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) méthode pour inscrire des jeux de données à votre espace de travail afin de pouvoir être partagés avec d’autres personnes et réutilisées dans différentes expériences.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Si `exist_ok = False` (valeur par défaut), et vous tentez d’enregistrer un jeu de données portant le même nom qu’une autre, une erreur se produit. La valeur `True` pour remplacer l’existant.

## <a name="access-data-in-datasets"></a>Accéder aux données dans les jeux de données

Jeux de données inscrits est accessibles et consommables localement, à distance et sur des clusters de calcul tels que les ressources de calcul Azure Machine Learning. Pour réutiliser votre jeu de données des expériences et environnements de calcul, utilisez le code suivant pour récupérer votre espace de travail et le jeu de données inscrit par nom.

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
Dataset.list(workspace)

# Get dataset by name
dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>Étapes suivantes

* [Explorer et de préparer des jeux de données](how-to-explore-prepare-data.md).
* [Gérer le cycle de vie des définitions de jeu de données](how-to-manage-dataset-definitions.md).
* Pour obtenir un exemple d’utilisation de jeux de données, consultez le [exemples de blocs-notes](https://aka.ms/dataset-tutorial).
