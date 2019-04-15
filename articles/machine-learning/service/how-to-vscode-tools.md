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
ms.openlocfilehash: 5e7e405bdb251bbebc5da81253eea2414b434cb8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838308"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Premiers pas avec Azure Machine Learning pour Visual Studio Code

Dans cet article, vous allez apprendre à installer l’extension **Azure Machine Learning pour Visual Studio Code** et à créer votre première expérience avec le service Azure Machine Learning dans Visual Studio Code (VS Code).

L’extension Azure Machine Learning dans Visual Studio Code vous permet d’utiliser le service Azure Machine Learning pour préparer vos données, effectuer l’apprentissage et tester les modèles Machine Learning sur des cibles de calcul locales et distantes, déployer ces modèles et suivre les expériences et les métriques personnalisées.

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

1. Un onglet de bienvenue s’ouvre dans VS Code pour l’extension, et le symbole Azure est ajouté à la barre d’activités.

   ![Icône Azure dans la barre d’activités Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Dans la boîte de dialogue, cliquez sur **Se connecter** et suivez les instructions à l’écran pour vous authentifier avec Azure. 
   
   L’extension Azure Account, qui a été installée avec l’extension Azure Machine Learning pour VS Code, vous permet de vous authentifier avec votre compte Azure. Consultez la liste des commandes sur la page de [l’extension Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Découvrez [l’extension IntelliCode pour VS Code (préversion)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode fournit un ensemble de fonctionnalités assistées par AI pour IntelliSense dans Python, telles que la déduction des auto-complétions les plus pertinentes en fonction du contexte de code actuel.

## <a name="azure-ml-sdk-installation"></a>Installation du kit SDK Azure Machine Learning

1. Assurez-vous que Python 3.5 ou version supérieure est installé et reconnu par VS Code. Si vous l’installez maintenant, redémarrez VS Code et sélectionnez un interpréteur Python en utilisant les instructions fournies sur https://code.visualstudio.com/docs/python/python-tutorial.

1. Dans la fenêtre du terminal intégré, indiquez l’interpréteur Python à utiliser ou appuyez sur **Entrée** pour utiliser votre interpréteur Python par défaut.

   ![Choisir l’interpréteur](./media/vscode-tools-for-ai/python.png)

1. Dans le coin inférieur droit de la fenêtre, une notification indique que le SDK Azure ML en cours d’installation automatique.    Un environnement Python privé local est créé avec la configuration requise de Visual Studio Code pour une utilisation avec Azure Machine Learning.

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

### <a name="use-keyboard-shortcuts"></a>Utiliser les raccourcis clavier

Comme la plupart du temps dans VS Code, les fonctionnalités Azure Machine Learning dans VS Code sont accessibles depuis le clavier. La combinaison de touches la plus importante à retenir est « Ctrl+Maj+P », qui permet d’afficher la palette de commandes. Depuis celle-ci, vous avez accès à toutes les fonctionnalités de VS Code, y compris aux raccourcis clavier pour les opérations les plus courantes.

[![Raccourcis clavier de Azure Machine Learning pour VS Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez maintenant utiliser Visual Studio Code pour travailler avec Azure Machine Learning.

Découvrez comment [créer des cibles de calcul, effectuer l’apprentissage et déployer des modèles dans Visual Studio Code](how-to-vscode-train-deploy.md).
