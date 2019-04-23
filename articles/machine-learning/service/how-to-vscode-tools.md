---
title: Utiliser Visual Studio Code avec
titleSuffix: Azure Machine Learning service
description: Découvrez comment installer Azure Machine Learning pour Visual Studio Code et créer une expérience simple dans Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: bbeb0dbbd5e9c919eda4b298dc5bee31965e9bac
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995249"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Premiers pas avec Azure Machine Learning pour Visual Studio Code

Dans cet article, vous allez apprendre à utiliser l’extension **Azure Machine Learning pour Visual Studio Code** pour effectuer l’apprentissage et déployer des modèles de Machine Learning et de Deep Learning avec Azure Machine Learning service dans Visual Studio Code (VS Code).

Service Azure Machine Learning prend en charge l’exécution d’expériences localement et sur les cibles de calcul à distance. Pour chaque expérience, vous effectuez le suivi de plusieurs exécutions aussi souvent que vous le voulez. L’objectif est ici de tester de manière itérative différentes techniques, les hyperparamètres, etc. Vous pouvez utiliser Azure Machine Learning pour effectuer le suivi des métriques personnalisées et des exécutions afin de favoriser la reproductibilité et les capacités d’audit dans le domaine de la science des données.

De même, vous pouvez déployer ces modèles pour vos besoins de test et de production.

## <a name="prerequisites"></a>Conditions préalables

+ Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree) dès aujourd’hui.

+ Visual Studio Code doit être installé. VS Code est un éditeur de code source léger mais puissant qui s’exécute sur votre bureau. Il est fourni avec une prise en charge intégrée pour Python et bien plus encore.  [Apprenez à installer VS Code](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Installez Python 3.5 ou version ultérieure](https://www.anaconda.com/download/).


## <a name="install-the-azure-machine-learning-for-vs-code-extension"></a>Installer l’extension Azure Machine Learning pour VS Code

Lorsque vous installez l’extension **Azure Machine Learning**, deux autres extensions sont automatiquement installées (si vous avez accès à Internet). Il s’agit de l’extension [Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) et de l’extension [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

Pour utiliser Azure Machine Learning, nous devons convertir VS Code en un IDE Python. L’utilisation de [Python dans Visual Studio Code](https://code.visualstudio.com/docs/languages/python) requiert l’extension Microsoft Python, qui est automatiquement installée avec Azure Machine Learning. L’extension permet de convertir VS Code en un excellent IDE et fonctionne sur n’importe quel système d’exploitation avec divers interpréteurs Python. Elle tire parti de toute la puissance de VS Code pour proposer des fonctionnalités d’auto-complétion et IntelliSense, de linting, de débogage et de test unitaire, ainsi que la possibilité de basculer facilement entre les environnements Python, y compris les environnements virtuels et conda. Consultez cette procédure détaillée sur la modification, l’exécution et le débogage de code Python dans le [Didacticiel Python Hello World](https://code.visualstudio.com/docs/python/python-tutorial).

**Installer l’extension Azure Machine Learning :**

1. Lancez VS Code.

1. Dans un navigateur, rendez-vous sur : [extension Azure Machine Learning pour Visual Studio Code (version préliminaire)](https://aka.ms/vscodetoolsforai)

1. Sur cette page web, cliquez sur **Installer**. 

1. Dans l’onglet de l’extension, cliquez sur **Installer**.

1. Un onglet Accueil s’ouvre dans Visual Studio Code pour l’extension et le symbole de Azure (décrit dans la zone rouge dans l’image ci-dessous) est ajouté à la barre d’activités.

   ![Icône Azure dans la barre d’activités Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Dans la boîte de dialogue, cliquez sur **Se connecter** et suivez les instructions à l’écran pour vous authentifier avec Azure. 
   
   L’extension Azure Account, qui a été installée avec l’extension Azure Machine Learning pour VS Code, vous permet de vous authentifier avec votre compte Azure. Consultez la liste des commandes sur la page de [l’extension Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Découvrez [l’extension IntelliCode pour VS Code (préversion)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode fournit un ensemble de fonctionnalités assistées par AI pour IntelliSense dans Python, telles que la déduction des auto-complétions les plus pertinentes en fonction du contexte de code actuel.

## <a name="azure-ml-sdk-installation"></a>Installation du kit SDK Azure Machine Learning

1. Assurez-vous que Python 3.5 ou version supérieure est installé et reconnu par VS Code. Si vous l’installez maintenant, redémarrez VS Code et sélectionnez un interpréteur Python en utilisant les instructions fournies sur https://code.visualstudio.com/docs/python/python-tutorial.

1. Dans la fenêtre du terminal intégré, indiquez l’interpréteur Python à utiliser ou appuyez sur **Entrée** pour utiliser votre interpréteur Python par défaut.

   ![Choisir l’interpréteur](./media/vscode-tools-for-ai/python.png)

1. Dans le coin inférieur droit de la fenêtre, une notification indique que le SDK Azure ML en cours d’installation automatique.    Un environnement Python privé local est créé avec la configuration requise de Visual Studio Code pour travailler avec le service Azure Machine Learning.

   ![Installer le SDK Azure Machine Learning pour Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Prise en main d’Azure Machine Learning

Avant de commencer à effectuer l’apprentissage et à déployer les modèles Machine Learning à l’aide de VS Code, vous devez créer un [espace de travail de service Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace) dans le cloud pour héberger vos modèles et ressources. Découvrez comment en créer un, et créez votre première expérience dans cet espace de travail.

1. Cliquez sur l’icône Azure dans la barre d’activités Visual Studio Code. La barre latérale Azure Machine Learning apparaît.

   [![installer](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Cliquez avec le bouton droit sur votre abonnement Azure et sélectionnez **Créer un espace de travail**. Une liste s’affiche. Dans l’image animée, le nom de l’abonnement est « Free Trial » (Essai gratuit) et l’espace de travail est « TeamWorkspace » (Espace de travail d’équipe). 

1. Sélectionnez un groupe de ressources existant dans la liste ou créez-en un à l’aide de l’Assistant dans la palette de commandes.

1. Dans le champ, tapez un nom unique et clair pour votre nouvel espace de travail. Dans les captures d’écran, l’espace de travail s’appelle « TeamWorkspace » (Espace de travail d’équipe).

1. Appuyez sur Entrée pour créer l’espace de travail. Il apparaît dans l’arborescence située sous le nom de l’abonnement.

1. Cliquez avec le bouton droit sur le nœud « Experiment » (Expérience), et choisissez **Create Experiment** (Créer une expérience) dans le menu contextuel.  Les expériences suivent vos exécutions à l’aide d’Azure Machine Learning.

1. Dans le champ, entrez un nom pour votre expérience. Dans les captures d’écran, l’expérience s’appelle « MNIST ».
 
1. Appuyez sur Entrée pour créer l’expérience. Elle apparaît dans l’arborescence située sous le nom de l’espace de travail.

1. Vous pouvez cliquer avec le bouton droit sur une expérience dans un espace de travail et sélectionner « Définir comme expérience active ». L’expérience « **Active** » est celle que vous utilisez actuellement, et votre dossier ouvert dans Visual Studio Code sera lié à cette expérience dans le cloud. Ce dossier doit contenir vos scripts Python locaux.

   Maintenant que toutes vos expériences s’exécutent dans votre expérience, l’ensemble de vos métriques clés sont stockées dans l’historique des expériences et les modèles dont vous effectuez l’entraînement sont chargés automatiquement dans Azure Machine Learning et stockés dans vos journaux d’activité et métriques d’expérience.

   [![Joindre un dossier dans VS Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


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

L’extension VS Code va créer automatiquement une cible de calcul locale et des configurations de série de tests pour votre environnement **local** et votre environnement **docker** sur l’ordinateur local. Vous trouverez les fichiers de configuration d’exécution sous le nœud cible de calcul associées. 

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

Par défaut, un nouvel environnement conda est créé pour vous, et vos dépendances d’installation sont managées. Toutefois, vous devez spécifier vos dépendances et leurs versions dans le fichier `aml_config/conda_dependencies.yml`. 

Il s’agit d’un extrait de code issu du fichier « aml_config/conda_dependencies.yml ». Par exemple, vous pouvez spécifier « tensorflow = 1.12.0 » comme ci-dessous. Si vous ne spécifiez pas la version de la dépendance, la version la plus récente sera utilisée.  
Vous pouvez ajouter des dépendances supplémentaires dans le fichier config.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow=1.12.0

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

### <a name="use-keyboard-shortcuts"></a>Utiliser les raccourcis clavier

Comme la plupart du temps dans VS Code, les fonctionnalités Azure Machine Learning dans VS Code sont accessibles depuis le clavier. La combinaison de touches la plus importante à retenir est « Ctrl+Maj+P », qui permet d’afficher la palette de commandes. Depuis celle-ci, vous avez accès à toutes les fonctionnalités de VS Code, y compris aux raccourcis clavier pour les opérations les plus courantes.

[![Raccourcis clavier de Azure Machine Learning pour VS Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Étapes suivantes

Pour une procédure détaillée d’apprentissage avec Machine Learning en dehors de VS Code, consultez le [Tutoriel : Former des modèles avec Azure Machine Learning](tutorial-train-models-with-aml.md).

Pour une procédure détaillée sur la modification, l’exécution et le débogage de code en local, consultez le [Didacticiel Python Hello World](https://code.visualstudio.com/docs/python/python-tutorial).
