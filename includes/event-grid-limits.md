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
ms.openlocfilehash: 3f94481e6a8550479788d92c744327e1dc3b58c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66376933"
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
| Rubriques par domaine d’événements | 1 000 pendant la phase de préversion publique |
| Abonnements à des événements par rubrique dans un domaine | 50 pendant la phase de préversion publique |
| Abonnements d’événements à l’étendue de domaine | 50 pendant la phase de préversion publique |
| Taux de publication pour un Domaine d’événements (entrée) | 5 000 événements par seconde pendant la phase de préversion publique |
| Publier les requêtes | 250 par seconde |