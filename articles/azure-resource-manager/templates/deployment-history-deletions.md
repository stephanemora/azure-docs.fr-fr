---
title: Suppressions de l’historique de déploiement
description: Décrit la manière dont Azure Resource Manager supprime automatiquement les déploiements de l’historique de déploiement. Les déploiements sont supprimés lorsque l’historique approche de la limite des 800 déploiements.
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 8ec3291dc5e35689d4e2c614949e0328057fbfd3
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86248977"
---
# <a name="automatic-deletions-from-deployment-history"></a>Suppressions automatiques de l’historique de déploiement

Chaque fois que vous déployez un modèle, les informations concernant le déploiement sont écrites dans l’historique de déploiement. L'historique de déploiement de chaque groupe de ressources est limité à 800 déploiements.

Azure Resource Manager commencera bientôt à supprimer automatiquement les déploiements de votre historique lorsque vous vous approchez de la limite. Cette suppression automatique est un changement de comportement par rapport au passé. Auparavant, vous deviez supprimer manuellement les déploiements de l’historique des déploiements pour éviter d’obtenir une erreur. **Cette fonctionnalité n’a pas encore été ajoutée à Azure. Nous vous informons de cette modification à venir, au cas où vous souhaiteriez refuser.**

> [!NOTE]
> La suppression d'un déploiement de l'historique n'a aucun impact sur les ressources déployées.
>
> Si vous avez un [verrou CanNotDelete](../management/lock-resources.md) sur un groupe de ressources, les déploiements pour ce groupe de ressources ne peuvent pas être supprimés. Vous devez supprimer le verrou pour tirer parti des suppressions automatiques dans l’historique de déploiement.

## <a name="when-deployments-are-deleted"></a>Lors de la suppression des déploiements

Les déploiements sont supprimés de votre historique lorsque vous approchez la limite des 775 déploiements ou plus. Azure Resource Manager supprime les déploiements jusqu’à ce que l’historique soit à 750. Les déploiements les plus anciens sont toujours supprimés en premier.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Suppressions de l’historique de déploiement":::

> [!NOTE]
> Le nombre de départ (775) et le nombre de fin (750) sont susceptibles d’être modifiés.
>
> Si votre groupe de ressources est déjà à la limite de 800, le prochain déploiement échoue avec une erreur. Le processus de suppression automatique démarre immédiatement. Vous pouvez réessayer votre déploiement après une brève attente.

En plus des déploiements, vous déclenchez également des suppressions lorsque vous exécutez l’[opération de simulation](template-deploy-what-if.md) ou validez un déploiement.

Lorsque vous donnez à un déploiement le même nom qu’un autre dans l’historique, vous réinitialisez sa place dans l’historique. Le déploiement passe au rang le plus récent dans l’historique. Vous pouvez également réinitialiser le rang d’un déploiement lorsque vous [restaurer ce déploiement](rollback-on-error.md) après une erreur.

## <a name="opt-out-of-automatic-deletions"></a>Refuser les suppressions automatiques

Vous pouvez refuser les suppressions automatiques de l’historique. **Utilisez cette option uniquement lorsque vous souhaitez gérer vous-même l’historique des déploiements.** La limite de 800 déploiements dans l’historique est toujours en vigueur. Si vous dépassez les 800 déploiements, vous recevrez une erreur et votre déploiement échouera.

Pour désactiver les suppressions automatiques, inscrivez l’indicateur de fonctionnalité `Microsoft.Resources/DisableDeploymentGrooming`. Lorsque vous enregistrez l’indicateur de fonctionnalité, vous refusez les suppressions automatiques pour l’intégralité de l’abonnement Azure. Vous ne pouvez pas refuser uniquement pour un groupe de ressources particulier. Pour réactiver les suppressions automatiques, désinscrivez l’indicateur de fonctionnalité.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour PowerShell, utilisez [Register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Pour afficher l’état actuel de votre abonnement, utilisez :

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Pour réactiver les suppressions automatiques, utilisez l’API REST Azure ou Azure CLI.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour Azure CLI, utilisez [az feature register](/cli/azure/feature#az-feature-register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Pour afficher l’état actuel de votre abonnement, utilisez :

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Pour réactiver les suppressions automatiques, utilisez [az Feature Unregister](/cli/azure/feature#az-feature-unregister).

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

Pour l’API REST, utilisez [Fonctionnalités – Inscrire](/rest/api/resources/features/register).

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Pour afficher l’état actuel de votre abonnement, utilisez :

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Pour réactiver les suppressions automatiques, utilisez [fonctionnalités - annuler l’inscription](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur l’affichage de l’historique des déploiements, consultez [Afficher l’historique des déploiements avec Azure Resource Manager](deployment-history.md).
