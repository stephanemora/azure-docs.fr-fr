---
title: Modules Bicep
description: Décrit comment définir et consommer un module et comment utiliser des étendues de module.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 10/05/2021
ms.openlocfilehash: bd5069db6a2ad9cb14f5f0b3bc28612afa519727
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619592"
---
# <a name="use-bicep-modules"></a>Utiliser des modules Bicep

Bicep vous permet d’arrêter une solution complexe en modules. Un module Bicep est simplement un fichier Bicep qui est déployé à partir d’un autre fichier Bicep. Vous pouvez encapsuler des détails complexes de la déclaration de ressource dans un module, ce qui améliore la lisibilité des fichiers qui utilisent le module. Vous pouvez réutiliser ces modules et les partager avec d’autres personnes. Les modules Bicep sont transposés dans un seul modèle du Gestionnaire de ressource Azure avec des [modèles imbriqués](../templates/linked-templates.md#nested-template) pour le déploiement.

Cet article explique comment définir et utiliser les modules.

Pour voir un tutoriel, consultez [Déployer des ressources Azure à l’aide de modèles Bicep](/learn/modules/deploy-azure-resources-by-using-bicep-templates/).

## <a name="define-modules"></a>Définir des modules

Chaque fichier Bicep peut être utilisé comme un module. Un module expose uniquement les paramètres et les sorties comme un contrat avec d’autres fichiers Bicep. Les paramètres et les sorties sont facultatifs.

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

module stgModule 'storageAccount.bicep' = {
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
- La propriété **_params_** contient tous les paramètres à transmettre au fichier module. Ces paramètres correspondent aux paramètres définis dans le fichier bicep.

À l’instar des ressources, les modules sont déployés en parallèle, sauf s’ils dépendent d’autres modules ou déploiements de ressources. Pour en savoir plus sur les dépendances, consultez [Définir des dépendances de ressources](resource-declaration.md#set-resource-dependencies).

Pour obtenir une valeur de sortie à partir d’un module, récupérez la valeur de la propriété avec la syntaxe suivante : `stgModule.outputs.storageEndpoint` où `stgModule` est l’identificateur du module.

Vous pouvez déployer un module de manière conditionnelle. Utilisez la même syntaxe **if** que celle que vous utiliseriez lors du [déploiement conditionnel d'une ressource](conditional-resource-deployment.md).

```bicep
param deployZone bool

module dnsZone 'dnszones.bicep' = if (deployZone) {
  name: 'myZoneModule'
}
```

Vous pouvez déployer un module plusieurs fois à l’aide de boucles. Pour plus d’informations, voir [Itération de sorties dans Bicep](loop-modules.md).

## <a name="configure-module-scopes"></a>Configurer les étendues du module

Lors de la déclaration d’un module, vous pouvez définir une étendue pour le module qui est différente de l’étendue pour le fichier Bicep conteneur. Utilisez la propriété `scope` pour définir l’étendue du module. Lorsque la propriété d’étendue n’est pas fournie, le module est déployé au niveau de l’étendue cible du parent.

Le fichier Bicep suivant montre comment créer un groupe de ressources et déployer un module dans le groupe de ressources :

```bicep
// set the target scope for this file
targetScope = 'subscription'

@minLength(3)
@maxLength(11)
param namePrefix string

param location string = deployment().location

var resourceGroupName = '${namePrefix}rg'
resource myResourceGroup 'Microsoft.Resources/resourceGroups@2021-04-01' = {
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

L’exemple suivant déploie sur des groupes de ressources existants.

```bicep
targetScope = 'subscription'

resource firstRG 'Microsoft.Resources/resourceGroups@2021-04-01' existing = {
  name: 'demogroup1'
}

resource secondRG 'Microsoft.Resources/resourceGroups@2021-04-01' existing = {
  name: 'demogroup2'
}

module storage1 'storageAccount.bicep' = {
  name: 'westusdeploy'
  scope: firstRG
  params: {
    storagePrefix: 'stg1'
    location: 'westus'
  }
}

module storage2 'storageAccount.bicep' = {
  name: 'eastusdeploy'
  scope: secondRG
  params: {
    storagePrefix: 'stg2'
    location: 'eastus'
  }
}
```

La propriété d’étendue doit être définie sur un objet d’étendue valide. Si votre fichier Bicep déploie un groupe de ressources, un abonnement ou un groupe d’administration, vous pouvez définir l’étendue d’un module sur le nom symbolique de cette ressource. Vous pouvez également utiliser les fonctions d’étendue pour obtenir une étendue valide.

Ces fonctions sont les suivantes :

- [resourceGroup](bicep-functions-scope.md#resourcegroup)
- [subscription](bicep-functions-scope.md#subscription)
- [managementGroup](bicep-functions-scope.md#managementgroup)
- [client](bicep-functions-scope.md#tenant)

L’exemple suivant utilise la fonction `managementGroup` pour définir l’étendue.

```bicep
param managementGroupName string

module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupName)
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour passer une valeur sensible à un module, utilisez la fonction [getSecret](bicep-functions-resource.md#getsecret) .
- Vous pouvez déployer un module plusieurs fois à l’aide de boucles. Pour plus d’informations, voir [Itération de sorties dans Bicep](loop-modules.md).
