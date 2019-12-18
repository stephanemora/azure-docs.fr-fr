---
title: Accéder aux données dans les services de stockage Azure
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser des magasins de données pour accéder aux services de stockage Azure lors de l’entraînement avec Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 94cdf683bc8524786e1f32607ef18f976990ba07
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979119"
---
# <a name="access-data-in-azure-storage-services"></a>Accéder aux données dans les services de stockage Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, découvrez comment accéder facilement à vos données dans les services de stockage Azure via des magasins de données Azure Machine Learning. Les banques de données permettent de stocker les informations de connexion, comme votre ID d’abonnement et votre autorisation de jeton. L’utilisation de banques de données vous permet d’accéder à votre stockage sans avoir à coder en dur les informations de connexion dans vos scripts. Vous pouvez créer des magasins de données à partir de ces [solutions de stockage Azure](#matrix). Pour les solutions de stockage non prises en charge, nous vous recommandons de déplacer vos données vers nos solutions de stockage Azure prises en charge afin de réduire le coût de sortie des données pendant les expériences de Machine Learning. [Découvrez comment déplacer vos données](#move). 

Cette procédure montre des exemples des tâches suivantes :
* Enregistrer les magasins de données
* Récupérer les magasins de données de l’espace de travail
* Charger et télécharger des données à l’aide de magasins de données
* Accéder aux données pendant l’entraînement
* Déplacer des données vers le service de stockage Azure

## <a name="prerequisites"></a>Prérequis
Vous devez disposer des éléments suivants :
- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

- Un compte de stockage Azure avec un [conteneur d’objets blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) ou un [partage de fichiers Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- Le [Kit de développement logiciel (SDK) Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou l’accès à [Azure Machine Learning studio](https://ml.azure.com/).

- Un espace de travail Azure Machine Learning. 
    - [Créez un espace de travail Azure Machine Learning](how-to-manage-workspace.md) ou utilisez un espace de travail existant à l’aide du SDK Python.

        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore
        
        ws = Workspace.from_config()
        ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Créer et inscrire des magasins de données

Quand vous inscrivez une solution de stockage Azure en tant que magasin de données, ce dernier est automatiquement créé dans un espace de travail spécifique. Vous pouvez créer des magasins de données et les inscrire auprès d’un espace de travail à l’aide du SDK Python ou d’Azure Machine Learning Studio.

### <a name="using-the-python-sdk"></a>Utilisation du Kit de développement logiciel (SDK) Python

Toutes les méthodes d’inscription sont sur la classe [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) et ont la forme register_azure_*.

Vous trouverez les informations dont vous avez besoin pour renseigner la méthode register() dans [Azure Machine Learning Studio](https://ml.azure.com) et dans les instructions suivantes.

1. Sélectionnez **Comptes de stockage** dans le volet gauche, puis choisissez le compte de stockage à inscrire. 
2. La page **Vue d’ensemble** fournit des informations telles que le nom du compte et le nom du conteneur ou du partage de fichiers. 
3. Pour obtenir des informations d’authentification comme la clé de compte ou le jeton SAS, accédez à **Clés de compte** sous le volet **Paramètres** à gauche. 

>[IMPORTANT] Si votre compte de stockage se trouve dans un réseau virtuel, seule la création d’un magasin de données d’objets blob Azure est prise en charge. Définissez le paramètre, `grant_workspace_access` sur `True` pour accorder à votre espace de travail l’accès à votre compte de stockage.

Les exemples suivants vous montrent comment inscrire un conteneur d’objets blob Azure ou un partage de fichiers Azure comme magasin de données.

+ Pour un **magasin de données de conteneur d’objets blob Azure**, utilisez [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)

    Le code suivant crée le magasin de données `my_datastore` et l’inscrit auprès de l’espace de travail `ws`. Ce magasin de données accède au conteneur d’objets blob Azure `my_blob_container` sur le compte de stockage Azure `my_storage_account` à l’aide de la clé de compte fournie.

    ```Python
       datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                          datastore_name='my_datastore', 
                                                          container_name='my_blob_container',
                                                          account_name='my_storage_account', 
                                                          account_key='your storage account key',
                                                          create_if_not_exists=True)
    ```

+ Pour un **magasin de données de partage de fichiers Azure**, utilisez [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

    Le code suivant crée le magasin de données `my_datastore` et l’inscrit auprès de l’espace de travail `ws`. Ce magasin de données accède au partage de fichiers Azure `my_file_share` sur le compte de stockage Azure `my_storage_account` à l’aide de la clé de compte fournie.

    ```Python
       datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                      datastore_name='my_datastore', 
                                                      file_share_name='my_file_share',
                                                      account_name='my_storage account', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
    ```

####  <a name="storage-guidance"></a>Conseils liés au stockage

Nous vous recommandons d’utiliser le conteneur d’objets Blob Azure. Les stockages Standard et Premium sont tous deux disponibles pour les objets blob. Bien que plus cher, nous vous suggérons le stockage Premium pour ses vitesses de débit supérieures qui peuvent améliorer la vitesse de vos exécutions d’entraînement, en particulier si vous effectuez l’entraînement sur un jeu de données volumineux. Pour plus d’informations sur les coûts des comptes de stockage, consultez [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

### <a name="using-azure-machine-learning-studio"></a>Utilisation d’Azure Machine Learning Studio 

Créez un nouveau magasin de données en quelques étapes dans Azure Machine Learning Studio.

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com/).
1. Sélectionnez **Magasins de données** dans le volet gauche sous **Gérer**.
1. Sélectionnez **+ Nouveau magasin de données**.
1. Remplissez le formulaire du nouveau magasin de données. Le formulaire est mis à jour intelligemment en fonction du type de stockage Azure et des sélections relatives au type d’authentification.
  
Vous pouvez trouver les informations dont vous avez besoin pour remplir le formulaire sur le [portail Azure](https://portal.azure.com). Sélectionnez **Comptes de stockage** dans le volet gauche, puis choisissez le compte de stockage à inscrire. La page **Vue d’ensemble** fournit des informations telles que le nom du compte et le nom du conteneur ou du partage de fichiers. Pour obtenir des éléments d’authentification comme une clé de compte ou un jeton SAS, accédez à **Clés de compte** sous le volet **Paramètres** à gauche.

L’exemple suivant montre à quoi ressemble le formulaire pour la création d’un magasin de données d’objets blob Azure. 
    
 ![Nouveau magasin de données](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Récupérer les magasins de données à partir de votre espace de travail

Pour obtenir une banque de donnée spécifique inscrite dans l’espace de travail actuel, utilisez la méthode statique [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) sur la classe Magasin de données :

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Pour obtenir la liste des banques de données inscrites avec un espace de travail donné, vous pouvez utiliser la propriété [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) sur un objet d’espace de travail :

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Lorsque vous créez un espace de travail, un conteneur d’objets Blob Azure et un partage de fichiers Azure sont inscrits automatiquement dans les espaces de travail nommés `workspaceblobstore` et `workspacefilestore`, respectivement. Ceux-ci stockent les informations de connexion du conteneur d’objets Blob et du partage de fichiers qui sont provisionnés dans le compte de stockage associé à l’espace de travail. Le `workspaceblobstore` est défini comme le magasin de données par défaut.

Pour obtenir la banque de données par défaut de l’espace de travail :

```Python
datastore = ws.get_default_datastore()
```

Pour définir un magasin de données par défaut différent pour l’espace de travail actuel, utilisez la méthode [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) sur l’objet de l’espace de travail :

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Charger et télécharger des données
Les méthodes [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) et [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) décrites dans les exemples suivants sont spécifiques aux classes [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) et [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py), et fonctionnent de la même manière pour les deux.

### <a name="upload"></a>Télécharger

 Chargez un répertoire ou des fichiers individuels dans la banque de données avec le kit de développement logiciel (SDK) Python.

Pour charger un répertoire dans une banque de données `datastore` :

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

Le paramètre `target_path` spécifie l’emplacement dans le partage de fichiers (ou le conteneur d’objets blob) à charger. La valeur par défaut est `None`, auquel cas les données sont chargées à la racine. Sinon, quand `overwrite=True`, toutes les données existantes dans `target_path` sont remplacées.

Ou téléchargez une liste de fichiers individuels sur le magasin de donnés via la méthode `upload_files()`.

### <a name="download"></a>Téléchargement

De même, téléchargez des données d’une banque de données vers votre système de fichiers local.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Le paramètre `target_path` correspond à l’emplacement du répertoire local dans lequel les données doivent être téléchargées. Pour spécifier un chemin au dossier dans le partage de fichiers (ou le conteneur d’objets blob) où effectuer le téléchargement, fournissez ce chemin à `prefix`. Si `prefix` est `None`, tout le contenu de votre partage de fichiers (ou conteneur d’objets blob) est téléchargé.

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Accédez à vos données pendant la formation

> [!IMPORTANT]
> L’utilisation de [jeux de données Azure Machine Learning](how-to-create-register-datasets.md) est le nouveau moyen recommandé pour accéder à vos données pendant l’entraînement. Les jeux de données fournissent des fonctions qui chargent des données tabulaires dans le dataframe Pandas ou Spark, ainsi que la possibilité de télécharger ou de monter des fichiers de n’importe quel format à partir d’Azure BLOB, Azure File, Azure Data Lake GEN 1, Azure Data Lake Gen 2, Azure SQL et Azure PostgreSQL. Apprenez-en davantage sur la [façon de former des modèles avec des jeux de données](how-to-train-with-datasets.md).

Le tableau suivant liste les méthodes qui indiquent à la cible de calcul comment utiliser le magasin de données lors des exécutions. 

Moyen|Méthode|Description|
----|-----|--------
Monter| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Permet de monter le magasin de données sur la cible de calcul. Une fois le magasin monté, tous les fichiers de votre magasin de données sont rendus accessibles à votre cible de calcul.
Téléchargement|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Permet de télécharger le contenu de votre magasin de données à l’emplacement spécifié par `path_on_compute`. <br><br> Ce téléchargement se produit avant l’exécution.
Télécharger|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Permet de charger un fichier à partir de l’emplacement spécifié par `path_on_compute` sur votre magasin de données. <br><br> Ce chargement a lieu après votre exécution.

Pour référencer un dossier ou fichier spécifique dans votre magasin de données et le mettre à disposition sur la cible de calcul, utilisez la méthode [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) du magasin de données.

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of only the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Tout objet `datastore` ou `datastore.path` est résolu en un nom de variable d’environnement au format `"$AZUREML_DATAREFERENCE_XXXX"`, dont la valeur représente le chemin de montage/téléchargement sur le calcul cible. Le chemin du magasin de données sur le calcul cible peut ne pas être identique au chemin d’exécution du script d’entraînement.

### <a name="examples"></a>Exemples 

Nous vous recommandons d’utiliser la classe [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) pour accéder aux données pendant l’entraînement. 

La variable `script_params` est un dictionnaire contenant les paramètres du entry_script. Utilisez-le pour transmettre une banque de données et décrire comment les données doivent être rendues disponibles sur la cible de calcul. Pour en savoir plus, consultez notre [didacticiel](tutorial-train-models-with-aml.md) de bout en bout.

```Python
from azureml.train.estimator import Estimator

# notice '/' is in front, this indicates the absolute path
script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Vous pouvez également transmettre une liste de magasins de données au paramètre `inputs` du constructeur Estimator afin de monter ou de copier des données depuis/vers vos magasins de données. Cet exemple de code :
* télécharge tout le contenu du `datastore1` vers la cible de calcul avant l’exécution du script d’entraînement `train.py`.
* télécharge le dossier `'./foo'` dans `datastore2` vers la cible de calcul avant l’exécution de `train.py`.
* charge le fichier `'./bar.pkl'` à partir de la cible de calcul vers `datastore3` après l’exécution de votre script.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
Si vous préférez utiliser un objet RunConfig pour l’entraînement, vous devez configurer un objet [DataReference](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py). 

Le code suivant montre comment utiliser un objet DataReference dans un pipeline d’estimation. Pour obtenir un exemple complet, consultez ce [notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-estimatorstep.ipynb).

```Python
from azureml.core import Datastore
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

def_blob_store = Datastore(ws, "workspaceblobstore")

input_data = DataReference(
       datastore=def_blob_store,
       data_reference_name="input_data",
       path_on_datastore="20newsgroups/20news.pkl")

   output = PipelineData("output", datastore=def_blob_store)
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>Matrice de calcul et de magasin de données

Les magasins de données prennent actuellement en charge le stockage des informations de connexion dans les services de stockage figurant dans la matrice suivante. Cette matrice affiche les fonctionnalités d’accès aux données disponibles pour les différents scénarios de cibles de calcul et de magasins de données. Apprenez-en davantage sur les [cibles de calcul pour Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Calcul|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Local|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/A         |N/A                                                                         |
| Capacité de calcul Azure Machine Learning |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [pipelines&nbsp;ML](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [pipelines&nbsp;ML](concept-ml-pipelines.md)|N/A         |N/A                                                                         |
| Machines virtuelles               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| Transfert de données                  |[Pipelines&nbsp;ML](concept-ml-pipelines.md)                                               |N/A                                           |[Pipelines&nbsp;ML](concept-ml-pipelines.md)            |[Pipelines&nbsp;ML](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[Pipelines&nbsp;ML](concept-ml-pipelines.md)                                              |N/A                                           |[Pipelines&nbsp;ML](concept-ml-pipelines.md)             |N/A                                                                         |
| Azure Batch                    |[Pipelines&nbsp;ML](concept-ml-pipelines.md)                                               |N/A                                           |N/A         |N/A                                                                         |
| Azure Data Lake Analytics       |N/A                                           |N/A                                           |[Pipelines&nbsp;ML](concept-ml-pipelines.md)             |N/A                                                                         |

> [!NOTE]
> Il peut exister des scénarios dans lesquels des processus de données de grande taille hautement itératifs s’exécutent plus rapidement avec `as_download()` qu’avec `as_mount()` ; cela peut être validé de façon expérimentale.

### <a name="accessing-source-code-during-training"></a>Accès au code source pendant l’entraînement

Le stockage Blob Azure offre des vitesses de débit supérieures à celles du partage de fichiers Azure et s’adapte à un grand nombre de travaux démarrés en parallèle. C’est pourquoi nous vous recommandons de configurer vos exécutions pour utiliser le stockage d’objets blob pour le transfert des fichiers de code source.

L’exemple de code suivant spécifie dans la configuration d’exécution le magasin de données d’objets blob à utiliser pour les transferts de code source.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Accéder aux données pendant le scoring

Azure Machine Learning offre plusieurs moyens d’utiliser vos modèles pour le scoring. Certaines de ces méthodes ne fournissent pas d’accès aux magasins de données. Utilisez le tableau suivant pour comprendre les méthodes qui vous permettent d’accéder aux magasins de données pendant le scoring :

| Méthode | Accès aux magasins de données | Description |
| ----- | :-----: | ----- |
| [Prédiction par lots](how-to-run-batch-predictions.md) | ✔ | Effectuez des prédictions sur de grandes quantités de données de façon asynchrone. |
| [Service web](how-to-deploy-and-where.md) | &nbsp; | Déployez des modèles en tant que services web. |
| [Module IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Déployez des modèles en tant qu’appareils IoT Edge. |

Dans les situations où le SDK ne fournit pas d’accès aux banques de données, vous pouvez créer un code personnalisé à l’aide du SDK Azure approprié pour accéder aux données. Par exemple, le [kit de développement logiciel (SDK) de stockage Azure pour Python](https://github.com/Azure/azure-storage-python) est une bibliothèque cliente que vous pouvez utiliser pour accéder aux données stockées dans des objets blob ou des fichiers.

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>Déplacer des données vers des solutions de stockage Azure prises en charge

Azure Machine Learning prend en charge l’accès aux données à partir d’Azure Blob, Azure file, Azure Data Lake Gen 1, Azure Data Lake Gen 2, Azure SQL, Azure PostgreSQL. Pour le stockage non pris en charge, nous vous recommandons de déplacer vos données vers nos solutions de stockage Azure prises en charge à l’aide d’Azure Data Factory pour réduire le coût de sortie des données pendant les expériences de Machine Learning. Azure Data Factory offre un transfert de données efficace et résilient avec plus de 80 connecteurs préconfigurés, notamment les services de données Azure, les sources de données locales, Amazon S3, Redshift et Google BigQuery®, sans frais supplémentaires. [Suivez le guide pas à pas pour déplacer vos données à l’aide d’ Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Étapes suivantes

* [Effectuer l’apprentissage d’un modèle](how-to-train-ml-models.md).

* [Déployer un modèle](how-to-deploy-and-where.md).
