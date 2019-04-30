---
title: Diagnostiquer la perte des notifications dans Azure Notification Hubs
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
ms.date: 04/04/2019
ms.author: jowargo
ms.openlocfilehash: 4fc4175c03baa4ddb81507dd4001fcdbe7c7058b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61458725"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Diagnostiquer la perte des notifications dans Azure Notification Hubs

Une question courante concernant Azure Notification Hubs est comment résoudre les problèmes lorsque les notifications à partir d’une application n’apparaissent pas sur les appareils clients. Les clients veulent savoir où et pourquoi les notifications ont été supprimées et comment résoudre le problème. Cet article explique les raisons pour lesquelles les notifications peuvent être perdues ou ne pas apparaître sur les appareils. Il explique également comment déterminer la cause racine.

En premier lieu, il est essentiel de comprendre la manière dont Notification Hubs envoie (push) les notifications vers un appareil.

![Architecture de Notification Hubs][0]

Dans un flux de notification d’envoi type, le message est envoyé par le *serveur d’applications backend* à Notification Hubs. Notification Hubs traite toutes les inscriptions. Il prend en compte les balises configurées et les expressions de balise pour déterminer les cibles. Les cibles sont les inscriptions qui doivent recevoir la notification push. Ces inscriptions peuvent s’étendre sur des plateformes prises en charge : Android, Baidu (appareils Android en Chine), le système d’exploitation de feu (Amazon) iOS, Windows et Windows Phone.

Une fois les cibles établies, Notification Hubs envoie (push) des notifications au *service de notifications Push* correspondant à la plateforme de l’appareil. Il peut s’agir, par exemple, d’Apple Push Notification Service (APNs) pour Apple ou de Firebase Cloud Messaging (FCM) pour Google. Notification Hubs envoie les notifications regroupées en plusieurs lots d’inscriptions. Il s’authentifie auprès du service de notification push correspondant, selon les informations d’identification que vous définissez dans le portail Azure, sous **configurer un Hub de Notification**. Le service de notifications Push transmet alors les notifications aux *appareils clients* correspondants.

Le dernier tronçon de remise des notifications est entre le service de notification push de la plateforme et l’appareil. Remise de notification peut échouer au niveau de chacun des quatre phases dans le processus de notification push (client, serveur principal d’application, Notification Hubs et service de notifications push de la plateforme). Pour plus d’informations sur l’architecture de Notification Hubs, consultez [Vue d’ensemble de Notification Hubs].

Un échec de remise des notifications peut se produire lors de la phase de test ou de préproduction. À ce stade, la perte des notifications peut indiquer un problème de configuration. En cas de défaillance de remettre des notifications en production, certaines ou toutes les notifications peuvent être supprimées. Une application plus approfondie ou problème de modèle de messagerie est indiquée dans ce cas.

La section suivante présente les scénarios dans lesquels notifications peuvent être perdues, des plus courants de rares.

## <a name="notification-hubs-misconfiguration"></a>Configuration incorrecte de Notification Hubs ##

Pour envoyer des notifications au service de notifications push correspondant, Notification Hubs doit s’authentifier lui-même dans le contexte de votre application. Vous devez créer un compte de développeur avec le service de notification de la plateforme cible (Microsoft, Apple, Google, etc.). Ensuite, vous devez inscrire votre application avec le système d’exploitation où vous obtenez un jeton ou la clé que vous utilisez pour travailler avec le PNS cible.

Vous devez ajouter ces informations d’identification de plateforme dans le portail Azure. Si aucune notification n’arrive sur l’appareil, la première étape consiste à vérifier que les informations d’identification correctes sont configurées dans Notification Hubs. Les informations d’identification doivent correspondre à l’application est créée sous un compte de développeur spécifique à la plateforme.

Pour obtenir des instructions pas à pas pour ce processus, consultez [Bien démarrer avec Azure Notification Hubs].

Voici certaines erreurs de configuration courantes :

### <a name="notification-hub-name-location"></a>Emplacement de nom de concentrateur de notification

Vérifiez que le nom de votre hub de notification (sans fautes de frappe) est le même dans chacun de ces emplacements :
   * Dans lequel vous inscrivez à partir du client
   * Où vous envoyer des notifications à partir du backend
   * Où vous avez configuré les informations d’identification du service de notification push

Vérifiez que vous utilisez les chaînes de configuration de signature accès partagé sur le client et la serveur principal d’applications. En règle générale, vous devez utiliser **DefaultListenSharedAccessSignature** sur le client et **DefaultFullSharedAccessSignature** sur l’application back-end. Cela accorde des autorisations pour envoyer des notifications à Notification Hubs.

### <a name="apn-configuration"></a>Configuration d’APN ###

Vous devez disposer de deux hubs : un pour la production et l’autre pour le test. Vous devez télécharger le certificat que vous utilisez dans un environnement de bac à sable pour un concentrateur distinct que le certificat/hub que vous utiliserez en production. N’essayez pas de charger plusieurs types de certificats dans un même hub. Cela entraînera des défaillances de notification.

Si vous chargez par inadvertance différents types de certificats dans le même hub, supprimez le hub et commencez avec un nouveau concentrateur. Si pour une raison quelconque vous ne pouvez pas supprimer le hub, vous devez au moins supprimer tous les enregistrements existants à partir du concentrateur.

### <a name="fcm-configuration"></a>Configuration de FCM ###

1. Vérifiez que le *clé serveur* obtenu à partir de correspondances de Firebase la clé de serveur que vous avez inscrit dans le portail Azure.

   ![Clé serveur Firebase][3]

2. Vérifiez que vous avez configuré **l’ID de projet** dans le client. La valeur de **l’ID de projet** se trouve dans le tableau de bord Firebase.

   ![ID de projet Firebase][1]

## <a name="application-issues"></a>Problèmes de l’application ##

### <a name="tags-and-tag-expressions"></a>Balises et expressions de balise ###

Si vous utilisez des balises ou des expressions de balise pour segmenter votre public, il est possible que lorsque vous envoyez la notification, aucune cible n’est trouvée. Cette erreur est basée sur les balises spécifiées ou les expressions de balise dans votre appel d’envoi.

Passez en revue vos inscriptions pour vérifier les balises correspondent à lorsque vous envoyez une notification. Ensuite, vérifiez l’accusé de réception à partir des seuls les clients qui ont ces inscriptions.

Par exemple, supposons que toutes vos inscriptions avec Notification Hubs utilisent la balise « Politique ». Si vous envoyez ensuite une notification avec la balise « Sports », la notification ne seront pas envoyée à n’importe quel appareil. Un cas complexe peut impliquer des expressions de balise où vous avez enregistré à l’aide de « Balise A » *ou* « Balise B », mais vous ciblés « balise A & & balise b ». La section de conseils d’autodiagnostic plus loin dans cet article vous montre comment vérifier vos inscriptions et leurs balises.

### <a name="template-issues"></a>Problèmes liés aux modèles ###

Si vous utilisez des modèles, suivez bien les instructions décrites dans la section [Modèles].

### <a name="invalid-registrations"></a>Inscriptions non valides ###

Si le hub de notification a été configuré correctement et / expressions de balises ont été correctement utilisées, les cibles valides sont trouvées. Les notifications doivent être envoyées à ces cibles. Notification Hubs déclenche ensuite plusieurs traitements en parallèle. Chaque lot envoie des messages à un ensemble d’inscriptions.

> [!NOTE]
> Étant donné que Notification Hubs traite les lots en parallèle, l’ordre dans lequel les notifications sont remises n’est pas garanti.

Notification Hubs est optimisé pour un modèle de remise de message de « en-une fois maximum ». Nous tentons une déduplication de sorte qu’aucune notification ne soit remise plus d’une fois au même périphérique. Les enregistrements sont vérifiés pour s’assurer qu’un seul message est envoyé par identificateur de périphérique avant d’être envoyé au service de notifications push.

Chaque lot est envoyé au service de notifications push, qui à son tour accepte et valide les inscriptions. Pendant ce processus, il est possible que le service de notification push détecte une erreur avec un ou plusieurs inscriptions dans un lot. Le service de notification push puis retourne une erreur à Notification Hubs, et le processus s’arrête. Le service de notifications Push abandonne alors l’intégralité du lot. Cela est particulièrement vrai avec APNs, qui utilise un protocole de flux TCP.

Dans ce cas, l’inscription défaillante est supprimée à partir de la base de données. Ensuite, la remise des notifications est retentée pour les appareils restants du lot.

Pour obtenir plus d’informations sur la tentative de remise a échoué pour une inscription erreur, vous pouvez utiliser les API REST de Notification Hubs [télémétrie par Message : Obtenir la télémétrie de message de Notification](https://msdn.microsoft.com/library/azure/mt608135.aspx) et [commentaires PNS](https://msdn.microsoft.com/library/azure/mt705560.aspx). Pour obtenir un exemple de code, consultez [l’exemple d’API REST d’envoi](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Problèmes liés au service de notifications Push

Une fois que le service de notification push reçoit la notification, il remet la notification à l’appareil. À ce stade, Notification Hubs a aucun contrôle sur la remise de la notification à l’appareil.

Étant donné que les services de notification de plateforme sont fiables, notifications ont tendance à atteindre des appareils en quelques secondes. Si la limitation de service de notifications push, Notification Hubs applique une stratégie de temporisation exponentielle. Si le service de notification push reste inaccessible pendant 30 minutes, il existe une stratégie en place pour faire expirer et supprimer les messages définitivement.

Si un service de notifications push tente de remettre une notification, mais l’appareil est hors connexion, la notification est stockée par le service de notification push. Il est stocké pour une période donnée. La notification est envoyée à l’appareil lorsque celui-ci est de nouveau disponible.

Chaque application stocke uniquement une notification récente. Si plusieurs notifications sont envoyées lorsque l’appareil est hors connexion, chaque nouvelle notification provoque l’avant-dernière sont ignorés. Conserver uniquement la dernière notification est appelé *fusion* dans APN et *réduction* dans FCM. (FCM utilise une clé de réduction). Lorsque le périphérique reste hors connexion pendant un certain temps, les notifications qui ont été stockées pour l’appareil sont ignorées. Pour plus d’informations, consultez [Présentation d’Apple Push Notification Service] et [À propos des messages Firebase Cloud Messaging].

Avec Notification Hubs, vous pouvez passer une clé de fusion via un en-tête HTTP à l’aide de l’API générique SendNotification. Par exemple, pour le SDK .NET, vous devez utiliser `SendNotificationAsync`. L’API SendNotification accepte également les en-têtes HTTP qui sont passés en l’état au service de notifications push correspondant.

## <a name="self-diagnosis-tips"></a>Conseils d’autodiagnostic

Voici les chemins d’accès pour diagnostiquer la cause racine des pertes de notifications dans Notification Hubs.

### <a name="verify-credentials"></a>Vérification des informations d’identification ###

#### <a name="push-notification-service-developer-portal"></a>Portail développeur de service de notifications Push ####

Vérifiez les informations d’identification dans le portail des développeurs du service de notifications Push correspondant (Apple Push Notification Service, Firebase Cloud Messaging, service de notification Windows, etc.). Pour plus d’informations, consultez [Tutoriel : Envoyer des notifications vers des applications de plateforme Windows universelle avec Azure Notification Hubs](https://docs.microsoft.com/en-us/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification).

#### <a name="azure-portal"></a>Portail Azure ####

Pour examiner et faire correspondre les informations d’identification avec ceux obtenus avec le portail des développeurs push notification service, accédez à la **des stratégies d’accès** onglet dans le portail Azure.

![Portail Azure - Stratégies d’accès][4]

### <a name="verify-registrations"></a>Vérification des inscriptions

#### <a name="visual-studio"></a>Visual Studio ####

Dans Visual Studio, vous pouvez vous connecter à Azure via l’Explorateur de serveurs pour afficher et gérer plusieurs services Azure, y compris les concentrateurs de Notification. Ce raccourci est principalement utile pour votre environnement de développement/test.

![Explorateur de serveurs Visual Studio.][9]

Vous pouvez afficher et gérer toutes les inscriptions dans votre hub. Les inscriptions peuvent être classées par plate-forme, inscription native ou modèle, balise, identificateur de service de notification push, ID d’inscription et date d’expiration. Dans cette page, vous pouvez également modifier une inscription. Il est particulièrement utile pour la modification de balises.

Avec le bouton droit de votre hub de notification dans **Explorateur de serveurs**, puis sélectionnez **diagnostiquer**. 

![Explorateur de serveurs Visual Studio : Diagnostiquer menu](./media/notification-hubs-diagnosing/diagnose-menu.png)

Vous consultez la page suivante :

![Visual Studio : Diagnostiquer la page](./media/notification-hubs-diagnosing/diagnose-page.png)

Basculez vers le **inscriptions d’appareils** page :

![Visual Studio : Inscriptions d’appareils](./media/notification-hubs-diagnosing/VSRegistrations.png)

Vous pouvez utiliser **Test d’envoi** page pour envoyer un message de notification de test :

![Visual Studio : Testez les envois.](./media/notification-hubs-diagnosing/test-send-vs.png)

> [!NOTE]
> Utilisez Visual Studio pour modifier les inscriptions uniquement pendant le développement/test et avec un nombre limité d’inscriptions. Si vous devez modifier vos inscriptions en bloc, envisagez d’utiliser l’exportation et importent les fonctionnalités d’inscription décrite dans [How To : Exporter et modifier des inscriptions en bloc](https://msdn.microsoft.com/library/dn790624.aspx).

#### <a name="service-bus-explorer"></a>Explorateur Service Bus ####

De nombreux clients utilisent [Explorateur Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer) pour afficher et gérer leurs hubs de notification. Service Bus Explorer est un projet open source. 

### <a name="verify-message-notifications"></a>Vérification des notifications de messages

#### <a name="azure-portal"></a>Portail Azure ####

Pour envoyer une notification de test à vos clients sans backend de service opérationnel, sous **Support + dépannage**, sélectionnez **Envoi de test**.

![Fonctionnalité Envoi de test dans Azure][7]

#### <a name="visual-studio"></a>Visual Studio ####

Vous pouvez également envoyer des notifications de test à partir de Visual Studio.

![Fonctionnalité Envoi de test dans Visual Studio][10]

Pour plus d’informations sur l’utilisation de Notification Hubs avec l’Explorateur de serveurs Visual Studio, consultez les articles suivants :

* [Comment afficher les inscriptions d’appareils pour les concentrateurs de notification](https://docs.microsoft.com/en-us/previous-versions/windows/apps/dn792122(v=win.10))
* [Présentation approfondie : Visual Studio 2013 Update 2 RC and Azure SDK 2.3]
* [Announcing release of Visual Studio 2013 Update 3 and Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Résoudre les échecs de remise de notification et analyser les résultats des notifications

#### <a name="enabletestsend-property"></a>Propriété EnableTestSend ####

Lorsque vous envoyez une notification via Notification Hubs, la notification est initialement en file d’attente. Notification Hubs détermine les cibles qui conviennent, puis envoie la notification au service de notifications Push. Si vous utilisez l’API REST ou un des kits de développement logiciel clients, le retour de votre appel d’envoi signifie uniquement que le message est en file d’attente avec Notification Hubs. Il ne donne une idée que s’est-il passé lorsque Notification Hubs a finalement envoyé la notification au service de notifications push.

Si votre notification n’arrive pas à l’appareil client, une erreur peut avoir s’est produite lors de la Notification Hubs a tenté de transmettre au service de notifications push. Par exemple, la taille de la charge utile peut dépasser le seuil maximal autorisé par le service de notifications Push, ou les informations d’identification configurées dans Notification Hubs peuvent ne pas être valides.

Pour comprendre ce qu’il s’est passé lors d’une erreur du service de notifications Push, vous pouvez utiliser la propriété [EnableTestSend]. Cette propriété est activée automatiquement lorsque vous envoyez des messages de test à partir du portail ou du client Visual Studio. Vous pouvez utiliser cette propriété pour afficher des informations de débogage détaillées ainsi que via les API. Actuellement, vous pouvez l’utiliser dans le SDK .NET. Il sera ajouté à tous les kits de développement logiciel client par la suite.

Pour utiliser la propriété `EnableTestSend` avec l’appel REST, ajoutez un paramètre de chaîne de requête appelé *test* à la fin de votre appel d’envoi. Par exemple : 

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>Exemple de kit de développement logiciel .NET ####

Voici un exemple d’utilisation du SDK .NET pour envoyer une notification (toast) sous forme de fenêtre contextuelle native :

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

À la fin de l’exécution, `result.State` indique seulement la valeur `Enqueued`. Les résultats ne fournissent pas d’informations sur ce qui est arrivé à votre notification push.

Ensuite, vous pouvez utiliser la propriété booléenne `EnableTestSend`. Utilisez la propriété `EnableTestSend` quand vous initialisez `NotificationHubClient` pour obtenir des informations détaillées sur les erreurs du service de notifications Push qui se produisent lors de l’envoi de la notification. L’appel d’envoi prend plus de temps pour retourner, car il doit tout d’abord de Notification Hubs pour remettre la notification au service de notifications push.

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

#### <a name="sample-output"></a>Exemple de sortie ####

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Ce message indique que les informations d’identification configurées dans Notification Hubs ne sont pas valides ou qu’il existe un problème avec les inscriptions dans le hub. Supprimer cette inscription et permettre au client de recréer l’inscription avant d’envoyer le message.

> [!NOTE]
> L’utilisation de la propriété `EnableTestSend` entraîne une limitation importante. Utilisez cette option uniquement dans un environnement de développement/test et avec un ensemble limité d’inscriptions. Déboguer des notifications sont envoyées à un maximum de 10 appareils. Il existe également une limite sur le traitement des envois de débogage, à 10 par minute.

### <a name="review-telemetry"></a>Révision de la télémétrie ###

#### <a name="azure-portal"></a>Portail Azure ####

Le portail vous permet d’obtenir un aperçu rapide de toutes les activités sur votre hub de notification.

1. Sous l’onglet **Vue d’ensemble**, vous pouvez voir les inscriptions, les notifications et les erreurs regroupées par plateforme.

   ![Tableau de bord de Notification Hubs][5]

2. Sous l’onglet **Surveiller**, vous pouvez ajouter de nombreuses autres métriques relatives à la plateforme. Vous pouvez consulter en particulier les erreurs qui sont retournées lorsque Notification Hubs tente d’envoyer la notification au service de notifications push.

   ![Journal d’activités - Portail Azure][6]

3. Commencez par consultez les **messages entrants**, les **opérations d’inscription** et les **notifications réussies**. Ensuite, accédez à l’onglet de la plateforme pour examiner les erreurs relatives au service de notifications Push.

4. Si les paramètres d’authentification du hub de notification sont incorrects, le message **Erreur d’authentification PNS** s’affiche. Il est une bonne indication pour vérifier les informations d’identification du service de notification push.

#### <a name="programmatic-access"></a>Accès par programme ####

Pour plus d’informations sur l’accès par programmation, consultez [accès par programme](https://docs.microsoft.com/en-us/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Plusieurs fonctionnalités de télémétrie, comme l’exportation et l’importation des inscriptions, et l’accès à la télémétrie via des API, sont disponibles uniquement avec le niveau de service Standard. Si vous essayez d’utiliser ces fonctionnalités à partir de gratuit ou de base de niveau de service, vous obtiendrez un message d’exception si vous utilisez le Kit de développement. Vous obtiendrez une erreur HTTP 403 (interdit) si vous utilisez les fonctionnalités directement à partir de l’API REST.
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
[Modèles]: https://msdn.microsoft.com/library/dn530748.aspx
[Présentation d’Apple Push Notification Service]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[À propos des messages Firebase Cloud Messaging]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Présentation approfondie : Visual Studio 2013 Update 2 RC and Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Announcing release of Visual Studio 2013 Update 3 and Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
