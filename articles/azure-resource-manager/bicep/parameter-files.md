---
title: Créer un fichier de paramètres pour Bicep
description: Créer un fichier de paramètres pour transmettre des valeurs pendant le déploiement d'un fichier Bicep
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: eab3052b55b1dc1033139c734605e72b5494b174
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026289"
---
# <a name="create-bicep-parameter-file"></a>Créer un fichier de paramètres Bicep

Au lieu de passer des paramètres en tant que valeurs inline dans votre script, vous pouvez utiliser un fichier JSON qui contient les valeurs des paramètres. Cet article explique comment créer un fichier de paramètres à utiliser avec un fichier Bicep.

## <a name="parameter-file"></a>Fichier de paramètres

Un fichier de paramètres utilise le format suivant :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

Notez que le fichier de paramètre stocke les valeurs de paramètre sous forme de texte brut. Cette approche fonctionne pour les valeurs qui ne sont pas sensibles, comme la référence SKU pour une ressource. Le texte brut ne fonctionne pas pour les valeurs sensibles, telles que les mots de passe. Si vous devez passer un paramètre qui contient une valeur sensible, stockez cette dernière dans un coffre de clés. Vous pouvez ensuite référencer le coffre de clés dans votre fichier de paramètres. La valeur sensible est récupérée en toute sécurité pendant le déploiement.

Le fichier de paramètres suivant comprend une valeur de texte brut et une valeur sensible stockée dans un coffre de clés.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "reference": {
        "keyVault": {
          "id": "<resource-id-key-vault>"
        },
        "secretName": "<secret-name>"
      }
    }
  }
}
```

Pour plus d’informations sur l’utilisation de valeurs d’un coffre de clés, consultez la page [Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement](./key-vault-parameter.md).

## <a name="define-parameter-values"></a>Définissez des valeurs de paramètre

Pour déterminer comment définir les noms et les valeurs des paramètres, ouvrez votre fichier Bicep. Consultez la section Paramètres du fichier Bicep. Les exemples suivants illustrent les paramètres d'un fichier Bicep.

```bicep
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageAccountType string = 'Standard_LRS'
```

Dans le fichier de paramètres, le premier détail à noter est le nom de chaque paramètre. Les noms des paramètres de votre fichier de paramètres doivent correspondre à ceux de votre fichier Bicep.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

Notez le type de paramètre. Les types de paramètres de votre fichier de paramètres doivent utiliser les mêmes types que votre fichier Bicep. Dans cet exemple, les deux types de paramètres sont des chaînes.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

Vérifiez dans le fichier Bicep les paramètres comportant une valeur par défaut. Si un paramètre a une valeur par défaut, vous pouvez fournir une valeur dans le fichier de paramètres, mais ce n’est pas obligatoire. La valeur du fichier de paramètres remplace la valeur par défaut du fichier Bicep.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Bicep will use default value if not provided.
    }
  }
}
```

Vérifiez les valeurs autorisées du fichier Bicep et les éventuelles restrictions, comme la longueur maximale. Ces valeurs spécifient la plage de valeurs que vous pouvez fournir pour un paramètre. Dans cet exemple, `storagePrefix` peut avoir un maximum de 11 caractères et `storageAccountType` doit spécifier une valeur autorisée.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

> [!NOTE]
> Votre fichier de paramètres ne peut contenir des valeurs que pour les paramètres définis dans le fichier Bicep. Si votre fichier de paramètres contient des paramètres supplémentaires qui ne correspondent pas à des paramètres définis dans le fichier Bicep, une erreur s'affiche.

## <a name="parameter-type-formats"></a>Formats de types de paramètres

L’exemple suivant illustre les formats de différents types de paramètres : chaîne, entier, booléen, tableau et objet.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
  }
}
```

## <a name="deploy-bicep-file-with-parameter-file"></a>Déployer le fichier Bicep avec le fichier de paramètres

À partir d’Azure CLI, vous transmettez un fichier de paramètres local à l’aide de `@` et du nom de fichier du paramètre. Par exemple : `@storage.parameters.json`.

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.bicep \
  --parameters @storage.parameters.json
```

Pour plus d'informations, consultez [Déployer des ressources à l'aide de Bicep et d'Azure CLI](./deploy-cli.md#parameters). Pour déployer des fichiers _.bicep_, vous devez disposer d’Azure CLI version 2.20 ou ultérieure.

À partir d’Azure PowerShell vous transmettez un fichier de paramètres local à l’aide du paramètre `TemplateParameterFile`.

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile C:\MyTemplates\storage.bicep `
  -TemplateParameterFile C:\MyTemplates\storage.parameters.json
```

Pour plus d'informations, consultez [Déployer des ressources à l'aide de Bicep et d'Azure PowerShell](./deploy-powershell.md#parameters). Pour déployer des fichiers _.bicep_, vous devez disposer d’Azure PowerShell version 5.6.0 ou ultérieure.

## <a name="file-name"></a>Nom de fichier

Généralement, vous devez inclure _parameters_ dans le nom du fichier Bicep. Par exemple, si votre fichier Bicep s'appelle _azuredeploy.bicep_, le nom de votre fichier de paramètres est _azuredeploy.parameters.json_. Cette convention d'affectation de noms vous permet de comprendre le lien entre le fichier Bicep et les paramètres.

Pour effectuer un déploiement dans différents environnements, créez plusieurs fichiers de paramètres. Lorsque vous nommez les fichiers de paramètres, identifiez leur utilisation, comme le développement ou la production. Par exemple, utilisez _azuredeploy.parameters-dev.json_ et _azuredeploy.parameters-prod.json_ pour déployer des ressources.

## <a name="parameter-precedence"></a>Priorité des paramètres

Vous pouvez utiliser des paramètres inline et un fichier de paramètres local pendant la même opération de déploiement. Par exemple, vous pouvez spécifier certaines valeurs dans le fichier de paramètres local et ajouter d’autres valeurs inline pendant le déploiement. Si vous fournissez des valeurs pour un paramètre à la fois dans le fichier de paramètres local et inline, la valeur inline est prioritaire.

Il est possible d’utiliser un fichier de paramètres externe, en indiquant l’URI du fichier. Quand vous utilisez un fichier de paramètres externe, vous ne pouvez pas transmettre d’autres valeurs, qu’elles soient incluses ou extraites d’un fichier local. Tous les paramètres inline sont ignorés. Fournissez toutes les valeurs de paramètre dans le fichier externe.

## <a name="parameter-name-conflicts"></a>Conflits de noms de paramètre

Si votre fichier Bicep inclut un paramètre utilisant le même nom que l'un des paramètres de la commande PowerShell, PowerShell présente le paramètre de votre fichier Bicep avec le suffixe `FromTemplate`. Par exemple, un paramètre nommé `ResourceGroupName` dans votre fichier Bicep est en conflit avec le paramètre `ResourceGroupName` dans la cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Vous êtes invité à fournir une valeur pour `ResourceGroupNameFromTemplate`. Pour éviter cette confusion, utilisez des noms de paramètres qui ne sont pas utilisés pour les commandes de déploiement.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d'informations sur la définition des paramètres dans un fichier Bicep, consultez [Paramètres dans Bicep](./parameters.md).
- Pour plus d’informations sur l’utilisation de valeurs d’un coffre de clés, consultez la page [Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement](./key-vault-parameter.md).
