---
title: Configurer le Service de notifications Push Windows dans Azure Notification Hubs | Microsoft Docs
description: Découvrez comment configurer les paramètres de Service de Notification Push Windows pour un hub de notification Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: c3e3f1e7df5c90c690756375ff1e1b0350c72714
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488248"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurer les paramètres de Service de Notification Push Windows (WNS) pour un concentrateur de notification dans le portail Azure
Cet article vous montre comment configurer les paramètres de Service de Notification de Windows (WNS) pour un hub de notification Azure à l’aide du portail Azure.  

## <a name="prerequisites"></a>Conditions préalables
Si vous n’avez pas déjà créé un hub de notification, créez-en un maintenant. Pour plus d’informations, consultez [créer un hub de notification Azure dans le portail Azure](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Configurer le Service de notifications Push Windows (WNS)

La procédure suivante vous donne les étapes pour configurer les paramètres de Service de Notification Push Windows (WNS) pour un concentrateur de notification : 

1. Dans le portail Azure, sur le **Hub de Notification** page, sélectionnez **Windows (WNS)** sur le menu de gauche.
2. Entrez des valeurs pour **SID de package** et **Clé de sécurité**.
3. Sélectionnez **Enregistrer**.

   ![Capture d’écran montrant les zones SID du Package et la clé de sécurité](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir un didacticiel avec des instructions pas à pas pour envoyer des notifications vers les applications de plateforme Windows universelle à l’aide d’Azure Notification Hubs et Service de Notification Push Windows (WNS), consultez [envoyer des notifications à des applications UWP à l’aide d’Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


