---
title: Fichier Include
description: Fichier Include
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 00b7ffcba876b6abea59cff170331c7413a61d39
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823204"
---
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** dans le menu de gauche, puis sélectionnez **Notification Hubs** dans la section **Mobile**. Sélectionnez l’étoile (`*`) en regard du nom du service pour l’ajouter à la section **FAVORIS** dans le menu de gauche. Une fois que **Notification Hubs** est ajouté aux **FAVORIS**, sélectionnez-le dans le menu de gauche. 

      ![Portail Azure - sélectionner Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)
3. Dans la page **Notification Hubs**, sélectionnez **Ajouter** dans la barre d’outils. 

      ![Notification Hubs - bouton Ajouter dans la barre d’outils](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)
4. Dans la page **Notification Hub**, effectuez les étapes suivantes : 
    1. Spécifiez un **nom** pour le **hub** de notification.  
    2. Spécifiez un **nom** pour l’**espace de noms**.
    3. Sélectionnez un **emplacement** dans lequel vous voulez créer le hub de notification. 
    4. Sélectionnez un groupe de ressources existant ou entrez un nom pour le nouveau **groupe de ressources**.
    5. Sélectionnez **Créer**. 

        ![Portail Azure - Définition des propriétés du hub de notification](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)
4. Sélectionnez **Notifications** (icône représentant une cloche), puis sélectionnez **Accéder à la ressource**, ou bien actualisez la liste dans la page **Notification Hubs**, puis sélectionnez votre hub de notification. 

      ![Portail Azure -> Notifications -> Accéder à la ressource](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)
5. Sélectionnez **Stratégies d’accès** dans la liste. Notez les deux chaînes de connexion sont disponibles pour vous. Vous en avez besoin pour gérer les notifications Push plus tard.

      >[!IMPORTANT]
      >N’utilisez **PAS** le DefaultFullSharedAccessSignature dans votre application. Il est prévu pour être utilisé uniquement dans votre serveur principal.
      >

      ![Portail Azure - Chaînes de connexion du hub de notification](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
