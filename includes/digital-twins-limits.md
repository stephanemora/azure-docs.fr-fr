---
author: baanders
description: Fichier include pour les limites Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/8/2021
ms.author: baanders
ms.openlocfilehash: 2a6cff1aa93c4f3ac4f95fd176e1b1e42736d8f4
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108763345"
---
### <a name="functional-limits"></a>Limitations fonctionnelles

Le tableau suivant liste les limites opérationnelles d’Azure Digital Twins. 

> [!TIP]
> Pour pouvoir appliquer les recommandations de modélisation dans le cadre de ces limites opérationnelles, consultez [Bonnes pratiques en matière de conception de modèles](../articles/digital-twins/concepts-models.md#best-practices-for-designing-models).

| Domaine | Fonctionnalité | Limite par défaut | Ajustable ? |
| --- | --- | --- | --- |
| Ressource Azure | Nombre d’instances Azure Digital Twins dans une région, par abonnement | 10 | Oui |
| Jumeaux numériques | Nombre de jumeaux dans une instance Azure Digital Twins | 200 000 | Oui |
| Jumeaux numériques | Nombre de relations entrantes à un seul jumeau | 5 000 | Non |
| Jumeaux numériques | Nombre de relations sortantes à partir d’un seul jumeau | 5 000 | Non |
| Jumeaux numériques | Taille maximale (du corps JSON dans une requête PUT ou PATCH) d’un seul jumeau | 32 Ko | Non |
| Jumeaux numériques | Taille maximale de la charge utile de requête | 32 Ko | Non | 
| Routage | Nombre de points de terminaison pour une même instance Azure Digital Twins | 6 | Non |
| Routage | Nombre d’itinéraires pour une même instance Azure Digital Twins | 6 | Oui |
| Modèles | Nombre de modèles dans une même instance Azure Digital Twins | 10 000 | Oui |
| Modèles | Nombre de modèles qui peuvent être chargés en un seul appel d’API | 250 | Non |
| Modèles | Taille maximale (du corps JSON dans une requête PUT ou PATCH) d’un seul modèle | 1 Mo | Non |
| Modèles | Nombre d’éléments retournés sur une même page | 100 | Non |
| Requête | Nombre d’éléments retournés sur une même page | 100 | Oui |
| Requête | Nombre d’expressions `AND` / `OR` dans une requête | 50 | Oui |
| Requête | Nombre d’éléments de tableau dans une clause `IN` / `NOT IN` | 50 | Oui |
| Requête | Nombre de caractères dans une requête | 8,000 | Oui |
| Requête | Nombre de `JOINS` dans une requête | 5 | Oui |

### <a name="rate-limits"></a>Limites du taux de transfert

Le tableau suivant reflète les limites de débit de différentes API.

| API | Fonctionnalité | Limite par défaut | Ajustable ? |
| --- | --- | --- | --- |
| API Modèles | Nombre de demandes par seconde | 100 | Oui |
| API Digital Twins | Nombre de requêtes de lecture par seconde | 1 000 | Oui |
| API Digital Twins | Nombre de requêtes patch par seconde | 1 000 | Oui |
| API Digital Twins | Nombre d’opérations de création/suppression par seconde pour l’**ensemble des jumeaux et des relations** | 50 | Oui |
| API Digital Twins | Nombre d’opérations de création/mise à jour/suppression par seconde sur un **jumeau unique** ou ses relations | 10 | Non |
| API de requête | Nombre de demandes par seconde | 500 | Oui |
| API de requête | [Unités de requête](../articles/digital-twins/concepts-query-units.md) par seconde | 4 000 | Oui |
| API Routage d’événement | Nombre de demandes par seconde | 100 | Oui |

### <a name="other-limits"></a>Autres limites

Les limites des types de données et des champs dans les documents DTDL pour les modèles Azure Digital Twins sont disponibles dans la documentation des spécifications sur GitHub : [DTDL (Digital Twins Definition Language) – version 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Les détails de la latence des requêtes sont décrits dans [Concepts : Langage de requête](../articles/digital-twins/concepts-query-language.md#considerations-for-querying). Les limitations des fonctionnalités spécifiques du langage de requête se trouvent dans la [documentation de référence sur les requêtes](../articles/digital-twins/concepts-query-language.md#reference-documentation).
