---
title: Sorties dans des modèles
description: Explique comment définir des valeurs de sortie dans un modèle Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: b4c652d71436202b9b6e551f9c582e5c98508259
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149188"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Sorties dans un modèle Azure Resource Manager

Cet article explique comment définir des valeurs de sortie dans votre modèle Azure Resource Manager. Vous utilisez des sorties quand vous devez retourner des valeurs à partir des ressources déployées.

## <a name="define-output-values"></a>Définir des valeurs de sortie

L’exemple suivant montre comment retourner l’ID de ressource d’une adresse IP publique :

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Sortie conditionnelle

Dans la section outputs, vous pouvez retourner une valeur de manière conditionnelle. En général, vous utilisez une condition dans les sorties quand vous avez [déployé de manière conditionnelle](conditional-resource-deployment.md) une ressource. L’exemple suivant montre comment retourner de façon conditionnelle l’ID de ressource pour une adresse IP publique si une nouvelle a été déployée :

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Pour obtenir un exemple simple de sortie conditionnelle, consultez [Modèle de sortie conditionnelle](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="linked-templates"></a>Modèles liés

Pour récupérer la valeur de sortie à partir d’un modèle lié, utilisez la fonction [reference](resource-group-template-functions-resource.md#reference) dans le modèle parent. La syntaxe dans le modèle parent est la suivante :

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Lors de l’obtention d’une propriété de sortie à partir d’un modèle lié, le nom de propriété ne peut pas inclure de tiret.

L’exemple suivant montre comment définir l’adresse IP sur un équilibreur de charge en récupérant une valeur à partir d’un modèle lié.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Vous ne pouvez pas utiliser la fonction `reference` dans la section de sortie d’un [modèle imbriqué](resource-group-linked-templates.md#nested-template). Pour renvoyer les valeurs d’une ressource déployée dans un modèle imbriqué, convertissez votre modèle imbriqué en modèle lié.

## <a name="get-output-values"></a>Obtenir des valeurs de sortie

Une fois le déploiement terminé, les valeurs de sortie sont automatiquement retournées dans les résultats du déploiement.

Pour obtenir des valeurs de sortie à partir de l’historique des déploiements, vous pouvez utiliser un script.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

```azurecli-interactive
az group deployment show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Exemples de modèles

Les exemples suivants illustrent des scénarios d’utilisation de sorties.

|Modèle  |Description  |
|---------|---------|
|[Copie de variables](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Crée des variables complexes et génère ces valeurs. Ne déploie aucune ressource. |
|[Adresse IP publique](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Crée une adresse IP publique et génère l’ID de ressource. |
|[Équilibreur de charge](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Est lié au modèle précédent. Utilise l’ID de ressource dans la sortie durant la création de l’équilibreur de charge. |

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les propriétés disponibles pour les sorties, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour obtenir des recommandations sur la création de sorties, consultez [Bonnes pratiques - Sorties](template-best-practices.md#outputs).
