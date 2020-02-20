---
title: Fonctions et déploiement du modèle
description: Décrit les fonctions à utiliser dans un modèle Azure Resource Manager pour récupérer des informations de déploiement.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: b241aaf43ee3204c9960d0099ce3c61d4c1a80ee
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207279"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Fonctions de déploiement pour les modèles Azure Resource Manager 

Resource Manager offre les fonctions ci-après pour l’obtention de valeurs associées au déploiement actuel :

* [deployment](#deployment)
* [environment](#environment)
* [parameters](#parameters)
* [variables](#variables)

Pour obtenir des valeurs de ressources, de groupes de ressources ou d’abonnements, consultez [Fonctions de ressource](template-functions-resource.md).

## <a name="deployment"></a>deployment

`deployment()`

Renvoie des informations sur l’opération de déploiement actuelle.

### <a name="return-value"></a>Valeur retournée

Cette fonction retourne l’objet transmis au cours du déploiement. Les propriétés de l’objet renvoyé diffèrent selon que l’objet de déploiement est passé sous forme de lien ou d’objet inline. Quand l’objet de déploiement est passé inline, comme lors de l’utilisation du paramètre **-TemplateFile** dans Azure PowerShell pour pointer vers un fichier local, l’objet renvoyé a le format suivant :

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Quand l’objet est passé comme lien, par exemple lors de l’utilisation du paramètre **-TemplateUri** pour pointer vers un objet distant, l’objet est retourné dans le format suivant : 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Lors d’un [déploiement sur un abonnement Azure](deploy-to-subscription.md), l’objet retourné inclut une propriété `location` au lieu d’un groupe de ressources. La propriété d’emplacement est incluse lors du déploiement d’un modèle local ou d’un modèle externe.

### <a name="remarks"></a>Notes

Vous pouvez utiliser deployment() pour établir une liaison à un autre modèle en fonction de l’URI du modèle parent.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

Si vous redéployez un modèle à partir de l’historique de déploiement dans le portail, le modèle est déployé comme un fichier local. La propriété `templateLink` n’est pas retournée dans la fonction de déploiement. Si votre modèle s’appuie sur `templateLink` pour construire un lien vers un autre modèle, n’utilisez pas le portail pour redéployer. À la place, utilisez les commandes dont vous vous êtes servi pour déployer le modèle à l’origine.

### <a name="example"></a>Exemple

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) suivant retourne l’objet de déploiement :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

L’exemple précédent retourne l’objet suivant :

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

Pour un modèle de niveau d’abonnement qui utilise la fonction de déploiement, consultez [Fonction de déploiement d’abonnement](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json). Le déploiement s’effectue à l’aide des commandes `az deployment create` ou `New-AzDeployment`.

## <a name="environment"></a>Environnement

`environment()`

Retourne des informations sur l’environnement Azure utilisé pour le déploiement.

### <a name="return-value"></a>Valeur retournée

Cette valeur retourne des propriétés pour l’environnement Azure actuel. L’exemple suivant présente les propriétés pour Azure international. Les clouds souverains peuvent retourner des propriétés légèrement différentes.

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>Exemple

L’exemple de modèle suivant retourne l’objet d’environnement.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "environmentOutput": {
            "value": "[environment()]",
            "type" : "object"
        }
    }
}
```

L’exemple précédent retourne l’objet suivant lorsqu’il est déployé vers Azure international :

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Retourne une valeur de paramètre. Le nom de paramètre spécifié doit être défini dans la section parameters du modèle.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| parameterName |Oui |string |Nom du paramètre à retourner. |

### <a name="return-value"></a>Valeur retournée

La valeur du paramètre spécifié.

### <a name="remarks"></a>Notes

En général, vous utilisez les paramètres pour définir les valeurs de la ressource. L’exemple suivant définit le nom du site web sur la valeur du paramètre transmise au cours du déploiement.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>Exemple

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) suivant montre une utilisation simplifiée de la fonction parameters.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| stringOutput | String | option 1 |
| intOutput | Int | 1 |
| objectOutput | Object | {"one": "a", "two": "b"} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | String | option 1 |

Pour plus d’informations sur l’utilisation des paramètres, consultez [Paramètres dans les modèles Azure Resource Manager](template-parameters.md).

## <a name="variables"></a>variables

`variables(variableName)`

Retourne la valeur de la variable. Le nom de variable spécifié doit être défini dans la section variables du modèle.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| variableName |Oui |String |Nom de la variable à retourner. |

### <a name="return-value"></a>Valeur retournée

La valeur de la variable spécifiée.

### <a name="remarks"></a>Notes

En général, vous utilisez les variables pour simplifier votre modèle en créant des valeurs complexes une seule fois. L’exemple suivant crée un nom unique pour un compte de stockage.

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>Exemple

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) suivant retourne différentes valeurs de variables.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| exampleOutput1 | String | myVariable |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | String | myVariable |
| exampleOutput4 |  Object | {"property1": "value1", "property2": "value2"} |

Pour plus d’informations sur l’utilisation des variables, consultez [Variables dans un modèle Azure Resource Manager](template-variables.md).

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir une description des sections d’un modèle Azure Resource Manager, consultez [Création de modèles Azure Resource Manager](template-syntax.md).
* Pour fusionner plusieurs modèles, consultez [Utilisation de modèles liés avec Azure Resource Manager](linked-templates.md).
* Pour itérer un nombre de fois spécifié lors de la création d'un type de ressource, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](copy-resources.md).
* Pour savoir comment déployer le modèle que vous avez créé, consultez [Déployer une application avec un modèle Azure Resource Manager](deploy-powershell.md).

