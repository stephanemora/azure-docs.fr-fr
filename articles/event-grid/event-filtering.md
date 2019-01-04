---
title: Filtrage d’événements pour Azure Event Grid
description: Explique comment filtrer des événements lors de la création d’un abonnement Azure Event Grid.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: tomfitz
ms.openlocfilehash: 973b5d3f40968790dc6153ae91d1638cb83202ee
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384375"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Comprendre le filtrage d’événements pour les abonnements Event Grid

Cet article décrit les différentes façons de filtrer les événements qui sont envoyés à votre point de terminaison. Quand vous créez un abonnement à un événement, trois options de filtrage s’offrent à vous :

* Types d’événements
* Objet commençant ou se terminant par
* Opérateurs et champs avancés

## <a name="event-type-filtering"></a>Filtrage par type d’événement

Par défaut, tous les [types d’événements](event-schema.md) pour la source de l’événement sont envoyés au point de terminaison. Vous pouvez décider d’envoyer uniquement certains types d’événements à votre point de terminaison. Par exemple, vous pouvez être notifié des mises à jour de vos ressources, mais ne pas être notifié d’autres opérations comme les suppressions. Dans ce cas, effectuez le filtrage par type d’événement `Microsoft.Resources.ResourceWriteSuccess`. Fournissez un tableau avec les types d’événements, ou spécifiez `All` pour obtenir tous les types d’événements de la source de l’événement.

La syntaxe JSON pour le filtrage par type d’événement est :

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Filtrage par objet

Pour le filtrage simple par objet, spécifiez une valeur de départ ou de fin pour l’objet. Par exemple, vous pouvez spécifier que l’objet se termine par `.txt` pour obtenir uniquement les événements liés au chargement d’un fichier texte sur le compte de stockage. Ou vous pouvez filtrer sur l’objet commençant par `/blobServices/default/containers/testcontainer` pour obtenir tous les événements pour ce conteneur, mais pas pour d’autres conteneurs du compte de stockage.

Lorsque vous publiez des événements dans des rubriques personnalisées, créez des objets pour vos événements permettant aux abonnés de savoir facilement si l’événement les intéresse. Les abonnés utilisent la propriété d’objet pour filtrer et router des événements. Envisagez d’ajouter le chemin à l’origine de l’événement, de sorte que les abonnés puissent filtrer sur des segments de ce chemin. Le chemin d’accès permet aux abonnés de filtrer les événements avec précision ou à grande échelle. Si vous fournissez un chemin de trois segments comme `/A/B/C` dans l’objet, les abonnés peuvent filtrer sur le premier segment `/A` pour obtenir un vaste ensemble d’événements. Ces abonnés obtiennent des événements avec des objets tels que `/A/B/C` ou `/A/D/E`. Les autres abonnés peuvent filtrer par `/A/B` pour obtenir un ensemble plus restreint d’événements.

La syntaxe JSON pour le filtrage par type d’événement est :

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Filtrage avancé

Pour filtrer sur des valeurs dans les champs de données et spécifier l’opérateur de comparaison, utilisez l’option de filtrage avancé. Dans le filtrage avancé, vous spécifiez les éléments suivants :

* type d'opérateur : type de comparaison.
* clé : champ des données d’événement que vous utilisez pour le filtrage. Ce peut être un nombre, une valeur booléenne ou une chaîne.
* valeur ou valeurs : valeur ou valeurs à comparer à la clé.

La syntaxe JSON pour utiliser des filtres avancés est la suivante :

```json
"filter": {
  "advancedFilters": [
    {
      "operatorType": "NumberGreaterThanOrEquals",
      "key": "Data.Key1",
      "value": 5
    },
    {
      "operatorType": "StringContains",
      "key": "Subject",
      "values": ["container1", "container2"]
    }
  ]
}
```

### <a name="operator"></a>Operator

Les opérateurs disponibles pour les nombres sont les suivants :

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

L’opérateur disponible pour les valeurs booléennes est : BoolEquals

Les opérateurs disponibles pour les chaînes sont les suivants :

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Aucune des comparaisons de chaînes n’est sensible à la casse.

### <a name="key"></a>Clé

Pour les événements dans le schéma Event Grid, utilisez les valeurs suivantes pour la clé :

* ID
* Rubrique
* Objet
* Type d’événement
* DataVersion
* Données d’événement (comme Data.key1)

Pour les événements dans le schéma des événements cloud, utilisez les valeurs suivantes pour la clé :

* EventId
* Source
* Type d’événement
* EventTypeVersion
* Données d’événement (comme Data.key1)

Pour le schéma d’entrée personnalisé, utilisez les champs de données d’événement (comme Data.key1).

### <a name="values"></a>Valeurs

Les valeurs peuvent être les suivantes :

* number
* chaîne
* booléenne
* array

### <a name="limitations"></a>Limites

Le filtrage avancé présente les limites suivantes :

* Cinq filtres avancés par abonnement Event Grid
* 512 caractères par valeur de type chaîne
* Cinq valeurs pour les opérateurs **dans** et **pas dans**
* La clé ne peut avoir qu'un niveau d’imbrication (comme data.key1)

La même clé peut être utilisée dans plusieurs filtres.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur le filtrage d’événements avec PowerShell et Azure CLI, consultez [Filtrer des événements pour Event Grid](how-to-filter-events.md).
* Pour une prise en main rapide d’Event Grid, consultez [Créer et acheminer des événements personnalisés avec Azure Event Grid](custom-event-quickstart.md).
