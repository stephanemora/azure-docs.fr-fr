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
ms.openlocfilehash: 6d6e6a2aea867c5b603d950a4bbaa33f14695f45
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47010971"
---
Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide des informations d’identification de l’abonnement Azure que vous allez utiliser. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) maintenant.

Le tableau de bord de l’espace de travail du portail est pris en charge uniquement sur les navigateurs Edge, Chrome et Firefox.

   ![Portail Azure](./media/aml-create-in-portal/portal-dashboard.png)

Cliquez sur le bouton **Créer une ressource** (+) dans le coin supérieur gauche du portail.

   ![Créer une ressource dans le portail Azure](./media/aml-create-in-portal/portal-create-a-resource.png)

Tapez **Machine Learning** dans la barre de recherche. Sélectionnez le résultat de la recherche intitulé **Espace de travail Machine Learning**.

   ![Rechercher un espace de travail](./media/aml-create-in-portal/allservices-search.PNG)

Dans le volet **Espace de travail Azure Machine Learning**, faites défiler vers le bas, puis sélectionnez **Créer**.

   ![create](./media/aml-create-in-portal/portal-create-button.png)

Dans le volet **Espace de travail ML**, configurez votre espace de travail.

   Champ|Description
   ---|---
   Nom de l’espace de travail |Entrez un nom unique qui identifie votre espace de travail.  Ici, nous allons utiliser docs-ws. Dans le groupe de ressources, les noms doivent être uniques. Utilisez un nom dont il est facile de se rappeler et que vous pouvez facilement différencier des autres espaces de travail.  
   Abonnement |Choisissez l’abonnement Azure que vous souhaitez utiliser. Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel la ressource est facturée.
   Groupe de ressources | Utilisez un groupe de ressources existant dans votre abonnement, ou entrez un nom pour créer un groupe de ressources. Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure.  Ici, nous allons utiliser docs-aml. 
   Lieu | Choisissez l’emplacement le plus proche de vos utilisateurs et des ressources de données. C’est là que l’espace de travail est créé.

   ![Créer un espace de travail](./media/aml-create-in-portal/workspace-create.png)

Sélectionnez **Créer** pour commencer le processus de création.  La création de l’espace de travail peut prendre quelques instants.

   Pour vérifier l’état du déploiement, sélectionnez l’icône Notifications, représentée par une cloche dans la barre d’outils.

   ![Créer un espace de travail](./media/aml-create-in-portal/notifications.png)

   Lorsque vous avez terminé, un message indiquant la réussite du déploiement s’affiche.  Ce message s’affiche également dans la section Notifications.   Cliquez sur le bouton **Accéder à la ressource** pour afficher le nouvel espace de travail.
