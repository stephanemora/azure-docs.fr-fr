---
title: Configurer Google Firebase Cloud Messaging dans Azure Notification Hubs | Microsoft Docs
description: Découvrez comment configurer un hub de notification Azure avec les paramètres Google Firebase Cloud Messaging.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: d200f5746d5f7c62ff3a52873d5d5d6c3e1322dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60239199"
---
# <a name="configure-google-firebase-cloud-messaging-fcm-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurer les paramètres de Google Firebase Cloud Messaging (FCM) pour un concentrateur de notification dans le portail Azure
Cet article vous montre comment configurer les paramètres de Google Firebase Cloud Messaging (FCM) pour un hub de notification Azure à l’aide du portail Azure.  

## <a name="prerequisites"></a>Conditions préalables
Si vous n’avez pas déjà créé de hub de notification, créez-en un maintenant. Pour plus d’informations, consultez [Créer un hub de notification Azure dans le portail Azure](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Configurer Google Firebase Cloud Messaging (FCM)

La procédure suivante vous donne les étapes pour configurer les paramètres Google Firebase Cloud Messaging (FCM) pour un concentrateur de notification : 

1. Dans le portail Azure, sur le **Hub de Notification** page, sélectionnez **Google (GCM/FCM)** sur le menu de gauche. 
2. Collez la **Clé API** pour le projet FCM que vous avez enregistré précédemment. 
3. Sélectionnez **Enregistrer**. 

   ![Capture d’écran montrant comment configurer Notification Hubs pour Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir un didacticiel avec des instructions pas à pas pour envoyer des notifications aux appareils Android à l’aide d’Azure Notification Hubs et Google Firebase Cloud Messaging, consultez [des notifications Push aux appareils Android à l’aide de Notification Hubs et Google FCM ](notification-hubs-android-push-notification-google-fcm-get-started.md).

