---
title: Configurer le Service de notifications Push de Microsoft dans Azure Notification Hubs | Microsoft Docs
description: Découvrez comment configurer les paramètres de Service de Notification Push Microsoft pour un hub de notification Azure.
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
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488259"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurer les paramètres de Service de Notification Push Microsoft (MPNS) pour un concentrateur de notification dans le portail Azure
Cet article vous montre comment configurer les paramètres de Service de Notification Push Microsoft (MPNS) pour un hub de notification Azure à l’aide du portail Azure. 

## <a name="prerequisites"></a>Conditions préalables
Si vous n’avez pas déjà créé un hub de notification, créez-en un maintenant. Pour plus d’informations, consultez [créer un hub de notification Azure dans le portail Azure](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Configurer le Service de Notification Push Microsoft (MPNS)

La procédure suivante vous donne les étapes pour configurer les paramètres de Service de Notification Push Microsoft (MPNS) pour un concentrateur de notification : 

1. Dans le portail Azure, sur le **Hub de Notification** page, sélectionnez **Windows Phone (MPNS)** sur le menu de gauche.
1. Activer les deux notifications push non authentifiées ou authentifié :

   a. Pour activer les notifications push non authentifiées, sélectionnez **activer les notifications push non authentifiées** > **enregistrer**.

      ![Capture d’écran montrant comment activer les notifications push non authentifiées](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Pour activer les notifications push authentifié :
      * Dans la barre d’outils, sélectionnez **télécharger un certificat**.
      * Sélectionnez l’icône de fichier, puis sélectionnez le fichier de certificat.
      * Entrez le mot de passe du certificat.
      * Sélectionnez **OK**.
      * Sur le **Windows Phone (MPNS)** page, sélectionnez **enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir un didacticiel avec des instructions pas à pas pour envoyer des notifications vers les appareils Windows Phone à l’aide d’Azure Notification Hubs et Service de Notification Push Microsoft (MPNS), consultez [des notifications Push pour les applications Windows Phone à l’aide de Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).

