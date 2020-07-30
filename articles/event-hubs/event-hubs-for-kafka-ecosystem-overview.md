---
title: Utiliser un hub d’événements à partir de l’application Apache Kafka - Azure Event Hubs | Microsoft Docs
description: Cet article fournit des informations sur la prise en charge d’Apache Kafka par Azure Event Hubs.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: 6dad797f6769e9804cce7d47ed74794fdddcfb0b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87002476"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Utiliser Azure Event Hubs à partir d’applications Apache Kafka
Event Hubs fournit un point de terminaison Kafka qui peut être utilisé par vos applications Kafka pour éviter d’exécuter votre propre cluster Kafka. Event Hubs prend en charge [Apache Kafka protocole 1.0 et ultérieurs](https://kafka.apache.org/documentation/), et fonctionne avec vos applications Kafka, notamment MirrorMaker.  

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>Qu’apporte Event Hubs à Kafka ?

La fonctionnalité Event Hubs pour Kafka fournit une tête de protocole sur Azure Event Hubs qui est compatible d’un point de vue binaire avec Kafka versions 1.0 et ultérieures pour lire et écrire dans des rubriques Kafka. Vous pouvez démarrer à l’aide du point de terminaison Kafka à partir de vos applications avec une modification de configuration minimale, mais aucune modification de code. Vous mettez à jour la chaîne de connexion dans les configurations pour pointer vers le point de terminaison Kafka exposé par votre hub d’événements au lieu de pointer vers votre cluster Kafka. Vous pouvez ensuite commencer à diffuser des événements en continu à partir de vos applications qui utilisent le protocole Kafka dans Event Hubs. Cette intégration prend également en charge les infrastructures telles que [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), qui est actuellement en préversion. 

Sur le plan conceptuel, Kafka et Event Hubs sont presque identiques : ce sont tous les deux des journaux d’activité partitionnés générés pour la diffusion en continu de données. Le tableau suivant mappe les concepts entre Kafka et Event Hubs.

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
Chaque fois que vous publiez ou utilisez des événements à partir d'une instance Event Hubs pour Kafka, votre client tente d'accéder aux ressources Event Hubs. Vous voulez assurer l'accès aux ressources à l’aide d’une entité autorisée. Lorsque vous utilisez le protocole Apache Kafka avec vos clients, vous pouvez utiliser des mécanismes SASL pour définir votre configuration à des fins d’authentification et de chiffrement. L’utilisation d'Event Hubs pour Kafka nécessite le chiffrement TLS (car toutes les données en transit avec Event Hubs sont chiffrées avec le protocole TLS). Pour ce faire, vous pouvez spécifier l'option SASL_SSL dans votre fichier de configuration. 

Azure Event Hubs propose plusieurs options afin d'autoriser l’accès à vos ressources sécurisées. 

- OAuth
- Signature d’accès partagé (SAP)

#### <a name="oauth"></a>OAuth
Event Hubs s’intègre à Azure Active Directory (Azure AD), qui fournit un serveur d'autorisation centralisé compatible avec **OAuth** 2.0. Avec Azure AD, vous pouvez utiliser le contrôle d’accès en fonction du rôle (RBAC) pour accorder des autorisations affinées à vos identités clientes. Vous pouvez utiliser cette fonctionnalité avec vos clients Kafka en spécifiant **SASL_SSL** pour le protocole et **OAUTHBEARER** pour le mécanisme. Pour plus d’informations sur les rôles RBAC et niveaux en matière d'accès, consultez [Autoriser l’accès avec Azure AD](authorize-access-azure-active-directory.md).

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Signature d’accès partagé (SAS)
Event Hubs fournit également les **signatures d'accès partagé (SAP)** à des fins d'accès délégué aux ressources Event Hubs pour Kafka. Autoriser l’accès à l’aide d’un mécanisme basé sur les jetons OAuth 2.0 offre une sécurité et une facilité d'utilisation supérieures par rapport aux SAP. Grâce aux rôles intégrés, il n'est plus nécessaire de recourir à une autorisation basée sur une liste de contrôle d’accès, qui doit être gérée par l’utilisateur. Vous pouvez utiliser cette fonctionnalité avec vos clients Kafka en spécifiant **SASL_SSL** pour le protocole et **PLAIN** pour le mécanisme. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

#### <a name="samples"></a>Exemples 
Pour consulter un **tutoriel** avec des instructions pas à pas afin de créer un Event Hub et y accéder à l’aide d’une signature d’accès partagé ou OAuth, consultez [Démarrage rapide : Streaming de données avec Event Hubs en utilisant le protocole Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Pour plus d'**exemples** montrant comment utiliser OAuth avec Event Hubs pour Kafka, consultez les [exemples sur GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features-available-for-kafka"></a>Autres fonctionnalités Event Hubs disponibles pour Kafka

La fonctionnalité Event Hubs pour Kafka vous permet d’écrire avec un protocole et de lire avec un autre afin que vos producteurs Kafka actuels puissent continuer la publication via Kafka, et vous pouvez ajouter des lecteurs avec Event Hubs comme Azure Stream Analytics ou Azure Functions. De plus, les fonctionnalités Event Hubs telles que la [capture](event-hubs-capture-overview.md) et la [géoreprise d’activité après sinistre](event-hubs-geo-dr.md) fonctionnent également avec la fonctionnalité Event Hubs pour Kafka.

## <a name="features-that-are-not-yet-supported"></a>Fonctionnalités qui ne sont pas encore prises en charge 

Voici la liste des fonctionnalités de Kafka qui ne sont pas prises en charge :

*   Transaction
*   Compression
*   Rétention basée sur la taille
*   Compactage de journal
*   Prise en charge de l’API Kafka HTTP
*   Kafka Streams

## <a name="next-steps"></a>Étapes suivantes
Cet article a présenté une introduction à Event Hubs pour Kafka. Pour en savoir plus, consultez [Guide du développeur Apache Kafka pour Azure Event Hubs](apache-kafka-developer-guide.md).


