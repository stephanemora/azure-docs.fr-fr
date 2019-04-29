---
title: Utiliser un hub d’événements à partir de l’application Apache Kafka - Azure Event Hubs | Microsoft Docs
description: Cet article fournit des informations sur la prise en charge d’Apache Kafka par Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: shvija
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 8bf381e7c66e06bbaa140ed865f0f7c9b4f001af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821699"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Utiliser Azure Event Hubs à partir d’applications Apache Kafka
Event Hubs fournit un point de terminaison Kafka qui peut être utilisé par vos applications Kafka pour éviter d’exécuter votre propre cluster Kafka. Event Hubs prend en charge [Apache Kafka protocole 1.0 et ultérieurs](https://kafka.apache.org/documentation/), et fonctionne avec vos applications Kafka, notamment MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Qu’apporte Event Hubs à Kafka ?

La fonctionnalité Event Hubs pour Kafka fournit une tête de protocole sur Azure Event Hubs qui est compatible d’un point de vue binaire avec Kafka versions 1.0 et ultérieures pour lire et écrire dans des rubriques Kafka. Vous pouvez démarrer à l’aide du point de terminaison Kafka à partir de vos applications avec une modification de configuration minimale, mais aucune modification de code. Vous mettez à jour la chaîne de connexion dans les configurations pour pointer vers le point de terminaison Kafka exposé par votre hub d’événements au lieu de pointer vers votre cluster Kafka. Vous pouvez ensuite commencer à diffuser des événements en continu à partir de vos applications qui utilisent le protocole Kafka dans Event Hubs. Cette intégration prend également en charge les infrastructures telles que [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), qui est actuellement en préversion. 

Sur le plan conceptuel, Kafka et Event Hubs sont presque identiques : ce sont tous les deux des journaux d’activité partitionnés générés pour le streaming de données. Le tableau suivant mappe les concepts entre Kafka et Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Mappage des concepts Kafka et Event Hubs

| Concept Kafka | Concept Event Hubs|
| --- | --- |
| Cluster | Espace de noms |
| Rubrique | Event Hub |
| Partition | Partition|
| Groupe de consommateurs | Groupe de consommateurs |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Principales différences entre Kafka et Event Hubs

[Apache Kafka](https://kafka.apache.org/) est un logiciel que vous pouvez exécuter où vous voulez, tandis qu’Event Hubs est un service cloud similaire au stockage Blob Azure. Il n’existe aucun serveur ni réseau à gérer, ni aucun répartiteur à configurer. Vous créez un espace de noms, qui est un nom de domaine complet dans lequel figurent vos rubriques, puis vous créez des hubs d’événements ou rubriques dans cet espace de noms. Pour plus d’informations sur les espaces de noms et Event Hubs, consultez [Fonctionnalités Event Hubs](event-hubs-features.md#namespace). En tant que service cloud, Event Hubs utilise une seule adresse IP virtuelle stable comme point de terminaison, les clients n’ont donc pas besoin de connaître les répartiteurs ou machines dans un cluster. 

La mise à l’échelle dans Event Hubs est contrôlée par le nombre d’unités de débit que vous achetez, chacune d’elles vous donnant droit à 1 Mo par seconde ou 1 000 événements par seconde d’entrée. Par défaut, Event Hubs augmente le nombre d’unités de débit (scale up) quand vous atteignez votre limite avec la fonctionnalité [d’augmentation automatique](event-hubs-auto-inflate.md) ; celle-ci fonctionne également avec la fonctionnalité Event Hubs pour Kafka. 

### <a name="security-and-authentication"></a>Sécurité et authentification

Azure Event Hubs nécessite SSL ou TLS pour toutes les communications et utilise les signatures d’accès partagé pour l’authentification. Cette condition est également vraie pour les points de terminaison Kafka dans Event Hubs. Pour des raisons de compatibilité avec Kafka, Event Hubs utilise SASL PLAIN pour l’authentification et SASL SSL pour la sécurité de transport. Pour plus d’informations sur la sécurité dans Event Hubs, consultez [Présentation du modèle de sécurité et de l’authentification Event Hubs](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Autres fonctionnalités Event Hubs disponibles pour Kafka

La fonctionnalité Event Hubs pour Kafka vous permet d’écrire avec un protocole et de lire avec un autre afin que vos producteurs Kafka actuels puissent continuer la publication via Kafka, et vous pouvez ajouter des lecteurs avec Event Hubs comme Azure Stream Analytics ou Azure Functions. De plus, les fonctionnalités Event Hubs telles que la [capture](event-hubs-capture-overview.md) et la [géoreprise d’activité après sinistre](event-hubs-geo-dr.md) fonctionnent également avec la fonctionnalité Event Hubs pour Kafka.

## <a name="features-that-are-not-yet-supported"></a>Fonctionnalités qui ne sont pas encore prises en charge 

Voici la liste des fonctionnalités de Kafka qui ne sont pas prises en charge :

*   Producteur idempotent
*   Transaction
*   Compression
*   Rétention basée sur la taille
*   Compactage de journal
*   Ajout de partitions à une rubrique existante
*   Prise en charge de l’API Kafka HTTP
*   Kafka Streams

## <a name="next-steps"></a>Étapes suivantes

Cet article a présenté une introduction à Event Hubs pour Kafka. Pour en savoir plus, consultez les liens suivants :

- [Créer un espace de noms Event Hubs prenant en charge Kafka](event-hubs-create-kafka-enabled.md)
- [Diffuser dans Event Hubs à partir de vos applications Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Mettre en miroir un répartiteur Kafka dans un Event Hub prenant en charge Kafka](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connecter Apache Spark à un Event Hub prenant en charge Kafka](event-hubs-kafka-spark-tutorial.md)
- [Connecter Apache Flink à un Event Hub prenant en charge Kafka](event-hubs-kafka-flink-tutorial.md)
- [Intégrer Kafka Connect à un Event Hub prenant en charge Kafka](event-hubs-kafka-connect-tutorial.md)
- [Connecter Akka Streams à un Event Hub prenant en charge Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Explorer des exemples sur GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)


