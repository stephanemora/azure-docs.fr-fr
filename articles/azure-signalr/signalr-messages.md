---
title: Messages et connexions dans Azure SignalR
description: Présentation des concepts clés concernant les messages et les connexions dans Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 5a0430e9ad124319147342c49fc51e11472ac8ff
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53812709"
---
# <a name="message-and-connection-in-azure-signalr-service"></a>Messages et connexions dans Azure SignalR Service

Le modèle de facturation d’Azure SignalR Service est basé sur le nombre de connexions et le nombre de messages. La manière de définir et de compter les messages et les connexions est expliquée ci-dessous.

## <a name="message-formats-supported"></a>Formats de message pris en charge

Azure SignalR Service prend en charge les mêmes formats qu’ASP.NET Core SignalR : [JSON](https://www.json.org/) et [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

## <a name="message-size"></a>Taille des messages

Service Azure SignalR ne présente pas de limite de taille de message.

Dans la pratique, les messages volumineux sont divisés en plus petits messages de 2 Ko maximum chacun et transmis en tant que messages distincts. Le fractionnement et l’assemblage des messages sont gérés par les kits de développement logiciel. Aucune implication des développeurs n’est nécessaire.

Cependant, les messages volumineux ont un impact négatif sur les performances de la messagerie. Autant que faire se peut, envoyez des messages les plus petits possibles et procédez à des tests afin de choisir la taille de message optimale pour chaque scénario d’utilisation.

## <a name="how-to-count-messages-for-billing-purpose"></a>Comment compter les messages à des fins de facturation ?

Nous comptons uniquement les messages sortants à partir de SignalR Service et ignorons les messages ping entre clients et serveurs.

Un message supérieur à 2 Ko est considéré comme un multiple de messages de 2 Ko. Le graphique du nombre de messages dans le portail Azure se met à jour tous les 100 messages par hub.

Par exemple, un utilisateur dispose de 3 clients et d’un serveur d’application. Un client envoie un message de 4 Ko pour permettre au serveur de diffuser vers tous les clients. Le nombre de messages sera de 8 : 1 message du service vers le serveur d’applications, 3 messages du service aux clients et chaque message est compté comme 2 messages de 2 Ko.

Le nombre de messages affiché dans le portail Azure est toujours 0, jusqu’à ce que le cumul dépasse 100.

## <a name="how-to-count-connections"></a>Comment compter le nombre de connexions ?

Il existe des connexions serveur et des connexions client. Par défaut, chaque serveur d’applications dispose de 5 connexions par hub avec SignalR Service et chaque client, d’une connexion client avec SignalR Service.

Le nombre de connexions indiqué dans le portail Azure inclut les connexions serveur et les connexions client.

Par exemple, un utilisateur dispose de deux serveurs d’applications et définit 5 hubs dans ses codes. Le nombre de connexions de serveur indiqué dans le portail Azure sera de 2 serveurs d’applications * 5 hubs * 5 connexions/hub = 50 connexions au serveur.

## <a name="related-resources"></a>Ressources associées

- [Configuration d’ASP.NET Core SignalR](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)