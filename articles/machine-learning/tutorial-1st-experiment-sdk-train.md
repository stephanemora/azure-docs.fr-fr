---
title: 'Tutoriel : Former votre premier modèle Machine Learning — Python'
titleSuffix: Azure Machine Learning
description: Dans cette troisième partie de notre série consacrée à la prise en main d’Azure Machine Learning, vous découvrirez comment former un modèle Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: a267231dd447b114c69e6ead20c8ab5252f85d0e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90896727"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>Tutoriel : Former votre premier modèle Machine Learning (Partie 3 sur 4)

Ce tutoriel vous montre comment former un modèle Machine Learning dans Azure Machine Learning.

Ce tutoriel constitue la **troisième partie d’une série de quatre tutoriels** conçus pour vous apprendre les principes fondamentaux de Azure Machine Learning à travers des exercices Machine Learning basés sur des tâches Azure. Ce tutoriel s’appuie sur les exercices que vous avez effectués lors des tutoriels précédents ([Partie 1 : Configurer votre ordinateur local](tutorial-1st-experiment-sdk-setup-local.md) et [Partie 2 : Exécuter un script « Hello World »](tutorial-1st-experiment-hello-world.md)) de la série.

À présent, vous allez soumettre un script qui forme un modèle Machine Learning. Cet exemple vous permettra de comprendre comment Azure Machine Learning facilite la cohérence entre les procédures de débogage local et d’exécutions distantes.

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Créer un script de formation.
> * Utiliser Conda pour définir un environnement Azure Machine Learning.
> * Créer un script de contrôle.
> * Comprendre les classes Azure Machine Learning (Environment, Run, Metrics).
> * Soumettre et exécuter votre script de formation.
> * Afficher le résultat de votre code dans le cloud.
> * Journaliser des métriques dans Azure Machine Learning.
> * Afficher vos métriques dans le cloud.

## <a name="prerequisites"></a>Prérequis

* Terminer la [première partie de cette série de tutoriels](tutorial-1st-experiment-sdk-setup-local.md) si vous n’avez pas déjà d’espace de travail Azure Machine Learning.
* Avoir des connaissances préliminaires du langage Python et des workflows Machine Learning.
* Avoir un environnement de développement local. Cet environneent doit se composer notamment de Visual Studio Code, Jupyter ou PyCharm.
* Avoir au moins la version 3.5-3.7 de Python.

## <a name="create-training-scripts"></a>Créer des scripts de formation

Tout d’abord, vous définissez l’architecture du réseau neuronal dans un fichier `model.py`. Tout votre code de formation sera placé dans le sous-répertoire `src`, y compris `model.py`.

Le code ci-dessous est tiré de [cet exemple d’introduction](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) à partir de PyTorch. Notez que les concepts de Azure Machine Learning s’appliquent à tout code Machine Learning, pas seulement à PyTorch.

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

Ensuite, vous définissez le script de formation. Ce script télécharge le jeu de données CIFAR10 à l’aide des API PyTorch `torchvision.dataset` , configure le réseau défini dans `model.py` et effectue sa formation pour deux époques à l’aide d’un algorithme de gradient stochastique (ou SGD « Stochastic Gradient Descent ») et du calcul de la perte d’entropie croisée.

Créez un script `train.py` dans le sous-répertoire `src` :

```python
# tutorial/src/train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net

# download CIFAR 10 data
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
                print(f"epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}")
                running_loss = 0.0

    print("Finished Training")

```

Vous disposez maintenant de la structure de répertoires présentée ci-dessous :

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

## <a name="define-a-python-environment"></a>Définir un environnement Python

Pour notre exemple, nous allons utiliser un environnement Conda (la procédure que nous présenterons est presque identique pour un environnement virtuel PIP).

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

Cet environnement comporte toutes les dépendances requises par votre modèle et votre script de formation. Notez qu’il n’existe aucune dépendance avec le Kit de développement logiciel (SDK) Azure Machine Learning Python.

## <a name="test-locally"></a>Tester les topologies localement

Testez vos exécutions de script en local en utilisant cet environnement avec :

```bash
conda env create -f .azureml/pytorch-env.yml    # create conda environment
conda activate pytorch-env             # activate conda environment
python src/train.py                    # train model
```

Après avoir exécuté ce script, toutes les données téléchargées s’affichent dans un répertoire nommé `tutorial/data`.

## <a name="create-the-control-script"></a>Créer le script de contrôle

La différence entre le script de contrôle ci-dessous et celui utilisé pour soumettre « Hello World » consiste en l’ajout de quelques lignes de code, qui serviront à définir l’environnement.

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
      `env = Environment.from_conda_specification( ... )`
   :::column-end:::
   :::column span="2":::
      Azure Machine Learning fournit le concept d’un [Environnement](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) pour représenter un environnement Python reproductible, avec version, pour l’exécution d’expériences. La création d’un environnement à partir d’un environnement Conda ou PIP local est simple.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      Ajoute l’environnement au [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true).
   :::column-end:::
:::row-end:::

## <a name="submit-run-to-azure-machine-learning"></a>Soumettre l’exécution à Azure Machine Learning

Si vous avez échangé les environnements locaux, veillez à revenir à un environnement sur lequel le Kit de développement logiciel (SDK) Azure Machine Learning Python est installé, puis exécutez :

```bash
python 04-run-pytorch.py
```

>[!NOTE] 
> La première fois que vous exécutez ce script, Azure Machine Learning générera une nouvelle image Docker à partir de votre environnement PyTorch. La totalité de l’exécution peut prendre 5-10 minutes. Vous pouvez voir les journaux de génération Docker dans Azure Machine Learning Studio : Suivez le lien vers Machine Learning Studio > Sélectionnez l’onglet « Sorties + Journaux » > Sélectionnez `20_image_build_log.txt`.
Cette image sera réutilisée dans les prochaines exécutions, ce qui les accélèrera.

Une fois votre image générée, sélectionnez `70_driver_log.txt` pour afficher la sortie de votre script de formation.

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
> Si un message d’erreur semblable à « `Your total snapshot size exceeds the limit` » s’affiche, cela signifie que le répertoire `data` se trouve dans le répertoire `source_directory` utilisé dans `ScriptRunConfig`.
> Veillez à déplacer `data` en dehors de `src`.

Les environnements peuvent être enregistrés dans un espace de travail avec `env.register(ws)`, ce qui leur permet d’être facilement partagés, réutilisés et dotés d’une version. Les environnements permettent de reproduire facilement les résultats précédents et de collaborer avec votre équipe.

Azure Machine Learning conserve aussi une collection d’environnements organisés. Ces environnements couvrent les scénarios de Machine Learning courants et sont sauvegardés par les images Docker mises en cache. Les images Docker mises en cache accélèrent la première exécution à distance.

En bref, l’utilisation d’environnements inscrits peut vous faire gagner du temps ! Pour plus d’informations, consultez la [documentation sur les environnements](./how-to-use-environments.md).

## <a name="log-training-metrics"></a>Journaliser les métriques de formation

Maintenant que vous avez un modèle de formation dans Azure Machine Learning, commencez le suivi de certaines mesures de performances.
Le script de formation en cours imprime les métriques sur le terminal. Azure Machine Learning fournit un mécanisme de journalisation des métriques avec davantage de fonctionnalités. En ajoutant quelques lignes de code, vous avez la possibilité de visualiser les métriques dans Studio et de comparer les métriques entre plusieurs exécutions.

### <a name="modify-trainpy-to-include-logging"></a>Modifier `train.py` pour inclure la journalisation

Modifiez votre script `train.py` pour inclure deux lignes de code supplémentaires :

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

# download CIFAR 10 data
trainset = torchvision.datasets.CIFAR10(root='./data', train=True, download=True, transform=torchvision.transforms.ToTensor())
trainloader = torch.utils.data.DataLoader(trainset, batch_size=4, shuffle=True, num_workers=2)

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

Dans `train.py`, vous accédez à l’objet d’exécution _depuis_ le script de formation à l’aide de la méthode `Run.get_context()` et vous l’utilisez pour enregistrer les métriques :

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

Les métriques de Azure Machine Learning sont :

- Organisées par expérimentation et exécutées, il est facile de les suivre et de les comparer.
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

### <a name="submit-run-to-azure-machine-learning"></a>Soumettre l’exécution à Azure Machine Learning
Soumettre ce script une fois de plus :

```bash
python 04-run-pytorch.py
```

Cette fois, lorsque vous accédez à Studio, accédez à l’onglet « Métriques », où vous pouvez maintenant voir les mises à jour en direct sur les pertes du modèle de formation !

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="Graphique des pertes de formation dans l’onglet Métriques":::

## <a name="next-steps"></a>Étapes suivantes

Dans cette session, vous avez effectué une mise à niveau à partir d’un script « Hello World » de base vers un script de formation plus réaliste qui nécessitait l’exécution d’un environnement Python spécifique. Vous avez vu comment adopter un environnement Conda local dans le cloud avec des environnements Azure Machine Learning. Enfin, vous avez vu comment l’ajout de quelques lignes de code vous permet de journaliser des métriques dans Azure Machine Learning.

Il existe d’autres façons de créer des environnements Azure Machine Learning, y compris [à partir d’un PIP requirements.txt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#from-pip-requirements-name--file-path-), ou même [à partir d’un environnement Conda existant](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#from-existing-conda-environment-name--conda-environment-name-).

Dans la prochaine session, vous verrez comment utiliser des données dans Azure Machine Learning en chargeant le jeu de données CIFAR10 sur Azure.

> [!div class="nextstepaction"]
> [Tutoriel : Apporter vos propres données](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Si vous souhaitez terminer la série de tutoriels dès maintenant, sans passer à l’étape suivante, n’oubliez pas de [nettoyer vos ressources](tutorial-1st-experiment-bring-data.md#clean-up-resources)