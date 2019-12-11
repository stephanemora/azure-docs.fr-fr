---
title: Ajouter des notifications Push à iOS
description: Découvrez comment utiliser Azure Mobile Apps pour envoyer des notifications Push à votre application iOS.
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 2864dfca72c91acd10fd6a4ee8038040ba5defc8
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668818"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Ajout de notifications Push à votre application iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center prend en charge les services intégrés essentiels au développement d’applications mobiles. Les développeurs peuvent utiliser les services **Build**, **Test** et **Distribute** pour configurer le pipeline de livraison et d’intégration continues. Une fois l’application déployée, les développeurs peuvent superviser l’état et l’utilisation de leur application à l’aide des services **Analytics** et **Diagnostics**, puis interagir avec les utilisateurs à l’aide du service **Push**. Les développeurs peuvent aussi utiliser **Auth** pour authentifier leurs utilisateurs ainsi que le service **Data** pour conserver et synchroniser les données d’application dans le cloud.
>
> Si vous souhaitez intégrer des services cloud à votre application mobile, inscrivez-vous à [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) dès aujourd’hui.

## <a name="overview"></a>Vue d'ensemble

Dans ce didacticiel, vous ajoutez des notifications Push au projet [Démarrage rapide d’iOS] pour qu’une notification Push soit envoyée chaque fois qu’un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter le package d’extension de notification Push. Pour plus d’informations, consultez le guide [Utiliser le kit SDK du serveur backend .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Les [notifications Push ne sont pas prises en charge par le simulateur iOS](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Vous avez besoin d’un appareil iOS physique et d’une [appartenance au programme pour développeurs Apple](https://developer.apple.com/programs/ios/).

## <a name="configure-hub"></a>Configurer un hub de notification

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Inscrire une application pour les notifications Push

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurer Azure pour l’envoi de notifications Push

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>Mettre à jour le serveur principal pour l'envoi de notifications push

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Ajouter des notifications Push à l’application

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Tester les notifications Push

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>En savoir plus

* Les modèles vous apportent la souplesse nécessaire pour envoyer des notifications push multiplateformes et localisées. [Utilisation de la bibliothèque cliente iOS pour Azure Mobile Apps](app-service-mobile-ios-how-to-use-client-library.md#templates) vous montre comment enregistrer des modèles.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[Démarrage rapide d’iOS]: app-service-mobile-ios-get-started.md
