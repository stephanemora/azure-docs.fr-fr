---
title: Fichier include
description: Fichier include
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/22/2020
ms.author: dacoulte
ms.openlocfilehash: 2707523947cae77dd800dd5f560ebe1c47f3893a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905972"
---
Il existe un nombre maximal pour chaque type d'objet concernant Azure Policy. Une entrée _Scope_ (Étendue) fait référence soit à l’abonnement, soit au [groupe d’administration](../articles/governance/management-groups/overview.md).

| Where | Quoi | Nombre maximal |
|---|---|---|
| Étendue | Définitions de stratégies | 500 |
| Étendue | Définitions d’initiative | 200 |
| Locataire | Définitions d’initiative | 2 500 |
| Étendue | Affectations de stratégies et d'initiatives | 200 |
| Étendue | Exemptions | 1 000 |
| Définition de stratégie | Paramètres | 20 |
| Définition d’initiative | Stratégies | 1 000 |
| Définition d’initiative | Paramètres | 100 |
| Affectations de stratégies et d'initiatives | Exclusion (notScopes) | 400 |
| Règle de stratégie | Éléments conditionnels imbriqués | 512 |
| Tâche de correction | Ressources | 500 |
