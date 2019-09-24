---
title: Créer des jeux de données pour accéder aux données avec azureml-datasets
titleSuffix: Azure Machine Learning
description: Apprenez à créer des jeux de données à partir de différentes sources et à inscrire des jeux de données avec votre espace de travail
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 08/22/2019
ms.openlocfilehash: 6c3a8d62bd6b3650f834540bd7bb13027792b091
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076982"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Créer des jeux de données et y accéder (préversion) dans Azure Machine Learning

Dans cet article, vous allez découvrir comment créer des jeux de données Azure Machine Learning (préversion) et accéder aux données d’expériences locales ou à distance.

Avec les jeux de données Azure Machine Learning, vous pouvez : 

* **Conserver une copie unique des données dans votre stockage** référencée par des jeux de données. 

* **Accéder facilement aux données lors de l’entraînement du modèle** sans vous soucier des chaînes de connexion ou des chemins des données.

* **Partager des données et collaborer** avec d’autres utilisateurs.

## <a name="prerequisites"></a>Prérequis

Pour créer et utiliser des jeux de données, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) aujourd’hui.

* Un [espace de travail Azure Machine Learning](how-to-manage-workspace.md)

* Le [SDK Azure Machine Learning pour Python installé](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), qui inclut le paquet azureml-datasets.

> [!Note]
> Certaines classes Dataset (préversion) comportent des dépendances sur le package [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py). Pour les utilisateurs Linux, ces classes sont uniquement prises en charge dans les distributions suivantes :  Red Hat Enterprise Linux, Ubuntu, Fedora et CentOS.

## <a name="dataset-types"></a>Types de jeux de données

Les jeux de données sont classés en deux types, en fonction de la façon dont les utilisateurs les consomment lors de l’entraînement. 

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) représente les données sous forme de tableau en analysant le fichier ou la liste de fichiers fournis. Cela vous permet de matérialiser les données dans un dataframe Pandas. Vous pouvez créer un objet `TabularDataset` à partir de fichiers CSV, TSV, Parquet, de résultats de requête SQL, et ainsi de suite. Pour obtenir la liste complète, consultez notre [documentation](https://aka.ms/tabulardataset-api-reference).

* [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) fait référence à des fichiers uniques ou multiples dans vos magasins de fichiers ou vos URL publiques. Cela vous offre la possibilité de télécharger ou de monter les fichiers dans votre calcul. Les fichiers peuvent être de n’importe quel format, ce qui permet un éventail plus large de scénarios de Machine Learning, y compris le Deep Learning.

Pour en savoir plus sur les futures modifications de l’API, consultez [ceci](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Créez les jeux de données 

En créant un jeu de données, vous créez une référence à l’emplacement de la source de données, ainsi qu’une copie de ses métadonnées. Les données restant à leur emplacement existant, aucun coût de stockage supplémentaire n’est encouru.

Pour que les données soient accessibles par Azure Machine Learning, les jeux de données doivent être créés à partir de chemins de [magasin de données Azure](how-to-access-data.md) ou d’URL web publiques.

Pour créer des jeux de données à partir d’une [banque de données Azure](how-to-access-data.md)

* Veillez à disposer d’un accès `contributor` ou `owner` à la banque de données Azure inscrite.

* Créez le jeu de données en référençant un chemin dans le magasin de données.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)
```

### <a name="create-tabulardatasets"></a>Créer un TabularDatasets

Vous pouvez créer des TabularDatasets via le kit de développement logiciel (SDK) ou à l’aide de la page d’accueil de l’espace de travail (préversion). Un timestamp peut être spécifié à partir d’une colonne dans les données ou du modèle de chemin d’accès dans lequel les données sont stockées pour activer une caractéristique de série chronologique, qui permet un filtrage facile et efficace par heure. 

#### <a name="using-the-sdk"></a>Utilisation du kit de développement logiciel 

Utilisez la méthode [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---partition-format-none-) sur la classe `TabularDatasetFactory` pour lire des fichiers au format CSV ou TSV, puis créez un TabularDataset non inscrit. Si vous effectuez la lecture à partir de plusieurs fichiers, les résultats sont agrégés dans une représentation tabulaire.

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)

# create a TabularDataset from a delimited file behind a public web url
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Survived|Pclass|Nom|Sex|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Braund, Mr. Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|1|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|female|38.0|1|0|PC 17599|71.2833|C85|C
2|3|1|3|Heikkinen, Miss. Laina|female|26,0|0|0|STON/O2. 3101282|7.9250||S

Utilisez la méthode [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) sur la classe `TabularDataset` pour permettre un filtrage facile et efficace par heure. Vous trouverez d’autres exemples et détails [ici](http://aka.ms/azureml-tsd-notebook). 

```Python
# create a TabularDataset with timeseries trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with timeseries trait specific methods 
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1)) 
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))                  
```

#### <a name="using-the-workspace-landing-page"></a>Avec la page d’accueil de l’espace de travail 

Connectez-vous à la [page d’accueil de l’espace de travail](https://ml.azure.com) pour créer un jeu de données via l’expérience web. Actuellement, la page d’accueil de l’espace de travail prend uniquement en charge la création de TabularDatasets.

L’animation suivante montre comment créer un jeu de données sur la page d’accueil de l’espace de travail. 

Tout d’abord, sélectionnez **Jeu de données** dans la section **Ressources** du volet gauche. Ensuite, sélectionnez **+ Créer un jeu de données** pour choisir la source de votre jeu de données. Il peut s’agir de fichiers locaux, de banques de données ou d’URL web publiques. Les formulaires **Paramètres et aperçu** et **Schéma** sont remplis intelligemment en fonction du type de fichier. Sélectionnez **Suivant** pour les vérifier ou pour configurer davantage votre jeu de données avant création. Sélectionnez **Terminé** pour terminer la création de votre jeu de données. 

![Créer un jeu de données avec l’interface utilisateur](media/how-to-create-register-datasets/create-dataset-ui.gif)

### <a name="create-filedatasets"></a>Créer des FileDatasets

Utilisez la méthode [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) sur la classe `FileDatasetFactory` pour charger des fichiers de tout format, puis créez un FileDataset non inscrit.

```Python
# create a FileDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'animals/dog/1.jpg'),
                  (datastore, 'animals/dog/2.jpg'),
                  (datastore, 'animals/dog/*.jpg')
                 ]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = [
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz'
           ]          
mnist_ds = Dataset.File.from_files(path=web_paths)
```

## <a name="register-datasets"></a>Inscrire des jeux de données

Pour terminer le processus de création, inscrivez vos jeux de données dans l’espace de travail :

Utilisez la méthode [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) pour inscrire des jeux de données dans votre espace de travail afin de pouvoir les partager avec d’autres personnes et les réutiliser dans différentes expériences.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

>[!Note]
> Les jeux de données créés via la page d’accueil de l’espace de travail sont automatiquement inscrits dans l’espace de travail. 

## <a name="version-datasets"></a>Jeux de données avec version

Vous pouvez inscrire un nouveau jeu de données sous le même nom en créant une nouvelle version. La version d’un jeu de données est un moyen de marquer l’état de vos données, afin de pouvoir appliquer une version spécifique du jeu de données pour l’expérimentation ou une reproduction future. Scénarios classiques pouvant impliquer la gestion des versions : 
* Quand de nouvelles données sont disponibles pour un nouvel entraînement.
* Quand vous appliquez différentes approches de préparation des données ou d’ingénierie des caractéristiques.

```Python
# create a TabularDataset from Titanic training data
web_paths = [
            'https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
            'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv'
           ]          
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```


## <a name="access-your-data-during-training"></a>Accédez à vos données pendant la formation

Les jeux de données inscrits sont accessibles localement et à distance sur des clusters de calcul comme Azure Machine Learning. Pour accéder à votre jeu de données inscrit dans plusieurs expériences, utilisez le code suivant afin de récupérer votre espace de travail et jeu de données inscrit par nom. La méthode [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) sur la classe `Dataset` retourne par défaut la dernière version du jeu de données inscrite auprès de l’espace de travail.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="next-steps"></a>Étapes suivantes

* Utilisez le Machine Learning automatisé pour [vous entraîner avec les TabularDatasets](https://aka.ms/automl-dataset).
* Pour obtenir plus d’exemples d’entraînement avec des jeux de données, consultez les [exemples de notebooks](https://aka.ms/dataset-tutorial).
