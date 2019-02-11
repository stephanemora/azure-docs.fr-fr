---
title: Sorties de modèles Azure Resource Manager | Microsoft Docs
description: Explique comment définir des sorties dans des modèles Azure Resource Manager suivant une syntaxe JSON déclarative.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2019
ms.author: tomfitz
ms.openlocfilehash: aadc92c232d32d827644caa52b3c362d9c8d4c9b
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55691029"
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Section outputs des modèles Azure Resource Manager

Dans la section des sorties, vous spécifiez des valeurs retournées à partir du déploiement. Par exemple, vous pouvez retourner l'URI d'accès à une ressource déployée.

## <a name="define-and-use-output-values"></a>Définir et utiliser des valeurs de sortie

L’exemple suivant montre comment retourner l’ID de ressource d’une adresse IP publique :

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Après le déploiement, vous pouvez récupérer la valeur à l’aide d’un script. Pour PowerShell, utilisez la commande suivante :

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Pour l’interface de ligne de commande Azure, consultez :

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Vous pouvez récupérer la valeur de sortie à partir d’un modèle lié à l’aide de la fonction [reference](resource-group-template-functions-resource.md#reference). Pour obtenir une valeur de sortie d’un modèle lié, récupérez la valeur de propriété à l’aide d’une syntaxe comme : `"[reference('deploymentName').outputs.propertyName.value]"`.

Lors de l’obtention d’une propriété de sortie à partir d’un modèle lié, le nom de propriété ne peut pas inclure de tiret.

Par exemple, vous pouvez définir l’adresse IP sur un équilibreur de charge en récupérant une valeur à partir d’un modèle lié.

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Vous ne pouvez pas utiliser la fonction `reference` dans la section de sortie d’un [modèle imbriqué](resource-group-linked-templates.md#link-or-nest-a-template). Pour renvoyer les valeurs d’une ressource déployée dans un modèle imbriqué, convertissez votre modèle imbriqué en modèle lié.

## <a name="available-properties"></a>Propriétés disponibles

L'exemple suivant illustre la structure de la définition d'une sortie :

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Nom de l'élément | Obligatoire | Description |
|:--- |:--- |:--- |
| outputName |Oui |Nom de la valeur de sortie. Doit être un identificateur JavaScript valide. |
| Type |Oui |Type de la valeur de sortie. Les valeurs de sortie prennent en charge les mêmes types que les paramètres d'entrée du modèle. |
| value |Oui |Expression du langage du modèle évaluée et retournée sous forme de valeur de sortie. |

Pour plus d’informations sur l’ajout de commentaires, consultez [Commentaires dans les modèles](resource-group-authoring-templates.md#comments).

## <a name="example-templates"></a>Exemples de modèles

|Modèle  |Description  |
|---------|---------|
|[Copie de variables](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Crée des variables complexes et génère ces valeurs. Ne déploie aucune ressource. |
|[Adresse IP publique](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Crée une adresse IP publique et génère l’ID de ressource. |
|[Équilibreur de charge](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Est lié au modèle précédent. Utilise l’ID de ressource dans la sortie durant la création de l’équilibreur de charge. |


## <a name="next-steps"></a>Étapes suivantes
* Pour afficher des modèles complets pour de nombreux types de solutions, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).
* Pour plus d’informations sur les fonctions que vous pouvez utiliser dans un modèle, consultez [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md).
* Pour obtenir des recommandations sur la création de modèles, consultez [Bonnes pratiques relatives aux modèles Azure Resource Manager](template-best-practices.md).
