---
title: Accéder aux données contenues dans des magasins de données / blobs à des fins de formation
titleSuffix: Azure Machine Learning service
description: Apprendre à utiliser des magasins de données pour accéder au stockage de données blob pendant la formation avec Azure Machine Learning service
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 05/24/2019
ms.custom: seodec18
ms.openlocfilehash: 97a4bc20394553b97211763cedaa76c3711306f2
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68319328"
---
# <a name="access-data-from-your-datastores"></a>Accéder aux données depuis vos magasins de données

 Dans le service Azure Machine Learning, les magasins de données sont des mécanismes de calcul indépendants de l’emplacement qui permettent d’accéder au stockage sans changer le code source. Que vous écriviez du code d’entraînement pour prendre un chemin comme paramètre ou que vous fournissiez un magasin de données directement à un estimateur, les workflows Azure Machine Learning assurent l’accessibilité de vos emplacements de magasin de données et leur disponibilité dans votre contexte de calcul.

Cette procédure montre des exemples des tâches suivantes :
* [Choisir un magasin de données](#access)
* [Obtenir des données](#get)
* [Charger et télécharger des données sur les magasins de données](#up-and-down)
* [Accéder au magasin de données pendant l’entraînement](#train)

## <a name="prerequisites"></a>Prérequis

Pour utiliser des banques de données, vous devez tout d’abord disposer d’un [espace de travail](concept-workspace.md).

Commencez soit par [créer un espace de travail](setup-create-workspace.md#sdk), soit par en récupérer un existant :

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>Choisir un magasin de données

Vous pouvez utiliser le magasin de données par défaut ou apporter le vôtre.

### <a name="use-the-default-datastore-in-your-workspace"></a>Utiliser le magasin de données par défaut dans votre espace de travail

 Chaque espace de travail dispose d’un magasin de données par défaut inscrit que vous pouvez utiliser immédiatement.

Pour obtenir la banque de données par défaut de l’espace de travail :

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>Inscrire votre propre magasin de données auprès de l’espace de travail

Si vous avez du stockage Azure, vous pouvez l’inscrire en tant que banque de données sur votre espace de travail. 

<a name="store"></a>

####  <a name="storage-guidance"></a>Conseils liés au stockage

Nous recommandons le stockage Blob et des magasins de données d’objets blob. Les stockages Standard et Premium sont tous deux disponibles pour les objets blob. Bien que plus cher, nous vous suggérons le stockage Premium pour ses vitesses de débit supérieures qui peuvent améliorer la vitesse de vos exécutions d’entraînement, en particulier si vous effectuez l’entraînement sur un jeu de données volumineux. Pour plus d’informations sur les coûts des comptes de stockage, consultez [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

>[!NOTE]
> Le service Azure Machine Learning prend en charge d’autres types de magasins de données, ce qui peut être utile pour des scénarios spécifiques. Par exemple, si vous devez effectuer l’entraînement en utilisant des données stockées dans une base de données, vous pouvez utiliser AzureSQLDatabaseDatastore ou AzurePostgreSqlDatastore. Consultez [ce tableau](#matrix) pour connaître les types de magasins de données disponibles.

#### <a name="register-your-datastore"></a>Inscrire votre magasin de données
Toutes les méthodes d’inscription sont sur la classe [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) et ont la forme register_azure_*.

Les exemples suivants vous montrent comment inscrire un conteneur d’objets blob Azure ou un partage de fichiers Azure comme magasin de données.

+ Pour un **magasin de données de conteneur d’objets blob Azure**, utilisez [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ Pour un **magasin de données de partage de fichiers Azure**, utilisez [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Par exemple : 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           file_share_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>Rechercher et définir des magasins de données

Pour obtenir un magasin de données spécifié inscrit dans l’espace de travail actuel, utilisez [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) :

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Pour obtenir une liste de tous les magasins de données dans un espace de travail donné, utilisez ce code :

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

Pour définir un magasin de données par défaut différent pour l’espace de travail actuel, utilisez [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) :

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Charger et télécharger des données
Les méthodes [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) et [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) décrites dans les exemples suivants sont spécifiques aux classes [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) et [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py), et fonctionnent de la même manière pour les deux.

### <a name="upload"></a>Télécharger

 Chargez un répertoire ou des fichiers individuels dans la banque de données avec le kit de développement logiciel (SDK) Python.

Pour charger un répertoire dans une banque de données `ds` :

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```

`target_path` spécifie l’emplacement dans le partage de fichiers (ou le conteneur d’objets blob) à charger. La valeur par défaut est `None`, auquel cas les données sont chargées à la racine. `overwrite=True` remplace toutes les données existantes à `target_path`.

Ou chargez une liste de fichiers individuels dans la banque de données par le biais de la méthode `upload_files()` de la banque de données.

### <a name="download"></a>Téléchargement
De même, téléchargez des données d’une banque de données vers votre système de fichiers local.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```

`target_path` est l’emplacement du répertoire local dans lequel télécharger les données. Pour spécifier un chemin au dossier dans le partage de fichiers (ou le conteneur d’objets blob) où effectuer le téléchargement, fournissez ce chemin à `prefix`. Si `prefix` est `None`, tout le contenu de votre partage de fichiers (ou conteneur d’objets blob) est téléchargé.

<a name="train"></a>
## <a name="access-datastores-during-training"></a>Accéder aux magasins de données pendant l’entraînement

Une fois que vous avez mis votre magasin de données à disposition sur la cible de calcul d’entraînement, vous pouvez y accéder lors des exécutions d’entraînement (par exemple, des données pour l’entraînement ou la validation) en lui passant simplement le chemin en tant que paramètre dans votre script d’entraînement.

Le tableau suivant liste les méthodes [`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) qui indiquent à la cible de calcul comment utiliser le magasin de données lors des exécutions.

Moyen|Méthode|Description|
----|-----|--------
Monter| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Permet de monter le magasin de données sur la cible de calcul.
Téléchargement|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Permet de télécharger le contenu de votre magasin de données à l’emplacement spécifié par `path_on_compute`. <br> Pour le contexte de l’exécution d’entraînement, ce téléchargement se produit avant l’exécution.
Télécharger|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Permet de charger un fichier à partir de l’emplacement spécifié par `path_on_compute` sur votre magasin de données. <br> Pour le contexte de l’exécution d’entraînement, ce chargement se produit après l’exécution.

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Pour référencer un dossier ou fichier spécifique dans votre magasin de données et le mettre à disposition sur la cible de calcul, utilisez la fonction [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) du magasin de données.

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> Tout objet `ds` ou `ds.path` est résolu en un nom de variable d’environnement au format `"$AZUREML_DATAREFERENCE_XXXX"`, dont la valeur représente le chemin de montage/téléchargement sur le calcul cible. Le chemin du magasin de données sur le calcul cible peut ne pas être identique au chemin d’exécution du script d’entraînement.

<a name="matrix"></a>
### <a name="training-compute-and-datastore-matrix"></a>Matrice de magasin de données et calcul d’entraînement

La matrice suivante affiche les fonctionnalités d’accès aux données disponibles pour les différents scénarios de magasin de données et cibles de calcul d’entraînement. Apprenez-en davantage sur les [cibles de calcul d’entraînement pour Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

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

### <a name="examples"></a>Exemples 

Les exemples de code suivants sont spécifiques à la classe [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) pour accéder à votre magasin de données pendant l’entraînement.

Ce code crée un estimateur en utilisant le script d’entraînement, `train.py`, à partir du répertoire source indiqué à l’aide des paramètres définis dans `script_params`, le tout sur la cible de calcul d’entraînement spécifiée.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Vous pouvez également transmettre une liste de magasins de données au paramètre `inputs` du constructeur Estimator afin de les monter ou de les copier depuis/vers vos magasins de données. Cet exemple de code :
* télécharge tout le contenu du magasin de données `ds1` vers la cible de calcul avant l’exécution du script d’entraînement `train.py`.
* télécharge le dossier `'./foo'` dans le magasin de données `ds2` vers la cible de calcul avant l’exécution de `train.py`.
* charge le fichier `'./bar.pkl'` à partir de la cible de calcul vers le magasin de données `ds3` après l’exécution de votre script.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="access-datastores-during-for-scoring"></a>Accéder aux magasins de données pendant le scoring

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
