---
title: Apprentissage et déploiement de modèles à partir de VS Code
titleSuffix: Azure Machine Learning service
description: Apprenez-en davantage sur Azure Machine Learning pour Visual Studio Code et sur la façon de commencer l’apprentissage et de déployer des modèles de Machine Learning et de Deep Learning dans Azure Machine Learning service à l’aide de Visual Studio Code.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: e7df9086fa5ffc6273a6cb063bdee3cfdfa73e34
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54013313"
---
# <a name="use-visual-studio-code-to-train-and-deploy-machine-learning-models"></a>Utiliser Visual Studio Code pour effectuer l'apprentissage de modèles Machine Learning et les déployer

Dans cet article, vous allez apprendre à utiliser l’extension **Azure Machine Learning pour Visual Studio Code** pour effectuer l’apprentissage et déployer des modèles de Machine Learning et de Deep Learning avec Azure Machine Learning service dans Visual Studio Code (VS Code).

Azure Machine Learning permet d’exécuter des expériences en local mais aussi sur des cibles de calcul à distance. Pour chaque expérience, vous effectuez le suivi de plusieurs exécutions aussi souvent que vous le voulez. L’objectif est ici de tester de manière itérative différentes techniques, les hyperparamètres, etc. Vous pouvez utiliser Azure Machine Learning pour effectuer le suivi des métriques personnalisées et des exécutions afin de favoriser la reproductibilité et les capacités d’audit dans le domaine de la science des données.

De même, vous pouvez déployer ces modèles pour vos besoins de test et de production.

## <a name="prerequisites"></a>Prérequis

+ Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](http://aka.ms/AMLFree) dès aujourd’hui.

+ Extension [Azure Machine Learning pour VS Code](how-to-vscode-tools.md) installée et configurée.

+ Faites également en sorte que le [SDK Azure Machine Learning pour Python soit installé](how-to-vscode-tools.md) avec VS Code.

## <a name="create-and-manage-compute-targets"></a>Créer et gérer des cibles de calcul

Avec Azure Machine Learning pour VS Code, vous pouvez préparer vos données, effectuer l’apprentissage des modèles et les déployer à la fois en local et sur des cibles de calcul à distance.

Cette extension prend en charge plusieurs cibles de calcul à distance pour Azure Machine Learning. Consultez la [liste complète des cibles de calcul prises en charge](how-to-set-up-training-targets.md) pour Azure Machine Learning.

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>Créer des cibles de calcul pour Azure Machine Learning dans VS Code

**Pour créer une cible de calcul :**

1. Cliquez sur l’icône Azure dans la barre d’activités Visual Studio Code. La barre latérale Azure Machine Learning apparaît.

2. Dans l’arborescence, développez l’abonnement Azure et l’espace de travail du service Azure Machine Learning. Dans l’image animée, le nom de l’abonnement est « Free Trial » (Essai gratuit) et l’espace de travail est « TeamWorkspace » (Espace de travail d’équipe). 

3. Sous le nœud de l’espace de travail, cliquez avec le bouton droit sur le nœud **Calcul**, puis choisissez **Create Compute** (Créer un calcul).

4. Choisissez le type de cible de calcul dans la liste. 

5. Dans la Palette de commandes, sélectionnez une taille de machine virtuelle.

6. Dans la Palette de commandes, entrez un nom pour la cible de calcul dans le champ. 

7. Dans le fichier config JSON qui s’ouvre dans un nouvel onglet, indiquez des propriétés avancées. Vous pouvez spécifier des propriétés telles que le nombre maximal de nœuds.

8. Après avoir configuré votre cible de calcul, cliquez sur **Envoyer** en bas à droite de l’écran.

Voici un exemple de création de Capacité de calcul Azure Machine Learning (AMLCompute) : [![Créer AML Compute dans VS Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Fichier de configuration de série de tests

L’extension VS Code va créer automatiquement une cible de calcul locale et des configurations de série de tests pour votre environnement **local** et votre environnement **docker** sur l’ordinateur local. Vous trouverez les fichiers de configuration d’exécution sous la cible de calcul associée. 

Il s’agit d’un extrait de code issu du fichier de configuration de série de tests locale par défaut. Par défaut, `userManagedDependencies: True`. Vous devez donc installer l’ensemble de vos bibliothèques et dépendances vous-même. Par la suite, les exécutions d’expériences locales utiliseront votre environnement Python par défaut comme indiqué par l’extension Python VS Code.

```yaml
# user_managed_dependencies = True indicates that the environment will be user managed. False indicates that AzureML will manage the user environment.
    userManagedDependencies: True
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
    docker:
# Set True to perform this run inside a Docker container.
    enabled: false
```

## <a name="train-and-tune-models"></a>Effectuer l’apprentissage des modèles et les ajuster

Utiliser Azure Machine Learning pour VS Code (préversion) pour rapidement itérer votre code, le parcourir et le déboguer, puis utiliser la solution de contrôle de code source de votre choix. 

**Pour exécuter votre expérience localement avec Azure Machine Learning**

1. Cliquez sur l’icône Azure dans la barre d’activités Visual Studio Code. La barre latérale Azure Machine Learning apparaît.

1. Dans l’arborescence, développez l’abonnement Azure et l’espace de travail du service Azure Machine Learning. 

1. Sous le nœud d’espace de travail, développez le nœud **Calcul** et effectuez un clic droit sur la méthode de calcul **Run Config** que vous voulez utiliser. 

1. Sélectionnez **Run Experiment** (Exécuter l’expérience).

1. Sélectionnez le script à exécuter à partir de l’Explorateur de fichiers. 

1. Cliquez sur **View Experiment Run** (Afficher l’exécution d’expérience) pour afficher le portail Azure Machine Learning intégré et ainsi surveiller vos exécutions et consulter les modèles formés.

Voici un exemple illustrant comment exécuter une expérience localement : [![Exécution d’une expérience localement](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>Utiliser des services de calcul à distance pour les expériences dans VS Code

Pour utiliser une cible de calcul à distance au moment de l’apprentissage, vous devez créer un fichier de configuration de série de tests. Ce fichier indique à Azure Machine Learning non seulement où exécuter l’expérience, mais également comment préparer l’environnement.

#### <a name="the-conda-dependencies-file"></a>Fichier de dépendance Conda

Par défaut, un nouvel environnement conda est créé pour vous, et vos dépendances d’installation sont managées. Toutefois, vous devez spécifier vos dépendances dans le fichier `aml_config/conda_dependencies.yml`.

Il s’agit d’un extrait de code issu du fichier « aml_config/conda_dependencies.yml ».
Vous pouvez ajouter des dépendances supplémentaires dans le fichier config.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

**Pour exécuter votre expérience avec Azure Machine Learning sur un ordinateur de calcul distant**

1. Cliquez sur l’icône Azure dans la barre d’activités Visual Studio Code. La barre latérale Azure Machine Learning apparaît.

1. Dans l’arborescence, développez l’abonnement Azure et l’espace de travail du service Azure Machine Learning. 

1. Cliquez avec le bouton droit sur votre script python dans la fenêtre de l’éditeur et sélectionnez **AML : Exécuter en tant qu’expérience dans Azure**. 

1. Dans la Palette de commandes, sélectionnez la cible de calcul. 

1. Dans la Palette de commandes, entrez le nom de la configuration d’exécution dans le champ. 

1. Modifiez le fichier conda_dependencies.yml pour spécifier les dépendances de runtime de l’expérience, puis cliquez sur **Envoyer** en bas à droite de l’écran. 

1. Cliquez sur **View Experiment Run** (Afficher l’exécution d’expérience) pour afficher le portail Azure Machine Learning intégré et ainsi surveiller vos exécutions et consulter les modèles formés.

Voici un exemple illustrant comment exécuter une expérience sur une cible de calcul distante : [![Exécution d’une expérience sur une cible distante](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Déployer et gérer des modèles
Azure Machine Learning permet de déployer et de gérer vos modèles Machine Learning dans le cloud et en périphérie. 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>Enregistrer votre modèle auprès d’Azure Machine Learning à partir de Visual Studio Code

Maintenant que vous avez effectué l’apprentissage de votre modèle, vous pouvez l’inscrire dans votre espace de travail.
Les modèles inscrits peuvent être suivis et déployés.

**Pour enregistrer votre modèle :**

1. Cliquez sur l’icône Azure dans la barre d’activités Visual Studio Code. La barre latérale Azure Machine Learning apparaît.

1. Dans l’arborescence, développez l’abonnement Azure et l’espace de travail du service Azure Machine Learning.

1. Sous le nœud d’espace de travail, cliquez avec le bouton droit sur **Modèles**, puis choisissez **Register Model** (Inscrire le modèle).

1. Dans la Palette de commandes, entrez un nom de modèle dans le champ. 

1. À partir de cette liste, choisissez si vous souhaitez charger un **fichier de modèle** (pour les modèles uniques) ou un **dossier modèle** (pour les modèles avec plusieurs fichiers, par exemple Tensorflow). 

1. Sélectionnez votre dossier ou fichier.

1. Après avoir configuré les propriétés de votre modèle, cliquez sur **Envoyer** en bas à droite de l’écran. 

Voici un exemple illustrant comment inscrire votre modèle dans AML : [![Inscription d’un modèle dans AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-vs-code"></a>Déployer votre service depuis Visual Studio Code

À l’aide de VS Code, vous pouvez déployer votre service web sur :
+ Azure Container Instance (ACI) : à des fins de test
+ Azure Kubernetes Service (AKS) : en production 

Vous n’avez pas besoin de créer de conteneur ACI pour les tests, car cette création s’effectue à la volée. Toutefois, les clusters AKS doivent être configurés à l’avance. 

Découvrez-en davantage sur le [déploiement avec Azure Machine Learning](how-to-deploy-and-where.md) de manière générale.

**Pour déployer un service web :**

1. Cliquez sur l’icône Azure dans la barre d’activités Visual Studio Code. La barre latérale Azure Machine Learning apparaît.

1. Dans l’arborescence, développez votre abonnement Azure et l’espace de travail du service Azure Machine Learning.

1. Sous le nœud d’espace de travail, développez le nœud **Modèles**.

1. Cliquez sur le modèle que vous souhaitez déployer avec le bouton droit, puis choisissez la commande **Deploy Service from Registered Model** (Déployer le service depuis le modèle inscrit) dans le menu contextuel.

1. Dans la palette de commandes, choisissez la cible de calcul sur laquelle déployer à partir de la liste. 

1. Dans la Palette de commandes, entrez un nom pour ce service dans le champ.  

1. Dans la palette de commandes, appuyez sur la touche entrée de votre clavier pour parcourir et sélectionner le fichier de script.

1. Dans la palette de commandes, appuyez sur la touche entrée de votre clavier pour parcourir et sélectionner le fichier de dépendance conda.

1. Après avoir configuré les propriétés de votre service, cliquez sur **Envoyer** en bas à droite de l’écran pour procéder au déploiement. Dans ce fichier de propriétés de service, vous pouvez spécifier le fichier Docker local ou le fichier schema.json que vous souhaitez utiliser.

Le service web est désormais déployé.

Voici un exemple de déploiement d’un service web : [![Déploiement d’un service web](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

## <a name="next-steps"></a>Étapes suivantes

Pour une procédure détaillée d’apprentissage avec Machine Learning en dehors de VS Code, consultez le [Tutoriel : Former des modèles avec Azure Machine Learning](tutorial-train-models-with-aml.md).

Pour une procédure détaillée sur la modification, l’exécution et le débogage de code en local, consultez le [Didacticiel Python Hello World](https://code.visualstudio.com/docs/python/python-tutorial).
