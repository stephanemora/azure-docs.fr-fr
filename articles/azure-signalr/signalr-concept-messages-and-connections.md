---
title: Messages et connexions dans Azure SignalR Service
description: Présentation des concepts clés concernant les messages et les connexions dans Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 5f6428231a3639738e8fb52e7dc3f2f2a3d2a26e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392807"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Messages et connexions dans Azure SignalR Service

Le modèle de facturation d’Azure SignalR Service est basé sur le nombre de connexions et le nombre de messages. Cet article explique de quelle manière les messages et les connexions sont définis et comptabilisés en vue de la facturation.


## <a name="message-formats"></a>Formats des messages 

Azure SignalR Service prend en charge les mêmes formats qu’ASP.NET Core SignalR : [JSON](https://www.json.org/) et [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Taille des messages

Azure SignalR Service ne limite pas la taille des messages.

Les messages volumineux sont divisés en messages plus petits de 2 Ko maximum chacun et sont transmis séparément. Des SDK gèrent la division et l’assemblage des messages. Aucune implication des développeurs n’est nécessaire.

Les messages volumineux diminuent les performances de messagerie. Utilisez les messages les plus petits possibles et faites des tests afin de déterminer la taille de message optimale pour chaque scénario d’usage.

## <a name="how-messages-are-counted-for-billing"></a>Mode de calcul des messages pour la facturation

Pour la facturation, seuls les messages envoyés à partir d’Azure SignalR Service sont comptabilisés. Les messages Ping transmis entre les clients et les serveurs ne sont pas pris en compte.

Les messages de plus de 2 Ko sont comptabilisés comme plusieurs messages de 2 Ko chacun. Le graphique du nombre de messages dans le portail Azure est mis à jour tous les 100 messages par hub.

Par exemple, imaginez que vous avez trois clients et un serveur d’applications. Un client envoie un message de 4 Ko pour permettre au serveur de diffuser vers tous les clients. Il y a huit messages comptabilisés : un message entre le service et le serveur d’applications, et trois messages entre le service et les clients. Chaque message est comptabilisé comme deux messages de 2 Ko chacun.

## <a name="how-connections-are-counted"></a>Mode de calcul des connexions

Il existe des connexions serveur et des connexions client avec Azure SignalR Service. Par défaut, chaque serveur d’applications démarre avec cinq connexions initiales par hub et chaque client dispose d’une connexion client.

Le nombre de connexions indiqué dans le portail Azure inclut les connexions serveur et les connexions clientes.

Par exemple, imaginez que vous utilisez deux serveurs d’applications et que vous définissez cinq hubs dans le code. Le nombre de connexions serveur sera de 50 : 2 serveurs d’applications * 5 hubs * 5 connexions par hub.

ASP.NET SignalR calcule le nombre de connexions serveur de manière différente. Il inclut un hub par défaut en plus des hubs que vous définissez. Par défaut, chaque serveur d’applications a besoin de cinq connexions serveur initiales supplémentaires. Le nombre de connexions initiales pour le hub par défaut reste cohérent avec le nombre des autres hubs.

Pendant la durée de vie du serveur d’applications, le service et le serveur d’applications conservent l’état de la connexion de synchronisation et effectuent des ajustements aux connexions serveur pour améliorer les performances et la stabilité du service. Par conséquent, le numéro de connexion au serveur peut changer de temps en temps.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Mode de calcul du trafic entrant/sortant

La distinction entre le trafic entrant et le trafic sortant est basée sur la perspective d’Azure SignalR Service. Le trafic est calculé en octets.

## <a name="related-resources"></a>Ressources associées

- [Types d’agrégation dans Azure Monitor](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [Configuration d’ASP.NET Core SignalR](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
