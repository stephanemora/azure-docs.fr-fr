---
title: Utiliser le linter Bicep
description: Découvrez comment utiliser le linter Bicep.
ms.topic: conceptual
ms.date: 07/01/2021
ms.openlocfilehash: 6b270a87a67235a6663a697b1329c5f86d570fdf
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779726"
---
# <a name="use-bicep-linter"></a>Utiliser le linter Bicep

Le linter Bicep peut être utilisé pour analyser les fichiers Bicep. Il vérifie les erreurs de syntaxe et intercepte un ensemble personnalisable de meilleures pratiques de création avant la compilation ou le déploiement de vos fichiers Bicep. Le linter facilite l’application des normes de codage en donnant des conseils pendant le développement.

## <a name="install-linter"></a>Installer le linter

Le linter peut être utilisé avec Visual Studio Code et l’interface CLI Bicep. Les conditions suivantes doivent être remplies :

- CLI Bicep 0.4 ou version ultérieure.
- Extension Bicep pour Visual Studio Code 0.4 ou version ultérieure.

## <a name="customize-linter"></a>Personnaliser le linter

Avec bicepconfig.json, vous pouvez activer ou désactiver le linter, fournir des valeurs spécifiques à des règles et définir le niveau de règles également. Le bicepconfig.json par défaut est le suivant :

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

Vous pouvez placer un bicepconfig.json personnalisé avec vos modèles dans le même répertoire. Le fichier de configuration le plus proche trouvé dans l’arborescence des dossiers est utilisé.

Le code JSON suivant est un exemple de bicepconfig.json :

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

- **enabled** : entrez **true** pour activer le linter, entrez **false** pour le désactiver.
- **verbose** : entrez **true** pour afficher le fichier bicepconfig.json utilisé par Visual Studio Code.
- **rules** : entrez des valeurs spécifiques aux règles. Chaque règle a au moins une propriété et un niveau. Cette propriété commande le comportement de Bicep si le cas se trouve dans le fichier Bicep.

Vous pouvez utiliser plusieurs valeurs pour le niveau de la règle :

| **level**  | **Comportement lors de la création** | **Comportement de l’éditeur** |
|--|--|--|
| `Error` | Les violations apparaissent sous forme d’erreurs dans la sortie de la génération de la ligne de commande et entraînent l’échec des builds. | Le code incriminé est souligné d’un tilde rouge et apparaît sous l’onglet Problèmes. |
| `Warning` | Les violations apparaissent sous forme d’avertissements dans la sortie de la génération de la ligne de commande, mais ne provoquent pas l’échec des builds. | Le code incriminé est souligné d’un tilde jaune et apparaît sous l’onglet Problèmes. |
| `Info` | Les violations n’apparaissent pas dans la sortie de la génération de la ligne de commande. | Le code incriminé est souligné d’un tilde bleu et apparaît sous l’onglet Problèmes. |
| `Off` | Entièrement supprimé. | Entièrement supprimé. |

L’ensemble actuel de règles de linter est minimal et extrait de [arm-ttk test cases](../templates/template-test-cases.md). L’extension Visual Studio Code et l’interface CLI Bicep vérifient toutes les règles disponibles par défaut et toutes les règles sont définies sur le niveau d’avertissement. En fonction du niveau d’une règle, vous pouvez voir des erreurs, des avertissements ou des messages d’information dans l’éditeur.

- [no-hardcoded-env-urls](https://github.com/Azure/bicep/blob/main/docs/linter-rules/no-hardcoded-env-urls.md)
- [no-unused-params](https://github.com/Azure/bicep/blob/main/docs/linter-rules/no-unused-params.md)
- [no-unused-vars](https://github.com/Azure/bicep/blob/main/docs/linter-rules/no-unused-vars.md)
- [prefer-interpolation](https://github.com/Azure/bicep/blob/main/docs/linter-rules/prefer-interpolation.md)
- [secure-parameter-default](https://github.com/Azure/bicep/blob/main/docs/linter-rules/secure-parameter-default.md)
- [simplify-interpolation](https://github.com/Azure/bicep/blob/main/docs/linter-rules/simplify-interpolation.md)

L’extension Bicep de Visual Studio Code donne accès à IntelliSense pour la modification des fichiers de configuration Bicep :

:::image type="content" source="./media/linter/bicep-linter-configure-intellisense.png" alt-text="Prise en charge d’IntelliSense dans le fichier de configuration bicepconfig.json.":::

## <a name="use-in-visual-studio-code"></a>Utiliser dans Visual Studio Code

Installez l’extension Bicep 0.4 ou version ultérieure pour utiliser le linter.  La capture d’écran suivante illustre le linter en action :

:::image type="content" source="./media/linter/bicep-linter-show-errors.png" alt-text="Utilisation du linter Bicep dans Visual Studio Code.":::

Le volet **PROBLÈMES** contient quatre erreurs, un avertissement et un message d’information illustrés sur la capture d’écran.  Le message d’information affiche le fichier de configuration Bicep qui est utilisé. Ce type d’information n’est affichée que lorsque vous définissez **verbose** sur **true** dans le fichier de configuration.

Passez le curseur de la souris sur l’une des zones de problèmes. Le linter donne les détails de l’erreur ou de l’avertissement. Cliquez sur la zone pour afficher également une ampoule bleue :

:::image type="content" source="./media/linter/bicep-linter-show-quickfix.png" alt-text="Utilisation du linter Bicep dans Visual Studio Code - afficher le correctif rapide.":::

Sélectionnez soit l’ampoule, soit le lien **Correctif rapide** pour voir la solution :

:::image type="content" source="./media/linter/bicep-linter-show-quickfix-solution.png" alt-text="Utilisation du linter Bicep dans Visual Studio Code - afficher la solution de correctif rapide.":::

Sélectionnez la solution pour résoudre le problème automatiquement.

## <a name="use-in-bicep-cli"></a>Utiliser dans l’interface CLI Bicep

Installez l’interface CLI Bicep 0.4 ou version ultérieure pour utiliser le linter.  La capture d’écran suivante illustre le linter en action. Le fichier Bicep est le même que celui utilisé dans la section [Utiliser dans Visual Studio Code](#use-in-visual-studio-code).

:::image type="content" source="./media/linter/bicep-linter-command-line.png" alt-text="Utilisation du linter Bicep dans la ligne de commande.":::

Vous pouvez intégrer ces vérifications dans le cadre de vos pipelines CI/CD. Vous pouvez utiliser une action GitHub pour tenter une génération de Bicep. Les erreurs font échouer les pipelines.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de Visual Studio Code et l’extension Bicep, consultez [Démarrage rapide : Créer des fichiers Bicep avec Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
