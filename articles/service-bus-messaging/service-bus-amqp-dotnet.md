---
title: Azure Service Bus avec .NET et AMQP 1.0 | Microsoft Docs
description: Cet article explique comment utiliser Azure Service Bus à partir d’une application .NET avec AMQP (Advanced Messaging Queuing Protocol).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 20800363327aefda073cd484dc737b28e60466a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632848"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>Utiliser Service Bus à partir de .NET avec AMQP 1.0

La prise en charge d’AMQP 1.0 est disponible dans la version 2.1 du package Service Bus ou supérieure. Vous pouvez vérifier que vous disposez de la dernière version en téléchargeant les éléments Service Bus à partir de [NuGet][NuGet].

> [!NOTE]
> Vous pouvez utiliser le protocole Advance Message Queueing Protocol (AMQP) ou Service Bus Messaging Protocol (SBMP) avec la bibliothèque .NET pour Service Bus. AMQP est le protocole par défaut utilisé par la bibliothèque .NET. Nous vous recommandons d’utiliser le protocole AMQP (qui est la valeur par défaut) et de ne pas le remplacer. 

## <a name="configure-net-applications-to-use-amqp-10"></a>Configurer des applications .NET pour utiliser AMQP 1.0

Par défaut, la bibliothèque cliente .NET Service Bus communique avec le Service Bus à l’aide du protocole AMQP. Vous pouvez également spécifier explicitement AMQP comme type de transport, comme indiqué dans la section suivante. 

Dans la version actuelle, quelques fonctionnalités de l’API ne sont pas prises en charge lors de l’utilisation d’AMQP. Ces fonctionnalités non prises en charge sont répertoriées dans la section [Différences de comportement](#behavioral-differences). Certains paramètres de configuration avancés ont également une signification différente lors de l’utilisation d’AMQP.

### <a name="configuration-using-appconfig"></a>Configuration à l’aide d’App.config

Une bonne pratique pour les applications est d’utiliser le fichier de configuration App.config afin de stocker les paramètres. Pour les applications Service Bus, vous pouvez utiliser App.config pour stocker la chaîne de connexion Service Bus. Voici un exemple de fichier App.config :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

La valeur du paramètre `Microsoft.ServiceBus.ConnectionString` est la chaîne de connexion Service Bus utilisée pour configurer la connexion à Service Bus. au format suivant :

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Où les éléments `namespace` et `SAS key` sont obtenus à partir du [portail Azure][Azure portal] lorsque vous créez un espace de noms Service Bus. Pour plus d’informations, consultez [Créer un espace de noms Service Bus à l’aide du portail Azure][Create a Service Bus namespace using the Azure portal].

Lorsque vous utilisez AMQP, ajoutez la chaîne de connexion avec `;TransportType=Amqp`. Cette notation informe la bibliothèque cliente qu’elle doit se connecter à Service Bus à l’aide d’AMQP 1.0.

### <a name="amqp-over-websockets"></a>AMQP sur WebSockets
Pour utiliser AMQP sur WebSockets, définissez `TransportType` dans la chaîne de connexion sur `AmqpWebSockets`. Par exemple : `Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=AmqpWebSockets`. 

Si vous utilisez la bibliothèque .NET Microsoft.Azure.ServiceBus, définissez [ServiceBusConnection.TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) sur AmqpWebSockets de l’[enum TransportType](/dotnet/api/microsoft.azure.servicebus.transporttype).

Si vous utilisez la bibliothèque .NET Azure.Messaging.ServiceBus, définissez [ServiceBusClient.TransportType](/dotnet/api/azure.messaging.servicebus.servicebusclient.transporttype) sur AmqpWebSockets de l’[enum ServiceBusTransportType](/dotnet/api/azure.messaging.servicebus.servicebustransporttype).


## <a name="message-serialization"></a>Sérialisation de messages

Lorsque vous utilisez le protocole par défaut, le comportement de sérialisation par défaut de la bibliothèque cliente .NET consiste à utiliser le type [DataContractSerializer][DataContractSerializer] pour sérialiser une instance [BrokeredMessage][BrokeredMessage] en vue de son transport entre la bibliothèque cliente et le service Service Bus. Lorsque vous utilisez le mode de transport AMQP, la bibliothèque cliente utilise le système de type AMQP pour la sérialisation du [message réparti][BrokeredMessage] dans un message AMQP. Cette sérialisation permet au message d’être reçu et interprété par une application réceptrice potentiellement exécutée sur une plateforme différente, par exemple, une application Java qui utilise l’API JMS pour accéder à Service Bus.

Lorsque vous construisez une instance [BrokeredMessage][BrokeredMessage], vous pouvez fournir un objet .NET en tant que paramètre au constructeur pour servir de corps au message. Pour les objets qui peuvent être mappés sur des types primitifs AMQP, le corps est sérialisé en types de données AMQP. Si l’objet ne peut pas être directement mappé sur un type primitif AMQP, c’est-à-dire un type personnalisé défini par l’application, l’objet est sérialisé à l’aide de [DataContractSerializer][DataContractSerializer] et les octets sérialisés sont envoyés dans un message de données AMQP.

Pour faciliter l’interopérabilité avec les clients autres que .NET, utilisez uniquement des types .NET qui peuvent être sérialisés directement en types AMQP pour le corps du message. Le tableau suivant détaille ces types et le mappage correspondant sur le système de types AMQP.

| Type d’objet de corps .NET | Type AMQP mappé | Type de section de corps AMQP |
| --- | --- | --- |
| bool |boolean |Valeur AMQP |
| byte |ubyte |Valeur AMQP |
| ushort |ushort |Valeur AMQP |
| uint |uint |Valeur AMQP |
| ulong |ulong |Valeur AMQP |
| sbyte |byte |Valeur AMQP |
| short |short |Valeur AMQP |
| int |int |Valeur AMQP |
| long |long |Valeur AMQP |
| float |float |Valeur AMQP |
| double |double |Valeur AMQP |
| Décimal |decimal128 |Valeur AMQP |
| char |char |Valeur AMQP |
| DateTime |timestamp |Valeur AMQP |
| Guid |uuid |Valeur AMQP |
| byte[] |binary |Valeur AMQP |
| string |string |Valeur AMQP |
| System.Collections.IList |list |Valeur AMQP : les éléments contenus dans la collection peuvent uniquement être ceux qui sont définis dans ce tableau. |
| System.Array |tableau |Valeur AMQP : les éléments contenus dans la collection peuvent uniquement être ceux qui sont définis dans ce tableau. |
| System.Collections.IDictionary |map |Valeur AMQP : les éléments contenus dans la collection peuvent uniquement être ceux qui sont définis dans ce tableau. Remarque : seules les clés de chaîne sont prises en charge. |
| Uri |Valeur string décrite (voir le tableau suivant) |Valeur AMQP |
| DateTimeOffset |Valeur long décrite (voir le tableau suivant) |Valeur AMQP |
| TimeSpan |Valeur long décrite (voir la suite) |Valeur AMQP |
| STREAM |binary |Données AMQP (peuvent être multiples). Les sections de données contiennent les octets bruts lus à partir de l’objet Stream. |
| Objet Other |binary |Données AMQP (peuvent être multiples). Contient le binaire sérialisé de l’objet qui utilise DataContractSerializer ou un sérialiseur fourni par l’application. |

| Type .NET | Type décrit AMQP mappé | Notes |
| --- | --- | --- |
| Uri |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |TimeSpan.Ticks |

## <a name="behavioral-differences"></a>Différences de comportement

Il existe quelques petites différences dans le comportement de l’API .NET Service Bus lors de l’utilisation d’AMQP par rapport au protocole par défaut :

* La propriété [OperationTimeout][OperationTimeout] est ignorée.
* `MessageReceiver.Receive(TimeSpan.Zero)` est implémenté en tant que `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.
* Seuls les destinataires du message qui ont initialement reçu les messages peuvent terminer des messages par des lock-tokens.

## <a name="control-amqp-protocol-settings"></a>Contrôler les paramètres de protocole AMQP

Les [API .NET](/dotnet/api/) exposent plusieurs paramètres pour contrôler le comportement du protocole AMQP :

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**  : Contrôle le crédit initial appliqué à un lien. La valeur par défaut est 0.
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**  : Contrôle la taille de trame AMQP maximale fournie durant la négociation lors de l’ouverture de la connexion. La valeur par défaut est 65 536 octets.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**  : Si les transferts sont exécutables par lots, cette valeur détermine le délai maximal pour l’envoi des dispositions. Héritée par les expéditeurs/destinataires par défaut. Un expéditeur/destinataire individuel peut remplacer la valeur par défaut, qui est de 20 millisecondes.
* **[MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**  : Contrôle si les connexions AMQP sont établies via une connexion TLS. La valeur par défaut est **true**.

## <a name="next-steps"></a>Étapes suivantes

Prêt à en savoir plus ? Visitez les liens suivants :

* [Vue d’ensemble du protocole AMQP de Service Bus]
* [Guide du protocole AMQP 1.0]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: /dotnet/api/system.runtime.serialization.datacontractserializer
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: https://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Vue d’ensemble du protocole AMQP de Service Bus]: service-bus-amqp-overview.md
[Guide du protocole AMQP 1.0]: service-bus-amqp-protocol-guide.md