---
title: Ajouter des étiquettes à des jumeaux numériques
titleSuffix: Azure Digital Twins
description: Découvrez comment implémenter des étiquettes sur des jumeaux numériques
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0d45ef8c32e61b5567798b7c42af28badb222601
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376713"
---
# <a name="add-tags-to-digital-twins"></a>Ajouter des étiquettes à des jumeaux numériques 

Vous pouvez utiliser le concept d’étiquettes pour identifier et catégoriser davantage vos jumeaux numériques. En particulier, les utilisateurs peuvent être amenés à répliquer des étiquettes à partir de systèmes existants, tels que les [étiquettes Haystack](https://project-haystack.org/doc/TagModel), dans leurs instances Azure Digital Twins. 

Ce document décrit les modèles qui peuvent être utilisés pour implémenter des étiquettes sur des jumeaux numériques.

Les étiquettes sont d’abord ajoutées en tant que propriétés dans le [modèle](concepts-models.md) qui décrit un jumeau numérique. Cette propriété est ensuite définie sur le jumeau lorsqu’il est créé en fonction du modèle. Après cela, les étiquettes peuvent être utilisées dans les [requêtes](concepts-query-language.md) pour identifier et filtrer vos jumeaux.

## <a name="marker-tags"></a>Étiquettes de marqueur 

Une **étiquette de marqueur** est une chaîne simple utilisée pour marquer ou catégoriser un jumeau numérique, telle que « bleu » ou « rouge ». Cette chaîne est le nom de l’étiquette et les étiquettes de marqueur n’ont pas de valeur significative : l’étiquette est significative par sa seule présence (ou absence). 

### <a name="add-marker-tags-to-model"></a>Ajouter des étiquettes de marqueur à un modèle 

Les balises de marqueur sont modélisées sous la forme d’un mappage [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) de `string` en `boolean`. La valeur booléenne `mapValue` est ignorée, car la présence de la balise est tout ce qui est importe. 

Voici un extrait d’un modèle de jumeau qui implémente une balise de marqueur en tant que propriété :

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="2-16":::

### <a name="add-marker-tags-to-digital-twins"></a>Ajouter des balises de marqueur à des jumeaux numériques

Une fois que la propriété `tags` fait partie d’un modèle de jumeau numérique, vous pouvez définir la balise de marqueur dans le jumeau numérique en définissant la valeur de cette propriété. 

Voici un exemple qui remplit la propriété `tags` de marqueur pour trois jumeaux :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesMarker":::

Voici un exemple de code permettant de définir le marqueur `tags` d'un jumeau à l'aide du [kit de développement logiciel (SDK) .NET](/dotnet/api/overview/azure/digitaltwins/client) :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesCsharp":::

### <a name="query-with-marker-tags"></a>Requête avec des balises de marqueur

Une fois que des balises ont été ajoutées aux jumeaux numériques, les balises peuvent être utilisées pour filtrer les jumeaux dans des requêtes. 

Voici une requête permettant d’obtenir tous les jumeaux qui sont rouges (marqués de la balise « red ») : 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Vous pouvez également combiner des balises pour créer des requêtes plus complexes. Voici une requête permettant d’obtenir tous les jumeaux qui sont ronds et pas rouges : 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags2":::

## <a name="value-tags"></a>Balises de valeur 

Une **balise de valeur** est une paire clé-valeur utilisée pour attribuer une valeur à chaque balise, telle que `"color": "blue"` ou `"color": "red"`. Une fois qu’une balise de valeur a été créée, vous pouvez également l’utiliser comme balise de marqueur en ignorant sa valeur. 

### <a name="add-value-tags-to-model"></a>Ajouter des balises de valeur à un modèle 

Les balises de valeur sont modélisées sous la forme d’un mappage [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) de `string` en `string`. Les éléments `mapKey` et `mapValue` sont tous les deux significatifs. 

Voici un extrait d’un modèle de jumeau qui implémente une balise de valeur en tant que propriété :

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="17-31":::

### <a name="add-value-tags-to-digital-twins"></a>Ajouter des balises de valeur à des jumeaux numériques

Comme avec les balises de marqueur, vous pouvez définir la balise de valeur dans un jumeau numérique en définissant la valeur de cette propriété `tags` à partir du modèle. Pour utiliser une balise de valeur comme balise de marqueur, vous pouvez définir le champ `tagValue` sur la valeur de chaîne vide (`""`). 

Voici un exemple qui remplit la propriété `tags` de valeur pour trois jumeaux :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesValue":::

Notez que `red` et `purple` sont utilisés comme balises de marqueur dans cet exemple.

### <a name="query-with-value-tags"></a>Requête avec des balises de valeur

Comme avec les balises de marqueur, vous pouvez utiliser des balises de valeur pour filtrer les jumeaux dans des requêtes. Vous pouvez également utiliser des balises de valeur et des balises de marqueur ensemble.

Dans l’exemple ci-dessus, `red` est utilisé en tant que balise de marqueur. Voici une requête permettant d’obtenir tous les jumeaux qui sont rouges (marqués de la balise « red ») : 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Voici une requête permettant d’obtenir toutes les entités qui sont de petite taille (balise de valeur) et pas rouges : 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerValueTags":::

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus d’informations sur la conception et la gestion de modèles de jumeaux numériques :
* [*Guide pratique : Gérer les modèles DTDL*](how-to-manage-model.md)

Découvrez plus d’informations sur l’interrogation du graphe de jumeaux :
* [*Guide pratique : Interroger le graphe de jumeaux*](how-to-query-graph.md)
