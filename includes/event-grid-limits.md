---
title: Fichier include
description: Fichier include
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: e084256d9c2043d4382ca180ef3178175b301367
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91745598"
---
Les limites suivantes s’appliquent aux **rubriques** Azure Event Grid (rubriques système, personnalisées et partenaire). 

| Ressource | Limite |
| --- | --- |
| Rubriques personnalisées par abonnement Azure | 100 |
| Abonnements à des événements par rubrique | 500 |
| Taux de publication pour une rubrique personnalisée ou partenaire (entrée) | 5 000 événements/s ou 1 Mo/s (selon la première valeur atteinte)<br/>Ne s’applique pas aux rubriques système. |
| Taille d’événement | 1 Mo  |
| Connexions de point de terminaison privées par rubrique  | 64 | 
| Règles de pare-feu IP par rubrique | 16 | 

Les limites suivantes s’appliquent aux **domaines** Azure Event Grid. 

| Ressource | Limite |
| --- | --- |
| Rubriques par domaine d’événements | 100 000 |
| Abonnements à des événements par rubrique dans un domaine | 500 |
| Abonnements d’événements à l’étendue de domaine | 50 |
| Taux de publication pour un Domaine d’événements (entrée) | 5 000 événements/s ou 1 Mo/s (selon la première valeur atteinte) |
| Domaines d’événements par abonnement Azure | 100 |
| Connexions de point de terminaison privé par domaine | 64 | 
| Règles de pare-feu IP par domaine | 16 | 


