---
title: Rechercher la région LUIS avec C# dans les limites de Language Understanding (LUIS) | Microsoft Docs
description: Recherchez par programmation une région de publication avec une clé d’abonnement et un ID d’application pour LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/31/2018
ms.author: v-geberr
ms.openlocfilehash: c8d2024567255083aec470adfebff0d1706fd472
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2018
ms.locfileid: "35370900"
---
# <a name="region-can-be-determined-from-api-call"></a>La région peut être déterminée à partir de l’appel d’API 
Si vous disposez d’un ID d’application LUIS et d’un ID d’abonnement LUIS, vous pouvez rechercher la région à utiliser pour les requêtes de point de terminaison.

> [!NOTE] 
> La solution C# complète est disponible dans le [**référentiel Github** LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/csharp/) (Exemples-LUIS).

## <a name="luis-endpoint-query-strategy"></a>Stratégie de requête de point de terminaison LUIS
Chaque requête de point de terminaison LUIS nécessite :

* Une clé d’abonnement
* Un ID d’application
* Une région

Si la requête de point de terminaison LUIS utilise la bonne clé d’abonnement et l’ID d’application correcte, mais pas la bonne région, le code de réponse est 401. La requête 401 n’est pas comptabilisée dans le quota de l’abonnement. Transformez cette requête en stratégie pour interroger toutes les régions afin de trouver la bonne. La bonne région correspond à la seule requête qui renvoie un code d’état 2xx. 

|Response code|parameters|
|--|--|
|2xx|clé d’abonnement appropriée<br>ID d’application approprié<br>région d’hôte appropriée|
|401|clé d’abonnement appropriée<br>ID d’application approprié<br>région d’hôte _incorrecte_|

## <a name="c-class-code-to-find-region"></a>Code de la classe C# pour rechercher la région
L’application de console accepte l’ID d’application LUIS et la clé d’abonnement et retourne toutes les régions associées. Actuellement, une clé d’abonnement est créée par région, ce qui signifie qu’une seule région est renvoyée.

Incluez les dépendances de la bibliothèque .Net :

[!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=1-6 "Add the dependencies")]

Ajoutez cette classe LUIS personnalisée conçue pour rechercher la région. Remplacez les valeurs variables de `luisAppId` et `luisSubscriptionKey` par vos propres valeurs. Toutes les régions qui renvoient une réponse 401 sont ajoutées à la console de débogage. 

[!code-csharp[Add the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=10-83 "Add the LUIS class")]

Il s’agit d’un exemple d’appel de la classe LUIS personnalisée dans la méthode principale de l’application de console :

[!code-csharp[Call the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=85-101 "Call the LUIS class")]

Lorsque l’application s’exécute, la console affiche la région de l’ID d’application et de la clé d’abonnement.

![Capture d’écran de l’application de console montrant la région LUIS](./media/find-region-csharp/console.png)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [régions](luis-reference-regions.md) LUIS.
