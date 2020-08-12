---
title: Créer des jeux de données Azure Machine Learning pour accéder aux données
titleSuffix: Azure Machine Learning
description: Découvrez comment créer des jeux de données Azure Machine Learning pour accéder à vos données pour des exécutions d’expérience de machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: 18eecdfeca58bc04c77dd0e39658a51fe56d0e68
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513092"
---
# <a name="create-azure-machine-learning-datasets"></a>Créer des jeux de données Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous découvrez comment créer des jeux de données Azure Machine Learning pour accéder aux données de vos expériences locales ou à distance. Pour comprendre où figurent les jeux de données dans le flux de travail global d’accès aux données d’Azure Machine Learning, consultez l’article [Sécuriser l’accès aux données](concept-data.md#data-workflow).

En créant un jeu de données, vous créez une référence à l’emplacement de la source de données, ainsi qu’une copie de ses métadonnées. Étant donné que les données restent à leur emplacement existant, vous n’exposez aucun coût de stockage supplémentaire et ne risquez pas l’intégrité de vos sources de données. Les jeux de données sont également évalués tardivement, ce qui contribue aux vitesses de performances de flux de travail.

Avec les jeux de données Azure Machine Learning, vous pouvez :

* Conserver une seule copie des données dans votre stockage, référencée par des jeux de données.

* Accéder simplement aux données lors de l’entraînement du modèle sans vous soucier des chaînes de connexion ou des chemins des données.

* Partager des données et collaborer avec d’autres utilisateurs.

[Découvrez-en plus sur l’entraînement avec des jeux de données](how-to-train-with-datasets.md).

## <a name="prerequisites"></a>Prérequis

Pour créer et utiliser des jeux de données, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’en avez pas, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree).

* Un [espace de travail Azure Machine Learning](how-to-manage-workspace.md).

* Le [SDK Azure Machine Learning pour Python installé](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), qui inclut le paquet azureml-datasets.

> [!NOTE]
> Certaines classes de jeu de données ont des dépendances avec le package [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py), qui n’est compatible qu’avec Python 64 bits. Pour les utilisateurs Linux, ces classes sont uniquement prises en charge dans les distributions suivantes :  Red Hat Enterprise Linux (7, 8), Ubuntu (14.04, 16.04, 18.04), Fedora (27, 28), Debian (8, 9) et CentOS (7).

## <a name="compute-size-guidance"></a>Conseils liés à la taille de calcul

Quand vous créez un jeu de données, passez en revue la puissance de traitement de calcul et la taille de vos données en mémoire. La taille de vos données dans le stockage n’est pas la même que la taille des données dans un dataframe. Par exemple, les données des fichiers CSV peuvent décupler de volume dans un dataframe ; ainsi, un fichier CSV de 1 Go peut occuper 10 Go dans un dataframe. 

Si vos données sont compressées, elles peuvent s’étendre davantage ; 20 Go de données relativement éparses stockées au format de compression Parquet peuvent s’étendre jusqu’à environ 800 Go en mémoire. Étant donné que les fichiers Parquet stockent les données dans un format en colonnes, si vous avez uniquement besoin de la moitié des colonnes, il vous suffit de charger environ 400 Go en mémoire.

[Apprenez-en davantage sur l’optimisation du traitement des données dans Azure Machine Learning](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Types de jeux de données

Il existe deux types de jeux de données, selon la façon dont les utilisateurs les consomment lors de l’entraînement : FileDatasets et TabularDatasets. Les deux types peuvent être utilisés dans des flux de travail d’apprentissage Azure Machine Learning impliquant des estimateurs, AutoML, hyperDrive et des pipelines. 

### <a name="filedataset"></a>FileDataset

Un [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) fait référence à des fichiers uniques ou multiples dans vos magasins de données ou vos URL publiques. Si vos données sont déjà nettoyées et prêtes à l’emploi dans des expériences d’apprentissage, vous pouvez [télécharger ou monter](how-to-train-with-datasets.md#mount-vs-download) les fichiers dans votre calcul en tant qu’objet FileDataset. 

Nous vous recommandons d’utiliser FileDatasets pour vos flux de travail d’apprentissage automatique, car les fichiers sources peuvent être dans n’importe quel format, ce qui permet un éventail plus large de scénarios d’apprentissage automatique, dont l’apprentissage profond.

Créer un FileDataset avec le [Kit de développement logiciel (SDK) Python](#create-a-filedataset) ou [Azure Machine Learning Studio](#create-datasets-in-the-studio)

### <a name="tabulardataset"></a>TabularDataset

Un [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) représente les données sous forme de tableau en analysant le fichier ou la liste de fichiers fournis. Cela vous permet de matérialiser les données dans une tramedonnées pandas ou Spark afin de pouvoir travailler avec des bibliothèques de formation et de préparation des données familières sans avoir à quitter votre notebook. Vous pouvez créer un objet `TabularDataset` à partir de fichiers .csv, .tsv, .parquet et .jsonl, et à partir de [résultats de requête SQL](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-).

Avec TabularDatasets, vous pouvez spécifier un horodatage à partir d’une colonne dans les données ou à partir de là où les données du modèle de chemin sont stockées pour activer une caractéristique de série chronologique. Cette spécification permet un filtrage chronologique facile et efficace. Pour un exemple, consultez [Démonstration de l’API pour une série chronologique tabulaire avec des données météo NOAA](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

Créez un TabularDataset avec le [Kit de développement logiciel (SDK) Python](#create-a-tabulardataset) ou [Azure Machine Learning Studio](#create-datasets-in-the-studio).

>[!NOTE]
> Les flux de travail AutoML générés via Azure Machine Learning Studio prennent actuellement en charge uniquement les TabularDatasets. 

## <a name="access-datasets-in-a-virtual-network"></a>Accéder à des jeux de données dans un réseau virtuel

Si votre espace de travail se trouve dans un réseau virtuel, vous devez configurer le jeu de données pour ignorer la validation. Pour plus d’informations sur l’utilisation des magasins de données et des jeux de données dans un réseau virtuel, consultez [Isolement réseau pendant la formation et inférence avec les réseaux virtuels privés](how-to-enable-virtual-network.md#use-datastores-and-datasets).

<a name="datasets-sdk"></a>

## <a name="create-datasets-via-the-sdk"></a>Créer des jeux de données via le Kit de développement logiciel (SDK)

 Pour que les données soient accessibles par Azure Machine Learning, les jeux de données doivent être créés à partir de chemins dans des [magasins de données Azure](how-to-access-data.md) ou des URL web publiques. 

Pour créer des jeux de données à partir d’un [magasin de données Azure](how-to-access-data.md) à l’aide du Kit de développement logiciel (SDK) Python :

1. Vérifiez que vous disposez d’un accès `contributor` ou `owner` au magasin de données Azure inscrit.

2. Créez le jeu de données en référençant des chemins d’accès dans le magasin de données. Vous pouvez créer un jeu de données à partir de plusieurs chemins d’accès dans plusieurs magasins de données. Il n’existe aucune limite inconditionnelle quant au nombre de fichiers ou à la taille de données à partir desquels vous pouvez créer un jeu de données. 

> [!Note]
> Pour chaque chemin d’accès aux données, quelques requêtes sont envoyées au service de stockage pour vérifier s’il pointe vers un fichier ou un dossier. Cette surcharge peut entraîner une dégradation des performances ou une défaillance. Un jeu de données référençant un dossier contenant 1 000 fichiers est considéré comme référençant un chemin d’accès de données. Nous vous recommandons de créer un jeu de données référençant moins de 100 chemins d’accès dans des magasins de données pour obtenir des performances optimales.

### <a name="create-a-filedataset"></a>Créer un FileDataset

Utilisez la méthode [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) sur la classe `FileDatasetFactory` pour charger des fichiers de n’importe quel format et créer un FileDataset non inscrit. 

Si votre stockage se trouve derrière un réseau virtuel ou un pare-feu, définissez le paramètre `validate=False` dans votre méthode `from_files()`. Cela permet de contourner l’étape de validation initiale et vous permet de créer votre jeu de données à partir de ces fichiers sécurisés. Apprenez-en davantage sur l’utilisation de [magasins de données et de jeux de données dans un réseau virtuel](how-to-enable-virtual-network.md#use-datastores-and-datasets).

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

### <a name="create-a-tabulardataset"></a>Créer un TabularDataset

Utilisez la méthode [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) sur la classe `TabularDatasetFactory` pour lire des fichiers au format .csv ou .tsv, puis créez un TabularDataset non inscrit. Si vous lisez à partir de plusieurs fichiers, les résultats sont agrégés dans une même représentation tabulaire. 

Si votre stockage se trouve derrière un réseau virtuel ou un pare-feu, définissez le paramètre `validate=False` dans votre méthode `from_delimited_files()`. Cela permet de contourner l’étape de validation initiale et vous permet de créer votre jeu de données à partir de ces fichiers sécurisés. Apprenez-en davantage sur l’utilisation de [magasins de données et de jeux de données dans un réseau virtuel](how-to-enable-virtual-network.md#use-datastores-and-datasets).

Le code suivant obtient l’espace de travail existant et le magasin de données souhaité par nom. Ensuite, il transmet les emplacements du magasin de fichiers et des fichiers au paramètre `path` pour créer un nouvel objet TabularDataset, `weather_ds`.

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Par défaut, quand vous créez un TabularDataset, les types de données des colonnes sont inférés automatiquement. Si les types inférés ne sont pas ceux que vous attendez, vous pouvez les spécifier en utilisant le code suivant. Le paramètre `infer_column_type` s’applique uniquement aux jeux de données créés à partir de fichiers délimités. [Apprenez-en davantage sur les types de données pris en charge](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py).


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(Index)|PassengerId|Survived|Pclass|Nom|Sex|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|female|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, Miss. Laina|female|26,0|0|0|STON/O2. 3101282|7.9250||S

### <a name="create-a-dataset-from-pandas-dataframe"></a>Créer un jeu de données à partir d’une tramedonnées pandas

Pour créer un TabularDataset à partir d’une tramedonnées pandas en mémoire, écrivez les données dans un fichier local (par exemple, un fichier csv), puis créez votre jeu de données à partir de ce fichier. Le code suivant illustre ce workflow.

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')

# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(datastore.path('data/prepared.csv'))
```

## <a name="register-datasets"></a>Inscrire des jeux de données

Pour terminer le processus de création, inscrivez vos jeux de données dans un espace de travail. Utilisez la méthode [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) pour inscrire des jeux de données auprès de votre espace de travail afin de pouvoir les partager avec d’autres personnes et les réutiliser dans des expériences dans votre espace de travail :

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

<a name="datasets-ui"></a>
## <a name="create-datasets-in-the-studio"></a>Créer des jeux de données dans le studio
L’animation et les étapes suivantes montrent comment créer un jeu de données dans [Azure Machine Learning Studio](https://ml.azure.com).

> [!Note]
> Les jeux de données créés via Azure Machine Learning Studio sont automatiquement inscrits auprès de l’espace de travail.

![Créer un jeu de données avec l’interface utilisateur](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Pour créer un jeu de données dans le studio :
1. Connectez-vous à https://ml.azure.com.
1. Sélectionnez **Jeux de données** dans la section **Ressources** du volet gauche. 
1. Sélectionnez **Créer un jeu de données** pour choisir la source de votre jeu de données. Cette source peut être des fichiers locaux, un magasin de fichiers ou des URL publiques.
1. Sélectionnez le type de jeu de données **Tabulaire** ou **Fichier**.
1. Sélectionnez **Suivant** pour ouvrir le formulaire **Sélection d’un magasin de données et de fichiers**. Sur ce formulaire, vous sélectionnez l’emplacement où conserver votre jeu de données après sa création, ainsi que les fichiers de données à utiliser pour votre jeu de données. 
    1. Vous pouvez choisir d’ignorer la validation si vos données se trouvent dans un réseau virtuel. En savoir plus sur l’[isolement et la confidentialité des réseaux virtuels](how-to-enable-virtual-network.md#machine-learning-studio).
1. Sélectionnez **Suivant** pour renseigner les formulaires **Paramètres et aperçu** et **Schéma** ; ils sont renseignés intelligemment en fonction du type de fichier et vous pouvez configurer davantage votre jeu de données avant de le créer sur ces formulaires. 
1. Sélectionnez **Suivant** pour passer en revue le formulaire **Confirmer les détails**. Vérifiez vos sélections et créez un profil de données facultatif pour votre jeu de données. En savoir plus sur le [profilage des données](how-to-use-automated-ml-for-ml-models.md#profile). 
1. Sélectionnez **Créer** pour terminer la création de votre jeu de données.

## <a name="train-with-datasets"></a>Entraîner avec des jeux de données

Utilisez vos jeux de données dans vos expériences d’apprentissage automatique pour la formation de modèles ML. [En savoir plus sur l’apprentissage avec des jeux de données](how-to-train-with-datasets.md)

## <a name="create-datasets-with-azure-open-datasets"></a>Créer des jeu de données avec Azure Open Datasets

Les jeux de données [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) sont des jeux de données publics organisés que vous pouvez utiliser pour ajouter des fonctionnalités spécifiques à des scénarios à des solutions de Machine Learning afin d'obtenir des modèles plus précis. Les jeux de données englobent des données du domaine public portant sur la météo, le recensement, les jours fériés, la sécurité publique et la localisation et vous aident à former des modèles Machine Learning et à enrichir des solutions prédictives. Open Datasets se trouve dans le cloud dans Microsoft Azure et est inclus dans le SDK et dans l’interface utilisateur de l’espace de travail.

### <a name="use-the-sdk"></a>Utiliser le SDK

Pour créer des jeux de données avec Azure Open Datasets à partir du SDK, vérifiez que vous avez installé le package avec `pip install azureml-opendatasets`. Chaque jeu de données discret est représenté par sa propre classe dans le SDK, et certaines classes sont disponibles en tant que `TabularDataset`, `FileDataset` ou les deux. Pour obtenir la liste complète des classes, consultez la [documentation de référence](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py).

Vous pouvez récupérer certaines classes en tant que `TabularDataset` ou `FileDataset`, ce qui vous permet de manipuler et/ou de télécharger les fichiers directement. D’autres classes peuvent obtenir un jeu de données **seulement** avec l’une des fonctions `get_tabular_dataset()` ou `get_file_dataset()`. L’exemple de code suivant montre des exemples de ces types de classes.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Lorsque vous inscrivez un jeu de données créé à partir d’Open Datasets, aucune donnée n’est immédiatement téléchargée, mais les données seront accessibles ultérieurement à la demande (pour l’apprentissage, par exemple) à partir d’un emplacement de stockage central.

### <a name="use-the-ui"></a>Utiliser l’interface utilisateur

Vous pouvez également créer des jeux de données à partir des classes Open Datasets via l’interface utilisateur. Dans votre espace de travail, sélectionnez l’onglet **Jeux de données** sous **Ressources**. Dans le menu déroulant **Créer un jeu de données**, sélectionnez **À partir d’Open Datasets**.

![Ouvrir un jeu de données avec l’interface utilisateur](./media/how-to-create-register-datasets/open-datasets-1.png)

Choisissez un jeu de données en sélectionnant sa vignette. (Vous avez la possibilité de filtrer en utilisant la barre de recherche.) Sélectionnez **Suivant**.

![Choisir un jeu de données](./media/how-to-create-register-datasets/open-datasets-2.png)

Choisissez ensuite un nom sous lequel inscrire le jeu de données, puis filtrez éventuellement les données en utilisant les filtres disponibles. Dans cet exemple, pour le jeu de données des jours fériés, vous filtrez la période sur une année et le code pays sur États-Unis seulement. Sélectionnez **Create** (Créer).

![Définir les paramètres du jeu de données et créer le jeu de données](./media/how-to-create-register-datasets/open-datasets-3.png)

Le jeu de données est maintenant disponible dans votre espace de travail sous **Jeux de données**. Vous pouvez l’utiliser de la même façon que les autres jeux de données que vous avez créés.

## <a name="version-datasets"></a>Jeux de données avec version

Vous pouvez inscrire un nouveau jeu de données sous le même nom en créant une nouvelle version. La version d’un jeu de données est un moyen de marquer l’état de vos données, afin de pouvoir appliquer une version spécifique du jeu de données pour une expérimentation ou une reproduction ultérieure. En savoir plus sur les [versions des jeux de données](how-to-version-track-datasets.md).
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="next-steps"></a>Étapes suivantes

* Découvrir [comment former un modèle avec des jeux de données](how-to-train-with-datasets.md).
* Utilisez le Machine Learning automatisé pour [vous entraîner avec les TabularDatasets](https://aka.ms/automl-dataset).
* Pour obtenir plus d’exemples d’entraînement de jeux de données, consultez les [exemples de notebooks](https://aka.ms/dataset-tutorial).
