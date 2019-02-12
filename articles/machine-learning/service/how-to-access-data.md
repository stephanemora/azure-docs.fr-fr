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
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 759ae1c077a2c93ee4450843a796b84d95701a10
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55769893"
---
# <a name="access-data-during-training-from-your-datastores"></a>Accéder aux données durant l’entraînement à partir de vos banques de données
Utiliser une banque de données pour accéder à vos données et interagir avec elles dans des flux de travail Azure Machine Learning.

Dans le service Azure Machine Learning, la banque de données est une abstraction sur [Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction). La banque de données peut référencer un conteneur d’[objets blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) ou un [partage de fichiers Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) en tant que stockage sous-jacent. 

## <a name="create-a-datastore"></a>Créer une banque de données
Pour utiliser des banques de données, vous devez tout d’abord disposer d’un [espace de travail](concept-azure-machine-learning-architecture.md#workspace). Commencez soit par [créer un espace de travail](quickstart-create-workspace-with-python.md), soit par en récupérer un existant :

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>Utiliser la banque de données par défaut
Il n’est pas nécessaire de créer ou de configurer un compte de stockage.  Chaque espace de travail a une banque de données par défaut que vous pouvez commencer à utiliser immédiatement.

Pour obtenir la banque de données par défaut de l’espace de travail :
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>Inscrire une banque de données
Si vous avez du stockage Azure, vous pouvez l’inscrire en tant que banque de données sur votre espace de travail. Vous pouvez inscrire un conteneur d’objets blob Azure ou un partage de fichiers Azure comme banque de données. Toutes les méthodes d’inscription sont sur la classe `Datastore` et ont la forme `register_azure_*`.

#### <a name="azure-blob-container-datastore"></a>Banque de données de conteneur d’objets blob Azure
Pour inscrire une banque de données de conteneur d’objets blob Azure :

```Python
ds = Datastore.register_azure_blob_container(workspace=ws, 
                                             datastore_name='your datastore name', 
                                             container_name='your azure blob container name',
                                             account_name='your storage account name', 
                                             account_key='your storage account key',
                                             create_if_not_exists=True)
```

#### <a name="azure-file-share-datastore"></a>Banque de données de partage de fichiers Azure
Pour inscrire une banque de données de partage de fichiers Azure :

```Python
ds = Datastore.register_azure_file_share(workspace=ws, 
                                         datastore_name='your datastore name', 
                                         container_name='your file share name',
                                         account_name='your storage account name', 
                                         account_key='your storage account key',
                                         create_if_not_exists=True)
```

### <a name="get-an-existing-datastore"></a>Obtenir une banque de données existante
Pour interroger une banque de données inscrite par nom :
```Python
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Vous pouvez également obtenir toutes les banques de données pour un espace de travail :
```Python
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

Pour des raisons pratiques, définissez l’une de vos banques de données inscrites comme banque de données par défaut pour votre espace de travail :
```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>Charger et télécharger des données
### <a name="upload"></a>Télécharger
Chargez un répertoire ou des fichiers individuels dans la banque de données avec le kit de développement logiciel (SDK) Python.

Pour charger un répertoire dans une banque de données `ds` :
```Python
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

## <a name="access-datastores-for-training"></a>Accéder à des banques de données pour l’entraînement
Vous pouvez accéder à une banque de données pendant une exécution de formation (par exemple, pour des données de formation ou de validation) sur une cible de calcul distante par le biais du kit de développement logiciel (SDK) Python. 

Deux méthodes sont prises en charge pour rendre votre banque de données accessible sur la cible de calcul distante :
* **Montage**  
`ds.as_mount()` : avec ce mode de montage, la banque de données sera montée pour vous sur la cible de calcul distante. 
* **Téléchargement/chargement**  
    * `ds.as_download(path_on_compute='your path on compute')` : les données sont téléchargées de votre banque de données vers la cible de calcul distante à l’emplacement spécifié par `path_on_compute`.
    * `ds.as_upload(path_on_compute='yourfilename'` transfère les données vers la banque de données.  Supposons que votre script de formation crée un fichier `foo.pkl` dans le répertoire de travail actuel de la cible de calcul distante. Chargez ce fichier dans votre banque de données avec `ds.as_upload(path_on_compute='./foo.pkl')` après que le script a créé le fichier. Le fichier est chargé à la racine de votre banque de données.
    
Pour référencer un dossier ou fichier spécifique dans votre banque de données, utilisez la fonction **`path`** de la banque de données. Par exemple, pour télécharger le contenu du répertoire `./bar` du magasin de données vers votre cible de calcul, utilisez `ds.path('./bar').as_download()`.

Tout objet `ds` ou `ds.path` est résolu en un nom de variable d’environnement au format `"$AZUREML_DATAREFERENCE_XXXX"`, dont la valeur représente le chemin de montage/téléchargement sur la cible de calcul distante. Le chemin de la banque de données sur la cible de calcul distante peut ne pas être identique au chemin d’exécution du script.

Pour accéder à votre banque de données pendant la formation, transmettez-la à votre script de formation en tant qu’argument de ligne de commande par le biais de `script_params` :

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
`as_mount()` étant le mode par défaut pour une banque de données, vous pourriez aussi passer simplement et directement `ds` à l’argument `'--data_dir'`.

Ou transmettez une liste de banques de données au paramètre `inputs` du constructeur de l’outil de sizing afin de les monter ou de les copier à partir de/vers vos banques de données :

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```
Le code ci-dessus :
* téléchargera tout le contenu dans la banque de données `ds1` vers la cible de calcul distante avant que votre script d’entraînement `train.py` soit exécuté.
* téléchargera le dossier `'./foo'` dans la banque de données `ds2` vers la cible de calcul distante avant que `train.py` soit exécuté.
* chargera le fichier `'./bar.pkl'` à partir de la cible de calcul distante vers la banque de données `d3` après l’exécution de votre script.

## <a name="next-steps"></a>Étapes suivantes
* [Entraîner un modèle](how-to-train-ml-models.md)
