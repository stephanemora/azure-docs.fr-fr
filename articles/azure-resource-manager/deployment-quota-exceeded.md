---
title: Dépassement du quota de déploiement Azure
description: Explique comment résoudre l'erreur liée à la présence de plus de 800 déploiements dans l'historique du groupe de ressources.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: tomfitz
ms.openlocfilehash: 755383c9d40c104d50ad9bb7a31b3a00f8348313
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827012"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Résoudre l'erreur liée à un nombre de déploiements supérieur à 800

L'historique de déploiement de chaque groupe de ressources est limité à 800 déploiements. Cet article décrit l'erreur que vous recevez lorsqu'un déploiement échoue pour cause de dépassement du quota des 800 déploiements autorisés. Pour résoudre cette erreur, supprimez des déploiements de l'historique du groupe de ressources. La suppression d'un déploiement de l'historique n'a aucun impact sur les ressources déployées.

## <a name="symptom"></a>Symptôme

Pendant un déploiement, vous recevez une erreur indiquant que celui-ci va entraîner un dépassement du quota de 800 déploiements.

## <a name="solution"></a>Solution

### <a name="azure-cli"></a>D’Azure CLI

Utilisez la commande [az group deployment delete](/cli/azure/group/deployment#az-group-deployment-delete) pour supprimer des déploiements de l'historique.

```azurecli-interactive
az group deployment delete --resource-group exampleGroup --name deploymentName
```

Pour connaître le nombre de déploiements actuellement contenu dans l'historique, utilisez la commande suivante :

```azurecli-interactive
az group deployment list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Utilisez la commande [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) pour supprimer des déploiements de l'historique.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Pour connaître le nombre de déploiements actuellement contenu dans l'historique, utilisez la commande suivante :

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Solutions tierces

Les solutions externes suivantes s'appliquent à des scénarios spécifiques :

* [Solutions Azure Logic Apps et PowerShell](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [Extension AzDevOps](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
