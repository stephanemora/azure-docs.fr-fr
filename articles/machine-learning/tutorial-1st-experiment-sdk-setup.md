---
title: 'Tutoriel : Prise en main des notebooks Jupyter (Python)'
titleSuffix: Azure Machine Learning
description: Tutoriels sur la configuration des notebooks Jupyter.  Créez un espace de travail Azure Machine Learning, clonez les notebooks Jupyter dans l’espace de travail et créez une instance de calcul dans laquelle vous exécutez les notebooks.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python
ms.openlocfilehash: de52013628f5d02bedcf72a99e0fad25cabe5d8f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90896889"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>Tutoriel : Commencez à utiliser Azure Machine Learning dans des notebooks Jupyter

Dans ce tutoriel, vous allez effectuer les étapes de prise en main d’Azure Machine Learning à l’aide de notebooks Jupyter sur une [station de travail cloud managée (instance de calcul)](concept-compute-instance.md). Ce tutoriel est le premier d’une série de tutoriels sur les notebooks Jupyter.

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Créez un [espace de travail Azure Machine Learning](concept-workspace.md) à utiliser dans d’autres tutoriels relatifs aux notebooks Jupyter.
> * Clonez le notebook des tutoriels dans votre dossier au sein de l’espace de travail.
> * Préinstallez et préconfigurez le Kit de développement logiciel (SDK) Python Azure Machine Learning, puis créez une instance de calcul basée sur le cloud.

Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

## <a name="create-a-workspace"></a>Créer un espace de travail

Un espace de travail Azure Machine Learning est une ressource fondamentale du cloud que vous utilisez pour expérimenter, entraîner et déployer des modèles Machine Learning. Il lie votre abonnement Azure et votre groupe de ressources à un objet facile à consommer dans le service.

Vous créez un espace de travail par le biais du portail Azure, une console web pour la gestion de vos ressources Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> Prenez note de votre **espace de travail** et de votre **abonnement**. Vous en aurez besoin pour être sûr de créer votre expérience au bon endroit. 

## <a name="run-notebook-in-your-workspace"></a><a name="azure"></a>Exécuter un notebook dans votre espace de travail

Azure Machine Learning inclut un serveur de notebook cloud dans votre espace de travail, qui vous offre une expérience préconfigurée qui ne nécessite aucune installation. Utilisez [votre propre environnement](tutorial-1st-experiment-sdk-setup-local.md) si vous préférez contrôler votre environnement, vos packages et vos dépendances.

 Suivez cette vidéo ou suivez les étapes détaillées ci-dessous pour cloner et exécuter le notebook du tutoriel à partir de votre espace de travail.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a>Cloner un dossier de notebooks

Vous effectuez les étapes de configuration et d’exécution d’expérience suivantes dans Azure Machine Learning Studio, une interface centralisée qui comprend des outils de Machine Learning permettant de mettre en œuvre des scénarios de science des données pour les utilisateurs de science des données de tous niveaux de compétence.

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com/).

1. Sélectionnez votre abonnement et l’espace de travail que vous avez créé.

1. Sélectionnez **Notebooks** à gauche.

1. Sélectionnez l’onglet **Exemples** en haut.

1. Ouvrez le dossier **Python**.

1. Ouvrez le dossier contenant un numéro de version.  Ce numéro représente la version actuelle du Kit de développement logiciel (SDK) Python.

1. Sélectionnez **« ... »** à droite du dossier **tutorials** (tutoriels), puis sélectionnez **Clone** (Cloner).

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="Cloner le dossier tutorials":::

1. Une liste de dossiers indiquant tous les utilisateurs qui accèdent à l’espace de travail s’affiche.  Sélectionnez le dossier où cloner le dossier **tutorials**.

### <a name="open-the-cloned-notebook"></a><a name="open"></a>Ouvrir le notebook cloné

1. Ouvrez le dossier **tutorials** qui vient d’être fermé dans la section **User Files**.

    > [!IMPORTANT]
    > Vous pouvez voir les notebooks présents dans le dossier **samples** (exemples), mais vous ne pouvez pas exécuter de notebook à partir de cet emplacement.  Pour exécuter un notebook, veillez à ouvrir sa version clonée dans la section **User Files**.
    
1. Sélectionnez le fichier **tutorial-1st-experiment-sdk-train.ipynb** dans votre dossier **tutorials/image-classification-mnist-data**.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="Ouvrir le dossier tutorials":::

1. Dans la barre supérieure, sélectionnez une instance de calcul à utiliser pour exécuter le notebook. Ces machines virtuelles sont préconfigurées avec [tout ce dont vous avez besoin pour exécuter Azure Machine Learning](concept-compute-instance.md#contents).

1. Si aucune machine virtuelle n’est trouvée, sélectionnez **+ Ajouter** pour créer la machine virtuelle d’instance de calcul. 

    1. Quand vous créez une machine virtuelle, suivez ces règles :  
        + Le nom est obligatoire et ne peut pas être vide.
        + Le nom doit être unique (insensible à la casse) dans toutes les instances de calcul existantes de la région Azure de l’instance d’espace de travail/de calcul. Vous obtenez une alerte si le nom que vous choisissez n’est pas unique.
        + Les caractères valides sont les lettres majuscules et minuscules, les chiffres (0 à 9) et le tiret (-).
        + Le nom doit comporter entre 3 et 24 caractères.
        + Le nom doit commencer par une lettre (et non par un chiffre ou un tiret).
        + S’il contient un tiret, celui-ci doit être suivi d’au moins une lettre. Exemple : Test-, test-0, test-01 ne sont pas noms d’instances valides, alors que test-a0, test-0a le sont.

    1.  Sélectionnez la taille de la machine virtuelle parmi les options disponibles. Pour les didacticiels, la machine virtuelle par défaut constitue un bon choix.

    1. Sélectionnez ensuite **Créer**. Environ 5 minutes sont nécessaires pour configurer votre machine virtuelle.

1. Une fois la machine virtuelle disponible, elle s’affiche dans la barre d’outils supérieure.  Vous pouvez maintenant exécuter le notebook à l’aide de **Run all** (Tout exécuter) dans la barre d’outils, ou à l’aide de **Maj+Entrée** dans les cellules de code du notebook.

Si vous avez des widgets personnalisés ou préférez utiliser Jupyter/JupyterLab, sélectionnez la liste déroulante **Jupyter** à l’extrême droite, puis sélectionnez **Jupyter** ou **JupyterLab**. La nouvelle fenêtre de navigateur s’ouvre.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré un environnement de développement, passez à l’entraînement d’un modèle dans un notebook Jupyter :

> [!div class="nextstepaction"]
> [Tutoriel : Entraîner des modèles de classification d’images avec des données MNIST et scikit-learn](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a> Si vous ne prévoyez pas de suivre d’autres tutoriels maintenant, vous devez arrêter la machine virtuelle du serveur de notebooks cloud quand vous ne l’utilisez pas afin de réduire les coûts :

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]
