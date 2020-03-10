---
title: Messagerie Azure Service Bus - files d’attente, rubriques et abonnements
description: Cet article fournit une vue d’ensemble des entités de messagerie Azure Service Bus (file d’attente, rubriques et abonnements).
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/16/2020
ms.author: aschhab
ms.openlocfilehash: 3dc78a22e0e596d812d90fec63475a0b21e9164f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357067"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Files d’attente, rubriques et abonnements Service Bus

Microsoft Azure Service Bus prend en charge un ensemble de technologies middleware Cloud orientées messages, notamment la mise en file d’attente de messages fiable et la messagerie de publication/abonnement durable. Ces fonctionnalités de messagerie répartie peuvent être considérées comme des fonctions de messagerie découplée prenant en charge la publication-abonnement, le découplage temporel et les scénarios d’équilibrage de charge qui utilisent la charge de travail de messagerie Service Bus. La communication découplée présente de nombreux avantages ; par exemple, les clients et les serveurs peuvent se connecter en fonction des besoins et exécuter leurs opérations de manière asynchrone.

Les entités de messagerie qui constituent l’essentiel des fonctionnalités de messagerie dans Service Bus sont les files d’attente, les rubriques et abonnements, les règles et les actions.

## <a name="queues"></a>Files d’attente

Les files d’attente permettent la livraison de messages sur le principe du *premier entré, premier sorti (FIFO)* à un ou plusieurs destinataires concurrents. Autrement dit, les messages sont en général reçus et traités par les destinataires dans l’ordre dans lequel ils ont été ajoutés à la file d’attente ; chaque message est reçu et traité par un seul consommateur de message. Un des principaux avantages de l’utilisation des files d’attente consiste à réaliser le « découplage temporel » des composants d’application. En d’autres termes, les producteurs (expéditeurs) et les consommateurs (récepteurs) ne sont pas forcés d’envoyer et de recevoir des messages simultanément, car les messages sont stockés durablement dans la file d’attente. En outre, le producteur n’a pas à attendre de réponse du destinataire pour continuer de traiter et d’envoyer d’autres messages.

Un des avantages associés est le nivellement de charge, qui permet aux producteurs et aux consommateurs d’envoyer et de recevoir des messages à des vitesses différentes. Dans de nombreuses applications, la charge système varie au fil du temps. Cependant le temps de traitement nécessaire à chaque élément de travail est normalement constant. L’ajout d’une file d’attente entre les producteurs et les consommateurs des messages fait que l’application de destination doit être en mesure de gérer seulement une charge moyenne, plutôt que la charge de travail maximale. La file d’attente s’allonge et se raccourcit en fonction de la charge entrante. Cette fonctionnalité permet de faire des économies concernant les infrastructures nécessaires pour faire face à la charge de travail de l’application. À mesure que la charge augmente, d’autres processus de travail peuvent être ajoutés pour lire les éléments de la file d’attente. Chaque message est traité par un seul des processus de travail. De plus, cet équilibrage de la charge basé sur le tirage (pull) permet d’optimiser l’utilisation des ordinateurs de travail, même si ceux-ci n’ont pas la même puissance de traitement, puisqu’ils tirent les messages au maximum de leur capacité. Ce modèle est souvent appelé modèle consommateur concurrent.

L’utilisation de files d’attente comme intermédiaire entre les producteurs et les consommateurs de message fournit un couplage souple inhérent entre les composants. Producteurs et consommateurs étant indépendants les uns des autres, il est possible de mettre à niveau un consommateur sans que cela affecte le producteur.

### <a name="create-queues"></a>Créer des files d’attente

Pour créer des files d’attente, il est possible d’utiliser le [Portail Azure](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [l’interface de ligne de commande (CLI)](service-bus-quickstart-cli.md) ou les [modèles Resource Manager](service-bus-resource-manager-namespace-queue.md). Les messages sont alors envoyés et reçus à l’aide d’un objet [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient).

Pour apprendre rapidement à créer une file d’attente, puis à envoyer et recevoir des messages en provenance et à destination de la file d’attente, consultez les [Démarrages rapides](service-bus-quickstart-portal.md) de chacune des méthodes. Vous trouverez un tutoriel plus approfondi sur l’utilisation des files d’attente dans [Bien démarrer avec les files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md).

Pour un exemple concret, voir [BasicSendReceiveUsingQueueClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) sur GitHub.

### <a name="receive-modes"></a>Modes de réception

Service Bus propose deux modes différents de réception des messages : *ReceiveAndDelete* et *PeekLock*. En mode [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode), l’opération est exécutée une seule fois, en d’autres termes, lorsque Service Bus reçoit la demande du consommateur, il marque ce message comme étant consommé et le renvoie à l’application du consommateur. Le mode **ReceiveAndDelete** est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels l’application peut tolérer l’absence de traitement d’un message en cas d’échec. Pour comprendre ce comportement, imaginez un scénario selon lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Service Bus ayant marqué le message comme étant consommé, lorsque l’application redémarre et recommence à traiter des messages, elle ignore le message consommé avant l’incident.

En mode [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode), la réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas se permettre de manquer des messages. Lorsque Service Bus reçoit la demande, il recherche le message suivant à consommer, le verrouille pour empêcher d’autres consommateurs de le recevoir, puis le renvoie à l’application. Dès lors que l’application a terminé le traitement du message (ou qu’elle l’a stocké de manière fiable en vue d’un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) sur le message reçu. Lorsque Service Bus voit l’appel **CompleteAsync**, il marque le message comme étant consommé.

Si l’application est dans l’impossibilité de traiter le message pour une raison quelconque, elle peut appeler la méthode [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) sur le message reçu (au lieu de la méthode [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)). Cette méthode permet à Service Bus de déverrouiller le message et de faire en sorte qu’il puisse être à nouveau reçu, soit par le même consommateur, soit par un consommateur concurrent. Deuxièmement, le verrou est assorti d’un délai d’attente ; si l’application ne parvient pas à traiter le message dans le temps imparti (par exemple, en cas d’incident), Service Bus le déverrouille et fait en sorte qu’il puisse être à nouveau reçu (essentiellement en effectuant une opération [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) par défaut).

Si l’application subit un incident après le traitement du message, mais avant l’envoi de la demande **CompleteAsync**, le message est à nouveau remis à l’application lorsqu’elle redémarre. Ce processus est souvent appelé *Au moins une fois*. Chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer un traitement dupliqué, une logique supplémentaire doit être ajoutée à l’application pour détecter les doublons. Pour cela, utilisez la propriété [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) du message, car elle demeure constante entre les tentatives de remise. Cette fonctionnalité est connue sous le nom de traitement *Exactement une fois*.

## <a name="topics-and-subscriptions"></a>Rubriques et abonnements

Contrairement aux files d’attente, où chaque message est traité par un seul consommateur, les *rubriques* et les *abonnements* fournissent une forme de communication « un à plusieurs », à l’aide d’un modèle de *publication et d’abonnement*. Aspect utile en cas de mise à l'échelle vers un grand nombre de destinataires, chaque message publié est mis à disposition de tous les abonnements inscrits auprès de la rubrique. Les messages sont envoyés à une rubrique et remis à un ou plusieurs abonnements associés, en fonction des règles de filtrage qui peuvent être définies sur une base par abonnement. Les abonnements peuvent utiliser des filtres supplémentaires pour restreindre les messages qu’ils souhaitent recevoir. Les messages sont envoyés à une rubrique de la même façon qu’ils sont envoyés à une file d’attente, mais les messages ne sont pas reçus directement de la rubrique. Ils sont envoyés depuis les abonnements. Un abonnement à une rubrique ressemble à une file d’attente virtuelle recevant des copies des messages envoyés à la rubrique. Les messages sont reçus d’un abonnement identique de la même façon que ceux qui sont reçus de la file d’attente.

Par comparaison, la fonctionnalité d’envoi de messages d’une file d’attente mappe directement sur une rubrique et sa fonctionnalité de réception de messages est mappé sur un abonnement. Cette fonctionnalité implique, entre autres, que les abonnements prennent en charge les modèles décrits plus haut dans cette section concernant les files d’attente : consommateurs simultanés, découplage temporel, nivellement et équilibrage de charge.

### <a name="create-topics-and-subscriptions"></a>Créer des rubriques et des abonnements

La création d’une rubrique est similaire à la création d’une file d’attente, comme le montre la section précédente. Les messages sont envoyés à l’aide de la classe [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient). Pour recevoir des messages, vous créerez un ou plusieurs abonnements à la rubrique. À l’instar des files d’attente, les messages sont reçus d’un abonnement à l’aide d’un objet [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) et non d’un objet [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient). Créez le client d’abonnement, en transférant le nom du sujet, le nom de l’abonnement et (éventuellement) le mode de réception en tant que paramètres.

Pour obtenir un exemple complet et concret, consultez [BasicSendReceiveUsingTopicSubscriptionClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) sur GitHub.

### <a name="rules-and-actions"></a>Règles et actions

Dans de nombreux scénarios, les messages ayant des caractéristiques spécifiques doivent être traités différemment. Pour mettre en place ce traitement, vous pouvez configurer des abonnements de sorte qu’ils recherchent les messages présentant les propriétés souhaitées, puis apporter certaines modifications à ces propriétés. Bien que les abonnements Service Bus voient tous les messages envoyés à la rubrique, vous pouvez uniquement copier un sous-ensemble de ces messages dans la file d’attente d’abonnement virtuelle. On utilise pour cela des filtres d’abonnement. Ces modifications sont appelées *actions de filtrage*. Lorsqu’un abonnement est créé, vous pouvez fournir une expression de filtre fonctionnant sur les propriétés du message, aussi bien les propriétés du système (par exemple, **Label**) et les propriétés personnalisées de l’application (par exemple, **StoreName**.) L’expression de filtre SQL est facultative dans ce cas ; sans expression de filtre SQL, toute action de filtre définie sur un abonnement sera effectuée sur tous les messages de cet abonnement.

Pour un exemple complet et concret, voir [TopicSubscriptionWithRuleOperationsSample](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) sur GitHub.

Pour plus d’informations sur les valeurs de filtre possibles, consultez la documentation des classes [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) et [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations et des exemples d’utilisation de la messagerie Service Bus, consultez les rubriques avancées suivantes :

* [Présentation de la messagerie Service Bus](service-bus-messaging-overview.md)
* [Démarrage rapide : Envoyer et recevoir des messages à l’aide du portail Azure et de .NET](service-bus-quickstart-portal.md)
* [Tutoriel : Mettre à jour l’inventaire à l’aide du portail Azure et des rubriques/abonnements](service-bus-tutorial-topics-subscriptions-portal.md)


