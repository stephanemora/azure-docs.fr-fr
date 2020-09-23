---
title: 'Tutoriel : Utiliser vos propres données'
titleSuffix: Azure Machine Learning
description: La partie 4 de la série de tutoriels sur la prise en main d’Azure ML vous explique comment utiliser vos propres données dans le cadre d’une formation à distance.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: tracking-python
ms.openlocfilehash: 876ba76655572979a1d831a1ca07e5f3871a3283
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929339"
---
# <a name="tutorial-use-your-own-data-part-4-of-4"></a>Tutoriel : Utiliser vos propres données (partie 4 sur 4)

Ce tutoriel vous montre comment télécharger et utiliser vos propres données pour former des modèles Machine Learning dans Azure Machine Learning.

Ce tutoriel constitue la **quatrième partie d’une série de quatre tutoriels** conçus pour vous apprendre les principes fondamentaux de Azure Machine Learning à travers des exercices Machine Learning basés sur des tâches Azure. Ce tutoriel s’appuie sur les exercices que vous avez effectués lors des tutoriels précédents ([Partie 1 : Configurer un script « Hello World »](tutorial-1st-experiment-sdk-setup-local.md), [Partie 2 : Exécuter un script « Hello World »](tutorial-1st-experiment-hello-world.md) et [Partie 3 : Effectuer l'apprentissage d'un modèle](tutorial-1st-experiment-sdk-train.md).

Dans le troisième tutoriel de notre série, [Partie 3 : Former un modèle](tutorial-1st-experiment-sdk-train.md), vous avez déclanché le téléchargement de vos données en utilisant la méthode `torchvision.datasets.CIFAR10` incorporée dans l’API PyTorch. Toutefois, dans de nombreux cas, nous vous recommandons d’utiliser vos propres données pour mener à bien la formation à distance. Cet article présente le workflow que vous pouvez suivre pour exploiter vos données dans Azure Machine Learning.

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Configurer un script de formation pour utiliser des données dans un répertoire local
> * Tester le script de formation en local
> * Charger des données dans Azure
> * Créer un script de contrôle
> * Comprendre les nouveaux concepts de Azure Machine Learning (passage de paramètres, jeux de données, magasins de données)
> * Soumettre et exécuter votre script de formation
> * Afficher la sortie de votre code dans le cloud

## <a name="prerequisites"></a>Prérequis

* Terminer la [troisième partie](tutorial-1st-experiment-sdk-train.md) de notre série de tutoriels.
* Avoir des connaissances préliminaires du langage Python et des workflows Machine Learning.
* Avoir un environnement de développement local. Cet environneent doit se composer notamment de Visual Studio Code, Jupyter ou PyCharm.
* Avoir au moins la version 3.5-3.7 de Python.

## <a name="adjust-the-training-script"></a>Ajuster le script de formation
À présent, votre script de formation (tutorial/src/train.py) s’exécute dans Azure Machine Learning et peut surveiller les performances du modèle. Nous allons paramétrer le script de formation en introduisant des arguments. L’utilisation d’arguments vous permet de comparer facilement différents hyperparamètres.

Actuellement, notre script de formation est configuré pour télécharger le jeu de données CIFAR10 à chaque exécution. Le code Python ci-dessous a été ajusté pour lire les données d’un répertoire.

>[!NOTE] 
> L’utilisation de `argparse` pour paramétrer le script.

```python
# tutorial/src/train.py
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
    parser.add_argument('--data_path', type=str, help='Path to the training data')
    parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
    parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
    args = parser.parse_args()
    
    print("===== DATA =====")
    print("DATA PATH: " + args.data_path)
    print("LIST FILES IN DATA PATH...")
    print(os.listdir(args.data_path))
    print("================")
    
    # prepare DataLoader for CIFAR10 data
    transform = transforms.Compose([transforms.ToTensor(), transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))])
    trainset = torchvision.datasets.CIFAR10(
        root=args.data_path,
        train=True,
        download=False,
        transform=transform,
    )
    trainloader = torch.utils.data.DataLoader(trainset, batch_size=4, shuffle=True, num_workers=2)

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
                run.log('loss', loss) # log loss metric to AML
                print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                running_loss = 0.0

    print('Finished Training')
```

### <a name="understanding-the-code-changes"></a>Compréhension des modifications du code

Le code utilisé dans `train.py` a exploité la bibliothèque `argparse` pour configurer le chemin d’accès aux données (`data_path`), le taux de formation (`learning_rate`) et l’inertie (`momentum`).

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

## <a name="test-the-script-locally"></a>Tester le script localement

Votre script accepte à présent _data path_ (chemin d’accès aux données) en tant qu’argument. Pour commencer, effectuez un test en local. Ajoutez à la structure de répertoires de votre tutoriel un dossier appelé `data`. La structure de votre répertoire doit ressembler à ce qui suit :

```txt
tutorial
└──.azureml
|  └──config.json
|  └──pytorch-env.yml
└──data
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
└──04-run-pytorch.py
```

Si vous n’avez pas exécuté `train.py` en local dans le tutoriel précédent, vous ne disposez pas du répertoire `data/` . Dans ce cas, exécutez la méthode `torchvision.datasets.CIFAR10` en local avec `download=True` dans votre script `train.py`.

Pour exécuter en local le script de formation modifié, appelez :

```bash
python src/train.py --data_path ./data --learning_rate 0.003 --momentum 0.92
```

Vous ne devez pas télécharger le jeu de données CIFAR10 en passant un chemin d’accès local aux données. Vous pouvez également faire des essais avec différentes valeurs pour les hyperparamètres _learning rate_ (taux de formation) et _momentum_ (inertie) sans avoir à les coder en dur dans le script de formation.

## <a name="upload-the-data-to-azure"></a>Charger les données dans Azure

Pour exécuter ce script dans Azure Machine Learning, vous devez rendre vos données de formation disponibles dans Azure. Votre espace de travail Azure Machine Learning est fourni avec un **magasin de données** _par défaut_ (un compte de stockage d’objets BLOB Azure) que vous pouvez utiliser pour stocker vos données de formation.

>[!NOTE] 
> Azure Machine Learning vous permet de connecter d’autres magasins de données cloud qui stockent vos données. Pour plus d’informations, consultez la [documentation des magasins de données](./concept-data.md) :  

Créez un script de contrôle Python appelé `05-upload-data.py` dans le répertoire `tutorial` :

```python
# tutorial/05-upload-data.py
from azureml.core import Workspace
ws = Workspace.from_config()
datastore = ws.get_default_datastore()
datastore.upload(src_dir='./data', target_path='datasets/cifar10', overwrite=True)
```

`target_path` spécifie le chemin d’accès au magasin de données où les données CIFAR10 seront téléchargées.

>[!TIP] 
> Même si vous utilisez Azure Machine Learning pour télécharger les données, vous pouvez utiliser l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour charger des fichiers ad hoc. Si vous avez besoin d’un outil ETL, vous pouvez faire appel à [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) pour ingérer vos données dans Azure.

Exécutez le fichier Python pour télécharger les données (Remarque : Le téléchargement doit être rapide, moins de 60 secondes.)

```bash
python 05-upload-data.py
```
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


## <a name="create-a-control-script"></a>Créer un script de contrôle

Comme vous l’avez fait précédemment, créez un script de contrôle Python appelé `06-run-pytorch-data.py` :

```python
# tutorial/06-run-pytorch-data.py
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
    
    # set up pytorch environment
    env = Environment.from_conda_specification(name='pytorch-env',file_path='.azureml/pytorch-env.yml')
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to an Azure Machine Learning compute cluster. Click on the link below")
    print("")
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>Comprendre les modifications du code

Le script de contrôle est similaire à celui de la [troisième partie de notre série de tutoriels](tutorial-1st-experiment-sdk-train.md) en contenir ces nouvelles lignes de code :

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      Un [jeu de données](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py&preserve-view=true) est utilisé pour référencer les données que vous avez chargées dans le magasin d’objets BLOB Azure. Les jeux de données sont une couche d’abstraction au-dessus de vos données, conçue pour améliorer la fiabilité.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) est modifié pour inclure une liste d’arguments qui seront passés dans `train.py`. L’argument `dataset.as_named_input('input').as_mount()` signifie que le répertoire spécifié sera _monté_ sur la cible de calcul.
   :::column-end:::
:::row-end:::

## <a name="submit-run-to-azure-machine-learning"></a>Soumettre l’exécution à Azure Machine Learning

À présent, soumettez à nouveau l’exécution pour utiliser la nouvelle configuration :

```bash
python 06-run-pytorch-data.py
```

Ceci va imprimer une URL vers l’expérience dans Azure Machine Learning Studio. Si vous accédez à ce lien, vous serez en mesure de voir votre code en cours d’exécution.

### <a name="inspect-the-70_driver_log-log-file"></a>Inspecter le fichier journal 70_driver_log

Dans Azure Machine Learning Studio, accédez à l’exécution de l’expérience (en cliquant sur la sortie d’URL à partir de la cellule ci-dessus), puis sélectionnez **Sorties + Journaux**. Cliquez sur le fichier 70_driver_log.txt. Cette sortie doit s’afficher :

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

1. Azure Machine Learning a monté automatiquement le magasin d’objets BLOB sur le cluster de calcul.
2. Le ``dataset.as_named_input('input').as_mount()`` utilisé dans le script de contrôle donne le point de montage.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Vous pouvez également conserver le groupe de ressources mais supprimer un espace de travail unique. Affichez les propriétés de l’espace de travail, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, nous avons vu comment télécharger des données sur Azure à l’aide d’un `Datastore`. Le magasin de données a servi de stockage cloud pour votre espace de travail, ce qui vous offre un emplacement permanent et flexible pour conserver vos données.

Vous avez appris comment modifier votre script de formation pour accepter un chemin d’accès aux données via la ligne de commande. En utilisant un `Dataset` vous avez pu monter un répertoire sur l’exécution distante. 

Maintenant que vous avez un modèle, découvrez :

* [Comment déployer des modèles avec Azure Machine Learning](how-to-deploy-and-where.md)
