---
title: Fichier Include
description: Fichier Include
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 04/30/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 443db1b4609e62fb7c57de417e42a2b4d0737ada
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553621"
---
Les limites suivantes s’appliquent aux rubriques du système Azure Event Grid et les rubriques personnalisées, *pas* domaines de l’événement.

| Ressource | Limite |
| --- | --- |
| Rubriques personnalisées par abonnement Azure | 100 |
| Abonnements à des événements par rubrique | 500 |
| Taux de publication pour une rubrique personnalisée (entrée) | 5 000 événements par seconde et par rubrique |

Les limites suivantes s’appliquent à des domaines d’événement uniquement.

| Ressource | Limite |
| --- | --- |
| Rubriques par domaine d’événements | 1 000 pendant la phase de préversion publique |
| Abonnements aux événements par rubrique dans un domaine | 50 pendant la phase de préversion publique |
| Abonnements aux événements de domaine étendue | 50 pendant la phase de préversion publique |
| Publier les taux pour un domaine d’événement (entrée) | 5 000 événements par seconde pendant la phase de préversion publique |