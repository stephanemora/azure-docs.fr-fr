---
title: Fichier include
description: Fichier Include
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 04/08/2021
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 08fccf366321b075542f36b86c9bf22d5d877167
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112415675"
---
L’option de protocole AMQP sur WebSockets s’exécute sur le port TCP 443, tout comme l’API HTTP/REST, mais son fonctionnement est identique au mode AMQP classique. Cette option présente une latence de connexion initiale plus élevée en raison des allers-retours de liaison supplémentaire et d’une surcharge légèrement plus importante en cas de compromis pour le partage du port HTTPS. Si ce mode est sélectionné, le port TCP 443 s’avère suffisant à des fins de communication. Les options suivantes permettent de sélectionner le mode AMQP WebSockets. 

| Language | Option   |
| -------- | ----- |
| .NET (Azure.Messaging.ServiceBus)    | Créez [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient.-ctor) en utilisant un constructeur qui prend [ServiceBusClientOptions](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions) comme paramètre. Définissez [ServiceBusClientOptions.TransportType](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions.transporttype) sur [ServiceBusTransportType.AmqpWebSockets](/dotnet/api/azure.messaging.servicebus.servicebustransporttype). |
| .NET (Microsoft.Azure.ServiceBus)    | Lors de la création d’objets clients, utilisez des constructeurs qui acceptent [TransportType](/dotnet/api/microsoft.azure.servicebus.transporttype), [ServiceBusConnection](/dotnet/api/microsoft.azure.servicebus.servicebusconnection) ou [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder) comme paramètres. <p>Pour la construction qui prend `transportType` comme paramètre, définissez le paramètre sur [TransportType.AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype).</p> <p>Pour le constructeur qui prend `ServiceBusConnection` comme paramètre, définissez [ServiceBusConnection.TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) sur [TransportType.AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype).</p> <p>Si vous utilisez `ServiceBusConnectionStringBuilder`, utilisez des constructeurs qui vous donnent la possibilité de spécifier le `transportType`.</p> |
| Java (com.azure.messaging.servicebus)     | Lorsque vous créez des clients, définissez [ServiceBusClientBuilder.transportType](/java/api/com.azure.messaging.servicebus.servicebusclientbuilder.transporttype) sur [AmqpTransportType.AMQP.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype). |
| Java (com.microsoft.azure.servicebus)    | Lorsque vous créez des clients, définissez `transportType` dans [com.microsoft.azure.servicebus.ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings#com_microsoft_azure_servicebus_ClientSettings_ClientSettings_com_microsoft_azure_servicebus_security_TokenProvider_com_microsoft_azure_servicebus_primitives_RetryPolicy_java_time_Duration_com_microsoft_azure_servicebus_primitives_TransportType_) sur [com.microsoft.azure.servicebus.primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype). |
| JavaScript  | Lorsque vous créez des objets clients Service Bus, utilisez la propriété `webSocketOptions` dans [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions). |
| Python | Lorsque vous créez des clients Service Bus, définissez [ServiceBusClient.transport_type](/python/api/azure-servicebus/azure.servicebus.servicebusclient) sur [TransportType.AmqpOverWebSocket](/python/api/azure-servicebus/azure.servicebus.transporttype). |

