---
title: Forum aux questions sur Azure SignalR Service
description: Bénéficiez d’un accès rapide aux questions fréquemment posées sur Azure SignalR Service, sur la résolution des problèmes et sur les scénarios d’utilisation classiques.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: c944ae3a5d647cc457edd20a5d3dd0489e19e286
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192284"
---
# <a name="azure-signalr-service-faq"></a>FAQ sur Azure SignalR Service

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Azure SignalR Service est-il prêt pour une utilisation en production ?

Oui.
Pour notre annonce sur la disponibilité générale, consultez [Disponibilité générale d’Azure SignalR Service](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/). 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) est entièrement pris en charge.

La prise en charge d’ASP.NET SignalR est toujours en *préversion publique*. Voici un [exemple de code](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>La connexion du client se ferme avec le message d’erreur « Aucun serveur disponible ». Qu’est-ce que cela signifie ?

Cette erreur se produit uniquement quand les clients envoient des messages à SignalR Service.

Si vous n’avez pas de serveur d’applications et que vous utilisez uniquement l’API REST SignalR Service, ce comportement est **lié à la conception**.
Dans l’architecture serverless, les connexions clientes sont en mode **ÉCOUTER** et n’envoient aucun message à SignalR Service.
Découvrez-en plus sur l’[API REST](./signalr-quickstart-rest-api.md).

Si vous avez des serveurs d’applications, ce message d’erreur signifie qu’aucun serveur d’applications n’est connecté à votre instance SignalR Service.

Les causes possibles sont les suivantes :
- Aucun serveur d’applications n’est connecté à SignalR Service. Consultez les journaux d’activité des serveurs d’applications à la recherche d’éventuelles erreurs de connexion. Ce cas est rare dans le paramètre de haute disponibilité avec plusieurs serveurs d’applications.
- Les instances de SignalR Service présentent des problèmes de connectivité. Ce problème est temporaire et fait l’objet d’une récupération automatique.
S’il persiste pendant plus d’une heure, [ouvrez un problème sur GitHub](https://github.com/Azure/azure-signalr/issues/new) ou [créez une demande de support dans Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Si plusieurs serveurs d’applications sont en place, les messages d’un client sont-ils envoyés à tous les serveurs ou à un seul d’entre eux ?

Un client et un serveur d’applications sont liés par un mappage un-à-un. Les messages d’un client sont donc toujours envoyés au même serveur d’applications.

Le mappage entre le client et le serveur d’applications est conservé jusqu’à la déconnexion du client ou du serveur d’applications.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Si l’un de mes serveurs d’applications est en panne, comment puis-je le trouver et recevoir une notification ?

SignalR Service supervise les pulsations des serveurs d’applications.
Si aucune pulsation n’est reçue pendant une période spécifiée, le serveur d’applications est considéré comme étant hors connexion. Toutes les connexions clientes mappées à ce serveur d’applications sont alors déconnectées.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Pourquoi mon `IUserIdProvider` personnalisé lève-t-il une exception lors du passage du SDK ASP.NET Core SignalR au SDK Azure SignalR Service ?

Le paramètre `HubConnectionContext context` varie entre le SDK ASP.NET Core SignalR et le SDK Azure SignalR Service quand `IUserIdProvider` est appelé.

Dans ASP.NET Core SignalR, `HubConnectionContext context` est le contexte de la connexion cliente physique avec des valeurs valides pour toutes les propriétés.

Dans le SDK Azure SignalR Service, `HubConnectionContext context` est le contexte de la connexion cliente logique. La connexion cliente physique étant connectée à l’instance SignalR Service, un nombre limité de propriétés sont fournies.

Pour l’instant, seules les propriétés `HubConnectionContext.GetHttpContext()` et `HubConnectionContext.User` sont accessibles.
Vous pouvez examiner le code source [ici](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Puis-je configurer les transports disponibles dans SignalR Service parallèlement à sa configuration côté serveur avec ASP.NET Core SignalR ? Puis-je par exemple désactiver le transport WebSocket ?

Non.

Azure SignalR Service fournit les trois transports pris en charge par ASP.NET Core SignalR par défaut. Ceci n’est pas configurable. SignalR Service gère les connexions et les transports pour toutes les connexions clientes.

Vous pouvez configurer les transports côté client conformément à cette [documentation](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-showed-in-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Que signifient les métriques, telles que le nombre de messages ou de connexions affiché dans le portail Azure ? Quel type d’agrégation dois-je choisir ?

Vous trouverez plus d’informations sur la façon de calculer ces métriques [ici](signalr-concept-messages-and-connections.md).

Dans le panneau Vue d’ensemble des ressources d’Azure SignalR Service, nous avons déjà choisi le type d’agrégation adapté à vos besoins. Si vous accédez au panneau Métriques, vous pouvez prendre ce type d’agrégation-[ici](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) comme référence.

## <a name="what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose"></a>Que signifie le mode de service `Default`/`Serverless`/`Classic` ? Comment faire mon choix ?

Modes :
* Le mode `Default` **nécessite** un serveur hub. Si aucune connexion serveur n’est disponible pour le hub, la tentative de connexion du client au hub échoue.
* Le mode `Serverless` n'autorise **AUCUNE** connexion au serveur, c’est-à-dire qu’il rejette toute connexion au serveur, tous les clients doivent entrer en mode serverless.
* Le mode `Classic` est un état mixte. Lorsqu’un hub dispose d’une connexion au serveur, le nouveau client est dirigé vers le serveur hub, si ce n’est pas le cas, le client entre en mode serverless.

  Cela peut entraîner des problèmes, par exemple, la perte de toutes les connexions au serveur pendant un moment, certains clients entreront en mode serverless au lieu d’être dirigés vers le serveur hub.

Choix :
1. Aucun serveur hub : choisissez `Serverless`.
1. Tous les hubs disposent de serveurs hub : choisissez `Default`.
1. Certains hubs disposent de serveurs Hub, d’autres non : choisissez `Classic`, mais cela peut entraîner un problème. Créer deux instances est la meilleure solution, l’une est `Serverless` et l’autre est `Default`.

## <a name="any-feature-differences-when-using-azure-signalr-for-aspnet-signalr"></a>Y a t-il des différence de fonctionnalités lorsqu’on utilise Azure SignalR pour ASP.NET SignalR ?
Lorsque vous utilisez Azure SignalR, certaines API et fonctionnalités d’ASP.NET SignalR ne sont plus prises en charge :
- Il n’est pas possible de faire passer l’état arbitraire des clients au hub (souvent appelé `HubState`) lors de l’utilisation d’Azure SignalR
- La classe `PersistentConnection` n’est pas encore prise en charge lors de l’utilisation d’Azure SignalR
- **Forever Frame Transport** n’est pas pris en charge lors de l’utilisation d’Azure SignalR
- Azure SignalR ne relit plus les messages envoyés au client lorsque le client est hors connexion
- Lors de l’utilisation d’Azure SignalR, le trafic pour une connexion client est toujours routé (également appelé **rémanent**) vers une instance de serveur d’applications pendant la durée de la connexion

La prise en charge d’ASP.NET SignalR se concentre sur la compatibilité, c’est pourquoi, toutes les nouvelles fonctionnalités d’ASP.NET Core SignalR ne sont pas prises en charge. Par exemple, **MessagePack**, **Streaming**, etc., sont uniquement disponibles pour les applications ASP.NET Core SignalR.

SignalR Service peut être configuré pour différents modes de service : `Classic`/`Default`/`Serverles`s. Dans cette prise en charge d’ASP.NET, le mode `Serverless` n’est pas pris en charge. Le plan de données API REST n’est pas non plus pris en charge.

## <a name="where-do-my-data-reside"></a>Où mes données résident-elles ?

Le service Azure SignalR fonctionne comme un service de processeur de données. Il ne stocke pas de contenu client et la résidence des données est prévue par essence. Si vous utilisez le service Azure SignalR avec d’autres services Azure, tels que le Stockage Azure pour les diagnostics, consultez [cette page](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) pour obtenir des conseils sur la façon de maintenir la résidence des données dans les régions Azure.
