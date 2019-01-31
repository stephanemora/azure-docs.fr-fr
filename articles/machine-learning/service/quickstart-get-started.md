---
title: Démarrage rapide via le Portail Azure
titleSuffix: Azure Machine Learning service
description: Prise en main d’Azure Machine Learning service. Utilisez le Portail Azure pour créer un espace de travail, qui est le socle dans le cloud que vous utilisez pour expérimenter, effectuer l’apprentissage et déployer des modèles Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 01/18/2019
ms.custom: seodec18
ms.openlocfilehash: f04cd2c6d3d4eff0cbc03ee57b9a57c6883db1d6
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239007"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning"></a>Démarrage rapide : Utiliser le Portail Azure pour prendre en main Azure Machine Learning

Utilisez le Portail Azure pour créer un espace de travail Azure Machine Learning. Dans le cloud, cet espace de travail est le socle que vous utilisez pour expérimenter, effectuer l’apprentissage et déployer des modèles Machine Learning avec Machine Learning. Ce démarrage rapide utilise des ressources cloud et ne requiert aucune installation. Pour configurer votre propre serveur Jupyter Notebook à la place, consultez [Démarrage rapide : Utilisation de Python pour démarrer avec Azure Machine Learning](quickstart-create-workspace-with-python.md).  
 
Ce démarrage rapide décrit les actions suivantes :

* Créer un espace de travail dans votre abonnement Azure.
* Essayez-le avec Python dans un notebook Jupyter et journalisez les valeurs sur plusieurs itérations.
* Afficher les valeurs journalisées dans votre espace de travail.

Les ressources Azure suivantes sont automatiquement ajoutées à votre espace de travail lorsqu’elles sont disponibles au niveau régional :

  - [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
  - [Stockage Azure](https://azure.microsoft.com/services/storage/)
  - [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
  - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

Les ressources que vous créez peuvent être utilisées comme prérequis dans d’autres didacticiels et guides pratiques du service Machine Learning. Comme avec d’autres services Azure, il existe des limites concernant certaines ressources associées à Machine Learning. La taille du cluster de calcul est un exemple de limite. Apprenez-en davantage sur [les limites par défaut et comment augmenter votre quota](how-to-manage-quotas.md).

Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](http://aka.ms/AMLFree) dès aujourd’hui.


## <a name="create-a-workspace"></a>Créer un espace de travail 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]


## <a name="use-the-workspace"></a>Utiliser l'espace de travail

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]



Voyons maintenant comme un espace de travail vous permet de gérer vos scripts de machine learning. Dans cette section, vous allez exécuter les étapes suivantes :

* Ouvrir un bloc-notes dans Azure Notebooks.
* Exécuter le code qui crée certaines valeurs journalisées.
* Afficher les valeurs journalisées dans votre espace de travail.

Cet exemple montre comment l’espace de travail peut vous aider à suivre les informations générées dans un script. 

### <a name="open-a-notebook"></a>Ouvrir un bloc-notes 

[Azure Notebooks](https://notebooks.azure.com) offre une plateforme cloud gratuite pour les notebooks Jupyter, préconfigurée avec tout ce dont vous avez besoin pour exécuter Machine Learning. À partir de votre espace de travail, vous pouvez lancer cette plateforme pour commencer à utiliser votre espace de travail du service Azure Machine Learning.

1. Dans la page de l’espace de travail, sélectionnez **Explorer votre espace de travail de service Azure Machine Learning**.

 ![Explorer l’espace de travail](./media/quickstart-get-started/explore_aml.png)

1. Sélectionnez **Ouvrir Azure Notebooks** pour essayer votre première expérience dans Azure Notebooks.  Azure Notebooks est un service distinct qui vous permet d’exécuter des notebooks Jupyter gratuitement dans le cloud.  Quand vous utilisez ce lien vers le service, des informations sur la façon de se connecter à votre espace de travail sont ajoutées à la bibliothèque que vous créez dans Azure Notebooks.

 ![Ouvrir Azure Notebooks](./media/quickstart-get-started/explore_ws.png)

1. Connectez-vous à Azure Notebooks.  Veillez à vous connecter avec le même compte que celui que vous avez utilisé pour vous connecter au portail Azure. Votre organisation peut exiger le [consentement de l’administrateur](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent) avant que vous puissiez vous connecter.

1. Une fois que vous êtes connecté, un nouvel onglet s’ouvre et une invite `Clone Library` s’affiche. Le clonage de cette bibliothèque charge un ensemble de notebooks et d’autres fichiers dans votre compte Azure Notebooks.  Ces fichiers vous aident à explorer les fonctionnalités d’Azure Machine Learning.

1. Décochez la case **Public**, afin de ne pas partager les informations de votre espace de travail avec d’autres utilisateurs.

1. Sélectionnez **Cloner**.

 ![Cloner une bibliothèque](./media/quickstart-get-started/clone.png)

1. Si vous voyez que l’état du projet est « arrêté », cliquez sur **Exécuter sur la ressource de calcul gratuite** pour utiliser le serveur de notebooks gratuit.

    ![Exécuter un projet sur la ressource de calcul gratuite](./media/quickstart-get-started/run-project.png)

### <a name="run-the-notebook"></a>Exécuter le bloc-notes

Dans la liste des fichiers de ce projet, vous voyez un fichier `config.json`. Ce fichier config contient des informations sur l’espace de travail que vous avez créé dans le portail Azure.  Ce fichier permet à votre code de se connecter à votre espace de travail et d’y ajouter des informations.

1. Sélectionnez **01.run-experiment.ipynb** pour ouvrir le notebook.

1. La zone d’état vous indique d’attendre le démarrage du noyau.  Le message disparaît une fois que le noyau est prêt.

    ![Attendre que le noyau démarre](./media/quickstart-get-started/wait-for-kernel.png)

1. Une fois que le noyau a démarré, exécutez les cellules une à la fois en utilisant **Maj+Entrée**. Vous pouvez aussi sélectionner **Cellules** > **Exécuter tout** pour exécuter tout le notebook. Quand vous voyez un astérisque, __*__ à côté d’une cellule, cela signifie qu’elle est en cours d’exécution. À la fin de l’exécution du code de cette cellule, un numéro s’affiche. 

1. Suivez les instructions dans le notebook pour authentifier votre abonnement Azure.

Une fois que vous avez fini d’exécuter toutes les cellules dans le notebook, vous pouvez afficher les valeurs enregistrées dans votre espace de travail.

## <a name="view-logged-values"></a>Afficher les valeurs journalisées

1. La sortie de la cellule `run` contient un lien qui ramène au portail Azure pour voir les résultats de l’expérience dans votre espace de travail. 

    ![Afficher les expériences](./media/quickstart-get-started/view_exp.png)

1. Cliquez sur le **lien vers le portail Azure** pour voir des informations sur l’exécution dans votre espace de travail.  Le lien ouvre votre espace de travail dans le portail Azure.

1. Les tracés des valeurs consignées que vous voyez ont été créés automatiquement dans l’espace de travail. Chaque fois que vous consignez plusieurs valeurs avec le même paramètre de nom, un tracé est généré automatiquement pour vous.

   ![Afficher l’historique](./media/quickstart-get-started/web-results.png)

Étant donné que le code pour se rapprocher de pi utilise des valeurs aléatoires, vos tracés affichent des valeurs différentes.  

## <a name="clean-up-resources"></a>Supprimer des ressources 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Vous pouvez également conserver le groupe de ressources mais supprimer un espace de travail unique. Affichez les propriétés de l’espace de travail, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé les ressources nécessaires pour expérimenter des modèles et les déployer. Vous avez également exécuté du code dans un bloc-notes. Enfin, vous avez exploré l’historique des exécutions de ce code dans votre espace de travail dans le cloud.

Pour une expérience approfondie du flux de travail, suivez les didacticiels Machine Learning relatifs à l’apprentissage et au déploiement d’un modèle :  

> [!div class="nextstepaction"]
> [Tutoriel : Entraîner un modèle de classification d’images](tutorial-train-models-with-aml.md)
