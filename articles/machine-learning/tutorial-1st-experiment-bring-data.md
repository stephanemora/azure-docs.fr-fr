---
title: 'Tutoriel : Charger des données et entraîner un modèle'
titleSuffix: Azure Machine Learning
description: Guide pratique pour charger et utiliser vos propres données dans un entraînement à distance. Il s’agit de la deuxième partie d’une série de prise en main qui en compte trois.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 04/29/2021
ms.custom: tracking-python, contperf-fy21q3, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: dbbd71a40419ee3472b01be11c101567e6945634
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112028214"
---
# <a name="tutorial-upload-data-and-train-a-model-part-3-of-3"></a>Tutoriel : Charger des données et entraîner un modèle (partie 3 sur 3)

Ce tutoriel vous montre comment télécharger et utiliser vos propres données pour former des modèles Machine Learning dans Azure Machine Learning. Ce tutoriel est la *troisième partie d’une série de tutoriels qui en compte trois*.  

Dans la [Partie 2 : Entraîner un modèle](tutorial-1st-experiment-sdk-train.md), vous avez entraîné un modèle dans le cloud, en utilisant des exemples de données de `PyTorch`.  Vous avez également téléchargé ces données à l’aide de la méthode `torchvision.datasets.CIFAR10` dans l’API PyTorch. Dans ce tutoriel, vous allez utiliser les données téléchargées pour découvrir le workflow afin d’utiliser vos propres données dans Azure Machine Learning.

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Charger des données sur Azure.
> * Créer un script de contrôle.
> * Comprendre les nouveaux concepts d’Azure Machine Learning (passage de paramètres, jeux de données, magasins de données).
> * Soumettre et exécuter votre script de formation.
> * Afficher le résultat de votre code dans le cloud.

## <a name="prerequisites"></a>Prérequis

Vous aurez besoin des données téléchargées dans le tutoriel précédent.  Vérifiez que vous avez effectué ces étapes :

1. [Créer le script d’entraînement](tutorial-1st-experiment-sdk-train.md#create-training-scripts).  
1. [Tester localement](tutorial-1st-experiment-sdk-train.md#test-local).

## <a name="adjust-the-training-script"></a>Ajuster le script de formation

À présent, votre script d’entraînement (get-started/src/train.py) s’exécute dans Azure Machine Learning, et vous pouvez superviser les performances du modèle. Nous allons paramétrer le script d’entraînement en introduisant des arguments. L’utilisation d’arguments vous permet de comparer facilement différents hyperparamètres.

Notre script d’entraînement est configuré pour télécharger le jeu de données CIFAR10 à chaque exécution. Le code Python ci-dessous a été ajusté pour lire les données d’un répertoire.

>[!NOTE] 
> L’utilisation de `argparse` paramètre le script.

1. Ouvrez *train.py*, et remplacez son contenu par le code suivant :

    ```python
    import os
    import argparse
    import torch
    import torch.optim as optim
    import torchvision
    import torchvision.transforms as transforms
    from model import Net
    from azureml.core import Run
    run = Run.get_context()
    if __name__ == "__main__":
        parser = argparse.ArgumentParser()
        parser.add_argument(
            '--data_path',
            type=str,
            help='Path to the training data'
        )
        parser.add_argument(
            '--learning_rate',
            type=float,
            default=0.001,
            help='Learning rate for SGD'
        )
        parser.add_argument(
            '--momentum',
            type=float,
            default=0.9,
            help='Momentum for SGD'
        )
        args = parser.parse_args()
        print("===== DATA =====")
        print("DATA PATH: " + args.data_path)
        print("LIST FILES IN DATA PATH...")
        print(os.listdir(args.data_path))
        print("================")
        # prepare DataLoader for CIFAR10 data
        transform = transforms.Compose([
            transforms.ToTensor(),
            transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))
        ])
        trainset = torchvision.datasets.CIFAR10(
            root=args.data_path,
            train=True,
            download=False,
            transform=transform,
        )
        trainloader = torch.utils.data.DataLoader(
            trainset,
            batch_size=4,
            shuffle=True,
            num_workers=2
        )
        # define convolutional network
        net = Net()
        # set up pytorch loss /  optimizer
        criterion = torch.nn.CrossEntropyLoss()
        optimizer = optim.SGD(
            net.parameters(),
            lr=args.learning_rate,
            momentum=args.momentum,
        )
        # train the network
        for epoch in range(2):
            running_loss = 0.0
            for i, data in enumerate(trainloader, 0):
                # unpack the data
                inputs, labels = data
                # zero the parameter gradients
                optimizer.zero_grad()
                # forward + backward + optimize
                outputs = net(inputs)
                loss = criterion(outputs, labels)
                loss.backward()
                optimizer.step()
                # print statistics
                running_loss += loss.item()
                if i % 2000 == 1999:
                    loss = running_loss / 2000
                    run.log('loss', loss)  # log loss metric to AML
                    print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                    running_loss = 0.0
        print('Finished Training')
    ```

1. **Enregistrez** le fichier.  Fermez l’onglet si vous le souhaitez.

### <a name="understanding-the-code-changes"></a>Compréhension des modifications du code

Le code de `train.py` a utilisé la bibliothèque `argparse` pour configurer `data_path`, `learning_rate` et `momentum`.

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

En outre, le script `train.py` a été adapté pour mettre à jour l’optimiseur afin d’utiliser les paramètres définis par l’utilisateur :

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```

> [!div class="nextstepaction"]
> [J’ai modifié le script d’entraînement](?success=adjust-training-script#upload) [J’ai rencontré un problème](https://www.research.net/r/7C6W7BQ?issue=adjust-training-script)


## <a name="upload-the-data-to-azure"></a><a name="upload"></a> Charger les données dans Azure

Pour exécuter ce script dans Azure Machine Learning, vous devez rendre vos données d’entraînement disponibles dans Azure. Votre espace de travail Azure Machine Learning est fourni avec un magasin de données _par défaut_. Il s’agit d’un compte Stockage Blob Azure où vous pouvez stocker vos données d’entraînement.

>[!NOTE] 
> Azure Machine Learning vous permet de connecter d’autres magasins de données cloud qui stockent vos données. Pour plus d’informations, consultez la [documentation des magasins de données](./concept-data.md).  

1. Créez un script de contrôle Python dans le dossier de **get-started**. (Vérifiez qu’il se trouve dans le dossier **get-started**, et *pas* dans le dossier **/src**.)  Nommez le script *upload-data.py*, puis copiez ce code dans le fichier :
    
    ```python
    # upload-data.py
    from azureml.core import Workspace
    ws = Workspace.from_config()
    datastore = ws.get_default_datastore()
    datastore.upload(src_dir='./data',
                     target_path='datasets/cifar10',
                     overwrite=True)
    
    ```

    La valeur de `target_path` spécifie le chemin du magasin de données où les données CIFAR10 seront téléchargées.

    >[!TIP] 
    > Vous utilisez Azure Machine Learning pour charger les données, mais vous pouvez utiliser l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour charger des fichiers ad hoc. Si vous avez besoin d’un outil ETL, vous pouvez utiliser [Azure Data Factory](../data-factory/introduction.md) pour ingérer vos données dans Azure.

2. Sélectionnez **Enregistrer et exécuter le script dans le terminal** pour exécuter le script *upload-data.py*.

    Vous devez normalement voir la sortie standard suivante :

    ```txt
    Uploading ./data\cifar-10-batches-py\data_batch_2
    Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
    .
    .
    Uploading ./data\cifar-10-batches-py\data_batch_5
    Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
    Uploaded 9 files
    ```

> [!div class="nextstepaction"]
> [J’ai chargé les données](?success=upload-data#control-script) [J’ai rencontré un problème](https://www.research.net/r/7C6W7BQ?issue=upload-data)

## <a name="create-a-control-script"></a><a name="control-script"></a> Créer un script de contrôle

À nouveau, créez un script de contrôle Python appelé *run-pytorch-data.py* dans le dossier **get-started** :

```python
# run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
    )

    # use curated pytorch environment 
    env = ws.environments['AzureML-PyTorch-1.6-CPU']
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to compute cluster. Click link below")
    print("")
    print(aml_url)
```

> [!TIP]
> Si vous avez utilisé un autre nom quand vous avez créé le cluster de calcul, veillez à modifier également ce nom dans le code `compute_target='cpu-cluster'`.

### <a name="understand-the-code-changes"></a>Comprendre les modifications du code

Le script de contrôle est similaire à celui de la [partie 3 de notre série](tutorial-1st-experiment-sdk-train.md), avec ces nouvelles lignes de code :

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      Un [jeu de données](/python/api/azureml-core/azureml.core.dataset.dataset) est utilisé pour référencer les données que vous avez chargées dans Stockage Blob Azure. Les jeux de données sont une couche d’abstraction au-dessus de vos données, conçue pour améliorer la fiabilité.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) est modifié pour inclure une liste d’arguments qui seront passés dans `train.py`. L’argument `dataset.as_named_input('input').as_mount()` signifie que le répertoire spécifié sera _monté_ sur la cible de calcul.
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [J’ai créé le script de contrôle](?success=control-script#submit-to-cloud) [J’ai rencontré un problème](https://www.research.net/r/7C6W7BQ?issue=control-script)

## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-to-cloud"></a> Soumettre l’exécution à Azure Machine Learning

Sélectionnez **Enregistrer et exécuter le script dans le terminal** pour exécuter le script *run-pytorch-data.py*.  Cette exécution permet d’entraîner le modèle sur le cluster de calcul à l’aide des données que vous avez chargées.

Ce code va produire une URL vers l’expérience dans Azure Machine Learning Studio. Si vous accédez à ce lien, vous pourrez voir votre code en cours d’exécution.

> [!div class="nextstepaction"]
> [J’ai resoumis l’exécution](?success=submit-to-cloud#inspect-log) [J’ai rencontré un problème](https://www.research.net/r/7C6W7BQ?issue=submit-to-cloud)

### <a name="inspect-the-log-file"></a><a name="inspect-log"></a> Examiner le fichier journal

Dans le studio, accédez à l’exécution de l’expérience (en sélectionnant la sortie de l’URL précédente), puis à **Sorties + journaux**. Sélectionnez le fichier `70_driver_log.txt`. Faites défiler le fichier journal jusqu’à ce que la sortie suivante s’affiche :

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

Remarques :

- Azure Machine Learning a monté automatiquement pour vous Stockage Blob sur le cluster de calcul.
- ``dataset.as_named_input('input').as_mount()`` utilisé dans le script de contrôle donne après résolution le point de montage.

> [!div class="nextstepaction"]
> [J’ai examiné le fichier journal](?success=inspect-log#clean-up-resources) [J’ai rencontré un problème](https://www.research.net/r/7C6W7BQ?issue=inspect-log)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous comptez suivre à présent un autre tutoriel, ou commencer vos propres entraînements, passez à [Étapes suivantes](#next-steps).

### <a name="stop-compute-instance"></a>Arrêter l’instance de calcul

Si vous ne comptez pas l’utiliser maintenant, arrêtez l’instance de calcul :

1. Dans le studio, sur la gauche, sélectionnez **Calcul**.
1. Sous les onglets supérieurs, sélectionnez **Instances de calcul**
1. Sélectionnez l’instance de calcul dans la liste.
1. Dans la barre d’outils supérieure, sélectionnez **Arrêter**.


### <a name="delete-all-resources"></a>Supprimer toutes les ressources

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Vous pouvez également conserver le groupe de ressources mais supprimer un espace de travail unique. Affichez les propriétés de l’espace de travail, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, nous avons vu comment charger des données sur Azure en utilisant `Datastore`. Le magasin de données a servi de stockage cloud pour votre espace de travail, ce qui vous offre un emplacement permanent et flexible pour conserver vos données.

Vous avez appris comment modifier votre script de formation pour accepter un chemin d’accès aux données via la ligne de commande. En utilisant `Dataset` vous avez pu monter un répertoire sur l’exécution distante.

Maintenant que vous avez un modèle, découvrez :

* Comment [déployer des modèles avec Azure Machine Learning](how-to-deploy-and-where.md).
