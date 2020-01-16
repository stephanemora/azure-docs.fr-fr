---
title: Accéder aux données dans les services de stockage Azure
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser des magasins de données pour vous connecter en toute sécurité aux services de stockage Azure lors de l’entraînement avec Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: ylxiong
author: YLXiong1125
ms.reviewer: nibaccam
ms.date: 12/10/2019
ms.custom: seodec18
ms.openlocfilehash: ac6ef6341013ca13d5a9f27be8897365c1c2155d
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75535449"
---
# <a name="access-data-in-azure-storage-services"></a>Accéder aux données dans les services de stockage Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, découvrez comment accéder facilement à vos données dans les services de stockage Azure par le biais des magasins de données Azure Machine Learning. Les banques de données permettent de stocker les informations de connexion, comme votre ID d’abonnement et votre autorisation de jeton. En utilisant des magasins de données, vous pouvez accéder à votre stockage sans avoir à coder en dur les informations de connexion dans vos scripts. 

Vous pouvez créer des magasins de données à partir de ces [solutions de stockage Azure](#matrix). Pour les solutions de stockage non prises en charge, nous vous recommandons de [déplacer vos données](#move) vers nos solutions de stockage Azure prises en charge afin de réduire le coût de sortie des données pendant les expériences de Machine Learning. 

## <a name="prerequisites"></a>Conditions préalables requises
Vous devez disposer des éléments suivants :
- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree).

- Un compte de stockage Azure avec un [conteneur d’objets blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) ou un [partage de fichiers Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- Le [Kit de développement logiciel (SDK) Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou l’accès à [Azure Machine Learning studio](https://ml.azure.com/).

- Un espace de travail Azure Machine Learning.
  
  [Créez un espace de travail Azure Machine Learning](how-to-manage-workspace.md) ou utilisez un espace de travail existant avec le SDK Python :

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Créer et inscrire des magasins de données

Quand vous inscrivez une solution de stockage Azure comme magasin de données, ce dernier est automatiquement créé dans un espace de travail spécifique. Vous pouvez créer des magasins de données et les inscrire auprès d’un espace de travail en utilisant le SDK Python ou Azure Machine Learning Studio.

### <a name="python-sdk"></a>Kit de développement logiciel (SDK) Python

Toutes les méthodes d’inscription se trouvent dans la classe [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) et ont le format `register_azure_*`.

Vous pouvez trouver les informations dont vous avez besoin pour renseigner la méthode `register()` sur le [portail Azure](https://portal.azure.com) :

1. Sélectionnez **Comptes de stockage** dans le volet gauche, puis choisissez le compte de stockage que vous voulez inscrire. 
2. Pour obtenir des informations telles que le nom du compte, le conteneur et le nom du partage de fichiers, accédez à la page **Vue d’ensemble**. Pour obtenir des informations d’authentification comme la clé de compte ou le jeton SAS, accédez à **Clés d’accès** dans le volet **Paramètres**. 

> [!IMPORTANT]
> Si votre compte de stockage appartient à un réseau virtuel, seule la création d’un magasin de données d’objets blob Azure est prise en charge. Pour accorder à votre espace de travail l’accès à votre compte de stockage, définissez le paramètre `grant_workspace_access` sur `True`.

Les exemples suivants montrent comment inscrire un conteneur d’objets blob Azure, un partage de fichiers Azure et des données Azure SQL comme magasin de données.

#### <a name="blob-container"></a>Conteneur d’objets blob

Pour inscrire un conteneur d’objets blob Azure comme magasin de données, utilisez [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

Le code suivant crée le magasin de données `blob_datastore_name` et l’inscrit auprès de l’espace de travail `ws`. Ce magasin de données accède au conteneur d’objets blob `my-container-name` sur le compte de stockage `my-account-name` avec la clé de compte fournie.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

#### <a name="file-share"></a>Partage de fichiers

Pour inscrire un partage de fichiers Azure comme magasin de données, utilisez [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

Le code suivant crée le magasin de données `file_datastore_name` et l’inscrit auprès de l’espace de travail `ws`. Ce magasin de données accède au partage de fichiers `my-fileshare-name` sur le compte de stockage `my-account-name` avec la clé de compte fournie.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                 datastore_name=file_datastore_name, 
                                                 file_share_name=file_share_name, 
                                                 account_name=account_name,
                                                 account_key=account_key)
```

#### <a name="sql-data"></a>Données SQL

Pour un magasin de données Azure SQL, utilisez [register_azure_sql_database()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-sql-database-workspace--datastore-name--server-name--database-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--endpoint-none--overwrite-false--username-none--password-none-) si vous souhaitez inscrire un magasin de données d’informations d’identification connecté à une base de données Azure SQL avec l’authentification SQL ou les autorisations d’un principal de service. 

Pour l’inscription par le biais de l’authentification SQL :

```python
sql_datastore_name="azsqlsdksql"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the Azure SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the Azure SQL database
username=os.getenv("SQL_USER_NAME", "<my-sql-user-name>") # Username of the database user to access the database
password=os.getenv("SQL_USER_PASSWORD", "<my-sql-user-password>") # Password of the database user to access the database

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                  datastore_name=sql_datastore_name,
                                                  server_name=server_name,
                                                  database_name=database_name,
                                                  username=username,
                                                  password=password)

```

Pour l’inscription par le biais d’un principal de service :

```python 
sql_datastore_name="azsqlsdksp"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the SQL database
client_id=os.getenv("SQL_CLIENTNAME", "<my-client-id>") # Client ID of the service principal with permissions to access the database
client_secret=os.getenv("SQL_CLIENTSECRET", "<my-client-secret>") # Secret of the service principal
tenant_id=os.getenv("SQL_TENANTID", "<my-tenant-id>") # Tenant ID of the service principal

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                      datastore_name=sql_datastore_name,
                                                      server_name=server_name,
                                                      database_name=database_name,
                                                      client_id=client_id,
                                                      client_secret=client_secret,
                                                      tenant_id=tenant_id)
```

#### <a name="storage-guidance"></a>Conseils liés au stockage

Nous vous recommandons d’utiliser un conteneur d’objets blob Azure. Les stockages Standard et Premium sont tous deux disponibles pour les objets blob. Le stockage Premium est plus cher, mais ses vitesses de débit supérieures vous feront gagner du temps sur vos exécutions d’entraînement, en particulier si vous effectuez l’entraînement sur un jeu de données volumineux. Pour plus d’informations sur le coût des comptes de stockage, consultez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio 

Créez un magasin de données en quelques étapes dans Azure Machine Learning Studio :

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com/).
1. Sélectionnez **Magasins de données** dans le volet gauche sous **Gérer**.
1. Sélectionnez **+ Nouveau magasin de données**.
1. Remplissez le formulaire de création d’un magasin de données. Le formulaire est mis à jour intelligemment en fonction du type de stockage Azure et du type d’authentification que vous sélectionnez.
  
Vous pouvez trouver les informations dont vous avez besoin pour renseigner le formulaire sur le [portail Azure](https://portal.azure.com). Sélectionnez **Comptes de stockage** dans le volet gauche, puis choisissez le compte de stockage que vous voulez inscrire. La page **Vue d’ensemble** fournit des informations telles que le nom du compte, le conteneur et le nom du partage de fichiers. Pour obtenir des informations d’authentification comme la clé de compte ou le jeton SAS, accédez à **Clés de compte** dans le volet **Paramètres**.

L’exemple suivant montre à quoi ressemble le formulaire quand vous créez un magasin de données d’objets blob Azure : 
    
![Formulaire de création d’un magasin de données](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Récupérer les magasins de données à partir de votre espace de travail

Pour obtenir un magasin de données spécifique inscrit dans l’espace de travail actif, utilisez la méthode statique [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) sur la classe `Datastore` :

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Pour obtenir la liste des banques de données inscrites avec un espace de travail donné, vous pouvez utiliser la propriété [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) sur un objet d’espace de travail :

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Quand vous créez un espace de travail, un conteneur d’objets blob Azure et un partage de fichiers Azure sont inscrits automatiquement dans l’espace de travail, sous les noms `workspaceblobstore` et `workspacefilestore` respectivement. Ils stockent les informations de connexion pour le conteneur d’objets blob et le partage de fichiers qui sont provisionnés dans le compte de stockage attaché à l’espace de travail. Le conteneur `workspaceblobstore` est défini comme magasin de données par défaut.

Pour obtenir le magasin de données par défaut de l’espace de travail, utilisez cette ligne :

```Python
datastore = ws.get_default_datastore()
```

Pour changer de magasin de données par défaut pour l’espace de travail actif, utilisez la méthode [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) sur l’espace de travail :

```Python
# Define the default datastore for the current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Charger et télécharger des données

Les méthodes [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) et [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) décrites dans les exemples suivants sont propres aux classes [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) et [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py), et fonctionnent de la même manière pour les deux.

### <a name="upload"></a>Télécharger

Chargez un répertoire ou des fichiers individuels dans le magasin de données à l’aide du SDK Python :

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

Le paramètre `target_path` spécifie l’emplacement dans le partage de fichiers (ou le conteneur d’objets blob) à charger. La valeur par défaut est `None` ; les données sont donc chargées à la racine. Quand `overwrite=True` est défini, toutes les données existantes dans `target_path` sont remplacées.

Vous pouvez aussi charger une liste de fichiers individuels dans le magasin de donnés avec la méthode `upload_files()`.

### <a name="download"></a>Téléchargement

Téléchargez des données d’un magasin de données vers votre système de fichiers local :

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Le paramètre `target_path` correspond à l’emplacement du répertoire local dans lequel les données doivent être téléchargées. Pour spécifier un chemin au dossier dans le partage de fichiers (ou le conteneur d’objets blob) où effectuer le téléchargement, fournissez ce chemin à `prefix`. Si `prefix` est défini à `None`, tout le contenu de votre partage de fichiers (ou conteneur d’objets blob) est téléchargé.

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Accédez à vos données pendant la formation

> [!IMPORTANT]
> Nous vous recommandons d’utiliser des [jeux de données Azure Machine Learning](how-to-create-register-datasets.md) pour accéder à vos données pendant l’entraînement. Les jeux de données fournissent des fonctions qui chargent des données tabulaires dans un dataframe Pandas ou Spark. Ils offrent également la possibilité de télécharger ou monter des fichiers de n’importe quel format à partir des stockages suivants : Stockage Blob Azure, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database et Azure Database pour PostgreSQL. [Découvrez-en plus sur l’entraînement avec des jeux de données](how-to-train-with-datasets.md).

Le tableau suivant liste les méthodes qui indiquent à la cible de calcul comment utiliser les magasins de données lors des exécutions : 

Moyen|Méthode|Description|
----|-----|--------
Monter| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Permet de monter le magasin de données sur la cible de calcul. Une fois le magasin de données monté, tous les fichiers qu’il contient sont rendus accessibles à votre cible de calcul.
Téléchargement|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Permet de télécharger le contenu de votre magasin de données à l’emplacement spécifié par `path_on_compute`. <br><br> Ce téléchargement se produit avant l’exécution.
Télécharger|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Permet de charger un fichier à partir de l’emplacement spécifié par `path_on_compute` sur votre magasin de données. <br><br> Ce chargement a lieu après l’exécution.

Pour référencer un dossier ou fichier spécifique dans votre magasin de données et le mettre à disposition sur la cible de calcul, utilisez la méthode [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) du magasin de données :

```Python
# To mount the full contents in your storage to the compute target
datastore.as_mount()

# To download the contents of only the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Tout objet `datastore` ou `datastore.path` spécifié correspond à un nom de variable d’environnement au format `"$AZUREML_DATAREFERENCE_XXXX"`. La valeur de ce nom représente le chemin de montage/téléchargement sur la cible de calcul. Le chemin du magasin de données sur la cible de calcul peut être différent du chemin d’exécution du script d’entraînement.

### <a name="examples"></a>Exemples 

Nous vous recommandons d’utiliser la classe [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) pour accéder aux données pendant l’entraînement. 

La variable `script_params` est un dictionnaire qui contient des paramètres pour `entry_script`. Utilisez-le pour transmettre une banque de données et décrire comment les données doivent être rendues disponibles sur la cible de calcul. Pour en savoir plus, consultez le [tutoriel de bout en bout](tutorial-train-models-with-aml.md).

```Python
from azureml.train.estimator import Estimator

# Notice that '/' is in front, which indicates the absolute path
script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Vous pouvez également passer une liste de magasins de données au paramètre `inputs` du constructeur `Estimator` afin de monter ou copier des données depuis/vers votre magasin de données. Cet exemple de code :
* Télécharge tout le contenu de `datastore1` vers la cible de calcul avant l’exécution du script d’entraînement `train.py`.
* Télécharge le dossier `'./foo'` dans `datastore2` vers la cible de calcul avant l’exécution de `train.py`.
* Charge le fichier `'./bar.pkl'` à partir de la cible de calcul vers `datastore3` après l’exécution du script.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
Si vous préférez utiliser un objet `RunConfig` pour l’entraînement, vous devez configurer un objet [`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py). 

Le code suivant montre comment utiliser un objet `DataReference` dans un pipeline d’estimation. Pour obtenir un exemple complet, consultez [ce notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-estimatorstep.ipynb).

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

Les magasins de données prennent actuellement en charge le stockage des informations de connexion dans les services de stockage figurant dans la matrice suivante. Cette matrice affiche les fonctionnalités d’accès aux données disponibles pour les différents scénarios de cibles de calcul et de magasins de données. [Apprenez-en davantage sur les cibles de calcul pour Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Calcul|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Local|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/A         |N/A                                                                         |
| Capacité de calcul Azure Machine Learning |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [Pipelines Machine Learning](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [Pipelines Machine Learning](concept-ml-pipelines.md)|N/A         |N/A                                                                         |
| Machines virtuelles               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| Azure HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| Transfert de données                  |[Pipelines Machine Learning](concept-ml-pipelines.md)                                               |N/A                                           |[Pipelines Machine Learning](concept-ml-pipelines.md)            |[Pipelines Machine Learning](concept-ml-pipelines.md)                                                                            |
| Azure Databricks                     |[Pipelines Machine Learning](concept-ml-pipelines.md)                                              |N/A                                           |[Pipelines Machine Learning](concept-ml-pipelines.md)             |N/A                                                                         |
| Azure Batch                    |[Pipelines Machine Learning](concept-ml-pipelines.md)                                               |N/A                                           |N/A         |N/A                                                                         |
| Service Analytique Azure Data Lake       |N/A                                           |N/A                                           |[Pipelines Machine Learning](concept-ml-pipelines.md)             |N/A                                                                         |

> [!NOTE]
> Dans certains scénarios, les processus de données de grande taille et hautement itératifs s’exécutent plus rapidement avec `as_download()` qu’avec `as_mount()`. Vous pouvez le vérifier de façon expérimentale.

### <a name="accessing-source-code-during-training"></a>Accès au code source pendant l’entraînement

Le stockage Blob Azure offre des vitesses de débit supérieures à celles du partage de fichiers Azure et s’adapte à un grand nombre de travaux démarrés en parallèle. C’est pourquoi nous vous recommandons de configurer vos exécutions pour utiliser le stockage Blob pour le transfert des fichiers de code source.

L’exemple de code suivant spécifie dans la configuration d’exécution le magasin de données d’objets blob à utiliser pour les transferts de code source :

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Accéder aux données pendant le scoring

Azure Machine Learning offre plusieurs moyens d’utiliser vos modèles pour le scoring. Certaines de ces méthodes ne fournissent pas d’accès aux magasins de données. Utilisez le tableau suivant pour comprendre les méthodes qui vous permettent d’accéder aux magasins de données pendant le scoring :

| Méthode | Accès aux magasins de données | Description |
| ----- | :-----: | ----- |
| [Prédiction par lots](how-to-run-batch-predictions.md) | ✔ | Effectuez des prédictions sur de grandes quantités de données de façon asynchrone. |
| [Service web](how-to-deploy-and-where.md) | &nbsp; | Déployez des modèles comme un service web. |
| [Module Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Déployez des modèles sur des appareils IoT Edge. |

Dans les situations où le SDK ne fournit pas d’accès aux magasins de données, vous pouvez créer du code personnalisé à l’aide du SDK Azure approprié pour accéder aux données. Par exemple, le [kit de développement logiciel (SDK) de stockage Azure pour Python](https://github.com/Azure/azure-storage-python) est une bibliothèque cliente que vous pouvez utiliser pour accéder aux données stockées dans des objets blob ou des fichiers.

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>Déplacer des données vers des solutions de stockage Azure prises en charge

Azure Machine Learning prend en charge l’accès aux données à partir des stockages suivants : Stockage Blob Azure, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database et Azure Database pour PostgreSQL. Si vous utilisez un stockage non pris en charge, nous vous recommandons de déplacer vos données vers des solutions de stockage Azure prises en charge à l’aide d’Azure Data Factory. Déplacer vos données vers un stockage pris en charge peut vous aider à réduire les coûts de sortie des données pendant les expériences Machine Learning. 

Azure Data Factory fournit un moyen de transfert des données efficace et résilient avec plus de 80 connecteurs prédéfinis, sans coût supplémentaire. Ces connecteurs incluent les services de données Azure, les sources de données locales, Amazon S3 et Redshift, et Google BigQuery. [Suivez le guide pas à pas pour déplacer vos données à l’aide d’Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Étapes suivantes

* [Entraîner un modèle](how-to-train-ml-models.md)
* [Déployer un modèle](how-to-deploy-and-where.md)
