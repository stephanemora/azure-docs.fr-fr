---
title: Fichier de configuration Bicep
description: Décrit comment personnaliser les valeurs de configuration de vos déploiements Bicep
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 7a057d353fd5b25ae122e7856f1ccb560d7fce56
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130071831"
---
# <a name="add-custom-settings-in-the-bicep-config-file"></a>Ajouter des paramètres personnalisés dans le fichier de configuration Bicep

Pour personnaliser les valeurs de configuration de vos déploiements Bicep, ajoutez un fichier nommé **bicepconfig.json** au répertoire dans lequel vous stockez les fichiers Bicep. Dans ce fichier, spécifiez les valeurs de configuration à utiliser.

Vous pouvez ajouter plusieurs fichiers bicepconfig.json. Le fichier de configuration le plus proche dans l’arborescence du répertoire est utilisé.

Cet article décrit les propriétés qui sont disponibles dans le fichier de configuration. Toutefois, vous pouvez également découvrir ces propriétés par le biais d’IntelliSense accessible par l’extension Bicep pour Visual Studio Code.

:::image type="content" source="./media/bicep-config/bicep-linter-configure-intellisense.png" alt-text="Prise en charge d’IntelliSense dans le fichier de configuration bicepconfig.json.":::

## <a name="aliases-for-modules"></a>Alias pour les modules

Afin de simplifier le chemin pour la liaison aux modules, vous pouvez créer des alias dans le fichier de configuration. Un alias peut faire référence à un registre de module ou à un groupe de ressources qui contient des spécifications de modèle. Le fichier de configuration a une propriété pour `moduleAliases`. Pour créer un alias pour un registre Bicep, ajoutez une propriété `br` sous la propriété `moduleAliases`. Pour ajouter un alias pour une spécification de modèle, utilisez la propriété `ts`.

```json
{
  "moduleAliases": {
    "br": {
      <add-registry-aliases>
    },
    "ts": {
      <add-template-specs-aliases>
    }
  }
}
```

Dans la propriété `br`, ajoutez autant d’alias que nécessaire. Attribuez un nom et les propriétés suivantes à chaque alias :

- **registry** (obligatoire) : nom de serveur de connexion au registre
- **modulePath** (facultatif) : référentiel du registre dans lequel les modules sont stockés

Dans la propriété `ts`, ajoutez autant d’alias que nécessaire. Attribuez un nom et les propriétés suivantes à chaque alias :

- **subscription** (obligatoire) : ID de l’abonnement qui héberge les spécifications de modèle
- **resourceGroup** (obligatoire) : nom du groupe de ressources qui contient les spécifications de modèle

L’exemple suivant montre un fichier de configuration qui définit deux alias pour un registre de modules et un alias pour un groupe de ressources qui contient des spécifications de modèle.

```json
{
  "moduleAliases": {
    "br": {
      "ContosoRegistry": {
        "registry": "contosoregistry.azurecr.io"
      },
      "CoreModules": {
        "registry": "contosoregistry.azurecr.io",
        "modulePath": "bicep/modules/core"
      }
    },
    "ts": {
      "CoreSpecs": {
        "subscription": "00000000-0000-0000-0000-000000000000",
        "resourceGroup": "CoreSpecsRG"
      }
    }
  }
}
```

Lorsque vous utilisez un alias dans la référence de module, vous devez utiliser les formats suivants :

```bicep
br/<alias>:<file>:<tag>
ts/<alias>:<file>:<tag>
```

Définissez vos alias sur le dossier ou le groupe de ressources qui contient les modules, pas sur le fichier lui-même. Le nom de fichier doit être inclus dans la référence au module.

**Sans les alias**, vous créez un lien vers un module dans un registre avec le chemin d’accès complet.

```bicep
module stgModule 'br:contosoregistry.azurecr.io/bicep/modules/core/storage:v1' = {
```

**Avec les alias**, vous pouvez simplifier le lien à l’aide de l’alias du registre.

```bicep
module stgModule 'br/ContosoRegistry:bicep/modules/core/storage:v1' = {
```

Ou vous pouvez simplifier le lien à l’aide de l’alias qui spécifie le chemin d’accès au registre et au module.

```bicep
module stgModule  'br/CoreModules:storage:v1' = {
```

Pour une spécification de modèle, utilisez :

```bicep
module stgModule  'ts/CoreSpecs:storage:v1' = {
```

## <a name="credentials-for-restoring-modules"></a>Informations d’identification pour la restauration des modules

Pour [restaurer](bicep-cli.md#restore) des modules externes dans le cache local, le compte doit disposer des autorisations appropriées pour accéder au registre. Vous pouvez configurer la priorité des informations d’identification pour l’authentification auprès du registre. Par défaut, Bicep utilise les informations d’identification de l’utilisateur authentifié dans Azure CLI ou Azure PowerShell. Pour personnaliser la priorité des informations d’identification, ajoutez les éléments `cloud` et `credentialPrecedence` dans le fichier de configuration.

```json
{
    "cloud": {
      "credentialPrecedence": [
        "AzureCLI",
        "AzurePowerShell"
      ]
    }
}
```

Les informations d’identification disponibles sont les suivantes :

* AzureCLI
* AzurePowerShell
* Environnement
* ManagedIdentity
* VisualStudio
* VisualStudioCode

## <a name="customize-linter"></a>Personnaliser le linter

Dans le fichier de configuration, vous personnalisez les paramètres du [linter Bicep](linter.md). Vous pouvez activer ou désactiver le linter, fournir des valeurs spécifiques à des règles et définir le niveau de règles.

L’exemple suivant montre les règles qui sont disponibles pour la configuration.

```json
{
  "analyzers": {
    "core": {
      "enabled": true,
      "verbose": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning"
        },
        "no-unused-params": {
          "level": "error"
        },
        "no-unused-vars": {
          "level": "error"
        },
        "prefer-interpolation": {
          "level": "warning"
        },
        "secure-parameter-default": {
          "level": "error"
        },
        "simplify-interpolation": {
          "level": "warning"
        }
      }
    }
  }
}
```

Les propriétés sont les suivantes :

- **enabled** : indiquez **true** pour activer le linter, **false** pour le désactiver.
- **verbose** : indiquez **true** pour afficher le fichier bicepconfig.json utilisé par Visual Studio Code.
- **rules** : indiquez des valeurs spécifiques aux règles. Chaque règle a un niveau qui détermine la manière dont le linter répond lorsqu’une violation est trouvée.

Les valeurs disponibles pour **level** sont :

| **level**  | **Comportement lors de la création** | **Comportement de l’éditeur** |
|--|--|--|
| `Error` | Les violations apparaissent sous forme d’erreurs dans la sortie de build de la ligne de commande et entraînent l’échec de la build. | Le code incriminé est souligné d’un tilde rouge et apparaît sous l’onglet Problèmes. |
| `Warning` | Les violations apparaissent sous forme d’avertissements dans la sortie de build de la ligne de commande, mais n’entraînent pas l’échec de la build. | Le code incriminé est souligné d’un tilde jaune et apparaît sous l’onglet Problèmes. |
| `Info` | Les violations n’apparaissent pas dans la sortie de la génération de la ligne de commande. | Le code incriminé est souligné d’un tilde bleu et apparaît sous l’onglet Problèmes. |
| `Off` | Entièrement supprimé. | Entièrement supprimé. |

Pour la règle relative aux URL d’environnement codées en dur, vous pouvez choisir quelles URL sont vérifiées. Par défaut, les paramètres suivants sont appliqués :

```json
{
  "analyzers": {
    "core": {
      "verbose": false,
      "enabled": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning",
          "disallowedhosts": [
            "management.core.windows.net",
            "gallery.azure.com",
            "management.core.windows.net",
            "management.azure.com",
            "database.windows.net",
            "core.windows.net",
            "login.microsoftonline.com",
            "graph.windows.net",
            "trafficmanager.net",
            "vault.azure.net",
            "datalake.azure.net",
            "azuredatalakestore.net",
            "azuredatalakeanalytics.net",
            "vault.azure.net",
            "api.loganalytics.io",
            "api.loganalytics.iov1",
            "asazure.windows.net",
            "region.asazure.windows.net",
            "api.loganalytics.iov1",
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
}
```


