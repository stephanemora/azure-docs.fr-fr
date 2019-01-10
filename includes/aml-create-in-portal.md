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
ms.date: 09/24/2018
ms.openlocfilehash: 6f73b15ed16cfe26bf14e60a5206568e1a1564fd
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53594317"
---
Connectez-vous au [portail Azure](https://portal.azure.com/) avec les informations d’identification de l’abonnement Azure que vous utilisez. 

Le tableau de bord de l’espace de travail du portail est pris en charge uniquement sur les navigateurs Edge, Chrome et Firefox.

   ![Portail Azure](./media/aml-create-in-portal/portal-dashboard.png)

Dans l’angle supérieur gauche du portail, sélectionnez **Créer une ressource**.

   ![Créer une ressource dans le portail Azure](./media/aml-create-in-portal/portal-create-a-resource.png)

Dans la barre de recherche, entrez **Machine Learning**. Sélectionnez le résultat de la recherche **Espace de travail du service Machine Learning**.

   ![Rechercher un espace de travail](./media/aml-create-in-portal/allservices-search.PNG)

Dans le volet **Espace de travail du service Machine Learning**, faites défiler vers le bas et sélectionnez **Créer** pour commencer.

   ![Créer](./media/aml-create-in-portal/portal-create-button.png)

Dans le volet **Espace de travail ML**, configurez votre espace de travail.

   Champ|Description
   ---|---
   Nom de l’espace de travail |Entrez un nom unique qui identifie votre espace de travail. Dans cet exemple, nous allons utiliser **docs-ws**. Dans le groupe de ressources, les noms doivent être uniques. Utilisez un nom dont il est facile de se rappeler et que vous pouvez facilement différencier des autres espaces de travail.  
   Abonnement |Sélectionnez l’abonnement Azure que vous souhaitez utiliser.
   Groupe de ressources | Utilisez un groupe de ressources existant dans votre abonnement, ou entrez un nom pour créer un groupe de ressources. Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Dans cet exemple, nous allons utiliser **docs-aml**. 
   Lieu | Sélectionnez l’emplacement le plus proche de vos utilisateurs et des ressources de données. C’est là que l’espace de travail est créé.

   ![Créer un espace de travail](./media/aml-create-in-portal/workspace-create.png)

Pour démarrer le processus de création, sélectionnez **Créer**. La création de l’espace de travail peut prendre quelques instants.

Pour vérifier l’état du déploiement, sélectionnez l’icône Notifications, représentée par une **cloche**, dans la barre d’outils.

   ![État de création de l’espace de travail](./media/aml-create-in-portal/notifications.png)

Une fois le processus terminé, un message indiquant la réussite du déploiement s’affiche. Ce message s’affiche également dans la section des notifications. Pour afficher le nouvel espace de travail, sélectionnez **Accéder à la ressource**.
