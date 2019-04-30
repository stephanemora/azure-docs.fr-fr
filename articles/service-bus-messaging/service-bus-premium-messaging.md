---
title: Vue d’ensemble des niveaux tarifaires de messagerie Azure Service Bus Premium et Standard | Microsoft Docs
description: Couches messagerie Service Bus Premium et Standard
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: aschhab
ms.openlocfilehash: 478446c834f5e6c811d78828868ba12a90e603ac
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125829"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Couches messagerie Service Bus Premium et Standard

La messagerie Service Bus, qui comprend des entités telles que les files d’attente et les rubriques, associe des fonctionnalités de messagerie d’entreprise à une sémantique riche de publication et d’abonnement à l’échelle du cloud. La messagerie Service Bus sert d’infrastructure de communication à de nombreuses solutions cloud sophistiquées.

La couche *Premium* de la messagerie Service Bus répond aux demandes couramment formulées par les clients concernant la mise à l’échelle, les performances et la disponibilité de leurs applications critiques. Le niveau Premium est recommandé pour les scénarios de production. Bien qu’offrant des ensembles de fonctionnalités quasiment identiques, les deux couches de messagerie Service Bus sont conçues pour différents cas de figure.

Les principales différences sont répertoriées dans le tableau suivant.

| Premium | Standard |
| --- | --- |
| Débit élevé |Débit variable |
| Performances prévisibles |Latence variable |
| Prix fixe |Tarification à l’utilisation variable |
| Possibilité de faire évoluer la charge de travail |N/A |
| Taille de message maximale de 1 Mo |Taille de message maximale de 256 Ko |

La **messagerie Service Bus Premium** isole les ressources au niveau processeur et mémoire, ce qui permet d’exécuter chaque charge de travail client de manière isolée. Ce conteneur de ressources est appelé *unité de messagerie*. Au moins une unité de messagerie est allouée à chaque espace de noms premium. Vous pouvez acheter une, deux ou quatre unités de messagerie pour chaque espace de noms Service Bus Premium. Une charge de travail unique ou une entité peut s’étendre sur plusieurs unités de messagerie et le nombre d’unités de messagerie peut être modifié à volonté. Au final, les performances de votre solution Service Bus sont prévisibles et répétables.

Au final, les performances de votre solution Service Bus sont non seulement prévisibles et répétables, mais aussi supérieures. La messagerie Service Bus Premium s’appuie sur le moteur de stockage introduit dans [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). La messagerie Premium offre des performances de pointe bien supérieures à celles de la couche Standard.

## <a name="premium-messaging-technical-differences"></a>Différences techniques de la messagerie Premium

Les sections suivantes décrivent les quelques différences entre les couches de messagerie Standard et Premium.

### <a name="partitioned-queues-and-topics"></a>Files d’attente et rubriques partitionnées

Les files d’attente et les rubriques partitionnées ne sont pas prises en charge dans la messagerie Premium. Pour plus d’informations sur le partitionnement, voir [Files d’attentes et rubriques partitionnées](service-bus-partitioning.md).

### <a name="express-entities"></a>Entités Express

La messagerie Premium s’exécutant dans un environnement d’exécution complètement isolé, les entités express ne sont pas prises en charge dans les espaces de noms Premium. Pour en savoir plus sur la fonctionnalité express, consultez la propriété [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Si vous avez du code s’exécutant dans la messagerie Standard et que vous souhaitez le faire passer au niveau Premium, vérifiez que la propriété [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) est définie sur **false** (la valeur par défaut).

## <a name="premium-messaging-resource-usage"></a>Utilisation des ressources Premium Messaging
En règle générale, toute opération sur une entité peut entraîner l’utilisation du processeur et mémoire. Voici quelques-unes de ces opérations : 

- Opérations de gestion comme CRUD (Create, Retrieve, Update et Delete) des opérations sur les files d’attente, rubriques et abonnements.
- Opérations d’exécution (envoyer et recevoir des messages)
- Opérations d’analyse et alertes

L’utilisation de mémoire et du processeur supplémentaire n'est pas facturée en outre cependant. Pour le niveau de la messagerie Premium, il existe un prix unique pour l’unité de message.

L’utilisation du processeur et mémoire sont suivies et vous s’affichent pour les raisons suivantes : 

- Fournit la transparence dans les profondeurs de système
- Comprendre la capacité des ressources achetées.
- Planification de la capacité vous permet de décider à l’échelle /.

## <a name="get-started-with-premium-messaging"></a>Prise en main de Premium Messaging

La prise en main de Premium Messaging est immédiate ; le processus est similaire à celui de la messagerie Standard. Commencez par [créer un espace de noms](service-bus-create-namespace-portal.md) dans le [portail Azure](https://portal.azure.com). Veillez à sélectionner **Premium** sous **Niveau de tarification**. Cliquez sur **Afficher tous les détails de la tarification** pour voir plus d’informations sur chaque niveau.

![create-premium-namespace][create-premium-namespace]

Vous pouvez également créer des [espaces de noms Premium à l’aide de modèles Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la messagerie Service Bus, consultez les liens suivants :

* [Introducing Azure Service Bus Premium Messaging (Présentation de la messagerie Azure Service Bus Premium (billet de blog))](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introducing Azure Service Bus Premium Messaging (Présentation de la messagerie Azure Service Bus Premium (Channel9))](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Service Bus messaging: flexible data delivery in the cloud (Messagerie Service Bus : service flexible de distribution de données dans le cloud)](service-bus-messaging-overview.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
