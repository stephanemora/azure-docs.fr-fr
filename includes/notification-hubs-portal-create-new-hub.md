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
ms.openlocfilehash: 244a4ebe20863945bfc3b6236e70e786387c8909
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116679"
---
1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Sélectionnez **Tous les services** dans le menu de gauche, puis sélectionnez **Notification Hubs** dans la section **Mobile**. Sélectionnez l’icône d’étoile en regard du nom du service pour l’ajouter à la section **FAVORIS** dans le menu de gauche. Après avoir ajouté **Notification Hubs** aux **FAVORIS**, sélectionnez-le dans le menu de gauche.

      ![Portail Azure - sélectionner Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Dans la page **Notification Hubs**, sélectionnez **Ajouter** dans la barre d’outils.

      ![Notification Hubs - bouton Ajouter dans la barre d’outils](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Dans la page **Notification Hub**, effectuez les étapes suivantes :

    1. Entrez un nom dans **Notification Hub**.  

    1. Entrez un nom dans **Créer un espace de noms**. Un espace de noms contient un ou plusieurs hubs.

    1. Sélectionnez une valeur dans la zone de liste déroulante **Emplacement**. Cette valeur spécifie l’emplacement où vous voulez créer le hub de notification.

    1. Sélectionnez un groupe de ressources existant dans **Groupe de ressources**, ou créez un nom pour un nouveau groupe.

    1. Sélectionnez **Créer**.

        ![Portail Azure - Définition des propriétés du hub de notification](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Sélectionnez **Notifications** (icône de cloche), puis **Accéder à la ressource**. Vous pouvez également actualiser la liste dans la page **Notification Hubs** et sélectionner votre hub de notification.

      ![Portail Azure -> Notifications -> Accéder à la ressource](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Sélectionnez **Stratégies d’accès** dans la liste. Notez les deux chaînes de connexion sont disponibles pour vous. Vous en aurez besoin pour gérer les notifications Push.

      >[!IMPORTANT]
      >N’utilisez *pas* la stratégie **DefaultFullSharedAccessSignature** dans votre application. Elle est censée être utilisée uniquement dans votre back-end.
      >

      ![Portail Azure - Chaînes de connexion du hub de notification](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
