---
title: Créer des fonctions de date pour la définition de l’interface utilisateur
description: Décrit les fonctions à utiliser lors de l’utilisation de valeurs de date.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 80484fd15e51bae7036c43bd3ca8fe8167387ede
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87094190"
---
# <a name="createuidefinition-date-functions"></a>fonctions de date CreateUiDefinition

Fonctions à utiliser lors de l’utilisation de valeurs de date.

## <a name="addhours"></a>addHours

Ajoute un nombre entier d’heures à l’horodatage spécifié.

L’exemple suivant retourne `"1991-01-01T00:59:59.000Z"`:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addminutes"></a>addMinutes

Ajoute un nombre entier de minutes à l’horodatage spécifié.

L’exemple suivant retourne `"1991-01-01T00:00:59.000Z"`:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addseconds"></a>addSeconds
Ajoute un nombre entier de secondes à l’horodatage spécifié.

L’exemple suivant retourne `"1991-01-01T00:00:00.000Z"`:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

## <a name="utcnow"></a>utcNow

Retourne une chaîne au format ISO 8601 indiquant la date et l’heure de l’ordinateur local.

L’exemple suivant peut retourner `"1990-12-31T23:59:59.000Z"` :

```json
"[utcNow()]"
```

## <a name="next-steps"></a>Étapes suivantes

* Pour découvrir Azure Resource Manager, consultez [Vue d’ensemble d’Azure Resource Manager](../management/overview.md).
