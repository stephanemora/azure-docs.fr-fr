---
title: Fichier include
description: Fichier include
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/25/2020
ms.author: dacoulte
ms.openlocfilehash: 19b43c857058b3af50db3bc1b42ec1ae558ee884
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80272946"
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
| Tâche de correction | Ressources | 1 000 |
