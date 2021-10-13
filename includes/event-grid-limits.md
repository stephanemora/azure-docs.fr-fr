---
title: Fichier include
description: Fichier include
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 09/07/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0afa9814a7fb6b726d07fe5f1a5b2c863df72e2e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778831"
---
Les limites suivantes s’appliquent aux **rubriques** Azure Event Grid (rubriques système, personnalisées et partenaire). 

> [!NOTE]
> Ces limites s’appliquent par région. 

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


