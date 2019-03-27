---
title: Configurer Baidu Cloud Push dans Azure Notification Hubs | Microsoft Docs
description: Découvrez comment configurer les paramètres de Baidu pour un hub de notification Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9942b1d4d8b5d538f5150a36e596753282039be7
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488271"
---
# <a name="configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurer les paramètres de Baidu Cloud Push pour un concentrateur de notification dans le portail Azure
Cet article vous montre comment configurer les paramètres de Baidu Cloud Push pour un hub de notification Azure à l’aide du portail Azure. 

## <a name="prerequisites"></a>Conditions préalables
Si vous n’avez pas déjà créé un hub de notification, créez-en un maintenant. Pour plus d’informations, consultez [créer un hub de notification Azure dans le portail Azure](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Configurer Baidu Cloud Push
La procédure suivante vous donne les étapes pour configurer les paramètres de Baidu Cloud Push pour un concentrateur de notification :

1. Dans le portail Azure, sur le **Hub de Notification** page, sélectionnez **Baidu (Android China)** sur le menu de gauche. 
2. Entrez le **clé Api** que vous avez obtenue à partir de la console Baidu dans le projet de push cloud Baidu. 
3. Entrez le **clé secrète** que vous avez obtenue à partir de la console Baidu dans le projet de push cloud Baidu. 
4. Sélectionnez **Enregistrer**. 

    ![Capture d’écran de Notification Hubs, qui illustre la configuration de Baidu (Android China) pour les notifications push](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir un didacticiel avec des instructions pas à pas pour envoyer des notifications vers Baidu à l’aide d’Azure Notification Hubs et Baidu Cloud Push, consultez [prise en main Notification Hubs à l’aide de Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
