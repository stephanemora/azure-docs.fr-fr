---
title: Messages et connexions dans Azure SignalR Service
description: Présentation des concepts clés concernant les messages et les connexions dans Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: ce1542278303910837a69d3184c1de86a9237f8e
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55996234"
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

Le nombre de messages affiché dans le portail Azure reste à 0 jusqu’à ce que le total de messages dépasse 100.

## <a name="how-connections-are-counted"></a>Mode de calcul des connexions

Il existe des connexions serveur et des connexions client. Par défaut, chaque serveur d’applications dispose de cinq connexions par hub avec Azure SignalR Service et chaque client a une connexion cliente avec Azure SignalR Service.

Le nombre de connexions indiqué dans le portail Azure inclut les connexions serveur et les connexions clientes.

Par exemple, imaginez que vous utilisez deux serveurs d’applications et que vous définissez cinq hubs dans le code. Le nombre de connexions serveur sera de 50 : 2 serveurs d’applications * 5 hubs * 5 connexions par hub.

ASP.NET SignalR calcule le nombre de connexions serveur de manière différente. Il inclut un hub par défaut en plus des hubs que vous définissez. Par défaut, chaque serveur d’applications a besoin de cinq connexions serveur supplémentaires. Le nombre de connexions pour le hub par défaut reste cohérent avec le nombre des autres hubs.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Mode de calcul du trafic entrant/sortant

La distinction entre le trafic entrant et le trafic sortant est basée sur la perspective d’Azure SignalR Service. Le trafic est calculé en octets. Comme le nombre de messages, le trafic a également son taux d’échantillonnage. Le graphique du trafic entrant/sortant dans le portail Azure est mis à jour tous les 100 Ko par hub.

## <a name="related-resources"></a>Ressources associées

- [Types d’agrégation dans Azure Monitor](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [Configuration d’ASP.NET Core SignalR](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)