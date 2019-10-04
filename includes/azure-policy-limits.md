---
title: Fichier Include
description: Fichier Include
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/05/2019
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: ea87e51e66985f860cd6d10595a32facde1dc639
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "67133469"
---
Il existe un nombre maximal pour chaque type d'objet concernant Azure Policy. Une entrée _Scope_ (Étendue) fait référence soit à l’abonnement, soit au [groupe d’administration](../articles/governance/management-groups/overview.md).

| Where | Quoi | Nombre maximal |
|---|---|---|
| Étendue | Définitions de stratégies | 500 |
| Étendue | Définitions d’initiative | 100 |
| Locataire | Définitions d’initiative | 1 000 |
| Étendue | Affectations de stratégies et d'initiatives | 100 |
| Définition de stratégie | parameters | 20 |
| Définition d’initiative | Stratégies | 100 |
| Définition d’initiative | parameters | 100 |
| Affectations de stratégies et d'initiatives | Exclusion (notScopes) | 400 |
| Règle de stratégie | Éléments conditionnels imbriqués | 512 |
