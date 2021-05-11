---
title: Data wrangling avec des pools Apache Spark (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment attacher et lancer des pools Apache Spark pour le data wrangling avec Azure Synapse Analytics et Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: f175e8d5c3dd19b212dfbdd04025d12f549667ed
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293293"
---
# <a name="attach-apache-spark-pools-powered-by-azure-synapse-analytics-for-data-wrangling-preview"></a>Attacher des pools Apache Spark (avec Azure Synapse Analytics) pour le data wrangling (préversion)

Dans cet article, vous allez apprendre à attacher un pool Apache Spark avec [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) à votre [espace de travail Azure Machine Learning](concept-workspace.md), afin de pouvoir le lancer et effectuer du data wrangling à grande échelle. 

Cet article contient des conseils pour effectuer des tâches de data wrangling de manière interactive au sein d’une session Synapse dédiée dans un notebook Jupyter au moyen du [kit SDK Python d’Azure Machine Learning](/python/api/overview/azure/ml/). Si vous préférez utiliser des pipelines Azure Machine Learning, consultez le [Guide pratique pour utiliser Apache Spark (fourni par Azure Synapse Analytics) dans votre pipeline Machine Learning (préversion)](how-to-use-synapsesparkstep.md).

Si vous recherchez des conseils sur l’utilisation d’Azure Synapse Analytics avec un espace de travail Synapse, consultez la [série Prise en main d’Azure Synapse Analytics](../synapse-analytics/get-started.md).

>[!IMPORTANT]
> L’intégration d’Azure Machine Learning et d’Azure Synapse Analytics est disponible en préversion. Les fonctionnalités présentées dans cet article utilisent le package `azureml-synapse` qui contient des fonctionnalités d’évaluation [expérimentales](/python/api/overview/azure/ml/#stable-vs-experimental) susceptibles de changer à tout moment.

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration-preview"></a>Intégration d’Azure Machine Learning et d’Azure Synapse Analytics (préversion)

L’intégration d’Azure Synapse Analytics à Azure Machine Learning (préversion) vous permet d’attacher un pool Apache Spark en utilisant Azure Synapse pour l’exploration et la préparation de données interactives. Avec cette intégration, vous pouvez disposer d’un calcul dédié au data wrangling à grande échelle, le tout dans le notebook Python que vous utilisez pour entraîner vos modèles Machine Learning.

## <a name="prerequisites"></a>Prérequis

* Le [kit SDK Python d’Azure Machine Learning est installé](/python/api/overview/azure/ml/install). 

* [Créez un espace de travail Azure Machine Learning](how-to-manage-workspace.md?tabs=python).

* [Créez un espace de travail Azure Synapse Analytics dans le portail Azure](../synapse-analytics/quickstart-create-workspace.md).

* [Créez un pool Apache Spark à l’aide du portail Azure, d’outils web ou de Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).

* [Configurez votre environnement de développement](how-to-configure-environment.md) pour installer le SDK Azure Machine Learning ou utilisez une [instance de calcul Azure Machine Learning](concept-compute-instance.md#create) avec le SDK déjà installé. 

* Installez le package `azureml-synapse` (préversion) avec le code suivant :

  ```python
  pip install azureml-synapse
  ```

* [Liez l’espace de travail Azure Machine Learning et l’espace de travail Azure Synapse Analytics](how-to-link-synapse-ml-workspaces.md).

## <a name="get-an-existing-linked-service"></a>Obtenir un service lié existant
Avant de pouvoir attacher un calcul dédié au data wrangling, vous devez disposer d’un espace de travail ML lié à un espace de travail Azure Synapse Analytics, ce que l’on appelle un service lié. 

Pour récupérer et utiliser un service lié existant, vous devez disposer des autorisations **Utilisateur ou Collaborateur** sur l’espace de travail Azure Synapse Analytics.

Affichez tous les services liés associés à votre espace de travail de Machine Learning. 

```python
from azureml.core import LinkedService

LinkedService.list(ws)
```

Cet exemple récupère un service lié existant, `synapselink1`, à partir de l’espace de travail, `ws`, avec la méthode [`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-).
```python
from azureml.core import LinkedService

linked_service = LinkedService.get(ws, 'synapselink1')
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Attacher un pool Synapse Spark en tant que calcul

Après avoir récupéré le service lié, attachez un pool Synapse Apache Spark en tant que ressource de calcul dédiée à vos tâches de data wrangling. 

Vous pouvez attacher des pools Apache Spark en utilisant :
* Azure Machine Learning Studio
* [Modèles Azure Resource Manager (ARM)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* Le kit SDK Python d’Azure Machine Learning 

### <a name="attach-a-pool-via-the-studio"></a>Attacher un pool par le biais du studio
Suivez les étapes ci-dessous : 

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com/).
1. Sélectionnez **Services liés** dans la section **Gérer** du volet gauche.
1. Sélectionnez votre espace de travail Synapse.
1. Sélectionnez **Pools Spark attachés** en haut à gauche. 
1. Sélectionnez **Attacher**. 
1. Sélectionnez votre pool Apache Spark dans la liste et indiquez un nom.  
    1. Cette liste identifie les pools Synapse Spark disponibles qui peuvent être attachés à votre calcul. 
    1. Pour créer un pool Synapse Spark, consultez [Créer un pool Apache Spark avec Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).
1. Sélectionnez **Attacher l’élément sélectionné**. 

### <a name="attach-a-pool-with-the-python-sdk"></a>Attacher un pool à l’aide du kit SDK Python

Vous pouvez également utiliser le **SDK Python** pour attacher un pool Apache Spark. 

Le code suivant, 
1. Configure le [`SynapseCompute`](/python/api/azureml-core/azureml.core.compute.synapsecompute) avec,

   1. Le [`LinkedService`](/python/api/azureml-core/azureml.core.linkedservice), `linked_service` que vous avez créé ou récupéré à l’étape précédente. 
   1. Le type de cible de calcul que vous souhaitez attacher, `SynapseSpark`.
   1. Le nom du pool Apache Spark. Celui-ci doit correspondre à un pool Apache Spark existant qui se trouve dans votre espace de travail Azure Synapse Analytics.
   
1. Crée un [`ComputeTarget`](/python/api/azureml-core/azureml.core.computetarget) de Machine Learning en passant, 
   1. L’espace de travail de Machine Learning que vous souhaitez utiliser, `ws`
   1. Le nom que vous souhaitez pour faire référence au calcul dans l’espace de travail Azure Machine Learning. 
   1. Le paramètre attach_configuration que vous avez spécifié lors de la configuration de votre calcul Synapse.
       1. L’appel à ComputeTarget.attach() étant asynchrone, l’exemple se bloque jusqu’à la fin de l’appel.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name=synapse_spark_pool_name) #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name= synapse_compute_name, 
                                       attach_configuration= attach_config
                                      )

synapse_compute.wait_for_completion()
```

Vérifiez que le pool Apache Spark est attaché.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-wrangling-tasks"></a>Lancer le pool Synapse Spark pour les tâches de data wrangling

Pour commencer la préparation des données avec le pool Apache Spark, spécifiez le nom du pool Apache Spark :

> [!IMPORTANT]
> Pour continuer à utiliser le pool Apache Spark, vous devez indiquer la ressource de calcul à utiliser dans vos tâches de data wrangling, avec `%synapse` pour les lignes de code uniques, et `%%synapse` lorsqu’il y a plusieurs lignes. 

```python
%synapse start -c SynapseSparkPoolAlias
```

Une fois que la session a démarré, vous pouvez vérifier ses métadonnées.

```python
%synapse meta
```

Vous pouvez spécifier un [environnement Azure Machine Learning](concept-environments.md) à utiliser au cours de votre session Apache Spark. Seules les dépendances Conda spécifiées dans l’environnement prennent effet. L’image Docker n’est pas prise en charge.

>[!WARNING]
>  Les dépendances Python spécifiées dans les dépendances Conda de l’environnement ne sont pas prises en charge dans les pools Apache Spark. Seules les versions de Python fixes sont prises en charge. Vérifiez votre version de Python en incluant `sys.version_info` dans votre script.

Le code suivant crée l’environnement, `myenv`, qui installe `azureml-core` version 1.20.0 et `numpy` version 1.17.0 avant le début de la session. Vous pouvez ensuite inclure cet environnement dans l’instruction `start` de votre session Apache Spark.

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Pour démarrer la préparation des données avec le pool Apache Spark et votre environnement personnalisé, spécifiez le nom du pool Apache Spark et l’environnement à utiliser pendant la session Apache Spark. En outre, vous pouvez fournir votre ID d’abonnement, le groupe de ressources de l’espace de travail Machine Learning et le nom de l’espace de travail Machine Learning.

```python
%synapse start -c SynapseSparkPoolAlias -e myenv -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName
```
## <a name="load-data-from-storage"></a>Charger des données à partir du stockage

Une fois votre session Apache Spark démarrée, lisez les données que vous souhaitez préparer. Le chargement des données est pris en charge pour Stockage Blob Azure et Azure Data Lake Storage générations 1 et 2.

Il existe deux façons de charger des données à partir de ces services de stockage : 

* Chargez directement les données à partir du stockage à l’aide de son chemin HDFS (Hadoop Distributed Files System).

* Lisez les données d’un [jeu de données Azure Machine Learning](how-to-create-register-datasets.md) existant.

Pour accéder à ces services de stockage, vous avez besoin d’autorisations **Lecteur des données Blob du stockage**. Si vous envisagez d’écrire des données dans ces services de stockage, vous avez besoin d’autorisations **Contributeur aux données Blob du stockage**. [Découvrez-en plus sur les rôles et autorisations de stockage](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Charger des données avec le chemin HDFS (Hadoop Distributed Files System)

Pour que vous puissiez charger et lire des données depuis le stockage avec le chemin HDFS correspondant, les informations d’authentification d’accès aux données doivent être immédiatement disponibles. Ces informations d’identification diffèrent selon votre type de stockage.  

Le code suivant montre comment lire des données à partir d’un **stockage d’objets Blob Azure** dans un dataframe Spark avec votre jeton de signature d’accès partagé (SAS) ou votre clé d’accès. 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

Le code suivant montre comment lire des données à partir d’**ADLS GEN 1 (Azure Data Lake Storage Generation 1)** avec vos informations d’identification de principal de service. 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

Le code suivant montre comment lire des données à partir d’**ADLS GEN 2 (Azure Data Lake Storage Generation 2)** avec vos informations d’identification de principal de service. 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>Lire des données à partir de jeux de données inscrits

Vous pouvez également obtenir un jeu de données inscrit existant dans votre espace de travail et effectuer la préparation des données sur ce jeu en le convertissant en dataframe Spark.

L’exemple suivant s’authentifie à un espace de travail, obtient un TabularDataset enregistré, `blob_dset`, qui fait référence à des fichiers dans le stockage blob et le convertit en tramedonnées Spark. Quand vous convertissez vos jeux de données en dataframe Spark, vous pouvez tirer parti des bibliothèques `pyspark` d’exploration et de préparation des données.  

``` python
%%synapse

from azureml.core import Workspace, Dataset

subscription_id = "<enter your subscription ID>"
resource_group = "<enter your resource group>"
workspace_name = "<enter your workspace name>"

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-wrangling-tasks"></a>Effectuer des tâches de data wrangling

Une fois que vous avez récupéré et exploré vos données, vous pouvez effectuer des tâches de data wrangling.

Le code suivant, qui reprend l’exemple HDFS de la section précédente, filtre les données du dataframe Spark, `df`, en fonction de la colonne **Survivor** et regroupe cette liste par le critère **Age**.

```python
%%synapse

from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>Enregistrer les données dans le stockage et arrêter la session Spark

Une fois l’exploration et la préparation des données terminées, stockez vos données préparées pour une utilisation ultérieure dans votre compte de stockage sur Azure.

Dans l’exemple suivant, les données préparées sont écrites dans le stockage d’objets Blob Azure et remplacent le fichier `Titanic.csv` d’origine dans le répertoire `training_data`. Pour écrire dans le stockage, vous avez besoin d’autorisations **Contributeur aux données Blob du stockage**. [Découvrez-en plus sur les rôles et autorisations de stockage](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

Une fois que vous avez terminé la préparation des données et enregistré vos données préparées dans le stockage, arrêtez d’utiliser votre pool Apache Spark avec la commande suivante.

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>Créer un jeu de données pour représenter les données préparées

Quand vous êtes prêt à consommer vos données préparées pour l’entraînement des modèles, connectez-vous à votre stockage avec un [magasin de données Azure Machine Learning](how-to-access-data.md) et spécifiez le ou les fichiers que vous souhaitez utiliser avec un [jeu de données Azure Machine Learning](how-to-create-register-datasets.md).

L’exemple de code suivant :

* Part du principe que vous avez déjà créé un magasin de données qui se connecte au service de stockage où vous avez enregistré vos données préparées.  
* Obtient ce magasin de données existant, `mydatastore`, à partir de l’espace de travail, `ws`, avec la méthode get().
* Crée un [jeu de données de fichiers](how-to-create-register-datasets.md#filedataset), `train_ds`, qui référence les fichiers de données préparées situés dans le répertoire `training_data` dans `mydatastore`.  
* Crée la variable `input1`, qui peut être utilisée ultérieurement pour mettre à la disposition d’une cible de calcul les fichiers de données du jeu de données `train_ds` pour vos tâches de formation.

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="use-a-scriptrunconfig-to-submit-an-experiment-run-to-a-synapse-spark-pool"></a>Utiliser un `ScriptRunConfig` pour soumettre une exécution d’essai à un pool Synapse Spark

Si vous êtes prêt à automatiser et à mettre en production vos tâches de data wrangling, vous pouvez soumettre une exécution de test au [pool Synapse Spark que vous avez attaché précédemment](#attach-a-pool-with-the-python-sdk) avec l’objet [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig).  

De même, si vous avez un pipeline Azure Machine Learning, vous pouvez utiliser l’objet [SynapseSparkStep pour spécifier votre pool Synapse Spark comme cible de calcul pour l’étape de préparation des données dans votre pipeline](how-to-use-synapsesparkstep.md).

La mise à disposition de vos données au pool Synapse Spark dépend du type de votre jeu de données. 

* Pour un FileDataset, vous pouvez utiliser la méthode [`as_hdfs()`](/python/api/azureml-core/azureml.data.filedataset#as-hdfs--). Lorsque l’exécution est soumise, le jeu de données est mis à la disposition du pool Synapse Spark en tant que système de fichiers distribué Hadoop (HFDS). 
* Pour un [TabularDataset](how-to-create-register-datasets.md#tabulardataset), vous pouvez utiliser la méthode [`as_named_input()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#as-named-input-name-). 

Le code suivant, 

* Crée la variable `input2` à partir du FileDataset `train_ds` qui a été créé dans l’exemple de code précédent.
* Crée la variable `output` avec la classe HDFSOutputDatasetConfiguration. Une fois l’exécution terminée, cette classe nous permet d’enregistrer la sortie de l’exécution en tant que jeu de données `test` dans le magasin de données `mydatastore`. Dans l’espace de travail Azure Machine Learning, le jeu de données `test` est enregistré sous le nom `registered_dataset`. 
* Configure les paramètres que l’exécution doit utiliser afin de fonctionner sur le pool Synapse Spark. 
* Définit les paramètres ScriptRunConfig pour : 
  * Utiliser le `dataprep.py` pour l’exécution. 
  * Spécifier les données à utiliser comme entrée et la façon de les mettre à la disposition du pool Synapse Spark.
  * Spécifier où stocker les données de sortie, `output`.  

```Python
from azureml.core import Dataset, HDFSOutputDatasetConfig
from azureml.core import RunConfiguration
from azureml.core import ScriptRunConfig 
from azureml.core import Experiment

input2 = train_ds.as_hdfs()
output = HDFSOutputDatasetConfig(destination=(datastore, "test").register_on_complete(name="registered_dataset")

run_config = RunConfiguration(framework="pyspark")
run_config.target = synapse_compute_name

run_config.spark.configuration["spark.driver.memory"] = "1g" 
run_config.spark.configuration["spark.driver.cores"] = 2 
run_config.spark.configuration["spark.executor.memory"] = "1g" 
run_config.spark.configuration["spark.executor.cores"] = 1 
run_config.spark.configuration["spark.executor.instances"] = 1 

run_config.environment.python.conda_dependencies = conda_dep

script_run_config = ScriptRunConfig(source_directory = './code',
                                    script= 'dataprep.py',
                                    arguments = ["--file_input", input2,
                                                 "--output_dir", output],
                                    run_config = run_config)
```

Une fois votre objet `ScriptRunConfig` configuré, vous pouvez soumettre l’exécution.

```python
from azureml.core import Experiment 

exp = Experiment(workspace=ws, name="synapse-spark") 
run = exp.submit(config=script_run_config) 
run
```

Pour plus d’informations, comme le script `dataprep.py` utilisé dans cet exemple, consultez l’[exemple de notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb).

Une fois vos données préparées, vous pouvez les utiliser comme entrée pour vos travaux de formation. Dans l’exemple de code ci-dessus, le `registered_dataset` est ce que vous spécifiez comme données d’entrée pour les travaux de formation. 

## <a name="example-notebooks"></a>Exemples de notebooks

Consultez les exemples de notebooks pour découvrir d’autres concepts et démonstrations des capacités d’intégration d’Azure Synapse Analytics et d’Azure Machine Learning.
* [Exécutez une session Spark interactive à partir d’un notebook dans votre espace de travail Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_session_on_synapse_spark_pool.ipynb).
* [Soumettez une exécution de test Azure Machine Learning avec un pool Synapse Spark comme cible de calcul](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb).

## <a name="next-steps"></a>Étapes suivantes

* [Effectuer l’apprentissage d’un modèle](how-to-set-up-training-targets.md).
* [Effectuer l’entraînement avec un jeu de données Azure Machine Learning](how-to-train-with-datasets.md).
