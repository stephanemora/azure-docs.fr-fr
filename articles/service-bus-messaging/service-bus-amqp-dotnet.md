---
title: Utiliser la bibliothèque .NET Framework WindowsAzure.ServiceBus héritée avec AMQP 1.0 | Microsoft Docs
description: Cet article explique comment utiliser la bibliothèque .NET Framework WindowsAzure.ServiceBus héritée avec AMQP (Advanced Messaging Queuing Protocol).
ms.topic: article
ms.date: 04/30/2021
ms.openlocfilehash: 160da6a770e58e9a76e966f018d25dc9bc8a8c76
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108770062"
---
# <a name="use-legacy-windowsazureservicebus-net-framework-library-with-amqp-10"></a>Utiliser la bibliothèque .NET Framework WindowsAzure.ServiceBus héritée avec AMQP 1.0

> [!NOTE]
> Cet article s’adresse aux utilisateurs du package WindowsAzure.ServiceBus qui cherchent à passer à l’utilisation d’AMQP dans le même package. Même si ce package continuera de recevoir des correctifs de bogues critiques, nous vous encourageons vivement à effectuer plutôt une mise à niveau vers le nouveau package [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus), qui est disponible depuis novembre 2020 et qui prend en charge AMQP par défaut.

Par défaut, le package WindowsAzure.ServiceBus communique avec le service Service Bus à l’aide d’un protocole SOAP dédié nommé SBMP (Service Bus Messaging Protocol). Dans la version 2.1, la prise en charge d’AMQP 1.0 a été ajoutée. Nous vous recommandons de l’utiliser plutôt que le protocole par défaut.

Pour utiliser AMQP 1.0 au lieu du protocole par défaut, vous avez besoin d’une configuration explicite sur la chaîne de connexion Service Bus, ou dans les constructeurs clients par le biais de l’option [TransportType](/dotnet/api/microsoft.servicebus.messaging.transporttype). À l'exception de cette modification, le code de l'application reste inchangé lors de l'utilisation d'AMQP 1.0.

Quelques fonctionnalités d’API ne sont pas prises en charge lors de l’utilisation d’AMQP. Ces fonctionnalités non prises en charge sont répertoriées dans la section [Différences de comportement](#behavioral-differences). Certains paramètres de configuration avancés ont également une signification différente lors de l’utilisation d’AMQP.

## <a name="configure-connection-string-to-use-amqp-10"></a>Configurer une chaîne de connexion pour utiliser AMQP 1.0

Ajoutez votre chaîne de connexion avec `;TransportType=Amqp` pour indiquer au client d’établir sa connexion à Service Bus à l’aide d’AMQP 1.0.
Par exemple, 

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Où les éléments `namespace` et `SAS key` sont obtenus à partir du [portail Azure][Azure portal] lorsque vous créez un espace de noms Service Bus. Pour plus d’informations, consultez [Créer un espace de noms Service Bus à l’aide du portail Azure][Create a Service Bus namespace using the Azure portal].

### <a name="amqp-over-websockets"></a>AMQP sur WebSockets
Pour utiliser AMQP sur WebSockets, définissez `TransportType` dans la chaîne de connexion sur `AmqpWebSockets`. Par exemple : `Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=AmqpWebSockets`. 

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

Il existe quelques petites différences dans le comportement de l’API WindowsAzure.ServiceBus lors de l’utilisation d’AMQP par rapport au protocole par défaut :

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
[Azure portal]: https://portal.azure.com
[Vue d’ensemble du protocole AMQP de Service Bus]: service-bus-amqp-overview.md
[Guide du protocole AMQP 1.0]: service-bus-amqp-protocol-guide.md
