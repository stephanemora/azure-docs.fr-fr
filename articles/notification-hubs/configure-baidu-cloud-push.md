---
title: Configurer Baidu Cloud Push dans Azure Notification Hubs | Microsoft Docs
description: Apprenez à configurer les paramètres de Baidu pour un hub de notification Azure.
services: notification-hubs
author: jwargo
manager: patniko
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9feb5f66c5ae979a239c3349b2b1347ad307ce49
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884647"
---
# <a name="deprecated-configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Déconseillé : Configurer les paramètres de Baidu Cloud Push pour un hub de notification sur le portail Azure

Cet article explique comment configurer les paramètres de Baidu Cloud Push pour un hub de notification Azure à l'aide du portail Azure.

> [!IMPORTANT]
> Ce didacticiel est déconseillé. 

## <a name="prerequisites"></a>Prérequis
Si vous n’avez pas déjà créé de hub de notification, créez-en un maintenant. Pour plus d’informations, consultez [Créer un hub de notification Azure dans le portail Azure](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Configurer Baidu Cloud Push
La procédure suivante décrit les étapes à suivre afin de configurer les paramètres de Baidu Cloud Push pour un hub de notification :

1. Sur la page **Hub de notification** du portail Azure, sélectionnez **Baidu (Android China)** dans le menu de gauche. 
2. Entrez la **Clé API** que vous avez obtenue à partir de la console Baidu du projet Push cloud Baidu. 
3. Entrez la **Clé secrète** que vous avez obtenue à partir de la console Baidu du projet Push cloud Baidu. 
4. Sélectionnez **Enregistrer**. 

    ![Capture d’écran de Notification Hubs montrant la configuration de Baidu (Android China) pour les notifications Push](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Étapes suivantes
Pour accéder à un tutoriel contenant des instructions détaillées sur l'envoi de notifications à Baidu à l'aide d'Azure Notification Hubs et de Baidu Cloud Push, consultez [Prise en main de Notification Hubs via Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
