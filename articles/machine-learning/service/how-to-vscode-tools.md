---
title: Utiliser Visual Studio Code pour l'apprentissage automatique
titleSuffix: Azure Machine Learning service
description: Découvrez comment installer Azure Machine Learning pour Visual Studio Code et créer une expérience simple dans Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 7/12/2019
ms.custom: seodec18
ms.openlocfilehash: 3b49def1af7f13e38c3a9daea32d56bf3c633261
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871762"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Premiers pas avec Azure Machine Learning pour Visual Studio Code

Dans cet article, vous apprendrez à utiliser l’extension Azure Machine Learning pour Visual Studio Code afin d’effectuer l’apprentissage et de déployer des modèles de Machine Learning et d’apprentissage profond.

Le service [Azure Machine Learning](overview-what-is-azure-ml.md) permet d’exécuter des expériences localement mais aussi sur des cibles de calcul à distance. Pour chaque expérience, vous effectuez le suivi de plusieurs exécutions aussi souvent que vous le voulez. L’objectif est ici de tester de manière itérative différentes techniques, les hyperparamètres, etc. Vous pouvez utiliser Azure Machine Learning pour effectuer le suivi des métriques personnalisées et des exécutions afin de favoriser la reproductibilité et les capacités d’audit dans le domaine de la science des données.

Vous pouvez également déployer ces modèles pour vos besoins de test et de production.

## <a name="prerequisites"></a>Prérequis

+ Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree).

+ Visual Studio Code doit être installé. Visual Studio Code est un éditeur de code source léger mais puissant qui s’exécute sur votre bureau. Il est fourni avec une prise en charge intégrée pour Python et d’autres langages de programmation. Si vous n'avez pas déjà installé Visual Studio Code, [découvrez comment procéder](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Installez Python 3.5 ou version ultérieure](https://www.anaconda.com/download/).


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>Installer l'extension Azure Machine Learning for Visual Studio Code

Lorsque vous installez l’extension Azure Machine Learning, deux autres extensions sont automatiquement installées (si vous avez accès à Internet). Il s’agit de l’extension [Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) et de l’extension [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

Pour utiliser Azure Machine Learning, vous devez convertir Visual Studio Code en un environnement de développement intégré (IDE) Python. Vous avez besoin de l'extension Microsoft Python pour utiliser [Python dans Visual Studio Code](https://code.visualstudio.com/docs/languages/python). Cette extension est automatiquement installée avec l'extension Azure Machine Learning. L’extension permet de convertir Visual Studio Code en un excellent IDE et fonctionne sur n’importe quel système d’exploitation avec divers interpréteurs Python. L'extension Microsoft Python exploite toute la puissance de Visual Studio Code pour fournir des tests unitaires avec saisie semi-automatique, IntelliSense, de référencement et de débogage. L'extension vous permet également de passer facilement d'un environnement Python à un autre, y compris les environnements virtuels et Conda. Pour plus d'informations sur la modification, l'exécution et le débogage de code Python, consultez le tutoriel [Python Hello World](https://code.visualstudio.com/docs/python/python-tutorial).

Installer l’extension Azure Machine Learning :

1. Ouvrez Visual Studio Code.

1. Dans un navigateur Web, accédez à l’[extension Azure Machine Learning pour Visual Studio Code (préversion)](https://aka.ms/vscodetoolsforai).

1. Sur cette page Web, sélectionnez **Installer**. 

1. Dans l’onglet de l’extension, cliquez sur **Installer**.

1. Un onglet de bienvenue dans l'extension s'ouvre dans Visual Studio Code, et le symbole Azure (souligné en rouge dans la capture d'écran suivante) est ajouté à la barre d'activités.

   ![Icône Azure dans la barre d’activités Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Dans la boîte de dialogue, sélectionnez **Se connecter** et suivez les instructions pour vous authentifier auprès Azure. 
   
   L’extension Azure Account, qui a été installée avec l’extension Azure Machine Learning pour Visual Studio Code, vous permet de vous authentifier avec votre compte d’Azure. Pour obtenir une liste des commandes, consultez la page de l’[extension Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Découvrez [l’extension IntelliCode pour Visual Studio Code (préversion)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode fournit un ensemble de fonctionnalités assistées par AI pour IntelliSense dans Python, telles que la déduction des auto-complétions les plus pertinentes en fonction du contexte de code actuel.

## <a name="install-the-azure-machine-learning-sdk"></a>Installer le Kit de développement logiciel (SDK) Azure Machine Learning

1. Assurez-vous que Python 3.5 ou version supérieure est installé et reconnu par Visual Studio Code. Si vous l’installez maintenant, redémarrez Visual Studio Code et [sélectionnez un interpréteur Python](https://code.visualstudio.com/docs/python/python-tutorial).

1. Dans la fenêtre du terminal intégré, spécifiez l'interpréteur Python à utiliser. Ou sélectionnez Entrée pour utiliser votre interpréteur Python par défaut.

   ![Choisir l’interpréteur](./media/vscode-tools-for-ai/python.png)

1. Dans le coin inférieur droit de la fenêtre, une notification indique que le [SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) est en cours d’installation automatique. L’environnement Python nouvellement créé est local et privé, et dispose de la configuration Visual Studio Code requise pour être utilisé avec le service Azure Machine Learning.

   ![Installer le kit de développement logiciel (SDK) Azure Machine Learning pour Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Prise en main d’Azure Machine Learning

Avant de commencer à effectuer l’apprentissage et à déployer les modèles Machine Learning à l’aide de Visual Studio Code, vous devez créer un [espace de travail de service Azure Machine Learning](concept-workspace.md) dans le cloud. Cet espace de travail contiendra vos modèles et ressources. 

Pour créer un espace de travail et ajouter votre première expérience :

1. Dans la barre d’activités Visual Studio Code, sélectionnez l’icône Azure. La barre latérale Azure Machine Learning apparaît.

   [![Créer un espace de travail](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Cliquez avec le bouton droit sur votre abonnement Azure et sélectionnez **Créer un espace de travail**. Une liste s’affiche. Dans l’exemple d’image animée, le nom de l’abonnement est **Free Trial** (Essai gratuit) et l’espace de travail est **TeamWorkspace** (Espace de travail d’équipe). 

1. Sélectionnez un groupe de ressources dans la liste ou créez-en un à l’aide de l’Assistant dans la palette de commandes.

1. Dans le champ, tapez un nom unique et clair pour votre nouvel espace de travail. Dans l’exemple d’image, l’espace de travail s’appelle **TeamWorkspace** (Espace de travail d’équipe).

1. Sélectionnez Entrée pour créer le nouvel espace de travail. Il apparaît dans l’arborescence située sous le nom de l’abonnement.

1. Cliquez avec le bouton droit sur le nœud **Experiment** (Expérience), et choisissez **Create Experiment** (Créer une expérience) dans le menu contextuel.  Les expériences suivent vos exécutions à l’aide d’Azure Machine Learning.

1. Dans le champ, entrez un nom pour votre expérience. Dans les exemples de captures d’écran, l’expérience s’appelle **MNIST**.
 
1. Sélectionnez Entrée pour créer la nouvelle l’expérience. L’expérience apparaît dans l’arborescence située sous le nom de l’espace de travail.

1. Dans un espace de travail, vous pouvez cliquer avec le bouton droit sur une expérience pour la définir comme **Active**. L'expérience **Active** est votre expérience actuelle. Votre dossier ouvert dans Visual Studio Code sera associé à cette expérience dans le cloud. Ce dossier doit contenir vos scripts Python locaux.

Vos métriques clés seront maintenant stockées dans l'historique de l'expérience. De même, les modèles dont vous effectuerez l'apprentissage seront automatiquement chargés vers Azure Machine Learning et stockés avec vos métriques et journaux d'expériences. 

[![Joindre un dossier dans Visual Studio Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Créer et gérer des cibles de calcul

Avec Azure Machine Learning pour Visual Studio Code, vous pouvez préparer vos données, effectuer l’apprentissage des modèles et les déployer à la fois en local et sur des cibles de calcul à distance.

L’extension prend en charge plusieurs cibles de calcul à distance pour Azure Machine Learning. Pour plus d’informations, consultez la liste complète des [cibles de calcul prises en charge pour Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Créer des cibles de calcul pour Azure Machine Learning dans Visual Studio Code

Pour créer une cible de calcul :

1. Dans la barre d’activités Visual Studio Code, sélectionnez l’icône Azure. La barre latérale Azure Machine Learning apparaît.

2. Dans l’arborescence, développez l’abonnement Azure et l’espace de travail du service Azure Machine Learning. Dans l’exemple d’image suivant, le nom de l’abonnement est **Free Trial** (Essai gratuit) et l’espace de travail est **TeamWorkspace** (Espace de travail d’équipe). 

3. Sous le nœud de l’espace de travail, cliquez avec le bouton droit sur le nœud **Calcul**, puis choisissez **Create Compute** (Créer un calcul).

4. Choisissez le type de cible de calcul dans la liste. 

5. Dans la palette de commandes, sélectionnez une taille de machine virtuelle.

6. Dans la palette de commandes, entrez un nom pour la cible de calcul dans le champ. 

7. Dans le fichier config JSON qui s’ouvre dans un nouvel onglet, indiquez des propriétés avancées. Vous pouvez spécifier des propriétés telles que le nombre maximal de nœuds.

8. Lorsque vous avez terminé de configurer votre cible de calcul, dans le coin inférieur droit de la fenêtre, sélectionnez **Soumettre**.

Voici un exemple de création d’un calcul Azure Machine Learning (AMLCompute) :

[![Créer un calcul AML dans Visual Studio Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox).

#### <a name="the-run-configuration-file"></a>Fichier de configuration de série de tests

L’extension Visual Studio Code crée automatiquement une cible de calcul locale et des configurations de série de tests pour votre environnement local et votre environnement docker sur l’ordinateur local. Vous trouverez les fichiers de configuration d’exécution sous le nœud cible du calcul associé. 

## <a name="train-and-tune-models"></a>Effectuer l’apprentissage des modèles et les ajuster

Utilisez Azure Machine Learning pour Visual Studio Code (préversion) pour rapidement itérer votre code, le parcourir et le déboguer, puis utilisez votre solution de contrôle de code source. 

Pour exécuter votre expérience localement en utilisant Azure Machine Learning :

1. Dans la barre d’activités Visual Studio Code, sélectionnez l’icône Azure. La barre latérale Azure Machine Learning apparaît.

1. Dans l’arborescence, développez l’abonnement Azure et l’espace de travail du service Azure Machine Learning. 

1. Sous le nœud d’espace de travail, développez le nœud **Calcul** et effectuez un clic droit sur la méthode de calcul **Run Config** que vous voulez utiliser. 

1. Sélectionnez **Run Experiment** (Exécuter l’expérience).

1. Dans l’Explorateur de fichiers, sélectionnez le script à exécuter. 

1. Sélectionnez **View Experiment Run** (Afficher l’exécution d’expérience) pour afficher le portail Azure Machine Learning intégré et ainsi surveiller vos exécutions et consulter les modèles formés.

Voici un exemple d’expérience exécutée localement :

[![Exécuter une expérience localement](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Utiliser des services de calcul à distance pour les expériences dans Visual Studio Code

Pour utiliser une cible de calcul à distance au moment de l’apprentissage, vous devez créer un fichier de configuration de série de tests. Ce fichier indique à Azure Machine Learning non seulement où exécuter l’expérience, mais également comment préparer l’environnement.

#### <a name="the-conda-dependencies-file"></a>Fichier de dépendance Conda

Par défaut, un nouvel environnement conda est créé pour vous, et vos dépendances d’installation sont managées. Toutefois, vous devez spécifier vos dépendances et leurs versions dans le fichier *aml_config/conda_dependencies.yml*. 

L’extrait de code suivant issu du fichier par défaut *aml_config/conda_dependencies.yml* spécifie `tensorflow=1.12.0`. Si vous ne spécifiez pas la version de la dépendance, la version la plus récente sera utilisée. Vous pouvez ajouter des dépendances supplémentaires dans le fichier config.

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

Pour exécuter votre expérience avec Azure Machine Learning sur un ordinateur de calcul distant

1. Dans la barre d’activités Visual Studio Code, sélectionnez l’icône Azure. La barre latérale Azure Machine Learning apparaît.

1. Dans l’arborescence, développez l’abonnement Azure et l’espace de travail du service Azure Machine Learning. 

1. Dans la fenêtre de l’éditeur, cliquez avec le bouton droit sur le script Python, puis sélectionnez **AML : Exécuter en tant qu’expérience dans Azure**. 

1. Dans la palette de commandes, sélectionnez la cible de calcul. 

1. Dans la palette de commandes, entrez le nom de la configuration d’exécution dans le champ. 

1. Modifiez le fichier *conda_dependencies.yml* pour spécifier les dépendances d'exécution de l'expérience. Puis, dans le coin inférieur droit de la fenêtre, sélectionnez **Soumettre**. 

1. Sélectionnez **View Experiment Run** (Afficher l’exécution d’expérience) pour afficher le portail Azure Machine Learning intégré et ainsi surveiller vos exécutions et consulter les modèles formés.

Voici un exemple montrant comment exécuter une expérience sur une cible de calcul distante :

[![Exécuter une expérience sur une cible distante](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Déployer et gérer des modèles
Azure Machine Learning vous permet de déployer et de gérer vos modèles Machine Learning dans le cloud et en périphérie. 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Enregistrer votre modèle auprès d’Azure Machine Learning à partir de Visual Studio Code

Maintenant que vous avez effectué l’apprentissage de votre modèle, vous pouvez l’inscrire dans votre espace de travail. Vous pouvez suivre et déployer des modèles enregistrés.

Pour enregistrer votre modèle :

1. Dans la barre d’activités Visual Studio Code, sélectionnez l’icône Azure. La barre latérale Azure Machine Learning apparaît.

1. Dans l’arborescence, développez l’abonnement Azure et l’espace de travail du service Azure Machine Learning.

1. Sous le nœud d’espace de travail, cliquez avec le bouton droit sur **Modèles**, puis choisissez **Register Model** (Inscrire le modèle).

1. Dans la palette de commandes, entrez un nom de modèle dans le champ. 

1. À partir de cette liste, choisissez si vous souhaitez charger un **fichier de modèle** (pour les modèles uniques) ou un **dossier modèle** (pour les modèles avec plusieurs fichiers, par exemple TensorFlow). 

1. Sélectionnez votre dossier ou fichier.

1. Lorsque vous avez terminé de configurer les propriétés de votre modèle, dans le coin inférieur droit de la fenêtre, sélectionnez **Soumettre**. 

Voici un exemple montrant comment enregistrer votre modèle auprès d’Azure Machine Learning :

[![Inscription d’un modèle dans AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>Déployer votre service à partir de Visual Studio Code

Dans Visual Studio Code, vous pouvez déployer votre service web sur :
+ Azure Container Instances (ACI) à des fins de test.
+ Azure Kubernetes Service (AKS) en production.

Vous n’avez pas besoin de créer un conteneur ACI en vue de le tester au préalable, car les conteneurs ACI sont créés instantanément. Toutefois, vous devez configurer les clusters AKS à l’avance. Pour plus d’informations, consultez [Déployer des modèles avec le service Azure Machine Learning](how-to-deploy-and-where.md).

Pour déployer un service web :

1. Dans la barre d’activités Visual Studio Code, sélectionnez l’icône Azure. La barre latérale Azure Machine Learning apparaît.

1. Dans l’arborescence, développez votre abonnement Azure et l’espace de travail du service Azure Machine Learning.

1. Sous le nœud d’espace de travail, développez le nœud **Modèles**.

1. Cliquez sur le modèle que vous souhaitez déployer avec le bouton droit, puis choisissez **Deploy Service from Registered Model** (Déployer le service depuis le modèle inscrit) dans le menu contextuel.

1. Dans la palette de commandes, choisissez la cible de calcul sur laquelle effectuer le déploiement. 

1. Dans la palette de commandes, entrez un nom pour ce service dans le champ.  

1. Dans la palette de commandes, appuyez sur la touche entrée de votre clavier pour parcourir et sélectionner le fichier de script.

1. Dans la palette de commandes, appuyez sur la touche entrée de votre clavier pour parcourir et sélectionner le fichier de dépendance Conda.

1. Lorsque vous avez terminé de configurer les propriétés de votre service, dans le coin inférieur droit de la fenêtre, sélectionnez **Soumettre** pour procéder au déploiement. Dans le fichier de propriétés de service, vous pouvez spécifier un fichier docker local ou un fichier schema.json.

Le service web est désormais déployé.

Voici un exemple de déploiement d'un service web :

[![Déployer un service web](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Utiliser les raccourcis clavier

Vous pouvez utiliser le clavier pour accéder aux fonctionnalités Azure Machine Learning dans Visual Studio Code. Le raccourci clavier le plus important à retenir est Ctrl+Maj+P, qui permet d’afficher la palette de commandes. Depuis celle-ci, vous avez accès à toutes les fonctionnalités de Visual Studio Code, y compris aux raccourcis clavier pour les opérations les plus courantes.

[![Raccourcis clavier d’Azure Machine Learning pour Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Étapes suivantes

* Pour une procédure détaillée sur l’apprentissage automatique avec Azure Machine Learning en dehors de Visual Studio Code, consultez le [tutoriel : Former des modèles avec Azure Machine Learning](tutorial-train-models-with-aml.md).
* Pour une procédure détaillée sur la modification, l’exécution et le débogage de code localement, consultez le [didacticiel Python Hello World](https://code.visualstudio.com/docs/python/python-tutorial).
