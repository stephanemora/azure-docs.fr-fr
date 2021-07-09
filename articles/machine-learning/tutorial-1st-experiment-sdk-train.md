---
title: 'Tutoriel : Entraîner un premier modèle Machine Learning Python '
titleSuffix: Azure Machine Learning
description: Guide pratique pour entraîner un modèle Machine Learning dans Azure Machine Learning. Il s’agit de la deuxième partie d’une série de prise en main qui en compte trois.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 04/27/2021
ms.custom: devx-track-python, contperf-fy21q3, FY21Q4-aml-seo-hack, contperf-fy21q
ms.openlocfilehash: 616723e66dceeb3c0de21b36e3af3039dafbe63e
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112028268"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-2-of-3"></a>Tutoriel : Entraîner votre premier modèle Machine Learning (partie 2 sur 3)

Ce tutoriel vous montre comment former un modèle Machine Learning dans Azure Machine Learning.  Ce tutoriel est la *deuxième partie d’une série de tutoriels qui en compte trois*.

 Dans la [Partie 1 : Exécuter « Hello World ! »](tutorial-1st-experiment-hello-world.md) de la série, vous avez appris à utiliser un script de contrôle pour exécuter un tâches dans le cloud.  

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

- Avoir suivi la [partie 1](tutorial-1st-experiment-hello-world.md) de la série.

## <a name="create-training-scripts"></a>Créer des scripts de formation

Définissez tout d’abord l’architecture du réseau neuronal dans un fichier *model.py*. Tout votre code d’entraînement sera placé dans le sous-répertoire `src`, notamment *model.py*.

Le code d’entraînement est tiré de [cet exemple d’introduction](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) de PyTorch. Notez que les concepts de Azure Machine Learning s’appliquent à tout code Machine Learning, pas seulement à PyTorch.

1. Créez un fichier *model.py* dans le sous-dossier **src**. Copiez ce code dans le fichier :

    ```python
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
1. Dans la barre d’outils, sélectionnez **Enregistrer** pour enregistrer le fichier.  Fermez l’onglet si vous le souhaitez.

1. Définissez ensuite le script d’entraînement, également dans le sous-dossier **src**. Ce script télécharge le jeu de données CIFAR10 en utilisant les API PyTorch `torchvision.dataset`. De plus, il configure le réseau défini dans *model.py* et l’entraîne pour deux périodes à l’aide de la méthode SGD (Stochastic Gradient Descent) et de la méthode de l’entropie croisée standard.

    Créez un script *train.py* dans le sous-dossier **src** :

     ```python
    import torch
    import torch.optim as optim
    import torchvision
    import torchvision.transforms as transforms
    
    from model import Net
    
    # download CIFAR 10 data
    trainset = torchvision.datasets.CIFAR10(
        root="../data",
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

1. Vous disposez à présent de la structure de dossiers suivante :

    :::image type="content" source="media/tutorial-1st-experiment-sdk-train/directory-structure.png" alt-text="La structure de répertoires affiche train.py dans le sous-répertoire src":::
    

> [!div class="nextstepaction"]
> [J’ai créé les scripts d’entraînement](?success=create-scripts#test-local) [J’ai rencontré un problème](https://www.research.net/r/7CTJQQN?issue=create-scripts)


## <a name="test-locally"></a><a name="test-local"></a> Tester localement

Sélectionnez **Enregistrer et exécuter le script dans le terminal** pour exécuter le script *train.py* directement sur l’instance de calcul.

Une fois le script exécuté, sélectionnez **Actualiser** au-dessus des dossiers de fichiers. Vous verrez le nouveau dossier de données appelé **get-started/data**. Développez ce dossier pour voir les données téléchargées.  

:::image type="content" source="media/tutorial-1st-experiment-hello-world/directory-with-data.png" alt-text="La capture d’écran des dossiers montre le dossier de données créé en exécutant le fichier localement.":::

> [!div class="nextstepaction"]
> [J’ai exécuté le code localement](?success=test-local#create-local) [J’ai rencontré un problème](https://www.research.net/r/7CTJQQN?issue=test-local)


## <a name="create-the-control-script"></a><a name="create-local"></a> Créer le script de contrôle

La différence entre le script de contrôle suivant et celui que vous avez utilisé pour soumettre « Hello World ! » est que vous ajoutez quelques lignes supplémentaires pour définir l’environnement.

Créez un fichier Python dans le dossier **get-started** appelé `run-pytorch.py` :

```python
# run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='./src',
                             script='train.py',
                             compute_target='cpu-cluster')

    # use curated pytorch environment 
    env = ws.environments['AzureML-PyTorch-1.6-CPU']
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```

> [!TIP]
> Si vous avez utilisé un autre nom quand vous avez créé le cluster de calcul, veillez à modifier également ce nom dans le code `compute_target='cpu-cluster'`.

### <a name="understand-the-code-changes"></a>Comprendre les modifications du code

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      Azure Machine Learning fournit le concept d’[environnement](/python/api/azureml-core/azureml.core.environment.environment) pour représenter un environnement Python reproductible et versionné afin d’exécuter des expériences. Ici, vous utilisez l’un des [environnements organisés](how-to-use-environments.md#use-a-curated-environment).  Il est également facile de créer un environnement à partir d’un environnement Conda ou pip local.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      Ajoute l’environnement à [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig).
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [J’ai créé le script de contrôle](?success=control-script#submit) [J’ai rencontré un problème](https://www.research.net/r/7CTJQQN?issue=control-script)


## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit"></a> Soumettre l’exécution à Azure Machine Learning

Sélectionnez **Enregistrer et exécuter le script dans le terminal** pour exécuter le script *run-pytorch.py*.

>[!NOTE] 
> La première fois que vous exécutez ce script, Azure Machine Learning va générer une nouvelle image Docker à partir de votre environnement PyTorch. La totalité de l’exécution peut prendre 3 à 4 minutes. 
>
> Vous pouvez visualiser les journaux de la génération Docker dans Azure Machine Learning Studio. Suivez le lien vers le studio, sélectionnez l’onglet **Sorties + journaux**, puis sélectionnez `20_image_build_log.txt`.
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
> Si vous voyez une erreur `Your total snapshot size exceeds the limit`, cela signifie que le dossier **data** se trouve dans la valeur `source_directory` utilisée dans `ScriptRunConfig`.
>
> Sélectionnez **...** à la fin du dossier, puis sélectionnez **Déplacer** pour déplacer **data** vers le dossier **get-started**.  



> [!div class="nextstepaction"]
> [J’ai soumis l’exécution](?success=test-w-environment#log) [J’ai rencontré un problème](https://www.research.net/r/7CTJQQN?issue=test-w-environment)

## <a name="log-training-metrics"></a><a name="log"></a> Journaliser les métriques d’entraînement

Maintenant que vous avez un modèle de formation dans Azure Machine Learning, commencez le suivi de certaines mesures de performances.

Le script de formation en cours imprime les métriques sur le terminal. Azure Machine Learning fournit un mécanisme de journalisation des métriques avec davantage de fonctionnalités. En ajoutant quelques lignes de code, vous avez la possibilité de visualiser les métriques dans Studio et de comparer les métriques entre plusieurs exécutions.

### <a name="modify-trainpy-to-include-logging"></a>Modifier *train.py* pour inclure la journalisation

1. Modifiez le script *train.py* pour inclure deux lignes de code supplémentaires :
    
    ```python
    import torch
    import torch.optim as optim
    import torchvision
    import torchvision.transforms as transforms
    from model import Net
    from azureml.core import Run
    # ADDITIONAL CODE: get run from the current context
    run = Run.get_context()
    # download CIFAR 10 data
    trainset = torchvision.datasets.CIFAR10(
        root='./data',
        train=True,
        download=True,
        transform=torchvision.transforms.ToTensor()
    )
    trainloader = torch.utils.data.DataLoader(
        trainset,
        batch_size=4,
        shuffle=True,
        num_workers=2
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
                    # ADDITIONAL CODE: log loss metric to AML
                    run.log('loss', loss)
                    print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                    running_loss = 0.0
        print('Finished Training')
    ```

2. **Enregistrez** ce fichier, puis fermez l’onglet si vous le souhaitez.

#### <a name="understand-the-additional-two-lines-of-code"></a>Comprendre les deux lignes de code supplémentaires

Dans *train.py*, vous accédez à l’objet d’exécution _depuis_ le script d’entraînement lui-même à l’aide de la méthode `Run.get_context()`, et vous l’utilisez pour journaliser des métriques :

```python
# ADDITIONAL CODE: get run from the current context
run = Run.get_context()

...
# ADDITIONAL CODE: log loss metric to AML
run.log('loss', loss)
```

Les métriques de Azure Machine Learning sont :

- Organisées par expérience et par exécution ; il est donc facile de suivre et de comparer des métriques.
- Équipées d’une interface utilisateur qui vous permet de visualiser les performances de formation dans Studio.
- Conçues pour être mises à l’échelle, vous conservez ces avantages même lorsque vous exécutez des centaines d’expériences.

> [!div class="nextstepaction"]
> [J’ai modifié train.py ](?success=modify-train#log) [J’ai rencontré un problème](https://www.research.net/r/7CTJQQN?issue=modify-train)


### <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-again"></a> Soumettre l’exécution à Azure Machine Learning

Sélectionnez l’onglet du script *run-pytorch.py*, puis sélectionnez **Enregistrer et exécuter le script dans le terminal** pour réexécuter le script *run-pytorch.py*. 

Cette fois, quand vous accédez au studio, accédez à l’onglet **Métriques**, où vous pouvez maintenant voir les mises à jour en direct sur la perte du modèle d’entraînement ! 1 à 2 minutes sont parfois nécessaires avant le début de l’entraînement.  

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="Graphe des pertes de l’entraînement dans l’onglet Métriques.":::

> [!div class="nextstepaction"]
> [J’ai resoumis l’exécution](?success=resubmit-with-logging#next-steps) [J’ai rencontré un problème](https://www.research.net/r/7CTJQQN?issue=resubmit-with-logging)

## <a name="next-steps"></a>Étapes suivantes

Dans cette session, vous avez effectué une mise à niveau à partir d’un script « Hello World » de base vers un script de formation plus réaliste qui nécessitait l’exécution d’un environnement Python spécifique. Vous avez vu comment utiliser des environnements Azure Machine Learning organisés. Enfin, vous avez vu comment l’ajout de quelques lignes de code vous permet de journaliser des métriques dans Azure Machine Learning.

Il existe d’autres façons de créer des environnements Azure Machine Learning, y compris [à partir d’un fichier pip requirements.txt](/python/api/azureml-core/azureml.core.environment.environment#from-pip-requirements-name--file-path-) ou [à partir d’un environnement Conda existant](/python/api/azureml-core/azureml.core.environment.environment#from-existing-conda-environment-name--conda-environment-name-).

Dans la prochaine session, vous verrez comment utiliser des données dans Azure Machine Learning en chargeant le jeu de données CIFAR10 sur Azure.

> [!div class="nextstepaction"]
> [Tutoriel : Apporter vos propres données](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Si vous voulez arrêter la série de tutoriels ici sans passer à l’étape suivante, n’oubliez pas de [nettoyer vos ressources](tutorial-1st-experiment-bring-data.md#clean-up-resources).
