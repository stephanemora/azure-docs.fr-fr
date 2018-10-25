---
title: Utiliser l’extension Visual Studio Code Tools for AI avec Azure Machine Learning
description: Apprenez-en davantage sur Visual Studio Code Tools for AI et sur la façon d’effectuer l’apprentissage et le déploiement des modèles de Machine Learning et d’apprentissage profond avec le service Azure Machine Learning dans VS Code.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.reviewer: jmartens
ms.date: 10/1/2018
ms.openlocfilehash: a2f37cffb37ce7008c3e372763784240e0d4250b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945550"
---
# <a name="vs-code-tools-for-ai-get-started-with-azure-machine-learning-from-visual-studio-code"></a>VS Code Tools for AI : Prendre en main Azure Machine Learning depuis Visual Studio Code

Dans cet article, vous allez en apprendre davantage sur l’extension Visual Studio Code (VS Code), **Tools for AI**, et sur la façon d’effectuer l’apprentissage et le déploiement des modèles de Machine Learning et d’apprentissage profond avec le service Azure Machine Learning dans VS Code.

L’extension Tools for AI dans Visual Studio Code vous permet d’utiliser le service Azure Machine Learning pour préparer vos données, effectuer l’apprentissage et tester les modèles Machine Learning sur des cibles de calcul locales et distantes, déployer ces modèles et suivre les expériences et les métriques personnalisées.

## <a name="prerequisite"></a>Configuration requise

+ Visual Studio Code doit être installé. VS Code est un éditeur de code source léger mais puissant qui s’exécute sur votre bureau. Il est fourni avec une prise en charge intégrée pour Python et bien plus encore.  [Apprenez à installer VS Code](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Installez Python 3.5 ou version ultérieure](https://www.anaconda.com/download/).

+ Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="install-vs-code-tools-for-ai-extension"></a>Installer l’extension VS Code Tools for AI

Lorsque vous installez l’extension **Tools for AI**, deux autres extensions sont automatiquement installées (si vous avez accès à Internet). Il s’agit de l’extension [Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) et de l’extension [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

Pour utiliser Azure Machine Learning, nous devons convertir VS Code en un IDE Python. L’utilisation de [Python dans Visual Studio Code](https://code.visualstudio.com/docs/languages/python) requiert l’extension Microsoft Python, qui est automatiquement installée avec Tools for AI. L’extension permet de convertir VS Code en un excellent IDE et fonctionne sur n’importe quel système d’exploitation avec divers interpréteurs Python. Elle tire parti de toute la puissance de VS Code pour proposer des fonctionnalités d’auto-complétion et IntelliSense, de linting, de débogage et de test unitaire, ainsi que la possibilité de basculer facilement entre les environnements Python, y compris les environnements virtuels et conda. Consultez cette procédure détaillée sur la modification, l’exécution et le débogage de code Python dans le [Didacticiel Python Hello World](https://code.visualstudio.com/docs/python/python-tutorial).

**Pour installer l’extension Tools for AI :**

1. Lancez VS Code.

1. Dans un navigateur, rendez-vous sur : http://aka.ms/vscodetoolsforai. 

1. Sur cette page web, cliquez sur **Installer**. 

1. Dans l’onglet de l’extension, cliquez sur **Installer**.

1. Un onglet de bienvenue s’ouvre dans VS Code pour l’extension, et le symbole Azure est ajouté à la barre d’activités.

   ![Icône Azure dans la barre d’activités Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Dans la boîte de dialogue, cliquez sur **Se connecter** et suivez les instructions à l’écran pour vous authentifier avec Azure. 
   
   L’extension Azure Account, qui a été installée avec l’extension VS Code Tools for AI, vous permet de vous authentifier avec votre compte Azure. Consultez la liste des commandes sur la page de [l’extension Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Découvrez [l’extension IntelliCode pour VS Code (préversion)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode fournit un ensemble de fonctionnalités assistées par AI pour IntelliSense dans Python, telles que la déduction des auto-complétions les plus pertinentes en fonction du contexte de code actuel.

## <a name="install-the-sdk"></a>Installer le Kit de développement logiciel (SDK)

1. Assurez-vous que Python 3.5 ou version supérieure est installé et reconnu par VS Code. Si vous l’installez maintenant, redémarrez VS Code et sélectionnez un interpréteur Python en utilisant les instructions fournies sur https://code.visualstudio.com/docs/python/python-tutorial.

1. Dans VS Code, ouvrez la palette de commandes via **Ctrl+Maj+P**.

1. Tapez « Install Azure ML SDK » (Installer le Kit de développement logiciel (SDK) Azure Machine Learning) pour rechercher la commande d’installation pip pour le Kit de développement logiciel (SDK). Un environnement Python privé local est créé avec la configuration requise de Visual Studio Code pour une utilisation avec Azure Machine Learning.
   ![installer](./media/vscode-tools-for-ai/install-sdk.png)

1. Dans la fenêtre du terminal intégré, indiquez l’interpréteur Python à utiliser ou appuyez sur **Entrée** pour utiliser votre interpréteur Python par défaut.

   ![installer](./media/vscode-tools-for-ai/python.png)

## <a name="get-started-with-azure-machine-learning"></a>Prise en main d’Azure Machine Learning

Avant de commencer à effectuer l’apprentissage et à déployer les modèles Machine Learning, vous devez créer un [espace de travail de service Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace) dans le cloud pour héberger vos modèles et ressources. Découvrez comment en créer un, et créez votre première expérience dans cet espace de travail.

1. Cliquez sur l’icône Azure dans la barre d’activités Visual Studio Code. La barre latérale Azure : Machine Learning apparaît.

   ![installer](./media/vscode-tools-for-ai/createworkspace.gif)

1. Cliquez avec le bouton droit sur votre abonnement Azure et sélectionnez **Créer un espace de travail**. Une liste s’affiche. Dans l’image animée, le nom de l’abonnement est « OpenMind Studio » et l’espace de travail est « MyWorkspace ». 

1. Sélectionnez un groupe de ressources existant dans la liste ou créez-en un à l’aide de l’Assistant dans la palette de commandes.

1. Dans le champ, tapez un nom unique et clair pour votre nouvel espace de travail. Dans les captures d’écran, l’espace de travail s’appelle « MyWorkspace ».

1. Appuyez sur Entrée pour créer l’espace de travail. Il apparaît dans l’arborescence située sous le nom de l’abonnement.

1. Cliquez avec le bouton droit sur le nom de l’espace de travail et choisissez **Create Experiment** (Créer une expérience) dans le menu contextuel.  Les expériences suivent vos exécutions à l’aide d’Azure Machine Learning.

1. Dans le champ, entrez un nom pour votre expérience. Dans les captures d’écran, l’expérience s’appelle « MNIST ».
 
1. Appuyez sur Entrée pour créer l’expérience. Elle apparaît dans l’arborescence située sous le nom de l’espace de travail.

1. Cliquez avec le bouton droit sur le nom de l’expérience et choisissez **Attach a local folder** (Joindre un dossier local). Ce dossier doit contenir vos scripts Python locaux. Le dossier est ensuite lié à l’expérience dans le cloud. 

   Maintenant que toutes vos expériences s’exécutent dans votre expérience, l’ensemble de vos métriques clés sont stockées dans l’historique des expériences et les modèles dont vous effectuez l’apprentissage sont automatiquement chargés dans Azure Machine Learning et stockés dans vos journaux et métriques d’expérience.

   [![Joindre un dossier dans VS Code](./media/vscode-tools-for-ai/attachfolder.gif)](./media/vscode-tools-for-ai/attachfolder.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Utiliser les raccourcis clavier

Comme la plupart du temps dans VS Code, les fonctionnalités Azure Machine Learning dans VS Code sont accessibles depuis le clavier. La combinaison de touches la plus importante à retenir est « Ctrl+Maj+P », qui permet d’afficher la palette de commandes. Depuis celle-ci, vous avez accès à toutes les fonctionnalités de VS Code, y compris aux raccourcis clavier pour les opérations les plus courantes.

[![Raccourcis clavier pour VS Code Tools for AI](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez maintenant utiliser Visual Studio Code pour travailler avec Azure Machine Learning.

Découvrez comment [créer des cibles de calcul, effectuer l’apprentissage et déployer des modèles dans Visual Studio Code](how-to-vscode-train-deploy.md).