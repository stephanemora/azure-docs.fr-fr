---
title: Cas de test pour tous les fichiers pour le kit de ressources de test Azure Resource Manager
description: Décrit les tests exécutés pour tous les fichiers par le kit de ressources de test de modèle Azure Resource Manager.
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 6af9a7c149aacbd50537086ae70a8d845604d2ef
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393509"
---
# <a name="test-cases-for-all-files"></a>Cas de test pour tous les fichiers

Cet article décrit les tests exécutés avec le [kit de ressources de test de modèle](test-toolkit.md) pour tous les fichiers JSON (JavaScript Object Notation). Les exemples incluent les noms des tests et des exemples de code avec lesquels les tests **réussissent** ou **échouent**. Pour plus d’informations sur l’exécution de tests ou sur l’exécution d’un test spécifique, consultez [Paramètres de test](test-toolkit.md#test-parameters).

## <a name="use-valid-json-syntax"></a>Utiliser une syntaxe JSON valide

Nom du test : JSONFiles Should Be Valid (Les fichiers JSON doivent être valides)

Ce test vérifie que tous les fichiers JSON contiennent une syntaxe valide, par exemple les fichiers _azuredeploy.json_, _azuredeploy.parameters.json_ ou _createUiDefinition.json_. Si le test **échoue**, vous constaterez des échecs ou verrez des avertissements pour d’autres tests ou l’analyse JSON.

### <a name="template-file-example"></a>Exemple de fichier de modèle

Dans l’exemple suivant, le test échoue, car `parameters`, `comboBox`et `location` dans le fichier _azuredeploy.json_ ne contiennent pas l’accolade ouvrante (`{`).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters":
    "comboBox":
      "type": "string"
    },
    "location":
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "comboBox": {
      "type": "string",
      "value": "[parameters('comboBox')]"
    },
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

Dans l’exemple suivant, le test **réussit**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "string"
    },
    "location": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "comboBox": {
      "type": "string",
      "value": "[parameters('comboBox')]"
    },
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

### <a name="parameter-file-example"></a>Exemple avec un fichier de paramètres

Dans l’exemple suivant, le test **échoue**, car _azuredeploy.parameters.json_ utilise un paramètre sans `value`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "value":
    }
  }
}
```

Dans l’exemple suivant, le test **réussit**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "value": "westus"
    }
  }
}
```

### <a name="createuidefintion-example"></a>Exemple avec CreateUiDefintion

Dans l’exemple suivant, le test **échoue**, car la section `outputs` dans le fichier _createUiDefinition.json_ ne contient pas l’accolade ouvrante (`{`).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs":
      "comboBox": "[basics('comboBox')]",
      "location": "[location()]"
    }
  }
}
```

Dans l’exemple suivant, le test **réussit**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]",
      "location": "[location()]"
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le kit de ressources de test, consultez [Utiliser le kit de ressources de test de modèle ARM](test-toolkit.md).
- Pour les tests de modèle ARM, consultez [Cas de test pour les modèles ARM](template-test-cases.md).
- Pour tester des fichiers de paramètres, consultez [Cas de test pour les fichiers de paramètres](parameters.md).
- Pour les tests createUiDefinition, consultez [Cas de test pour createUiDefinition.json](createUiDefinition-test-cases.md).
