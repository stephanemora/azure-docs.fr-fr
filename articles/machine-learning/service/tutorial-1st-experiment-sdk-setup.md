---
title: 'Didacticiel : Configuration de l’environnement et de l’espace de travail.'
titleSuffix: Azure Machine Learning service
description: Dans cette série de tutoriels, vous allez effectuer des étapes de bout en bout pour vous familiariser avec le kit SDK Python Azure Machine Learning s’exécutant dans des notebooks Jupyter.  La première partie aborde la création d’un environnement serveur de notebooks cloud, ainsi que la création d’un espace de travail pour gérer vos expériences et vos modèles de Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 07/20/2019
ms.openlocfilehash: b3fdcfbb9869c0199c91f5bbc6ca1f02fec996cf
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828630"
---
# <a name="tutorial-setup-environment-and-workspace"></a>Didacticiel : Configuration de l’environnement et de l’espace de travail

Dans ce tutoriel, vous allez effectuer des étapes de bout en bout pour vous familiariser avec le kit de développement logiciel (SDK) Python pour Azure Machine Learning s’exécutant dans des notebooks Jupyter. Ce tutoriel **fait partie d’une série de tutoriels en deux parties** et aborde l’installation et la configuration de l’environnement Python, ainsi que la création d’un espace de travail pour gérer vos expériences et vos modèles Machine Learning. [**La deuxième partie**](tutorial-1st-experiment-sdk-train.md) se poursuit avec l’apprentissage de plusieurs modèles Machine Learning et présente le processus de gestion des modèles avec le portail Azure et le kit de développement logiciel (SDK).

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Créer un espace de travail Machine Learning à utiliser dans le tutoriel suivant.
> * Avec le kit SDK Python Azure Machine Learning préinstallé et préconfiguré, créez une machine virtuelle Notebook ainsi qu’un serveur de notebooks Jupyter basé sur le cloud.

## <a name="prerequisites"></a>Prérequis

La seule condition préalable pour ce tutoriel est un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree) dès aujourd’hui.

## <a name="create-a-workspace"></a>Créer un espace de travail

Dans le cloud, l’espace de travail est le socle que vous utilisez pour expérimenter, entraîner et déployer des modèles Machine Learning. Il lie votre abonnement Azure et votre groupe de ressources à un objet facile à consommer dans le kit de développement logiciel (SDK). Si vous disposez déjà d’un espace de travail Azure Machine Learning service, passez à la [section suivante](#azure). Dans le cas contraire, créez-en un maintenant.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>Créer un serveur de notebooks basé sur le cloud

Cet exemple utilise le serveur de notebook cloud dans votre espace de travail pour une expérience préconfigurée sans installation. Utilisez [votre propre environnement](how-to-configure-environment.md#local) si vous préférez contrôler votre environnement, vos packages et vos dépendances.

À partir de votre espace de travail, créez une ressource cloud pour commencer à utiliser des notebooks Jupyter. Cette ressource est une machine virtuelle Linux cloud préconfigurée avec tout ce dont vous avez besoin pour exécuter Azure Machine Learning service.

1. Ouvrez votre espace de travail dans le [portail Azure](https://portal.azure.com/).  Si vous ne savez pas comment localiser votre espace de travail sur le portail, découvrez comment [retrouver votre espace de travail](how-to-manage-workspace.md#view).

1. Dans la page de votre espace de travail sur le portail Azure, sélectionnez **Machines virtuelles Notebook** à gauche.

1. Sélectionnez **+Nouveau** pour créer un machine virtuelle Notebook.

     ![Sélectionner Nouvelle machine virtuelle](./media/tutorial-1st-experiment-sdk-setup/add-workstation.png)

1. Attribuez un nom à votre machine virtuelle. Sélectionnez ensuite **Créer**.

    > [!NOTE]
    > Le nom de votre machine virtuelle Notebook doit comprendre entre 2 et 16 caractères. Les caractères valides sont les lettres, les chiffres et le caractère « - ».  Le nom doit aussi être unique dans votre abonnement Azure.

1. Attendez que l’état du travail passe à **Exécution**.

### <a name="launch-jupyter-web-interface"></a>Lancer l’interface web Jupyter

Dès lors que votre machine virtuelle s’exécute, lancez l’interface web Jupyter à partir de la section **Machines virtuelles Notebook**.

1. Sélectionnez **Jupyter** dans la colonne **URI** correspondant à votre machine virtuelle.

    ![Démarrer le serveur de notebooks Jupyter](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

    Le lien démarre votre serveur de notebooks et ouvre la page web de notebook Jupyter dans un nouvel onglet du navigateur.  Ce lien fonctionne uniquement pour la personne qui crée la machine virtuelle. Chaque utilisateur de l’espace de travail doit créer sa propre machine virtuelle.

1. Dans la page web du notebook Jupyter, le nom de dossier en haut est votre nom d’utilisateur.  Sélectionnez ce dossier.

    > [!TIP]
    > Ce dossier se trouve sur le [conteneur de stockage](concept-workspace.md#resources) dans votre espace de travail plutôt que sur le notebook de machine virtuelle elle-même.  Vous pouvez supprimer la machine virtuelle du notebook et conserver tous vos travaux.  Lorsque vous créez une nouvelle machine virtuelle notebook, elle chargera ce même dossier. Si vous partagez votre espace de travail avec d’autres utilisateurs, ils verront votre dossier et vous verrez le leur.

1. Ouvrez le sous-répertoire `samples-*`, puis ouvrez `tutorials/tutorial-1st-experiment-sdk-train.ipynb` pour exécuter la **deuxième** partie du tutoriel.

## <a name="end"></a> Nettoyer les ressources

N’effectuez pas cette section si vous envisagez de passer à la **partie 2** du tutoriel.

### <a name="stop-the-notebook-vm"></a>Arrêter la machine virtuelle Notebook

Si vous avez utilisé un serveur de notebook cloud, arrêtez la machine virtuelle lorsque vous ne l’utilisez pas afin de réduire les coûts.

1. Dans votre espace de travail, sélectionnez **Machines virtuelles Notebook**.

   ![Arrêter le serveur de machines virtuelles](./media/tutorial-1st-experiment-sdk-setup/stop-server.png)

1. Sélectionnez la machine virtuelle dans la liste.

1. Sélectionnez **Arrêter**.

1. Quand vous êtes prêt à utiliser à nouveau le serveur, sélectionnez **Démarrer**.

### <a name="delete-everything"></a>Tout supprimer

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Vous pouvez également conserver le groupe de ressources mais supprimer un espace de travail unique. Affichez les propriétés de l’espace de travail, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez effectué les tâches suivantes :

* Créer un espace de travail Azure Machine Learning service.
* Utiliser et configurer un serveur de notebooks cloud dans votre espace de travail.

Passez à la **partie 2** de ce tutoriel pour effectuer l’apprentissage d’un modèle Machine Learning simple.

> [!div class="nextstepaction"]
> [Tutoriel : effectuer l’apprentissage de votre premier modèle](tutorial-1st-experiment-sdk-train.md)
