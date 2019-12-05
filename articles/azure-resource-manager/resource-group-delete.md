---
title: Supprimer un groupe de ressources et des ressources
description: Décrit comment supprimer des groupes de ressources et des ressources. Décrit comment Azure Resource Manager organise la suppression des ressources pendant la suppression d’un groupe de ressources. Décrit les codes de réponse et comment Resource Manager les gère pour déterminer si la suppression a réussi.
ms.topic: conceptual
ms.date: 09/03/2019
ms.custom: seodec18
ms.openlocfilehash: c84dcf64555f40f45dbdaeedb05d4cc461f2cbb7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150813"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Suppression d’un groupe de ressources et de ressources Azure Resource Manager

Cet article explique comment supprimer des groupes de ressources et des ressources. Il décrit comment Azure Resource Manager organise la suppression des ressources quand vous supprimez un groupe de ressources.

## <a name="how-order-of-deletion-is-determined"></a>Détermination de l’ordre de suppression

Quand vous supprimez un groupe de ressources, Resource Manager détermine l’ordre de suppression des ressources. Il utilise l’ordre suivant :

1. Toutes les ressources enfants (imbriquées) sont supprimées.

2. Les ressources qui gèrent d’autres ressources sont supprimées ensuite. Une ressource peut avoir la propriété `managedBy` définie pour indiquer qu’une autre ressource la gère. Quand cette propriété est définie, la ressource qui gère l’autre ressource est supprimée avant les autres ressources.

3. Les ressources restantes sont supprimées après les deux catégories précédentes.

Une fois l’ordre déterminé, Resource Manager envoie une opération DELETE pour chaque ressource. Il attend la fin de toutes les dépendances pour continuer.

Pour les opérations synchrones, les codes de réponse corrects attendus sont :

* 200
* 204
* 404

Pour les opérations asynchrones, le code de réponse correct attendu est 202. Resource Manager effectue le suivi de l’en-tête d’emplacement ou de l’en-tête d’opération asynchrone Azure pour déterminer l’état de l’opération de suppression asynchrone.
  
### <a name="deletion-errors"></a>Erreurs de suppression

Quand une opération de suppression retourne une erreur, Resource Manager retente l’appel DELETE. Les nouvelles tentatives sont déclenchées avec les codes d’état 5xx, 429 et 408. Par défaut, l’intervalle entre chaque nouvelle tentative est de 15 minutes.

## <a name="after-deletion"></a>Après la suppression

Resource Manager envoie un appel GET sur chaque ressource qu’il a essayé de supprimer. La réponse attendue de cet appel GET est 404. Quand Resource Manager obtient le code 404, il considère que la suppression a été effectuée. Resource Manager supprime la ressource de son cache.

Toutefois, si l’appel GET sur la ressource retourne 200 ou 201, Resource Manager recrée la ressource.

Si l’opération GET retourne une erreur, Resource Manager relance l’appel GET pour le code d’erreur suivant :

* Inférieur à 100
* 408
* 429
* Supérieur à 500

Pour les autres codes d’erreur, Resource Manager ne parvient pas à supprimer la ressource.

## <a name="delete-resource-group"></a>Supprimer un groupe de ressources

Utilisez l’une des méthodes suivantes pour supprimer le groupe de ressources.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Dans le [portail](https://portal.azure.com), sélectionnez le groupe de ressources à supprimer.

1. Sélectionnez **Supprimer le groupe de ressources**.

   ![Supprimer un groupe de ressources](./media/resource-group-delete/delete-group.png)

1. Pour confirmer la suppression, tapez le nom du groupe de ressources.

---

## <a name="delete-resource"></a>Supprimer une ressource

Utilisez l’une des méthodes suivantes pour supprimer une ressource.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Dans le [portail](https://portal.azure.com), sélectionnez la ressource à supprimer.

1. Sélectionnez **Supprimer**. La capture d’écran suivante montre les options de gestion pour une machine virtuelle.

   ![Supprimer une ressource](./media/resource-group-delete/delete-resource.png)

1. Quand vous y êtes invité, confirmez la suppression.

---


## <a name="next-steps"></a>Étapes suivantes

* Pour comprendre les concepts de Resource Manager, consultez [Vue d’ensemble d’Azure Resource Manager](resource-group-overview.md).
* Pour les commandes de suppression, consultez [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete) et l’[API REST](/rest/api/resources/resourcegroups/delete).
