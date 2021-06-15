---
title: Modules Bicep
description: Décrit comment définir et consommer un module et comment utiliser des étendues de module.
ms.topic: conceptual
ms.date: 03/30/2021
ms.openlocfilehash: 6c325bbbe265e13241119761373985ca4552b158
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353399"
---
# <a name="use-bicep-modules-preview"></a>Utiliser des modules Bicep (préversion)

Bicep vous permet d’arrêter une solution complexe en modules. Un module Bicep est un ensemble d’une ou plusieurs ressources à déployer ensemble. Les modules extraient des détails complexes de la déclaration des ressources brutes, ce qui peut améliorer la lisibilité. Vous pouvez réutiliser ces modules et les partager avec d’autres personnes. Combiné aux [spécifications de modèle](./template-specs.md), il crée un mode de modularité et de réutilisation du code. Les modules Bicep sont transposés dans un seul modèle ARM avec des [modèles imbriqués](./linked-templates.md#nested-template) pour le déploiement. Dans Bicep, [_dependsOn_](./template-syntax.md#resources) est géré automatiquement.

Pour obtenir un tutoriel, consultez [Tutoriel : ajouter des modules Bicep](./bicep-tutorial-add-modules.md).

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
- **fichier de module** : dans cet exemple, le chemin d’accès au module est spécifié à l’aide d’un chemin d’accès relatif (./storageAccount.bicep). Tous les chemins d’accès dans du code Bicep doivent être spécifiés en utilisant le séparateur de répertoires de la barre oblique (/) pour garantir une compilation cohérente entre les plateformes. Le caractère barre oblique inverse (\\) de Windows n’est pas pris en charge.
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

La propriété _étendue_ peut être omise lorsque l’étendue cible du module et l’étendue cible du parent sont les mêmes. Lorsque la propriété étendue n’est pas fournie, le module est déployé au niveau de l’étendue cible du parent.

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

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir un tutoriel, consultez [Tutoriel : ajouter des modules Bicep](./bicep-tutorial-add-modules.md).
