---
title: Domaines d’événements dans Azure Event Grid
description: Décrit comment utiliser les domaines d’événements pour gérer les rubriques dans Azure Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: fe66ca8b8f5b4474290e302f73b35868dce68caa
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669326"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Comprendre les domaines d’événements pour gérer les rubriques Event Grid

Cet article décrit comment utiliser les domaines d’événements pour gérer le flux d’événements personnalisés vers vos différentes unités organisationnelles, vos clients ou vos applications. Utilisez les domaines d’événements pour :

* Gérer des architectures d’événements à grande échelle avec abonnés multiples.
* Gérer les authentifications et les autorisations.
* Partitionner les sujets sans avoir à les gérer de façon individuelle.
* Éviter de publier les sujets un par un dans chaque point de terminaison.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]


## <a name="event-domains-overview"></a>Vue d’ensemble des domaines d’événements

Un domaine d’événements est un outil de gestion pour un nombre élevé de rubriques Event Grid relatives à une même application. Vous pouvez le considérer comme une méta-rubrique pouvant contenir des milliers de rubriques individuelles.

Les domaines d’événements créent l’architecture des services Azure, comme le service de stockage Azure et IoT Hub, utilisée pour la publication des événements disponibles afin que vous puissiez les utiliser dans votre propre système. Ils vous permettent de publier des événements dans des milliers de rubriques. Les domaines vous donnent également le contrôle sur l’authentification et l’autorisation sur chaque rubrique pour que vous puissiez partitionner vos abonnés.

### <a name="example-use-case"></a>Exemple de cas d’usage

Il est plus simple de partir d’un exemple pour expliquer les domaines d’événements. Disons que votre société, Contoso Construction Machinery, produit des tracteurs, du matériel d’excavation et autres machineries lourdes. Dans le cadre de la gestion de votre entreprise, vous envoyez des informations en temps réel à vos clients sur la maintenance des machines, l’état des systèmes et les mises à jour de vos contrats, etc. Tout ceci est envoyé vers différents points de terminaison, par exemple votre application, les points de terminaison côté client ou toute autre infrastructure que les clients ont mis en place.

Les domaines d’événements vous permettent de modéliser Contoso Construction Machinery en une unique entité source d’événements. Chaque client est représenté par une rubrique dans le domaine et l’authentification et les autorisations sont gérées via Azure Active Directory. Chacun de vos clients peut s’abonner à sa rubrique et recevoir ses événements. L’accès géré via le domaine d’événements garantit qu’ils n’ont accès qu’à leur rubrique.

Il vous donne également un point de terminaison unique, dans lequel vous pouvez publier l’ensemble des événements du client. Event Grid fera en sorte que chaque rubrique prenne uniquement en compte les événements qui concernent son abonné.

![Exemple Contoso Construction](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>gestion de l’accès

 Avec un domaine, vous bénéficiez d’autorisations détaillées et du contrôle de l’authentification sur chaque rubrique via la vérification d’accès par rôle (RBAC) d’Azure. Vous pouvez vous servir de ces rôles pour limiter l’accès de chaque abonné dans votre application aux rubriques que souhaitez uniquement.

La vérification d’accès par rôle (RBAC) dans les domaines d’événements fonctionne de la même façon que [Contrôle d’accès gérés](https://docs.microsoft.com/azure/event-grid/security-authentication#management-access-control) dans le reste de Event Grid et Azure. Utilisez la vérification d’accès par rôle (RBAC) pour créer et appliquer des définitions de rôles personnalisés dans les domaines d’événements.

### <a name="built-in-roles"></a>Vérification d’accès par rôle : rôles intégrés

Event Grid possède deux définitions de rôles intégrés pour faciliter la vérification d’accès par rôle :

#### <a name="eventgrid-eventsubscription-contributor-preview"></a>EventGrid EventSubscription Contributeur (préversion)

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

#### <a name="eventgrid-eventsubscription-reader-preview"></a>EventGrid EventSubscription Lecteur (préversion)

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

## <a name="subscribing-to-topics"></a>S’abonner aux rubriques

S’abonner aux événements d’une rubrique dans un domaine d’événements revient à [crée un abonnement à un événement dans une rubrique personnalisée](./custom-event-quickstart.md) ou s’abonner à un des éditeurs d’événements intégrés qu’Azure propose.

### <a name="domain-scope-subscriptions"></a>Abonnements à l’étendue de domaine

Les domaines d’événements permettent également des abonnements à l’étendue de domaine. Un abonnement aux événements d’un domaine d’événements recevra l’ensemble des événements envoyés au domaine, quelle que soit la rubrique dans laquelle les événements sont envoyés. Les abonnements à l’étendue de domaine peuvent être utiles à des fins de gestion et d’audit.

## <a name="publishing-to-an-event-domain"></a>Publier dans un domaine d’événements

Lorsque vous créez un domaine d’événements, vous recevez un point de terminaison de publication identique à celui que vous auriez reçu si vous aviez créé une rubrique dans Event Grid. 

Pour publier des événements dans une rubrique d’un domaine d’événements, envoyez les événements vers le point de terminaison du domaine de la [même manière que vous le feriez pour une rubrique personnalisée](./post-to-custom-topic.md). La seule différence est que vous devez spécifier la rubrique dans laquelle vous souhaitez envoyer l’événement.

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

Les domaines d’événements gèrent la publication des rubriques à votre place. Plutôt que de publier vos événements un par un dans chaque rubrique, vous pouvez publier l’ensemble de vos événements dans le point de terminaison du domaine et Event Grid s’assure que chaque événement est envoyé vers la bonne rubrique.

## <a name="limits-and-quotas"></a>Limites et quotas

### <a name="control-plane"></a>Plan de contrôle

Lors de l’aperçu, les domaines d’événements seront limités à 1 000 rubriques dans un domaine et 50 abonnements aux événements par rubrique dans un domaine. Les abonnements à l’étendue de domaine d’événements seront également limités à 50.

### <a name="data-plane"></a>Plan de données

Lors de l’aperçu, le débit d’événements pour un domaine d’événement sera limité aux mêmes 5 000 événements par seconde de taux d’ingestion auxquels les rubriques personnalisées sont limitées.

## <a name="pricing"></a>Tarifs

Lors de l’aperçu, les domaines d’événements utiliseront les mêmes [opérations de tarification](https://azure.microsoft.com/pricing/details/event-grid/) que toutes les autres fonctionnalités dans Event Grid.

Ces opérations fonctionnent de la même manière dans les domaines d’événements que dans les rubriques personnalisées. Chaque entrée d’un événement dans un domaine d’événements est une opération et chaque tentative de livraison d’un événement est aussi une opération.

## <a name="next-steps"></a>Étapes suivantes

* Pour découvrir comment configurer les domaines d’événements, créer des rubriques, créer des abonnements à des événements et publier des événements, consultez [Gérer les domaines d’événements](./how-to-event-domains.md).