---
title: Créer un hub de notification Azure à l’aide du portail Azure | Microsoft Docs
description: Dans ce tutoriel, vous allez apprendre à créer un hub de notification Azure à l’aide du portail Azure.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 3aeeb989d15dc74849c85fa58cbefa891809f3c5
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80347085"
---
# <a name="quickstart-create-an-azure-notification-hub-in-the-azure-portal"></a>Démarrage rapide : Créer un hub de notification Azure dans le portail Azure 
Azure Notification Hubs fournit un moteur d’envoi de notifications Push facile à utiliser et à grande échelle qui vous permet d’envoyer des notifications à n’importe quelle plateforme (iOS, Android, Windows, Kindle, Baidu, etc.) à partir de n’importe quel serveur principal (cloud ou local). Pour plus d’informations sur le service, consultez [Présentation d’Azure Notification Hubs](notification-hubs-push-notification-overview.md).

Dans ce guide de démarrage rapide, vous créez un hub de notification dans le portail Azure. La première section vous présente les étapes pour créer un espace de noms Notification Hubs et un hub dans cet espace de noms. La deuxième section vous présente les étapes pour créer un hub de notification dans un espace de noms Notification Hubs existant. 

## <a name="create-a-namespace-and-a-notification-hub"></a>Créer un espace de noms et un hub de notification
Dans cette section, vous créez un espace de noms et un hub dans l’espace de noms. 

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>Créer un hub de notification dans un espace de noms existant
Dans cette section, vous créez un hub de notification dans un espace de noms existant. 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** dans le menu de gauche, recherchez **Hub de notification**, sélectionnez l’**étoile** (`*`) en regard d’**Espaces de noms du hub de notification** pour l’ajouter à la section **FAVORIS** dans le menu de gauche. Sélectionnez **Espaces de noms du hub de notification**. 

      ![Portail Azure - sélectionner Espaces de noms du hub de notification](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. Dans la page **Espaces de noms du hub de notification**, sélectionnez votre espace de noms dans la liste. 

      ![Sélectionner votre espace de noms dans la liste](./media/create-notification-hub-portal/select-namespace.png)
1. Dans la page **Espaces de noms du hub de notification**, sélectionnez **Ajouter un hub** dans la barre d’outils. 

      ![Espaces de noms du hub de notification - bouton Ajouter un hub](./media/create-notification-hub-portal/add-hub-button.png)
4. Dans la page **Nouveau hub de notification**, entrez un nom pour le hub de notification, puis sélectionnez **OK**.

      ![Page Nouveau hub de notification -> entrer un nom pour votre hub](./media/create-notification-hub-portal/new-notification-hub-page.png)
4. Sélectionnez **Notifications** (icône de cloche) en haut pour voir l’état du déploiement du nouveau hub. Sélectionnez **X** dans l’angle droit pour fermer la fenêtre de notification. 

      ![Notification de déploiement](./media/create-notification-hub-portal/deployment-notification.png)
5. Actualisez la page web **Espaces de noms du hub de notification** pour voir votre nouveau hub dans la liste. 

      ![Portail Azure -> Notifications -> Accéder à la ressource](./media/create-notification-hub-portal/new-hub-in-list.png)
6. Sélectionnez votre **hub de notification** pour afficher sa page d’accueil. 

      ![Portail Azure -> Notifications -> Accéder à la ressource](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez créé un hub de notification. Pour savoir comment configurer le hub avec les paramètres PNS (Platform Notification System), consultez [Configurer un hub de notification avec les paramètres PNS](configure-notification-hub-portal-pns-settings.md). 