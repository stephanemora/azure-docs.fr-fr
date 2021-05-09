---
title: Langage de requête
titleSuffix: Azure Digital Twins
description: Découvrez les principes de base du langage des requêtes Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 4/22/2021
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 13bf3b8b8756fcc83211b02379980972629d9438
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108290539"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>À propos du langage de requête pour Azure Digital Twins

N’oubliez pas que le centre d’Azure Digital Twins est le [graphe de jumeaux](concepts-twins-graph.md) construit à partir des jumeaux numériques et des relations. 

Ce graphique peut être interrogé pour obtenir des informations sur les jumeaux numériques et les relations qu’il contient. Ces requêtes sont écrites dans un langage de requête de type SQL personnalisé, appelé **langage des requêtes Azure Digital Twins**. Ce langage est similaire au [langage de requête IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) par ses nombreuses fonctionnalités comparables.

Cet article décrit les principes fondamentaux du langage de requête et de ses fonctionnalités. Pour obtenir des exemples plus détaillés de syntaxe de requête et savoir comment exécuter des requêtes, consultez [Guide pratique : Interroger le graphique de jumeaux](how-to-query-graph.md).

## <a name="about-the-queries"></a>À propos des requêtes

Vous pouvez utiliser le langage de requête Azure Digital Twins pour récupérer des jumeaux numériques en fonction de leurs...
* leurs propriétés (y compris les [propriétés de balise](how-to-use-tags.md))
* modèles
* relationships
  - leurs propriétés des relations

Pour soumettre une requête au service à partir d’une application cliente, vous allez utiliser l [’API de requête](/rest/api/digital-twins/dataplane/query) Azure Digital Twins. L’une des façons d’utiliser l’API consiste à utiliser l’un des [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis) pour Azure Digital Twins.

[!INCLUDE [digital-twins-query-reference.md](../../includes/digital-twins-query-reference.md)]

## <a name="considerations-for-querying"></a>Considérations liées à l’interrogation

Quand vous écrivez des requêtes pour Azure Digital Twins, gardez à l’esprit les points suivants :
* **Penser au respect de la casse** : toutes les opérations de requête Azure Digital Twins sont sensibles à la casse. Veillez donc à utiliser les noms exacts définis dans les modèles. Si les noms des propriétés sont mal orthographiés ou ne respectent pas la casse, le jeu de résultats sera vide et aucune erreur ne sera retournée.
* **Placer les guillemets simples dans une séquence d’échappement** : si le texte de votre requête comprend un guillemet simple dans les données, celui-ci doit être placé dans une séquence d’échappement avec le caractère `\`. Voici un exemple qui traite une valeur de propriété *D'Souza* :

  :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="EscapedSingleQuote":::

## <a name="next-steps"></a>Étapes suivantes

Apprenez à écrire des requêtes et consultez des exemples de codes clients dans [Guide pratique : interroger le graphique de jumeaux](how-to-query-graph.md).