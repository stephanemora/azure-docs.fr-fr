---
title: Unités de requête dans Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Comprendre le concept de facturation des unités de requête dans Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c68cb8cc0ecf759b9af0e313e09663cdbc327917
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89067703"
---
# <a name="query-units-in-azure-digital-twins"></a>Unités de requête dans Azure Digital Twins 

Une **unité de requête** Azure Digital Twins est une unité de calcul à la demande qui est utilisée pour exécuter vos [requêtes Azure Digital Twins](how-to-query-graph.md) à l’aide de l’[API de requête](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query). 

Elle soustrait les ressources système comme le processeur, l’IOPS et la mémoire qui sont requises pour effectuer les opérations de requête prises en charge par Azure Digital Twins, ce qui vous permet de suivre l’utilisation des unités de requête à la place.

La quantité d’unités de requête consommées pour exécuter une requête est affectée par...
* la complexité de la requête 
* la taille du jeu de résultats (ainsi, une requête retournant 10 résultats consommera plus d’unités de requête qu’une requête de complexité similaire qui retourne un seul résultat)

Cet article explique comment comprendre les unités de requête et effectuer le suivi de leur consommation.

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>Trouvez la consommation d’unités de requête dans Azure Digital Twins 

Lorsque vous exécutez une requête à l’aide de l’[API de requête](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query) d’Azure Digital Twins, vous pouvez examiner l’en-tête de réponse pour suivre le nombre de RU utilisées par la requête. Recherchez « query-charge » dans la réponse renvoyée par Azure Digital Twins. 

Les [Kits de développement logiciel (SDK)](how-to-use-apis-sdks.md) Azure Digital Twins vous permettent d’extraire l’en-tête query-charge de la réponse paginable. Cette section montre comment interroger des jumeaux numériques et comment itérer sur la réponse paginable pour extraire l’en-tête query-charge. 

L’extrait de code suivant montre comment extraire les frais d’interrogation encourus lors de l’appel de l’API de requête. Il itère d’abord sur les pages de réponse pour accéder à l’en-tête query-charge, puis itère au sein des résultats du jumeau numérique dans chaque page. 
 
```csharp
AsyncPageable<string> asyncPageableResponseWithCharge = client.QueryAsync("SELECT * FROM digitaltwins"); 
int pageNum = 0; 

// The "await" keyword here is required, as a call is made when fetching a new page. 

await foreach (Page<string> page in asyncPageableResponseWithCharge.AsPages()) 
{ 
    Console.WriteLine($"Page {++pageNum} results:"); 

    // Extract the query-charge header from the page 

    if (QueryChargeHelper.TryGetQueryCharge(page, out float queryCharge)) 
    { 
        Console.WriteLine($"Query charge was: {queryCharge}"); 
    } 

    // Iterate over the twin instances. 

    // The "await" keyword is not required here, as the paged response is local. 

    foreach (string response in page.Values) 
    { 
        BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(response); 
        Console.WriteLine($"Found digital twin '{twin.Id}'"); 
    } 
} 
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’interrogation d’Azure Digital Twins, rendez-vous sur le site :
* [*Concepts : langage de requête*](concepts-query-language.md)
* [*Guide pratique : Interroger le graphe de jumeaux*](how-to-query-graph.md)
* [Documentation de référence de l’API de requête](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query/querytwins)

Vous trouverez des limites relatives aux requêtes Azure Digital Twins dans [*Référence : Limites du service en préversion publique*](reference-service-limits.md).