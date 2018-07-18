---
title: Rechercher la région LUIS avec Node.js dans les limites de Language Understanding (LUIS) | Microsoft Docs
description: Recherchez par programmation une région de publication avec une clé d’abonnement et un ID d’application pour LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/01/2018
ms.author: v-geberr
ms.openlocfilehash: 18ee324c10f074601c0c04573ca1a5266481f21c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2018
ms.locfileid: "35370901"
---
# <a name="region-can-be-determined-from-api-call"></a>La région peut être déterminée à partir de l’appel d’API 
Si vous disposez d’un ID d’application LUIS et d’un ID d’abonnement LUIS, vous pouvez rechercher la région à utiliser pour les requêtes de point de terminaison.

> [!NOTE] 
> La solution Node.js complète est disponible dans le [**référentiel Github** LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/nodejs/) (Exemples-LUIS).

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

## <a name="nodejs-code-to-find-region"></a>Code Node.js pour rechercher la région
L’application de console accepte l’ID d’application LUIS et la clé d’abonnement et retourne toutes les régions associées. Actuellement, une clé d’abonnement est créée par région, ce qui signifie qu’une seule région est renvoyée.

Incluez les dépendances NPM :

[!code-javascript[Add the dependencies](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=5-6 "Add the dependencies")]

Ajoutez des constantes. Remplacez les valeurs variables de `subscriptionKey` et `appId` par vos propres valeurs.  

[!code-javascript[Add constants](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=8-25 "Add constants")]

Ajoutez la fonction `searchRegions` pour rechercher la région. Toutes les régions incorrectes renvoient une réponse 401, qui est interceptée et ignorée.

[!code-javascript[Find region](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=27-37 "Find region")]

Appelez la fonction `searchRegions` et renvoyez une seule région :

[!code-javascript[Call the function](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=39-43 "Call the function")]

Lorsque l’application s’exécute, le terminal affiche la région de l’ID d’application et de la clé d’abonnement.

![Capture d’écran de l’application de console montrant la région LUIS](./media/find-region-nodejs/console.png)


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [régions](luis-reference-regions.md) LUIS.