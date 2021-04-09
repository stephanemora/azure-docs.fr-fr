---
title: Fichier include
description: Fichier include
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 02/17/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2f52e8a89ec9dd78a1951836053cb2c698310bbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100645539"
---
Les limites suivantes s’appliquent aux **rubriques** Azure Event Grid (rubriques système, personnalisées et partenaire). 

| Ressource | Limite |
| --- | --- |
| Rubriques personnalisées par abonnement Azure | 100 |
| Abonnements à des événements par rubrique | 500 |
| Taux de publication pour une rubrique personnalisée ou partenaire (entrée) | 5 000 événements/s ou 5 Mo/s (selon la première valeur atteinte) |
| Taille d’événement | 1 Mo  |
| Connexions de point de terminaison privées par rubrique  | 64 | 
| Règles de pare-feu IP par rubrique | 16 | 

Les limites suivantes s’appliquent aux **domaines** Azure Event Grid. 

| Ressource | Limite |
| --- | --- |
| Rubriques par domaine d’événements | 100 000 |
| Abonnements à des événements par rubrique dans un domaine | 500 |
| Abonnements d’événements à l’étendue de domaine | 50 |
| Taux de publication pour un Domaine d’événements (entrée) | 5 000 événements/s ou 5 Mo/s (selon la première valeur atteinte) |
| Domaines d’événements par abonnement Azure | 100 |
| Connexions de point de terminaison privé par domaine | 64 | 
| Règles de pare-feu IP par domaine | 16 | 


