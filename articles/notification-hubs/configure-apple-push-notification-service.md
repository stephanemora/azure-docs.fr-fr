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
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488272"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurer les paramètres de Service de Notification Push Apple (APNS) pour un concentrateur de notification dans le portail Azure
Cet article vous montre comment configurer les paramètres de Service de Notification Push Apple (APNS) pour un hub de notification Azure à l’aide du portail Azure. 

## <a name="prerequisites"></a>Conditions préalables
Si vous n’avez pas déjà créé un hub de notification, créez-en un maintenant. Pour plus d’informations, consultez [créer un hub de notification Azure dans le portail Azure](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Configurer le Service de notifications Push Apple

La procédure suivante vous donne les étapes pour configurer les paramètres de Service de Notification Push Apple (APNS) pour un concentrateur de notification :

1. Dans le portail Azure, sur le **Hub de Notification** page, sélectionnez **Apple (APNS)** sur le menu de gauche.

1. Pour **Mode d’authentification**, sélectionnez **certificat** ou **jeton**.

   a. Si vous sélectionnez **certificat**:
   * Sélectionnez l’icône du fichier, puis le *.p12* fichier à télécharger.
   * Entrez un mot de passe.
   * Sélectionnez le mode **Bac à sable**. Ou, pour envoyer des notifications push aux utilisateurs ayant acheté votre application à partir du magasin, sélectionnez **Production** mode.

     ![Capture d’écran d’un APNS certificat de configuration dans le portail Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Si vous sélectionnez **jeton**:

   * Entrez les valeurs pour **ID de la clé**, **ID d’offre groupée**, **ID de l’équipe**, et **jeton**.
   * Sélectionnez le mode **Bac à sable**. Ou, pour envoyer des notifications push aux utilisateurs ayant acheté votre application à partir du magasin, sélectionnez **Production** mode.

     ![Capture d’écran d’un APNS jeton configuration dans le portail Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir un didacticiel avec des instructions pas à pas pour envoyer des notifications aux appareils iOS, consultez l’article suivant : [Notifications Push aux appareils iOS à l’aide de Notification Hubs et APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
