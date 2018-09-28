---
title: Utiliser des banques de données dans Azure Machine Learning pour accéder à des données
description: Guide pratique pour utiliser des banques de données afin d’accéder au stockage de données pendant l’entraînement
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 615ab592c040eedf7d31e3a3036f558ea6c09740
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990805"
---
# <a name="how-to-access-data-during-training"></a>Guide pratique pour accéder aux données pendant l’entraînement
Dans les services Azure Machine Learning, une banque de données est une abstraction sur [Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction). La banque de données peut référencer un conteneur d’[objets blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) ou un [partage de fichiers Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) en tant que stockage sous-jacent. Les banques de données vous permettent d’accéder et d’interagir facilement avec votre stockage de données pendant vos workflows Azure Machine Learning par le biais de l’interface CLI ou du SDK Python.

## <a name="create-a-datastore"></a>Créer une banque de données
Pour utiliser des banques de données, vous devez tout d’abord disposer d’un [espace de travail](concept-azure-machine-learning-architecture.md#workspace). Vous pouvez créer un espace de travail ou en récupérer un existant :

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>Utiliser la banque de données par défaut
Chaque espace de travail a une banque de données par défaut que vous pouvez commencer à utiliser immédiatement, ce qui vous évite d’avoir à créer et à configurer vos propres comptes de stockage.

Pour obtenir la banque de données par défaut de l’espace de travail :
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>Inscrire une banque de données
Si vous avez déjà du stockage Azure, vous pouvez l’inscrire en tant que banque de données sur votre espace de travail. Azure Machine Learning fournit la fonctionnalité nécessaire pour inscrire un conteneur d’objets blob Azure ou un partage de fichiers Azure en tant que banque de données. Toutes les méthodes d’inscription sont sur la classe `Datastore` et ont la forme `register_azure_*`.

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
datastores = ws.datastores()
for name, ds in datastores.items(),
    print(name, ds.datastore_type)"
```

Pour des raisons pratiques, si vous souhaitez définir l’une de vos banques de données inscrites comme banque de données par défaut pour votre espace de travail, vous pouvez le faire comme suit :
```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>Charger et télécharger des données
### <a name="upload"></a>Charger
Vous pouvez charger un répertoire ou des fichiers individuels dans la banque de données à l’aide du SDK Python.

Pour charger un répertoire dans une banque de données `ds` :
```Python
ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```
`target_path` spécifie l’emplacement dans le partage de fichiers (ou le conteneur d’objets blob) à charger. La valeur par défaut est `None`, auquel cas les données sont chargées à la racine. `overwrite=True` remplace toutes les données existantes à `target_path`.

Vous pouvez également charger une liste de fichiers individuels dans la banque de données par le biais de la méthode `upload_files()` de la banque de données.

### <a name="download"></a>Télécharger
De même, vous pouvez télécharger des données d’une banque de données vers votre système de fichiers local.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```
`target_path` est l’emplacement du répertoire local dans lequel télécharger les données. Pour spécifier un chemin au dossier dans le partage de fichiers (ou le conteneur d’objets blob) où effectuer le téléchargement, fournissez ce chemin à `prefix`. Si `prefix` est `None`, tout le contenu de votre partage de fichiers (ou conteneur d’objets blob) est téléchargé.

## <a name="access-datastores-for-training"></a>Accéder à des banques de données pour l’entraînement
Vous pouvez accéder à une banque de données pendant une exécution d’entraînement (par exemple pour des données d’entraînement ou de validation) sur une cible de calcul distante par le biais du SDK Python. 

Deux méthodes sont prises en charge pour rendre votre banque de données accessible sur la cible de calcul distante :
* **Montage**  
`ds.as_mount()` : avec ce mode de montage, la banque de données sera montée pour vous sur la cible de calcul distante. 
* **Téléchargement/chargement**  
    * `ds.as_download(path_on_compute='your path on compute')` : avec ce mode de téléchargement, les données seront téléchargées de votre banque de données vers la cible de calcul distante à l’emplacement spécifié par `path_on_compute`.
    * À l’inverse, vous pouvez également charger des données qui ont été produites à partir de votre exécution d’entraînement dans une banque de données. Par exemple, si votre script d’entraînement crée un fichier `foo.pkl` dans le répertoire de travail actif sur la cible de calcul distante, vous pouvez spécifier son chargement dans votre banque de données une fois que le script a été exécuté : `ds.as_upload(path_on_compute='./foo.pkl')`. Cela chargera le fichier à la racine de votre banque de données.
    
Si vous souhaitez référencer un dossier ou fichier spécifique dans votre banque de données, vous pouvez utiliser la fonction **`path`** de la banque de données. Par exemple, si votre banque de données a un répertoire avec le chemin relatif `./bar` et que vous souhaitez télécharger uniquement le contenu de ce dossier vers la cible de calcul, vous pouvez le faire comme suit : `ds.path('./bar').as_download()`

Tout objet `ds` ou `ds.path` est résolu en un nom de variable d’environnement au format `"$AZUREML_DATAREFERENCE_XXXX"`, dont la valeur représente le chemin de montage/téléchargement sur la cible de calcul distante. Le chemin de la banque de données sur la cible de calcul distante peut ne pas être identique au chemin d’exécution du script.

Pour accéder à votre banque de données pendant l’entraînement, vous pouvez la transmettre dans votre script d’entraînement en tant qu’argument de ligne de commande par le biais de `script_params` :

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

Vous pouvez également transmettre une liste de banques de données au paramètre `inputs` du constructeur Estimator afin d’effectuer un montage ou une copie vers/à partir de vos banques de données :

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
