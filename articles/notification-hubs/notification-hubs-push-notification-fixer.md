---
title: Diagnostic d’Azure Notification Hubs - notifications ignorées
description: Découvrez comment diagnostiquer les problèmes courants liés à la perte des notifications dans Azure Notification Hubs.
services: notification-hubs
documentationcenter: Mobile
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: c0fd7dec31a2c4054c59db3bae52cdb15ba01eed
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884419"
---
# <a name="azure-notification-hubs---diagnose-dropped-notifications"></a>Diagnostic d’Azure Notification Hubs - notifications ignorées

L’une des questions les plus couramment posées par les clients d’Azure Notification Hubs concerne les notifications qui sont envoyées à partir d’une application et qui n’apparaissent pas sur leurs appareils. Ils souhaitent savoir où se trouvent ces notifications, pourquoi elles se sont perdues et comment résoudre ce problème. Cet article explique les raisons pour lesquelles les notifications peuvent être perdues ou ne pas apparaître sur les appareils. Découvrez comment analyser le problème et déterminer la cause racine.

En premier lieu, il est essentiel de comprendre la manière dont le service Notification Hubs envoie (push) les notifications vers un appareil.

![Architecture de Notification Hubs][0]

Dans un flux de notification d’envoi type, le message est envoyé par le *serveur d’applications backend* à Notification Hubs. Notification Hubs procède à un traitement de toutes les inscriptions. Le traitement prend en compte les balises configurées et les expressions de balise pour déterminer les cibles. Les cibles correspondent à tous les enregistrements qui doivent recevoir la notification Push. Ces inscriptions peuvent inclure toutes les plateformes prises en charge : iOS, Google, Windows, Windows Phone, Kindle et Baidu pour Android en Chine.

Une fois les cibles établies, le service Notification Hubs envoie (push) des notifications au *service de notifications Push* correspondant à la plateforme de l’appareil. Il peut s’agir, par exemple, d’Apple Push Notification Service (APNs) pour Apple ou de Firebase Cloud Messaging (FCM) pour Google. Notification Hubs envoie les notifications regroupées en plusieurs lots d’inscriptions. Notification Hubs s’authentifie auprès du service de notifications Push correspondant, en fonction des informations d’identification que vous avez configurées dans le portail Azure, sous **Configure Notification Hub** (Configurer Notification Hubs). Le service de notifications Push transmet alors les notifications aux *appareils clients* correspondants.

Le dernier tronçon de remise des notifications s’effectue entre le service de notifications Push de la plateforme et l’appareil. La perte des notifications peut être causée par l’un des quatre composants principaux du processus de notification Push, à savoir le client, le serveur d’applications backend, Notification Hubs et le service de notifications Push de la plateforme. Pour plus d’informations sur l’architecture de Notification Hubs, consultez [Vue d’ensemble de Notification Hubs].

L’échec de la remise des notifications peut se produire lors de la phase de test ou de préproduction. À ce stade, la perte des notifications peut indiquer un problème de configuration. Si l’échec de remise des notifications se produit lors de la phase de production, il se peut que certaines ou que l’ensemble des notifications soient perdues. Dans ce cas, cela indique un problème plus complexe lié à l’application ou au type de messagerie.

La section suivante présente les scénarios dans lesquels les notifications peuvent être perdues, des plus courants aux plus rares.

## <a name="notification-hubs-misconfiguration"></a>Configuration incorrecte de Notification Hubs

Pour envoyer des notifications au service de notifications Push adapté, le service Notification Hubs doit s’authentifier dans le contexte de l’application du développeur. Pour ce faire, le développeur doit créer un compte de développeur avec la plateforme correspondante (Google, Apple, Windows, etc.). Ensuite, il doit inscrire son application auprès de la plateforme qui lui fournit des informations d’identification.

Vous devez ajouter ces informations d’identification de plateforme dans le portail Azure. Si aucune notification n’arrive sur l’appareil, la première chose à faire est de vérifier que les bonnes informations d’identification sont configurées dans Notification Hubs. Les informations d’identification doivent être celles de l’application qui a été créée sous un compte de développeur spécifique à la plateforme.

Pour obtenir des instructions pas à pas pour ce processus, consultez [Bien démarrer avec Azure Notification Hubs].

Voici certaines erreurs de configuration courantes :

**Généralités :**

    * Vérifiez que le nom de votre hub de notification (sans fautes de frappe) est le même dans chacun de ces emplacements :
        * Là où vous vous êtes inscrit auprès du client
        * Là où vous envoyez des notifications à partir du backend
        * Là où vous avez configuré les informations d’identification du service de notifications Push.
    * Vérifiez que vous utilisez les mêmes chaînes de configuration de signature d’accès partagé sur le client et sur le serveur d’applications backend. En règle générale, vous devez utiliser **DefaultListenSharedAccessSignature** sur le client et **DefaultFullSharedAccessSignature** sur le serveur d’applications backend (ce qui autorise l’envoi de notifications à Notification Hubs)

**Configuration d’Apple Push Notification Service :**

    You must maintain two different hubs: one hub for production, and another hub for testing. This means that you must upload the certificate that you use in a sandbox environment to a separate hub than the certificate and hub that you are going to use in production. Don't try to upload different types of certificates to the same hub. This might cause notification failures.

    If you inadvertently upload different types of certificates to the same hub, we recommend that you delete the hub and start fresh with a new hub. If for some reason you can't delete the hub, at a minimum, you must delete all the existing registrations from the hub.

**Configuration de Firebase Cloud Messaging :**

    1. Vérifiez que la *clé serveur* que vous avez obtenue de Firebase correspond à celle que vous avez inscrite dans le portail Azure.

    ![Clé serveur Firebase][3]

    2. Vérifiez que vous avez configuré **l’ID de projet** dans le client. La valeur de **l’ID de projet** se trouve dans le tableau de bord Firebase.

    ![ID de projet Firebase][1]

## <a name="application-issues"></a>Problèmes de l’application

**Balises et expressions de balise :**

    If you use tags or tag expressions to segment your audience, it's possible that when you send the notification, no target is found based on the tags or tag expressions that you specify in your send call.

    Review your registrations to ensure that there are matching tags when you send a notification. Then, verify the notification receipt only from the clients that have those registrations.

    As an example, if all your registrations with Notification Hubs were made by using the tag "Politics" and you send a notification with the tag "Sports," the notification isn't sent to any device. A complex case might involve tag expressions in which you registered by using "Tag A" OR "Tag B," but while sending notifications, you target "Tag A && Tag B." In the self-diagnosis tips section later in the article, we show you how to review your registrations and their tags.

**Problèmes liés aux modèles : **

    If you use templates, ensure that you follow the guidelines described in [Templates].

**Inscriptions non valides :**

    If the notification hub was configured correctly, and if any tags or tag expressions were used correctly, valid targets are found. Notifications should be sent to these targets. The Notification Hubs service then fires off several processing batches in parallel. Each batch sends messages to a set of registrations.

    > [!NOTE]
    > Because processing is performed in parallel, the order in which the notifications are delivered is not guaranteed.

    Notification Hubs is optimized for an "at-most once" message delivery model. We attempt deduplication, so that no notifications are delivered more than once to a device. To ensure this, we check registrations and ensure that only one message is sent per device identifier before the message is sent to the push notification service.

    As each batch is sent to the push notification service, which in turn is accepting and validating the registrations, it's possible that the push notification service will detect an error with one or more of the registrations in a batch. In this case, the push notification service returns an error to Notification Hubs, and the process stops. The push notification service drops that batch completely. This is especially true with APNS, which uses a TCP stream protocol.

    We are optimized for at-most once delivery. But in this case, the faulting registration is removed from the database. Then, we retry notification delivery for the rest of the devices in that batch.

    To get more error information about the failed delivery attempt against a registration, you can use the Notification Hubs REST APIs [Per Message Telemetry: Get Notification Message Telemetry](https://msdn.microsoft.com/library/azure/mt608135.aspx) and [PNS feedback](https://msdn.microsoft.com/library/azure/mt705560.aspx). For sample code, see the [Send REST example](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample).

## <a name="push-notification-service-issues"></a>Problèmes liés au service de notifications Push

Une fois que le message de notification a été reçu par le service de notifications Push de la plateforme, le service de notifications Push est chargé de remettre la notification à l’appareil. À ce stade, le service Notification Hubs n’intervient pas et n’a aucun contrôle sur le moment de livraison ou la livraison en elle-même à l’appareil.

Les services de notifications de plateforme étant très performants, les notifications envoyées par le service de notifications Push arrivent généralement sur les appareils en quelques secondes. Si le service de notifications Push subit une limitation, Notification Hubs applique une stratégie d’interruption exponentielle. Si le service de notifications Push reste inaccessible pendant 30 minutes, l’une de nos stratégies consiste à faire expirer puis à supprimer définitivement ces messages.

Si un service de notifications Push tente de remettre une notification alors que l’appareil est hors connexion, la notification est stockée par le service pendant une période limitée. La notification est envoyée à l’appareil lorsque celui-ci est de nouveau disponible.

Pour chaque application, seule une notification récente est stockée. Si plusieurs notifications sont envoyées lorsque l’appareil est hors connexion, chaque nouvelle notification provoque la suppression de la précédente. Ce comportement consistant à ne conserver que la dernière notification est appelé *fusion des notifications* dans Apple Push Notification Service et *réduction* dans Firebase Cloud Messaging (qui utilise une clé de réduction). Si l’appareil reste hors connexion pendant une longue période, les notifications qui ont été stockées pour l’appareil sont supprimées. Pour plus d’informations, consultez [Présentation d’Apple Push Notification Service] et [À propos des messages Firebase Cloud Messaging].

Avec Azure Notification Hubs, vous pouvez passer une clé de fusion via un en-tête HTTP à l’aide de l’API générique SendNotification. Par exemple, pour le SDK .NET, vous devez utiliser `SendNotificationAsync`. L’API SendNotification accepte également les en-têtes HTTP qui sont passés tels quels au service de notifications Push correspondant.

## <a name="self-diagnosis-tips"></a>Conseils d’autodiagnostic

Voici des méthodes permettant de déterminer la cause racine des pertes de notifications dans Notification Hubs :

### <a name="verify-credentials"></a>Vérification des informations d’identification

**Service de notifications Push - Portail des développeurs :**

Vérifiez les informations d’identification dans le portail des développeurs du service de notifications Push correspondant (Apple Push Notification Service, Firebase Cloud Messaging, service de notification Windows, etc.). Pour plus d’informations, consultez [Bien démarrer avec Azure Notification Hubs].

**Portail Azure :**

Pour vérifier et comparer les informations d’identification avec celles que vous avez obtenues via le portail des développeurs du service de notifications Push, dans le portail Azure, accédez à l’onglet **Stratégies d’accès**.

![Portail Azure - Stratégies d’accès][4]

### <a name="verify-registrations"></a>Vérification des inscriptions

**Visual Studio :**

Si vous utilisez Visual Studio à des fins de développement, vous pouvez vous connecter à Azure via l’Explorateur de serveurs pour afficher et gérer un ensemble de services Azure, y compris Notifications Hubs. Cela est particulièrement utile pour votre environnement de développement et de test.

![Explorateur de serveurs Visual Studio.][9]

Vous pouvez afficher et gérer toutes les inscriptions de votre hub, et les classer en fonction de leur plateforme, leur type (native ou basée sur un modèle), leurs balises, leur identificateur de service de notifications Push, leur ID d’inscription et leur date d’expiration. Dans cette page, vous pouvez également modifier une inscription. Ceci est particulièrement utile pour la modification des balises.

![Inscriptions des appareils dans Visual Studio][8]

> [!NOTE]
> Utilisez Visual Studio pour modifier les inscriptions uniquement pendant le développement ou les tests, et avec un nombre limité d’inscriptions. Si vous avez besoin de modifier vos inscriptions en bloc, utilisez la fonctionnalité d’exportation et d’importation des inscriptions décrite dans [Exportation et modification des inscriptions en bloc](https://msdn.microsoft.com/library/dn790624.aspx).

**Explorateur Service Bus :**

De nombreux clients utilisent [Explorateur Service Bus] pour afficher et gérer leur hub de notification. Service Bus Explorer est un projet open source. Pour obtenir des exemples, consultez [Code Service Bus Explorer].

### <a name="verify-message-notifications"></a>Vérification des notifications de messages

 **Portail Azure :**

Pour envoyer une notification de test à vos clients sans backend de service opérationnel, sous **Support + dépannage**, sélectionnez **Envoi de test**.

![Fonctionnalité Envoi de test dans Azure][7]

**Visual Studio :**

Vous pouvez également envoyer des notifications de test à partir de Visual Studio.

![Fonctionnalité Envoi de test dans Visual Studio][10]

Pour plus d’informations sur l’utilisation de Notification Hubs avec l’Explorateur de serveurs Visual Studio, consultez les articles suivants :

* [Afficher les inscriptions d’appareils pour les hubs de notification]
* [Présentation approfondie : Visual Studio 2013 Update 2 RC and Azure SDK 2.3]
* [Announcing release of Visual Studio 2013 Update 3 and Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Résoudre les échecs de remise de notification et analyser les résultats des notifications

**Propriété `EnableTestSend` :**

Lorsque vous envoyez une notification via Notification Hubs, la notification est d’abord mise en file d’attente en vue d’être traitée dans Notification Hubs. Notification Hubs détermine les cibles qui conviennent, puis envoie la notification au service de notifications Push. Si vous utilisez l’API REST ou l’un des SDK clients, le retour réussi de votre appel d’envoi signifie donc uniquement que le message a été correctement placé dans la file d’attente de Notification Hubs. Vous ne savez pas exactement ce qu’il s’est passé lorsque Notification Hubs a finalement envoyé le message au service de notifications Push.

Si votre notification n’arrive pas jusqu’à l’appareil client, il est possible qu’une erreur se soit produite au moment où Notification Hubs a tenté de remettre le message au service de notifications Push. Par exemple, la taille de la charge utile peut dépasser le seuil maximal autorisé par le service de notifications Push, ou les informations d’identification configurées dans Notification Hubs peuvent ne pas être valides.

Pour comprendre ce qu’il s’est passé lors d’une erreur du service de notifications Push, vous pouvez utiliser la propriété [EnableTestSend]. Cette propriété est activée automatiquement lorsque vous envoyez des messages de test à partir du portail ou du client Visual Studio. Elle vous permet d’afficher des informations de débogage détaillées. Vous pouvez également utiliser cette propriété via l’API. Actuellement, vous pouvez l’utiliser dans le SDK .NET. Il est prévu qu’elle soit ajoutée à tous les SDK clients.

Pour utiliser la propriété `EnableTestSend` avec l’appel REST, ajoutez un paramètre de chaîne de requête appelé *test* à la fin de votre appel d’envoi. Par exemple : 

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

**Exemple (Kit de développement logiciel (SDK) .NET) :**

Voici un exemple d’utilisation du SDK .NET pour envoyer une notification (toast) sous forme de fenêtre contextuelle native :

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

À la fin de l’exécution, `result.State` indique seulement la valeur `Enqueued`. Les résultats ne fournissent pas d’informations sur ce qui est arrivé à votre notification Push.

Ensuite, vous pouvez utiliser la propriété booléenne `EnableTestSend`. Utilisez la propriété `EnableTestSend` quand vous initialisez `NotificationHubClient` pour obtenir des informations détaillées sur les erreurs du service de notifications Push qui se produisent lors de l’envoi de la notification. L’appel d’envoi met davantage de temps à être retourné, car il doit attendre que Notification Hubs ait remis la notification au service de notifications Push pour déterminer le résultat.

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

**Exemple de sortie :**

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Ce message indique que des informations d’identification non valides sont configurées dans Notification Hubs, ou qu’il existe un problème avec les inscriptions du hub. Nous vous recommandons de supprimer cette inscription et de laisser le client la recréer avant d’envoyer le message.

> [!NOTE]
> L’utilisation de la propriété `EnableTestSend` entraîne une limitation importante. Utilisez cette option uniquement dans un environnement de développement et de test, et avec un nombre limité d’inscriptions. Les notifications de débogage sont envoyées à un maximum de 10 appareils. De plus, le traitement des envois de débogage est limité à 10 envois par minute.

### <a name="review-telemetry"></a>Révision de la télémétrie

**Utiliser le portail Azure :**

Le portail vous permet d’obtenir un aperçu rapide de toutes les activités sur votre hub de notification.

1. Sous l’onglet **Vue d’ensemble**, vous pouvez voir les inscriptions, les notifications et les erreurs regroupées par plateforme.

    ![Tableau de bord de Notification Hubs][5]

2. Sous l’onglet **Surveiller**, vous pouvez ajouter de nombreuses autres métriques relatives à la plateforme. Vous pouvez ainsi obtenir des informations plus précises sur les erreurs liées au service de notifications Push qui sont retournées lorsque le service Notification Hubs tente d’envoyer la notification au service de notifications Push.

    ![Journal d’activités - Portail Azure][6]

3. Commencez par consultez les **messages entrants**, les **opérations d’inscription** et les **notifications réussies**. Ensuite, accédez à l’onglet de la plateforme pour examiner les erreurs relatives au service de notifications Push.

4. Si les paramètres d’authentification du hub de notification sont incorrects, le message **Erreur d’authentification PNS** s’affiche. Dans ce cas, il est nécessaire de vérifier les informations d’identification du service de notifications Push.

* **Accès par programme**

Pour plus d’informations sur l’accès par programmation, consultez [Accès par programmation à la télémétrie]

> [!NOTE]
> Plusieurs fonctionnalités de télémétrie, comme l’exportation et l’importation des inscriptions, et l’accès à la télémétrie via des API, sont disponibles uniquement avec le niveau de service Standard. Si vous tentez d’utiliser ces fonctionnalités avec le niveau de service Gratuit ou De base, un message d’exception s’affiche lorsque vous utilisez le SDK, et une erreur HTTP 403 (Refusé) s’affiche si vous utilisez les fonctionnalités directement dans les API REST.
>
> Pour utiliser les fonctionnalités de télémétrie, vérifiez d’abord dans le portail Azure que vous utilisez le niveau de service Standard.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/FCMConfigure.png
[3]: ./media/notification-hubs-diagnosing/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Vue d’ensemble de Notification Hubs]: notification-hubs-push-notification-overview.md
[Bien démarrer avec Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Templates]: https://msdn.microsoft.com/library/dn530748.aspx
[Présentation d’Apple Push Notification Service]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[À propos des messages Firebase Cloud Messaging]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Explorateur Service Bus]: https://msdn.microsoft.com/library/dn530751.aspx#sb_explorer
[Code Service Bus Explorer]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Afficher les inscriptions d’appareils pour les hubs de notification]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Présentation approfondie : Visual Studio 2013 Update 2 RC and Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Announcing release of Visual Studio 2013 Update 3 and Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Accès par programmation à la télémétrie]: https://msdn.microsoft.com/library/azure/dn458823.aspx
