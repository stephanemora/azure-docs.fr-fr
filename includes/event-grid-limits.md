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
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66376933"
---
Les limites suivantes s’appliquent aux rubriques du système Azure Event Grid et les rubriques personnalisées, *pas* domaines de l’événement.

| Ressource | Limite |
| --- | --- |
| Rubriques personnalisées par abonnement Azure | 100 |
| Abonnements à des événements par rubrique | 500 |
| Taux de publication pour une rubrique personnalisée (entrée) | 5 000 événements par seconde et par rubrique |
| Requêtes de publication | 250 par seconde |
| Taille d’événement | Prend en charge de 64 Ko en général la disposition (générale). Prise en charge de 1 Mo est actuellement en version préliminaire. |

Les limites suivantes s’appliquent à des domaines d’événement uniquement.

| Ressource | Limite |
| --- | --- |
| Rubriques par domaine d’événements | 1 000 pendant la phase de préversion publique |
| Abonnements aux événements par rubrique dans un domaine | 50 pendant la phase de préversion publique |
| Abonnements aux événements de domaine étendue | 50 pendant la phase de préversion publique |
| Publier les taux pour un domaine d’événement (entrée) | 5 000 événements par seconde pendant la phase de préversion publique |
| Requêtes de publication | 250 par seconde |