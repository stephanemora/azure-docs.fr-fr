---
title: Fichier Include
description: Fichier Include
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: e34325094060e31f9915a04c7aabdeee6aa33ef2
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012115"
---
1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Dans le volet de gauche, sélectionnez **Créer une ressource**. Recherchez **digital twins**, puis sélectionnez **Digital Twins**. Sélectionnez **Créer** pour commencer le processus de déploiement.

   ![Sélections pour la création d’une nouvelle instance Digital Twins](./media/create-digital-twins-portal/create-digital-twins.png)

1. Dans le volet **Digital Twins**, entrez les informations suivantes :
   * **Nom de la ressource** : créez un nom unique pour votre instance Digital Twins.
   * **Abonnement**: choisissez l’abonnement que vous souhaitez utiliser pour créer cette instance Digital Twins. 
   * **Groupe de ressources** : sélectionnez ou créez un [groupe de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) pour l’instance Digital Twins.
   * **Emplacement** : Sélectionnez l’emplacement le plus proche de vos appareils.

     ![Volet Digital Twins avec les informations entrées](./media/create-digital-twins-portal/create-digital-twins-param.png)

1. Vérifiez les informations de votre instance Digital Twins et sélectionnez **Créer**. La création de votre instance Digital Twins peut prendre quelques minutes. Vous pouvez suivre la progression dans le volet **Notifications**.

1. Ouvrez le volet **Vue d’ensemble** de votre instance Digital Twins. Notez le lien sous **API de gestion**.

   L’URL de l’**API de gestion** respecte le format suivant : `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger`. Cette URL vous renvoie à la documentation de l’API REST d’Azure Digital Twins qui correspond à votre instance. Pour savoir comment lire et utiliser la documentation de cette API, consultez la section [Comment utiliser Azure Digital Twins Swagger](../articles/digital-twins/how-to-use-swagger.md).

    Modifiez l’URL de l’**API de gestion** pour qu’elle respecte le format suivant : `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Votre application se servira de l’URL modifiée comme de l’URL de base pour accéder à votre instance. Copiez cette URL modifiée dans un fichier temporaire. Vous en aurez besoin dans la prochaine section.

    ![API de gestion](./media/create-digital-twins-portal/digital-twins-management-api.png)