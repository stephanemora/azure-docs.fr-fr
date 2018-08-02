---
title: Rechercher la région LUIS avec C# dans les limites de Language Understanding (LUIS) | Microsoft Docs
description: Rechercher par programmation une région de publication avec un point de terminaison et un ID d’application pour LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/31/2018
ms.author: v-geberr
ms.openlocfilehash: f0df14736e0ed47957999e3aa7c6a22b0b0c0a35
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110404"
---
# <a name="region-can-be-determined-from-api-call"></a>La région peut être déterminée à partir de l’appel d’API 
Si vous disposez d’un ID d’application LUIS et d’un ID d’abonnement LUIS, vous pouvez rechercher la région à utiliser pour les requêtes de point de terminaison.

> [!NOTE] 
> La solution C# complète est disponible dans le [**référentiel Github** LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/csharp/) (Exemples-LUIS).

## <a name="luis-endpoint-query-strategy"></a>Stratégie de requête de point de terminaison LUIS
Chaque requête de point de terminaison LUIS nécessite :

* Une clé de point de terminaison
* Un ID d’application
* Une région

Si la requête de point de terminaison LUIS utilise le bon point de terminaison et l’ID d’application correcte, mais pas la bonne région, le code de réponse est 401. La requête 401 n’est pas comptabilisée dans le quota de l’abonnement. Transformez cette requête en stratégie pour interroger toutes les régions afin de trouver la bonne. La bonne région correspond à la seule requête qui renvoie un code d’état 2xx. 

|Response code|parameters|
|--|--|
|2xx|clé de point de terminaison appropriée<br>ID d’application approprié<br>région d’hôte appropriée|
|401|clé de point de terminaison appropriée<br>ID d’application approprié<br>région d’hôte _incorrecte_|

## <a name="c-class-code-to-find-region"></a>Code de la classe C# pour rechercher la région
L’application de console accepte l’ID d’application LUIS et le point de terminaison et retourne toutes les régions associées. Actuellement, un point de terminaison est créé par région, ce qui signifie qu’une seule région doit être retournée.

Incluez les dépendances de la bibliothèque .Net :

[!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=1-6 "Add the dependencies")]

Ajoutez cette classe LUIS personnalisée conçue pour rechercher la région. Remplacez les valeurs variables de `luisAppId` et `luisSubscriptionKey` par vos propres valeurs. Toutes les régions qui renvoient une réponse 401 sont ajoutées à la console de débogage. 

[!code-csharp[Add the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=10-83 "Add the LUIS class")]

Il s’agit d’un exemple d’appel de la classe LUIS personnalisée dans la méthode principale de l’application de console :

[!code-csharp[Call the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=85-101 "Call the LUIS class")]

Lorsque l’application s’exécute, la console affiche la région de l’ID d’application.

![Capture d’écran de l’application de console montrant la région LUIS](./media/find-region-csharp/console.png)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [régions](luis-reference-regions.md) LUIS.
