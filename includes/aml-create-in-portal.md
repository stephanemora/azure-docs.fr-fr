---
title: Fichier Include
description: Fichier Include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/21/2019
ms.openlocfilehash: 72f23b10047928f32886d9054f4dd1abdc569bd8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66396952"
---
1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec les informations d’identification de l’abonnement Azure que vous utilisez. 

   ![Portail Azure](./media/aml-create-in-portal/portal-dashboard-05-2019.png)

1. Dans l’angle supérieur gauche du portail, sélectionnez **Créer une ressource**.

   ![Créer une ressource dans le portail Azure](./media/aml-create-in-portal/portal-create-a-resource-05-2019.png)

1. Dans la barre de recherche, entrez **Machine Learning**. Sélectionnez le résultat de la recherche **Espace de travail du service Machine Learning**.

   ![Rechercher un espace de travail](./media/aml-create-in-portal/allservices-search-05-2019.png)

1. Dans le volet **Espace de travail du service Machine Learning**, sélectionnez **Créer** pour commencer.

    ![Bouton Créer](./media/aml-create-in-portal/portal-create-button-05-2019.png)

1. Dans le volet **Espace de travail ML**, configurez votre espace de travail.

   Champ|Description
   ---|---
   Nom de l’espace de travail |Entrez un nom unique qui identifie votre espace de travail. Dans cet exemple, nous allons utiliser **docs-ws**. Dans le groupe de ressources, les noms doivent être uniques. Utilisez un nom dont il est facile de se rappeler et que vous pouvez facilement différencier des autres espaces de travail.  
   Abonnement |Sélectionnez l’abonnement Azure que vous souhaitez utiliser.
   Groupe de ressources | Utilisez un groupe de ressources existant dans votre abonnement, ou entrez un nom pour créer un groupe de ressources. Un groupe de ressources contient les ressources associées d’une solution Azure. Dans cet exemple, nous allons utiliser **docs-aml**. 
   Lieu | Sélectionnez l’emplacement le plus proche de vos utilisateurs et des ressources de données. C’est là que l’espace de travail est créé.

   ![Créer un espace de travail](./media/aml-create-in-portal/workspace-create-main-tab-05-2019.png)

1. Pour démarrer le processus de création, sélectionnez **Vérifier + créer**.

    ![Créer](./media/aml-create-in-portal/workspace-create-main-review-button-05-2019.png)

1. Passez en revue la configuration de votre espace de travail. Si elle est correcte, sélectionnez **Créer**. La création de l’espace de travail peut prendre quelques instants.

    ![Créer](./media/aml-create-in-portal/workspace-create-review-tab-05-2019.png)

1. Pour vérifier l’état du déploiement, sélectionnez l’icône Notifications, représentée par une **cloche**, dans la barre d’outils.

1. Une fois le processus terminé, un message indiquant la réussite du déploiement s’affiche. Ce message s’affiche également dans la section des notifications. Pour afficher le nouvel espace de travail, sélectionnez **Accéder à la ressource**.

   ![État de création de l’espace de travail](./media/aml-create-in-portal/notifications-05-2019.png)
