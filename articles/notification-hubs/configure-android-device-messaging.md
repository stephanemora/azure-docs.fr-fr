---
title: Configurer la messagerie de l’appareil Android dans Azure Notification Hubs | Microsoft Docs
description: Découvrez comment configurer les paramètres de messagerie d’appareil Android (ADM) pour un hub de notification Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 0d79e53f731c20777ffa2fb57b3b8149fe4e60ab
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488231"
---
# <a name="configure-android-device-messaging-adm-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurer les paramètres de messagerie d’appareil Android (ADM) pour un concentrateur de notification dans le portail Azure
Cet article vous montre comment configurer les paramètres de messagerie d’appareil Android (ADM) pour un hub de notification Azure à l’aide du portail Azure. 

## <a name="prerequisites"></a>Conditions préalables
Si vous n’avez pas déjà créé un hub de notification, créez-en un maintenant. Pour plus d’informations, consultez [créer un hub de notification Azure dans le portail Azure](create-notification-hub-portal.md). 

## <a name="configure-android-device-messaging"></a>Configurer la messagerie de l’appareil Android

La procédure suivante vous donne les étapes pour configurer les paramètres d’Amazon Device Messaging (ADM) pour un concentrateur de notification : 

1. Dans le portail Azure, sur le **Hub de Notification** page, sélectionnez **Amazon (ADM)** sur le menu de gauche.
2. Entrez les valeurs **ID Client** et **clé secrète Client**.
3. Sélectionnez **Enregistrer**.
    
   ![Paramètres de capture d’écran de ADM dans le portail Azure](./media/notification-hubs-kindle-get-started/notification-hub-adm-settings.png)

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir un didacticiel avec des instructions pas à pas pour envoyer des notifications aux appareils Android à l’aide d’Azure Notification Hubs et Android Device Messaging (ADM), consultez [prise en main Notification Hubs pour les applications Kindle](notification-hubs-kindle-amazon-adm-push-notification.md).

