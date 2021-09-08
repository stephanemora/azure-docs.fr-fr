---
title: Fichier Include
description: Fichier include
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: e9ba8efefd2238b1aadc2fbeaf17a286e0714bcd
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123485092"
---
Il existe un nombre maximal pour chaque type d'objet concernant Azure Policy. Pour les définitions, une entrée d’_étendue_ désigne le [groupe d’administration](../articles/governance/management-groups/overview.md) ou l’abonnement.
Pour les affectations et les exemptions, une entrée d’_étendue_ signifie le [groupe d’administration](../articles/governance/management-groups/overview.md), l’abonnement, le groupe de ressources ou la ressource individuelle.

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
