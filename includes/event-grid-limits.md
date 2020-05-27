---
title: Fichier include
description: Fichier include
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 05/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7e7a0424e4454639211c6494aab0700e75269361
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83721286"
---
Les limites suivantes s’appliquent aux rubriques système Azure Event Grid et aux rubriques personnalisées, et  *non pas* aux domaines d’événements.

| Ressource | Limite |
| --- | --- |
| Rubriques personnalisées par abonnement Azure | 100 |
| Abonnements à des événements par rubrique | 500 |
| Taux de publication pour une rubrique personnalisée (entrée) | 5 000 événements par seconde et par rubrique |
| Taille d’événement | 1 Mo. Toutefois, les opérations sont facturées par incréments de 64 Ko. Ainsi, les événements de plus de 64 Ko entraînent des frais d’opération comme s’il s’agissait de plusieurs événements. Par exemple, un événement d’une taille de 130 Ko entraîne des opérations équivalents à 3 événements distincts.  |
| Rubriques par domaine d’événements | 100 000 |
| Abonnements à des événements par rubrique dans un domaine | 500 |
| Abonnements d’événements à l’étendue de domaine | 50 |
| Taux de publication pour un Domaine d’événements (entrée) | 5 000 événements par seconde |
| Domaines d’événements par abonnement Azure | 100 |
| Connexions de point de terminaison privées par rubrique ou domaine | 64 | 
| Règles de pare-feu IP par rubrique ou domaine | 16 | 