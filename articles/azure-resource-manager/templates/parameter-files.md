---
title: Créer un fichier de paramètres
description: Créer un fichier de paramètres pour transmettre des valeurs pendant le déploiement d’un modèle d’Azure Resource Manager
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: ddeaed94396aa662b795ae5701aa367ba13d869b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531217"
---
# <a name="create-resource-manager-parameter-file"></a>Créer un fichier de paramètres Resource Manager

Au lieu de passer des paramètres en tant que valeurs inline dans votre script, vous pouvez utiliser un fichier JSON qui contient les valeurs des paramètres. Cet article explique comment créer un fichier de paramètres que vous utilisez avec un modèle JSON ou un fichier Bicep.

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

Pour plus d’informations sur l’utilisation de valeurs d’un coffre de clés, consultez la page [Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement](key-vault-parameter.md).

## <a name="define-parameter-values"></a>Définissez des valeurs de paramètre

Pour déterminer comment définir les noms et les valeurs des paramètres, ouvrez votre modèle JSON ou Bicep. Consultez la section Paramètres du modèle. Les exemples suivants illustrent les paramètres des modèles JSON et Bicep.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storagePrefix": {
    "type": "string",
    "maxLength": 11
  },
  "storageAccountType": {
    "type": "string",
    "defaultValue": "Standard_LRS",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS",
    "Standard_ZRS",
    "Premium_LRS"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

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

---

Dans le fichier de paramètres, le premier détail à noter est le nom de chaque paramètre. Les noms de paramètres dans votre fichier de paramètres doivent correspondre aux noms de paramètres de votre modèle.

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

Notez le type de paramètre. Les types de paramètres dans votre fichier de paramètres doivent utiliser les mêmes types que votre modèle. Dans cet exemple, les deux types de paramètres sont des chaînes.

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

Vérifiez le modèle pour les paramètres avec une valeur par défaut. Si un paramètre a une valeur par défaut, vous pouvez fournir une valeur dans le fichier de paramètres, mais ce n’est pas obligatoire. La valeur du fichier de paramètres remplace la valeur par défaut du modèle.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Template will use default value if not provided.
    }
  }
}
```

Vérifiez les valeurs autorisées du modèle et les éventuelles restrictions, comme la longueur maximale. Ces valeurs spécifient la plage de valeurs que vous pouvez fournir pour un paramètre. Dans cet exemple, `storagePrefix` peut avoir un maximum de 11 caractères et `storageAccountType` doit spécifier une valeur autorisée.

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
> Votre fichier de paramètres peut contenir uniquement des valeurs pour les paramètres définis dans le modèle. Si votre fichier de paramètres contient des paramètres supplémentaires qui ne correspondent pas à des paramètres définis dans le modèle, une erreur s’affiche.

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

## <a name="deploy-template-with-parameter-file"></a>Déployer un modèle avec un fichier de paramètres

À partir d’Azure CLI, vous transmettez un fichier de paramètres local à l’aide de `@` et du nom de fichier du paramètre. Par exemple : `@storage.parameters.json`.

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Pour plus d’informations, consultez [Déployer des ressources à l’aide de modèles ARM et d’Azure CLI](./deploy-cli.md#parameters). Pour déployer des fichiers _.bicep_, vous devez disposer d’Azure CLI version 2.20 ou ultérieure.

À partir d’Azure PowerShell vous transmettez un fichier de paramètres local à l’aide du paramètre `TemplateParameterFile`.

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile C:\MyTemplates\storage.json `
  -TemplateParameterFile C:\MyTemplates\storage.parameters.json
```

Pour plus d’informations, consultez [Déployer des ressources à l’aide de modèles ARM et d’Azure PowerShell](./deploy-powershell.md#pass-parameter-values). Pour déployer des fichiers _.bicep_, vous devez disposer d’Azure PowerShell version 5.6.0 ou ultérieure.

> [!NOTE]
> Il n’est pas possible d’utiliser un fichier de paramètres avec le panneau de modèle personnalisé dans le portail.

> [!TIP]
> Si vous utilisez le [projet de groupe de ressources Azure dans Visual Studio](create-visual-studio-deployment-project.md), vérifiez que l’**Action de génération** du fichier de paramètres est définie sur **Contenu**.

## <a name="file-name"></a>Nom de fichier

Généralement, vous devez inclure _parameters_ au nom du modèle pour nommer le fichier de paramètres. Par exemple, si votre modèle est nommé _azuredeploy.json_, votre fichier de paramètres est _azuredeploy.parameters.json_. Cette convention d’affectation de noms vous permet de comprendre le lien entre le modèle et les paramètres.

Pour effectuer un déploiement dans différents environnements, créez plusieurs fichiers de paramètres. Lorsque vous nommez les fichiers de paramètres, identifiez leur utilisation, comme le développement ou la production. Par exemple, utilisez _azuredeploy.parameters-dev.json_ et _azuredeploy.parameters-prod.json_ pour déployer des ressources.

## <a name="parameter-precedence"></a>Priorité des paramètres

Vous pouvez utiliser des paramètres inline et un fichier de paramètres local pendant la même opération de déploiement. Par exemple, vous pouvez spécifier certaines valeurs dans le fichier de paramètres local et ajouter d’autres valeurs inline pendant le déploiement. Si vous fournissez des valeurs pour un paramètre à la fois dans le fichier de paramètres local et inline, la valeur inline est prioritaire.

Il est possible d’utiliser un fichier de paramètres externe, en indiquant l’URI du fichier. Quand vous utilisez un fichier de paramètres externe, vous ne pouvez pas transmettre d’autres valeurs, qu’elles soient incluses ou extraites d’un fichier local. Tous les paramètres inline sont ignorés. Fournissez toutes les valeurs de paramètre dans le fichier externe.

## <a name="parameter-name-conflicts"></a>Conflits de noms de paramètre

Si votre modèle inclut un paramètre utilisant le même nom que l’un des paramètres dans la commande PowerShell, PowerShell présente le paramètre de votre modèle avec le suffixe `FromTemplate`. Par exemple, un paramètre nommé `ResourceGroupName` dans votre modèle est en conflit avec le paramètre `ResourceGroupName` dans la cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Vous êtes invité à fournir une valeur pour `ResourceGroupNameFromTemplate`. Pour éviter cette confusion, utilisez des noms de paramètres qui ne sont pas utilisés pour les commandes de déploiement.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la façon de définir des paramètres dans un modèle, consultez [Paramètres dans les modèles ARM](template-parameters.md).
- Pour plus d’informations sur l’utilisation de valeurs d’un coffre de clés, consultez la page [Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement](key-vault-parameter.md).
