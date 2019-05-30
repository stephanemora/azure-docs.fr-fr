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
ms.openlocfilehash: 93fc9a4e9e44bd7e8db3d49fe390ebe273c45ce9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239045"
---
# <a name="access-data-from-your-datastores"></a>Accéder aux données à partir de vos banques de données

 Dans le service Azure Machine Learning, les banques de données sont calcul indépendante de l’emplacement des mécanismes pour accéder au stockage sans avoir à modifier votre code source. Si vous écrivez du code de formation pour spécifier un chemin d’accès en tant que paramètre, ou fournissez une banque de données directement à un estimator, flux de travail Azure Machine Learning Vérifiez vos emplacements de magasin de données sont accessibles et mis à disposition votre contexte de calcul.

Cette procédure montre des exemples des tâches suivantes :
* [Choisissez une banque de données](#access)
* [Obtenir des données](#get)
* [Charger et télécharger des données aux magasins de données](#up-and-down)
* [Accès de banque de données pendant la formation](#train)

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser des banques de données, vous devez tout d’abord disposer d’un [espace de travail](concept-workspace.md).

Commencez soit par [créer un espace de travail](setup-create-workspace.md#sdk), soit par en récupérer un existant :

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>Choisissez une banque de données

Vous pouvez utiliser le magasin de données par défaut ou apportez votre propre.

### <a name="use-the-default-datastore-in-your-workspace"></a>Utiliser le magasin de données par défaut dans votre espace de travail

 Chaque espace de travail a une banque de données par défaut inscrit, vous pouvez utiliser tout de suite.

Pour obtenir la banque de données par défaut de l’espace de travail :

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>Inscrire votre propre magasin de données avec l’espace de travail

Si vous avez du stockage Azure, vous pouvez l’inscrire en tant que banque de données sur votre espace de travail.   Toutes les méthodes d’inscription sont sur le [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) classe et ont la forme register_azure_ *. 

Les exemples suivants montrent que vous enregistriez un conteneur d’objets Blob Azure ou un partage de fichiers Azure comme banque de données.

+ Pour un **banque de données Azure Blob conteneur**, utiliser [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ Pour un **Datastore de partage de fichiers Azure**, utilisez [ `register_azure_file_share()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Exemple : 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           file_share_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>Rechercher et définir des banques de données

Pour obtenir un magasin de données spécifié inscrit dans l’espace de travail actuel, utilisez [ `get()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) :

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

Pour définir une banque de données par défaut différent pour l’espace de travail actuel, utilisez [ `set_default_datastore()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-):

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Charger et télécharger des données
Le [ `upload()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) et [ `download()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) méthodes décrites dans les exemples suivants sont spécifiques à et fonctionner de manière identique pour le [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) et [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) classes.

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
## <a name="access-datastores-during-training"></a>Accès des magasins de données pendant la formation

Une fois votre banque de données disponibles sur cible de calcul de la formation, vous pouvez y accéder lors des exécutions d’apprentissage (par exemple, les données de formation ou de validation) en passant simplement le chemin d’accès à celui-ci en tant que paramètre dans votre script de formation.

Le tableau suivant répertorie les [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) méthodes qui indiquent comment utiliser le magasin de données lors des exécutions à la cible de calcul.

moyen|Méthode|Description|
----|-----|--------
Monter| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Permet de monter la banque de données sur la cible de calcul.
Téléchargement|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Pour télécharger le contenu de votre banque de données à l’emplacement spécifié par `path_on_compute`. <br> Pour le contexte de la formation de s’exécuter, ce téléchargement se produit avant l’exécution.
Télécharger|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Permet de charger un fichier à partir de l’emplacement spécifié par `path_on_compute` à votre banque de données. <br> Pour le contexte de la formation de s’exécuter, ce chargement se produit après l’exécution.

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Pour faire référence à un dossier ou fichier spécifique dans votre banque de données et le rendre disponible sur la cible de calcul, utilisez le magasin de données [ `path()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) (fonction).

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> N’importe quel `ds` ou `ds.path` objet correspond à un nom de variable d’environnement du format `"$AZUREML_DATAREFERENCE_XXXX"` dont la valeur représente le chemin d’accès de montage/téléchargement sur l’ordinateur cible. Le chemin d’accès de banque de données sur l’ordinateur cible ne peut pas être le même que le chemin d’accès de l’exécution du script de formation.

### <a name="training-compute-and-datastore-matrix"></a>Matrice de calcul et de la banque de données d’apprentissage

La matrice suivante affiche les fonctionnalités d’accès de données disponibles pour les cibles de calcul de formation différents et les scénarios de la banque de données. En savoir plus sur la [formation des cibles de calcul pour Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Calcul|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Local|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/A         |N/A                                                                         |
| Capacité de calcul Azure Machine Learning |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|N/A         |N/A                                                                         |
| Machines virtuelles               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| Transfert de données                  |[ML&nbsp;pipelines](concept-ml-pipelines.md)                                               |N/A                                           |[ML&nbsp;pipelines](concept-ml-pipelines.md)            |[ML&nbsp;pipelines](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;pipelines](concept-ml-pipelines.md)                                              |N/A                                           |[ML&nbsp;pipelines](concept-ml-pipelines.md)             |N/A                                                                         |
| Azure Batch                    |[ML&nbsp;pipelines](concept-ml-pipelines.md)                                               |N/A                                           |N/A         |N/A                                                                         |
| Azure DataLake Analytics       |N/A                                           |N/A                                           |[ML&nbsp;pipelines](concept-ml-pipelines.md)             |N/A                                                                         |

> [!NOTE]
> Il peut y avoir des scénarios dans lesquels hautement itératif, processus de données volumineuses s’exécutent plus rapidement à l’aide de [`as_download()`] au lieu de [`as_mount()`] ; Cela peut être validé de façon expérimentale.

### <a name="examples"></a>Exemples 

Les exemples de code suivants sont spécifiques à la [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe pour accéder à votre banque de données pendant la formation.

Ce code crée un estimator utilisant le script de formation, `train.py`, à partir du répertoire source indiqué à l’aide des paramètres définis dans `script_params`, toutes sur la formation spécifiée cible de calcul.

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

Vous pouvez également passer dans la liste des banques de données au constructeur estimateur `inputs` paramètre à monter ou à copier vers/à partir de vos banques de données. Cet exemple de code :
* Télécharge le contenu de la banque de données `ds1` à la cible de calcul avant votre script de formation `train.py` est exécuté
* Le dossier Téléchargements `'./foo'` dans le magasin de données `ds2` à la cible de calcul avant `train.py` est exécuté
* Télécharge le fichier `'./bar.pkl'` à partir de la cible de calcul jusqu'à la banque de données `ds3` après l’exécution de votre script

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="next-steps"></a>Étapes suivantes

* [Entraîner un modèle](how-to-train-ml-models.md)

* [Déployer un modèle](how-to-deploy-and-where.md)
