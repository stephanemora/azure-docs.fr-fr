---
title: Déplacement de données dans des pipelines ML
titleSuffix: Azure Machine Learning
description: Découvrez l’entrée et la sortie de données dans des pipelines Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 08/20/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 1b6b5af2e6533c13165ae8253813a52b2c7ad261
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88756960"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Déplacement de données au sein d’un pipeline ML et vers un autre pipeline ML (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Cet article fournit du code pour l’importation, la transformation et le déplacement de données entre les étapes d’un pipeline Azure Machine Learning. Pour obtenir une vue d’ensemble du fonctionnement des données dans Azure Machine Learning, consultez [Accéder aux données dans les services de stockage Azure](how-to-access-data.md). Pour plus d’informations sur les avantages et la structure des pipelines Azure Machine Learning, consultez [Présentation des pipelines Azure Machine Learning](concept-ml-pipelines.md).

Cette article vous explique comment :

- Utiliser des objets `Dataset` pour les données préexistantes
- Accéder aux données dans vos étapes
- Fractionner les données `Dataset` en sous-ensembles, tels que des sous-ensembles d’apprentissage et de validation
- Créer des objets `OutputFileDatasetConfig` pour transférer des données à l’étape suivante du pipeline
- Utiliser des objets `OutputFileDatasetConfig` comme entrée pour les étapes de pipeline
- Créer des objets `Dataset` à partir des `OutputFileDatasetConfig` que vous souhaitez conserver

> [!NOTE]
>Les classes `OutputFileDatasetConfig` et `OutputTabularDatasetConfig` sont des fonctionnalités expérimentale en préversion qui peuvent évoluer à tout moment.
>
>Pour plus d'informations, consultez https://aka.ms/azuremlexperimental.

## <a name="prerequisites"></a>Prérequis

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree).

- Le [Kit de développement logiciel (SDK) Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou l’accès à [Azure Machine Learning studio](https://ml.azure.com/).

- Un espace de travail Azure Machine Learning.
  
  [Créez un espace de travail Azure Machine Learning](how-to-manage-workspace.md) ou utilisez un espace de travail existant via le Kit de développement logiciel (SDK) Python. Importez les classes `Workspace` et `Datastore`, puis chargez vos informations d’abonnement à partir du fichier `config.json` à l’aide de la fonction `from_config()`. Cette fonction recherche le fichier JSON dans le répertoire actif par défaut, mais vous pouvez également spécifier un paramètre de chemin d’accès pour pointer vers le fichier à l’aide de `from_config(path="your/file/path")`.

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Des données préexistantes. Cet article présente brièvement l’utilisation d’un [conteneur d’objets Blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview).

- Facultatif : Un pipeline de Machine Learning existant, tel que celui décrit dans [Créer et exécuter des pipelines de Machine Learning avec le kit SDK Azure Machine Learning](how-to-create-your-first-pipeline.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>Utiliser des objets `Dataset` pour les données préexistantes 

Le meilleur moyen d’ingérer des données dans un pipeline consiste à utiliser un objet [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py). Les objets `Dataset` représentent les données persistantes disponibles dans un espace de travail.

Il existe de nombreuses façons de créer et d’inscrire des objets `Dataset`. Les jeux de données tabulaires sont destinés à des données délimitées disponibles dans un ou plusieurs fichiers. Les jeux de données de fichier sont destinés à des données binaires (telles que des images) ou des données que vous allez analyser. La méthode la plus simple pour créer des objets `Dataset` consiste à utiliser des objets Blob existants dans le stockage de l’espace de travail ou à des URL publiques :

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Pour plus d’options sur la création de jeux de données avec différentes options et à partir de différentes sources, leur inscription et leur examen dans l’interface utilisateur Azure Machine Learning, la compréhension de la manière dont la taille des données influence la capacité de calcul et la gestion des versions, consultez [Créer des jeux de données Azure Machine Learning](how-to-create-register-datasets.md). 

### <a name="pass-datasets-to-your-script"></a>Passer des jeux de données à votre script

Pour passer le chemin d’accès du jeu de données à votre script, utilisez la méthode `as_named_input()` de l’objet `Dataset`. Vous pouvez passer l’objet `DatasetConsumptionConfig` résultant à votre script en tant qu’argument ou, en utilisant l’argument `inputs` à votre script de pipeline, vous pouvez récupérer le jeu de données à l’aide de `Run.get_context().input_datasets[]`.

Une fois que vous avez créé une entrée nommée, vous pouvez choisir son mode d’accès : `as_mount()` ou `as_download()`. Si votre script traite tous les fichiers de votre jeu de données et que le disque de votre ressource de calcul est suffisamment grand pour le jeu de données, le mode d’accès téléchargement est le meilleur choix. Le mode d’accès téléchargement évite la surcharge liée à la diffusion en continu des données au moment de l’exécution. Si votre script accède à un sous-ensemble du jeu de données ou s’il est trop grand pour votre calcul, utilisez le mode d’accès montage. Pour plus d’informations, consultez [Montage/ Téléchargement](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

Pour passer un jeu de données à votre étape de pipeline :

1. Utilisez `TabularDataset.as_named_inputs()` ou `FileDataset.as_named_input()` (pas de ’s’ à la fin) pour créer un objet `DatasetConsumptionConfig`
1. Utilisez `as_mount()` ou `as_download()` pour définir le mode d’accès
1. Transmettez les jeux de données à vos étapes de pipeline à l’aide de l'argument `arguments` ou `inputs`

L’extrait de code suivant montre le modèle commun de combinaison de ces étapes dans le constructeur `PythonScriptStep` : 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

Vous pouvez également utiliser des méthodes telles que `random_split()` et `take_sample()` pour créer plusieurs entrées ou réduire la quantité de données transmises à votre étape de pipeline :

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_inputs('train').as_download(), test.as_named_inputs('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Accéder aux jeux de données dans votre script

Les entrées nommées dans votre script d’étape de pipeline sont disponibles en tant que dictionnaire dans l’objet `Run`. Récupérez l’objet actif `Run` à l’aide de `Run.get_context()`, puis récupérez le dictionnaire d’entrées nommées à l’aide de `input_datasets`. Si vous avez passé l’objet `DatasetConsumptionConfig` à l’aide de l’argument `arguments` plutôt que de l’argument `inputs`, accédez aux données à l’aide du code `ArgParser`. Les deux techniques sont illustrées dans l’extrait de code suivant.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_inputs('train').as_download()]
    inputs=[test.as_named_inputs('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

La valeur transmise est le chemin d’accès au(x) fichier(s) de jeu de données.

Il est également possible d’accéder directement à un `Dataset` inscrit. Étant donné que les jeux de données inscrits sont persistants et partagés dans un espace de travail, vous pouvez les récupérer directement :

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-outputfiledatasetconfig-for-intermediate-data"></a>Utiliser `OutputFileDatasetConfig` pour les données intermédiaires

Si les objets `Dataset` représentent uniquement des données persistantes, les objets [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py) peuvent être utilisés pour les données temporaires générées à partir d’étapes de pipeline **et** de données de sortie persistantes. 

 Le comportement par défaut de l’objet `OutputFileDatasetConfig` consiste à écrire dans le magasin de données par défaut de l’espace de travail. Transmettez vos objets `OutputFileDatasetConfig` à votre `PythonScriptStep` avec le paramètre `arguments`.

```python
from azureml.data import OutputFileDatasetConfig
dataprep_output = OutputFileDatasetConfig()
input_dataset = Dataset.get_by_name(workspace, 'raw_data')

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
```

Vous pouvez choisir de charger le contenu de votre objet `OutputFileDatasetConfig` à la fin d’une exécution. Dans ce cas, utilisez la fonction `as_upload()` en même temps que votre objet `OutputFileDatasetConfig` et indiquez si les fichiers existants doivent être remplacés dans la destination. 

```python
#get blob datastore already registered with the workspace
blob_store= ws.datastores['my_blob_store']
OutputFileDatasetConfig(name="clean_data", destination=blob_store).as_upload(overwrite=False)
```

### <a name="use-outputfiledatasetconfig-as-outputs-of-a-training-step"></a>Utiliser `OutputFileDatasetConfig` comme sortie d’une étape d’apprentissage

Dans l’étape `PythonScriptStep` de votre pipeline, vous pouvez récupérer les chemins de sortie disponibles à l’aide des arguments du programme. Si cette étape est la première et initialise les données de sortie, vous devez créer le répertoire au chemin d’accès spécifié. Vous pouvez ensuite écrire les fichiers que vous souhaitez inclure dans les `OutputFileDatasetConfig`.

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

### <a name="read-outputfiledatasetconfig-as-inputs-to-non-initial-steps"></a>Lire les `OutputFileDatasetConfig` en tant qu’entrées pour les étapes non initiales

Une fois que l’étape de pipeline initiale a écrit des données dans le chemin `OutputFileDatasetConfig` et qu’elles deviennent une sortie de cette étape initiale, elles peuvent être utilisées comme entrée dans une étape ultérieure. 

Dans le code suivant, 

* `step1_output_data` indique que la sortie de PythonScriptStep `step1` est écrite dans le magasin de données ADLS Gen 2 `my_adlsgen2` en mode d’accès en chargement. Découvrez plus en détail la procédure de [configuration d’autorisations de rôle](how-to-access-data.md#azure-data-lake-storage-generation-2) en vue de réécrire les données dans des banques de données ADLS Gen 2. 

* Une fois `step1` terminé et la sortie écrite dans la destination indiquée par `step1_output_data`, l’étape 2 est prête à utiliser `step1_output_data` comme entrée. 

```python
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']
step1_output_data = OutputFileDatasetConfig(name="processed_data", destination=datastore).as_upload()

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data.as_input]

)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

## <a name="register-outputfiledatasetconfig-objects-for-reuse"></a>Inscrire des objets `OutputFileDatasetConfig` pour les réutiliser

Si vous souhaitez rendre votre `OutputFileDatasetConfig` disponible au-delà la durée de votre expérience, inscrivez-le dans votre espace de travail pour le partager et le réutiliser dans les expériences.

```python
step1_output_ds = step1_output_data.register_on_complete(name='processed_data', 
                                                         description = 'files from step1`)
```

## <a name="next-steps"></a>Étapes suivantes

* [Créer un jeu de données Azure Machine Learning](how-to-create-register-datasets.md)
* [Créer et exécuter des pipelines de machine learning avec le kit SDK Azure Machine Learning](how-to-create-your-first-pipeline.md)
