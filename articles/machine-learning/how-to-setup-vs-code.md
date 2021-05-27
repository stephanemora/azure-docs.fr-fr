---
title: Configurer l’extension Visual Studio Code (préversion)
titleSuffix: Azure Machine Learning
description: Découvrir comment configurer l’extension Visual Studio Code d’Azure Machine Learning
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: core
ms.date: 05/25/2021
ms.topic: how-to
ms.openlocfilehash: 80f6849bb4989692e8cd29a3288f9b104bf250bb
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110460083"
---
# <a name="set-up-the-visual-studio-code-azure-machine-learning-extension-preview"></a>Configurer l’extension Visual Studio Code d’Azure Machine Learning (préversion)

Découvrez comment configurer l’extension Visual Studio Code d’Azure Machine Learning pour vos workflows de machine learning.

> [!div class="mx-imgBorder"]
> ![Extension VS Code](./media/how-to-setup-vs-code/vs-code-extension.PNG)

L’extension Azure Machine Learning pour VS Code fournit une interface utilisateur pour :

- Gérer les ressources Azure Machine Learning (expériences, machines virtuelles, modèles, déploiements, etc.)
- Développer localement à l’aide d’instances de calcul distantes
- Effectuer l’apprentissage de modèles Machine Learning
- Déboguer les expériences de machine learning localement
- Prise en charge des langages basés sur un schéma, autocomplétion et diagnostics pour la création de fichiers de spécifications
- Extraits de code pour les tâches courantes

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure. Si vous n’en avez pas, inscrivez-vous ou essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree)
- Visual Studio Code. [Installez-le](https://code.visualstudio.com/docs/setup/setup-overview) si ce n’est déjà fait.
- [Python](https://www.python.org/downloads/)
- (Facultatif) Pour créer des ressources à l’aide de l’extension, vous devez installer l’interface CLI 2.0. Pour obtenir des instructions d’installation, consultez [Installer, configurer et utiliser l’interface CLI 2.0 (préversion)](how-to-configure-cli.md).

## <a name="install-the-extension"></a>Installer l’extension

1. Ouvrez Visual Studio Code.
1. Sélectionnez l’icône **Extensions** dans la **Barre d’activité** pour ouvrir la vue Extensions.
1. Dans la vue Extensions, recherchez « Azure Machine Learning ».
1. Sélectionnez **Installer**.

    > [!div class="mx-imgBorder"]
    > ![Installer l’extension Azure Machine Learning VS Code](./media/how-to-setup-vs-code/install-aml-vscode-extension.PNG)

> [!NOTE]
> Vous pouvez également installer l’extension Azure Machine Learning par le biais de Visual Studio Marketplace en [téléchargeant directement le programme d’installation](https://aka.ms/vscodetoolsforai).

Les autres étapes de ce tutoriel ont été testées avec la version la plus récente de l’extension.

> [!NOTE]
> L’extension Azure Machine Learning pour VS Code utilise l’interface CLI 2.0 par défaut. Pour passer à la CLI 1.0, définissez le paramètre `azureML.CLI Compatibility Mode` de Visual Studio Code sur `1.0`. Pour plus d’informations sur la modification de vos paramètres dans Visual Studio, consultez la [documentation sur les paramètres de l’utilisateur et de l’espace de travail](https://code.visualstudio.com/docs/getstarted/settings).

## <a name="sign-in-to-your-azure-account"></a>Vous connecter à votre compte Azure

Pour provisionner des ressources et exécuter des charges de travail sur Azure, vous devez vous connecter avec vos informations d’identification de compte Azure. Pour faciliter la gestion des comptes, Azure Machine Learning installe automatiquement l’extension Azure Account. Visitez le site suivant pour [en savoir plus sur l’extension Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

Pour vous connecter à votre compte Azure, sélectionnez le bouton **Azure : Se connecter** dans la barre d’état de Visual Studio Code pour lancer le processus de connexion.

Vous pouvez aussi utiliser la palette de commandes :

1. Ouvrez la palette de commandes en sélectionnant **Affichage > Palette de commandes** dans la barre de menus.
1. Entrez la commande « > Azure: Sign In » dans la palette de commandes pour lancer le processus de connexion.

## <a name="choose-your-default-workspace"></a>Choisir votre espace de travail par défaut

Le choix d’un espace de travail Azure Machine Learning par défaut offre les possibilités suivantes pendant que vous créez les fichiers de spécifications YAML de l’interface CLI 2.0 :

- Validation du schéma
- Autocomplétion
- Diagnostics

Si vous n’avez pas d’espace de travail, créez-en un. Pour plus d’informations, consultez [Gérer les ressources Azure Machine Learning avec l’extension VS Code](how-to-manage-resources-vscode.md).

Pour choisir votre espace de travail par défaut, sélectionnez le bouton **Définir l’espace de travail Azure ML** dans la barre d’état de Visual Studio Code, puis suivez les invites pour définir votre espace de travail.

Vous pouvez aussi utiliser la commande `> Azure ML: Set Default Workspace` de la palette de commandes et suivre les invites pour définir votre espace de travail.

## <a name="next-steps"></a>Étapes suivantes

- [Gérer vos ressources Azure Machine Learning](how-to-manage-resources-vscode.md)
- [Développer localement sur une instance de calcul distante](how-to-set-up-vs-code-remote.md)
- [Utiliser une instance de calcul en tant que serveur Jupyter distant](how-to-set-up-vs-code-remote.md)
- [Entraîner un modèle de classification d’images à l’aide de l’extension Visual Studio Code](tutorial-train-deploy-image-classification-model-vscode.md)
- [Exécuter et déboguer des expériences de machine learning localement](how-to-debug-visual-studio-code.md)