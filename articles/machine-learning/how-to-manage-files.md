---
title: Créer et gérer des fichiers dans votre espace de travail
titleSuffix: Azure Machine Learning
description: Découvrez comment créer et gérer des fichiers dans votre espace de travail avec Azure Machine Learning Studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: 474b3123513e4b8acf19ba9cdb42c3384ea3ced2
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100100991"
---
# <a name="how-to-create-and-manage-files-in-your-workspace"></a>Créer et gérer des fichiers dans votre espace de travail

Découvrez comment créer et gérer des fichiers dans votre espace de travail Azure Machine Learning.  Ces fichiers sont stockés dans le stockage de l’espace de travail par défaut. Les fichiers et les dossiers peuvent être partagés avec toute personne disposant d’un accès en lecture à l’espace de travail, et peuvent être utilisés à partir de n’importe quelle instance de calcul de l’espace de travail.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://aka.ms/AMLFree) avant de commencer.
* Un espace de travail Machine Learning. Consultez [Créer un espace de travail Microsoft Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-files"></a><a name="create"></a> Créer des fichiers

Pour créer un fichier dans votre dossier par défaut (`Users > yourname`) :

1. Dans [Azure Machine Learning Studio](https://ml.azure.com), ouvrez votre espace de travail.
1. Sur le côté gauche, sélectionnez **Bloc-notes**.
1. Sélectionnez l’image **+** .
1. Sélectionnez l’image **Créer un fichier**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Créer un fichier":::

1. Nommez le fichier.
1. Sélectionnez un type de fichier.
1. Sélectionnez **Create** (Créer).

Les blocs-notes et la plupart des types de fichiers texte figurent dans la section d’aperçu.  La plupart des autres types de fichiers n’ont pas de préversion.

Pour créer un fichier dans un autre dossier :
1. Sélectionnez les points de suspension (...) à la fin du fichier.
1. Sélectionnez **Create new file**.

> [!IMPORTANT]
> Le contenu dans les blocs-notes et les scripts peut potentiellement lire les données de vos sessions et accéder aux données sans votre organisation dans Azure.  Chargez uniquement des fichiers provenant de sources fiables. Pour plus d’informations, consultez [Meilleures pratiques pour un codage sûr](concept-secure-code-best-practice.md#azure-ml-studio-notebooks).

## <a name="manage-files-with-git"></a>Gérer des fichiers avec Git

[Utilisez un terminal d’instance de calcul](how-to-access-terminal.md#git) pour cloner et gérer les dépôts Git.

## <a name="clone-samples"></a>Cloner des exemples

Votre espace de travail comprend le dossier **Exemples de notebooks** qui contient des notebooks conçus pour vous aider à explorer le SDK et pour servir d’exemples à vos propres projets de machine learning.   Clonez ces notebooks dans votre propre dossier en vue de les exécuter et de les modifier.  

Pour obtenir un exemple, consultez [Tutoriel : Créer votre première expérience ML](tutorial-1st-experiment-sdk-setup.md#azure).

## <a name="share-files"></a>Partager des fichiers

Copiez-collez l’URL pour partager un fichier.  Seuls les autres utilisateurs de l’espace de travail peuvent accéder à cette URL.  Apprenez-en davantage sur l’[octroi d’accès à votre espace de travail](how-to-assign-roles.md).

## <a name="delete-a-file"></a>Supprimer un fichier

Vous *ne pouvez pas* supprimer les fichiers du dossier **Exemples de notebooks**.  Ces fichiers font partie du studio et sont mis à jour à chaque publication d’un nouveau SDK.  

Vous *pouvez* supprimer les fichiers qui se trouvent dans la section **Fichiers** à l’aide des méthodes suivantes :

* Dans le studio, sélectionnez les points de suspension ( **...** ) à la fin d’un nom de dossier ou de fichier.  Veillez à utiliser un navigateur pris en charge (Microsoft Edge, chrome ou Firefox).
* [Utilisez un terminal](how-to-access-terminal.md) à partir de l’une des instances de calcul de votre espace de travail. Le dossier **~/cloudfiles** est mappé au stockage du compte de stockage de votre espace de travail.
* Dans Jupyter ou JupyterLab, avec leurs outils.
