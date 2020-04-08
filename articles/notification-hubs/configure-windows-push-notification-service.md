---
title: Configurer le service de notifications Push Windows dans Azure Notification Hubs | Microsoft Docs
description: Découvrez comment configurer les paramètres du service de notifications Push Windows pour un hub de notifications Azure.
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
ms.openlocfilehash: 73304e191242725c80204efb132c26aede9ce7e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127324"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Configurer les paramètres du service de notifications Push Windows dans le portail Azure

Cet article explique comment configurer les paramètres du service de notifications Push Windows pour un hub de notifications Azure à l’aide du portail Azure.  

## <a name="prerequisites"></a>Prérequis
Si vous n’avez pas déjà créé de hub de notification, créez-en un maintenant. Pour plus d’informations, consultez [Créer un hub de notification Azure dans le portail Azure](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Configurer les services de notifications Push Windows (WNS)

La procédure qui suit vous guide dans la configuration des paramètres du service de notifications Push Windows pour un hub de notifications : 

1. Dans la page **Hub de notification** du portail Azure, sélectionnez **Windows (WNS)** dans le menu de gauche.
2. Entrez des valeurs pour **SID de package** et **Clé de sécurité**.
3. Sélectionnez **Enregistrer**.

   ![Capture d’écran montrant les zones SID du Package et Clé de sécurité](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Étapes suivantes
Pour consulter un didacticiel avec des instructions pas à pas pour envoyer des notifications vers la plateforme Windows universelle en utilisant Microsoft Azure Notification Hubs et le service de notifications Push Windows, consultez [Envoyer des notifications vers des applications UWP avec Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


