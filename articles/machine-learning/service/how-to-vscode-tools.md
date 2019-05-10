---
title: Utilisez Visual Studio Code pour l’apprentissage
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
ms.openlocfilehash: 19873256f8253fff75cfd42df7b876106a9e98e5
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464792"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Premiers pas avec Azure Machine Learning pour Visual Studio Code

Dans cet article, vous allez apprendre à utiliser l’extension Azure Machine Learning pour Visual Studio Code pour former et déployer l’apprentissage automatique et à l’aide du service Azure Machine Learning dans Visual Studio Code, les modèles d’apprentissage profond.

Le service Azure Machine Learning prend en charge les expériences que vous exécutez localement et sur les cibles de calcul à distance. Pour chaque expérience, vous effectuez le suivi de plusieurs exécutions aussi souvent que vous le voulez. L’objectif est ici de tester de manière itérative différentes techniques, les hyperparamètres, etc. Vous pouvez utiliser Azure Machine Learning pour effectuer le suivi des métriques personnalisées et des exécutions afin de favoriser la reproductibilité et les capacités d’audit dans le domaine de la science des données.

Vous pouvez également déployer ces modèles pour vos besoins de test et de production.

## <a name="prerequisites"></a>Conditions préalables

+ Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez le [version payante ou gratuite du service Azure Machine Learning](https://aka.ms/AMLFree).

+ Visual Studio Code doit être installé. Visual Studio Code est un éditeur de code source simple mais puissant qui s’exécute sur votre bureau. Il est fourni avec une prise en charge intégrée pour Python et d’autres langages de programmation. Si vous n’avez pas déjà installé Visual Studio Code, [Apprenez](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Installer Python 3.5 ou version ultérieure](https://www.anaconda.com/download/).


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>Installer l’extension pour Azure Machine Learning pour Visual Studio Code

Lorsque vous installez l’extension Azure Machine Learning, deux plusieurs extensions sont automatiquement installées (si vous avez accès à internet). Ils sont le [extension de compte Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) et [extension Python de Microsoft](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

Pour travailler avec Azure Machine Learning, vous devez activer le Code de Visual Studio dans un environnement de développement intégré Python (IDE). Vous avez besoin de l’extension Microsoft Python à utiliser [Python dans Visual Studio Code](https://code.visualstudio.com/docs/languages/python). Cette extension est automatiquement installée avec l’extension d’Azure Machine Learning. L’extension rend Visual Studio Code un excellent IDE, et fonctionne sur n’importe quel système d’exploitation avec un large éventail des interpréteurs Python. L’extension Python de Microsoft utilise toute la puissance de Visual Studio Code pour fournir la saisie semi-automatique, IntelliSense, linting, débogage et effectuer des tests unitaires. L’extension vous permet également de basculer facilement entre les environnements Python, y compris virtuel et des environnements conda. Pour plus d’informations sur la modification, en cours d’exécution et débogage de code Python, consultez le [didacticiel de hello-world Python](https://code.visualstudio.com/docs/python/python-tutorial).

Pour installer l’extension Azure Machine Learning :

1. Ouvrez Visual Studio Code.

1. Dans un navigateur web, accédez à [Azure Machine Learning pour l’extension Visual Studio Code (version préliminaire)](https://aka.ms/vscodetoolsforai).

1. Dans cette page web, sélectionnez **installer**. 

1. Sous l’onglet de l’extension, sélectionnez **installer**.

1. Un onglet Accueil pour l’extension s’ouvre dans Visual Studio Code, et le symbole Azure (entourée en rouge dans la capture d’écran suivante) est ajouté à la barre d’activités.

   ![Icône Azure dans la barre d’activités Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Dans la boîte de dialogue, sélectionnez **Sign In** et suivez les invites pour s’authentifier auprès d’Azure. 
   
   L’extension de compte Azure, ce qui a été installée en même temps que le Azure Machine Learning pour l’extension de Visual Studio Code, vous permet de s’authentifier avec votre compte Azure. Pour obtenir la liste des commandes, consultez la page pour le [extension de compte Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Découvrez le [extension IntelliCode pour Visual Studio Code (version préliminaire)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode fournit un ensemble de fonctionnalités assisté par intelligence artificielle pour IntelliSense dans Python, telles que l’inférence les saisies de semi-automatiques plus pertinentes en fonction du contexte de code en cours.

## <a name="install-the-azure-machine-learning-sdk"></a>Installer le Kit de développement logiciel d’apprentissage Azure

1. Assurez-vous que les Python 3.5 ou version ultérieure est installé et qu’il est reconnu par Visual Studio Code. Si vous installez maintenant, redémarrez Visual Studio Code et [sélectionner un interpréteur Python](https://code.visualstudio.com/docs/python/python-tutorial).

1. Dans la fenêtre du terminal intégré, spécifiez l’interpréteur Python à utiliser. Ou appuyez sur ENTRÉE pour utiliser votre interpréteur de Python par défaut.

   ![Choisir l’interpréteur](./media/vscode-tools-for-ai/python.png)

1. Dans le coin inférieur droit de la fenêtre, une notification s’affiche, indiquant que le Kit de développement logiciel Azure Machine Learning est automatiquement installé. L’environnement Python nouvellement créé est local et privé, et la présence des composants Visual Studio Code pour travailler avec le service Azure Machine Learning.

   ![Installer le kit de développement logiciel (SDK) Azure Machine Learning pour Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Prise en main d’Azure Machine Learning

Avant de commencer la formation et de déploiement de modèles d’apprentissage automatique dans Visual Studio Code, vous devez créer un [espace de travail de service Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace) dans le cloud. Cet espace de travail contient vos modèles et les ressources. 

Pour créer un espace de travail et ajoutez votre première expérience :

1. Dans la barre d’activités Visual Studio Code, sélectionnez l’icône Windows Azure. La barre latérale Azure Machine Learning apparaît.

   [![Créer un espace de travail](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Cliquez avec le bouton droit sur votre abonnement Azure et sélectionnez **Créer un espace de travail**. Une liste s’affiche. Dans l’exemple d’image animée, le nom de l’abonnement est **version d’évaluation gratuite**, et l’espace de travail est **espace de travail**. 

1. Sélectionnez un groupe de ressources dans la liste ou créez-en un à l’aide de l’Assistant dans la palette de commandes.

1. Dans le champ, tapez un nom unique et clair pour votre nouvel espace de travail. Dans l’exemple d’image, l’espace de travail est nommé **espace de travail**.

1. Appuyez sur ENTRÉE pour créer le nouvel espace de travail. Il apparaît dans l’arborescence, sous le nom de l’abonnement.

1. Avec le bouton droit le **expérience** nœud, puis choisissez **créer une expérience** dans le menu contextuel.  Les expériences suivent vos exécutions à l’aide d’Azure Machine Learning.

1. Dans le champ, entrez un nom pour votre expérience. Dans les captures d’écran de l’exemple, l’expérience est nommée **MNIST**.
 
1. Appuyez sur ENTRÉE pour créer la nouvelle expérience. L’expérience s’affiche dans l’arborescence, sous le nom de l’espace de travail.

1. Dans un espace de travail, vous pouvez cliquer sur une expérience pour la définir en tant que le **Active** faire des essais. Le **Active** expérience se trouve votre expérience actuelle. Votre dossier ouvert dans Visual Studio Code sera lié à cette expérience dans le cloud. Ce dossier doit contenir vos scripts Python locaux.

Désormais, vos mesures clés seront stockées au sein de l’historique de l’expérience. De même, les modèles que vous former seront automatiquement téléchargés vers Azure Machine Learning et stockées en même temps que votre expérience de métriques et des journaux. 

[![Joindre un dossier dans Visual Studio Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Créer et gérer des cibles de calcul

Avec Azure Machine Learning pour Visual Studio Code, vous pouvez préparer vos données, former des modèles et les déployer à la fois localement et sur les cibles de calcul à distance.

L’extension prend en charge plusieurs cibles de calcul à distance pour Azure Machine Learning. Pour plus d’informations, consultez la liste complète des prises en charge [cibles de calcul pour Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Créer des cibles de calcul pour Azure Machine Learning dans Visual Studio Code

Pour créer une cible de calcul :

1. Dans la barre d’activités Visual Studio Code, sélectionnez l’icône Windows Azure. La barre latérale Azure Machine Learning apparaît.

2. Dans l’arborescence, développez l’abonnement Azure et l’espace de travail du service Azure Machine Learning. Dans l’image d’exemple suivant, le nom de l’abonnement est **version d’évaluation gratuite**, et l’espace de travail est **espace de travail**. 

3. Sous le nœud de l’espace de travail, cliquez avec le bouton droit sur le nœud **Calcul**, puis choisissez **Create Compute** (Créer un calcul).

4. Choisissez le type de cible de calcul dans la liste. 

5. Dans la palette de commandes, sélectionnez une taille de machine virtuelle.

6. Dans la palette de commandes, dans le champ, entrez un nom pour la cible de calcul. 

7. Dans le fichier de configuration JSON qui s’ouvre dans un nouvel onglet, spécifiez les propriétés avancées. Vous pouvez spécifier des propriétés telles que le nombre maximal de nœuds.

8. Lorsque vous avez terminé la configuration de votre cible de calcul, dans le coin inférieur droit de la fenêtre, sélectionnez **envoyer**.

Voici un exemple montrant comment créer un calcul d’Azure Machine Learning (AMLCompute) :

[![Créer un calcul d’AML dans Visual Studio Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Le fichier de configuration d’exécution

L’extension de Code Visual Studio crée automatiquement une cible de calcul local et les configurations d’exécution pour vos environnements locaux et docker sur votre ordinateur local. Vous trouverez les fichiers de configuration d’exécution sous le nœud cible de calcul associées. 

## <a name="train-and-tune-models"></a>Effectuer l’apprentissage des modèles et les ajuster

Utilisez Azure Machine Learning pour Visual Studio Code (version préliminaire) pour rapidement itérer au sein de votre code, parcourir et déboguer et utiliser votre solution pour le contrôle de code source. 

Pour exécuter votre expérience localement à l’aide d’Azure Machine Learning :

1. Dans la barre d’activités Visual Studio Code, sélectionnez l’icône Windows Azure. La barre latérale Azure Machine Learning apparaît.

1. Dans l’arborescence, développez l’abonnement Azure et l’espace de travail du service Azure Machine Learning. 

1. Sous le nœud d’espace de travail, développez le **calcul** nœud et clic droit la **Config exécuter** du calcul que vous souhaitez utiliser. 

1. Sélectionnez **Run Experiment** (Exécuter l’expérience).

1. Dans l’Explorateur de fichiers, sélectionnez le script que vous souhaitez exécuter. 

1. Sélectionnez **vue expérience exécuter** pour afficher le portail Azure Machine Learning intégré pour surveiller vos exécutions et consultez vos modèles formés.

Voici un exemple montrant comment exécuter une expérience localement :

[![Exécutez une expérience localement](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Utilisation des services de calcul à distance les expériences disponibles dans Visual Studio Code

Pour utiliser une cible de calcul à distance pour l’apprentissage, vous devez créer un fichier de configuration d’exécution. Ce fichier indique à Azure Machine Learning non seulement où exécuter l’expérience, mais également comment préparer l’environnement.

#### <a name="the-conda-dependencies-file"></a>Fichier de dépendance Conda

Par défaut, un nouvel environnement conda est créé pour vous et vos dépendances d’installation sont gérés. Toutefois, vous devez spécifier vos dépendances et leurs versions dans le *aml_config/conda_dependencies.yml* fichier. 

L’extrait de code suivant à partir de la valeur par défaut *aml_config/conda_dependencies.yml* spécifie `tensorflow=1.12.0`. Si vous ne spécifiez pas la version de la dépendance, la dernière version sera utilisée. Vous pouvez ajouter des dépendances supplémentaires dans le fichier config.

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

Pour exécuter votre expérience avec Azure Machine Learning sur un ordinateur cible de calcul :

1. Dans la barre d’activités Visual Studio Code, sélectionnez l’icône Windows Azure. La barre latérale Azure Machine Learning apparaît.

1. Dans l’arborescence, développez l’abonnement Azure et l’espace de travail du service Azure Machine Learning. 

1. Dans la fenêtre de l’éditeur, avec le bouton droit de votre script Python, puis sélectionnez **AML : Exécuter en tant qu’expérience dans Azure**. 

1. Dans la palette de commandes, sélectionnez la cible de calcul. 

1. Dans la palette de commandes, dans le champ, entrez le nom de la configuration d’exécution. 

1. Modifier le *conda_dependencies.yml* fichier pour spécifier les dépendances de runtime de l’expérience. Puis, dans le coin inférieur droit de la fenêtre, sélectionnez **envoyer**. 

1. Sélectionnez **vue expérience exécuter** pour afficher le portail Azure Machine Learning intégré pour surveiller vos exécutions et consultez vos modèles formés.

Voici un exemple montrant comment exécuter une expérience sur une cible de calcul à distance :

[![Exécutez une expérience sur une cible distante](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Déployer et gérer des modèles
Dans Azure Machine Learning, vous pouvez déployer et gérer vos modèles machine learning dans le cloud et à la périphérie. 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Enregistrer votre modèle Azure Machine Learning à partir de Visual Studio Code

Maintenant que vous avez formé votre modèle, vous pouvez l’inscrire dans votre espace de travail. Vous pouvez effectuer le suivi et déployer des modèles inscrits.

Pour enregistrer votre modèle :

1. Dans la barre d’activités Visual Studio Code, sélectionnez l’icône Windows Azure. La barre latérale Azure Machine Learning apparaît.

1. Dans l’arborescence, développez l’abonnement Azure et l’espace de travail du service Azure Machine Learning.

1. Sous le nœud d’espace de travail, cliquez avec le bouton droit sur **Modèles**, puis choisissez **Register Model** (Inscrire le modèle).

1. Dans la palette de commandes, dans le champ, entrez un nom de modèle. 

1. Dans la liste, choisissez s’il faut télécharger un **fichier de modèle** (pour les modèles uniques) ou un **dossier modèle** (pour les modèles avec plusieurs fichiers, telles que TensorFlow). 

1. Sélectionnez votre dossier ou fichier.

1. Lorsque vous avez terminé la configuration des propriétés de votre modèle, dans le coin inférieur droit de la fenêtre, sélectionnez **envoyer**. 

Voici un exemple montrant comment inscrire votre modèle Azure Machine Learning :

[![Inscription d’un modèle dans AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>Déployer votre service depuis Visual Studio Code

Dans Visual Studio Code, vous pouvez déployer votre service web :
+ Azure Container Instances (ACI) pour le test.
+ Azure Kubernetes Service (AKS) pour la production.

Vous n’avez pas besoin de créer un conteneur ACI pour tester à l’avance, car les conteneurs ACI sont créés à la volée. Toutefois, vous n’avez pas besoin de configurer les clusters AKS à l’avance. Pour plus d’informations, consultez [déployer des modèles avec le service Azure Machine Learning](how-to-deploy-and-where.md).

Pour déployer un service web :

1. Dans la barre d’activités Visual Studio Code, sélectionnez l’icône Windows Azure. La barre latérale Azure Machine Learning apparaît.

1. Dans l’arborescence, développez votre abonnement Azure et l’espace de travail du service Azure Machine Learning.

1. Sous le nœud d’espace de travail, développez le nœud **Modèles**.

1. Cliquez sur le modèle que vous souhaitez déployer, puis choisissez **déployer le Service à partir du modèle inscrit** dans le menu contextuel.

1. Dans la palette de commandes, choisissez la cible de calcul que vous souhaitez déployer sur. 

1. Dans la palette de commandes, dans le champ, entrez un nom pour ce service.  

1. Dans la palette de commandes, sélectionnez la touche entrée de votre clavier pour rechercher et sélectionner le fichier de script.

1. Dans la palette de commandes, sélectionnez la touche entrée de votre clavier pour rechercher et sélectionner le fichier de dépendance conda.

1. Lorsque vous avez terminé la configuration des propriétés de votre service, dans le coin inférieur droit de la fenêtre, sélectionnez **envoyer** à déployer. Dans le fichier de propriétés de service, vous pouvez spécifier un fichier docker local ou un fichier schema.json.

Le service web est désormais déployé.

Voici un exemple montrant comment déployer un service web :

[![Déployer un service web](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Utiliser les raccourcis clavier

Vous pouvez utiliser le clavier pour accéder aux fonctionnalités d’Azure Machine Learning dans Visual Studio Code. Le raccourci clavier plus importants à connaître est Ctrl + Maj + P, qui affiche la palette de commandes. À partir de la palette de commandes, vous avez accès à toutes les fonctionnalités de Visual Studio Code, y compris les raccourcis clavier pour les opérations les plus courantes.

[![Raccourcis clavier pour Azure Machine Learning pour Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Étapes suivantes

* Pour une procédure pas à pas montrant comment effectuer l’apprentissage avec Azure Machine Learning en dehors de Visual Studio Code, consultez [didacticiel : Former des modèles avec Azure Machine Learning](tutorial-train-models-with-aml.md).
* Pour une procédure pas à pas montrant comment modifier, exécuter et déboguer le code localement, consultez le [didacticiel de hello-world Python](https://code.visualstudio.com/docs/python/python-tutorial).
