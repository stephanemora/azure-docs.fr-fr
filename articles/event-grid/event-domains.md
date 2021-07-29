---
title: Domaines d’événements dans Azure Event Grid
description: Cet article décrit comment utiliser les domaines d’événements pour gérer le flux d’événements personnalisés vers vos organisations, clients ou applications.
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 78a785d3f1ee0431b11e8c14c3e48f4a156b5fd4
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110537782"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Comprendre les domaines d’événements pour gérer les rubriques Event Grid

Cet article décrit comment utiliser les domaines d’événements pour gérer le flux d’événements personnalisés vers vos organisations, clients ou applications. Utilisez des domaines d’événements pour effectuer les opérations suivantes :

* Gérer des architectures d’événements à grande échelle avec abonnés multiples.
* Gérer les authentifications et les autorisations.
* Partitionner les sujets sans avoir à les gérer de façon individuelle.
* Éviter de publier les sujets un par un dans chaque point de terminaison.

## <a name="event-domain-overview"></a>Vue d’ensemble des domaine d’événements

Un domaine d’événements est un outil de gestion pour un nombre élevé de rubriques Event Grid relatives à la même application. Vous pouvez le considérer comme une méta-rubrique pouvant compter des milliers de rubriques individuelles.

Les domaines d’événements vous fournissent la même architecture que celle qu’utilisent les services Azure (par exemple, Stockage et IoT Hub) pour publier leurs événements. Ils vous permettent de publier des événements dans des milliers de rubriques. Les domaines vous donnent également le contrôle sur l’authentification et l’autorisation sur chaque rubrique pour que vous puissiez partitionner vos abonnés.

## <a name="example-use-case"></a>Exemple de cas d’usage
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="access-management"></a>Gestion de l’accès

Avec un domaine, vous bénéficiez du contrôle détaillé des autorisations et de l’authentification sur chaque rubrique via le contrôle d’accès en fonction du rôle Azure (Azure RBAC). Vous pouvez vous servir de ces rôles pour limiter l’accès de chaque abonné dans votre application aux rubriques que souhaitez uniquement.

Azure RBAC dans les domaines d’événements fonctionne de la même façon que le [contrôle d’accès géré](security-authorization.md) dans le reste d’Event Grid et d’Azure. Utilisez Azure RBAC pour créer et appliquer des définitions de rôles personnalisés dans des domaines d’événements.

### <a name="built-in-roles"></a>Vérification d’accès par rôle : rôles intégrés

Event Grid possède deux définitions de rôles intégrées pour faciliter l’utilisation d’Azure RBAC avec des domaines d’événements. Ces rôles sont **EventGrid EventSubscription Contributor (préversion)** et **EventGrid EventSubscription Reader (préversion)** . Vous affectez ces rôles aux utilisateurs qui doivent s’abonner à des rubriques dans votre domaine d’événements. Vous étendez l’attribution de rôle uniquement à la rubrique à laquelle les utilisateurs doivent s’abonner.

Pour plus d’informations sur ces rôles, voir [Rôles intégrés pour Event Grid](security-authorization.md#built-in-roles).

## <a name="subscribing-to-topics"></a>S’abonner aux rubriques

S’abonner aux événements d’une rubrique dans un domaine d’événements revient à [créer un abonnement à un événement dans une rubrique personnalisée](./custom-event-quickstart.md) ou à s’abonner à un événement d’un service Azure.

> [!IMPORTANT]
> La rubrique de domaine est considérée comme une ressource **managée automatiquement** dans Event Grid. Vous pouvez créer un abonnement aux événements au niveau de [l’étendue de domaine](#domain-scope-subscriptions) sans créer la rubrique de domaine. Dans ce cas, Event Grid crée automatiquement la rubrique de domaine en votre nom. Bien entendu, vous pouvez toujours choisir de créer la rubrique de domaine manuellement. Ce comportement vous permet de vous préoccuper d’une ressource en moins lorsque vous travaillez avec un grand nombre de rubriques de domaine. Lorsque le dernier abonnement à une rubrique de domaine est supprimé, la rubrique domaine est également supprimée, peu importe qu’elle ait été créée manuellement ou automatiquement. 

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
Voici les limites et quotas liés aux domaines d'événements :

- 100 000 rubriques par domaine d’événements 
- 100 domaines d’événements par abonnement Azure 
- 500 abonnements à des événements par rubrique dans un domaine d’événements
- 50 abonnements à l’étendue de domaine 
- Taux d'ingestion de 5 000 événements par seconde (dans un domaine)

Si ces limites ne vous conviennent pas, ouvrez un ticket de support ou envoyez un e-mail à [askgrid@microsoft.com](mailto:askgrid@microsoft.com). 

## <a name="pricing"></a>Tarifs
Les domaines d’événements utilisent la même [tarification des opérations](https://azure.microsoft.com/pricing/details/event-grid/) que toutes les autres fonctionnalités dans Event Grid.

Les opérations fonctionnent de la même manière dans des domaines d’événements que dans des rubriques personnalisées. Chaque entrée d’un événement dans un domaine d’événements est une opération, tout comme chaque tentative de livraison d’un événement.



## <a name="next-steps"></a>Étapes suivantes

* Pour découvrir comment configurer des domaines d’événements, créer des rubriques, créer des abonnements à des événements et publier des événements, voir [Gérer les domaines d’événements](./how-to-event-domains.md).
