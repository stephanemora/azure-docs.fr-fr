---
title: 'Règle de linter : aucune URL d’environnement codée en dur'
description: 'Règle de linter : aucune URL d’environnement codée en dur'
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 8bb3bbd4be61259d1a11184d879f737d970399cd
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165385"
---
# <a name="linter-rule---no-hardcoded-environment-url"></a>Règle de linter : aucune URL d’environnement codée en dur

Cette règle recherche les URL codées en dur qui varient en fonction de l’environnement cloud.

## <a name="returned-code"></a>Code renvoyé

`no-hardcoded-env-urls`

## <a name="solution"></a>Solution

Au lieu des URL codées en dur dans votre fichier Bicep, utilisez la [fonction environment](../templates/template-functions-deployment.md#environment) pour obtenir dynamiquement ces URL pendant le déploiement. La fonction environment retourne des URL différentes en fonction de l’environnement cloud dans lequel vous effectuez le déploiement.

L’exemple suivant échoue à ce test, car l’URL est codée en dur.

```bicep
var managementURL = 'https://management.azure.com'
```

De même, le test échoue quand il est utilisé avec concat ou uri.

```bicep
var galleryURL1 = concat('https://','gallery.azure.com')
var galleryURL2 = uri('gallery.azure.com','test')
```

Vous pouvez résoudre ce problème en remplaçant l’URL codée en dur par la fonction `environment()`.

```bicep
var galleryURL = environment().gallery
```

Dans certains cas, vous pouvez résoudre le problème en obtenant une propriété à partir d’une ressource que vous avez déployée. Par exemple, au lieu de construire le point de terminaison de votre compte de stockage, récupérez-le avec `.properties.primaryEndpoints`.

```bicep
param storageAccountName string

resource sa 'Microsoft.Storage/storageAccounts@2021-04-01' = {
  name: storageAccountName
  location: 'westus'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = sa.properties.primaryEndpoints.web
```

## <a name="configuration"></a>Configuration

Par défaut, cette règle utilise les paramètres suivants pour déterminer les URL qui ne sont pas autorisées. 

```json
"analyzers": {
  "core": {
    "verbose": false,
    "enabled": true,
    "rules": {
      "no-hardcoded-env-urls": {
        "level": "warning",
        "disallowedhosts": [
          "gallery.azure.com",
          "management.core.windows.net",
          "management.azure.com",
          "database.windows.net",
          "core.windows.net",
          "login.microsoftonline.com",
          "graph.windows.net",
          "trafficmanager.net",
          "datalake.azure.net",
          "azuredatalakestore.net",
          "azuredatalakeanalytics.net",
          "vault.azure.net",
          "api.loganalytics.io",
          "asazure.windows.net",
          "region.asazure.windows.net",
          "batch.core.windows.net"
        ],
        "excludedhosts": [
          "schema.management.azure.com"
        ]
      }
    }
  }
}
```

Vous pouvez la personnaliser en ajoutant un fichier bicepconfig.json et en appliquant de nouveaux paramètres.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le linter, consultez [Utiliser le linter Bicep](./linter.md).
