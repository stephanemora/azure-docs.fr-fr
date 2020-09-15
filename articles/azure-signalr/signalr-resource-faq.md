---
title: Forum aux questions sur Azure SignalR Service
description: Obtenez des réponses aux questions fréquemment posées sur Azure SignalR Service, notamment la résolution des problèmes et les scénarios d’utilisation classiques.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: d5dd765dd9b174ffbfec35b63ad5e55ce84193ad
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489559"
---
# <a name="azure-signalr-service-faq"></a>FAQ sur Azure SignalR Service

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Azure SignalR Service est-il prêt pour une utilisation en production ?

Oui.
Pour l’annonce sur la disponibilité générale, consultez [Disponibilité générale d’Azure SignalR Service](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/). 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) est entièrement pris en charge.

La prise en charge d’ASP.NET SignalR est toujours en *préversion publique*. [Voici un exemple de code](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>La connexion cliente se ferme avec le message d’erreur « Aucun serveur disponible ». Qu’est-ce que cela signifie ?

Cette erreur se produit uniquement quand les clients envoient des messages à Azure SignalR Service.

Si vous n’avez pas de serveur d’applications et que vous utilisez uniquement l’API REST Azure SignalR Service, ce comportement est *lié à la conception*.
Dans l’architecture serverless, les connexions clientes sont en mode d’*écoute* et n’envoient aucun message à Azure SignalR Service.
Approfondissez sur [l’API REST](./signalr-quickstart-rest-api.md).

Si vous avez des serveurs d’applications, ce message d’erreur signifie qu’aucun serveur d’applications n’est connecté à votre instance Azure SignalR Service.

Les causes possibles sont les suivantes :
- Aucun serveur d’applications n’est connecté à Azure SignalR Service. Consultez les journaux d’activité des serveurs d’applications à la recherche d’éventuelles erreurs de connexion. Ce cas est rare dans un paramètre de haute disponibilité avec plusieurs serveurs d’applications.
- Les instances Azure SignalR Service présentent des problèmes de connectivité. Ce problème est temporaire et les instances feront l’objet d’une récupération automatique.
S’il persiste pendant plus d’une heure, [ouvrez un problème sur GitHub](https://github.com/Azure/azure-signalr/issues/new) ou [créez une demande de support dans Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Si plusieurs serveurs d’applications sont en place, les messages d’un client sont-ils envoyés à tous les serveurs ou à un seul d’entre eux ?

Un client et un serveur d’applications sont liés par un mappage un-à-un. Les messages d’un client sont donc toujours envoyés au même serveur d’applications.

Le mappage est conservé jusqu’à ce que le client ou le serveur d’applications se déconnecte.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Si l’un de mes serveurs d’applications est en panne, comment puis-je le trouver et recevoir une notification ?

Azure SignalR Service supervise les pulsations des serveurs d’applications.
Si aucune pulsation n’est reçue pendant une période spécifiée, le serveur d’applications est considéré comme étant hors connexion. Toutes les connexions clientes mappées à ce serveur d’applications sont alors déconnectées.

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>Pourquoi mon `IUserIdProvider` personnalisé lève une exception quand je passe du SDK ASP.NET Core SignalR au SDK Azure SignalR Service ?

Le paramètre `HubConnectionContext context` varie entre le SDK ASP.NET Core SignalR et le SDK Azure SignalR Service quand `IUserIdProvider` est appelé.

Dans ASP.NET Core SignalR, `HubConnectionContext context` est le contexte de la connexion cliente physique avec des valeurs valides pour toutes les propriétés.

Dans le SDK Azure SignalR Service, `HubConnectionContext context` est le contexte de la connexion cliente logique. Le client physique étant connecté à l’instance Azure SignalR Service, un nombre limité de propriétés sont fournies.

Pour l’instant, seules les propriétés `HubConnectionContext.GetHttpContext()` et `HubConnectionContext.User` sont accessibles.
Vous pouvez [examiner le code source](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>Puis-je configurer les transports disponibles dans Azure SignalR Service côté serveur avec ASP.NET Core SignalR ? Est-ce que je peux par exemple désactiver le transport WebSocket ?

Non.

Azure SignalR Service fournit les trois transports pris en charge par ASP.NET Core SignalR par défaut. Ce n’est pas configurable. Azure SignalR Service gère les connexions et les transports pour toutes les connexions clientes.

Vous pouvez configurer des transports côté client comme indiqué dans [Configuration d’ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Que signifient les métriques, telles que le nombre de messages ou de connexions affiché dans le portail Azure ? Quel type d’agrégation dois-je choisir ?

Vous trouverez plus d’informations sur le calcul de ces métriques dans [Messages et connexions dans Azure SignalR Service](signalr-concept-messages-and-connections.md).

Dans le volet Vue d’ensemble des ressources Azure SignalR Service, nous avons déjà choisi le type d’agrégation adapté à vos besoins. Si vous accédez au volet Métriques, vous pouvez utiliser le type d’agrégation pour [Messages et connexions dans Azure SignalR Service](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) en tant que référence.

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>Quelle est la signification des modes de service `Default`, `Serverless` et `Classic` ? Comment faire mon choix ?

Voici des informations sur les modes :
* Le mode `Default` *nécessite* un serveur hub. Dans ce mode, Azure SignalR Service route le trafic client vers ses connexions de serveur hub connecté. Azure SignalR Service recherche un serveur hub connecté. Si le service ne trouve aucun serveur hub connecté, il rejette les connexions clientes entrantes. Vous pouvez également utiliser l’*API de gestion* dans ce mode pour gérer les clients connectés directement via Azure SignalR Service.
* Le mode `Serverless` n’autorise *aucune* connexion serveur. Autrement dit, il les rejette toutes. Tous les clients doivent être en mode serverless. Les clients se connectent à Azure SignalR Service et les utilisateurs emploient généralement des technologies serverless comme *Azure Functions* pour gérer la logique des hubs. [Consultez un exemple simple](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu) qui utilise le mode serverless dans Azure SignalR Service.
* Le mode `Classic` est un état mixte. Quand un hub dispose d’une connexion serveur, le nouveau client est dirigé vers un serveur hub. Si ce n’est pas le cas, le client entre en mode serverless. 

  Cela peut poser un problème. Par exemple, en cas de perte pendant un moment de toutes les connexions serveur, certains clients entrent en mode serverless au lieu d’être dirigés vers un serveur hub.

Voici quelques instructions pour le choix d’un mode :
- En l’absence de serveur hub, choisissez `Serverless`.
- Si tous les hubs disposent de serveurs hub, choisissez `Default`.
- Si certains hubs disposent de serveurs hub, mais que d’autres non, vous pouvez choisir `Classic`, mais cela peut poser un problème. La meilleure solution consiste à créer deux instances : l’une est `Serverless` et l’autre est `Default`.

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>Existe-t-il des différences de fonctionnalités lors de l’utilisation d’Azure SignalR Service avec ASP.NET SignalR ?
Lorsque vous utilisez Azure SignalR Service, certaines API et fonctionnalités d’ASP.NET SignalR ne sont pas prises en charge :
- Il n’est pas possible de faire passer l’état arbitraire entre les clients et le hub (souvent appelé `HubState`).
- La classe `PersistentConnection` n’est pas prise en charge.
- Le *transport Forever Frame* n’est pas pris en charge.
- Azure SignalR Service ne relit plus les messages envoyés au client lorsque le client est hors connexion.
- Lorsque vous utilisez Azure SignalR Service, le trafic pour une connexion cliente est toujours routé (également appelé *rémanent*) vers une instance de serveur d’applications pendant la durée de la connexion.

Comme la prise en charge d’ASP.NET SignalR se concentre sur la compatibilité, les nouvelles fonctionnalités d’ASP.NET Core SignalR ne sont pas toutes prises en charge. Par exemple, *MessagePack* et *Streaming* sont disponibles uniquement pour les applications ASP.NET Core SignalR.

Vous pouvez configurer Azure SignalR Service pour différents modes de service : `Classic`, `Default` et `Serverless`. Le mode `Serverless` n’est pas pris en charge pour ASP.NET. Le plan de données API REST n’est pas non plus pris en charge.

## <a name="where-does-my-data-reside"></a>Où mes données résident-elles ?

Azure SignalR Service fonctionne comme un service de processeur de données. Il ne stocke pas de contenu client et la résidence des données est incluse par défaut. Si vous utilisez Azure SignalR Service avec d’autres services Azure, comme le Stockage Azure pour les diagnostics, consultez [ce livre blanc](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) pour obtenir des conseils sur la façon de maintenir la résidence des données dans les régions Azure.
