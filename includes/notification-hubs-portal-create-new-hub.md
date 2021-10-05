---
title: fichier descriptif
description: Fichier Include
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 09/07/2021
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 9d33df05e54d9cdeb97631ca479dd8ac9869343c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "126056687"
---
1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Tous les services** dans le menu de gauche, puis sélectionnez **Notification Hubs** dans la section **Mobile**. Sélectionnez l’icône d’étoile en regard du nom du service pour l’ajouter à la section **FAVORIS** dans le menu de gauche. Après avoir ajouté **Notification Hubs** aux **FAVORIS**, sélectionnez-le dans le menu de gauche.

      ![Portail Azure - sélectionner Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Dans la page **Notification Hubs**, sélectionnez **Créer** dans la barre d’outils.

      ![Notification Hubs - bouton Ajouter dans la barre d’outils](./media/notification-hubs-portal-create-new-hub/create-toolbar-button.png)

1. Sous l’onglet **Informations de base** de la page **Notification Hub**, effectuez les étapes suivantes :

    1. Dans **Abonnement**, sélectionnez le nom de l’abonnement Azure que vous souhaitez utiliser, puis sélectionnez un groupe de ressources existant ou créez-en un.  

    1. Entrez un nom unique pour le nouvel espace de noms dans **Détails de l’espace de noms**. 
    
    1. Un espace de noms contient un ou plusieurs hubs de notification, donc tapez un nom pour le hub dans **Détails du hub de notification**. Ou sélectionnez un espace de noms existant dans la liste déroulante.

    1. Sélectionnez une valeur dans la zone de liste déroulante **Emplacement**. Cette valeur spécifie l’emplacement où vous voulez créer le hub.

    1. Sélectionnez **Create** (Créer).

        ![Portail Azure - Définition des propriétés du hub de notification](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Sélectionnez **Notifications** (icône de cloche), puis **Accéder à la ressource**. Vous pouvez également actualiser la liste dans la page **Notification Hubs** et sélectionner votre hub.

      ![Portail Azure -Accéder à la ressource](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Sélectionnez **Stratégies d’accès** dans la liste. Notez que les deux chaînes de connexion sont disponibles pour vous. Vous en aurez besoin pour gérer les notifications Push.

      >[!IMPORTANT]
      >N’utilisez *pas* la stratégie **DefaultFullSharedAccessSignature** dans votre application. Elle est censée être utilisée uniquement dans votre back-end.
      >

      ![Portail Azure - Chaînes de connexion du hub de notification](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
