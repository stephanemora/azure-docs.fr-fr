---
title: Azure Event Hubs pour les écosystèmes Kafka | Microsoft Docs
description: Vue d’ensemble et introduction à Azure Event Hubs prenant en charge Kafka
services: event-hubs
documentationcenter: .net
author: djrosanova
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 05/07/2018
ms.author: darosa
ms.openlocfilehash: 51e32737be4eaf3c61cdbe50b82a05c205800ac4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077604"
---
# <a name="event-hubs-for-kafka-ecosystems"></a>Event Hubs pour les écosystèmes Kafka

Event Hubs pour les écosystèmes Kafka fournit un point de terminaison Kafka qui peut être utilisé par vos applications Kafka pour éviter d’exécuter votre propre cluster Kafka. Event Hubs pour l’écosystème Kafka prend en charge [Apache Kafka 1.0](https://kafka.apache.org/10/documentation.html) et les versions clientes plus récentes, et fonctionne avec vos applications Kafka, notamment MirrorMaker. Modifiez votre chaîne de connexion et démarrez le streaming d’événements à partir de vos applications qui utilisent le protocole Kafka dans Event Hubs.

## <a name="what-does-event-hubs-for-kafka-ecosystems-provide"></a>Qu’apporte Event Hubs pour les écosystèmes Kafka ?

Event Hubs pour l’écosystème Kafka fournit une tête de protocole sur Azure Event Hubs qui est compatible d’un point de vue binaire avec Kafka versions 1.0 et ultérieures pour la lecture et l’écriture dans des rubriques Kafka. Sur le plan conceptuel, Kafka et Event Hubs sont presque identiques : ce sont tous les deux des journaux partitionnés générés pour le streaming de données. Le tableau suivant mappe les concepts entre Kafka et Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Mappage des concepts Kafka et Event Hubs

| Concept Kafka | Concept Event Hubs|
| --- | --- |
| Cluster | Espace de noms |
| Rubrique | Event Hubs |
| Partition | Partition|
| Groupe de consommateurs | Groupe de consommateurs |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Principales différences entre Kafka et Event Hubs

[Apache Kafka](https://kafka.apache.org/) est un logiciel que vous pouvez exécuter où vous voulez, tandis qu’Event Hubs est un service cloud similaire au stockage Blob Azure. Il n’existe aucun serveur ni réseau à gérer, ni aucun répartiteur à configurer. Vous créez un espace de noms, qui est un nom de domaine complet dans lequel figurent vos rubriques, puis vous créez des hubs d’événements ou rubriques dans cet espace de noms. Pour plus d’informations sur les espaces de noms et Event Hubs, consultez [Qu’est-ce qu’Event Hubs ?](event-hubs-what-is-event-hubs.md). En tant que service cloud, Event Hubs utilise une seule adresse IP virtuelle stable comme point de terminaison, les clients n’ont donc pas besoin de connaître les répartiteurs ou machines dans un cluster. 

La mise à l’échelle dans Event Hubs est contrôlée par le nombre d’unités de débit que vous achetez, chacune d’elles vous donnant droit à 1 Mo par seconde ou 1 000 événements par seconde d’entrée. Par défaut, Event Hubs effectue une montée en puissance en augmentant le nombre d’unités de débit quand vous atteignez votre limite avec la fonctionnalité [d’augmentation automatique](event-hubs-auto-inflate.md) ; celle-ci fonctionne également avec Event Hubs pour les écosystèmes Kafka. 

### <a name="security-and-authentication"></a>Sécurité et authentification

Azure Event Hubs nécessite SSL ou TLS pour toutes les communications et utilise les signatures d’accès partagé pour l’authentification. Cette condition est également vraie pour les points de terminaison Kafka dans Event Hubs. Pour des raisons de compatibilité avec Kafka, Event Hubs utilise SASL PLAIN pour l’authentification et SASL SSL pour la sécurité de transport. Pour plus d’informations sur la sécurité dans Event Hubs, consultez [Présentation du modèle de sécurité et de l’authentification Event Hubs](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Autres fonctionnalités Event Hubs disponibles pour Kafka

Event Hubs pour l’écosystème Kafka vous permet d’écrire avec un protocole et de lire avec un autre afin que vos producteurs Kafka actuels puissent continuer la publication via Kafka, et vous pouvez ajouter des lecteurs avec Event Hubs comme Azure Stream Analytics ou Azure Functions. De plus, les fonctionnalités Event Hubs telles que la [capture](event-hubs-capture-overview.md) et la [géoreprise d’activité après sinistre](event-hubs-geo-dr.md) fonctionnent également avec Event Hubs pour les écosystèmes Kafka.

## <a name="features-that-are-not-supported-in-the-preview"></a>Fonctionnalités qui ne sont pas prises en charge dans la préversion

Pour la préversion publique de l’intégration d’Event Hubs pour les écosystèmes Kafka, les fonctionnalités Kafka suivantes ne sont pas prises en charge :

*   Producteur idempotent
*   Transaction
*   Compression
*   Rétention basée sur la taille
*   Compactage de journal
*   Ajout de partitions à une rubrique existante
*   Prise en charge de l’API Kafka HTTP
*   Kafka Connect
*   Kafka Streams

## <a name="next-steps"></a>Étapes suivantes

Cet article a présenté Event Hubs pour les écosystèmes Kafka. Pour en savoir plus, consultez les liens suivants :

* [Créer un espace de noms Event Hubs prenant en charge Kafka](event-hubs-create-kafka-enabled.md)
* [Diffuser dans Event Hubs à partir de vos applications Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* Bien démarrer avec un [tutoriel sur Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [FAQ sur les hubs d'événements](event-hubs-faq.md)

 
 

