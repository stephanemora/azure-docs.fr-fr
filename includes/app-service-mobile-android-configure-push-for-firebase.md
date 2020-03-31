---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 51a75ee7bf87c38e3916bdbc8d85abcfb14dca8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67177568"
---
1. Dans le [Portail Azure](https://portal.azure.com/), cliquez sur **Parcourir tout** > **App Services**, puis cliquez sur votre serveur principal Mobile Apps. Sous **Paramètres**, cliquez sur **App Service Push (Notification Push App Service)** , puis sur le nom de votre hub de notification.
2. Accédez à **Google (GCM)** , entrez la valeur de **clé serveur** obtenue auprès de Firebase au cours de la procédure précédente, puis cliquez sur **Enregistrer**.

    ![Définir la clé API dans le portail](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

Le serveur principal Mobile Apps est désormais configuré pour utiliser Firebase Cloud Messaging. Ceci vous permet d’envoyer des notifications Push à votre application en cours d’exécution sur un appareil Android à l’aide du hub de notification.
