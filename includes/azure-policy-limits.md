---
title: Fichier Include
description: Fichier Include
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 57cec39bde460c6079091490acf541761c61e003
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553631"
---
Il existe un nombre maximal pour chaque type d’objet pour Azure Policy. Une entrée _Scope_ (Étendue) fait référence soit à l’abonnement, soit au [groupe d’administration](../articles/governance/management-groups/overview.md).

| Where | Quoi | Nombre maximal |
|---|---|---|
| Étendue | Définitions de stratégies | 250 |
| Étendue | Définitions d’initiative | 100 |
| Locataire | Définitions d’initiative | 1 000 |
| Étendue | Affectations de stratégie ou une initiative | 100 |
| Définition de stratégie | parameters | 20 |
| Définition d’initiative | Stratégies | 100 |
| Définition d’initiative | parameters | 100 |
| Affectations de stratégie ou une initiative | Exclusion (notScopes) | 250 |
| Règle de stratégie | Éléments conditionnels imbriqués | 512 |
