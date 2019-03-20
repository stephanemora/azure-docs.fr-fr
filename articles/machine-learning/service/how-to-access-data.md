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
ms.date: 02/25/2019
ms.custom: seodec18
ms.openlocfilehash: e6e1b304b90b37c93bed22bcb720a646680ee083
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58223612"
---
# <a name="access-data-from-your-datastores"></a>Accéder aux données à partir de vos banques de données

Magasins de données vous permettent d’interagir avec et accéder à vos données si vous exécutez votre code localement, sur un cluster de calcul, ou sur une machine virtuelle. Dans cet article, vous découvrez les flux de travail Azure Machine Learning qui garantissent que vos banques de données est accessibles et disponibles pour votre contexte de calcul.

Cette procédure montre des exemples pour les tâches suivantes :
* [Choisissez une banque de données](#access)
* [Obtenir des données](#get)
* [Charger et télécharger des données aux magasins de données](#up-and-down)
* [Accès de banque de données pendant la formation](#train)

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser des magasins de données, vous devez un [espace de travail](concept-azure-machine-learning-architecture.md#workspace) première. 

Commencez soit par [créer un espace de travail](quickstart-create-workspace-with-python.md), soit par en récupérer un existant :

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

Ou, [suivent ce démarrage rapide Python](quickstart-create-workspace-with-python.md) pour utiliser le SDK pour créer votre espace de travail et de prise en main.

<a name="access"></a>

## <a name="choose-a-datastore"></a>Choisissez une banque de données

Vous pouvez utiliser le magasin de données par défaut ou apportez votre propre.

### <a name="use-the-default-datastore-in-your-workspace"></a>Utiliser le magasin de données par défaut dans votre espace de travail

Pas nécessaire de créer ou configurer un compte de stockage dans la mesure où chaque espace de travail a une banque de données par défaut. Vous pouvez utiliser que la banque de données immédiatement tel qu’il est déjà inscrit dans l’espace de travail. 

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

+ Pour un **Datastore de partage de fichiers Azure**, utilisez [ `register_azure_file_share()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Par exemple :  
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           container_name='your file share name',
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
from azureml.data import AzureFileDatastore, AzureBlobDatastore

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

Une fois que vous apportez votre banque de données disponibles sur le calcul à distance, vous pouvez l’accéder lors des exécutions d’apprentissage (par exemple, les données de formation ou de validation) en passant simplement le chemin d’accès à celui-ci en tant que paramètre dans votre script de formation.

Le tableau suivant répertorie le commun [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) ou les méthodes qui rendent les banques de données disponibles sur le calcul à distance.

# #

moyen|Méthode|Description
----|-----|--------
Monter| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Permet de monter une banque de données sur le calcul à distance. Mode par défaut pour les banques de données.
Téléchargement|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Utilisez pour télécharger des données à partir de l’emplacement spécifié par `path_on_compute` dans votre banque de données pour le calcul à distance.
Télécharger|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Utiliser pour charger des données à la racine de votre banque de données à partir de l’emplacement spécifié par `path_on_compute`.

```Python
import azureml.data
from azureml.data import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Pour référencer un dossier ou fichier spécifique dans votre banque de données, utilisez la fonction [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) de la banque de données.

```Python
#download the contents of the `./bar` directory from the datastore to the remote compute
ds.path('./bar').as_download()
```



> [!NOTE]
> Tout objet `ds` ou `ds.path` est résolu en un nom de variable d’environnement au format `"$AZUREML_DATAREFERENCE_XXXX"`, dont la valeur représente le chemin de montage/téléchargement sur la cible de calcul distante. Le chemin d’accès de banque de données sur le calcul à distance ne peut pas être le même que le chemin d’accès de l’exécution du script de formation.

### <a name="examples"></a>Exemples 

Les éléments suivants illustrent des exemples spécifiques à la [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe pour accéder à votre banque de données pendant la formation.


```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py')
```

Dans la mesure où `as_mount()` est le mode par défaut pour une banque de données, vous pourriez également directement passer `ds` à la `'--data_dir'` argument.

Ou passer dans la liste des banques de données au constructeur estimateur `inputs` paramètre à monter ou à copier vers/à partir de vos banques de données. Cet exemple de code :
* Télécharge le contenu de la banque de données `ds1` pour le calcul à distance avant votre script de formation `train.py` est exécuté
* Le dossier Téléchargements `'./foo'` dans le magasin de données `ds2` pour le calcul à distance avant `train.py` est exécuté
* Télécharge le fichier `'./bar.pkl'` à partir du calcul à distance jusqu'à la banque de données `ds3` après l’exécution de votre script

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```


## <a name="next-steps"></a>Étapes suivantes

* [Entraîner un modèle](how-to-train-ml-models.md)

* [Déployer un modèle](how-to-deploy-and-where.md)
