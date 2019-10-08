---
title: 'Didacticiel : Créer votre première expérience ML Paramétrage'
titleSuffix: Azure Machine Learning
description: Dans cette série de tutoriels, vous allez effectuer des étapes de bout en bout pour vous familiariser avec le kit SDK Python Azure Machine Learning s’exécutant dans des notebooks Jupyter.  La première partie aborde la création d’un environnement serveur de notebooks cloud, ainsi que la création d’un espace de travail pour gérer vos expériences et vos modèles de Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/25/2019
ms.openlocfilehash: 3bbda22689bb330acc836173162a64b840f1bbd8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828031"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Didacticiel : Bien démarrer avec la création de votre première expérience ML avec le SDK Python

Dans ce tutoriel, vous allez effectuer des étapes de bout en bout pour vous familiariser avec le kit de développement logiciel (SDK) Python pour Azure Machine Learning s’exécutant dans des notebooks Jupyter. Ce tutoriel **fait partie d’une série de tutoriels en deux parties** et aborde l’installation et la configuration de l’environnement Python, ainsi que la création d’un espace de travail pour gérer vos expériences et vos modèles Machine Learning. [**La deuxième partie**](tutorial-1st-experiment-sdk-train.md) se poursuit avec l’apprentissage de plusieurs modèles Machine Learning et présente le processus de gestion des modèles avec le portail Azure et le kit de développement logiciel (SDK).

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Créer un [espace de travail Azure Machine Learning](concept-workspace.md) à utiliser dans le tutoriel suivant.
> * Clonez le notebook des tutoriels dans votre dossier au sein de l’espace de travail.
> * Préinstallez et préconfigurez le SDK Python Azure Machine Learning, puis créez une machine virtuelle Notebook Jupyter basée sur le cloud.

Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

## <a name="create-a-workspace"></a>Créer un espace de travail

Un espace de travail Azure Machine Learning est une ressource fondamentale du cloud que vous utilisez pour expérimenter, entraîner et déployer des modèles Machine Learning. Il lie votre abonnement Azure et votre groupe de ressources à un objet facile à consommer dans le service. 

Vous créez un espace de travail par le biais du portail Azure, une console web pour la gestion de vos ressources Azure. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Prenez note de votre **espace de travail** et de votre **abonnement**. Vous en aurez besoin pour être sûr de créer votre expérience au bon endroit. 


## <a name="azure"></a>Cloner un dossier de notebooks

Cet exemple utilise le serveur de notebook cloud dans votre espace de travail pour une expérience préconfigurée sans installation. Utilisez [votre propre environnement](how-to-configure-environment.md#local) si vous préférez contrôler votre environnement, vos packages et vos dépendances.

Vous effectuez les étapes de configuration et d’exécution d’expérience suivantes dans la page d’accueil de l’espace de travail (préversion), une interface centralisée qui comprend des outils de Machine Learning permettant de mettre en œuvre des scénarios de science des données pour les utilisateurs de science des données de tous niveaux de compétence.

1. Connectez-vous à la [page d’accueil de l’espace de travail](https://ml.azure.com/).

1. Sélectionnez votre abonnement et l’espace de travail que vous avez créé.

1. Sélectionnez **Notebooks and files** (Notebooks et fichiers) à gauche.

1. Ouvrez le dossier **samples** (exemples).

1. Sélectionnez **« ... »** à droite du dossier **tutorials** (tutoriels), puis sélectionnez **Clone** (Cloner).

    ![Cloner un dossier](media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. Un dossier est affiché pour chaque utilisateur qui accède à l’espace de travail.  Sélectionnez le dossier où cloner le dossier **tutorial**.

## <a name="a-nameopenselect-a-vm-to-run-the-notebook"></a><a name="open">Sélectionner une machine virtuelle pour exécuter le notebook

1. Sous **User Files** (Fichiers utilisateur), ouvrez votre dossier, puis ouvrez le dossier **tutorials** cloné.

    ![Ouvrir le dossier tutorials](media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > Vous pouvez voir les notebooks présents dans le dossier **samples** (exemples), mais vous ne pouvez pas exécuter de notebook à partir de cet emplacement.  Pour exécuter un notebook, veillez à ouvrir sa version clonée dans la section **User Files**.
    
1. Sélectionnez le fichier **tutorial-1st-experiment-sdk-train.ipynb** dans votre dossier **tutorials**.

1. Dans la barre supérieure, sélectionnez une machine virtuelle Notebook à utiliser pour exécuter le notebook. Ces machines virtuelles sont préconfigurées avec tout ce dont vous avez besoin pour exécuter Azure Machine Learning. Vous pouvez sélectionner une machine virtuelle créée par n’importe quel utilisateur de votre espace de travail. 

1. Si une machine virtuelle est introuvable, sélectionnez **+ New VM** (Nouvelle machine virtuelle) pour créer la machine virtuelle.

    ![Créer une machine virtuelle](media/tutorial-1st-experiment-sdk-setup/no-vm.png)

    1. Quand vous créez une machine virtuelle, indiquez un nom.  Le nom doit être compris entre 2 et 16 caractères. Les caractères valides sont les lettres, les chiffres et le caractère« - ». De plus, les noms doivent être uniques dans votre abonnement Azure.

    1. Sélectionnez ensuite **Créer**. Environ 5 minutes sont nécessaires pour configurer votre machine virtuelle.

1. Une fois la machine virtuelle disponible, elle s’affiche dans la barre d’outils supérieure.  Vous pouvez maintenant exécuter le notebook à l’aide de **Run all** (Tout exécuter) dans la barre d’outils, ou à l’aide de **Maj+Entrée** dans les cellules de code du notebook.


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez effectué les tâches suivantes :

* Création d’un espace de travail Azure Machine Learning
* Utiliser et configurer un serveur de notebooks cloud dans votre espace de travail.

Dans la **deuxième partie** du tutoriel, vous exécutez le code dans `tutorial-1st-experiment-sdk-train.ipynb` pour entraîner un modèle Machine Learning. 

> [!div class="nextstepaction"]
> [Tutoriel : effectuer l’apprentissage de votre premier modèle](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Si vous ne prévoyez pas de suivre la deuxième partie de ce tutoriel ou tout autre tutoriel, vous devez [arrêter la machine virtuelle du serveur de notebooks cloud](tutorial-1st-experiment-sdk-train.md#clean-up-resources) quand vous ne l’utilisez pas, afin de réduire les coûts.


