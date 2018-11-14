---
title: Domaines d’événements dans Azure Event Grid
description: Décrit comment utiliser les domaines d’événements pour gérer les rubriques dans Azure Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 11/01/2018
ms.openlocfilehash: b5a1e521f46da7feffd2eb556ad1662a9ccaee55
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913834"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Comprendre les domaines d’événements pour gérer les rubriques Event Grid

Cet article décrit comment utiliser les domaines d’événements pour gérer le flux d’événements personnalisés vers vos organisations, clients ou applications. Utilisez des domaines d’événements pour effectuer les opérations suivantes :

* Gérer des architectures d’événements à grande échelle avec abonnés multiples.
* Gérer les authentifications et les autorisations.
* Partitionner les sujets sans avoir à les gérer de façon individuelle.
* Éviter de publier les sujets un par un dans chaque point de terminaison.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="event-domain-overview"></a>Vue d’ensemble des domaine d’événements

Un domaine d’événements est un outil de gestion pour un nombre élevé de rubriques Event Grid relatives à la même application. Vous pouvez le considérer comme une méta-rubrique pouvant compter des milliers de rubriques individuelles.

Les domaines d’événements mettent à votre disposition la même architecture que celle qu’utilisent les services Azure (par exemple, Stockage et IoT Hub) pour publier leurs événements. Ils vous permettent de publier des événements dans des milliers de rubriques. Les domaines vous donnent également le contrôle sur l’authentification et l’autorisation sur chaque rubrique pour que vous puissiez partitionner vos abonnés.

### <a name="example-use-case"></a>Exemple de cas d’usage

Il est plus simple de partir d’un exemple pour expliquer les domaines d’événements. Disons que votre société, Contoso Construction Machinery, produit des tracteurs, du matériel d’excavation et autres machineries lourdes. Dans le cadre de la gestion de votre entreprise, vous envoyez des informations en temps réel à vos clients sur la maintenance des machines, l’état des systèmes et les mises à jour de vos contrats. Tout ceci est envoyé vers différents points de terminaison, dont votre application, des points de terminaison côté client ou toute autre infrastructure que des clients ont mise en place.

Les domaines d’événements vous permettent de modéliser Contoso Construction Machinery en une unique entité source d’événements. Chacun de vos clients est représenté en tant que rubrique au sein du domaine. L’authentification et l’autorisation sont gérées à l’aide d’Azure Active Directory. Chacun de vos clients peut s’abonner à sa rubrique et recevoir ses événements. L’accès géré via le domaine d’événements garantit qu’ils n’ont accès qu’à leur rubrique.

Il vous donne également un point de terminaison unique, dans lequel vous pouvez publier l’ensemble des événements du client. Event Grid veille à ce que chaque rubrique prenne uniquement en compte les événements qui concernent son abonné.

![Exemple Contoso Construction](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>gestion de l’accès

Avec un domaine, vous bénéficiez d’autorisations détaillées et du contrôle de l’authentification sur chaque rubrique via le contrôle d’accès en fonction du rôle (RBAC) d’Azure. Vous pouvez vous servir de ces rôles pour limiter l’accès de chaque abonné dans votre application aux rubriques que souhaitez uniquement.

Le contrôle d’accès en fonction du rôle (RBAC) dans les domaines d’événements fonctionne de la même façon que le [contrôle d’accès géré](security-authentication.md#management-access-control) dans le reste d’Event Grid et d’Azure. Utilisez le contrôle d’accès en fonction du rôle (RBAC) pour créer et appliquer des définitions de rôles personnalisés dans des domaines d’événements.

### <a name="built-in-roles"></a>Vérification d’accès par rôle : rôles intégrés

Event Grid possède deux définitions de rôles intégrées pour faciliter l’utilisation du contrôle d’accès en fonction du rôle (RBAC) avec des domaines d’événements. Ces rôles sont **EventGrid EventSubscription Contributor (préversion)** et **EventGrid EventSubscription Reader (préversion)**. Vous affectez ces rôles aux utilisateurs qui doivent s’abonner à des rubriques dans votre domaine d’événements. Vous étendez l’attribution de rôle uniquement à la rubrique à laquelle les utilisateurs doivent s’abonner.

Pour plus d’informations sur ces rôles, voir [Rôles intégrés pour Event Grid](security-authentication.md#built-in-roles).

## <a name="subscribing-to-topics"></a>S’abonner aux rubriques

S’abonner aux événements d’une rubrique dans un domaine d’événements revient à [créer un abonnement à un événement dans une rubrique personnalisée](./custom-event-quickstart.md) ou à s’abonner à un événement d’un service Azure.

### <a name="domain-scope-subscriptions"></a>Abonnements à l’étendue de domaine

Les domaines d’événements permettent également des abonnements à l’étendue de domaine. Un abonnement aux événements d’un domaine d’événements recevra l’ensemble des événements envoyés au domaine, quelle que soit la rubrique à laquelle les événements sont envoyés. Les abonnements à l’étendue de domaine peuvent être utiles à des fins de gestion et d’audit.

## <a name="publishing-to-an-event-domain"></a>Publier dans un domaine d’événements

Lorsque vous créez un domaine d’événements, vous recevez un point de terminaison de publication identique à celui que vous auriez reçu si vous aviez créé une rubrique dans Event Grid. 

Pour publier des événements dans une rubrique d’un domaine d’événements, envoyez-les au point de terminaison du domaine de la [même manière que vous le feriez pour une rubrique personnalisée](./post-to-custom-topic.md). La seule différence est que vous devez spécifier la rubrique dans laquelle vous souhaitez envoyer l’événement.

Par exemple, publier le tableau d’événements suivant enverrait les événements avec `"id": "1111"` dans la rubrique `foo` alors que l’événement avec `"id": "2222"` serait envoyé dans la rubrique `bar` :

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Les domaines d’événements gèrent la publication des rubriques à votre place. Au lieu de publier vos événements un par un dans chaque rubrique que vous gérez, vous pouvez les publier tous dans le point de terminaison du domaine. Event Grid s’assure que chaque événement est envoyé à la rubrique appropriée.

## <a name="limits-and-quotas"></a>Limites et quotas

### <a name="control-plane"></a>Plan de contrôle

Durant la période de préversion, les domaines d’événements seront limités à 1 000 rubriques par domaine et à 50 abonnements aux événements par rubrique au sein d’un domaine. Les abonnements à l’étendue de domaine d’événements sont également limités à 50.

### <a name="data-plane"></a>Plan de données

Durant la période de préversion, le débit d’événements pour un domaine d’événement sera limité au même taux d’ingestion de 5 000 événements par seconde que les rubriques personnalisées.

## <a name="pricing"></a>Tarifs

Durant la période de préversion, les domaines d’événements utiliseront la même [tarification des opérations](https://azure.microsoft.com/pricing/details/event-grid/) que toutes les autres fonctionnalités dans Event Grid.

Les opérations fonctionnent de la même manière dans des domaines d’événements que dans des rubriques personnalisées. Chaque entrée d’un événement dans un domaine d’événements est une opération, tout comme chaque tentative de livraison d’un événement.

## <a name="next-steps"></a>Étapes suivantes

* Pour découvrir comment configurer des domaines d’événements, créer des rubriques, créer des abonnements à des événements et publier des événements, voir [Gérer les domaines d’événements](./how-to-event-domains.md).