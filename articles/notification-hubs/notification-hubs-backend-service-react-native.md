---
title: Envoyer des notifications Push à des applications React Native à l’aide d’Azure Notification Hubs par le biais d’un service back-end | Microsoft Docs
description: Découvrez comment envoyer des notifications Push à des applications React Native qui utilisent Azure Notification Hubs par le biais d’un service back-end.
author: alexeystrakh
ms.service: notification-hubs
ms.topic: tutorial
ms.date: 06/11/2020
ms.author: alstrakh
ms.openlocfilehash: 007be386b7b64fd3461fa508d35a4ef9be377c1f
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170907"
---
# <a name="tutorial-send-push-notifications-to-react-native-apps-using-azure-notification-hubs-via-a-backend-service"></a>Tutoriel : Envoyer des notifications Push à des applications React Native à l’aide d’Azure Notification Hubs par le biais d’un service back-end  

[![Télécharger l’exemple](./media/notification-hubs-backend-service-react-native/download.png) Télécharger l’exemple](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Flutter](notification-hubs-backend-service-flutter.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

Dans ce tutoriel, vous allez utiliser [Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) pour envoyer des notifications Push à une [application React Native](https://reactnative.dev/) ciblant **Android** et **iOS**.  

[!INCLUDE [Notification Hubs Backend Service Introduction](../../includes/notification-hubs-backend-service-introduction.md)]

Ce didacticiel vous familiarise avec les procédures suivantes :

> [!div class="checklist"]
>
> * [Configurer Push Notification Services et Azure Notification Hubs](#set-up-push-notification-services-and-azure-notification-hub)
> * [Créez une application back-end d’API web ASP.NET Core](#create-an-aspnet-core-web-api-backend-application)
> * [Créer une application React Native multiplateforme](#create-a-cross-platform-react-native-application)
> * [Configurer le projet Android natif pour les notifications Push](#configure-the-native-android-project-for-push-notifications)
> * [Configurer le projet iOS natif pour les notifications Push](#configure-the-native-ios-project-for-push-notifications)
> * [Tester la solution](#test-the-solution)

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous avez besoin des éléments suivants :

* Un [abonnement Azure](https://portal.azure.com) où vous pouvez créer et gérer les ressources.
* Un Mac avec [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) installé (ou un PC exécutant [Visual Studio 2019](https://visualstudio.microsoft.com/vs) avec la charge de travail **Développement mobile en .NET**)
* La capacité à exécuter l’application sur **Android** (appareils physiques ou émulateurs) ou **iOS** (appareils physiques uniquement)

Pour Android, vous devez disposer des éléments suivants :

* Un appareil physique de développeur déverrouillé ou un émulateur  *(exécutant l’API 26 et ultérieur avec Google Play Services installé)*

Pour iOS, vous devez disposer des éléments suivants :

* Un [compte de développeur Apple](https://developer.apple.com) actif.
* Un appareil iOS physique [inscrit auprès de votre compte de développeur](https://help.apple.com/developer-account/#/dev40df0d9fa) *(exécutant iOS 13.0 et ultérieur)*
* Un [certificat de développement](https://help.apple.com/developer-account/#/dev04fd06d56) **.p12** installé dans votre **trousseau** vous permettant d’[exécuter une application sur un appareil physique](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca)

> [!NOTE]
> Le simulateur iOS ne prend pas en charge les notifications distantes. Un appareil physique est donc nécessaire lors de l’exploration de cet exemple sur iOS. Toutefois, vous n’avez pas besoin d’exécuter l’application à la fois sur **Android** et sur **iOS** pour suivre ce tutoriel.

Vous pouvez effectuer les étapes de cet exemple sans expérience préalable. Toutefois, une connaissance des éléments suivants vous sera utile :

* [Portail des développeurs Azure](https://developer.apple.com)
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Azure Notification Hubs](notification-hubs-push-notification-overview.md)
* [Console Google Firebase](https://console.firebase.google.com/u/0/)
* [React Native](https://reactnative.dev/docs/getting-started)

Les étapes fournies concernent [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) et [Visual Studio Code](https://code.visualstudio.com/download). Toutefois, il est possible d’utiliser [Visual Studio 2019](https://visualstudio.microsoft.com/vs).

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Configurer Push Notification Services et Azure Notification Hubs

Dans cette section, vous allez configurer **[Firebase Cloud Messaging (FCM)](https://firebase.google.com/docs/cloud-messaging)** et **[Apple Push Notification Services (APNS)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)** . Ensuite, vous allez créer et configurer un hub de notification pour travailler avec ces services.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](../../includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](../../includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](../../includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](../../includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>Créer une application back-end d’API web ASP.NET Core

Dans cette section, vous allez créer le back-end d’[API web ASP.NET Core](https://dotnet.microsoft.com/apps/aspnet/apis) pour gérer l’[inscription d’appareil](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) et l’envoi de notifications à l’application mobile React Native.

[!INCLUDE [Create an ASP.NET Core Web API backend application](../../includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-react-native-application"></a>Créer une application React Native multiplateforme

Dans cette section, vous allez créer une application mobile [React Native](https://reactnative.dev/) qui implémente des notifications Push en mode multiplateforme.

[!INCLUDE [Sample application generic overview](../../includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create React Native application](../../includes/notification-hubs-backend-service-sample-app-reactnative.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>Configurer le projet Android natif pour les notifications Push

[!INCLUDE [Configure the native Android project](../../includes/notification-hubs-backend-service-configure-reactnative-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>Configurer le projet iOS natif pour les notifications Push

[!INCLUDE [Configure the native iOS project](../../includes/notification-hubs-backend-service-configure-reactnative-ios.md)]

## <a name="test-the-solution"></a>Test de la solution

Vous pouvez maintenant tester l’envoi de notifications par le biais du service back-end.

[!INCLUDE [Testing the solution](../../includes/notification-hubs-backend-service-testing.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous devez maintenant disposer d’une application React Native de base connectée à un hub de notification par le biais d’un service back-end, et qui peut envoyer et recevoir des notifications.

[!INCLUDE [Next steps](../../includes/notification-hubs-backend-service-next-steps.md)]

## <a name="troubleshooting"></a>Dépannage

[!INCLUDE [Troubleshooting](../../includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>Liens connexes

* [Vue d’ensemble d’Azure Notification Hubs](notification-hubs-push-notification-overview.md)
* [Installation de Visual Studio pour Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* [Installation de Visual Studio Code](https://code.visualstudio.com/download)
* [Configuration de l’environnement de développement React Native](https://reactnative.dev/docs/environment-setup)
* [Kit de développement logiciel (SDK) Notification Hubs pour opérations backend](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [Kit de développement logiciel (SDK) Notification Hubs sur GitHub](https://github.com/Azure/azure-notificationhubs)
* [S’inscrire auprès d’un back-end d’application](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [Gestion des inscriptions](notification-hubs-push-notification-registration-management.md)
* [Utilisation de balises](notification-hubs-tags-segment-push-message.md)
* [Utilisation des modèles personnalisés](notification-hubs-templates-cross-platform-push-messages.md)
