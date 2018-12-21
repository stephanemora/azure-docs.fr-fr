---
title: Présentation d’Azure SignalR
description: Présentation du service Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: e66326c6c4d93a92c579255cb00b6614ecc03b8c
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255174"
---
# <a name="what-is-azure-signalr-service"></a>Présentation du service Azure SignalR

Le service Azure SignalR simplifie le processus d’ajout de fonctionnalités web en temps réel aux applications par HTTP. Cette fonctionnalité en temps réel permet au service d’envoyer des mises à jour de contenu aux clients connectés, comme une application web ou mobile monopage. Par conséquent, les clients sont mis à jour sans avoir à interroger le serveur ni à envoyer de nouvelles requêtes HTTP de mise à jour.

Cet article fournit une vue d’ensemble du service Azure SignalR.

## <a name="what-is-azure-signalr-service-used-for"></a>À quoi sert le service Azure SignalR ?

Il existe de nombreux types d’applications qui nécessitent des mises à jour de contenu en temps réel. Les exemples suivants sont de bons candidats pour le service Azure SignalR :

* Les applications ayant besoin de mises à jour fréquentes auprès du serveur. Exemples : jeux, scrutin, enchères, cartes et applications GPS.
* Les tableaux de bord et les applications de monitoring. Exemples : tableaux de bord des entreprises et mises à jour instantanées des ventes.
* Les applications de collaboration. Exemples : applications de tableau blanc et logiciels de réunion d’équipe.
* Les applications qui envoient des notifications. Exemples : réseaux sociaux, messagerie, conversation instantanée, jeux, alertes de voyage, etc.

SignalR offre une abstraction sur de nombreuses techniques utilisées pour créer des applications web en temps réel. [WebSocket](https://wikipedia.org/wiki/WebSocket) est le mécanisme de transport optimal, mais d’autres techniques comme [SSE (Server-Sent Events)](https://wikipedia.org/wiki/Server-sent_events) et le Long Polling sont utilisées lorsque les autres options ne sont pas disponibles. SignalR détecte automatiquement et initialise le mode de transport adapté en fonction des fonctionnalités prises en charge sur le serveur et le client.

SignalR fournit également un modèle de programmation d’applications en temps réel qui permet au serveur d’envoyer des messages à toutes les connexions, ou à un sous-ensemble de connexions appartenant à un utilisateur spécifique ou qui ont été placées dans un groupe arbitraire.

## <a name="how-to-use-azure-signalr-service"></a>Comment utiliser le service Azure SignalR

Il existe actuellement trois façons d’utiliser le service Azure SignalR :

- **[Mettre à l’échelle une application ASP.NET Core SignalR](signalr-overview-scale-aspnet-core.md)** : intégrez un service Azure SignalR à une application ASP.NET Core SignalR pour augmenter la taille des instances à des centaines de milliers de connexions.
- **[Générer des applications en temps réel serverless](signalr-overview-azure-functions.md)** : utilisez l’intégration d’Azure Functions au service Azure SignalR pour générer des applications en temps réel serverless dans des langages tels que JavaScript, C# et Java.
- **[Envoyer des messages à partir du serveur aux clients via l’API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** : le service Azure SignalR fournit l’API REST pour permettre aux applications de publier des messages aux clients connectés au service SignalR, dans n’importe quel langage de programmation compatible avec REST.