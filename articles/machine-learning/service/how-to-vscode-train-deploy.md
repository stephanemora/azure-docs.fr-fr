---
title: Utiliser l’extension Visual Studio Code Tools for AI avec Azure Machine Learning
description: Apprenez-en davantage sur Visual Studio Code Tools for AI et sur la façon de commencer l’apprentissage et de déployer des modèles de Machine Learning et d’apprentissage profond avec le service Azure Machine Learning dans VS Code.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.reviewer: jmartens
ms.date: 10/1/2018
ms.openlocfilehash: 6372aada8c3d380f8d3cefb0479e45bbd7c63898
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945244"
---
# <a name="vs-code-tools-for-ai-train-and-deploy-ml-models-from-vs-code"></a>VS Code Tools for AI : effectuer l’apprentissage et déployer des modèles de Machine Learning depuis VS Code
Dans cet article, vous allez apprendre à utiliser l’extension **VS Code Tools for AI** pour effectuer l’apprentissage et déployer des modèles de Machine Learning et d’apprentissage profond avec le service Azure Machine Learning dans VS Code.

Azure Machine Learning permet d’exécuter des expériences en local mais aussi sur des cibles de calcul à distance. Pour chaque expérience, vous effectuez le suivi de plusieurs exécutions aussi souvent que vous le voulez. L’objectif est ici de tester de manière itérative différentes techniques, les hyperparamètres, etc. Vous pouvez utiliser Azure Machine Learning pour effectuer le suivi des métriques personnalisées et des exécutions afin de favoriser la reproductibilité et les capacités d’audit dans le domaine de la science des données.

De même, vous pouvez déployer ces modèles pour vos besoins de test et de production.

## <a name="prerequisites"></a>Prérequis

+ [Faites en sorte que l’extension VS Code Tools for AI](how-to-vscode-tools.md) soit configurée dans Azure Machine Learning.

+ Faites également en sorte que le [SDK Azure Machine Learning pour Python soit installé](how-to-vscode-tools.md) avec VS Code.

+ Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-and-manage-compute-targets"></a>Créer et gérer des cibles de calcul

Avec Visual Studio Code Tools for AI, vous pouvez préparer vos données, effectuer l’apprentissage des modèles et les déployer à la fois en local et sur des cibles de calcul à distance.

Cette extension prend en charge plusieurs cibles de calcul à distance pour Azure Machine Learning. Consultez la [liste complète des cibles de calcul prises en charge](how-to-set-up-training-targets.md) pour Azure Machine Learning.

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>Créer des cibles de calcul pour Azure Machine Learning dans VS Code

**Pour créer une cible de calcul :**

1. Cliquez sur l’icône Azure dans la barre d’activités Visual Studio Code. La barre latérale Azure : Machine Learning apparaît.

2. Dans l’arborescence, développez l’abonnement Azure et l’espace de travail du service Azure Machine Learning. Dans l’image animée, le nom de l’abonnement est « OpenMind Studio » et l’espace de travail est « MyWorkspace ». 

3. Sous le nœud de l’espace de travail, cliquez avec le bouton droit sur le nœud **Calcul**, puis choisissez **Create Compute** (Créer un calcul).

4. Choisissez le type de cible de calcul dans la liste. 

5. Dans le champ, entrez un nom unique pour cette cible de calcul et spécifiez la taille de la machine virtuelle.

6. Dans le fichier config JSON qui s’ouvre dans un nouvel onglet, indiquez des propriétés avancées. 

7. Après avoir configuré votre cible de calcul, cliquez sur **Terminer** dans l’angle inférieur droit.

Voici un exemple pour Azure Batch AI : [ ![Créer un calcul Azure Batch AI dans VS Code](./media/vscode-tools-for-ai/createcompute.gif)](./media/vscode-tools-for-ai/createcompute.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>Utiliser des services de calcul à distance pour les expériences dans VS Code

Pour utiliser une cible de calcul à distance au moment de l’apprentissage, vous devez créer un fichier de configuration de série de tests. Ce fichier indique à Azure Machine Learning non seulement où exécuter l’expérience, mais également comment préparer l’environnement.

#### <a name="the-run-configuration-file"></a>Fichier de configuration de série de tests

L’extension VS Code va créer automatiquement une configuration de série de tests pour votre environnement **local** et votre environnement **docker** sur l’ordinateur local.

Il s’agit d’un extrait de code issu du fichier de configuration de série de tests par défaut.

Si vous souhaitez installer l’ensemble de vos bibliothèques et dépendances vous-même, définissez `userManagedDependencies: True`. Par la suite, les exécutions d’expériences en local utiliseront votre environnement Python par défaut comme indiqué par l’extension Python VS Code.

```yaml
# user_managed_dependencies=True indicates that the environment will be user managed. False indicates that AzureML will manage the user environment.
    userManagedDependencies: False
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

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

## <a name="train-and-tune-models"></a>Effectuer l’apprentissage des modèles et les ajuster

Utiliser Azure Machine Learning à partir de Visual Studio Code pour rapidement itérer votre code, le parcourir et le déboguer, puis utiliser la solution de contrôle de code source de votre choix. 

**Pour exécuter votre expérience avec Azure Machine Learning :**

1. Cliquez sur l’icône Azure dans la barre d’activités Visual Studio Code. La barre latérale Azure : Machine Learning apparaît.

1. Dans l’arborescence, développez l’abonnement Azure et l’espace de travail du service Azure Machine Learning. Dans l’image animée, le nom de l’abonnement est « OpenMind Studio » et l’espace de travail est « MyWorkspace ». 

1. Sous le nœud d’espace de travail, développez le nœud **Calcul** et effectuez un clic droit sur la méthode de calcul **Run Config** que vous voulez utiliser. 

1. Sélectionnez **Run Experiment** (Exécuter l’expérience).

1. Cliquez sur **View Experiment Run** (Afficher l’exécution d’expérience) pour afficher le portail Azure Machine Learning intégré et ainsi surveiller vos exécutions et consulter les modèles formés.

   [![Exécuter des expériences Machine Learning depuis VS Code](./media/vscode-tools-for-ai/runexperiment.gif)](./media/vscode-tools-for-ai/runexperiment.gif#lightbox)

## <a name="deploy-and-manage-models"></a>Déployer et gérer des modèles
Azure Machine Learning permet de déployer et de gérer vos modèles Machine Learning dans le cloud et en périphérie. 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>Enregistrer votre modèle auprès d’Azure Machine Learning à partir de Visual Studio Code

Maintenant que vous avez effectué l’apprentissage de votre modèle, vous pouvez l’inscrire dans votre espace de travail.
Les modèles inscrits peuvent être suivis et déployés.

**Pour enregistrer votre modèle :**

1. Cliquez sur l’icône Azure dans la barre d’activités Visual Studio Code. La barre latérale Azure : Machine Learning apparaît.

1. Dans l’arborescence, développez l’abonnement Azure et l’espace de travail du service Azure Machine Learning.

1. Sous le nœud d’espace de travail, cliquez avec le bouton droit sur **Modèles**, puis choisissez **Register Model** (Inscrire le modèle).

1. À partir de cette liste, choisissez si vous souhaitez charger un **fichier de modèle** (pour les modèles uniques) ou un **dossier modèle** (pour les modèles avec plusieurs fichiers, par exemple Tensorflow). 

1. Utilisez la boîte de dialogue de sélection des fichiers pour sélectionner le fichier ou le dossier.

   [![Calcul](./media/vscode-tools-for-ai/registermodel.gif)](./media/vscode-tools-for-ai/registermodel.gif#lightbox)

> [!Warning]
> Pour l’instant, supprimez les balises du fichier json généré.

### <a name="deploy-your-service-from-vs-code"></a>Déployer votre service depuis Visual Studio Code

À l’aide de VS Code, vous pouvez déployer votre service web sur :
+ Azure Container Instance (ACI) : à des fins de test
+ Azure Kubernetes Service (AKS) : en production 

Vous n’avez pas besoin de créer de conteneur ACI pour les tests, car cette création s’effectue à la volée. Toutefois, les clusters AKS doivent être configurés à l’avance. 

Découvrez-en davantage sur le [déploiement avec Azure Machine Learning](how-to-deploy-and-where.md) de manière générale.

**Pour déployer un service web :**

1. Cliquez sur l’icône Azure dans la barre d’activités Visual Studio Code. La barre latérale Azure : Machine Learning apparaît.

1. Dans l’arborescence, développez votre abonnement Azure et l’espace de travail du service Azure Machine Learning.

1. Sous le nœud d’espace de travail, développez le nœud **Modèles**.

1. Cliquez sur le modèle que vous souhaitez déployer avec le bouton droit, puis choisissez la commande **Deploy Service from Registered Model** (Déployer le service depuis le modèle inscrit) dans le menu contextuel.

1. Dans la palette de commandes VS Code, choisissez la cible de calcul sur laquelle déployer à partir de la liste. 

1. Dans le champ, entrez un nom pour ce service. 

1. Dans la boîte de dialogue située dans l’angle inférieur droit, cliquez sur **Parcourir** et sélectionnez votre script de notation. La boîte de dialogue se ferme.

1. Si vous possédez un fichier Docker local, cliquez sur **Parcourir** dans la deuxième boîte de dialogue qui s’affiche. 

   Si vous annulez la boîte de dialogue et que vous ne spécifiez aucun fichier Docker local, un fichier « Azure Machine Learning » est utilisé par défaut.

1. Dans la troisième boîte de dialogue qui s’affiche, cliquez sur **Parcourir** et sélectionnez le chemin d’accès du fichier local conda ou indiquez le chemin d’accès du fichier ultérieurement dans l’éditeur json.

1. Si vous possédez un fichier schema.json que vous souhaitez utiliser, cliquez sur **Parcourir** dans la quatrième boîte de dialogue qui s’affiche et sélectionnez le fichier.

Le service web est désormais déployé.

Voici un exemple d’instance de conteneur Azure : [ ![Azure Container Instance depuis Visual Studio Code](./media/vscode-tools-for-ai/deploy.gif)](./media/vscode-tools-for-ai/deploy.gif#lightbox)

## <a name="next-steps"></a>Étapes suivantes

Pour une procédure détaillée d’apprentissage avec Machine Learning en dehors de Visual Studio Code, consultez le [Didacticiel : Former des modèles avec Azure Machine Learning](tutorial-train-models-with-aml.md).

Pour une procédure détaillée sur la modification, l’exécution et le débogage de code en local, consultez le [Didacticiel Python Hello World](https://code.visualstudio.com/docs/python/python-tutorial).
