---
title: Cas de test createUiDefinition.json pour le kit de ressources de test Azure Resource Manager
description: Décrit les tests createUiDefinition.json qu’exécute le kit de ressources de test du modèle Azure Resource Manager.
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 573f7db7c1f6fd3d45531e8be15db3d8affc8eba
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114392031"
---
# <a name="test-cases-for-createuidefinitionjson"></a>Cas de test pour createUiDefinition.json

Cet article décrit les tests exécutés avec le [kit de ressources de test de modèle](test-toolkit.md) pour les fichiers [createUiDefinition.json](../managed-applications/create-uidefinition-overview.md). Les exemples incluent les noms des tests et des exemples de code avec lesquels les tests **réussissent** ou **échouent**.

La boîte à outils inclut les [cas de test](template-test-cases.md) pour les modèles Azure Resource Manager (modèles ARM) et les fichiers de modèles principaux nommés _azuredeploy.json_ ou _maintemplate.json_. Lorsque le répertoire contient un fichier _createUiDefinition.json_, des tests spécifiques sont exécutés pour les contrôles d’interface utilisateur. Pour plus d’informations sur l’exécution de tests ou sur l’exécution d’un test spécifique, consultez [Paramètres de test](test-toolkit.md#test-parameters).

Le fichier _createUiDefinition.json_ crée des contrôles d’interface utilisateur (IU) personnalisés à l’aide d’[éléments](../managed-applications/create-uidefinition-elements.md) et de [fonctions](../managed-applications/create-uidefinition-functions.md).

## <a name="verify-template-parameter-allows-values"></a>Vérifier que le paramètre de modèle autorise les valeurs

Nom du test : **Allowed Values Should Actually Be Allowed**

Ce test vérifie que les valeurs de chaque contrôle dans _createUiDefinition.json_ sont autorisées dans les paramètres du modèle principal. Les paramètres sont mappés par nom entre le modèle principal et le fichier _createUiDefinition.json_.

Le paramètre du modèle principal doit accepter les valeurs `allowedValues` du contrôle. Le test vérifie également que le contrôle est référencé dans la section `outputs` du fichier _createUiDefinition.json_.

Ce test vérifie le modèle principal et le fichier _createUiDefinition.json_. Un exemple du fichier _createUiDefinition.json_ est présenté après les exemples de modèles principaux.

Dans l’exemple suivant, le test **échoue** parce que le nom de paramètre du modèle principal `combo` ne correspond pas au nom de paramètre du contrôle `comboBox`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "combo": {
      "type": "string",
      "defaultValue": "two"
    }
  },
  "resources": [],
  "outputs": {
    "comboBoxOutput": {
      "type": "string",
      "value": "[parameters('combo')]"
    }
  }
}
```

Dans l’exemple suivant, le test **échoue** parce que le type de paramètre du modèle principal `int` n’accepte pas la valeur `string` du contrôle. Et si le paramètre d’un modèle principal définit une `defaultValue`, celle-ci doit être une `value` valide dans les `allowedValues` du contrôle.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "int",
      "defaultValue": 4
    }
  },
  "resources": [],
  "outputs": {
    "comboBoxOutput": {
      "type": "string",
      "value": "[parameters('combo')]"
    }
  }
}
```

Dans l’exemple suivant, le test **réussit** parce que le nom de paramètre du modèle principal correspond pas au nom de paramètre du contrôle. Et le type de paramètre du modèle est une `string` avec une `defaultValue` qui est spécifiés dans les `allowedValues` du contrôle.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "string",
      "defaultValue": "two"
    }
  },
  "resources": [],
  "outputs": {
    "comboBoxOutput": {
      "type": "string",
      "value": "[parameters('comboBox')]"
    }
  }
}
```

Fichier _createUiDefinition.json_ pour cet exemple :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "demoComboBox",
        "label": "demoComboBoxLabel",
        "elements": [
          {
            "name": "comboBox",
            "type": "Microsoft.Common.DropDown",
            "label": "Example drop down",
            "defaultValue": "Value two",
            "toolTip": "This is a tool tip",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Value one",
                  "description": "The value to select for option 1.",
                  "value": "one"
                },
                {
                  "label": "Value two",
                  "description": "The value to select for option 2.",
                  "value": "two"
                }
              ],
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "comboBox": "[steps('demoComboBox').comboBox]"
    }
  }
}
```

## <a name="output-controls-must-exist"></a>Des contrôles de sortie doivent exister

Nom du test : **Controls In Outputs Must Exist**

Les contrôles utilisés dans la section `outputs` doivent exister dans un élément situé ailleurs dans le fichier _createUiDefinition.json_. Le nom référencé dans `outputs` doit correspondre à un nom utilisé dans `basics[]` ou `steps[]`.

Dans l’exemple suivant, le test **échoue**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "nameDoesNotMatchOutput",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
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
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

## <a name="properties-must-include-values"></a>Les propriétés doivent inclure des valeurs

Nom du test : **CreateUIDefinition Must Not Have Blanks**

Les propriétés doivent inclure des valeurs. Les propriétés requises doivent utiliser des valeurs valides. Les propriétés facultatives qui sont vides doivent être supprimées. Le test autorise que les valeurs `"basics": []`, `"steps": []` ou `defaultValue` soient vides.

Dans l’exemple suivant, le test **échoue** parce que les valeurs `label`, `placeholder` et `toolTip` sont vides.

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
        "label": "",
        "placeholder": "",
        "defaultValue": "",
        "toolTip": ""
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

Dans l’exemple suivant, le test **réussit** parce que les valeurs `label` et `toolTip` sont définies et que la valeur `placeholder` a été supprimée.

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
        "defaultValue": "",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

## <a name="use-valid-schema-and-version"></a>Utiliser un schéma et une version valides

Nom du test : **CreateUIDefinition Should Have Schema**

Le fichier _createUiDefinition.json_ doit inclure une propriété `$schema` et utiliser des valeurs `$schema` et `version` valides. Les numéros de version dans `$schema` et `version` doivent correspondre.

Dans l’exemple suivant, le test **échoue**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.9.9-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.9.9-preview"
}
```

Dans l’exemple suivant, le test **réussit** parce qu’il utilise les dernière valeurs `$schema` et `version`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview"
}
```

## <a name="dont-hide-credential-confirmation"></a>Ne pas masquer la confirmation des informations d’identification

Nom du test : **Credential Confirmation Should Not Be Hidden**

Ce test vérifie que les informations d’identification sont confirmées pour [Microsoft.Common.PasswordBox](../managed-applications/microsoft-common-passwordbox.md) ou [Microsoft.Compute.CredentialsCombo](../managed-applications/microsoft-compute-credentialscombo.md). La propriété `hideConfirmation` doit être définie sur `false` afin que la confirmation soit visible.

Dans l’exemple suivant, le test **échoue** parce que la valeur `hideConfirmation` est `true`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "credentials",
        "type": "Microsoft.Compute.CredentialsCombo",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": {
          "password": "Type your credentials"
        },
        "constraints": {
          "required": true,
          "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
          "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
        },
        "options": {
          "hideConfirmation": true
        },
        "osPlatform": "Windows",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "credentials": "[basics('credentials')]"
    }
  }
}
```

Dans l’exemple suivant, le test **réussit** parce que la valeur `hideConfirmation` est `false`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "credentials",
        "type": "Microsoft.Compute.CredentialsCombo",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": {
          "password": "Type your credentials"
        },
        "constraints": {
          "required": true,
          "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
          "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
        },
        "options": {
          "hideConfirmation": false
        },
        "osPlatform": "Windows",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "credentials": "[basics('credentials')]"
    }
  }
}
```

## <a name="use-correct-handler"></a>Utiliser le gestionnaire approprié

Nom du test : **Handler Must Be Correct**

Utilisez `Microsoft.Azure.CreateUIDef` ou `Microsoft.Compute.MultiVm` dans le fichier _createUiDefinition.json_.

Dans l’exemple suivant, le test **échoue**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.",
  "version": "0.1.2-preview"
}
```

Dans l’exemple suivant, le test **réussit**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview"
}
```

## <a name="dont-hide-existing-resources"></a>Ne pas masquer les ressources existantes

Nom du test : **HideExisting Must Be Correctly Handled**

Si la valeur `hideExisting` est définie sur `false` ou omise, `outputs` doit contenir `resourceGroup` et `newOrExisting`. La valeur par défaut de `hideExisting` est `false`.

Des exemples de types de contrôles incluant `hideExisting` sont [Microsoft.Storage.StorageAccountSelector](../managed-applications/microsoft-storage-storageaccountselector.md), [Microsoft.Network.PublicIpAddressCombo](../managed-applications/microsoft-network-publicipaddresscombo.md) ou [Microsoft.Network.VirtualNetworkCombo](../managed-applications/microsoft-network-virtualnetworkcombo.md).

Dans l’exemple suivant, le test **échoue**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "storage",
        "type": "Microsoft.Storage.StorageAccountSelector",
        "label": "Storage account",
        "toolTip": "This is a demo storage account",
        "defaultValue": {
          "name": "storageaccount01",
          "type": "Premium_LRS"
        },
        "options": {
          "hideExisting": false
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
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
        "name": "storage",
        "type": "Microsoft.Storage.StorageAccountSelector",
        "label": "Storage account",
        "toolTip": "This is a demo storage account",
        "defaultValue": {
          "name": "storageaccount01",
          "type": "Premium_LRS"
        },
        "options": {
          "hideExisting": false
        },
        "visible": false
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "resourceGroup": "[basics('storage').resourceGroup]",
      "newOrExisting": "[basics('storage').newOrExisting]"
    }
  }
}
```

## <a name="use-location-in-outputs"></a>Utiliser une emplacement dans les sorties

Nom du test : **Location Should Be In Outputs**

La section `outputs` doit contenir un emplacement utilisant la fonction [location](../managed-applications/create-ui-definition-referencing-functions.md#location).

Dans l’exemple suivant, le test **échoue** parce que `outputs` n’inclut pas d’emplacement.

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
      "comboBox": "[basics('comboBox')]"
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

## <a name="include-control-outputs-in-template-parameters"></a>Inclure des sorties de contrôle dans les paramètres du modèle

Nom du test : **Outputs Must Be Present In Template Parameters**

Le test vérifie que le fichier _createUiDefinition.json_ inclut une section `outputs`. Le test vérifie également que ces `outputs` sont définies dans la section `parameters` du modèle principal. Les noms doivent correspondre parce que les paramètres sont mappés par nom entre le fichier _createUiDefinition.json_ et le modèle principal.

Ce test vérifie le modèle principal et le fichier _createUiDefinition.json_. Un exemple du fichier _createUiDefinition.json_ est présenté après les exemples de modèles principaux.

Dans l’exemple suivant, le test **échoue** parce que modèle principal n’inclut pas le paramètre `comboBox` de la section `outputs` du fichier _createUiDefinition.json_.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

Dans l’exemple suivant, le test **réussit** parce que le modèle principal inclut le paramètre `comboBox`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "string",
      "defaultValue": "two"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
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

Fichier _createUiDefinition.json_ pour cet exemple :

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

## <a name="parameters-without-default-must-exist-in-outputs"></a>Des paramètres sans valeur par défaut doivent exister dans les sorties

Nom du test : **Parameters Without Default Must Exist In CreateUIDefinition**

Dans le modèle principal, des paramètres sans valeur par défaut doivent exister dans la section `outputs` du fichier _createUiDefinition.json_.

Ce test vérifie le modèle principal et le fichier _createUiDefinition.json_. Un exemple de fichier _azuredeploy.jsson_ est présenté après les exemples du contrôle.

Dans l’exemple suivant, le test **échoue** parce que les `outputs` du fichier _createUiDefinition.json_ n’incluent pas le paramètre `comboBox` du modèle principal.

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
      "location": "[location()]"
    }
  }
}
```

Dans l’exemple suivant, le test **réussit** parce que le fichier _createUiDefinition.json_ inclut `comboBox` dans les `outputs`.

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

Le fichier _azuredeploy.json_ pour cet exemple. Le paramètre `comboBox` ne doit pas nécessairement avoir une valeur par défaut.

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

## <a name="use-secure-parameter-with-password-box"></a>Utiliser un paramètre sécurisé avec la zone mot de passe

Nom du test : **Password Textboxes Must Be Used For Password Parameters**

Ce test vérifie qu’un élément [Microsoft.Common.PasswordBox](../managed-applications/microsoft-common-passwordbox.md) est défini dans les `parameters` du modèle principal et les `outputs` de _createUiDefinition.json_. Le type de paramètre du modèle principal pour une zone de mot de passe doit être `secureString` ou `secureObject`.

Ce test vérifie le modèle principal et le fichier _createUiDefinition.json_. Un exemple du fichier _createUiDefinition.json_ est présenté après les exemples de modèles principaux.

Dans l’exemple suivant, le test **échoue** parce que le paramètre `passwordBox` du modèle principal est une `string`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "passwordBox": {
      "type": "string"
    },
    "location": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

Dans l’exemple suivant, le test **réussit** parce que le paramètre `passwordBox` du modèle principal est une `secureString`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "passwordBox": {
      "type": "secureString"
    },
    "location": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

Fichier _createUiDefinition.json_ pour cet exemple :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "passwordBox",
        "type": "Microsoft.Common.PasswordBox",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": "Type a password"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "passwordBox": "[basics('passwordBox')]"
    }
  }
}
```

## <a name="password-box-requires-minimum-length"></a>La zone de mot de passe requiert une longueur minimale

Nom du test : **PasswordBoxes Must Have Min Length**

Le test vérifie que l’élément [Microsoft.Common.PasswordBox](../managed-applications/microsoft-common-passwordbox.md) utilise des `constraints` avec une `regex` requérant au moins 12 caractères.

Dans l’exemple suivant, le test **échoue** parce qu’il n’y a pas de `constraints`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "passwordBox",
        "type": "Microsoft.Common.PasswordBox",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": "Type a password"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "passwordBox": "[basics('passwordBox')]"
    }
  }
}
```

Dans l’exemple suivant, le test **réussit** parce que la `regex` requiert au moins 12 caractères.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "passwordBox",
        "type": "Microsoft.Common.PasswordBox",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": "Type a password",
        "constraints": {
          "required": true,
          "regex": "^[a-zA-Z0-9]{12,}$",
          "validationMessage": "Password must be at least 12 characters long, contain only numbers and letters"
        }
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "passwordBox": "[basics('passwordBox')]"
    }
  }
}
```

## <a name="text-box-must-use-validation"></a>La zone de texte doit utiliser une validation

Nom du test : **Textboxes Are Well Formed**

Utilisez une validation avec des zones de texte pour vérifier la présence de `constraints` contenant une `regex` et un `message`.

Dans l’exemple suivant, le test **échoue**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
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
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z]{1,30}$",
              "message": "Only 1-30 characters alphanumeric characters are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

## <a name="tooltip-must-exist-with-a-value"></a>La propriété toolTip doit exister avec une valeur

Nom du test : **Tooltips Should Be Present**

Ce test vérifie que la propriété `toolTip` existe et contient une valeur.

Dans l’exemple suivant, le test **échoue**.

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
        "toolTip": ""
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "comboBox": "[basics('comboBox')]"
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
      "location": "[location()]",
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

## <a name="dont-set-a-default-user-name"></a>Ne pas définir de nom d’utilisateur par défaut

Nom du test : **Usernames Should Not Have A Default**

Le test vérifie s’il y a une `defaultValue` définie pour [Microsoft.Compute.UsernameTextBox](../managed-applications/microsoft-compute-usernametextbox.md).

Dans l’exemple suivant, le test **échoue** parce qu’une `defaultValue` est fournie.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "userNameBox",
        "type": "Microsoft.Compute.UserNameTextBox",
        "label": "User name",
        "defaultValue": "admin",
        "toolTip": "Enter your user name",
        "osPlatform": "Windows"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "userNameBox": "[basics('userNameBox')]"
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
        "name": "userNameBox",
        "type": "Microsoft.Compute.UserNameTextBox",
        "label": "User name",
        "toolTip": "Enter your user name",
        "osPlatform": "Windows"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "userNameBox": "[basics('userNameBox')]"
    }
  }
}
```

## <a name="use-message-with-validations"></a>Utiliser un message avec des validations

Nom du test : **Validations Must Have Message**

Ce test vérifie que toutes les `validations` dans le fichier _createUiDefinition.json_ incluent un `message`.

Dans l’exemple suivant, le test **échoue** parce que la validation `regex` n’a pas de `message`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z]{1,30}$"
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
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
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z]{1,30}$",
              "message": "Only 1-30 characters alphanumeric characters are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

## <a name="virtual-machine-sizes-must-match"></a>Les tailles de machine virtuelle doivent correspondre

Nom du test : **VM Sizes Must Match Template**

Ce test vérifie que [Microsoft.Compute.SizeSelector](../managed-applications/microsoft-compute-sizeselector.md) figure dans les `outputs` de _createUiDefinition.json_ et la section `parameters` du modèle principal. Les paramètres du modèle principal qui spécifient une `defaultValue` doivent correspondre à une valeur dans les `allowedSizes` du contrôle.

Ce test vérifie le modèle principal et le fichier _createUiDefinition.json_. Un exemple du fichier _createUiDefinition.json_ est présenté après les exemples de modèles principaux.

Dans l’exemple suivant, le test **échoue** parce que la `defaultValue` du modèle principal correspond à une valeur dans `allowedSizes`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D9"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    },
    "vmSize": {
      "type": "string",
      "value": "[parameters('vmSize')]"
    }
  }
}
```

Dans l’exemple suivant, le test **réussit** parce que la `defaultValue` du modèle principal correspond à une valeur dans `allowedSizes`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D3"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    },
    "vmSize": {
      "type": "string",
      "value": "[parameters('vmSize')]"
    }
  }
}
```

Fichier _createUiDefinition.json_ pour cet exemple :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "vmSize",
        "type": "Microsoft.Compute.SizeSelector",
        "label": "VM Size",
        "toolTip": "Select a virtual machine size",
        "recommendedSizes": [
          "Standard_D1"
        ],
        "constraints": {
          "allowedSizes": [
            "Standard_D1",
            "Standard_D2",
            "Standard_D3"
          ]
        },
        "osPlatform": "Windows",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "vmSize": "[basics('vmSize')]"
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour créer une interface utilisateur du portail Azure, consultez [CreateUiDefinition.json pour une expérience de création d’applications managées Azure](../managed-applications/create-uidefinition-overview.md).
- Pour utiliser le bac à sable Créer une définition d’interface utilisateur, consultez [Tester votre interface de portail pour Applications managées Azure](../managed-applications/test-createuidefinition.md).
- Pour plus d’informations sur les contrôles d’interface utilisateur, consultez [Éléments CreateUiDefinition](../managed-applications/create-uidefinition-elements.md) et [Fonctions CreateUiDefinition](../managed-applications/create-uidefinition-functions.md).
- Pour en savoir plus sur les tests de modèle ARM, consultez [Cas de test pour les modèles ARM](template-test-cases.md).
