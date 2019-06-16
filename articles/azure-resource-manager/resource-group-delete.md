---
title: Supprimer un groupe de ressources et des ressources - Azure Resource Manager
description: Décrit comment Azure Resource Manager organise la suppression des ressources pendant la suppression d’un groupe de ressources. Décrit les codes de réponse et comment Resource Manager les gère pour déterminer si la suppression a réussi.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: c38b1ccf7f7ccfe57e2b29f236f642238c4706a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61363590"
---
# <a name="azure-resource-manager-resource-group-deletion"></a>Suppression d’un groupe de ressources par Azure Resource Manager

Cet article décrit comment Azure Resource Manager organise la suppression des ressources quand vous supprimez un groupe de ressources.

## <a name="determine-order-of-deletion"></a>Déterminer l’ordre de suppression

Quand vous supprimez un groupe de ressources, Resource Manager détermine l’ordre de suppression des ressources. Il utilise l’ordre suivant :

1. Toutes les ressources enfants (imbriquées) sont supprimées.

2. Les ressources qui gèrent d’autres ressources sont supprimées ensuite. Une ressource peut avoir la propriété `managedBy` définie pour indiquer qu’une autre ressource la gère. Quand cette propriété est définie, la ressource qui gère l’autre ressource est supprimée avant les autres ressources.

3. Les ressources restantes sont supprimées après les deux catégories précédentes.

## <a name="resource-deletion"></a>Suppression de ressources

Une fois l’ordre déterminé, Resource Manager envoie une opération DELETE pour chaque ressource. Il attend la fin de toutes les dépendances pour continuer.

Pour les opérations synchrones, les codes de réponse corrects attendus sont :

* 200
* 204
* 404

Pour les opérations asynchrones, le code de réponse correct attendu est 202. Resource Manager effectue le suivi de l’en-tête d’emplacement ou de l’en-tête d’opération asynchrone Azure pour déterminer l’état de l’opération de suppression asynchrone.
  
### <a name="errors"></a>Errors

Quand une opération de suppression retourne une erreur, Resource Manager retente l’appel DELETE. Les nouvelles tentatives sont déclenchées avec les codes d’état 5xx, 429 et 408. Par défaut, l’intervalle entre chaque nouvelle tentative est de 15 minutes.

## <a name="after-deletion"></a>Après la suppression

Resource Manager envoie un appel GET sur chaque ressource qu’il a essayé de supprimer. La réponse attendue de cet appel GET est 404. Quand Resource Manager obtient le code 404, il considère que la suppression a été effectuée. Resource Manager supprime la ressource de son cache.

Toutefois, si l’appel GET sur la ressource retourne 200 ou 201, Resource Manager recrée la ressource.

### <a name="errors"></a>Errors

Si l’opération GET retourne une erreur, Resource Manager relance l’appel GET pour le code d’erreur suivant :

* Inférieur à 100
* 408
* 429
* Supérieur à 500

Pour les autres codes d’erreur, Resource Manager ne parvient pas à supprimer la ressource.

## <a name="next-steps"></a>Étapes suivantes

* Pour comprendre les concepts de Resource Manager, consultez [Vue d’ensemble d’Azure Resource Manager](resource-group-overview.md).
* Pour les commandes de suppression, consultez [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete) et l’[API REST](/rest/api/resources/resourcegroups/delete).
