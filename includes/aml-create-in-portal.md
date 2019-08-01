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
ms.date: 07/21/2019
ms.openlocfilehash: 51bd3dfb33b1f445db8672e1b987ee6c6242e09c
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370826"
---
1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec les informations d’identification de l’abonnement Azure que vous utilisez. 

1. Dans l’angle supérieur gauche du portail, sélectionnez **Créer une ressource**.

   ![Créer une ressource dans le portail Azure](./media/aml-create-in-portal/portal-create-a-resource-07-2019.png)

1. Utilisez la barre de recherche pour rechercher **Espace de travail Machine Learning service**.

1. Sélectionnez **Espace de travail Machine Learning service**.

1. Dans le volet **Espace de travail du service Machine Learning**, sélectionnez **Créer** pour commencer.

1. Configurez votre nouvel espace de travail en fournissant le nom de l’espace de travail, l’abonnement, le groupe de ressources et l’emplacement.

    ![Créer un espace de travail](./media/aml-create-in-portal/workspace-create-main-tab.png)

   Champ|Description
   ---|---
   Nom de l’espace de travail |Entrez un nom unique qui identifie votre espace de travail. Dans cet exemple, nous allons utiliser **docs-ws**. Dans le groupe de ressources, les noms doivent être uniques. Utilisez un nom dont il est facile de se rappeler et que vous pouvez facilement différencier des autres espaces de travail.  
   Subscription |Sélectionnez l’abonnement Azure que vous souhaitez utiliser.
   Resource group | Utilisez un groupe de ressources existant dans votre abonnement, ou entrez un nom pour créer un groupe de ressources. Un groupe de ressources contient les ressources associées d’une solution Azure. Dans cet exemple, nous allons utiliser **docs-aml**. 
   Location | Sélectionnez l’emplacement le plus proche de vos utilisateurs et des ressources de données pour créer votre espace de travail.

1. Une fois que vous avez terminé de configurer l’espace de travail, sélectionnez **Créer**. 

   La création de l’espace de travail peut prendre quelques instants.

   Une fois le processus terminé, un message indiquant la réussite du déploiement s’affiche. Ce message s’affiche également dans la section des notifications. Pour afficher le nouvel espace de travail, sélectionnez **Accéder à la ressource**.

   ![État de création de l’espace de travail](./media/aml-create-in-portal/notifications.png)
