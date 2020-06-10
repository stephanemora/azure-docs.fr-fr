---
title: Fichier include
description: Fichier include
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/23/2020
ms.author: dacoulte
ms.openlocfilehash: e9faea1d5913a19dfdeff662e26992529dc1b22d
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84466886"
---
Il existe un nombre maximal pour chaque type d'objet concernant Azure Policy. Une entrée _Scope_ (Étendue) fait référence soit à l’abonnement, soit au [groupe d’administration](../articles/governance/management-groups/overview.md).

| Where | Quoi | Nombre maximal |
|---|---|---|
| Étendue | Définitions de stratégies | 500 |
| Étendue | Définitions d’initiative | 100 |
| Locataire | Définitions d’initiative | 2 500 |
| Étendue | Affectations de stratégies et d'initiatives | 100 |
| Définition de stratégie | Paramètres | 20 |
| Définition d’initiative | Stratégies | 100 |
| Définition d’initiative | Paramètres | 100 |
| Affectations de stratégies et d'initiatives | Exclusion (notScopes) | 400 |
| Règle de stratégie | Éléments conditionnels imbriqués | 512 |
| Tâche de correction | Ressources | 500 |
