---
title: Fichier Include
description: Fichier Include
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c718fa972b9d6cba13a13edd3c7e8df29fa63207
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Sélectionnez **Créer une ressource** > **Web + Mobile** > **Hub de notification**.
   
      ![Portail Azure - Création d’un hub de notification](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
3. Dans la zone **Hub de notification**, saisissez un nom unique. Sélectionnez votre **Région**, **Abonnement** et **Groupe de ressources** (si vous en avez déjà un). 
   
      Si vous ne disposez pas d’un espace de noms Service Bus, vous pouvez utiliser le nom par défaut, qui est créé à partir du nom du hub (si l’espace de noms est disponible).
    
      Si vous disposez déjà d’un espace de noms Service Bus dans lequel vous voulez créer le hub, suivez les étapes suivantes

    a. Dans la zone **Espace de noms**, sélectionnez le lien **Sélectionner un existant**. 
   
    b. Sélectionnez **Créer**.
   
      ![Portail Azure - Définition des propriétés du hub de notification](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

4. Sélectionnez **Notifications** (icône de cloche) puis **Accéder à la ressource**. 

      ![Portail Azure -> Notifications -> Accéder à la ressource](./media/notification-hubs-portal-create-new-hub/notification-go-to-resource.png)    
5. Sélectionnez **Stratégies d’accès** dans la liste. Notez les deux chaînes de connexion sont disponibles pour vous. Vous en avez besoin pour gérer les notifications Push plus tard.

      >[!IMPORTANT]
      >N’utilisez **PAS** le DefaultFullSharedAccessSignature dans votre application. Il est prévu pour être utilisé uniquement dans votre serveur principal.
      >
   
      ![Portail Azure - Chaînes de connexion du hub de notification](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

