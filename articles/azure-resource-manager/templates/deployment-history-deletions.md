---
title: Suppressions de l’historique de déploiement
description: Décrit la manière dont Azure Resource Manager supprime automatiquement les déploiements de l’historique de déploiement. Les déploiements sont supprimés lorsque l’historique approche de la limite des 800 déploiements.
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 3e48b2da00986da00f7597cf887aa74f84587710
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84122335"
---
# <a name="automatic-deletions-from-deployment-history"></a>Suppressions automatiques de l’historique de déploiement

Chaque fois que vous déployez un modèle, les informations concernant le déploiement sont écrites dans l’historique de déploiement. L'historique de déploiement de chaque groupe de ressources est limité à 800 déploiements.

À compter de juin 2020, Azure Resource Manager supprime automatiquement les déploiements de votre historique lorsque vous vous approchez de la limite. Cette suppression automatique est un changement de comportement par rapport au passé. Auparavant, vous deviez supprimer manuellement les déploiements de l’historique des déploiements pour éviter d’obtenir une erreur.

> [!NOTE]
> La suppression d'un déploiement de l'historique n'a aucun impact sur les ressources déployées.

## <a name="when-deployments-are-deleted"></a>Lors de la suppression des déploiements

Les déploiements sont supprimés de votre historique uniquement lorsque vous approchez la limite des 800 déploiements. Azure Resource Manager supprime une petite série des déploiements les plus anciens pour libérer de l’espace pour les déploiements futurs. La majeure partie de votre historique reste inchangée. Les déploiements les plus anciens sont toujours supprimés en premier.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Suppressions de l’historique de déploiement":::

En plus des déploiements, vous déclenchez également des suppressions lorsque vous exécutez l’[opération de simulation](template-deploy-what-if.md) ou validez un déploiement.

Lorsque vous donnez à un déploiement le même nom qu’un autre dans l’historique, vous réinitialisez sa place dans l’historique. Le déploiement passe au rang le plus récent dans l’historique. Vous pouvez également réinitialiser le rang d’un déploiement lorsque vous [restaurer ce déploiement](rollback-on-error.md) après une erreur.

## <a name="opt-out-of-automatic-deletions"></a>Refuser les suppressions automatiques

Vous pouvez refuser les suppressions automatiques de l’historique. **Utilisez cette option uniquement lorsque vous souhaitez gérer vous-même l’historique des déploiements.** La limite de 800 déploiements dans l’historique est toujours en vigueur. Si vous dépassez les 800 déploiements, vous recevrez une erreur et votre déploiement échouera.

Pour désactiver les suppressions automatiques, inscrivez l’indicateur de fonctionnalité `Microsoft.Resources/DisableDeploymentGrooming`. Lorsque vous enregistrez l’indicateur de fonctionnalité, vous refusez les suppressions automatiques pour l’intégralité de l’abonnement Azure. Vous ne pouvez pas refuser uniquement pour un groupe de ressources particulier.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour PowerShell, utilisez [Register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Pour afficher l’état actuel de votre abonnement, utilisez :

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour Azure CLI, utilisez [az feature register](/cli/azure/feature#az-feature-register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Pour afficher l’état actuel de votre abonnement, utilisez :

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
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

---

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur l’affichage de l’historique des déploiements, consultez [Afficher l’historique des déploiements avec Azure Resource Manager](deployment-history.md).
