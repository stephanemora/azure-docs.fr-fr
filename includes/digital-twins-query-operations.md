---
author: baanders
description: Fichier include pour les opérations de requête Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 6dfddd9179569075abdbd94b6c1afa719db9ba1e
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905587"
---
## <a name="query-language-features"></a>Fonctionnalités du langage de requête

Azure Digital Twins fournit des fonctionnalités de requête étendues par rapport au graphique de jumeaux. Les requêtes sont décrites à l’aide de la syntaxe de type SQL, dans un langage de requête similaire au [langage de requête IoT Hub](../articles/iot-hub/iot-hub-devguide-query-language.md) avec de nombreuses fonctionnalités comparables.

> [!NOTE]
> Toutes les opérations de requête Azure Digital Twins respectent la casse.

Voici les opérations disponibles dans le langage de requête Azure Digital Twins.

Obtenir les jumeaux numériques d’après…
* leur modèle (à l’aide de l’opérateur `IS_OF_MODEL`)
* leurs propriétés (y compris les [propriétés de balise](../articles/digital-twins/how-to-use-tags.md))
* interfaces
* relationships
  - leurs propriétés des relations

Vous pouvez améliorer davantage vos requêtes grâce aux opérations suivantes :
* Obtenir des jumeaux via des types de relations multiples (requêtes`JOIN`). 
  - Pendant la préversion, jusqu’à cinq niveaux de `JOIN` sont autorisés.
* Sélectionnez uniquement les premiers résultats de la requête (opérateur `Select TOP`).
* Utilisez des fonctions scalaires : `IS_BOOL`, `IS_DEFINED`, `IS_NULL`, `IS_NUMBER`, `IS_OBJECT`, `IS_PRIMITIVE`, `IS_STRING`, `STARTSWITH`, `ENDSWITH`.
* Utilisez des opérateurs de comparaison de requêtes : `IN`/`NIN`, `=`, `!=`, `<`, `>`, `<=`, `>=`.
* Utilisez n’importe quelle combinaison (opérateur `AND`, `OR`, `NOT`) de `IS_OF_MODEL`, des fonctions scalaires et des opérateurs de comparaison.
* Utilisez la continuation : L’objet query est instancié avec une taille de page (jusqu’à 100). Vous pouvez récupérer les jumeaux numériques une page à la fois en fournissant le jeton de continuation dans les appels suivants à l’API.