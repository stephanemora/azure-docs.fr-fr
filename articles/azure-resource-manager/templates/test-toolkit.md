---
title: Kit de ressources de test de modèle ARM
description: Explique comment exécuter le kit de ressources de test de modèles Azure Resource Manager (ARM) sur un modèle. Le kit de ressources vous permet de voir si vous avez implémenté les pratiques recommandées.
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: c02d7183304ec3474427bf1687d67d7dd7058d44
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128576653"
---
# <a name="use-arm-template-test-toolkit"></a>Utiliser un kit de ressources de test de modèle ARM

Le [kit de ressources de test de modèles Azure Resource Manager (ARM](https://aka.ms/arm-ttk)) vérifie si un modèle applique les pratiques recommandées. Lorsque votre modèle n’est pas conforme aux pratiques recommandées, il retourne une liste d’avertissements avec les modifications suggérées. Avec le kit de ressources de test, vous pouvez apprendre à éviter les problèmes courants liés au développement de modèles. Cet article explique les procédures d’exécution du kit de ressources de test, et d’ajout ou de suppression des tests. Pour plus d’informations sur l’exécution de tests ou sur l’exécution d’un test spécifique, consultez [Paramètres de test](#test-parameters).

Le kit de ressources est un ensemble de scripts PowerShell qui peuvent être exécutés à partir d’une commande dans PowerShell ou CLI. Ces tests sont des recommandations, mais pas des exigences. Vous pouvez choisir les tests qui sont pertinents pour vos objectifs et personnaliser les tests à exécuter.

Le kit de ressources contient quatre ensembles de tests :

- [Cas de test pour les modèles ARM](template-test-cases.md)
- [Cas de test pour les fichiers de paramètres](parameter-file-test-cases.md)
- [Cas de test pour createUiDefinition.json](createUiDefinition-test-cases.md)
- [Cas de test pour tous les fichiers](all-files-test-cases.md)

### <a name="microsoft-learn"></a>Microsoft Learn

Pour en savoir plus sur le kit de ressources de test de modèle ARM et pour obtenir une aide pratique, consultez [Valider des ressources Azure à l’aide du kit de ressources de test de modèle ARM](/learn/modules/arm-template-test) sur **Microsoft Learn**.

## <a name="install-on-windows"></a>Installer sur Windows

1. Si vous n’avez pas encore PowerShell, [installez PowerShell sur Windows](/powershell/scripting/install/installing-powershell-core-on-windows).

1. [Téléchargez le dernier fichier. zip](https://aka.ms/arm-ttk-latest) pour la kit de ressources de test et extrayez-le.

1. Démarrez PowerShell.

1. Accédez au dossier dans lequel vous avez extrait le kit de ressources de test. Dans ce dossier, accédez au dossier _arm-ttk_.

1. Si votre [stratégie d’exécution](/powershell/module/microsoft.powershell.core/about/about_execution_policies) bloque les scripts provenant d’Internet, vous devez débloquer les fichiers de script. Vérifiez que vous êtes bien dans le dossier _arm-ttk_.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Importez le module.

   ```powershell
   Import-Module .\arm-ttk.psd1
   ```

1. Pour exécuter les tests, utilisez la commande suivante :

   ```powershell
   Test-AzTemplate -TemplatePath \path\to\template
   ```

## <a name="install-on-linux"></a>Installer sur Linux

1. Si vous n’avez pas encore PowerShell, [installez PowerShell sur Linux](/powershell/scripting/install/installing-powershell-core-on-linux).

1. [Téléchargez le dernier fichier. zip](https://aka.ms/arm-ttk-latest) pour la kit de ressources de test et extrayez-le.

1. Démarrez PowerShell.

   ```bash
   pwsh
   ```

1. Accédez au dossier dans lequel vous avez extrait le kit de ressources de test. Dans ce dossier, accédez au dossier _arm-ttk_.

1. Si votre [stratégie d’exécution](/powershell/module/microsoft.powershell.core/about/about_execution_policies) bloque les scripts provenant d’Internet, vous devez débloquer les fichiers de script. Vérifiez que vous êtes bien dans le dossier _arm-ttk_.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Importez le module.

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. Pour exécuter les tests, utilisez la commande suivante :

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="install-on-macos"></a>Installer sur macOS

1. Si vous n’avez pas encore PowerShell, [installez PowerShell sur macOS](/powershell/scripting/install/installing-powershell-core-on-macos).

1. Installez `coreutils` :

   ```bash
   brew install coreutils
   ```

1. [Téléchargez le dernier fichier. zip](https://aka.ms/arm-ttk-latest) pour la kit de ressources de test et extrayez-le.

1. Démarrez PowerShell.

   ```bash
   pwsh
   ```

1. Accédez au dossier dans lequel vous avez extrait le kit de ressources de test. Dans ce dossier, accédez au dossier _arm-ttk_.

1. Si votre [stratégie d’exécution](/powershell/module/microsoft.powershell.core/about/about_execution_policies) bloque les scripts provenant d’Internet, vous devez débloquer les fichiers de script. Vérifiez que vous êtes bien dans le dossier _arm-ttk_.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Importez le module.

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. Pour exécuter les tests, utilisez la commande suivante :

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="result-format"></a>Format de résultat

Les tests réussis s’affichent en **vert** et sont précédés de `[+]` .

Les tests qui échouent s’affichent en **rouge** et sont précédés de `[-]` .

Les tests avec un avertissement s’affichent en **jaune** et précédés de `[?]`.

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="afficher les résultats des tests.":::

Résultats de texte :

```powershell
deploymentTemplate
[+] adminUsername Should Not Be A Literal (6 ms)
[+] apiVersions Should Be Recent In Reference Functions (9 ms)
[-] apiVersions Should Be Recent (6 ms)
    Api versions must be the latest or under 2 years old (730 days) - API version 2019-06-01 of
    Microsoft.Storage/storageAccounts is 760 days old
    Valid Api Versions:
    2021-04-01
    2021-02-01
    2021-01-01
    2020-08-01-preview

[+] artifacts parameter (4 ms)
[+] CommandToExecute Must Use ProtectedSettings For Secrets (9 ms)
[+] DependsOn Best Practices (5 ms)
[+] Deployment Resources Must Not Be Debug (6 ms)
[+] DeploymentTemplate Must Not Contain Hardcoded Uri (4 ms)
[?] DeploymentTemplate Schema Is Correct (6 ms)
    Template is using schema version '2015-01-01' which has been deprecated and is no longer
    maintained.
```

## <a name="test-parameters"></a>Paramètres de test

Lorsque vous fournissez le paramètre `-TemplatePath`, le kit de ressources recherche dans ce dossier un modèle nommé _azuredeploy.json_ ou _maintemplate.json_. Il teste d’abord ce modèle, puis teste tous les autres modèles du dossier et de ses sous-dossiers. Les autres modèles sont testés en tant que modèles liés. Si votre chemin d’accès comprend un fichier nommé [createUiDefinition.json](../managed-applications/create-uidefinition-overview.md), il exécute les tests qui sont pertinents pour la définition de l’interface utilisateur. Les tests sont également exécutés pour les fichiers de paramètres et tous les fichiers JSON dans le dossier.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

Pour tester un fichier dans ce dossier, ajoutez le paramètre `-File`. Toutefois, le dossier doit toujours avoir un modèle principal nommé _azuredeploy.json_ ou _maintemplate.json_.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

Par défaut, tous les tests sont exécutés. Pour spécifier les tests individuels à exécuter, utilisez le paramètre `-Test` et fournissez le nom du test. Pour les noms de test, consultez [modèles ARM](template-test-cases.md), [fichiers de paramètres](parameter-file-test-cases.md), [createUiDefinition.json](createUiDefinition-test-cases.md) et [tous les fichiers](all-files-test-cases.md).

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>Personnaliser les tests

Vous pouvez personnaliser les tests par défaut ou créer vos propres tests. Si vous souhaitez supprimer définitivement un test, supprimez le fichier _.test.ps1_ du dossier.

Le kit de ressources contient quatre dossiers qui contiennent les tests par défaut exécutés pour des types de fichiers spécifiques :

- Modèles ARM : _\arm-ttk\testcases\deploymentTemplate_
- Fichiers de paramètres : _\arm-ttk\testcases\deploymentParameters_
- [createUiDefinition.json](../managed-applications/create-uidefinition-overview.md) : _\arm-ttk\testcases\CreateUIDefinition_
- Tous les fichiers : _\arm-ttk\testcases\AllFiles_

### <a name="add-a-custom-test"></a>Ajouter un test personnalisé

Pour ajouter votre propre test, créez un fichier respectant la convention d’affectation de noms : _Nom-de-votre-test-personnalisé.test.ps1_.

Le test peut obtenir le modèle sous la forme d’un paramètre d’objet ou d’un paramètre de chaîne. En règle générale, vous utilisez l’un ou l’autre, mais vous pouvez utiliser les deux.

Utilisez le paramètre d’objet lorsque vous devez obtenir une section du modèle et itérer au sein de ses propriétés. Un test qui utilise le paramètre d’objet a le format suivant :

```powershell
param(
  [Parameter(Mandatory=$true,Position=0)]
  [PSObject]
  $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

L’objet de modèle présente les propriétés suivantes :

- `$schema`
- `contentVersion`
- `parameters`
- `variables`
- `resources`
- `outputs`

Par exemple, vous pouvez obtenir la collection de paramètres avec `$TemplateObject.parameters`.

Utilisez le paramètre de chaîne lorsque vous devez effectuer une opération de chaîne sur l’ensemble du modèle. Un test qui utilise le paramètre de chaîne a le format suivant :

```powershell
param(
  [Parameter(Mandatory)]
  [string]
  $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

Par exemple, vous pouvez exécuter une expression régulière du paramètre de chaîne pour voir si une syntaxe spécifique est utilisée.

Pour en savoir plus sur l’implémentation du test, examinez les autres tests de ce dossier.

## <a name="integrate-with-azure-pipelines"></a>Intégrer à Azure Pipelines

Vous pouvez ajouter le kit de ressources de test à votre pipeline Azure. Avec un pipeline, vous pouvez exécuter le test chaque fois que le modèle est mis à jour ou l’exécuter dans le cadre de votre processus de déploiement.

Le moyen le plus simple d’ajouter le kit de ressources de test à votre pipeline consiste à utiliser des extensions tierces. Les deux extensions suivantes sont disponibles  :

- [Run ARM TTK Tests](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension) (Exécuter les tests ARM TTK)
- [ARM Template Tester](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk) (Testeur de modèle ARM)

Vous pouvez également implémenter vos propres tâches. L’exemple suivant illustre la procédure de téléchargement du kit de ressources de test.

```json
{
  "environment": {},
  "enabled": true,
  "continueOnError": false,
  "alwaysRun": false,
  "displayName": "Download TTK",
  "timeoutInMinutes": 0,
  "condition": "succeeded()",
  "task": {
    "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
    "versionSpec": "2.*",
    "definitionType": "task"
  },
  "inputs": {
    "targetType": "inline",
    "filePath": "",
    "arguments": "",
    "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
    "errorActionPreference": "stop",
    "failOnStderr": "false",
    "ignoreLASTEXITCODE": "false",
    "pwsh": "true",
    "workingDirectory": ""
  }
}
```

L’exemple suivant illustre la procédure d’exécution des tests.

```json
{
  "environment": {},
  "enabled": true,
  "continueOnError": true,
  "alwaysRun": false,
  "displayName": "Run Best Practices Tests",
  "timeoutInMinutes": 0,
  "condition": "succeeded()",
  "task": {
    "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
    "versionSpec": "2.*",
    "definitionType": "task"
  },
  "inputs": {
    "targetType": "inline",
    "filePath": "",
    "arguments": "",
    "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
    "errorActionPreference": "continue",
    "failOnStderr": "true",
    "ignoreLASTEXITCODE": "false",
    "pwsh": "true",
    "workingDirectory": ""
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les tests de modèle, consultez [Cas de test pour les modèles ARM](template-test-cases.md).
- Pour tester les fichiers de paramètres, consultez [Cas de test pour les fichiers de paramètres](parameters.md).
- Pour les tests createUiDefinition, consultez [Cas de test pour createUiDefinition.json](createUiDefinition-test-cases.md).
- Pour en savoir plus sur les tests pour tous les fichiers, consultez [Cas de test pour tous les fichiers](all-files-test-cases.md).
- Pour suivre un module Microsoft Learn couvrant l’utilisation du kit de ressources de test, consultez [Validation des ressources Azure à l’aide du kit de ressources de test de modèle ARM](/learn/modules/arm-template-test/).
