---
title: Modules Bicep
description: Décrit comment définir et consommer un module et comment utiliser des étendues de module.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/03/2021
ms.openlocfilehash: 85f345cfd7085f34f28e4b219c4f379abff74bff
ms.sourcegitcommit: ef950cf37f65ea7a0f583e246cfbf13f1913eb12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111421415"
---
# <a name="use-bicep-modules"></a>Utiliser des modules Bicep

Bicep vous permet d’arrêter une solution complexe en modules. Un module Bicep est un ensemble d’une ou plusieurs ressources à déployer ensemble. Les modules extraient des détails complexes de la déclaration des ressources brutes, ce qui peut améliorer la lisibilité. Vous pouvez réutiliser ces modules et les partager avec d’autres personnes. Les modules Bicep sont transposés dans un seul modèle ARM avec des [modèles imbriqués](../templates/linked-templates.md#nested-template) pour le déploiement.

Pour voir un tutoriel, consultez [Déployer des ressources Azure à l’aide de modèles Bicep](/learn/modules/deploy-azure-resources-by-using-bicep-templates/).

## <a name="define-modules"></a>Définir des modules

Chaque fichier Bicep peut être utilisé comme un module. Un module expose uniquement les paramètres et les sorties comme contrat pour d’autres fichiers Bicep. Les paramètres et les sorties sont facultatifs.

Le fichier Bicep suivant peut être déployé directement pour créer un compte de stockage ou être utilisé en tant que module.  La section suivante vous montre comment utiliser les modules :

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_RAGRS'
  'Standard_ZRS'
  'Premium_LRS'
  'Premium_ZRS'
  'Standard_GZRS'
  'Standard_RAGZRS'
])
param storageSKU string = 'Standard_LRS'
param location string

var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

La sortie est utilisée pour passer des valeurs aux fichiers Bicep parents.

## <a name="consume-modules"></a>Utiliser des modules

Utilisez le mot clé _module_ pour utiliser un module. Le fichier Bicep suivant déploie la ressource définie dans le fichier de module référencé :

```bicep
@minLength(3)
@maxLength(11)
param namePrefix string
param location string = resourceGroup().location

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

- **module** : mot clé.
- **nom symbolique** (stgModule) : identificateur du module.
- **Fichier de module** : les fichiers de module doivent être référencés à l’aide de chemins d’accès relatifs. Tous les chemins d’accès dans du code Bicep doivent être spécifiés en utilisant le séparateur de répertoires de la barre oblique (/) pour garantir une compilation cohérente entre les plateformes. Le caractère barre oblique inverse (\\) de Windows n’est pas pris en charge. Les chemins d’accès peuvent contenir des espaces.
- La propriété **_nom_** (storageDeploy) est requise lors de l’utilisation d’un module. Quand Bicep génère le modèle IL, ce champ est utilisé comme nom de la ressource de déploiement imbriquée, qui est générée pour le module :

    ```json
    ...
    ...
    "resources": [
      {
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2020-10-01",
        "name": "storageDeploy",
        "properties": {
          ...
        }
      }
    ]
    ...
    ```

Pour obtenir une valeur de sortie à partir d’un module, récupérez la valeur de la propriété avec la syntaxe suivante : `stgModule.outputs.storageEndpoint` où `stgModule` est l’identificateur du module.

## <a name="configure-module-scopes"></a>Configurer les étendues du module

Lors de la déclaration d’un module, vous pouvez fournir une propriété _étendue_ pour définir l’étendue selon laquelle le module doit être déployé :

```bicep
module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: resourceGroup('someOtherRg') // pass in a scope to a different resourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}
```

La propriété _étendue_ peut être omise lorsque l’étendue cible du module et l’étendue cible du parent sont les mêmes. Lorsque la propriété d’étendue n’est pas fournie, le module est déployé au niveau de l’étendue cible du parent.

Le fichier Bicep suivant montre comment créer un groupe de ressources et déployer un module dans le groupe de ressources :

```bicep
// set the target scope for this file
targetScope = 'subscription'

@minLength(3)
@maxLength(11)
param namePrefix string

param location string = deployment().location

var resourceGroupName = '${namePrefix}rg'
resource myResourceGroup 'Microsoft.Resources/resourceGroups@2020-01-01' = {
  name: resourceGroupName
  location: location
  scope: subscription()
}

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: myResourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

La propriété d’étendue doit être définie sur un objet d’étendue valide. Si votre fichier Bicep déploie un groupe de ressources, un abonnement ou un groupe d’administration, vous pouvez définir l’étendue d’un module sur le nom symbolique de cette ressource. Cette approche est illustrée dans l’exemple précédent où un groupe de ressources est créé et utilisé pour l’étendue d’un module.

Vous pouvez également utiliser les fonctions d’étendue pour obtenir une étendue valide. Ces fonctions sont les suivantes :

- [resourceGroup](bicep-functions-scope.md#resourcegroup)
- [subscription](bicep-functions-scope.md#subscription)
- [managementGroup](bicep-functions-scope.md#managementgroup)
- [client](bicep-functions-scope.md#tenant)

## <a name="next-steps"></a>Étapes suivantes

- Pour voir un tutoriel, consultez [Déployer des ressources Azure à l’aide de modèles Bicep](/learn/modules/deploy-azure-resources-by-using-bicep-templates/).
