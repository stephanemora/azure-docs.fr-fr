---
title: Accéder aux données dans les services de stockage Azure
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser des magasins de données pour vous connecter en toute sécurité aux services de stockage Azure lors de l’entraînement avec Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 01/15/2020
ms.custom: seodec18
ms.openlocfilehash: f8bad8be3c02228fee9dded729e22c5a406da178
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76025043"
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
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Types de services de stockage de données pris en charge

Les magasins de données prennent actuellement en charge le stockage des informations de connexion dans les services de stockage figurant dans la matrice suivante.

| Type de&nbsp;stockage | Type&nbsp;d’authentification | [Azure&nbsp;Machine&nbsp;Learning studio](https://ml.azure.com/) | [SDK Azure&nbsp;Machine&nbsp;Learning&nbsp; Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [CLI Azure&nbsp;Machine&nbsp;Learning](reference-azure-machine-learning-cli.md) | [API REST Azure&nbsp;Machine&nbsp;Learning&nbsp;](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Stockage&nbsp;Blob&nbsp;Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Clé de compte <br> Jeton SAS | ✓ | ✓ | ✓ |✓
[Partage&nbsp;de fichiers&nbsp;Azure]((https://docs.microsoft.com/azure/storage/files/storage-files-introduction))| Clé de compte <br> Jeton SAS | ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;1](https://docs.microsoft.com/azure/data-lake-store/)| Principal du service| ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Principal du service| ✓ | ✓ | ✓ |✓
Azure&nbsp;SQL&nbsp;Database| Authentification SQL <br>Principal du service| ✓ | ✓ | ✓ |✓
Azure&nbsp;PostgreSQL | Authentification SQL| ✓ | ✓ | ✓ |✓
Azure&nbsp;Database&nbsp;pour&nbsp;MySQL | Authentification SQL|  | ✓ | ✓ |✓
Système&nbsp;de fichiers&nbsp;Databricks| Aucune authentification | | ✓* | ✓ * |✓* 

*pris en charge uniquement dans les scénarios de cible de calcul locale

### <a name="storage-guidance"></a>Conseils liés au stockage

Nous vous recommandons de créer un magasin de données pour un conteneur d’objets blob Azure.  
Les stockages Standard et Premium sont tous deux disponibles pour les objets blob. Le stockage Premium est plus cher, mais ses vitesses de débit supérieures vous feront gagner du temps sur vos exécutions d’entraînement, en particulier si vous effectuez l’entraînement sur un jeu de données volumineux. Pour plus d’informations sur le coût des comptes de stockage, consultez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

Quand vous créez un espace de travail, un conteneur d’objets blob Azure et un partage de fichiers Azure sont inscrits automatiquement dans l’espace de travail, sous les noms `workspaceblobstore` et `workspacefilestore` respectivement. Ils stockent les informations de connexion pour le conteneur d’objets blob et le partage de fichiers qui sont provisionnés dans le compte de stockage attaché à l’espace de travail. Le conteneur `workspaceblobstore` est défini comme magasin de données par défaut.

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Créer et inscrire des magasins de données

Quand vous inscrivez une solution de stockage Azure en tant que magasin de données, ce dernier est automatiquement créé et inscrit dans un espace de travail spécifique. Vous pouvez créer des magasins de données et les inscrire auprès d’un espace de travail en utilisant le SDK Python ou Azure Machine Learning Studio.

>[!IMPORTANT]
> Dans le cadre du processus de création et d’inscription du magasin de données actuel, Azure Machine Learning valide que le principal fourni par l’utilisateur (nom d’utilisateur, principal du service ou jeton SAS) a accès au service de stockage sous-jacent. 
<br>
Toutefois, pour les magasins de données Azure Data Lake Storage Gen 1 et 2, cette validation se produit plus tard quand des méthodes d’accès aux données comme [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) ou [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) sont appelées. 

### <a name="python-sdk"></a>Kit de développement logiciel (SDK) Python

Toutes les méthodes d’inscription se trouvent dans la classe [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) et ont le format `register_azure_*`.

Vous pouvez trouver les informations dont vous avez besoin pour renseigner la méthode `register()` sur le [portail Azure](https://portal.azure.com) :

1. Sélectionnez **Comptes de stockage** dans le volet gauche, puis choisissez le compte de stockage que vous voulez inscrire. 
2. Pour obtenir des informations telles que le nom du compte, le conteneur et le nom du partage de fichiers, accédez à la page **Vue d’ensemble**. Pour obtenir des informations d’authentification comme la clé de compte ou le jeton SAS, accédez à **Clés d’accès** dans le volet **Paramètres**. 

> [!IMPORTANT]
> Si votre compte de stockage appartient à un réseau virtuel, seule la création d’un magasin de données d’objets blob Azure est prise en charge. Pour accorder à votre espace de travail l’accès à votre compte de stockage, définissez le paramètre `grant_workspace_access` sur `True`.

Les exemples suivants montrent comment inscrire un conteneur d’objets blob Azure, un partage de fichiers Azure et Azure Data Lake Storage Gen 2 comme magasin de données. Pour les autres services de stockage, consultez la [documentation de référence des méthodes `register_azure_*`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

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

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Gen 2

Pour un magasin de données Azure Data Lake Storage Gen 2 (ADLS Gen 2), utilisez [register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) pour inscrire un magasin de données d’informations d’identification connecté à un stockage Azure DataLake Gen 2 avec des autorisations de principal du service. Découvrez-en plus sur la [configuration du contrôle d’accès pour ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Le code suivant crée le magasin de données `adlsgen2_datastore_name` et l’inscrit auprès de l’espace de travail `ws`. Ce magasin de données accède au système de fichiers `test` sur le compte de stockage `account_name`, à l’aide des informations d’identification du principal du service fournies.

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```

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

Pour interagir avec les données de vos magasins de données ou pour empaqueter vos données dans un objet consommable pour les tâches de Machine Learning, comme l’entraînement, [créez un jeu de données Azure Machine Learning](how-to-create-register-datasets.md). Les jeux de données fournissent des fonctions qui chargent des données tabulaires dans un dataframe Pandas ou Spark. Ils offrent également la possibilité de télécharger ou monter des fichiers de n’importe quel format à partir des stockages suivants : Stockage Blob Azure, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database et Azure Database pour PostgreSQL. [Découvrez-en plus sur l’entraînement avec des jeux de données](how-to-train-with-datasets.md).

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

Azure Machine Learning prend en charge l’accès aux données à partir des stockages suivants : Stockage Blob Azure, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database et Azure Database pour PostgreSQL. Si vous utilisez un stockage non pris en charge, nous vous recommandons de déplacer vos données vers des solutions de stockage Azure prises en charge en utilisant [Azure Data Factory et ces étapes]((https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool)). Déplacer vos données vers un stockage pris en charge peut vous aider à réduire les coûts de sortie des données pendant les expériences Machine Learning. 

Azure Data Factory fournit un moyen de transfert des données efficace et résilient avec plus de 80 connecteurs prédéfinis, sans coût supplémentaire. Ces connecteurs incluent les services de données Azure, les sources de données locales, Amazon S3 et Redshift, et Google BigQuery.

## <a name="next-steps"></a>Étapes suivantes

* [Créer un jeu de données Azure Machine Learning](how-to-create-register-datasets.md)
* [Entraîner un modèle](how-to-train-ml-models.md)
* [Déployer un modèle](how-to-deploy-and-where.md)
