---
title: Présentation de la messagerie Azure Service Bus | Microsoft Docs
description: Description de la messagerie Service Bus
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: overview
ms.date: 09/22/2018
ms.custom: mvc
ms.author: aschhab
ms.openlocfilehash: 0f3995e8904396dbb0bcbeeea1f993913d68587e
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013116"
---
# <a name="what-is-azure-service-bus"></a>Qu’est-ce qu’Azure Service Bus ?

Microsoft Azure Service Bus est un courtier de messages d’[intégration](https://azure.com/integration) d’entreprise entièrement géré. Service Bus est généralement utilisé pour dissocier des applications et des services, et est une plateforme fiable et sécurisée pour le transfert d’état et de données asynchrones. Les données sont transférées entre différents services et applications à l’aide de *messages*. Un message est au format binaire, qui peut contenir un JSON, un XML, ou juste du texte. 

Les scénarios de messagerie courants sont :

* Messagerie : transfert de données d’entreprise, telles que les ventes ou les bons de commande, les journaux ou les mouvements de stock.
* Découpler les applications : améliorer la fiabilité et l’extensibilité des applications et des services (le client et le service n’ont pas à être en ligne en même temps).
* Rubriques et abonnements : autoriser les relations 1:*n* entre les éditeurs et les abonnés.
* Sessions de messagerie : implémenter des flux de travail qui nécessitent le classement des messages ou le report de message.

## <a name="namespaces"></a>Espaces de noms

Un espace de noms est un conteneur d’étendue pour tous les composants de messagerie. Plusieurs files d’attente et rubriques peuvent résider dans un seul espace de noms, et les espaces de noms servent souvent de conteneurs d’applications.

## <a name="queues"></a>Files d’attente

Les messages sont envoyés vers et reçus depuis les *files d’attente*. Les files d’attente vous permettent de stocker les messages jusqu’à ce que l’application réceptrice soit disponible pour recevoir et traiter ces messages.

![File d'attente](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Les messages dans les files d’attente sont classés et horodatés à l’arrivée. Une fois accepté, le message est conservé en toute sécurité dans un stockage redondant. Les messages sont remis en mode *pull*, qui remet les messages à la demande.

## <a name="topics"></a>Rubriques

Vous pouvez également utiliser des *rubriques* pour envoyer et recevoir des messages. Si une file d’attente est souvent utilisée pour la communication point à point, les rubriques sont utiles dans les scénarios de publication/abonnement.

![Rubrique](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Les rubriques peuvent avoir plusieurs abonnements indépendants. Un abonné à une rubrique peut recevoir une copie de chaque message envoyé à cette rubrique. Les abonnements sont des entités nommées qui sont créées durablement, mais qui peuvent éventuellement expirer ou se supprimer automatiquement.

Dans certains scénarios, vous n’avez pas besoin d’abonnements individuels pour recevoir tous les messages envoyés à une rubrique. Si c’est le cas, vous pouvez utiliser des [règles et des filtres](topic-filters.md) pour définir les conditions qui déclenchent des [actions](topic-filters.md#actions) facultatives, filtrent les messages spécifiques et définissent ou modifient les propriétés des messages.

## <a name="advanced-features"></a>Fonctionnalités avancées

Service Bus a également des fonctionnalités avancées qui vous permettent de résoudre des problèmes de messagerie plus complexes. Les sections suivantes décrivent ces fonctionnalités principales :

### <a name="message-sessions"></a>Sessions de message

Pour garantir l’application de la méthode FIFO (premier entré, premier sorti) dans Service Bus, utilisez Sessions. Les [sessions de messagerie](message-sessions.md) permettent un traitement conjoint et chronologique de séquences illimitées de messages associés. 

### <a name="auto-forwarding"></a>Transfert automatique

La fonctionnalité de [transfert automatique](service-bus-auto-forwarding.md) vous permet de chaîner une file d’attente ou un abonnement à une autre file d’attente ou rubrique qui fait partie du même espace de noms. Lorsque le transfert automatique est activé, Service Bus supprime automatiquement les messages placés dans la première file d’attente ou le premier abonnement (source) pour les placer dans la deuxième file d’attente ou rubrique (destination).

### <a name="dead-lettering"></a>Lettres mortes

Service Bus prend en charge une [file d’attente de lettres mortes](service-bus-dead-letter-queues.md) (DLQ) pour conserver les messages qui ne peuvent pas être remis aux destinataires ou les messages qui ne peuvent pas être traités. Vous pouvez supprimer des messages depuis la DLQ et les inspecter.

### <a name="scheduled-delivery"></a>Remise planifiée

Vous pouvez envoyer des messages dans une file d’attente ou une rubrique [pour les traiter en différé](message-sequencing.md#scheduled-messages), par exemple, si vous avez besoin de planifier un travail de sorte qu’il soit disponible pour être traité par un système à un moment précis.

### <a name="message-deferral"></a>Report de message

Lorsqu’un client de file d’attente ou d’abonnement reçoit un message qu’il souhaite traiter, mais pour lequel le traitement n’est pas possible actuellement en raison de circonstances particulières dans l’application, l’entité a la possibilité de [reporter la récupération du message](message-deferral.md) à plus tard. Le message reste dans la file d’attente ou l’abonnement, mais il est mis de côté.

### <a name="batching"></a>Traitement par lot

[Le traitement par lot côté client](service-bus-performance-improvements.md#client-side-batching) permet à un client de file d’attente ou de rubrique de retarder l’envoi d’un message pendant une période donnée. Si le client envoie des messages supplémentaires pendant cette période, il transmet les messages dans un seul lot. 

### <a name="transactions"></a>Transactions

Une [transaction](service-bus-transactions.md) regroupe deux ou plusieurs opérations dans une étendue d’exécution. Service Bus prend en charge les opérations de regroupement par rapport à une entité de messagerie unique (file d’attente, rubrique, abonnement) dans l’étendue d’une transaction.

### <a name="filtering-and-actions"></a>Filtrage et actions

Les abonnés peuvent définir les messages qu’ils veulent recevoir d’une rubrique. Ces messages sont spécifiés sous la forme d’une ou de plusieurs [règles d’abonnement nommées](topic-filters.md). Pour chaque condition de règle de correspondance, l’abonnement crée une copie du message, qui peut être annotée différemment selon la règle de correspondance utilisée.

### <a name="auto-delete-on-idle"></a>Suppression automatique inactive

[La suppression automatique inactive](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle) vous permet de spécifier un intervalle d’inactivité après lequel la file d’attente est automatiquement supprimée. La durée minimale est de 5 minutes.

### <a name="duplicate-detection"></a>Détection des doublons

Si une erreur se produit et suscite un doute chez le client concernant le résultat d’une opération d’envoi, la [détection des doublons](duplicate-detection.md) lève ce type d’incertitude en permettant à l’expéditeur de renvoyer le même message, et à la file d’attente ou la rubrique d’ignorer les copies en double.

### <a name="sas-rbac-and-managed-identities-for-azure-resources"></a>Protocoles SAP et RBAC, et identités managées pour les ressources Azure

Service Bus prend en charge les protocoles de sécurité tel que les [Signatures d’accès partagé](service-bus-sas.md) (SAP), le [Contrôle d’accès en fonction du rôle](authenticate-application.md) (RBAC) et les [Identités managées pour les ressources Azure](service-bus-managed-service-identity.md).

### <a name="geo-disaster-recovery"></a>Géorécupération d’urgence

Lorsque les régions ou centres de données Azure subissent un temps d’arrêt, la [géo-récupération d’urgence](service-bus-geo-dr.md) permet au traitement des données de continuer à fonctionner dans un autre centre de données ou région.

### <a name="security"></a>Sécurité

Service Bus prend en charge les protocoles [AMQP 1.0](service-bus-amqp-overview.md) et [HTTP/REST](/rest/api/servicebus/).

## <a name="client-libraries"></a>Bibliothèques clientes

Service Bus prend en charge les bibliothèques clientes pour [.NET](https://github.com/Azure/azure-service-bus-dotnet/tree/master), [Java](https://github.com/Azure/azure-service-bus-java/tree/master), et [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client).

## <a name="integration"></a>Intégration

Service Bus intègre complétement les services Azure suivants :

- [Event Grid](https://azure.microsoft.com/services/event-grid/) 
- [Logic Apps](https://azure.microsoft.com/services/logic-apps/) 
- [Fonctions](https://azure.microsoft.com/services/functions/) 
- [Dynamics 365](https://dynamics.microsoft.com)
- [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)
 
## <a name="next-steps"></a>Étapes suivantes

Pour commencer à utiliser la messagerie Service Bus, consultez les articles suivants :

* [Comparer les services de messagerie Azure](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)
* En savoir plus sur les niveaux [Standard et Premium](https://azure.microsoft.com/pricing/details/service-bus/) Azure Service Bus et leur tarification
* [Performances et latence du niveau Premium Azure Service Bus](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722)
* Essayez les Démarrages rapides de [.NET](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md), ou [JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [Gérer les ressources Service Bus avec l'explorateur Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
