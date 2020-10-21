---
author: baanders
description: Fichier include pour les limites Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 60a5f62d4ea23db1052b2e40d10775dfaa33c632
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91989554"
---
### <a name="functional-limits"></a>Limitations fonctionnelles

Le tableau ci-dessous présente les limites fonctionnelles d’Azure Digital Twins dans la préversion actuelle.

| Domaine | Fonctionnalité | Limite par défaut | Ajustable ? |
| --- | --- | --- | --- |
| Ressource Azure | Nombre d’instances Azure Digital Twins dans une région, par abonnement | 10 | Oui |
| Jumeaux numériques | Nombre de jumeaux dans une instance Azure Digital Twins | 200 000 | Oui |
| Jumeaux numériques | Nombre de relations entrantes à un seul jumeau | 5 000 | Non |
| Jumeaux numériques | Nombre de relations sortantes à partir d’un seul jumeau | 5 000 | Non |
| Jumeaux numériques | Taille maximale d’un seul jumeau | 32 Ko | Non |
| API Digital Twins | Taille maximale de la charge utile de requête | 32 Ko | Non | 
| Routage | Nombre de points de terminaison pour une même instance Azure Digital Twins | 6 | Non |
| Routage | Nombre d’itinéraires pour une même instance Azure Digital Twins | 6 | Oui |
| Modèles | Nombre de modèles dans une même instance Azure Digital Twins | 10 000 | Oui |
| Modèles | Nombre de modèles qui peuvent être chargés en un seul appel d’API | 250 | Non |
| Modèles | Nombre d’éléments retournés sur une même page | 100 | Non |
| Requête | Nombre d’éléments retournés sur une même page | 100 | Non |
| Requête | Nombre d’expressions `AND` / `OR` dans une requête | 50 | Oui |
| Requête | Nombre d’éléments de tableau dans une clause `IN` / `NOT IN` | 50 | Oui |
| Requête | Nombre de caractères dans une requête | 8,000 | Oui |
| Requête | Nombre de `JOINS` dans une requête | 5 | Oui |

### <a name="rate-limits"></a>Limites du taux de transfert

Ce tableau reflète les limites de débit de différentes API.

| API | Fonctionnalité | Limite par défaut | Ajustable ? |
| --- | --- | --- | --- |
| API Modèles | Nombre de demandes par seconde | 100 | Oui |
| API Digital Twins | Nombre de demandes par seconde | 1 000 | Oui |
| API de requête | Nombre de demandes par seconde | 500 | Oui |
| API de requête | [Unités de requête](../articles/digital-twins/concepts-query-units.md) par seconde | 4 000 | Oui |
| API Routage d’événement | Nombre de demandes par seconde | 100 | Oui |

### <a name="other-limits"></a>Autres limites

Les limites des types de données et des champs dans les documents DTDL pour les modèles Azure Digital Twins sont disponibles dans la documentation des spécifications sur GitHub : [*DTDL (Digital Twins Definition Language) – version 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Pour plus d’informations sur la latence des requêtes et d’autres instructions sur l’écriture de requêtes en préversion, consultez [*Guide pratique : Interroger le graphique de jumeaux*](../articles/digital-twins/how-to-query-graph.md).