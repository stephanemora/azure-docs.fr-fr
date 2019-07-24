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
ms.openlocfilehash: 2ec45b67367198c14fc9d03cdb659a51aed8a504
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841480"
---
1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec les informations d’identification de l’abonnement Azure que vous utilisez. 

   ![Portail Azure](./media/aml-create-in-portal/portal-dashboard-05-2019.png)

1. Dans l’angle supérieur gauche du portail, sélectionnez **Créer une ressource**.

   ![Créer une ressource dans le portail Azure](./media/aml-create-in-portal/portal-create-a-resource-07-2019.png)

1. Utilisez la barre de recherche pour sélectionner **Espace de travail Machine Learning service**.

   ![Rechercher un espace de travail](./media/aml-create-in-portal/allservices-search.png)

1. Dans le volet **Espace de travail du service Machine Learning**, sélectionnez **Créer** pour commencer.

    ![Bouton Créer](./media/aml-create-in-portal/portal-create-button.png)

1. Dans le volet **Espace de travail ML**, configurez votre espace de travail.

    ![Créer un espace de travail](./media/aml-create-in-portal/workspace-create-main-tab.png)

   Champ|Description
   ---|---
   Nom de l’espace de travail |Entrez un nom unique qui identifie votre espace de travail. Dans cet exemple, nous allons utiliser **docs-ws**. Dans le groupe de ressources, les noms doivent être uniques. Utilisez un nom dont il est facile de se rappeler et que vous pouvez facilement différencier des autres espaces de travail.  
   Subscription |Sélectionnez l’abonnement Azure que vous souhaitez utiliser.
   Resource group | Utilisez un groupe de ressources existant dans votre abonnement, ou entrez un nom pour créer un groupe de ressources. Un groupe de ressources contient les ressources associées d’une solution Azure. Dans cet exemple, nous allons utiliser **docs-aml**. 
   Location | Sélectionnez l’emplacement le plus proche de vos utilisateurs et des ressources de données. C’est là que l’espace de travail est créé.

1. Révisez la configuration de votre espace de travail, puis sélectionnez **Créer**. La création de l’espace de travail peut prendre quelques instants.

1. Une fois le processus terminé, un message indiquant la réussite du déploiement s’affiche. Ce message s’affiche également dans la section des notifications. Pour afficher le nouvel espace de travail, sélectionnez **Accéder à la ressource**.

   ![État de création de l’espace de travail](./media/aml-create-in-portal/notifications.png)
