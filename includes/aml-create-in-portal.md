---
title: Fichier include
description: Fichier Include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/11/2021
ms.openlocfilehash: 42af7691d7401e397e7e0d9819fded60b3821b26
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109785327"
---
1. Connectez-vous au [Portail Azure](https://portal.azure.com/) à l’aide des informations d’identification de votre abonnement Azure.

1. En haut à gauche du portail Azure, sélectionnez les trois lignes, puis **+Créer une ressource**.

    :::image type="content" source="media/aml-create-in-portal/create-resource.png" alt-text="Capture d’écran montrant +Créer une ressource.":::

1. Utilisez la barre de recherche pour rechercher **Machine Learning**.

1. Sélectionnez **Machine Learning**.

   :::image type="content" source="media/aml-create-in-portal/machine-learning.png" alt-text="Capture d’écran affiche les résultats de la recherche pour sélectionner Machine Learning.":::


1. Dans le volet **Machine Learning**, sélectionnez **Créer** pour commencer.

1. Fournissez les informations suivantes pour configurer votre nouvel espace de travail :

   Champ|Description
   ---|---
   Nom de l’espace de travail |Entrez un nom unique qui identifie votre espace de travail. Dans cet exemple, nous allons utiliser **docs-ws**. Dans le groupe de ressources, les noms doivent être uniques. Utilisez un nom dont il est facile de se rappeler et que vous pouvez facilement différencier des autres espaces de travail.
   Subscription |Sélectionnez l’abonnement Azure que vous souhaitez utiliser.
   Resource group | Utilisez un groupe de ressources existant dans votre abonnement, ou entrez un nom pour créer un groupe de ressources. Un groupe de ressources contient les ressources associées d’une solution Azure. Dans cet exemple, nous allons utiliser **docs-aml**. 
   Location | Sélectionnez l’emplacement le plus proche de vos utilisateurs et des ressources de données pour créer votre espace de travail.

1. Lorsque vous avez terminé de configurer l’espace de travail, sélectionnez **Vérifier + créer**.
1. Sélectionnez **Créer** pour créer l’espace de travail.

   > [!Warning]
   > La création de votre espace de travail dans le cloud peut prendre plusieurs minutes.

   Une fois le processus terminé, un message indiquant la réussite du déploiement s’affiche.
 
 1. Pour afficher le nouvel espace de travail, sélectionnez **Accéder à la ressource**.
 1. Dans la vue du portail de votre espace de travail, sélectionnez **Lancer Studio** pour accéder à Azure Machine Learning studio. 

