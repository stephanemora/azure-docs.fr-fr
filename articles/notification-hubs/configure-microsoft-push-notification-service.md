---
title: Configurer le service de notifications Push dans Azure Notification Hubs | Microsoft Docs
description: Découvrez comment configurer les paramètres du service de notifications Push Microsoft pour un hub de notifications Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 1c76b44438e6527439d0a370c92f4120424b8da5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60240311"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurer les paramètres du service de notifications Push Microsoft pour un hub de notifications dans le portail Azure
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

