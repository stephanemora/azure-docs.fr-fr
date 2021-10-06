---
title: Exporter un modèle dans Azure PowerShell
description: Utilisez Azure PowerShell pour exporter un modèle Azure Resource Manager à partir de ressources de votre abonnement.
ms.topic: conceptual
ms.date: 09/03/2021
ms.openlocfilehash: 15a81b845811f870be783807cf680c724413bf6c
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479151"
---
# <a name="use-azure-powershell-to-export-a-template"></a>Utiliser Azure PowerShell pour exporter un modèle

[!INCLUDE [Export template intro](../../../includes/resource-manager-export-template-intro.md)]

Cet article explique comment exporter des modèles via **Azure PowerShell**. Pour d'autres options, voir :

* [Exporter un modèle avec Azure CLI](export-template-cli.md)
* [Exporter un modèle avec le portail Azure](export-template-portal.md)
* [Exportation de l’API REST à partir d’un groupe de ressources](/rest/api/resources/resourcegroups/exporttemplate) et [Exportation de l’API REST à partir de l’historique de déploiement](/rest/api/resources/deployments/export-template).

[!INCLUDE [Export template choose option](../../../includes/resource-manager-export-template-choose-option.md)]

[!INCLUDE [Export template limitations](../../../includes/resource-manager-export-template-limitations.md)]

## <a name="export-template-from-a-resource-group"></a>Exportation du modèle à partir d’un groupe de ressources

Une fois que vous avez configuré votre groupe de ressources, vous pouvez exporter un modèle Resource Manager pour celui-ci. 

Pour exporter toutes les ressources d’un groupe de ressources, utilisez l’applet de commande [AzResourceGroup d’exportation](/powershell/module/az.resources/Export-AzResourceGroup) et indiquez le nom du groupe de ressources.

```azurepowershell-interactive
Export-AzResourceGroup -ResourceGroupName demoGroup
```

Il enregistre le modèle dans un fichier local.

Plutôt que d’exporter toutes les ressources dans le groupe de ressources, vous pouvez sélectionner les ressources à exporter.

Pour exporter une ressource, transmettez cet ID de ressource.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Pour exporter plusieurs ressources, transmettez les ID de ressource dans un tableau.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Lorsque vous exportez le modèle, vous pouvez spécifier si des paramètres sont utilisés dans celui-ci. Par défaut, les paramètres des noms de ressource sont inclus, mais ils n’ont pas de valeur par défaut.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "type": "String"
  }
}
```

Si vous utilisez le paramètre `-SkipResourceNameParameterization` lors de l’exportation du modèle, les paramètres des noms de ressources ne sont pas inclus dans le modèle. Au lieu de cela, le nom de ressource est défini directement sur la ressource sur sa valeur actuelle. Vous ne pouvez pas personnaliser le nom au cours du déploiement.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

Si vous utilisez le paramètre `-IncludeParameterDefaultValue` lors de l’exportation du modèle, le paramètre de modèle inclut une valeur par défaut qui est définie sur la valeur actuelle. Vous pouvez utiliser cette valeur par défaut ou remplacer la valeur par défaut en transmettant une valeur différente.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

## <a name="save-template-from-deployment-history"></a>Enregistrer un modèle à partir de l’historique de déploiement

Vous pouvez sauvegarder un modèle à partir d’un déploiement dans l’historique du déploiement. Le modèle que vous obtenez est exactement celui qui a été utilisé pour le déploiement. 

Pour obtenir un modèle à partir d’un déploiement de groupe de ressources, utilisez l’applet de commande [Save-AzResourceGroupDeploymentTemplate](/powershell/module/az.resources/save-azresourcegroupdeploymenttemplate). Vous spécifiez le nom du déploiement à récupérer. Pour obtenir le nom d’un déploiement, consultez la section [Afficher l’historique du déploiement avec Azure Resource Manager](deployment-history.md).

```azurepowershell-interactive
Save-AzResourceGroupDeploymentTemplate -ResourceGroupName demoGroup -DeploymentName demoDeployment
```

Le modèle est enregistré en tant que fichier local avec le nom du déploiement.

Pour obtenir des modèles déployés à d’autres niveaux, utilisez :

* [Save-AzDeploymentTemplate](/powershell/module/az.resources/save-azdeploymenttemplate) pour les déploiements vers des abonnements
* [Save-AzManagementGroupDeploymentTemplate](/powershell/module/az.resources/save-azmanagementgroupdeploymenttemplate) pour les déploiements vers des groupes d’administration
* [Save-AzTenantDeploymentTemplate](/powershell/module/az.resources/save-aztenantdeploymenttemplate) pour les déploiements vers des locataires

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment exporter des modèles avec [Azure CLI](export-template-cli.md), le[portail Azure](export-template-portal.md) ou l’[API REST](/rest/api/resources/resourcegroups/exporttemplate).
- Pour vous familiariser avec la syntaxe des modèles Resource Manager, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](./syntax.md).
- Pour apprendre à développer des modèles, consultez les [tutoriels pas à pas](../index.yml).