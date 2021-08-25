---
title: Nouveautés des concentrateurs d'événements Azure ? - service d'ingestion de Big Data | Microsoft Docs
description: Apprenez-en plus sur Azure Event Hubs, un service de diffusion en continu Big Data qui ingère des millions d’événements par seconde.
ms.topic: overview
ms.date: 05/25/2021
ms.openlocfilehash: fa32e26439cfd7f2e4319fdb7dc631bfadb023d4
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322146"
---
# <a name="azure-event-hubs--a-big-data-streaming-platform-and-event-ingestion-service"></a>Azure Event Hubs - plateforme de streaming de Big Data et service d’ingestion d’événements
Azure Event Hubs est une plateforme de streaming de Big Data et un service d’ingestion d’événements. Il peut recevoir et traiter des millions d’événements par seconde. Les données envoyées à un concentrateur d’événements peuvent être transformées et stockées à l’aide d’adaptateurs de traitement par lot/stockage ou d’un fournisseur d’analyse en temps réel.

Les scénarios suivants sont quelques-uns des scénarios dans lesquels vous pouvez utiliser Event Hubs :

- Détection d’anomalies (fraude/valeurs hors norme)
- Journalisation des applications
- Pipelines d’analyse, comme les parcours de visite
- Tableaux de bord en temps réel
- Archivage des données
- Traitement des transactions
- Traitement des données de télémétrie utilisateur
- Diffusion en continu de télémétrie d’appareil

## <a name="why-use-event-hubs"></a>Pourquoi utiliser Azure Event Hubs ?
Les données ne sont importantes que lorsqu’il y a une manière facile de les traiter et d’obtenir des insights en temps voulu de sources de données. Event Hubs fournit une plateforme de traitement de flux distribués avec une latence faible et une intégration sans interruption, avec des services de données et d’analyse dans et hors Azure pour générer votre pipeline de Big Data complet.

Event Hubs représente la « porte d’entrée » pour un pipeline d’événements, parfois qualifié de service de *réception d’événements* dans des architectures de solution. Un service de réception d'événements est un composant ou service qui se trouve entre les éditeurs d'événements et les consommateurs d'événements pour dissocier la production d'un flux d'événements de leur consommation. Event Hubs fournit une plateforme de streaming unifiée avec une mémoire tampon de conservation temporelle, en séparant les producteurs d’événements des consommateurs d’événements.


Les sections suivantes décrivent les fonctionnalités clés du service Azure Event Hubs :

## <a name="fully-managed-paas"></a>PaaS intégralement gérée
Event Hubs est un service PaaS (Platform-as-a-Service) entièrement géré avec peu de configuration ou de gestion, ce qui vous permet de vous concentrer sur vos solutions métier. [Event Hubs pour les écosystèmes d’Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) vous offre l’expérience de PaaS Kafka sans avoir à gérer, configurer ou exécuter vos clusters.

## <a name="support-for-real-time-and-batch-processing"></a>Prise en charge d’un traitement par lots et en temps réel
Ingérez, mettez en mémoire tampon, stockez et traitez votre flux en temps réel pour obtenir des insights actionnables. Event Hubs utilise un [modèle de consommateur partitionné](event-hubs-scalability.md#partitions), ce qui permet à plusieurs applications de traiter le flux de données en même temps et vous laisse le contrôle sur la rapidité du traitement. Azure Event Hubs s’intègre aussi avec [Azure Functions](../azure-functions/index.yml) pour une architecture sans serveur.

## <a name="capture-event-data"></a>Capturer les données d’événement
[Capturez](event-hubs-capture-overview.md) vos données en quasi temps réel dans un [Stockage Blob Azure](https://azure.microsoft.com/services/storage/blobs/) ou dans [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)  pour une conservation à long terme ou un traitement par micro-lots. Vous pouvez obtenir ce comportement sur le même flux que celui que vous utilisez pour la dérivation de l’analytique en temps réel. La configuration de l’outil Capture est rapide. Son exécution n’engendre aucun coût d’administration et il s’adapte automatiquement à vos  [unités de débit](event-hubs-scalability.md#throughput-units) ou [unités de traitement](event-hubs-scalability.md#processing-units) Event Hubs. Event Hubs vous permet de vous concentrer sur le traitement des données plutôt que sur leur capture.

## <a name="scalable"></a>Évolutif
Avec Event Hubs, vous pouvez commencer avec des flux de données en mégaoctets et passer à des gigaoctets ou téraoctets. La fonctionnalité de [majoration automatique](event-hubs-auto-inflate.md) est une des nombreuses options disponibles pour mettre à l'échelle le nombre d’unités de débit ou d’unités de traitement et ainsi répondre à vos besoins d’utilisation.

## <a name="rich-ecosystem"></a>Écosystème riche

Avec un vaste écosystème basé sur le protocole AMQP 1.0 standard et disponible dans différents langages ([.NET](https://github.com/Azure/azure-sdk-for-net/), [Java](https://github.com/Azure/azure-sdk-for-java/), [Python](https://github.com/Azure/azure-sdk-for-python/), [JavaScript](https://github.com/Azure/azure-sdk-for-js/)), vous pouvez facilement lancer le traitement de vos flux en provenance d’Event Hubs. Tous les langages client pris en charge fournissent une intégration de faible niveau. L’écosystème vous fournit également une intégration fluide aux services Azure tels qu’Azure Stream Analytics et Azure Functions, et vous permet donc de générer des architectures serverless.

## <a name="event-hubs-for-apache-kafka"></a>Event Hubs pour Apache Kafka
Par ailleurs, [Event Hubs pour les écosystèmes Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) permet aux clients et aux applications [Apache Kafka (1.0 et ultérieur)](https://kafka.apache.org/) de communiquer avec Event Hub. Vous n’avez pas besoin d’installer, de configurer et de gérer vos propres clusters Kafka et Zookeeper, ni d’utiliser une offre Kafka en tant que service qui n’est pas native pour Azure.

## <a name="event-hubs-premium-and-dedicated"></a>Niveaux Premium et Dédié d’Event Hubs 
Le niveau **Premium** d’Event Hubs répond aux besoins de streaming haut de gamme qui requièrent des performances supérieures, une meilleure isolation avec une latence prédictible et une interférence minimale dans un environnement PaaS multilocataire managé. En plus de toutes les fonctionnalités de l’offre Standard, le niveau Premium offre plusieurs fonctionnalités supplémentaires, telles que le scale-up dynamique des partitions, la conservation prolongée et les clés gérées par le client. Pour plus d'informations, consultez la page sur le [niveau Premium Event Hubs](event-hubs-premium-overview.md).

Le niveau **Dédié** d’Event Hubs offre des déploiements à locataire unique pour les clients aux besoins de streaming les plus pointus. Cette offre à locataire unique a un SLA de 99,99 % garanti et n'est disponible sur que notre niveau de tarification dédié. Un cluster Event Hubs peut recevoir des millions d’événements par seconde avec une garantie de capacité et de latence inférieure à la seconde. Les espaces de noms et les Event Hubs créés au sein du cluster dédié incluent toutes les fonctionnalités de l’offre Premium et bien davantage. Pour plus d’informations, consultez [Event Hubs Dedicated](event-hubs-dedicated-overview.md).

Si vous souhaitez en savoir plus, consultez la [comparaison entre les niveaux Event Hubs](event-hubs-quotas.md).

## <a name="event-hubs-on-azure-stack-hub"></a>Event Hubs sur Azure Stack Hub
Event Hubs sur Azure Stack Hub vous permet de réaliser des scénarios de cloud hybride. Le streaming et les solutions basées sur les événements sont pris en charge pour le traitement par le cloud Azure et local. Que votre scénario soit hybride (connecté) ou déconnecté, votre solution peut prendre en charge le traitement des événements/flux à grande échelle. Votre scénario est limité uniquement par la taille du cluster Event Hubs, que vous pouvez provisionner en fonction de vos besoins. 

Les éditions Event Hubs (sur Azure Stack Hub et sur Azure) offrent un degré élevé de parité des fonctionnalités. Cette parité signifie que les SDK, les exemples, PowerShell, l’interface CLI et les portails offrent une expérience similaire, avec quelques différences. 

Event Hubs sur Stack est gratuit pendant la préversion publique. Pour plus d’informations, consultez [Vue d’ensemble d’Event Hubs sur Azure Stack Hub](/azure-stack/user/event-hubs-overview).

## <a name="key-architecture-components"></a>Composants clés de l’architecture
Les concentrateurs d’événements incluent les [éléments clés suivants](event-hubs-features.md) :

- **Producteurs d’événements** : entité qui envoie des données à un hub d'événements. Les éditeurs d’événements peuvent publier des événements à l’aide du protocole HTTPS ou AMQP 1.0 ou Apache Kafka (1.0 et plus récent).
- **Partitions** : chaque consommateur lit uniquement un sous-ensemble spécifique (partition) du flux de messages.
- **Groupes de consommateurs** : vue (état, position ou décalage) d'un hub d'événements dans sa totalité. Les groupes de consommateurs permettent que les applications de consommation aient chacune une vue distincte du flux d’événements. Ils lisent le flux de manière indépendante à leur propre rythme et avec leurs propres décalages.
- [Unités de débit](event-hubs-scalability.md#throughput-units) ou [Unités de traitement](event-hubs-scalability.md#processing-units) : unités de capacité qui contrôlent la capacité de débit d’Event Hubs, achetées préalablement.
- **Récepteurs d’événements** : entité qui lit des données d'événement à partir d'un hub d’événements. Tous les consommateurs Event Hubs se connectent à l’aide de la session AMQP 1.0. Le service Event Hubs remet les événements au cours d’une session dès qu’ils sont disponibles. Tous les consommateurs de Kafka se connectent via le protocole Kafka 1.0 et versions ultérieures.

La figure suivante montre l’architecture de traitement de flux Event Hubs :

![Event Hubs](./media/event-hubs-about/event_hubs_architecture.svg)


## <a name="next-steps"></a>Étapes suivantes

Pour commencer à utiliser Event Hubs, consultez les tutoriels **Envoyer et recevoir des événements** :

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (envoi uniquement)](event-hubs-c-getstarted-send.md)
- [Apache Storm (réception uniquement)](event-hubs-storm-getstarted-receive.md)


Pour plus d’informations sur les hubs d’événements, consultez les articles suivants :

- [Vue d’ensemble des fonctionnalités des concentrateurs d’événements](event-hubs-features.md)
- [Forum aux questions](event-hubs-faq.yml).
