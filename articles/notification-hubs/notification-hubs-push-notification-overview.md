---
title: Présentation d’Azure Notification Hubs
description: Découvrez comment ajouter la fonctionnalité de notifications Push dans Azure Notification Hubs.
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 44086bc20966d9c01ff27dda68f837101c71a778
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-azure-notification-hubs"></a>Présentation d’Azure Notification Hubs
Azure Notification Hubs fournit un moteur d’envoi de notifications Push facile à utiliser et à grande échelle qui vous permet d’envoyer des notifications à n’importe quelle plateforme (iOS, Android, Windows, Kindle, Baidu, etc.) à partir de n’importe quel serveur principal (cloud ou local). Notification Hubs est parfaitement adapté lors de scénarios d’entreprise et de clients. Voici quelques exemples de scénarios :

- envoyer des notifications de dernières nouvelles à des millions de personnes avec une faible latence ;
- envoyer des coupons basés sur la localisation aux segments d’utilisateurs intéressés ;
- envoyer des notifications d’événements à des utilisateurs ou des groupes pour des applications de médias/sport/finance/jeux ;
- envoyer des notifications Push de contenu promotionnel vers les applications pour engager et toucher les clients ;
- informer les utilisateurs d’événements d’entreprise tels que des nouveaux messages et des éléments de travail ;
- envoyer des codes pour l’authentification MFA.

## <a name="what-are-push-notifications"></a>Présentation des notifications Push
Les notifications Push sont une forme de communication entre l’application et l’utilisateur dans laquelle les utilisateurs d’applications mobiles sont avertis de certaines informations choisies, le plus souvent via une fenêtre contextuelle ou une boîte de dialogue. Les utilisateurs peuvent généralement choisir d’afficher ou ignorer le message. La première option ouvre l’application mobile qui a transmis la notification.

Les notifications Push sont essentielles pour les applications clients en vue d’augmenter l’engagement envers l’application et l’utilisation. Pour les applications d’entreprise, elles permettent de communiquer des informations commerciales à jour. Il s’agit de la meilleure méthode de communication entre l’application et l’utilisateur, car elle consomme peu d’énergie pour les appareils mobiles, elle est flexible pour les expéditeurs de notifications et disponible alors que les applications correspondantes ne sont pas actives.

Pour plus d’informations sur les notifications Push des plateformes les plus populaires, consultez les rubriques suivantes : 
* [iOS](https://developer.apple.com/notifications/)
* [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
* [Windows](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Fonctionnement des notifications Push
Les notifications Push sont diffusées par l’intermédiaire d’infrastructures spécifiques à des plateformes appelées *Platform Notification Systems* (PNS). Elles proposent des fonctionnalités Push pour remettre un message à un appareil avec un handle fourni et sans interface commune. Pour envoyer une notification à tous les clients sur les versions iOS, Android et Windows d’une application, le développeur doit manipuler Apple Push Notification Service (APNS), Firebase Cloud Messaging (FCM) et le service de notification Windows (WNS).

À un niveau élevé, voici comment fonctionnent les notifications Push :

1. L’application cliente décide qu’elle souhaite recevoir une notification. Par conséquent, elle contacte le PNS correspondant pour récupérer son handle d’envoi (push) unique et temporaire. Le type de handle dépend du système (par exemple, WNS dispose d’un URI, tandis qu’APNS utilise des jetons).
2. L’application cliente stocke ce handle dans le serveur principal de l’application ou le fournisseur.
3. Pour envoyer une notification Push, le serveur principal de l’application contacte le PNS à l’aide du handle afin de cibler une application cliente spécifique.
4. Le PNS transmet la notification à l’appareil spécifié par le handle.

![Flux de travail de la notification Push](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Défis des notifications Push
Les PNS sont des outils puissants. Mais il reste beaucoup de travail au développeur d’applications pour implémenter ne serait-ce que des scénarios de notification Push courants, tels que la diffusion de notifications Push à un utilisateur.

L’envoi (Push) de notifications nécessite une infrastructure complexe qui n’est pas liée à la logique métier principale de l’application. Voici quelques défis liés à l’infrastructure :

- **Dépendance de la plateforme**
    - Le serveur principal doit disposer d’une logique liée à la plateforme complexe et difficile à gérer pour envoyer des notifications aux appareils sur différentes plateformes étant donné que les PNS ne sont pas unifiés.
- **Mettre à l'échelle**
    - Selon les instructions de PNS, les jetons des appareils doivent être actualisés chaque fois que l’application est lancée. Le serveur principal traite une grande quantité de trafic et d’accès à la base de données simplement pour maintenir les jetons à jour. Lorsque le nombre d’appareils s’accroît pour atteindre des milliers, voire des millions, le coût de création et de maintenance de cette infrastructure est conséquent.
    - La plupart des PNS ne prennent pas en charge la diffusion sur plusieurs appareils. Une simple diffusion sur un million d’appareils entraîne un million d’appels vers les PNS. La mise à l’échelle de cette quantité de trafic avec une latence minimale est complexe.
- **Routage** 
    - Bien que les PNS fournissent un moyen d’envoyer des messages aux appareils, la plupart des notifications d’applications sont ciblées sur les utilisateurs ou les groupes d’intérêt. Le serveur principal doit conserver un registre pour associer des appareils aux groupes d’intérêt, aux utilisateurs, aux propriétés, etc. Ce traitement s’ajoute à la durée totale de mise sur le marché et aux coûts de maintenance d’une application.

## <a name="why-use-azure-notification-hubs"></a>Pourquoi utiliser Azure Notification Hubs ?
Notification Hubs élimine toutes les difficultés liées à l’envoi de vos notifications Push à partir de votre application principale. Son infrastructure de notifications Push multi-plateforme et mise à l’échelle réduit le codage Push et simplifie votre serveur principal. Avec Notification Hubs, la seule responsabilité des appareils est d’enregistrer leur handle PNS auprès d’un hub, tandis que le serveur principal est responsable de l’envoi des messages aux utilisateurs et groupes d’intérêt, comme illustré dans la figure suivante :

![Diagramme Notification Hubs](./media/notification-hubs-overview/notification-hub-diagram.png)

Notification Hubs est votre moteur Push prêt à l’emploi et présente les avantages suivants :

- **Multi-plateforme**
    - Prise en charge de toutes les plateformes Push principales, dont iOS, Android, Windows, Kindle et Baidu.
    - Une interface commune pour envoyer des notifications Push à toutes les plateformes dans des formats spécifiques ou indépendants de la plateforme, sans aucune tâche propre à la plateforme.
    - Gestion centralisée du handle de l’appareil.
- **Entre les serveurs principaux**
    - Cloud ou local
    - .NET, Node.js, Java, etc.
- **Ensemble complet de modèles de remise**
    - Envoyé à une ou plusieurs plateformes : vous pouvez instantanément diffuser des contenus vers des millions d’appareils sur plusieurs plateformes en un seul appel d’API.
    - Transmission Push à un appareil : vous pouvez cibler des notifications sur des appareils individuels.
    - Transmission Push à l’utilisateur : les fonctionnalités de balises et de modèles vous aident à communiquer sur tous les appareils multi-plateformes d’un utilisateur.
    - Transmission Push au segment avec des balises dynamiques : la fonctionnalité de balises vous permet de segmenter les appareils et de leur transmettre des notifications Push selon vos besoins, que vous effectuiez un envoi vers un segment ou une expression de segments (par exemple, actif ET réside à Seattle PAS nouvel utilisateur). Au lieu d’être limité au mécanisme publish-subscribe, vous pouvez mettre à jour les balises des appareils n’importe où et à tout moment.
    - Notification Push localisée : la fonctionnalité de modèles permet d’obtenir la localisation sans affecter le code principal.
    - Notification Push en mode silencieux : vous pouvez activer le modèle de transmission de type push et pull en envoyant des notifications en mode silencieux aux appareils et en les déclenchant pour effectuer certaines extractions ou actions.
    - Notification Push planifiée : vous pouvez planifier l’envoi de notifications à tout moment.
    - Notification Push directe : vous pouvez ignorer l’enregistrement des appareils auprès du service Notification Hubs et envoyer directement des notifications Push par lot à une liste de handles d’appareils.
    - Notification Push personnalisée : les variables des notifications Push des appareils vous permettent d’envoyer des notifications Push personnalisées propres à l’appareil avec des paires clé-valeur personnalisées.
- **Télémétrie enrichie**
    - La télémétrie de notification Push générale, d’appareil, d’erreur et d’opération est disponible dans le portail Azure et par programme.
    - La télémétrie par message effectue le suivi de chaque transmission de données à partir de votre appel de demande initial au service Notification Hubs qui transmet les notifications Push par lot avec succès.
    - Platform Notification System Feedback communique tous les commentaires des systèmes de notification de plateforme pour faciliter le débogage.
- **Extensibilité** 
    - Envoyer des messages rapides à des millions d’appareils sans réorganisation ou partitionnement d’appareil.
- **Sécurité**
    - Secret d’accès partagé ou authentification fédérée.

## <a name="integration-with-app-service-mobile-apps"></a>Intégration d’App Service Mobile Apps
Pour offrir une expérience transparente et unifiée dans l’ensemble des services Azure, [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) intègre la prise en charge des notifications Push à l’aide de Notification Hubs. [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) offre une plateforme de développement d’applications mobiles hautement évolutive pour les développeurs d’entreprise et les intégrateurs système. Disponible pour tous, elle fournit un ensemble complet de fonctionnalités pour les développeurs d’applications mobiles.

Les développeurs Mobile Apps peuvent utiliser Notification Hubs avec le flux de travail suivant :

1. Récupération du handle PNS de l’appareil
2. Enregistrez l’appareil avec Notification Hubs grâce à l’API d’enregistrement de Kit de développement logiciel (SDK) client Mobile Apps adapté.

    > [!NOTE]
    > Notez que Mobile Apps supprime tous les mots clés des inscriptions pour des raisons de sécurité. Utilisez Notification Hubs directement depuis votre serveur principal pour associer des mots clés aux appareils.
1. Envoyez des notifications de votre serveur principal d’application avec Notification Hubs

Voici certains des avantages qu’offre cette intégration aux développeurs :

- **Kits de développement logiciel (SDK) clients Mobile Apps** : ces Kits de développement logiciel (SDK) multi-plateformes fournissent des API simples pour l’inscription et la communication avec le hub de notification lié à l’application mobile de manière automatique. Les développeurs n’ont ni à rechercher les informations d’identification de Notification Hubs ni à utiliser de service supplémentaire.
    - *Transmission de notifications Push à l’utilisateur* : les Kits de développement logiciel (SDK) balisent automatiquement l’appareil avec l’identifiant utilisateur authentifié auprès de Mobile Apps pour activer le scénario de transmission de notifications Push à l’utilisateur.
    - *Transmission de notifications Push à l’appareil* : les Kits de développement logiciel (SDK) utilisent automatiquement l’ID d’installation de Mobile Apps en tant que GUID pour l’inscription auprès de Notification Hubs, ce qui évite aux développeurs d’avoir à gérer plusieurs GUID de service.
- **Modèle d’installation** : Mobile Apps fonctionne avec le modèle d’émission le plus récent de Notification Hubs pour représenter l’ensemble des propriétés d’émission associées à un appareil dans une installation JSON alignée avec les services de notifications Push et facile à utiliser.
- **Flexibilité** : les développeurs peuvent toujours choisir d’utiliser Notification Hubs directement, même avec l’intégration effective.
- **Expérience intégrée dans le [portail Azure](https://portal.azure.com)**  : la fonctionnalité Notifications Push est représentée visuellement dans Mobile Apps. Les développeurs peuvent utiliser le hub de notification associé en toute simplicité via Mobile Apps.

## <a name="next-steps"></a>Étapes suivantes
Commencez à créer et à utiliser un concentrateur de notification en suivant le [tutoriel : notifications Push vers des applications mobiles](notification-hubs-android-push-notification-google-fcm-get-started.md). [0]: ./media/notification-hubs-overview/registration-diagram.png [1]: ./media/notification-hubs-overview/notification-hub-diagram.png [Comment les clients utilisent Notification Hubs] : http://azure.microsoft.com/services/notification-hubs [Tutoriels et guides Notification Hubs] : http://azure.microsoft.com/documentation/services/notification-hubs [iOS] : http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started [Android] : http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started [Windows Universal] : http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started [Windows Phone] : http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started [Kindle] : http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started [Xamarin.iOS] : http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started [Xamarin.Android] : http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started [Microsoft.WindowsAzure.Messaging.NotificationHub] : http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx [Microsoft.ServiceBus.Notifications] : http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx [App Service Mobile Apps] : https://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop/ [modèles] : notification-hubs-templates-cross-platform-push-messages.md [Portail Azure] : https://portal.azure.com [balises] : (http://msdn.microsoft.com/library/azure/dn530749.aspx)
