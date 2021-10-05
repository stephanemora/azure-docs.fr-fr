---
title: Exporter un modèle dans Azure CLI
description: Utilisez Azure CLI pour exporter un modèle Azure Resource Manager à partir des ressources de votre abonnement.
ms.topic: conceptual
ms.date: 09/03/2021
ms.openlocfilehash: b60c7ae3787b2b22b09eb844a9206b9530fb68df
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123480873"
---
# <a name="use-azure-cli-to-export-a-template"></a>Utiliser Azure CLI pour exporter un modèle

[!INCLUDE [Export template intro](../../../includes/resource-manager-export-template-intro.md)]

Cet article montre comment exporter des modèles via **Azure CLI**. Pour d'autres options, voir :

* [Modèle d'exportation avec le portail Azure](export-template-portal.md)
* [Exporter un modèle avec Azure PowerShell](export-template-powershell.md)
* [Exportation API REST du groupe de ressources](/rest/api/resources/resourcegroups/exporttemplate) et [exportation API REST de l'historique de déploiement](/rest/api/resources/deployments/export-template).

[!INCLUDE [Export template choose option](../../../includes/resource-manager-export-template-choose-option.md)]

[!INCLUDE [Export template limitations](../../../includes/resource-manager-export-template-limitations.md)]

## <a name="export-template-from-a-resource-group"></a>Exportation du modèle à partir d’un groupe de ressources

Après avoir correctement configuré votre groupe de ressources, vous pouvez afficher le modèle Resource Manager correspondant.

Pour exporter toutes les ressources d’un groupe de ressources, utilisez [az group export](/cli/azure/group#az_group_export) et indiquez le nom du groupe de ressources.

```azurecli-interactive
az group export --name demoGroup
```

Le script affiche le modèle sur la console. Pour enregistrer dans un fichier, utilisez :

```azurecli-interactive
az group export --name demoGroup > exportedtemplate.json
```

Plutôt que d’exporter toutes les ressources dans le groupe de ressources, vous pouvez sélectionner les ressources à exporter.

Pour exporter une ressource, transmettez cet ID de ressource.

```azurecli-interactive
storageAccountID=$(az resource show --resource-group demoGroup --name demostg --resource-type Microsoft.Storage/storageAccounts --query id --output tsv)
az group export --resource-group demoGroup --resource-ids $storageAccountID
```

Pour exporter plusieurs ressources, transmettez les ID de ressource séparés par des espaces. Pour exporter toutes les ressources, ne spécifiez pas cet argument ou indiquez « * ».

```azurecli-interactive
az group export --resource-group <resource-group-name> --resource-ids $storageAccountID1 $storageAccountID2
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

Si vous utilisez le paramètre `--skip-resource-name-params` lors de l’exportation du modèle, les paramètres des noms de ressources ne sont pas inclus dans le modèle. Au lieu de cela, le nom de ressource est défini directement sur la ressource sur sa valeur actuelle. Vous ne pouvez pas personnaliser le nom au cours du déploiement.

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

Si vous utilisez le paramètre `--include-parameter-default-value` lors de l’exportation du modèle, le paramètre de modèle inclut une valeur par défaut qui est définie sur la valeur actuelle. Vous pouvez utiliser cette valeur par défaut ou remplacer la valeur par défaut en transmettant une valeur différente.

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

Vous pouvez sauvegarder un modèle à partir d'un déploiement dans l'historique du déploiement. Le modèle que vous obtenez est exactement celui qui a été utilisé pour le déploiement.

Pour obtenir un modèle à partir d'un déploiement de groupe de ressources, utilisez la commande [az deployment group export](/cli/azure/deployment/group#az_deployment_group_export). Vous spécifiez le nom du déploiement à récupérer. Pour obtenir le nom d'un déploiement, consultez la section [Afficher l'historique du déploiement avec Azure Resource Manager](deployment-history.md).

```azurecli-interactive
az deployment group export --resource-group demoGroup --name demoDeployment 
```

Le modèle est affiché dans la console. Pour sauvegarder le fichier, utilisez :

```azurecli-interactive
az deployment group export --resource-group demoGroup --name demoDeployment > demoDeployment.json
```

Pour obtenir des modèles déployés à d'autres niveaux, utilisez :

* [exportation de sous-déploiement az](/cli/azure/deployment/sub#az_deployment_sub_export) pour les déploiements vers les abonnements
* [exportation de mg de déploiement az](/cli/azure/deployment/mg#az_deployment_mg_export) pour les déploiements vers les groupes de gestion
* [exportation du locataire de déploiement az](/cli/azure/deployment/tenant#az_deployment_tenant_export) pour les déploiements vers les locataires


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment exporter des modèles avec [Portail Azure](export-template-portal.md), [Azure PowerShell](export-template-powershell.md) ou l'[API REST](/rest/api/resources/resourcegroups/exporttemplate).
- Pour vous familiariser avec la syntaxe des modèles Resource Manager, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](./syntax.md).
- Pour apprendre à développer des modèles, consultez les [tutoriels pas à pas](../index.yml).