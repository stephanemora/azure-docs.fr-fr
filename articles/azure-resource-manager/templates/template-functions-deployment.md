---
title: Fonctions et déploiement du modèle
description: Décrit les fonctions à utiliser dans un modèle Azure Resource Manager (modèle ARM) pour récupérer des informations de déploiement.
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 12f449cd70f0ccbeeb0d232299f38fb814a8cb24
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124733329"
---
# <a name="deployment-functions-for-arm-templates"></a>Fonctions de déploiement pour les modèles ARM

Resource Manager fournit les fonctions suivantes pour obtenir des valeurs liées au déploiement actuel de votre modèle Azure Resource Manager (modèle ARM) :

* [deployment](#deployment)
* [environment](#environment)
* [parameters](#parameters)
* [variables](#variables)

Pour obtenir des valeurs de ressources, de groupes de ressources ou d’abonnements, consultez [Fonctions de ressource](template-functions-resource.md).

## <a name="deployment"></a>deployment

`deployment()`

Renvoie des informations sur l’opération de déploiement actuelle.

### <a name="return-value"></a>Valeur retournée

Cette fonction retourne l’objet transmis au cours du déploiement. Les propriétés de l’objet retourné diffèrent selon les scénarios suivants :

* déploiement d’un modèle ou d’une spec de modèle.
* Vous déployez un modèle qui est soit un fichier local, soit un fichier distant accessible via un URI.
* Vous déployez un groupe de ressources ou sur l’une des autres étendues ([abonnement Azure](deploy-to-subscription.md), [groupe d’administration](deploy-to-management-group.md) ou [locataire](deploy-to-tenant.md)).

Lors du déploiement d’un modèle local dans un groupe de ressources, la fonction retourne le format suivant :

```json
{
  "name": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

Lors du déploiement d’un modèle distant dans un groupe de ressources, la fonction retourne le format suivant :

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
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

Lors du déploiement d’une spec de modèle dans un groupe de ressources, la fonction retourne le format suivant :

```json
{
  "name": "",
  "properties": {
    "templateLink": {
      "id": ""
    },
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

Lorsque vous déployez sur un abonnement Azure, un groupe d’administration ou un locataire, l’objet retourné comprend une propriété `location`. La propriété d’emplacement est incluse lors du déploiement d’un modèle local ou d’un modèle externe. Le format est le suivant :

```json
{
  "name": "",
  "location": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

### <a name="remarks"></a>Notes 

Vous pouvez utiliser `deployment()` pour établir une liaison à un autre modèle en fonction de l’URI du modèle parent.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Si vous redéployez un modèle à partir de l’historique de déploiement dans le portail, le modèle est déployé comme un fichier local. La propriété `templateLink` n’est pas retournée dans la fonction de déploiement. Si votre modèle s’appuie sur `templateLink` pour construire un lien vers un autre modèle, n’utilisez pas le portail pour redéployer. À la place, utilisez les commandes dont vous vous êtes servi pour déployer le modèle à l’origine.

### <a name="example"></a>Exemple

L’exemple suivant retourne un objet de déploiement.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/deployment.json":::

L’exemple précédent retourne l’objet suivant :

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "deploymentOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "templateHash": "13135986259522608210",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

Dans le cas d’un déploiement d’abonnement, l’exemple suivant retourne un objet de déploiement.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/deploymentsubscription.json":::

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

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/environment.json":::

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

Dans Bicep, référencez directement les paramètres à l’aide de leurs noms symboliques.

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
}, "resources": [
  {
    "type": "Microsoft.Web/Sites",
    "apiVersion": "2016-08-01",
    "name": "[parameters('siteName')]",
    ...
  }
]
```

### <a name="example"></a>Exemple

L'exemple suivant montre une utilisation simplifiée de la fonction parameters.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/parameters.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| stringOutput | String | option 1 |
| intOutput | Int | 1 |
| objectOutput | Object | {"one": "a", "two": "b"} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | String | option 1 |

Pour plus d’informations sur l’utilisation des paramètres, consultez [Paramètres dans les modèles ARM](./parameters.md).

## <a name="variables"></a>variables

`variables(variableName)`

Retourne la valeur de la variable. Le nom de variable spécifié doit être défini dans la section variables du modèle.

Dans Bicep, référencez directement les variables à l’aide de leurs noms symboliques.

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

L’exemple suivant retourne différentes valeurs de variables.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/variables.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| exampleOutput1 | String | myVariable |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | String | myVariable |
| exampleOutput4 |  Object | {"property1": "value1", "property2": "value2"} |

Pour plus d’informations sur l’utilisation des variables, consultez [Variables dans un modèle ARM](./variables.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une description des sections d’un modèle ARM, consultez [Comprendre la structure et la syntaxe des modèles ARM](./syntax.md).
