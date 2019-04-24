---
title: Configurer le Service de notifications Push Apple dans Azure Notification Hubs | Microsoft Docs
description: Découvrez comment configurer un hub de notification Azure avec des paramètres de Service de Notification Push Apple (APNS).
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9a9db9f05895569b050e56cdeec1ee2ee25af0df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60237825"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurer les paramètres de Service de Notification Push Apple (APNS) pour un concentrateur de notification dans le portail Azure
Cet article vous montre comment configurer les paramètres de Service de Notification Push Apple (APNS) pour un hub de notification Azure à l’aide du portail Azure. 

## <a name="prerequisites"></a>Prérequis
Si vous n’avez pas déjà créé de hub de notification, créez-en un maintenant. Pour plus d’informations, consultez [Créer un hub de notification Azure dans le portail Azure](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Configurer le Service de notifications Push Apple

La procédure suivante vous donne les étapes pour configurer les paramètres de Service de Notification Push Apple (APNS) pour un concentrateur de notification :

1. Dans le portail Azure, sur le **Hub de Notification** page, sélectionnez **Apple (APNS)** sur le menu de gauche.

1. Pour **Mode d’authentification**, sélectionnez **Certificat** ou **Jeton**.

   a. Si vous sélectionnez **Certificat** :
   * Sélectionnez l’icône de fichier, puis sélectionnez le fichier *.p12* que vous voulez charger.
   * Entrez un mot de passe.
   * Sélectionnez le mode **Bac à sable**. Ou bien, pour envoyer des notifications Push aux utilisateurs qui ont acheté votre application sur le Store, sélectionnez le mode **Production**.

     ![Capture d’écran de la configuration d’un certificat APNS sur le portail Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Si vous sélectionnez **Jeton** :

   * Entrez les valeurs pour **ID de clé**, **ID d’offre groupée**, **ID de l’équipe** et **Jeton**.
   * Sélectionnez le mode **Bac à sable**. Ou bien, pour envoyer des notifications Push aux utilisateurs qui ont acheté votre application sur le Store, sélectionnez le mode **Production**.

     ![Capture d’écran de la configuration d’un jeton APNS sur le portail Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir un didacticiel avec des instructions pas à pas pour envoyer des notifications aux appareils iOS, consultez l’article suivant : [Notifications Push vers des appareils iOS avec Notification Hubs et APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
