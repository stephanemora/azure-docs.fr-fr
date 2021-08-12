---
title: Fichier Include
description: Fichier include
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: 4792245dff3784d1aa72120c6be1412bf6659c35
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113027449"
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
| Définition d’initiative | Paramètres | 250 |
| Affectations de stratégies et d'initiatives | Exclusion (notScopes) | 400 |
| Règle de stratégie | Éléments conditionnels imbriqués | 512 |
| Tâche de correction | Ressources | 500 |
