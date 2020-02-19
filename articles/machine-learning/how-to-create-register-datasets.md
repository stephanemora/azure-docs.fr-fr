---
title: Créer des jeux de données Azure Machine Learning pour accéder aux données
titleSuffix: Azure Machine Learning
description: Découvrez comment créer des jeux de données Azure Machine Learning pour accéder à vos données pour des exécutions d’expérience de machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 02/10/2020
ms.openlocfilehash: 0bfaef72be23f148c01e02e910b11128cec1659e
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116717"
---
# <a name="create-azure-machine-learning-datasets"></a>Créer des jeux de données Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous découvrez comment créer des jeux de données Azure Machine Learning pour accéder aux données de vos expériences locales ou à distance.

Avec les jeux de données Azure Machine Learning, vous pouvez :

* Conserver une seule copie des données dans votre stockage, référencée par des jeux de données.

* Accéder simplement aux données lors de l’entraînement du modèle sans vous soucier des chaînes de connexion ou des chemins des données.

* Partager des données et collaborer avec d’autres utilisateurs.

## <a name="prerequisites"></a>Conditions préalables requises

Pour créer et utiliser des jeux de données, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’en avez pas, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree).

* Un [espace de travail Azure Machine Learning](how-to-manage-workspace.md).

* Le [SDK Azure Machine Learning pour Python installé](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), qui inclut le paquet azureml-datasets.

> [!NOTE]
> Certaines classes de jeu de données ont des dépendances vis-à-vis du package [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py). Pour les utilisateurs Linux, ces classes sont uniquement prises en charge dans les distributions suivantes :  Red Hat Enterprise Linux, Ubuntu, Fedora et CentOS.

## <a name="dataset-types"></a>Types de jeux de données

Il existe deux types de jeux de données, selon la façon dont les utilisateurs les consomment lors de l’entraînement :

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) représente les données sous forme de tableau en analysant le fichier ou la liste de fichiers fournis. Cela vous permet de matérialiser les données dans un dataframe Pandas ou Spark. Vous pouvez créer un objet `TabularDataset` à partir de fichiers .csv, .tsv, .parquet et .jsonl, et à partir de résultats de requête SQL. Pour obtenir une liste complète, consultez [TabularDatasetFactory, classe](https://aka.ms/tabulardataset-api-reference)

* La classe [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) référence des fichiers uniques ou multiples dans vos magasins de fichiers ou vos URL publiques. Par cette méthode, vous pouvez télécharger ou monter les fichiers sur votre capacité de calcul en tant qu’objet FileDataset. Les fichiers peuvent être de n’importe quel format, ce qui autorise un éventail plus large de scénarios de machine learning, notamment le deep learning.

Pour plus d’informations sur les futures modifications de l’API, consultez [Notification des changements de l’API de jeu de données](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Créez les jeux de données

En créant un jeu de données, vous créez une référence à l’emplacement de la source de données, ainsi qu’une copie de ses métadonnées. Les données restant à leur emplacement existant, vous n’êtes soumis à aucun coût de stockage supplémentaire. Vous pouvez créer des jeux de données `TabularDataset` et `FileDataset` en utilisant le SDK Python ou la page d’accueil de l’espace de travail (préversion).

Pour que les données soient accessibles par Azure Machine Learning, les jeux de données doivent être créés à partir de chemins dans des [magasins de données Azure](how-to-access-data.md) ou des URL web publiques.

### <a name="use-the-sdk"></a>Utiliser le SDK

Pour créer des jeux de données à partir d’un [magasin de données Azure](how-to-access-data.md) en utilisant le SDK Python :

1. Vérifiez que vous disposez d’un accès `contributor` ou `owner` au magasin de données Azure inscrit.

2. Créez le jeu de données en référençant des chemins d’accès dans le magasin de données.
> [!Note]
> Vous pouvez créer un jeu de données à partir de plusieurs chemins d’accès dans plusieurs magasins de données. Il n’existe aucune limite inconditionnelle quant au nombre de fichiers ou à la taille de données à partir desquels vous pouvez créer un jeu de données. Toutefois, pour chaque chemin d’accès aux données, quelques requêtes sont envoyées au service de stockage pour vérifier s’il pointe vers un fichier ou un dossier. Cette surcharge peut entraîner une dégradation des performances ou une défaillance. Un jeu de données référençant un dossier contenant 1 000 fichiers est considéré comme référençant un chemin d’accès de données. Nous vous recommandons de créer un jeu de données référençant moins de 100 chemins d’accès dans des magasins de données pour obtenir des performances optimales.


#### <a name="create-a-tabulardataset"></a>Créer un TabularDataset

Vous pouvez créer des TabularDatasets via le SDK ou en utilisant Azure Machine Learning Studio. 

Utilisez la méthode [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none-) sur la classe `TabularDatasetFactory` pour lire des fichiers au format .csv ou .tsv, puis créez un TabularDataset non inscrit. Si vous lisez à partir de plusieurs fichiers, les résultats sont agrégés dans une même représentation tabulaire.

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 paths in datastore
datastore_paths = [(datastore, 'ather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Par défaut, quand vous créez un TabularDataset, les types de données des colonnes sont inférés automatiquement. Si les types inférés ne sont pas ceux que vous attendez, vous pouvez les spécifier en utilisant le code suivant. Vous pouvez aussi découvrir plus d’informations sur les [types de données pris en charge](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py).

```Python
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Survived|Pclass|Name|Sex|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|female|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, Miss. Laina|female|26,0|0|0|STON/O2. 3101282|7.9250||S

Utilisez la méthode [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-) sur la classe `TabularDatasetFactory` pour lire à partir d’Azure SQL Database :

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

Dans les TabularDatasets, vous pouvez spécifier un horodatage à partir d’une colonne dans les données ou à partir de là où les données du modèle de chemin sont stockées pour activer une caractéristique de série chronologique. Cette spécification permet un filtrage chronologique facile et efficace.

Utilisez la méthode [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) sur la classe `TabularDataset` pour spécifier votre colonne d’horodatage et activer le filtrage chronologique. Pour plus d’informations, consultez [Démonstration de l’API pour une série chronologique tabulaire avec des données météo NOAA](https://aka.ms/azureml-tsd-notebook).

```Python
# create a TabularDataset with time series trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with time-series-trait-specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```

#### <a name="create-a-filedataset"></a>Créer un FileDataset

Utilisez la méthode [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) sur la classe `FileDatasetFactory` pour charger des fichiers de n’importe quel format, puis créez un FileDataset non inscrit :

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>Sur le web 
L’animation et les étapes suivantes montrent comment créer un jeu de données dans Azure Machine Learning Studio, https://ml.azure.com.

![Créer un jeu de données avec l’interface utilisateur](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Pour créer un jeu de données dans le studio :
1. Connectez-vous à https://ml.azure.com.
1. Sélectionnez **Jeux de données** dans la section **Ressources** du volet gauche. 
1. Sélectionnez **Créer un jeu de données** pour choisir la source de votre jeu de données. Cette source peut être des fichiers locaux, un magasin de fichiers ou des URL publiques.
1. Sélectionnez le type de jeu de données **Tabulaire** ou **Fichier**.
1. Sélectionnez **Suivant** pour ouvrir le formulaire **Sélection d’un magasin de données et de fichiers**. Sur ce formulaire, vous sélectionnez l’emplacement où conserver votre jeu de données après sa création, ainsi que les fichiers de données à utiliser pour votre jeu de données. 
1. Sélectionnez **Suivant** pour renseigner les formulaires **Paramètres et aperçu** et **Schéma** ; ils sont renseignés intelligemment en fonction du type de fichier et vous pouvez configurer davantage votre jeu de données avant de le créer sur ces formulaires. 
1. Sélectionnez **Suivant** pour passer en revue le formulaire **Confirmer les détails**. Vérifiez vos sélections et créez un profil de données facultatif pour votre jeu de données. En savoir plus sur le [profilage des données](how-to-create-portal-experiments.md#profile). 
1. Sélectionnez **Créer** pour terminer la création de votre jeu de données.

## <a name="register-datasets"></a>Inscrire des jeux de données

Pour terminer le processus de création, inscrivez vos jeux de données dans un espace de travail. Utilisez la méthode [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) pour inscrire des jeux de données auprès de votre espace de travail afin de pouvoir les partager avec d’autres personnes et les réutiliser dans différentes expériences :

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Les jeux de données créés via Azure Machine Learning Studio sont automatiquement inscrits auprès de l’espace de travail.

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

# Diabetes class can return ONLY return TabularDataset and must be called from the static function
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

## <a name="access-datasets-in-your-script"></a>Accéder aux jeux de données dans votre script

Les jeux de données inscrits sont accessibles localement et à distance sur des clusters de calcul comme la capacité de calcul Azure Machine Learning. Pour accéder à votre jeu de données inscrit dans plusieurs expériences, utilisez le code suivant afin d’accéder à votre espace de travail et à votre jeu de données inscrit en utilisant son nom. Par défaut, la méthode [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) sur la classe `Dataset` retourne la dernière version du jeu de données inscrit auprès de l’espace de travail.

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

* Découvrir [comment former un modèle avec des jeux de données](how-to-train-with-datasets.md).
* Utilisez le Machine Learning automatisé pour [vous entraîner avec les TabularDatasets](https://aka.ms/automl-dataset).
* Pour obtenir plus d’exemples d’entraînement de jeux de données, consultez les [exemples de notebooks](https://aka.ms/dataset-tutorial).
