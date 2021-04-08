---
title: Fichier include
description: Fichier Include
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/24/2020
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: ca483d0b71bde945a7e46da785dd6a76b3a8f177
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98693395"
---
L’option de protocole AMQP sur WebSockets s’exécute sur le port TCP 443, tout comme l’API HTTP/REST, mais son fonctionnement est identique au mode AMQP classique. Cette option présente une latence de connexion initiale un peu plus élevée en raison des allers-retours de liaison supplémentaire et d’une surcharge légèrement plus importante en cas de compromis pour le partage du port HTTPS. Si ce mode est sélectionné, le port TCP 443 s’avère suffisant à des fins de communication. Les options suivantes permettent de sélectionner le mode AMQP classique ou le mode AMQP WebSockets :

| Language | Option   |
| -------- | ----- |
| .NET     | Propriété [ServiceBusConnection.TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) avec [TransportType.Amqp](/dotnet/api/microsoft.azure.servicebus.transporttype) ou [TransportType.AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype) |
| Java     | [com.microsoft.azure.servicebus.ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings) avec [com.microsoft.azure.servicebus.primitives.TransportType.AMQP](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) ou [com.microsoft.azure.servicebus.primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) |
| Nœud  | [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions) présente un argument de constructeur `webSocket`. |
| Python | [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) avec [TransportType.Amqp](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) ou [TransportType.AmqpOverWebSocket](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) |