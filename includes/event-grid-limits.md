---
title: Fichier Include
description: Fichier Include
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 05/22/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ee80c04a6365c2cf337c4033a90df8d2993c299d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76845974"
---
Les limites suivantes s’appliquent aux rubriques système Azure Event Grid et aux rubriques personnalisées, et  *non pas* aux domaines d’événements.

| Ressource | Limite |
| --- | --- |
| Rubriques personnalisées par abonnement Azure | 100 |
| Abonnements à des événements par rubrique | 500 |
| Taux de publication pour une rubrique personnalisée (entrée) | 5 000 événements par seconde et par rubrique |
| Publier les requêtes | 250 par seconde |
| Taille d’événement | 1 Mo (facturé sous la forme de plusieurs événements de 64 Ko) |

Les limites suivantes s’appliquent aux domaines d’événements uniquement.

| Ressource | Limite |
| --- | --- |
| Rubriques par domaine d’événements | 100 000 |
| Abonnements à des événements par rubrique dans un domaine | 500 |
| Abonnements d’événements à l’étendue de domaine | 50 |
| Taux de publication pour un Domaine d’événements (entrée) | 5 000 événements par seconde |
| Publier les requêtes | 250 par seconde |
| Domaines d’événements par abonnement Azure | 100 |