---
title: 'Tutoriel : Former votre premier modèle Machine Learning — Python'
titleSuffix: Azure Machine Learning
description: Dans cette troisième partie de notre série consacrée à la prise en main d’Azure Machine Learning, vous découvrez comment entraîner un modèle Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: f3ba5751e7a0c2369d505535896bbb4ff7523c02
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314565"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>Tutoriel : Entraîner votre premier modèle Machine Learning (partie 3 sur 4)

Ce tutoriel vous montre comment former un modèle Machine Learning dans Azure Machine Learning.

Ce tutoriel constitue la *partie 3 d’une série de quatre tutoriels* où vous découvrez les principes fondamentaux d’Azure Machine Learning et comment effectuer des tâches de machine learning basées sur des travaux dans Azure. Ce tutoriel s’appuie sur les exercices que vous avez effectués dans [Partie 1 : Configurer votre ordinateur local](tutorial-1st-experiment-sdk-setup-local.md) et [Partie 2 : Exécuter « Hello world! »](tutorial-1st-experiment-hello-world.md) de la série.

À présent, vous allez soumettre un script qui forme un modèle Machine Learning. Cet exemple vous permettra de comprendre comment Azure Machine Learning facilite la cohérence entre les procédures de débogage local et d’exécutions distantes.

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Créez un script d’entraînement.
> * Utiliser Conda pour définir un environnement Azure Machine Learning.
> * Créer un script de contrôle.
> * Comprendre les classes Azure Machine Learning (`Environment`, `Run`, `Metrics`).
> * Soumettre et exécuter votre script de formation.
> * Afficher le résultat de votre code dans le cloud.
> * Journaliser des métriques dans Azure Machine Learning.
> * Afficher vos métriques dans le cloud.

## <a name="prerequisites"></a>Prérequis

* Avoir effectué la [partie 2](tutorial-1st-experiment-hello-world.md) de la série.
* Avoir des connaissances préliminaires du langage Python et des workflows Machine Learning.
* Environnement de développement local, comme Visual Studio Code, Jupyter et PyCharm.
* Python (version 3.5 à 3.7).

## <a name="create-training-scripts"></a>Créer des scripts de formation

Tout d’abord, vous définissez l’architecture du réseau neuronal dans un fichier `model.py`. Tout votre code d’entraînement sera placé dans le sous-répertoire `src`, y compris `model.py`.

Le code suivant est tiré de [cet exemple d’introduction](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) de PyTorch. Notez que les concepts de Azure Machine Learning s’appliquent à tout code Machine Learning, pas seulement à PyTorch.

```python
# tutorial/src/model.py
import torch.nn as nn
import torch.nn.functional as F


class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 5)
        self.pool = nn.MaxPool2d(2, 2)
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16 * 5 * 5)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x
```

Ensuite, vous définissez le script de formation. Ce script télécharge le jeu de données CIFAR10 en utilisant les API PyTorch `torchvision.dataset`, configure le réseau défini dans `model.py` et l’entraîne pour deux périodes en utilisant un algorithme de gradient stochastique (ou SGD « Stochastic Gradient Descent ») et du calcul de la perte d’entropie croisée.

Créez un script `train.py` dans le sous-répertoire `src` :

```python
# tutorial/src/train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net

# download CIFAR10 data
trainset = torchvision.datasets.CIFAR10(
    root="./data",
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
trainloader = torch.utils.data.DataLoader(
    trainset, batch_size=4, shuffle=True
)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

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
                print(f"epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}")
                running_loss = 0.0

    print("Finished Training")

```

Vous disposez maintenant de la structure de répertoires suivante :

```txt
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
```

## <a name="create-a-python-environment"></a>Créer un environnement Python

À des fins de démonstration, nous allons utiliser un environnement Conda. (Les étapes pour un environnement virtuel pip sont pratiquement identiques.)

Créez un fichier nommé `pytorch-env.yml` dans le répertoire masqué `.azureml` :

```yml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
```

Cet environnement comporte toutes les dépendances requises par votre modèle et votre script de formation. Notez qu’il n’existe aucune dépendance du SDK Azure Machine Learning pour Python.

## <a name="test-locally"></a>Tester les topologies localement

Utilisez le code suivant pour tester les exécutions de votre script localement dans cet environnement :

```bash
conda env create -f .azureml/pytorch-env.yml    # create conda environment
conda activate pytorch-env                      # activate conda environment
python src/train.py                             # train model
```

Après l’exécution de ce script, vous voyez toutes les données téléchargées dans un répertoire nommé `tutorial/data`.

## <a name="create-the-control-script"></a>Créer le script de contrôle

La différence entre le script de contrôle suivant et celui que vous avez utilisé pour soumettre « Hello World ! » est que vous ajoutez quelques lignes supplémentaires pour définir l’environnement.

Créez un fichier Python dans le répertoire `tutorial` nommé `04-run-pytorch.py` :

```python
# tutorial/04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='src', script='train.py', compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(name='pytorch-env', file_path='.azureml/pytorch-env.yml')
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>Comprendre les modifications du code

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      Azure Machine Learning fournit le concept d’[environnement](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) pour représenter un environnement Python reproductible et versionné afin d’exécuter des expériences. La création d’un environnement à partir d’un environnement Conda ou PIP local est simple.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      Ajoute l’environnement à [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py).
   :::column-end:::
:::row-end:::

## <a name="submit-the-run-to-azure-machine-learning"></a>Soumettre l’exécution à Azure Machine Learning

Si vous êtes passé à des environnements locaux, veillez à revenir à un environnement où le SDK Azure Machine Learning pour Python est installé. 

Ensuite, exécutez :

```bash
python 04-run-pytorch.py
```

>[!NOTE] 
> La première fois que vous exécutez ce script, Azure Machine Learning va générer une nouvelle image Docker à partir de votre environnement PyTorch. La totalité de l’exécution peut prendre 5 à 10 minutes. 
>
> Vous pouvez visualiser les journaux de la génération Docker dans Azure Machine Learning Studio. Suivez le lien vers le studio, sélectionnez l’onglet **Sorties + journaux** , puis sélectionnez `20_image_build_log.txt`.
>
> Cette image sera réutilisée dans les prochaines exécutions, ce qui les rendra plus rapides.

Une fois votre image générée, sélectionnez `70_driver_log.txt` pour visualiser la sortie de votre script d’entraînement.

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

> [!WARNING]
> Si vous voyez une erreur `Your total snapshot size exceeds the limit`, cela signifie que le répertoire `data` se trouve dans la valeur de `source_directory` utilisée dans `ScriptRunConfig`.
>
> Déplacez `data` en dehors de `src`.

Les environnements peuvent être inscrits auprès d’un espace de travail avec `env.register(ws)`. Ils peuvent ensuite être facilement partagés, réutilisés et versionnés. Les environnements permettent de reproduire facilement les résultats précédents et de collaborer avec votre équipe.

Azure Machine Learning conserve aussi une collection d’environnements organisés. Ces environnements couvrent les scénarios de Machine Learning courants et sont sauvegardés par les images Docker mises en cache. Les images Docker mises en cache accélèrent la première exécution à distance.

En bref, l’utilisation d’environnements inscrits peut vous faire gagner du temps ! Pour plus d’informations, consultez [Comment utiliser les environnements](./how-to-use-environments.md).

## <a name="log-training-metrics"></a>Journaliser les métriques de formation

Maintenant que vous avez un modèle de formation dans Azure Machine Learning, commencez le suivi de certaines mesures de performances.

Le script de formation en cours imprime les métriques sur le terminal. Azure Machine Learning fournit un mécanisme de journalisation des métriques avec davantage de fonctionnalités. En ajoutant quelques lignes de code, vous avez la possibilité de visualiser les métriques dans Studio et de comparer les métriques entre plusieurs exécutions.

### <a name="modify-trainpy-to-include-logging"></a>Modifier `train.py` pour inclure la journalisation

Modifiez votre script `train.py` pour y inclure deux lignes de code supplémentaires :

```python
# train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net
from azureml.core import Run


# ADDITIONAL CODE: get Azure Machine Learning run from the current context
run = Run.get_context()

# download CIFAR10 data
trainset = torchvision.datasets.CIFAR10(
    root="./data",
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
trainloader = torch.utils.data.DataLoader(
    trainset, batch_size=4, shuffle=True, num_workers=2
)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

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
                run.log('loss', loss) # ADDITIONAL CODE: log loss metric to Azure Machine Learning
                print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                running_loss = 0.0

    print('Finished Training')
```

#### <a name="understand-the-additional-two-lines-of-code"></a>Comprendre les deux lignes de code supplémentaires

Dans `train.py`, vous accédez à l’objet d’exécution _depuis_ le script d’entraînement lui-même en utilisant la méthode `Run.get_context()` et vous l’utilisez pour consigner des métriques :

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

Les métriques de Azure Machine Learning sont :

- Organisées par expérience et par exécution ; il est donc facile de suivre et de comparer des métriques.
- Équipées d’une interface utilisateur qui vous permet de visualiser les performances de formation dans Studio.
- Conçues pour être mises à l’échelle, vous conservez ces avantages même lorsque vous exécutez des centaines d’expériences.

### <a name="update-the-conda-environment-file"></a>Mettre à jour le fichier d’environnement Conda

Le script `train.py` prenait uniquement une nouvelle dépendance sur `azureml.core`. Mettre à jour `pytorch-env.yml` pour refléter cette modification :

```yaml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
    - pip
    - pip:
        - azureml-sdk
```

### <a name="submit-the-run-to-azure-machine-learning"></a>Soumettre l’exécution à Azure Machine Learning
Soumettre ce script une fois de plus :

```bash
python 04-run-pytorch.py
```

Cette fois, quand vous accédez au studio, accédez à l’onglet **Métriques** , où vous pouvez maintenant voir les mises à jour en direct sur la perte du modèle d’entraînement !

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="Graphe des pertes de l’entraînement dans l’onglet Métriques.":::

## <a name="next-steps"></a>Étapes suivantes

Dans cette session, vous avez effectué une mise à niveau à partir d’un script « Hello World » de base vers un script de formation plus réaliste qui nécessitait l’exécution d’un environnement Python spécifique. Vous avez vu comment adopter un environnement Conda local dans le cloud avec des environnements Azure Machine Learning. Enfin, vous avez vu comment l’ajout de quelques lignes de code vous permet de journaliser des métriques dans Azure Machine Learning.

Il existe d’autres façons de créer des environnements Azure Machine Learning, y compris [à partir d’un fichier pip requirements.txt](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-pip-requirements-name--file-path-) ou [à partir d’un environnement Conda existant](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-).

Dans la prochaine session, vous verrez comment utiliser des données dans Azure Machine Learning en chargeant le jeu de données CIFAR10 sur Azure.

> [!div class="nextstepaction"]
> [Tutoriel : Apporter vos propres données](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Si vous voulez arrêter la série de tutoriels ici sans passer à l’étape suivante, n’oubliez pas de [nettoyer vos ressources](tutorial-1st-experiment-bring-data.md#clean-up-resources).