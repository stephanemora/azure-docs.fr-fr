---
title: Présentation d’Azure Notification Hubs
description: Découvrez comment ajouter la fonctionnalité de notifications Push dans Azure Notification Hubs.
author: jwargo
manager: patniko
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
ms.date: 04/30/2019
ms.author: jowargo
ms.openlocfilehash: 03d4c269f76a89c43dec253367d07f3bf71a06d8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141207"
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

Les notifications Push sont une forme de communication entre l’application et l’utilisateur dans laquelle les utilisateurs d’applications mobiles sont avertis de certaines informations choisies, le plus souvent via une fenêtre contextuelle ou une boîte de dialogue sur un appareil mobile. Les utilisateurs choisissent généralement d’afficher ou d’ignorer le message ; s’ils choisissent de l’afficher, l’application mobile qui a transmis la notification apparaît. Certaines notifications silencieuses sont remises en arrière-plan puis traitées également en arrière-plan par l’application qui décidera de l’action à exécuter.

Les notifications Push sont essentielles pour les applications clients en vue d’augmenter l’engagement envers l’application et l’utilisation. Pour les applications d’entreprise, elles permettent de communiquer des informations commerciales à jour. Il s’agit de la meilleure méthode de communication entre l’application et l’utilisateur, car elle consomme peu d’énergie pour les appareils mobiles, elle est flexible pour les expéditeurs de notifications et disponible alors que les applications correspondantes ne sont pas actives.

Pour plus d’informations sur les notifications Push des plateformes les plus populaires, consultez les rubriques suivantes :

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](https://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Fonctionnement des notifications Push

Les notifications Push sont diffusées par l’intermédiaire d’infrastructures spécifiques à des plateformes appelées *Platform Notification Systems* (PNS). Elles proposent des fonctionnalités Push pour remettre un message à un appareil avec un handle fourni et sans interface commune. Pour envoyer une notification à tous les clients pour les versions iOS, Android et Windows d’une application, le développeur doit utiliser Apple Push Notification Service (APNS), Firebase Cloud Messaging (FCM) et le service de notification Windows (WNS), séparément.

À un niveau élevé, voici comment fonctionnent les notifications Push :

1. Une application décide qu’elle souhaite recevoir une notification. Elle contacte donc le PNS pour connaître la plateforme cible sur laquelle l’application est en cours d’exécution, puis demande un handle push unique et temporaire. Le type de handle dépend du système (par exemple, WNS dispose d’un URI, tandis qu’APNS utilise des jetons).
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
  - Selon les instructions de PNS, les jetons des appareils doivent être actualisés chaque fois que l’application est lancée. Le serveur principal traite une grande quantité de trafic et d’accès à la base de données simplement pour maintenir les jetons à jour. Lorsque le nombre d’appareils s’accroît pour atteindre des centaines, des milliers voire des millions, le coût de création et de maintenance de cette infrastructure est conséquent.
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
  - Diffuser vers une ou plusieurs plateformes : Vous pouvez diffuser instantanément des contenus vers des millions d’appareils sur plusieurs plateformes avec un seul appel d’API.
  - Notification Push à un appareil : Vous pouvez cibler des notifications sur des appareils individuels.
  - Notification Push à un utilisateur : Les fonctionnalités des étiquettes et des modèles vous aident à atteindre tous les appareils multiplateformes d’un utilisateur.
  - Notification Push à un segment avec des étiquettes dynamiques : La fonctionnalité des étiquettes vous permet de segmenter les appareils et de leur envoyer des notifications selon vos besoins, vers un seul segment ou une expression de segments (par exemple, actif ET réside à Seattle PAS nouvel utilisateur). Au lieu d’être limité au mécanisme publish-subscribe, vous pouvez mettre à jour les balises des appareils n’importe où et à tout moment.
  - Notification Push localisé : la fonctionnalité des modèles permet d’effectuer une localisation sans affecter le code back-end.
  - Notification Push en mode silencieux : vous pouvez activer le modèle envoi-extraction en envoyant des notifications en mode silencieux aux appareils et en les déclenchant pour effectuer certaines extractions ou actions.
  - Notification Push planifiée : Vous pouvez planifier l’envoi de notifications à tout moment.
  - Notification Push directe : Vous pouvez ignorer l’inscription des appareils auprès du service Notification Hubs et envoyer directement des notifications Push par lot à une liste de handles d’appareils.
  - Notification Push personnalisée : Les variables des notifications Push des appareils vous permettent d’envoyer des notifications Push personnalisées spécifiques à un appareil avec des paires clé-valeur personnalisées.
- **Télémétrie enrichie**
  - La télémétrie de notification Push générale, d’appareil, d’erreur et d’opération est disponible dans le portail Azure et par programme.
  - La télémétrie par message effectue le suivi de chaque transmission de données à partir de votre appel de demande initial au service Notification Hubs qui transmet les notifications Push par lot avec succès.
  - Platform Notification System Feedback communique tous les commentaires des systèmes de notification de plateforme pour faciliter le débogage.
- **Extensibilité**
  - Envoyer des messages rapides à des millions d’appareils sans réorganisation ou partitionnement d’appareil.
- **Sécurité**
  - Secret d’accès partagé ou authentification fédérée.

## <a name="next-steps"></a>Étapes suivantes

Commencez à créer et à utiliser un hub de notification en suivant le [Tutoriel : Notifications Push vers des applications mobiles](notification-hubs-android-push-notification-google-fcm-get-started.md).

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png

[How customers are using Notification Hubs]: https://azure.microsoft.com/services/notification-hubs
[Notification Hubs tutorials and guides]: https://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: https://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[Windows Universal]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[Kindle]: https://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[Xamarin.iOS]: https://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[Xamarin.Android]: https://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: https://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: https://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[App Service Mobile Apps]: https://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop/
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure portal]: https://portal.azure.com
[tags]: (https://msdn.microsoft.com/library/azure/dn530749.aspx)
