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
ms.openlocfilehash: 890095718833c90a6764d0799d2081b393b1d0bd
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887768"
---
Les limites suivantes s’appliquent aux rubriques système Azure Event Grid et aux rubriques personnalisées, et  *non pas* aux domaines d’événements.

| Ressource | Limite |
| --- | --- |
| Rubriques personnalisées par abonnement Azure | 100 |
| Abonnements à des événements par rubrique | 500 |
| Taux de publication pour une rubrique personnalisée (entrée) | 5 000 événements par seconde et par rubrique |
| Publier les requêtes | 250 par seconde |
| Taille d’événement | Prise en charge de 64 Ko dans la disponibilité générale (GA). La prise en charge de 1 Mo est actuellement en préversion. |

Les limites suivantes s’appliquent aux domaines d’événements uniquement.

| Ressource | Limite |
| --- | --- |
| Rubriques par domaine d’événements | 100 000 |
| Abonnements à des événements par rubrique dans un domaine | 500 |
| Abonnements d’événements à l’étendue de domaine | 50 |
| Taux de publication pour un Domaine d’événements (entrée) | 5 000 événements par seconde |
| Publier les requêtes | 250 par seconde |
| Domaines d’événements par abonnement Azure | 100 |