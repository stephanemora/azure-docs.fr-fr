---
title: Messages et connexions dans Azure SignalR
description: Présentation des concepts clés concernant les messages et les connexions dans Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: c2348df7a1a55584807a03216e294486ddadfc52
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352595"
---
# <a name="message-and-connection-in-azure-signalr-service"></a>Messages et connexions dans Azure SignalR Service

Le modèle de facturation d’Azure SignalR Service est basé sur le nombre de connexions et le nombre de messages. La manière de définir et de compter les messages et les connexions est expliquée ci-dessous.

## <a name="message-formats-supported"></a>Formats de message pris en charge

Azure SignalR Service prend en charge les mêmes formats qu’ASP.NET Core SignalR : [JSON](https://www.json.org/) et [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

## <a name="message-size"></a>Taille des messages

Service Azure SignalR ne présente pas de limite de taille de message.

Dans la pratique, les messages volumineux sont divisés en plus petits messages de 2 Ko maximum chacun et transmis en tant que messages distincts. Des SDK gèrent la division et l’assemblage des messages. Aucune implication des développeurs n’est nécessaire.

Cependant, les messages volumineux ont un impact négatif sur les performances de la messagerie. Autant que faire se peut, envoyez des messages les plus petits possibles et procédez à des tests afin de choisir la taille de message optimale pour chaque scénario d’utilisation.

## <a name="how-to-count-messages-for-billing-purpose"></a>Comment compter les messages à des fins de facturation ?

Nous comptons uniquement les messages sortants à partir de SignalR Service et ignorons les messages ping entre clients et serveurs.

Un message supérieur à 2 Ko est considéré comme un multiple de messages de 2 Ko. Le graphique du nombre de messages dans le portail Azure se met à jour tous les 100 messages par hub.

Par exemple, vous avez trois clients et un serveur d’applications. Un client envoie un message de 4 Ko pour permettre au serveur de diffuser vers tous les clients. Le nombre de messages est de 8 : un message du service vers le serveur d’applications, trois messages du service vers les clients, et chaque message est compté comme deux messages de 2 Ko.

Le nombre de messages affiché dans le portail Azure est toujours 0, jusqu’à ce que le cumul dépasse 100.

## <a name="how-to-count-connections"></a>Comment compter le nombre de connexions ?

Il existe des connexions serveur et des connexions client. Par défaut, chaque serveur d’applications dispose de cinq connexions par hub avec SignalR Service et chaque client dispose d’une connexion client avec SignalR Service.

Le nombre de connexions indiqué dans le portail Azure inclut les connexions serveur et les connexions client.

Par exemple, vous disposez de deux serveurs d’applications et vous définissez cinq hubs dans des codes. Le nombre de connexions de serveur est de 50 : 2 serveurs d’applications * 5 hubs * 5 connexions/hub.

ASP.NET SignalR diffère dans le calcul des connexions au serveur. Il dispose d’un hub par défaut en plus des hubs définis par le client. Chaque serveur d’applications a besoin de 5 connexions serveur supplémentaires par défaut. Le nombre de connexions pour le hub par défaut reste cohérent avec les autres hubs.

## <a name="how-to-count-inbound-traffic--outbound-traffic"></a>Mode de calcul du trafic entrant/trafic sortant

Les termes « entrant » et « sortant » sont à prendre du point de vue de SignalR Service. Le trafic est calculé en octets. Comme le nombre de messages, le trafic a également son taux d’échantillonnage. Le graphique du trafic entrant/sortant dans le portail Azure se met à jour tous les 100 Ko par hub.

## <a name="related-resources"></a>Ressources associées

- [Type d’agrégation dans Azure Monitor](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [Configuration d’ASP.NET Core SignalR](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)