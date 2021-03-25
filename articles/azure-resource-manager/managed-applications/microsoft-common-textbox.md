---
title: Élément d'interface utilisateur TextBox
description: Décrit l’élément d’interface utilisateur Microsoft.Common.TextBox pour le Portail Azure. Permet d’ajouter du texte non mis en forme.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
ms.openlocfilehash: 8e4cfcc7fe46c19bf1e58ee5eadf1e0bb8c7bd8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124327"
---
# <a name="microsoftcommontextbox-ui-element"></a>Élément d’interface utilisateur Microsoft.Common.TextBox

Élément d’interface utilisateur (IU) utilisable pour ajouter du texte non mis en forme. L’élément `Microsoft.Common.TextBox` est un champ d’entrée d’une seule ligne, mais il prend en charge l’entrée de plusieurs lignes avec la propriété `multiLine`.

## <a name="ui-sample"></a>Exemple d’interface utilisateur

L’élément `TextBox` utilise une zone de texte d’une ou de plusieurs lignes.

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox.png" alt-text="Zone de texte d’une ligne unique de l’élément Microsoft.Common.TextBox.":::

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox-multi-line.png" alt-text="Zone de texte de plusieurs lignes de l’élément Microsoft.Common.TextBox.":::

## <a name="schema"></a>schéma

```json
{
  "name": "nameInstance",
  "type": "Microsoft.Common.TextBox",
  "label": "Name",
  "defaultValue": "contoso123",
  "toolTip": "Use only allowed characters",
  "placeholder": "",
  "multiLine": false,
  "constraints": {
    "required": true,
    "validations": [
      {
        "regex": "^[a-z0-9A-Z]{1,30}$",
        "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
      },
      {
        "isValid": "[startsWith(steps('resourceConfig').nameInstance, 'contoso')]",
        "message": "Must start with 'contoso'."
      }
    ]
  },
  "visible": true
}
```

## <a name="sample-output"></a>Exemple de sortie

```json
"contoso123"
```

## <a name="remarks"></a>Notes

- Utilisez la propriété `toolTip` pour afficher le texte relatif à l’élément quand le curseur de la souris est positionné sur le symbole d’informations.
- La propriété `placeholder` est un texte d’aide qui disparaît lorsque l’utilisateur commence à modifier. Si les `placeholder` et `defaultValue` sont définis, le `defaultValue` est prioritaire et est affiché.
- La propriété `multiLine` est booléenne, `true` ou `false`. Pour utiliser une zone de texte de plusieurs lignes, définissez la valeur de la propriété sur `true` . Si vous n’avez pas besoin d’une zone de texte de plusieurs lignes, définissez la valeur de la propriété sur `false` ou excluez la propriété. Pour les nouvelles lignes, la sortie JSON affiche `\n` pour le saut de ligne. La zone de texte de plusieurs ligne accepte `\r` en tant que retour chariot (CR) et `\n` en tant que saut de ligne (LF). Par exemple, une valeur par défaut peut inclure `\r\n` pour spécifier CRLF.
- Si `constraints.required` est défini sur `true`, la zone de texte doit contenir une valeur pour permettre la réussite de la validation. La valeur par défaut est `false`.
- La propriété `validations` est un tableau dans lequel vous ajoutez des conditions pour vérifier la valeur fournie dans la zone de texte.
- La propriété `regex` est un modèle d’expression régulière JavaScript. Si elle est spécifiée, la valeur de la zone de texte doit correspondre au modèle pour que la validation réussisse. La valeur par défaut est `null`. Pour plus d’informations sur la syntaxe d’expression régulière, consultez [Référence rapide des expressions régulières](/dotnet/standard/base-types/regular-expression-language-quick-reference).
- La propriété `isValid` contient une expression qui prend la valeur `true` ou `false`. Dans l’expression, vous définissez la condition qui détermine si la zone de texte est valide.
- La propriété `message` est une chaîne à afficher en cas d’échec de la validation de la valeur de la zone de texte.
- Il est possible de spécifier une valeur pour `regex` lorsque `required` est défini sur `false`. Dans ce scénario, aucune valeur n’est requise dans la zone de texte pour que la validation réussisse. Si elle est spécifiée, celle-ci doit correspondre au modèle d’expression régulière.

## <a name="examples"></a>Exemples

Les exemples montrent comment utiliser une zone de texte d’une ligne et une zone de texte de plusieurs lignes.

### <a name="single-line"></a>Une ligne

L’exemple suivant utilise une zone de texte avec le contrôle [Microsoft.Solutions.ArmApiControl](microsoft-solutions-armapicontrol.md) pour vérifier la disponibilité d’un nom de ressource.

```json
"basics": [
  {
    "name": "nameApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
      "method": "POST",
      "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
      "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
    }
  },
  {
    "name": "txtStorageName",
    "type": "Microsoft.Common.TextBox",
    "label": "Storage account name",
    "constraints": {
      "validations": [
        {
          "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
          "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
        }
      ]
    }
  }
]
```

### <a name="multi-line"></a>Plusieurs lignes

Cet exemple utilise la propriété `multiLine` pour créer une zone de texte de plusieurs lignes avec un texte d’espace réservé.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "demoTextBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Multi-line text box",
        "defaultValue": "",
        "toolTip": "Use 1-60 alphanumeric characters, hyphens, spaces, periods, and colons.",
        "placeholder": "This is a multi-line text box:\nLine 1.\nLine 2.\nLine 3.",
        "multiLine": true,
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z -.:\n]{1,60}$",
              "message": "Only 1-60 alphanumeric characters, hyphens, spaces, periods, and colons are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "textBox": "[basics('demoTextBox')]"
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour une introduction à la création de définitions d’interface utilisateur, consultez [CreateUiDefinition.json pour une expérience de création d’applications managées Azure](create-uidefinition-overview.md).
- Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](create-uidefinition-elements.md).
