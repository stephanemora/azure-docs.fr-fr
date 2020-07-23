---
title: Jumeaux numériques et graphe de jumeaux
titleSuffix: Azure Digital Twins
description: Comprendre le concept de jumeau numérique et comment leurs relations aboutissent à un graphique.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 955a3b8d12eb3b93bc9d44c624953cd5c1007318
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86258203"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>Comprendre les jumeaux numériques et leur graphique de jumeaux

Dans une solution Azure Digital Twins, les entités de votre environnement sont représentées par des **jumeaux numériques** Azure. Un jumeau numérique est une instance de l’un de vos [modèles](concepts-models.md) personnalisés. Il peut être connecté à d’autres jumeaux numériques par le biais de **relations** pour former un **graphique de jumeaux** : ce graphique jumeau représente l’ensemble de votre environnement.

> [!TIP]
> « Azure Digital Twins » fait référence à ce service Azure dans son ensemble. « Jumeaux numériques » ou tout simplement « jumeau(s) » fait référence à des nœuds individuels au sein de votre instance du service.

## <a name="digital-twins"></a>Jumeaux numériques

Avant de pouvoir créer un jumeau numérique dans votre instance Azure Digital Twins, vous devez disposer d’un *modèle* chargé sur le service. Un modèle décrit, entre autres choses, l’ensemble des propriétés, des messages de télémétrie et des relations qu’un jumeau particulier peut avoir. Pour obtenir les types d’informations définis dans un modèle, consultez [Concepts : Modèles personnalisés](concepts-models.md).

Après avoir créé et chargé un modèle, votre application cliente peut créer une instance du type ; il s’agit d’un jumeau numérique. Par exemple, après la création d’un modèle *Étage*, vous pouvez créer un ou plusieurs jumeaux numériques qui utilisent ce type (par exemple, un jumeau de type *Étage* appelé *Rez-de-chaussée*, un autre appelé *Étage2*, etc.). 

## <a name="relationships-a-graph-of-digital-twins"></a>Relation : un graphique de jumeaux numériques

Les jumeaux sont connectés dans un graphique de jumeaux par leurs relations. Les relations qu’un jumeau peut avoir sont définies dans le cadre de son modèle.  

Par exemple, le modèle *Étage* peut définir une relation *contient* relation qui cible les jumeaux de type *Salle*. Avec cette définition, Azure Digital Twins vous permet de créer des relations *contient* entre n’importe quel jumeau *Étage* et n’importe quel jumeau *Pièce* (y compris les jumeaux qui sont des sous-types de *Salle*). 

Le résultat de ce processus est un ensemble de nœuds (les jumeaux numériques) connectés par leurs périphéries (leurs relations) dans un graphique.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-with-the-apis"></a>Création avec des API

Cette section montre à quoi ressemble la création de jumeaux numériques et de relations à partir d’une application cliente. Il contient des exemples de code .NET qui utilisent les [API DigitalTwins](how-to-use-apis-sdks.md), afin de fournir un contexte supplémentaire sur ce qui se passe à l’intérieur de chacun de ces concepts.

### <a name="create-digital-twins"></a>Créer des jumeaux numériques

Vous trouverez ci-dessous un extrait de code client qui utilise les [API DigitalTwins](how-to-use-apis-sdks.md) pour instancier un jumeau de type *Salle*.

Dans la préversion actuelle d’Azure Digital Twins, toutes les propriétés d’un jumeau doivent être initialisées pour que le jumeau puisse être créé. Pour ce faire, créez un document JSON qui fournit les valeurs d’initialisation nécessaires.

```csharp
public Task<boolean> CreateRoom(string id, double temperature, double humidity) 
{
    // Define the model for the twin to be created
    Dictionary<string, object> meta = new Dictionary<string, object>()
    {
      { "$model", "dtmi:com:contoso:Room;2" }
    };
    // Initialize the twin properties
    Dictionary<string, object> initData = new Dictionary<string, object>()
    {
      { "$metadata", meta },
      { "Temperature", temperature},
      { "Humidity", humidity},
    };
    try
    {
      await client.DigitalTwins.AddAsync(id, initData);
      return true;
    }
    catch (ErrorResponseException e)
    {
      Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}");
      return false;
    }
}
```

### <a name="create-relationships"></a>Créer des relations

Voici un exemple de code client qui utilise les [API DigitalTwins](how-to-use-apis-sdks.md) pour créer une relation entre un jumeau numérique de type *Étage*, appelé *Rez-de-chaussée*, et un jumeau numérique de type *Salle*, appelé *Café*.

```csharp
// Create Twins, using functions similar to the previous sample
await CreateRoom("Cafe", 70, 66);
await CreateFloor("GroundFloor", averageTemperature=70);
// Create relationships
Dictionary<string, object> targetrec = new Dictionary<string, object>()
{
    { "$targetId", "Cafe" }
};
try
{
    await client.DigitalTwins.AddEdgeAsync("GroundFloor", "contains", "GF-to-Cafe", targetrec);
} catch(ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating relationship: {e.Response.StatusCode}");
}
```

## <a name="json-representations-of-graph-elements"></a>Représentations JSON des éléments de graphique

Les données des jumeaux numériques et les données de relation sont stockées au format JSON. Cela signifie que lorsque vous [interrogez le graphique des jumeaux](how-to-query-graph.md) dans votre instance Azure Digital Twins, le résultat est une représentation JSON des jumeaux numériques et des relations que vous avez créées.

### <a name="digital-twin-json-format"></a>Format JSON de jumeaux numériques

Lorsqu’il est représenté sous la forme d’un objet JSON, un jumeau numérique affiche les champs suivants :

| Nom du champ | Description |
| --- | --- |
| `$dtId` | Chaîne fournie par l’utilisateur représentant l’ID du jumeau numérique |
| `$etag` | Champ HTTP standard attribué par le serveur web |
| `$conformance` | Énumération qui contient l’état de conformité de ce jumeau numérique (*conforme*, *non conforme*, *inconnu*) |
| `{propertyName}` | Valeur d’une propriété au format JSON (`string`, type de nombre ou objet) |
| `$relationships` | URL du chemin de la collection des relations. Ce champ est absent si le jumeau numérique n’a pas de périphéries de relations sortantes. |
| `$metadata.$model` | [Facultatif] l’ID de l’interface de modèle qui caractérise ce jumeau numérique |
| `$metadata.{propertyName}.desiredValue` | [Uniquement pour les propriétés accessibles en écriture] Valeur souhaitée de la propriété spécifiée |
| `$metadata.{propertyName}.desiredVersion` | [Uniquement pour les propriétés accessibles en écriture] Version de la valeur souhaitée |
| `$metadata.{propertyName}.ackVersion` | Version reconnue par l’application d’appareil qui implémente le jumeau numérique |
| `$metadata.{propertyName}.ackCode` | [Uniquement pour les propriétés accessibles en écriture] Code `ack` retourné par l’application d’appareil qui implémente le jumeau numérique |
| `$metadata.{propertyName}.ackDescription` | [Uniquement pour les propriétés accessibles en écriture] Description `ack` retournée par l’application d’appareil qui implémente le jumeau numérique |
| `{componentName}` | Objet JSON contenant les valeurs de propriété et les métadonnées du composant, similaires à celles de l’objet racine. Cet objet existe même si le composant n’a pas de propriétés. |
| `{componentName}.{propertyName}` | Valeur de la propriété du composant au format JSON (`string`, type de nombre ou objet) |
| `{componentName}.$metadata` | Informations de métadonnées pour le composant, similaires aux `$metadata` au niveau de la racine |

Voici un exemple de jumeau numérique sous la forme d’un objet JSON :

```json
{
  "$dtId": "Cafe",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
      "TableOccupancy": {
        "desiredValue": 1,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Room;1",
    "Temperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "Location": {
      "desiredValue": {
        "x": 101,
        "y": 33,
      },
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="relationship-json-format"></a>Format JSON de la relation

Lorsqu’elle est représentée sous la forme d’un objet JSON, une relation d’un jumeau numérique affiche les champs suivants :

| Nom du champ | Description |
| --- | --- |
| `$relationshipId` | Chaîne fournie par l’utilisateur représentant l’ID de cette relation. Cette chaîne est unique dans le contexte du jumeau numérique source, ce qui signifie également que `sourceId` + `relationshipId` est unique dans le contexte de l’instance Azure Digital Twins. |
| `$etag` | Champ HTTP standard attribué par le serveur web |
| `$sourceId` | ID du jumeau numérique source |
| `$targetId` | ID du jumeau numérique cible |
| `$relationshipName` | Nom de la relation |
| `{propertyName}` | [Facultatif] Valeur d’une propriété de cette relation, au format JSON (`string`, type de nombre ou objet) |

Voici un exemple de relation sous la forme d’un objet JSON :

```json
{
  "$relationshipId": "relationship-01",
  "$etag": "W/\"506e8391-2b21-4ac9-bca3-53e6620f6a90\"",
  "$sourceId": "GroundFloor",
  "$targetId": "Cafe",
  "$relationshipName": "contains",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>Étapes suivantes

Voir comment gérer des éléments graphiques avec les API Azure Digital Twins :
* [Guide pratique pour gérer des jumeaux numériques](how-to-manage-twin.md)
* [Guide pratique pour gérer le graphe de jumeaux avec des relations](how-to-manage-graph.md)

Ou découvrez comment interroger le graphe de jumeaux Azure Digital Twins pour obtenir des informations :
* [Concepts : langage de requête](concepts-query-language.md)