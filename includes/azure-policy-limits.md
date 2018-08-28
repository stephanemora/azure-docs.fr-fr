---
title: Fichier Include
description: Fichier Include
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/16/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 0e5d1214a8e1af8299cb40d1a3b31ff6eafc8c5c
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40185498"
---
Il existe un nombre maximal pour chaque type d’objet pour Azure Policy. Une entrée _Scope_ (Étendue) fait référence soit à l’abonnement, soit au [groupe d’administration](../articles/azure-resource-manager/management-groups-overview.md).

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
