---
title: Créer des rubriques et des files d’attente Azure Service Bus partitionnées | Microsoft Docs
description: Décrit comment partitionner des files d’attente et des rubriques Service Bus à l’aide de plusieurs courtiers de messages.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 02/06/2019
ms.author: aschhab
ms.openlocfilehash: 699581c7ccd3f36da0cd0c1def623607b7c0a13b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589677"
---
# <a name="partitioned-queues-and-topics"></a>Files d’attente et rubriques partitionnées

Azure Service Bus utilise plusieurs courtiers de messages pour traiter les messages et plusieurs banques de messagerie pour stocker les messages. Une file d’attente ou une rubrique classique est gérée par un seul courtier de messages et stockée dans une seule banque de messagerie. Service Bus permet également le *partitionnement* des files d’attente et des rubriques, ou des *entités de messagerie* entre plusieurs courtiers de messages et banques de messagerie. Le partitionnement signifie que le débit global d’une entité partitionnée n’est plus limité par les performances d’un seul courtier de messages ou d’une seule banque de messagerie. En outre, la panne temporaire d’une banque de messagerie ne rend pas une rubrique ou une file d’attente partitionnée indisponible. Les rubriques et les files d’attente partitionnées peuvent contenir toutes les fonctionnalités avancées de Service Bus, comme la prise en charge des transactions et des sessions.

> [!NOTE]
> Le partitionnement est disponible au moment de la création de l’entité pour toutes les files d’attente et rubriques dans les références SKU De base et Standard. Il n’est pas disponible pour la référence SKU de messagerie Premium, mais toutes les entités partitionnées déjà existantes dans les espaces de noms Premium continuent de fonctionner comme prévu.
 
Il n’est pas possible de changer l’option de partitionnement dans une file d’attente ou une rubrique existante ; vous pouvez uniquement définir l’option quand vous créez l’entité.

## <a name="how-it-works"></a>Fonctionnement

Chaque file d’attente partitionnée ou une rubrique se compose de plusieurs partitions. Chaque partition est stockée dans une banque de messagerie différente et gérée par un courtier de messages différents. Lorsqu’un message est envoyé à une file d’attente partitionnée ou une rubrique, Service Bus affecte le message à une des partitions. La sélection est effectuée au hasard par Service Bus ou à l’aide d’une clé de partition que l’expéditeur peut spécifier.

Lorsqu’un client souhaite recevoir un message à partir d’une file d’attente partitionnée ou à partir d’un abonnement à une rubrique partitionnée, Service Bus interroge toutes les partitions pour les messages, puis retourne le premier message qui est obtenu à partir des banques de messagerie au destinataire. Service Bus place les autres messages en cache pour les retourner quand il reçoit d'autres requêtes de réception. Un client destinataire n’est pas conscient de ce partitionnement ; le comportement côté client d’une file d’attente ou d’une rubrique partitionnée (par exemple, lecture, exécution, report, rebut, préchargement) est identique à celui d’une entité ordinaire.

Il n’existe aucun coût supplémentaire lors de l’envoi d’un message à, ou lors de la réception d’un message depuis, une file d’attente ou une rubrique partitionnée.

## <a name="enable-partitioning"></a>Activation du partitionnement

Pour utiliser des rubriques et des files d’attente partitionnées avec Azure Service Bus, utilisez le Kit de développement logiciel (SDK) Microsoft Azure version 2.2 ou version ultérieure, ou spécifiez `api-version=2013-10` ou une version ultérieure dans vos requêtes HTTP.

### <a name="standard"></a>standard

Dans le niveau de messagerie Standard, vous pouvez créer des files d’attente et des rubriques Service Bus avec des tailles de 1, 2, 3, 4 ou 5 Go (la valeur par défaut est 1 Go). Le partitionnement est activé, Service Bus crée 16 copies (16 partitions) de l’entité, chacun de la même taille spécifiée. Par conséquent, si vous créez une file d’attente de 5 Go, avec 16 partitions, la taille maximale de la file d’attente est (5 \* 16) = 80 Go. Vous pouvez voir la taille maximale de votre file d’attente ou rubrique partitionnée en examinant son entrée sur le [portail Azure][Azure portal], dans le panneau **Aperçu** de cette entité.

### <a name="premium"></a>Premium

Dans un espace de noms de niveau Premium, les entités de partitionnement ne sont pas prises en charge. Toutefois, vous pouvez quand même créer des files d’attente et des rubriques Service Bus avec des tailles de 1, 2, 3, 4, 5, 10, 20, 40 ou 80 Go (la valeur par défaut est 1 Go). Vous pouvez voir la taille de votre file d’attente ou rubrique en examinant son entrée sur le [portail Azure][Azure portal], dans le panneau **Aperçu** de cette entité.

### <a name="create-a-partitioned-entity"></a>Créer une entité partitionnée

Il existe plusieurs façons de créer une file d’attente ou une rubrique partitionnée. Lorsque vous créez la file d’attente ou la rubrique à partir de votre application, vous pouvez activer le partitionnement de la file d’attente ou de la rubrique en définissant respectivement la propriété [QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] ou [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] sur **true**. Ces propriétés doivent être définies au moment de la création de la file d’attente ou de la rubrique et sont disponibles uniquement dans l’ancienne bibliothèque [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/). Comme indiqué précédemment, il n’est pas possible de modifier ces propriétés dans une file d’attente ou une rubrique existante. Par exemple : 

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Vous pouvez également créer une file d’attente ou une rubrique partitionnée sur le [portail Azure][Azure portal]. Lorsque vous créez une file d’attente ou une rubrique sur le portail, l’option **Activer le partitionnement** dans la boîte de dialogue **Créer** de la file d’attente ou de la rubrique est activée par défaut. Vous pouvez uniquement désactiver cette option dans une entité de niveau Standard ; dans le niveau Premium, le partitionnement n’est pas pris en charge et la case à cocher est sans effet. 

## <a name="use-of-partition-keys"></a>Utilisation de clés de partition

Lorsqu’un message est placé dans une file d’attente ou une rubrique partitionnée, Service Bus vérifie la présence d’une clé de partition. S’il en trouve, il sélectionne la partition basée sur cette clé. S’il ne trouve pas une clé de partition, il sélectionne la partition selon un algorithme interne.

### <a name="using-a-partition-key"></a>Utilisation d’une clé de partition

Certains scénarios, tels que les sessions ou les transactions, exiger que les messages soient stockés dans une partition spécifique. Tous ces scénarios requièrent l’utilisation d’une clé de partition. Tous les messages qui utilisent la même clé de partition sont affectés à la même partition. Si la partition est temporairement indisponible, Service Bus renvoie une erreur.

Selon le scénario, différentes propriétés de messages sont utilisées comme clé de partition :

**SessionId** : si un message est configuré par la propriété [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), Service Bus utilise **SessionID** comme clé de partition. De cette façon, tous les messages qui appartiennent à la même session sont gérés par le même courtier de messages. Les sessions permettent à Service Bus de garantir l’ordre des messages, ainsi que la cohérence des états de session.

**PartitionKey** : si un message est configuré avec la propriété [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) mais pas avec la propriété [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), Service Bus utilise la valeur de la propriété [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) comme clé de partition. Si le message a les deux propriétés [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) et [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) définies, alors elles doivent être identiques. Si la propriété [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) est définie sur une valeur différente de celle de la propriété [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), Service Bus retourne une exception d’opération non valide. La propriété [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) doit être utilisée si un expéditeur envoie des messages transactionnels non liés à une session. La clé de partition garantit que tous les messages qui sont envoyés dans une transaction sont gérés par le même courtier de messages.

**MessageId** : si la file d'attente ou la rubrique est configurée avec la propriété [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) définie sur **true** et que les propriétés [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) ou [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) ne sont pas définies, la valeur de la propriété [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) fait office de clé de partition. (Les bibliothèques Microsoft .NET et AMQP attribuent automatiquement un ID de message si l’application émettrice ne le fait pas.) Dans ce cas, toutes les copies du même message sont gérées par le même courtier de messages. Cet ID permet à Service Bus de détecter et d’éliminer les messages en double. Si la propriété [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) n’est pas définie sur **true**, Service Bus ne considère pas la propriété [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) comme clé de partition.

### <a name="not-using-a-partition-key"></a>Non utilisation d'une clé de partition

En l’absence d’une clé de partition, Service Bus distribue les messages en tourniquet à toutes les partitions de la file d’attente partitionnée ou la rubrique. Si la partition choisie n’est pas disponible, Service Bus affecte le message à une autre partition. De cette façon, l'opération d'envoi réussit malgré l'indisponibilité temporaire d'une banque de messagerie. Cependant, vous obtiendrez pas le classement garanti qui est fourni par une clé de partition.

Pour une discussion plus approfondie sur le compromis entre la disponibilité (pas de clé de partition) et la cohérence (en utilisant une clé de partition), consultez [cet article](../event-hubs/event-hubs-availability-and-consistency.md). Ces informations s’appliquent également aux entités Service Bus partitionnées.

Pour donner à Service Bus assez de temps en file d’attente le message dans une partition différente, le [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) valeur spécifiée par le client qui envoie le message doit être supérieur à 15 secondes. Il est recommandé de définir la propriété [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) sur la valeur par défaut de 60 secondes.

Une clé de partition « épingle » un message à une partition spécifique. Si la banque de messagerie qui détient cette partition n’est pas disponible, Service Bus renvoie une erreur. En l’absence d’une clé de partition, Service Bus peut choisir une autre partition et l’opération réussit. Par conséquent, il est recommandé de ne pas fournir de clé de partition, sauf si elle est requise.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Rubriques avancées : utiliser des transactions avec des entités partitionnées

Les messages envoyés dans le cadre d’une transaction doivent spécifier une clé de partition. La clé peut être l'une des propriétés suivantes : [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) ou [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid). Tous les messages envoyés dans le cadre d'une même transaction doivent spécifier la même clé de partition. Si vous essayez d’envoyer un message sans une clé de partition dans une transaction, Service Bus renvoie une exception d’opération non valide. Si vous essayez d’envoyer plusieurs messages dans la même transaction avec des clés de partition différentes, Service Bus renvoie une exception d’opération non valide. Par exemple : 

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

Si les propriétés qui servent à une clé de partition sont définies, Service Bus épingle le message à une partition spécifique. Ce comportement se produit qu'une transaction soit utilisée ou non. Il est recommandé de ne pas spécifier de clé de partition si elle n'est pas nécessaire.

## <a name="using-sessions-with-partitioned-entities"></a>Utilisation de sessions avec les entités partitionnées

Pour envoyer un message transactionnel à une file d’attente ou une rubrique utilisant une session, le message doit contenir la propriété [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) définie. Si la propriété [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) est également spécifiée, elle doit être identique à la propriété [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid). Si ces propriétés diffèrent, Service Bus renvoie une exception d’opération non valide.

Contrairement aux files d’attente ou rubriques standard (non partitionnées), il n’est pas possible d’utiliser une transaction unique pour envoyer plusieurs messages à différentes sessions. En cas de tentative, Service Bus renvoie une exception d’opération non valide. Par exemple : 

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Transfert automatique des messages avec des entités partitionnées

Service Bus prend en charge le transfert automatique des messages à partir de, vers ou entre les entités partitionnées. Pour activer le transfert automatique des messages, définissez la propriété [QueueDescription.ForwardTo][QueueDescription.ForwardTo] de la file d’attente ou de l’abonnement source. Si le message spécifie une clé de partition ([SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) ou [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)), cette clé de partition est utilisée pour l’entité de destination.

## <a name="considerations-and-guidelines"></a>Considérations et recommandations
* **Fonctionnalités de cohérence élevée** : Si une entité utilise des fonctionnalités telles que les sessions, détection des doublons ou un contrôle explicite de clé de partitionnement, puis les opérations de messagerie sont toujours acheminées vers une partition spécifique. Si chacune des partitions de rencontrer un trafic élevé ou le magasin sous-jacent n’est pas intègre, ces opérations échouent et la disponibilité est réduite. En général, la cohérence reste plus importante que le nombre d’entités non partitionnées. Seul un sous-ensemble de trafic rencontre des problèmes, et non le trafic tout en entier. Pour plus d’informations, consultez cette [discussion sur la disponibilité et la cohérence](../event-hubs/event-hubs-availability-and-consistency.md).
* **Gestion** : Opérations telles que Create, Update et Delete doivent être effectuées sur toutes les partitions de l’entité. Si n’importe quelle partition est défectueuse, cela peut entraîner d’échec de ces opérations. Pour l’opération d’obtention des informations telles que le nombre de messages doivent être agrégées à partir de toutes les partitions. Si n’importe quelle partition est défectueuse, l’état de disponibilité d’entité est signalé comme étant limité.
* **Scénarios de messages à faible volume** : pour ce type de scénarios, en particulier si vous utilisez le protocole HTTP, vous devrez peut-être effectuer plusieurs opérations de réception afin d'obtenir tous les messages. Pour les demandes de réception, le serveur frontal effectue une opération de réception sur toutes les partitions et met en cache toutes les réponses reçues. Une demande de réception ultérieure sur la même connexion profiterait de cette mise en cache et on assisterait à une réduction des latences liées à la réception. Toutefois, si vous disposez de plusieurs connexions ou si vous utilisez HTTP, une nouvelle connexion est établie pour chaque demande. Par conséquent, l’accès au même nœud n’est pas garanti. Si tous les messages existants sont verrouillés et mis en cache sur un autre serveur frontal, l’opération de réception renvoie la valeur **null**. Les messages peuvent arriver à expiration et vous pouvez les recevoir à nouveau. La persistance du protocole HTTP est recommandée.
* **Parcourir/Lire furtivement les messages** : uniquement disponible dans l'ancienne bibliothèque [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/). [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) ne retourne pas toujours le nombre de messages spécifié dans la propriété [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount). Il existe deux raisons courantes à ce comportement. L’une des raisons est que la taille agrégée de la collection de messages dépasse la taille maximale de 256 ko. Une autre raison est que si la [propriété EnablePartitioning](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) de la file d’attente ou de la rubrique est définie sur **true**, il se peut qu’une partition ne dispose pas de suffisamment de messages pour terminer le nombre de messages demandé. En règle générale, si une application veut recevoir un certain nombre de messages, elle doit appeler [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) à plusieurs reprises jusqu’à obtenir le nombre de messages voulu à moins qu’il n’y ait plus de messages pour lesquels afficher un aperçu. Pour plus d’informations, y compris pour obtenir des exemples de code, consultez la documentation d’API [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) ou [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch).

## <a name="latest-added-features"></a>Dernières fonctionnalités ajoutées

* L’ajout ou la suppression de règles est désormais pris en charge avec les entités partitionnées. À la différence des entités non partitionnée, ces opérations ne sont pas prises en charge dans le cadre des transactions. 
* AMQP est désormais pris en charge pour l’envoi et la réception de messages vers et à partir d’une entité partitionnée.
* AMQP est désormais pris en charge pour les opérations suivantes : [Envoi par lots](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), [Réception par lots](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), [Réception par numéro de séquence](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [Affichage d'aperçu](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [Verrouillage du renouvellement](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [Planification des messages](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), [Annulation des messages planifiés](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), [Ajout de règles](/dotnet/api/microsoft.azure.servicebus.ruledescription), [Suppression de règles](/dotnet/api/microsoft.azure.servicebus.ruledescription), [Verrouillage de renouvellement de session](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), [Définition de l'état d'une session](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), [Obtention de l'état d'une session](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate) et [Énumération des sessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Limites des entités partitionnées

Actuellement, Service Bus impose les limites suivantes aux files d’attente et rubriques partitionnées :

* Les files d’attente et les rubriques partitionnées ne sont pas prises en charge dans le niveau de messagerie Premium. Les sessions sont prises en charge dans le premier niveau à l’aide de SessionId. 
* Les rubriques et files d’attente partitionnées ne prennent pas en charge l’envoi de messages appartenant à des sessions différentes dans une transaction unique.
* Service Bus permet actuellement de disposer jusqu’à 100 rubriques ou files d’attente par espace de noms. Chaque file d’attente ou rubrique partitionnée est comptabilisée dans le quota de 10 000 entités par espace de noms (non applicable au niveau Premium).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les concepts fondamentaux de la spécification de la messagerie AMQP 1.0, consultez le [Guide du protocole AMQP 1.0](service-bus-amqp-protocol-guide.md).

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
