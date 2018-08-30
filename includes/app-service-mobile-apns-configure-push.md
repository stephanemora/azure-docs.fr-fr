---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 25bb5cfb0073f7533faddec43b38fb5031b82a43
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42809643"
---
1. Sur votre Mac, démarrez **Trousseau d’accès**. Dans la barre de navigation gauche, sous **Catégorie**, ouvrez **Mes certificats**. Recherchez le certificat SSL que vous avez téléchargé à la section précédente, puis affichez son contenu. Sélectionnez uniquement le certificat (sans la clé privée). Puis [exportez-le](https://support.apple.com/kb/PH20122?locale=en_US).
2. Dans le [portail Azure](https://portal.azure.com/), sélectionnez **Parcourir tout** > **App Services**. Puis sélectionnez votre serveur principal Mobile Apps. 
3. Sous **Paramètres**, sélectionnez **App Service Push** (Notification Push App Service). Ensuite, sélectionnez le nom de votre Hub de notification. 
4. Accédez à **Services de notifications Push Apple** > **Télécharger le certificat**. Chargez le fichier .p12 en sélectionnant le **mode** approprié (en fonction du type de votre certificat SSL client précédemment utilisé : Production ou Sandbox). Enregistrez les modifications.

Votre service est désormais configuré et prêt à fonctionner avec les notifications Push sur iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
