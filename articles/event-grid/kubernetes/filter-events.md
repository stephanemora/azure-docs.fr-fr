---
title: Azure Event Grid sur Kubernetes – Filtrer des événements
description: Cet article explique comment filtrer des événements lors de la création d’un abonnement Azure Event Grid.
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: e4dbdbb2f46546391b6e749a94bfa655bb9de45e
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112417340"
---
# <a name="event-grid-on-kubernetes---event-filtering-for-subscriptions"></a>Event Grid sur Kubernetes – Filtrage d’événements pour des abonnements
Event Grid sur Kubernetes permet de spécifier des filtres sur n’importe quelle propriété de la charge utile json. Ces filtres sont modélisés sous la forme d’un ensemble de conditions AND, chaque condition externe comportant des conditions OR internes facultatives. Pour chaque condition AND, vous spécifiez les valeurs suivantes :

- OperatorType : type de comparaison.
- Key : chemin JSON de la propriété sur laquelle appliquer le filtre.
- Value : valeur de référence du filtre (ou) Values : ensemble de valeurs de référence du filtre.

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]



## <a name="filter-by-event-type"></a>Filtrer par type d’événement
Par défaut, tous les [types d’événements](event-schemas.md) (attribut `type`) pour la source de l’événement sont envoyés au point de terminaison. Vous pouvez décider d’envoyer uniquement certains types d’événements à votre point de terminaison. La syntaxe JSON pour le filtrage par type d’événement est :

```json
"filter": {
  "includedEventTypes": [
    "orderCreated",
    "orderUpdated"
  ]
}
```

Dans l’exemple ci-dessus, les seuls événements de type `orderCreated` et les événements `orderUpdated` sont envoyés au point de terminaison de l’abonné. 

Voici un exemple d’événement :

```json
[{
      "specVersion": "1.0",
      "type" : "orderCreated",
      "source": "myCompanyName/us/webCommerceChannel/myOnlineCommerceSiteBrandName",
      "id" : "eventId-n",
      "time" : "2020-12-25T20:54:07+00:00",
      "subject" : "account/acct-123224/order/o-123456",
      "dataSchema" : "1.0",
      "data" : {
         "orderId" : "123",
         "orderType" : "PO",
         "reference" : "https://www.myCompanyName.com/orders/123"
      }
}]
```

## <a name="filter-by-subject"></a>Filtrer par objet
Pour le filtrage simple par objet, spécifiez une valeur de départ ou de fin pour l’objet. La syntaxe JSON pour le filtrage par objet est :

```json
"filter": {
  "subjectBeginsWith": "/account/acct-123224/"
}
``` 

Par exemple, le filtre configuré ci-dessus envoie toutes les commandes associées au compte `acct-123224` au point de terminaison de l’abonné. 

Lorsque vous publiez des événements dans des rubriques, créez des objets pour vos événements de manière à permettre aux abonnés de savoir facilement si l’événement les intéresse. Les abonnés utilisent la propriété d’objet pour filtrer et router des événements. Envisagez d’ajouter le chemin d’origine de l’événement, de sorte que les abonnés puissent filtrer sur des segments de ce chemin. Le chemin d’accès permet aux abonnés de filtrer les événements avec précision ou à grande échelle. Si vous fournissez un chemin de trois segments, tel /A/B/C, dans l’objet, les abonnés peuvent filtrer sur le premier segment, /A, pour obtenir un vaste ensemble d’événements. Ces abonnés obtiennent des événements avec des objets tels que /A/B/C ou /A/D/E. Les autres abonnés peuvent filtrer sur /A/B pour obtenir un ensemble plus restreint d’événements.

## <a name="filter-by-values-in-event-data"></a>Filtrer sur des valeurs dans des données d’événement
Pour en savoir plus sur le filtrage avancé, consultez la [section Filtrage avancé de l’article Event Grid sur Azure](../event-filtering.md). Event Grid sur Kubernetes ne prend pas en charge les fonctionnalités et opérateurs suivants. 

- Filtrage sur des données de tableau dans des clés d’événements entrants
- Autorisez le filtrage sur les [attributs de contexte d’extensions CloudEvents](https://github.com/cloudevents/spec/blob/v1.0/documented-extensions.md).
- Opérateurs suivants
    - StringNotContains
    - StringNotBeginsWith
    - StringNotEndsWith
    - NumberInRange
    - NumberNotInRange
    - IsNullOrUndefined
    - IsNotNull
    

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les destinations et les gestionnaires pris en charge par Event Grid sur Azure Arc pour Kubernetes, consultez [Event Grid sur Kubernetes - Gestionnaires d’événements](event-handlers.md).