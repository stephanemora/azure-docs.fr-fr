---
title: Fichier Include
description: Fichier Include
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/23/2020
ms.author: dacoulte
ms.openlocfilehash: 04e607296478520298a2febab61a7edac911a59c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82131560"
---
Il existe un nombre maximal pour chaque type d'objet concernant Azure Policy. Une entrée _Scope_ (Étendue) fait référence soit à l’abonnement, soit au [groupe d’administration](../articles/governance/management-groups/overview.md).

| Where | Quoi | Nombre maximal |
|---|---|---|
| Étendue | Définitions de stratégies | 500 |
| Étendue | Définitions d’initiative | 100 |
| Locataire | Définitions d’initiative | 1 000 |
| Étendue | Affectations de stratégies et d'initiatives | 100 |
| Définition de stratégie | Paramètres | 20 |
| Définition d’initiative | Stratégies | 100 |
| Définition d’initiative | Paramètres | 100 |
| Affectations de stratégies et d'initiatives | Exclusion (notScopes) | 400 |
| Règle de stratégie | Éléments conditionnels imbriqués | 512 |
| Tâche de correction | Ressources | 500 |
