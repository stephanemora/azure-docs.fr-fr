---
title: Fichier include
description: Fichier include
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: 1c63d79c82f667fbd7cfc9a62a741eab275757dd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131571272"
---
Il existe un nombre maximal pour chaque type d'objet concernant Azure Policy. Pour les définitions, une entrée d’_étendue_ désigne le [groupe d’administration](../articles/governance/management-groups/overview.md) ou l’abonnement. Pour les affectations et les exemptions, une entrée d’_étendue_ signifie le [groupe d’administration](../articles/governance/management-groups/overview.md), l’abonnement, le groupe de ressources ou la ressource individuelle.

| Where | Quoi | Nombre maximal |
|---|---|---|
| Étendue | Définitions de stratégies | 500 |
| Étendue | Définitions d’initiative | 200 |
| Locataire | Définitions d’initiative | 2 500 |
| Étendue | Affectations de stratégies et d'initiatives | 200 |
| Étendue | Exemptions | 1 000 |
| Définition de stratégie | Paramètres | 20 |
| Définition d’initiative | Stratégies | 1 000 |
| Définition d’initiative | Paramètres | 300 |
| Affectations de stratégies et d'initiatives | Exclusion (notScopes) | 400 |
| Règle de stratégie | Éléments conditionnels imbriqués | 512 |
| Tâche de correction | Ressources | 500 |
