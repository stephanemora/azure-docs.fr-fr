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
ms.openlocfilehash: c3365450c90c4fda37884e8998fad70f5d164244
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006479"
---
Il existe un nombre maximal pour chaque type d’objet pour Azure Policy. Une entrée _Scope_ (Étendue) fait référence soit à l’abonnement, soit au [groupe d’administration](../articles/governance/management-groups/overview.md).

| Where | Quoi | Nombre maximal |
|---|---|---|
| Étendue | Définitions de stratégies | 250 |
| Étendue | Définitions d’initiative | 100 |
| Locataire | Définitions d’initiative | 1 000 |
| Étendue | Affectations d'initiative/de stratégie | 100 |
| Définition de stratégie | parameters | 20 |
| Définition d’initiative | Stratégies | 100 |
| Définition d’initiative | parameters | 100 |
| Affectations d'initiative/de stratégie | Exclusion (notScopes) | 100 |
| Règle de stratégie | Éléments conditionnels imbriqués | 512 |
