---
title: Entraîner avec azureml-datasets
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser des jeux de données lors de l’entraînement.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: b20612756050ae2e9d39f59d049b8c097e3b8010
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88651214"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Entraîner avec des jeux de données dans Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Cet article va vous permettre d’apprendre à utiliser des jeux de données [Azure Machine Learning](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) dans vos expériences de formation.  Vous pouvez utiliser des jeux de données dans votre cible de calcul locale ou distante sans vous soucier des chaînes de connexion ou des chemins de données.

Les jeux de données Azure Machine Learning fournissent une intégration transparente avec les produits de formation Azure Machine Learning tels que [ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py), [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py), [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py) et les [pipelines Azure Machine Learning](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Prérequis

Pour créer des jeux de données et effectuer un entraînement avec eux, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

* Un [espace de travail Azure Machine Learning](how-to-manage-workspace.md).

* Le [SDK Azure Machine Learning pour Python installé](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), qui inclut le paquet azureml-datasets.

> [!Note]
> Certaines classes de jeu de données ont des dépendances avec le package [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py). Pour les utilisateurs Linux, ces classes sont uniquement prises en charge dans les distributions suivantes :  Red Hat Enterprise Linux, Ubuntu, Fedora et CentOS.

## <a name="access-and-explore-input-datasets"></a>Accéder aux jeux de données d’entrée et les explorer

Vous pouvez accéder à un TabularDataset existant à partir du script de formation d’une expérience sur votre espace de travail et charger ce jeu de données dans un dataframe pandas pour approfondir l’exploration de votre environnement local.

Le code suivant utilise la méthode [`get_context()`]() dans la classe [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) pour accéder au TabularDataset d’entrée existant, `titanic`, dans le script de formation. Il utilise ensuite la méthode [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) pour charger ce jeu de données dans un dataframe pandas afin d’approfondir l’exploration et la préparation des données avant la formation.

> [!Note]
> Si votre source de données originale contient NaN, des chaînes vides ou des valeurs vides, lorsque vous utilisez la fonction to_pandas_dataframe(), ces valeurs sont remplacées par une valeur *Null*. 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

Si vous avez besoin de charger les données préparées dans un nouveau jeu de données à partir d’un dataframe pandas en mémoire, écrivez les données dans un fichier local (par exemple un fichier parquet), puis créez un jeu de données à partir de ce fichier. Vous pouvez également créer des jeux de données à partir de chemins ou de fichiers locaux dans des magasins de données. Pour en savoir plus sur la création des jeux de données, consultez [cette page](how-to-create-register-datasets.md).

## <a name="use-datasets-directly-in-training-scripts"></a>Utiliser des jeux de données directement dans les script d’entraînement

Si vous disposez de données structurées qui ne sont pas encore inscrites en tant que jeu de données, créez un TabularDataset et utilisez-le directement dans votre script de formation pour votre expérience locale ou distante.

Dans cet exemple, vous créez un [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) non enregistré et vous en servez d’entrée directe dans votre objet `estimator` pour la formation. Si vous souhaitez réutiliser ce TabularDataset avec d’autres expériences dans votre espace de travail, consultez [Comment inscrire des jeux de données dans votre espace de travail](how-to-create-register-datasets.md#register-datasets).

### <a name="create-a-tabulardataset"></a>Créer un TabularDataset

Le code suivant crée un TabularDataset non inscrit à partir d’une URL web.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

Les objets TabularDataset permettent de charger les données de votre TabularDataset dans un DataFrame pandas ou Spark afin que vous puissiez travailler avec des bibliothèques de formation et de préparation des données familières sans avoir à quitter votre notebook. Pour tirer parti de cette fonctionnalité, consultez [Accéder aux jeux de données d’entrée et les explorer](#access-and-explore-input-datasets).

### <a name="configure-the-estimator"></a>Configurer l’estimateur

Un objet [estimateur](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) est utilisé pour soumettre l’exécution d’expérience. Azure Machine Learning propose des estimateurs préconfigurés pour les frameworks de Machine Learning courants, ainsi qu’un estimateur générique.

Ce code crée un objet estimateur générique, `est`, qui spécifie

* Un répertoire de script pour vos scripts. Tous les fichiers dans ce répertoire sont chargés dans les nœuds de cluster pour l’exécution.
* Le script d’entraînement, *train_titanic.py*.
* Le jeu de données d’entrée pour l’entraînement, `titanic_ds`. `as_named_input()` est requis pour que le jeu de données d’entrée puisse être référencé par le nom attribué `titanic` dans votre script de formation. 
* La cible de calcul pour l’expérience.
* La définition de l’environnement pour l’expérience.

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)
```

## <a name="mount-files-to-remote-compute-targets"></a>Monter des fichiers sur des cibles de calcul distantes

Si vous avez des données non structurées, créez un [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py) et montez ou téléchargez vos fichiers de données pour les mettre à la disposition de votre cible de calcul à distance pour la formation. Découvrez quand utiliser [le montage ou le téléchargement](#mount-vs-download) pour vos expériences de formation à distance. 

L’exemple suivant crée un FileDataset et monte l’ensemble de données sur la cible de calcul en le passant comme argument dans l’estimateur de formation. 

### <a name="create-a-filedataset"></a>Créer un FileDataset

L’exemple suivant crée un FileDataset non inscrit à partir d’URL web. Pour en savoir plus sur la création des jeux de données à partir d’autres sources, consultez [cette page](how-to-create-register-datasets.md).

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-estimator"></a>Configurer l’estimateur

Nous vous recommandons de passer le DataSet comme argument lors du montage. En plus de transmettre le jeu de données via le paramètre `inputs` dans l’estimateur, vous pouvez le transmettre via `script_params` et obtenir le chemin d’accès aux données (point de montage) dans votre script de formation via des arguments. Ainsi, vous serez en mesure d’utiliser le même script de formation pour le débogage local et la formation à distance sur toute plateforme cloud.

Un objet estimateur [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) est utilisé afin de soumettre l’exécution pour les expériences scikit-learn. Une fois que vous avez envoyé l’exécution, les fichiers de données référencés par le jeu de données `mnist` sont montés sur la cible de calcul. Apprenez-en davantage sur l’entraînement avec l’[estimateur SKlearn](how-to-train-scikit-learn.md).

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>Récupérer les données dans votre script d’entraînement

Le code suivant montre comment récupérer les données dans votre script.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through script_params in estimator
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="mount-vs-download"></a>Différences entre montage et téléchargement

Le montage ou le téléchargement de fichiers de tout format sont pris en charge pour des jeux de données créés à partir des stockages suivants : Stockage Blob Azure, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database et Azure Database pour PostgreSQL. 

Quand vous **montez** un jeu de données, vous attachez les fichiers référencés par le jeu de données à un répertoire (point de montage) et le rendez disponible sur la cible de calcul. Le montage est pris en charge pour les calculs basés sur Linux, y compris la capacité de calcul Azure Machine Learning, les machines virtuelles et HDInsight. 

Lorsque vous **téléchargez** un jeu de données, tous les fichiers référencés par le jeu de données sont téléchargés sur la cible de calcul. Le téléchargement est pris en charge pour tous les types de calcul. 

Si votre script traite tous les fichiers référencés par le jeu de données et que votre disque de calcul peut contenir le jeu de données complet, le téléchargement est recommandé pour éviter la charge de traitement inhérente à la diffusion en continu des données à partir des services de stockage. Si la taille de vos données dépasse la taille du disque de calcul, le téléchargement n’est pas possible. Pour ce scénario, nous recommandons un montage, car seuls les fichiers de données utilisés par votre script sont chargés au moment du traitement.

Le code suivant monte `dataset` dans le répertoire Temp dans le chemin `mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
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

## <a name="accessing-source-code-during-training"></a>Accès au code source pendant l’entraînement

Le stockage Blob Azure offre des vitesses de débit supérieures à celles du partage de fichiers Azure et s’adapte à un grand nombre de travaux démarrés en parallèle. C’est pourquoi nous vous recommandons de configurer vos exécutions pour utiliser le stockage Blob pour le transfert des fichiers de code source.

L’exemple de code suivant spécifie dans la configuration d’exécution le magasin de données d’objets blob à utiliser pour les transferts de code source.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Exemples de notebooks

Les [notebooks de jeux de données](https://aka.ms/dataset-tutorial) illustrent et développent les concepts abordés dans cet article.

## <a name="next-steps"></a>Étapes suivantes

* [Entraîner automatiquement des modèles Machine Learning](how-to-auto-train-remote.md) avec des TabularDatasets.

* [Entraîner des modèles de classification d’image](https://aka.ms/filedataset-samplenotebook) avec des FileDatasets.

* [Effectuez l’apprentissage des jeux de données à l'aide de pipelines](how-to-create-your-first-pipeline.md).
