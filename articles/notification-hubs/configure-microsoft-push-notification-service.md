---
title: Configurer le service de notifications Push dans Azure Notification Hubs | Microsoft Docs
description: Découvrez comment configurer les paramètres du service de notifications Push Microsoft pour un hub de notifications Azure.
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
ms.openlocfilehash: 99f29e7910fe6070c6202f6a936173455f979732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127338"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>Configurer les paramètres du service de notifications Push Microsoft dans le portail Azure

Cet article explique comment configurer les paramètres du service de notifications Push Microsoft pour un hub de notifications Azure à l’aide du portail Azure. 

## <a name="prerequisites"></a>Prérequis
Si vous n’avez pas déjà créé de hub de notification, créez-en un maintenant. Pour plus d’informations, consultez [Créer un hub de notification Azure dans le portail Azure](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Configurer Microsoft Push Notification Service (MPNS)

La procédure qui suit vous guide dans la configuration des paramètres du service de notifications Push Microsoft pour un hub de notifications : 

1. Dans la page **Hub de notification** du portail Azure, sélectionnez **Windows Phone (MPNS)** dans le menu de gauche.
1. Activez les notifications Push authentifiées ou non authentifiées :

   a. Pour activer les notifications Push non authentifiées, sélectionnez **Activer les notifications Push non authentifiées** > **Enregistrer**.

      ![Capture d’écran montrant comment activer les notifications Push non authentifiées](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Pour activer les notifications Push authentifiées :
      * Dans la barre d’outils, sélectionnez **Charger le certificat**.
      * Sélectionnez l’icône de fichier, puis le fichier de certificat.
      * Entrez le mot de passe du certificat.
      * Sélectionnez **OK**.
      * Dans la page **Windows Phone (MPNS)** , sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
Pour consulter un didacticiel avec des instructions pas à pas pour envoyer des notifications vers les appareils Windows Phone à l’aide d’Azure Notification Hubs et du Service de Notification Push Microsoft (MPNS), consultez [Notifications Push pour les applications Windows Phone à l’aide de Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).

