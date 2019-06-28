---
title: Configurer Google Firebase Cloud Messaging dans Azure Notification Hubs | Microsoft Docs
description: Découvrez comment configurer un Azure Notification Hub avec des paramètres Google Firebase Cloud Messaging.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60239199"
---
# <a name="configure-google-firebase-cloud-messaging-fcm-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurer les paramètres de Google Firebase Cloud Messaging (FCM) pour un hub de notification sur le portail Azure
Cet article explique comment configurer les paramètres Google Firebase Cloud Messaging (FCM) pour un hub de notification Azure à l'aide du portail Azure.  

## <a name="prerequisites"></a>Prérequis
Si vous n’avez pas déjà créé de hub de notification, créez-en un maintenant. Pour plus d’informations, consultez [Créer un hub de notification Azure dans le portail Azure](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Configurer Google Firebase Cloud Messaging (FCM)

La procédure suivante décrit les étapes à suivre afin de configurer les paramètres de Google Firebase Cloud Messaging (FCM) pour un hub de notification : 

1. Dans la page **Hub de notification** du portail Azure, sélectionnez **Google (GCM/FCM)** dans le menu de gauche. 
2. Collez la **Clé API** pour le projet FCM que vous avez enregistré précédemment. 
3. Sélectionnez **Enregistrer**. 

   ![Capture d’écran montrant comment configurer Notification Hubs pour Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Étapes suivantes
Pour accéder à un tutoriel contenant des instructions détaillées sur l'envoi de notifications à des appareils Android à l'aide d'Azure Notification Hubs et de Google Firebase Cloud Messaging, consultez [Notifications Push vers des appareils Android avec Notification Hubs et Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

