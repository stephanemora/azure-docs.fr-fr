---
title: Procédure d’accès à un terminal d’instance de calcul dans votre espace de travail
titleSuffix: Azure Machine Learning
description: Utilisez le terminal sur une instance de calcul pour les opérations Git, pour installer des packages, et pour ajouter des noyaux.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: e42d6d53e4d06e5ec7a33ea4b8ca18233dcf1dd7
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100100987"
---
# <a name="access-a-compute-instance-terminal-in-your-workspace"></a>Accès à un terminal d’instance de calcul dans votre espace de travail

Accédez au terminal d’une instance de calcul dans votre espace de travail pour :

* Utiliser des fichiers de Git et contrôler la version des fichiers. Ces fichiers sont stockés dans le système de fichiers de votre espace de travail, et ne sont pas limités à une seule instance de calcul.
* Installer des packages sur l’instance de calcul.
* Créer des noyaux supplémentaires sur l’instance de calcul.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://aka.ms/AMLFree) avant de commencer.
* Un espace de travail Machine Learning. Consultez [Créer un espace de travail Microsoft Azure Machine Learning](how-to-manage-workspace.md).

## <a name="access-a-terminal"></a>Accéder à un terminal

Pour accéder au terminal :

1. Dans [Azure Machine Learning Studio](https://ml.azure.com), ouvrez votre espace de travail.
1. Sur le côté gauche, sélectionnez **Bloc-notes**.
1. Sélectionnez l’image **Ouvrir le terminal**.

    :::image type="content" source="media/how-to-use-terminal/open-terminal-window.png" alt-text="Ouvrir la fenêtre de terminal":::

1. Quand une instance de calcul est en cours d’exécution, la fenêtre de terminal pour cette instance de calcul s’affiche.
1. Si aucune instance de calcul n’est en cours d’exécution, utilisez la section **Calcul** à droite pour démarrer ou créer une instance de calcul.
    :::image type="content" source="media/how-to-use-terminal/start-or-create-compute.png" alt-text="Démarrer ou créer une instance de calcul":::

Outre les étapes ci-dessus, vous pouvez également accéder au terminal comme suit :

* RStudio : Sélectionnez l’onglet **Terminal** en haut à gauche.
* Lab Jupyter :  Sélectionnez la vignette **Terminal** sous le titre **Autre** de l’onglet Lanceur.
* Jupyter :  Sélectionnez **Nouveau > Terminal** en haut à droite dans l’onglet Fichiers.
* Connectez-vous en mode SSH à la machine, si vous avez activé l’accès SSH lors de la création de l’instance de calcul.

## <a name="copy-and-paste-in-the-terminal"></a>Opérations Copier et Coller dans le terminal

> * Windows : `Ctrl-Insert` pour copier et `Ctrl-Shift-v` ou `Shift-Insert` pour coller.
> * Mac OS : `Cmd-c` pour copier et `Cmd-v` pour coller.
> * FireFox/IE peuvent ne pas prendre en charge correctement les autorisations de Presse-papiers.

## <a name="use-files-from-git-and-version-files"></a><a name=git></a> Utiliser des fichiers de Git et contrôler la version des fichiers

Accédez à toutes les opérations Git à partir du terminal. Tous les fichiers et dossiers Git sont stockés dans le système de fichiers de votre espace de travail. Ce stockage vous permet d’utiliser ces fichiers à partir de n’importe quelle instance de calcul dans votre espace de travail.

> [!NOTE]
> Ajoutez vos fichiers et dossiers n’importe où dans le dossier **~/cloudfiles/code/Users** afin qu’ils soient visibles dans tous vos environnements Jupyter.

Apprenez-en davantage sur le [clonage des dépôts Git dans le système de fichiers de votre espace de travail](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).

## <a name="install-packages"></a>Installer des packages

 Installez des packages à partir d’une fenêtre de terminal. Installez les packages Python dans l’environnement **Python 3.6 – AzureML**.  Installez les packages R dans l’environnement **R**.

Ou vous pouvez installer des packages directement dans Jupyter Notebook ou RStudio :

* RStudio utilise l’onglet **Packages** en bas à droite, ou l’onglet **Console** en haut à gauche.  
* Python : Ajoutez le code d’installation et exécutez-le dans une cellule Jupyter Notebook.

> [!NOTE]
> Pour la gestion des packages au sein d’un notebook, utilisez les fonctions magic **%pip** ou **%conda** pour installer automatiquement des packages dans le **noyau en cours d’exécution**, au lieu de **!pip** ou **!conda** qui se réfèrent à tous les packages (y compris les packages en dehors du noyau en cours d’exécution).

## <a name="add-new-kernels"></a>Ajouter de nouveaux noyaux

> [!WARNING]
>  Lors de la personnalisation de l’instance de calcul, veillez à ne pas supprimer l’environnement conda **azureml_py36** ou le noyau **Python 3.6 - AzureML**. Cela est nécessaire pour le bon fonctionnement de Jupyter/JupyterLab.

Pour ajouter un nouveau noyau Jupyter à l’instance de calcul :

1. Utilisez la fenêtre de terminal pour créer un environnement.  Par exemple, le code ci-dessous crée `newenv` :

    ```shell
    conda create --name newenv
    ```

1. Active l’environnement.  Par exemple, après la création de `newenv` :

    ```shell
    conda activate newenv
    ```

1. Installer pip et le package ipykernel dans le nouvel environnement et créer un noyau pour ce conda env

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

Vous pouvez installer n’importe lequel des [noyaux Jupyter disponibles](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels).

## <a name="manage-terminal-sessions"></a>Gérer les sessions de terminal

 Sélectionnez **Afficher les sessions actives** dans la barre d’outils du terminal pour afficher une liste de toutes les sessions de terminal actives. Si aucune session n’est active, cet onglet est désactivé.

Fermez toutes les sessions inutilisées pour conserver les ressources de votre instance de calcul.