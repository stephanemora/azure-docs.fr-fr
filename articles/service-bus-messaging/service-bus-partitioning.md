---
title: Créer des rubriques et des files d’attente Azure Service Bus partitionnées | Microsoft Docs
description: Décrit comment partitionner des files d’attente et des rubriques Service Bus à l’aide de plusieurs courtiers de messages.
ms.topic: article
ms.date: 04/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 36b3437d3f5e8b27f38ca414fc8a4b954226e608
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108136802"
---
# <a name="partitioned-queues-and-topics"></a>Files d’attente et rubriques partitionnées

Azure Service Bus utilise plusieurs courtiers de messages pour traiter les messages et plusieurs banques de messagerie pour stocker les messages. Une file d’attente ou une rubrique classique est gérée par un seul courtier de messages et stockée dans une seule banque de messagerie. Service Bus permet également le *partitionnement* des files d’attente et des rubriques, ou des *entités de messagerie* entre plusieurs courtiers de messages et banques de messagerie. Le partitionnement signifie que le débit global d’une entité partitionnée n’est plus limité par les performances d’un seul courtier de messages ou d’une seule banque de messagerie. En outre, la panne temporaire d’une banque de messagerie ne rend pas une rubrique ou une file d’attente partitionnée indisponible. Les rubriques et les files d’attente partitionnées peuvent contenir toutes les fonctionnalités avancées de Service Bus, comme la prise en charge des transactions et des sessions.

> [!NOTE]
> Le partitionnement est disponible au moment de la création de l’entité pour toutes les files d’attente et rubriques dans les références SKU De base et Standard. Il n’est plus disponible pour la référence SKU de messagerie Premium, mais toutes les entités partitionnées déjà existantes dans les espaces de noms Premium continueront de fonctionner comme prévu.
 
Il n’est pas possible de changer l’option de partitionnement dans une file d’attente ou une rubrique existante ; vous pouvez uniquement définir l’option lorsque vous créez l’entité.

## <a name="how-it-works"></a>Fonctionnement

Chaque file d’attente ou rubrique partitionnée est constituée de plusieurs partitions. Chaque partition est stockée dans une banque de messagerie différente, et gérée par un répartiteur de messages différent. Lorsqu’un message est envoyé à une file d’attente ou une rubrique partitionnée, Service Bus affecte le message à l’une des partitions. La sélection est effectuée au hasard par Service Bus ou à l’aide d’une clé de partition que l’expéditeur peut spécifier.

Lorsqu’un client souhaite recevoir un message à partir d’une file d’attente partitionnée ou d’un abonnement à une rubrique partitionnée, Service Bus interroge toutes les partitions à la recherche de messages, puis retourne au destinataire le premier message qui est obtenu à partir de l’une des banques de messagerie. Service Bus place les autres messages en cache pour les retourner lorsqu’il reçoit plus de requêtes de réception. Un client destinataire n’est pas conscient de ce partitionnement ; le comportement côté client d’une file d’attente ou d’une rubrique partitionnée (par exemple, lecture, exécution, report, rebut, préchargement) est identique à celui d’une entité ordinaire.

L’opération de lecture sur une entité non partitionnée renvoie toujours le message le plus ancien, mais pas sur une entité partitionnée. Au lieu de cela, elle renvoie le message le plus ancien dans l’une des partitions dont le courtier de messages a répondu en premier. Rien ne garantit que le message retourné est le plus ancien sur toutes les partitions. 

Il n’existe aucun coût supplémentaire lors de l’envoi d’un message à, ou lors de la réception d’un message depuis, une file d’attente ou une rubrique partitionnée.

> [!NOTE]
> L’opération de lecture retourne le plus ancien message de la partition en fonction de son numéro de séquence. Pour les entités partitionnées, le numéro de séquence est déterminé par rapport à la partition. Pour plus d’informations, consultez [Timestamps et séquencement de message](../service-bus-messaging/message-sequencing.md).

## <a name="use-of-partition-keys"></a>Utilisation de clés de partition

Lorsqu’un message est placé dans une file d’attente ou une rubrique partitionnée, Service Bus vérifie la présence d’une clé de partition. S’il en trouve une, il sélectionne la partition basée sur cette clé. S’il ne trouve pas de clé de partition, il sélectionne la partition basée sur un algorithme interne.

### <a name="using-a-partition-key"></a>Utilisation d’une clé de partition

Certains scénarios, tels que les sessions ou les transactions, nécessitent que les messages soient stockés dans une partition spécifique. Tous ces scénarios requièrent l’utilisation d’une clé de partition. Tous les messages qui utilisent la même clé de partition sont affectés à la même partition. Si la partition est momentanément indisponible, Service Bus retourne une erreur.

Selon le scénario, différentes propriétés de messages sont utilisées comme clé de partition :

**SessionId** : si la propriété d’ID de session d’un message est définie, Service Bus l’utilise comme clé de partition. De cette façon, tous les messages qui appartiennent à la même session sont gérés par le même courtier de messages. Les sessions permettent à Service Bus de garantir l’ordre des messages, ainsi que la cohérence des états de session.

**PartitionKey** : si un message a la propriété de clé de partition définie mais pas la propriété d’ID de session, alors Service Bus utilise la valeur de la propriété de clé de partition comme clé de partition. Si le message a les deux propriétés d’ID de session et de clé de partition, alors elles doivent être identiques. Si la propriété de clé de partition est définie sur une valeur différente de celle de la propriété d’ID de session, Service Bus retourne une exception d’opération non valide. La propriété de clé de partition doit être utilisée si un expéditeur envoie des messages transactionnels non liés à une session. La clé de partition garantit que tous les messages qui sont envoyés dans une transaction sont gérés par le même courtier de messages.

**MessageId** : si la file d’attente ou la rubrique a été créée avec la [fonctionnalité de détection des doublons](duplicate-detection.md) et que les propriétés d’ID de session et de clé de partition ne sont pas définies, la valeur de la propriété d’ID de message sert de clé de partition. (Les bibliothèques clientes attribuent automatiquement un ID de message si l’application émettrice ne le fait pas.) Dans ce cas, toutes les copies du même message sont gérées par le même courtier de messages. Cet ID permet à Service Bus de détecter et d’éliminer les messages en double. Si la fonctionnalité de détection des doublons n’est pas activée, Service Bus ne considère pas la propriété d’ID de message comme clé de partition.

### <a name="not-using-a-partition-key"></a>Non utilisation d'une clé de partition

En l’absence de clé de partition, Service Bus distribue les messages en tourniquet à toutes les partitions de la rubrique ou de la file d’attente partitionnée. Si la partition choisie n’est pas disponible, Service Bus attribue le message à une partition différente. De cette façon, l'opération d'envoi réussit malgré l'indisponibilité temporaire d'une banque de messagerie. Cependant, vous obtiendrez pas le classement garanti qui est fourni par une clé de partition.

Pour une discussion plus approfondie sur le compromis entre la disponibilité (pas de clé de partition) et la cohérence (en utilisant une clé de partition), consultez [Disponibilité et cohérence dans Event Hubs](../event-hubs/event-hubs-availability-and-consistency.md). À l’exception de l’ID de partition qui n’est pas exposé aux utilisateurs, ces informations s’appliquent également aux entités Service Bus partitionnées.

Afin de donner à Service Bus assez de temps pour placer le message en file d’attente dans une partition différente, la valeur de délai d’attente spécifiée par le client qui envoie le message doit être supérieure à 15 secondes. La valeur par défaut de 60 secondes est recommandée.

Une clé de partition « épingle » un message à une partition spécifique. Si la banque de messagerie qui contient cette partition n’est pas disponible, Service Bus retourne une erreur. En l’absence de clé de partition, Service Bus peut choisir une partition différente et l’opération réussit. Par conséquent, il est recommandé de ne pas fournir de clé de partition, sauf si elle est requise.

## <a name="advanced-topics"></a>Rubriques avancées

### <a name="use-transactions-with-partitioned-entities"></a>Utiliser des transactions avec des entités partitionnées

Les messages envoyés dans le cadre d’une transaction doivent spécifier une clé de partition. La clé peut correspondre à l’une des propriétés suivantes : ID de session, clé de partition ou ID de message. Tous les messages envoyés dans le cadre d'une même transaction doivent spécifier la même clé de partition. Si vous essayez d’envoyer un message sans une clé de partition dans une transaction, Service Bus renvoie une exception d’opération non valide. Si vous essayez d’envoyer plusieurs messages dans la même transaction avec des clés de partition différentes, Service Bus renvoie une exception d’opération non valide. Par exemple :

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

Si les propriétés qui servent de clé de partition sont définies, Service Bus épingle le message à une partition spécifique. Ce comportement se produit qu'une transaction soit utilisée ou non. Il est recommandé de ne pas spécifier de clé de partition si elle n'est pas nécessaire.

### <a name="use-sessions-with-partitioned-entities"></a>Utiliser des sessions avec les entités partitionnées

Pour envoyer un message transactionnel à une file d’attente ou une rubrique utilisant une session, le message doit contenir la propriété d’ID de session définie. Si la propriété de clé de partition est également spécifiée, elle doit être identique à la propriété d’ID de session. Si ces propriétés diffèrent, Service Bus renvoie une exception d’opération non valide.

Contrairement aux files d’attente ou rubriques standard (non partitionnées), il n’est pas possible d’utiliser une transaction unique pour envoyer plusieurs messages à différentes sessions. En cas de tentative, Service Bus renvoie une exception d’opération non valide. Par exemple :

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

### <a name="automatic-message-forwarding-with-partitioned-entities"></a>Transfert automatique des messages avec des entités partitionnées

Service Bus prend en charge le transfert automatique des messages à partir de, vers ou entre les entités partitionnées. Vous pouvez activer cette fonctionnalité lors de la création ou de la mise à jour des files d’attente et abonnements. Pour plus d’informations, consultez [Activer le transfert de messages](enable-auto-forward.md). Si le message spécifie une clé de partition (ID de session, clé de partition ou ID de message), cette clé de partition est utilisée pour l’entité de destination.

## <a name="considerations-and-guidelines"></a>Considérations et recommandations
* **Fonctionnalités de cohérence élevée** : si une entité utilise des fonctionnalités telles que les sessions, la détection des doublons ou le contrôle explicite de la clé de partitionnement, les opérations de messagerie sont toujours acheminées vers des partitions spécifiques. Si le trafic d’une des partitions est élevé, ou si le magasin sous-jacent est défectueux, ces opérations échouent et la disponibilité s’en trouve réduite. En général, la cohérence reste plus importante que le nombre d’entités non partitionnées. Seul un sous-ensemble de trafic rencontre des problèmes, et non le trafic tout en entier. Pour plus d’informations, consultez cette [discussion sur la disponibilité et la cohérence](../event-hubs/event-hubs-availability-and-consistency.md).
* **Gestion** : les opérations, comme la création, la mise à jour et la suppression, doivent être exécutées sur toutes les partitions de l’entité. Si une partition est défectueuse, l’échec de ces opérations peut en résulter. Pour l’opération Get, des informations, telles que le nombre de messages, doivent être agrégées à partir de toutes les partitions. Si une partition est défectueuse, l’état de disponibilité de l’entité est signalé comme étant limité.
* **Scénarios de messages à faible volume** : pour ce type de scénarios, en particulier si vous utilisez le protocole HTTP, vous devrez peut-être effectuer plusieurs opérations de réception afin d'obtenir tous les messages. Pour les demandes de réception, le serveur frontal effectue une opération de réception sur toutes les partitions, et met en cache toutes les réponses reçues. Une demande de réception ultérieure sur la même connexion profiterait de cette mise en cache et on assisterait à une réduction des latences liées à la réception. Toutefois, si vous disposez de plusieurs connexions ou si vous utilisez HTTP, une nouvelle connexion est établie pour chaque demande. Par conséquent, l’accès au même nœud n’est pas garanti. Si tous les messages existants sont verrouillés et mis en cache sur un autre serveur frontal, l’opération de réception renvoie la valeur **null**. Les messages peuvent arriver à expiration et vous pouvez les recevoir à nouveau. La persistance du protocole HTTP est recommandée. Lors de l’utilisation d’un partitionnement dans des scénarios de faible volume, les opérations de réception peuvent prendre plus de temps que prévu. Par conséquent, nous vous recommandons de ne pas utiliser de partitionnement dans ces scénarios. Supprimez toutes les entités partitionnées, puis recréez-les avec le partitionnement désactivé pour améliorer les performances.
* **Parcourir/Afficher l’aperçu des messages**  : cette opération ne renvoie pas toujours le nombre de messages demandé. Il existe deux raisons courantes à ce comportement. L’une des raisons est que la taille agrégée de la collection de messages dépasse la taille maximale de 256 ko. Autre raison, dans les files d’attente ou rubriques partitionnées, une partition peut ne pas avoir suffisamment de messages pour terminer le nombre de messages demandé. En règle générale, si une application veut parcourir/afficher l’aperçu d’un certain nombre de messages, elle doit appeler l’opération d’aperçu à plusieurs reprises jusqu’à obtenir le nombre de messages voulu à moins qu’il n’y ait plus de messages pour lesquels afficher un aperçu. Pour plus d’informations, y compris des exemples de code, consultez [Parcours des messages](message-browsing.md).

## <a name="partitioned-entities-limitations"></a>Limites des entités partitionnées
Actuellement, Service Bus impose les limites suivantes aux files d’attente et rubriques partitionnées :

* Les files d’attente et les rubriques partitionnées ne sont pas prises en charge dans le niveau de messagerie Premium. Les sessions sont prises en charge dans le premier niveau à l’aide de SessionId. 
* Les rubriques et files d’attente partitionnées ne prennent pas en charge l’envoi de messages appartenant à des sessions différentes dans une transaction unique.
* Service Bus permet actuellement de disposer jusqu’à 100 rubriques ou files d’attente par espace de noms. Chaque file d’attente ou rubrique partitionnée est comptabilisée dans le quota de 10 000 entités par espace de noms (non applicable au niveau Premium).

## <a name="other-features"></a>Autres fonctionnalités
- L’ajout ou la suppression de règles est pris en charge avec les entités partitionnées, mais pas dans le cadre des transactions. 
- AMQP est pris en charge pour l’envoi et la réception de messages vers et à partir d’une entité partitionnée.
- AMQP est désormais pris en charge pour les opérations suivantes : Envoi par lots, Réception par lots, Réception par numéro de séquence, Affichage d’aperçu, Verrouillage du renouvellement, Planification des messages, Annulation des messages planifiés, Ajout de règles, Suppression de règles, Verrouillage de renouvellement de session, Définition de l’état d’une session, Obtention de l’état d’une session et Énumération des sessions.



## <a name="next-steps"></a>Étapes suivantes
Vous pouvez activer le partitionnement en utilisant le portail Azure, PowerShell, l’interface CLI, un modèle Resource Manager, .NET, Java, Python et JavaScript. Pour plus d’informations, consultez [Activer le partitionnement](enable-partitions.md). 

Pour en savoir plus sur les concepts fondamentaux de la spécification de la messagerie AMQP 1.0, consultez le [Guide du protocole AMQP 1.0](service-bus-amqp-protocol-guide.md).

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: ./service-bus-amqp-protocol-guide.md
