---
title: 'Règle de linter : aucune URL d’environnement codée en dur'
description: 'Règle de linter : aucune URL d’environnement codée en dur'
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: d35736860dd672ffc00ea1d4cde52d8f4d4ef8c9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602477"
---
# <a name="linter-rule---no-hardcoded-environment-url"></a>Règle de linter : aucune URL d’environnement codée en dur

Le linter facilite l’application des normes de codage en donnant des conseils pendant le développement. L’ensemble actuel des règles de linter est minimal et provient des [cas de test arm-ttk](../templates/template-test-cases.md) :

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Pour plus d’informations, consultez [Utiliser un linter Bicep](./linter.md).

## <a name="code"></a>Code

`no-hardcoded-env-urls`

## <a name="description"></a>Description

Ne codez pas en dur les URL d’environnement dans votre modèle. Au lieu de cela, utilisez la [fonction d’environnement](../templates/template-functions-deployment.md#environment) pour obtenir dynamiquement ces URL pendant le déploiement. Pour obtenir la liste des hôtes d’URL bloqués, consultez la liste par défaut des `DisallowedHosts` dans [bicepconfig.json](https://github.com/Azure/bicep/blob/main/src/Bicep.Core/Configuration/bicepconfig.json).

## <a name="examples"></a>Exemples

L’exemple suivant échoue à ce test, car l’URL est codée en dur.

```bicep
var AzureURL = 'https://management.azure.com'
```

De même, le test échoue quand il est utilisé avec concat ou uri.

```bicep
var AzureSchemaURL1 = concat('https://','gallery.azure.com')
var AzureSchemaURL2 = uri('gallery.azure.com','test')
```

L’exemple suivant réussit ce test.

```bicep
var AzureSchemaURL = environment().gallery
```

## <a name="configuration"></a>Configuration

L’ensemble des hôtes d’URL à interdire peut être personnalisé à l’aide de la propriété disallowedHosts dans le fichier bicepconfig.json, comme suit :

```json
{
  "analyzers": {
    "core": {
      "enabled": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning",
          "disallowedHosts": [
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
          ]
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de Visual Studio Code et l’extension Bicep, consultez [Démarrage rapide : Créer des fichiers Bicep avec Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
