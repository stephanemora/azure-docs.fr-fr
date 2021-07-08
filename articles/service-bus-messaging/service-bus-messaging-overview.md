---
title: Présentation de la messagerie Azure Service Bus | Microsoft Docs
description: Cet article offre une vue d’ensemble d’Azure Service Bus, le répartiteur de messages Intégration Entreprise complètement managé.
ms.topic: overview
ms.date: 06/11/2021
ms.openlocfilehash: 932fde55bdd315993de5375706f7d5d3621cef6d
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112030710"
---
# <a name="what-is-azure-service-bus"></a>Qu’est-ce qu’Azure Service Bus ?
Microsoft Azure Service Bus est un répartiteur de messages d’entreprise complètement managé, avec des files d’attente de messages et des rubriques de publication/abonnement. Service Bus est utilisé pour découpler les applications et les services les uns des autres pour offrir les avantages suivants :

- Travail d’équilibrage de charge entre les workers concurrents
- Routage et transfert de façon sécurisée des données et du contrôle au-delà des limites des services et des applications
- Coordination du travail transactionnel qui nécessite un degré élevé de fiabilité 

## <a name="overview"></a>Vue d’ensemble
Les données sont transférées entre différents services et applications à l’aide de **messages**. Un message est un conteneur décoré de métadonnées, qui contient des données. Les données peuvent être n’importe quel type d’informations, y compris des données structurées, codées aux formats courants comme ceux-ci : JSON, XML, Apache Avro, Texte brut.

Les scénarios de messagerie courants sont :

* **Messagerie**. transfert de données d’entreprise, telles que les ventes ou les bons de commande, les journaux ou les mouvements de stock.
* **Découplage d’applications** : amélioration de la fiabilité et de l’extensibilité de vos applications et services. Il n’est pas nécessaire que les producteurs et les consommateurs soient en ligne ou immédiatement disponibles en même temps. La charge [est nivelée](/azure/architecture/patterns/queue-based-load-leveling) de telle sorte que les pics de trafic ne surtaxent pas un service. 
* **Équilibrage de la charge**. Possibilité pour plusieurs [consommateurs concurrents](/azure/architecture/patterns/competing-consumers) de lire dans une file d’attente en même temps, chacun obtenant de façon sécurisée la propriété exclusive sur des messages spécifiques. 
* **Rubriques et abonnements** : Activation des relations 1:*n* entre [les éditeurs et les abonnés](/azure/architecture/patterns/publisher-subscriber), ce qui permet aux abonnés de sélectionner des messages particuliers dans un flux de messages publiés.
* **Transactions**. Possibilité d’effectuer plusieurs opérations, toutes dans l’étendue d’une transaction atomique. Par exemple, les opérations suivantes peuvent être effectuées dans l’étendue d’une transaction.  

    1. Obtenir un message depuis une file d’attente.
    2. Publier les résultats du traitement dans une ou plusieurs files d’attente différentes.
    3. Déplacer le message d’entrée à partir de la file d’attente d’origine. 
    
    Pour les consommateurs en aval, les résultats ne sont visibles qu’en cas de réussite, ce qui comprend le règlement du message d’entrée autorisant la sémantique de traitement une fois seulement. Ce modèle de transaction constitue une base solide pour le modèle des [transactions de compensation](/azure/architecture/patterns/compensating-transaction) dans le contexte de solution supérieur. 
* **Sessions de messagerie** : Implémentez une coordination à grande échelle des flux de travail et des transferts multiplexés qui nécessitent un classement strict des messages ou le report des messages.

Si vous êtes familiarisé avec d’autres répartiteurs de messages, comme Apache ActiveMQ, les concepts de Service Bus sont similaires à ceux que vous connaissez. Service Bus étant une offre PaaS (Platform-as-a-service), la grande différence se situe dans les actions suivantes, dont vous n’avez pas à vous soucier. Azure s’occupe de ces tâches pour vous. 

- Inquiétude par rapport aux défaillances matérielles 
- Mise à jour des systèmes d’exploitation ou des produits
- Placement des journaux et gestion de l’espace disque
- Gestion des sauvegardes
- Basculement vers une machine de réserve

## <a name="compliance-with-standards-and-protocols"></a>Conformité avec les normes et les protocoles

Le protocole filaire principal de Service Bus est [AMQP (Advanced Messaging Queueing Protocol) 1.0](service-bus-amqp-overview.md), une norme ISO/IEC ouverte. Il permet aux clients d’écrire des applications qui fonctionnent sur Service Bus et sur des répartiteurs locaux, tels que ActiveMQ ou RabbitMQ. Le [Guide du protocole AMQP](service-bus-amqp-protocol-guide.md) fournit des informations détaillées au cas où vous souhaiteriez élaborer une telle abstraction.

[Service Bus Premium](service-bus-premium-messaging.md) est entièrement compatible avec l’API [Java Message Service (JMS) 2.0](how-to-use-java-message-service-20.md) de Java/Jakarta EE. De plus, Service Bus standard prend en charge le sous-ensemble JMS 1.1 axé sur les files d’attente. JMS est une abstraction courante pour les répartiteurs de messages, elle s’intègre à de nombreuses applications et frameworks, y compris le populaire framework Spring. Pour passer d’un autre répartiteur à Azure Service Bus, il vous suffit de recréer la topologie des files d’attente et des rubriques, puis de modifier les dépendances et la configuration du fournisseur client. Pour consulter un exemple, reportez-vous au [Guide de migration d’ActiveMQ](migrate-jms-activemq-to-servicebus.md).

## <a name="concepts-and-terminology"></a>Concepts et terminologie 
Cette section traite des concepts et de la terminologie de Service Bus.

### <a name="queues"></a>Files d’attente
Les messages sont envoyés vers et reçus depuis les **files d’attente**. Les files d’attente stockent les messages jusqu’à ce que l’application réceptrice soit disponible pour recevoir et traiter ces messages.

![File d'attente](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Les messages dans les files d’attente sont classés et horodatés à l’arrivée. Une fois accepté par le répartiteur, le message est toujours conservé durablement dans le stockage à triple redondance, réparti entre les zones de disponibilité si l’espace de noms est activé interzone. Service Bus ne laisse jamais des messages en mémoire ni dans un stockage volatile lorsqu’ils ont été signalés au client comme acceptés.

Les messages sont remis en mode **tirage (pull)** , qui remet uniquement les messages lorsqu’ils sont demandés. Contrairement au modèle busy-polling de certaines autres files d’attente cloud, l’opération de tirage peut être longue et ne se terminer qu’une fois un message disponible. 

### <a name="topics"></a>Rubriques

Vous pouvez également utiliser des **rubriques** pour envoyer et recevoir des messages. Si une file d’attente est souvent utilisée pour la communication point à point, les rubriques sont utiles dans les scénarios de publication/abonnement.

![Rubrique](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Les rubriques peuvent avoir plusieurs abonnements indépendants, qui s’attachent à la rubrique et fonctionnent exactement comme des files d’attente côté destinataire. Un abonné à une rubrique peut recevoir une copie de chaque message envoyé à cette rubrique. Les abonnements sont des entités nommées. Les abonnements sont durables par défaut, mais ils peuvent être configurés pour expirer, puis être automatiquement supprimés. Par le biais de l’API JMS, Service Bus Premium vous permet également de créer des abonnements volatiles qui existent le temps de la connexion.

Vous pouvez définir des règles sur un abonnement. Une règle d’abonnement est dotée d’un **filtre** permettant de définir une condition afin que le message soit copié dans l’abonnement, et d’une **action** facultative qui peut modifier les métadonnées du message. Si vous souhaitez en savoir plus, veuillez consulter l’article [Actions et filtres de rubrique](topic-filters.md). Cette fonctionnalité est utile dans les scénarios suivants :

- Lorsqu’il n’est pas souhaitable qu’un abonnement reçoive tous les messages envoyés à une rubrique.
- Quand il est nécessaire de marquer les messages avec des métadonnées supplémentaires lors du passage par un abonnement.

### <a name="namespaces"></a>Espaces de noms
Un espace de noms est un conteneur pour tous les composants de messagerie (files d'attente et rubriques). Plusieurs files d’attente et rubriques peuvent résider dans un seul espace de noms, et les espaces de noms servent souvent de conteneurs d’applications. 

Un espace de noms peut être comparé à un serveur dans la terminologie d’autres répartiteurs, mais les concepts ne sont pas directement équivalents. Un espace de noms Service Bus représente votre portion de capacité propre d’un grand cluster constitué de dizaines de machines virtuelles actives. Il peut éventuellement s’étendre sur trois [zones de disponibilité Azure](../availability-zones/az-overview.md). Vous bénéficiez ainsi de tous les avantages de la disponibilité et de la robustesse qui sont liés à l’exécution du répartiteur de messages à très grande échelle. Qui plus est, vous n’avez pas à vous soucier des complexités sous-jacentes. Service Bus est une messagerie serverless.

## <a name="advanced-concepts"></a>Concepts avancés
Service Bus inclut des fonctionnalités avancées telles que des sessions de message, la livraison planifiée, et des transactions qui vous permettent de résoudre des problèmes de messagerie plus complexes. Pour plus d’informations, consultez [Fonctionnalités avancées d’Azure Service Bus](advanced-features-overview.md).

## <a name="client-libraries"></a>Bibliothèques clientes

Les bibliothèques de client Service Bus entièrement prises en charge sont disponibles par le biais du kit SDK Azure.

- [Azure Service Bus pour .NET](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Bibliothèques Azure Service Bus pour Java](/java/api/overview/azure/servicebus?preserve-view=true)
- [Fournisseur Azure Service Bus pour Java JMS 2.0](how-to-use-java-message-service-20.md)
- [Modules Azure Service Bus pour JavaScript et TypeScript](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Bibliothèques Azure Service Bus pour Python](/python/api/overview/azure/servicebus?preserve-view=true)

[Le protocole principal d’Azure Service Bus est AMQP 1.0](service-bus-amqp-overview.md) ; il est utilisable depuis n’importe quel client de protocole compatible AMQP 1.0. Quelques clients AMQP open source offrent des exemples illustrant de manière explicite l’interopérabilité de Service Bus. Consultez le [Guide du protocole AMQP 1.0](service-bus-amqp-protocol-guide.md) pour comprendre comment utiliser les fonctionnalités Service Bus directement avec les clients AMQP 1.0.

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="integration"></a>Intégration

Service Bus s’intègre entièrement à de nombreux services Microsoft et Azure, par exemple :

* [Event Grid](service-bus-to-event-grid-integration-example.md)
* [Logic Apps](../connectors/connectors-create-api-servicebus.md)
* [Azure Functions](../azure-functions/functions-bindings-service-bus.md)
* [Power Platform](../connectors/connectors-create-api-servicebus.md)
* [Dynamics 365](/dynamics365/fin-ops-core/dev-itpro/business-events/how-to/how-to-servicebus)
* [Azure Stream Analytics](../stream-analytics/stream-analytics-define-outputs.md)

## <a name="next-steps"></a>Étapes suivantes

Pour commencer à utiliser la messagerie Service Bus, consultez les articles suivants :

* Si vous souhaitez comparer les services de messagerie Azure, veuillez consulter l’article [Choisissez entre les trois services de messagerie Azure - Event Grid, Event Hubs et Service Bus](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json).
* Essayez les Guides de démarrage rapide pour [.NET](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md) ou [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Si vous devez gérer des ressources Service Bus, veuillez consulter le référentiel GitHub [Explorateur Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases) (contenu en anglais).
* Si vous souhaitez en savoir plus sur les niveaux Standard et Premium et leur tarification, veuillez consulter la page [Tarification Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).
* Si vous souhaitez en savoir plus sur les performances et la latence du niveau Premium, veuillez consulter le billet de blog [Messagerie Premium](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722) (contenu en anglais).