---
title: Accéder aux données dans les services de stockage Azure
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser des magasins de données pour accéder aux services de stockage Azure lors de la formation avec Azure Machine Learning service
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 08/2/2019
ms.custom: seodec18
ms.openlocfilehash: 545860a394c7eac953c1cbacc9dd05fc3737f6c1
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856169"
---
# <a name="access-data-in-azure-storage-services"></a>Accéder aux données dans les services de stockage Azure

 Dans cet article, découvrez comment accéder facilement à vos données dans les services de stockage Azure via des magasins de données Azure Machine Learning. Les magasins de données sont utilisés pour stocker les informations de connexion, comme votre ID d’abonnement et votre autorisation de jeton, pour accéder à votre stockage sans avoir à coder en dur ces informations dans vos scripts.

Cette procédure montre des exemples des tâches suivantes :
* [Enregistrer les magasins de données](#access)
* [Récupérer les magasins de données de l’espace de travail](#get)
* [Charger et télécharger des données à l’aide de magasins de données](#up-and-down)
* [Accéder aux données pendant l’entraînement](#train)

## <a name="prerequisites"></a>Prérequis

Pour utiliser des banques de données, vous devez tout d’abord disposer d’un [espace de travail](concept-workspace.md).

Commencez soit par [créer un espace de travail](how-to-manage-workspace.md), soit par en récupérer un existant :

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="register-datastores"></a>Enregistrer les magasins de données

Toutes les méthodes d’inscription sont sur la classe [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) et ont la forme register_azure_*.

Les exemples suivants vous montrent comment inscrire un conteneur d’objets blob Azure ou un partage de fichiers Azure comme magasin de données.

+ Pour un **magasin de données de conteneur d’objets blob Azure**, utilisez [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                      datastore_name='your datastore name', 
                                                      container_name='your azure blob container name',
                                                      account_name='your storage account name', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
  ```

+ Pour un **magasin de données de partage de fichiers Azure**, utilisez [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Par exemple : 
  ```Python
  datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                  datastore_name='your datastore name', 
                                                  file_share_name='your file share name',
                                                  account_name='your storage account name', 
                                                  account_key='your storage account key',
                                                  create_if_not_exists=True)
  ```

####  <a name="storage-guidance"></a>Conseils liés au stockage

Nous vous recommandons d’utiliser le conteneur d’objets Blob Azure. Les stockages Standard et Premium sont tous deux disponibles pour les objets blob. Bien que plus cher, nous vous suggérons le stockage Premium pour ses vitesses de débit supérieures qui peuvent améliorer la vitesse de vos exécutions d’entraînement, en particulier si vous effectuez l’entraînement sur un jeu de données volumineux. Pour plus d’informations sur les coûts des comptes de stockage, consultez [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Récupérer les magasins de données à partir de votre espace de travail

Pour obtenir une banque de donnée spécifique inscrite dans l’espace de travail actuel, utilisez la méthode statique [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) sur la classe Magasin de données :

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Pour obtenir la liste des banques de données inscrites avec un espace de travail donné, vous pouvez utiliser la propriété `datastores` sur un objet d’espace de travail :

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Lorsque vous créez un espace de travail, un conteneur d’objets Blob Azure et un partage de fichiers Azure sont inscrits dans l’espace de travail nommé `workspaceblobstore` et `workspacefilestore`, respectivement. Elles stockent les informations de connexion du conteneur d’objets Blob et du partage de fichiers qui sont approvisionnés dans le compte de stockage associé à l’espace de travail. Le `workspaceblobstore` est défini comme le magasin de données par défaut.

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
Les méthodes [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) et [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) décrites dans les exemples suivants sont spécifiques aux classes [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) et [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py), et fonctionnent de la même manière pour les deux.

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

Le paramètre `target_path` spécifie l’emplacement dans le partage de fichiers (ou le conteneur d’objets blob) à charger. La valeur par défaut est `None`, auquel cas les données sont chargées à la racine. Quand `overwrite=True` toutes les données existantes dans `target_path` sont remplacées.

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

Pour accéder aux données lors de la formation, vous pouvez télécharger ou monter vos données à partir de vos services de stockage Azure vers la cible de calcul via des magasins de données.

Le tableau suivant liste les méthodes qui indiquent à la cible de calcul comment utiliser le magasin de données lors des exécutions. 

Moyen|Méthode|Description|
----|-----|--------
Monter| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Permet de monter le magasin de données sur la cible de calcul.
Téléchargement|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Permet de télécharger le contenu de votre magasin de données à l’emplacement spécifié par `path_on_compute`. <br> Ce téléchargement se produit avant l’exécution.
Télécharger|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Permet de charger un fichier à partir de l’emplacement spécifié par `path_on_compute` sur votre magasin de données. <br> Ce chargement a lieu après votre exécution.

Pour référencer un dossier ou fichier spécifique dans votre magasin de données et le mettre à disposition sur la cible de calcul, utilisez la méthode [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) du magasin de données.

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Tout objet `datastore` ou `datastore.path` est résolu en un nom de variable d’environnement au format `"$AZUREML_DATAREFERENCE_XXXX"`, dont la valeur représente le chemin de montage/téléchargement sur le calcul cible. Le chemin du magasin de données sur le calcul cible peut ne pas être identique au chemin d’exécution du script d’entraînement.

### <a name="examples"></a>Exemples 

Les exemples de code suivants sont spécifiques à la classe [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) pour accéder aux données pendant la formation. 

`script_params` est un dictionnaire contenant les paramètres du entry_script. Vous pouvez l’utiliser pour transmettre un magasin de données et décrire comment les données doivent être rendues disponibles sur la cible de calcul. Pour en savoir plus, consultez notre [didacticiel](tutorial-train-models-with-aml.md) de bout en bout.

```Python
from azureml.train.estimator import Estimator

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

## <a name="access-data-during-scoring"></a>Accéder aux données pendant le scoring

Azure Machine Learning service offre plusieurs moyens d’utiliser vos modèles pour le scoring. Certaines de ces méthodes ne fournissent pas d’accès aux magasins de données. Utilisez le tableau suivant pour comprendre les méthodes qui vous permettent d’accéder aux magasins de données pendant le scoring :

| Méthode | Accès aux magasins de données | Description |
| ----- | :-----: | ----- |
| [Prédiction par lots](how-to-run-batch-predictions.md) | ✔ | Effectuez des prédictions sur de grandes quantités de données de façon asynchrone. |
| [Service web](how-to-deploy-and-where.md) | &nbsp; | Déployez des modèles en tant que services web. |
| [Module IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Déployez des modèles en tant qu’appareils IoT Edge. |

Dans les situations où le SDK ne fournit pas d’accès aux magasins de données, vous pouvez créer un code personnalisé à l’aide du SDK Azure approprié pour accéder aux données. Par exemple, en utilisant le [SDK Stockage Azure pour Python](https://github.com/Azure/azure-storage-python) pour accéder aux données stockées dans des blobs.


## <a name="next-steps"></a>Étapes suivantes

* [Entraîner un modèle](how-to-train-ml-models.md)

* [Déployer un modèle](how-to-deploy-and-where.md)
