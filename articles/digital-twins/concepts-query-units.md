---
title: Unités de requête dans Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Comprendre le concept de facturation des unités de requête dans Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 9/16/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: eb5e7970c2ce44b5ee96f671464a3029cb9429b1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630998"
---
# <a name="query-units-in-azure-digital-twins"></a>Unités de requête dans Azure Digital Twins 

Une **unité de requête** Azure Digital Twins est une unité de calcul à la demande qui est utilisée pour exécuter vos [requêtes Azure Digital Twins](how-to-query-graph.md) à l’aide de l’[API de requête](/rest/api/digital-twins/dataplane/query). 

Elle soustrait les ressources système comme le processeur, l’IOPS et la mémoire qui sont requises pour effectuer les opérations de requête prises en charge par Azure Digital Twins, ce qui vous permet de suivre l’utilisation des unités de requête à la place.

La quantité d’unités de requête consommées pour exécuter une requête est affectée par...

* la complexité de la requête
* la taille du jeu de résultats (ainsi, une requête retournant 10 résultats consommera plus d’unités de requête qu’une requête de complexité similaire qui retourne un seul résultat)

Cet article explique comment comprendre les unités de requête et effectuer le suivi de leur consommation.

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>Trouvez la consommation d’unités de requête dans Azure Digital Twins

Lorsque vous exécutez une requête à l’aide de l’[API de requête](/rest/api/digital-twins/dataplane/query) d’Azure Digital Twins, vous pouvez examiner l’en-tête de réponse pour suivre le nombre de RU utilisées par la requête. Recherchez « query-charge » dans la réponse renvoyée par Azure Digital Twins.

Les [kits SDK Azure Digital Twins](concepts-apis-sdks.md) vous permettent d’extraire l’en-tête query-charge de la réponse paginable. Cette section montre comment interroger des jumeaux numériques et comment itérer sur la réponse paginable pour extraire l’en-tête query-charge. 

L’extrait de code suivant montre comment extraire les frais d’interrogation encourus lors de l’appel de l’API de requête. Il itère d’abord sur les pages de réponse pour accéder à l’en-tête query-charge, puis itère au sein des résultats du jumeau numérique dans chaque page. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/getQueryCharges.cs":::

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’interrogation d’Azure Digital Twins, rendez-vous sur le site :

* [Langage de requête](concepts-query-language.md)
* [Interroger le graphe de jumeaux](how-to-query-graph.md)
* [Documentation de référence de l’API de requête](/rest/api/digital-twins/dataplane/query/querytwins)

Vous trouverez des limites relatives aux requêtes Azure Digital Twins dans [Limites du service Azure Digital Twins](reference-service-limits.md).