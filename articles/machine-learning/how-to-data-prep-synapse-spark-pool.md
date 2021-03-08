---
title: Préparation des données avec des pools Apache Spark (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment attacher des pools Apache Spark pour la préparation des données avec Azure Synapse Analytics et Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 22945cdaff2696a15d5b119bd0f32fd0a179ebf7
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202091"
---
# <a name="attach-apache-spark-pools-powered-by-azure-synapse-analytics-for-data-preparation-preview"></a>Joindre des pools Apache Spark (gérés par Azure Synapse Analytics) pour la préparation des données (préversion)

Dans cet article, vous allez apprendre à attacher et à lancer un pool Apache Spark géré par [Azure Synapse Analytics](/synapse-analytics/overview-what-is.md) pour la préparation des données. 

>[!IMPORTANT]
> L’intégration d’Azure Machine Learning et d’Azure Synapse Analytics est disponible en préversion. Les fonctionnalités présentées dans cet article utilisent le package `azureml-synapse` qui contient des fonctionnalités d’évaluation [expérimentales](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) susceptibles de changer à tout moment.

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration-preview"></a>Intégration d’Azure Machine Learning et d’Azure Synapse Analytics (préversion)

L’intégration d’Azure Synapse Analytics à Azure Machine Learning (préversion) vous permet d’attacher un pool Apache Spark en utilisant Azure Synapse pour l’exploration et la préparation de données interactives. Avec cette intégration, vous pouvez avoir un calcul dédié à la préparation des données à grande échelle, le tout dans le notebook Python que vous utilisez pour entraîner vos modèles Machine Learning.

## <a name="prerequisites"></a>Prérequis

* [Créez un espace de travail Azure Machine Learning](how-to-manage-workspace.md?tabs=python).

* [Créez un espace de travail Azure Synapse Analytics dans le portail Azure](../synapse-analytics/quickstart-create-workspace.md).

* [Créez un pool Apache Spark à l’aide du portail Azure, d’outils web ou de Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).

* [Installez le SDK Python Azure Machine Learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) qui comprend le package `azureml-synapse` (préversion). 
    * Vous pouvez également l’installer vous-même, mais il est compatible uniquement avec les versions 1.20 ou ultérieures du SDK. 
        ```python
        pip install azureml-synapse
        ```

## <a name="link-machine-learning-workspace-and-synapse-analytics-assets"></a>Lier l’espace de travail de Machine Learning et les ressources Synapse Analytics

Avant de pouvoir attacher un pool Apache Synapse Spark pour la préparation des données, vous devez lier votre espace de travail Azure Machine Learning à votre espace de travail Azure Synapse Analytics. 

Vous pouvez lier votre espace de travail Machine Learning et votre espace de travail Synapse Analytics par le biais du [SDK Python](#link-sdk) ou d’[Azure Machine Learning Studio](#link-studio). 

> [!IMPORTANT]
> Pour établir correctement une liaison à l’espace de travail Azure Synapse Analytics, vous devez disposer du rôle **Propriétaire** de l’espace de travail Azure Synapse Analytics. Vérifiez votre accès dans le [portail Azure](https://ms.portal.azure.com/).
>
> Si vous n’êtes pas **Propriétaire** de l’espace de travail Azure Synapse Analytics, mais que vous souhaitez utiliser un service lié existant, consultez [Obtenir un service lié existant](#get-an-existing-linked-service).


<a name="link-sdk"></a>
### <a name="link-workspaces-with-the-python-sdk"></a>Lier des espaces de travail avec le SDK Python

Le code suivant utilise les classes [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice?preserve-view=true&view=azure-ml-py) et [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration?preserve-view=true&view=azure-ml-py) pour : 

* Lier votre espace de travail Azure Machine Learning, `ws` et votre espace de travail Azure Synapse Analytics. 
* Inscrire votre espace de travail Azure Synapse Analytics auprès d’Azure Machine Learning en tant que service lié.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```
> [!IMPORTANT] 
> Une identité managée, `system_assigned_identity_principal_id`, est créée pour chaque service lié. Cette identité managée doit se voir accorder le rôle **Administrateur Synapse Apache Spark** de l’espace de travail Azure Synapse Analytics avant que vous ne démarriez votre session Apache Spark. [Affectez le rôle Administrateur Synapse Apache Spark à l’identité managée dans Synapse Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md).
>
> Pour rechercher le `system_assigned_identity_principal_id` d’un service lié spécifique, utilisez `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')`.

<a name="link-studio"></a>
### <a name="link-workspaces-via-studio"></a>Lier des espaces de travail par le biais de Studio

Liez votre espace de travail Azure Machine Learning et votre espace de travail Azure Synapse Analytics par le biais d’Azure Machine Learning Studio en procédant comme suit : 

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com/).
1. Sélectionnez **Services liés** dans la section **Gérer** du volet gauche.
1. Sélectionnez **Ajouter une intégration**.
1. Renseignez les champs du formulaire **Lier l’espace de travail**.

   |Champ| Description    
   |---|---
   |Nom| Fournissez un nom pour votre service lié. Ce nom sera utilisé pour référencer ce service lié particulier.
   |Nom d’abonnement | Sélectionnez le nom de votre abonnement associé à votre espace de travail de Machine Learning. 
   |Espace de travail Synapse | Sélectionnez l’espace de travail Synapse vers lequel vous souhaitez établir un lien. 
   
1. Sélectionnez **Suivant** pour ouvrir le formulaire **Sélectionner des pools Spark (facultatif)** . Dans ce formulaire, vous sélectionnez le pool Synapse Apache Spark à attacher à votre espace de travail.

1. Sélectionnez **Suivant** pour ouvrir le formulaire **Vérifier** et vérifier vos sélections. 
1. Sélectionnez **Créer** pour achever le processus de création du service lié.

## <a name="get-an-existing-linked-service"></a>Obtenir un service lié existant

Pour récupérer et utiliser un service lié existant, vous devez disposer des autorisations **Utilisateur ou Collaborateur** sur l’espace de travail Azure Synapse Analytics.

Cet exemple récupère un service lié existant, `synapselink1`, à partir de l’espace de travail, `ws`, avec la méthode [`get()`](/python/api/azureml-core/azureml.core.linkedservice?preserve-view=true&view=azure-ml-py#get-workspace--name-).
```python
linked_service = LinkedService.get(ws, 'synapselink1')
```

### <a name="manage-linked-services"></a>Gérer des services liés

Pour dissocier vos espaces de travail, utilisez la méthode `unregister()`.

``` python
linked_service.unregister()
```

Affichez tous les services liés associés à votre espace de travail de Machine Learning. 

```python
LinkedService.list(ws)
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Attacher un pool Synapse Spark en tant que calcul

Une fois vos espaces de travail liés, attachez un pool Synapse Apache Spark en tant que ressource de calcul dédiée à vos tâches de préparation des données. 

Vous pouvez attacher des pools Apache Spark en utilisant :
* Azure Machine Learning Studio
* [Modèles Azure Resource Manager (ARM)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* SDK Python 

Procédez comme suit pour attacher un pool Apache Spark à l’aide de Studio. 

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com/).
1. Sélectionnez **Services liés** dans la section **Gérer** du volet gauche.
1. Sélectionnez votre espace de travail Synapse.
1. Sélectionnez **Pools Spark attachés** en haut à gauche. 
1. Sélectionnez **Attacher**. 
1. Sélectionnez votre pool Apache Spark dans la liste et indiquez un nom.  
    1. Cette liste identifie les pools Synapse Spark disponibles qui peuvent être attachés à votre calcul. 
    1. Pour créer un pool Synapse Spark, consultez [Créer un pool Apache Spark avec Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).
1. Sélectionnez **Attacher l’élément sélectionné**. 


Vous pouvez également utiliser le **SDK Python** pour attacher un pool Apache Spark. 

Le code suivant, 
1. Configure le calcul Synapse avec,

   1. Le service lié, `linked_service`, que vous avez créé ou récupéré à l’étape précédente. 
   1. Le type de cible de calcul que vous souhaitez attacher, `SynapseSpark`.
   1. Le nom du pool Apache Spark. Celui-ci doit correspondre à un pool Apache Spark existant qui se trouve dans votre espace de travail Azure Synapse Analytics.
   
1. Crée une cible de calcul de Machine Learning en passant 
   1. L’espace de travail de Machine Learning que vous souhaitez utiliser, `ws`
   1. Le nom que vous souhaitez pour faire référence au calcul dans l’espace de travail Azure Machine Learning. 
   1. Le paramètre attach_configuration que vous avez spécifié lors de la configuration de votre calcul Synapse.
       1. L’appel à ComputeTarget.attach() étant asynchrone, l’exemple se bloque jusqu’à la fin de l’appel.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name="<Synapse Spark pool name>") #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name='<Synapse Spark pool alias in Azure ML>', 
                                       attach_configuration=attach_config
                                      )

synapse_compute.wait_for_completion()
```

Vérifiez que le pool Apache Spark est attaché.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-preparation-tasks"></a>Lancer le pool Synapse Spark pour les tâches de préparation des données

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

Pour commencer la préparation des données avec le pool Apache Spark, spécifiez le nom du pool Apache Spark et fournissez votre ID d’abonnement, le groupe de ressources de l’espace de travail de Machine Learning, le nom de l’espace de travail de Machine Learning et l’environnement à utiliser pendant la session Apache Spark. 

> [!IMPORTANT]
> Pour continuer à utiliser le pool Apache Spark, vous devez indiquer la ressource de calcul à utiliser dans vos tâches de préparation des données avec `%synapse` pour les lignes de code uniques et `%%synapse` pour plusieurs lignes. 

```python
%synapse start -c SynapseSparkPoolAlias -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName -e myenv
```

Une fois que la session a démarré, vous pouvez vérifier ses métadonnées.

```python
%synapse meta
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
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "sas token")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

Le code suivant montre comment lire des données à partir d’**ADLS GEN 1 (Azure Data Lake Storage Generation 1)** avec vos informations d’identification de principal de service. 

```python

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
https://login.microsoftonline.com/<tenant id>/oauth2/token)

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

Le code suivant montre comment lire des données à partir d’**ADLS GEN 2 (Azure Data Lake Storage Generation 2)** avec vos informations d’identification de principal de service. 

```python
# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
https://login.microsoftonline.com/<tenant id>/oauth2/token)


df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>Lire des données à partir de jeux de données inscrits

Vous pouvez également obtenir un jeu de données inscrit existant dans votre espace de travail et effectuer la préparation des données sur ce jeu en le convertissant en dataframe Spark.  

L’exemple suivant obtient un jeu de données tabulaire inscrit, `blob_dset`, qui référence des fichiers dans le stockage d’objets blob, et le convertit en dataframe Spark. Quand vous convertissez vos jeux de données en dataframe Spark, vous pouvez tirer parti des bibliothèques `pyspark` d’exploration et de préparation des données.  

``` python

%%synapse
from azureml.core import Workspace, Dataset

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-preparation-tasks"></a>Effectuer des tâches de préparation des données

Une fois que vous avez récupéré et exploré vos données, vous pouvez effectuer des tâches de préparation des données.

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
* Crée la variable `input1`, qui peut être utilisée ultérieurement pour mettre à la disposition d’une cible de calcul les fichiers de données du jeu de données `train_ds`.

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="next-steps"></a>Étapes suivantes

* [Effectuer l’apprentissage d’un modèle](how-to-set-up-training-targets.md).
* [Effectuer l’entraînement avec un jeu de données Azure Machine Learning](how-to-train-with-datasets.md).
* [Créer un jeu de données Azure Machine Learning](how-to-create-register-datasets.md).