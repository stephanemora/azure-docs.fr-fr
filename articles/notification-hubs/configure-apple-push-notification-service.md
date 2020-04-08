---
title: Configurer Apple Push Notification Service dans Azure Notification Hubs | Microsoft Docs
description: Découvrez comment configurer un concentrateur de notifications Aure avec les paramètres d’Apple Push Notification Service.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: eb1122ba3de0002507589d3e607d1e39d905c308
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127512"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurer les paramètres d’Apple Push Notification Service pour un hub de notifications dans le portail Azure

Cet article explique comment configurer les paramètres d’Apple Push Notification Service pour un hub de notifications Azure à l’aide du portail Azure. 

## <a name="prerequisites"></a>Prérequis
Si vous n’avez pas déjà créé de hub de notification, créez-en un maintenant. Pour plus d’informations, consultez [Créer un hub de notification Azure dans le portail Azure](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Configurer Apple Push Notification Service

La procédure qui suit vous guide dans la configuration des paramètres d’Apple Push Notification Service pour un hub de notifications :

1. Dans le portail Azure, dans la page **Hub de notification**, sélectionnez **Apple (APNS)** dans le menu de gauche.

1. Pour **Mode d’authentification**, sélectionnez **Certificat** ou **Jeton**.

   a. Si vous sélectionnez **Certificat** :
   * Sélectionnez l’icône de fichier, puis sélectionnez le fichier *.p12* que vous voulez charger.
   * Entrez un mot de passe.
   * Sélectionnez le mode **Bac à sable**. Ou bien, pour envoyer des notifications Push aux utilisateurs qui ont acheté votre application sur le Store, sélectionnez le mode **Production**.

     ![Capture d’écran de la configuration d’un certificat APNS sur le portail Azure](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   b. Si vous sélectionnez **Jeton** :

   * Entrez les valeurs pour **ID de clé**, **ID d’offre groupée**, **ID de l’équipe** et **Jeton**.
   * Sélectionnez le mode **Bac à sable**. Ou bien, pour envoyer des notifications Push aux utilisateurs qui ont acheté votre application sur le Store, sélectionnez le mode **Production**.

     ![Capture d’écran de la configuration d’un jeton APNS sur le portail Azure](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Étapes suivantes
Pour consulter un didacticiel proposant des instructions étape par étape pour transmettre les notifications Push vers les périphériques iOS, veuillez consulter l’article suivant : [Notifications Push vers des appareils iOS avec Notification Hubs et APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
