---
title: Fonctions Bicep - Déploiement
description: Décrit les fonctions à utiliser dans un fichier Bicep pour récupérer des informations de déploiement.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 53e9f34e2d04f68add7babd8c12b4fd583015847
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026368"
---
# <a name="deployment-functions-for-bicep"></a>Fonctions de déploiement pour Bicep

Resource Manager offre les fonctions ci-après pour l’obtention de valeurs associées au déploiement actuel de votre fichier Bicep :

* [deployment](#deployment)
* [environment](#environment)

Pour obtenir des valeurs de ressources, de groupes de ressources ou d’abonnements, consultez [Fonctions de ressource](./bicep-functions-resource.md).

## <a name="deployment"></a>deployment

`deployment()`

Renvoie des informations sur l’opération de déploiement actuelle.

### <a name="return-value"></a>Valeur retournée

Cette fonction retourne l’objet transmis au cours du déploiement. Les propriétés de l’objet retourné diffèrent selon les scénarios suivants :

* déploiement d’un fichier Bicep local.
* Vous déployez un groupe de ressources ou sur l’une des autres étendues ([abonnement Azure](deploy-to-subscription.md), [groupe d’administration](deploy-to-management-group.md) ou [locataire](deploy-to-tenant.md)).

Lors du déploiement d’un fichier Bicep local dans un groupe de ressources, la fonction retourne le format suivant :

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

Lorsque vous déployez sur un abonnement Azure, un groupe d’administration ou un locataire, l’objet retourné comprend une propriété `location`. La propriété location est incluse lors du déploiement d’un fichier Bicep local. Le format est le suivant :

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

### <a name="example"></a>Exemple

L’exemple suivant retourne l’objet de déploiement :

```bicep
output deploymentOutput object = deployment()
```

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

L’exemple de fichier Bicep suivant retourne l’objet d’environnement.

```bicep
output environmentOutput object = environment()
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

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une description des sections d’un fichier Bicep, consultez [comprendre la structure et la syntaxe des fichiers Bicep](./file.md).
