---
title: Fonctions de modèle - Logique
description: Décrit les fonctions à utiliser dans un modèle Azure Resource Manager (modèle ARM) pour déterminer les valeurs logiques.
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: b94f7aa38c708278f2ccf54a5592016873fcd285
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744365"
---
# <a name="logical-functions-for-arm-templates"></a>Fonctions logiques pour les modèles ARM

Resource Manager fournit plusieurs fonctions pour effectuer des comparaisons dans votre modèle Azure Resource Manager (modèle ARM) :

* [and](#and)
* [bool](#bool)
* [false](#false)
* [if](#if)
* [not](#not)
* [or](#or)
* [true](#true)

## <a name="and"></a>et

`and(arg1, arg2, ...)`

Vérifie si toutes les valeurs de paramètres sont true.

La fonction `and` n’est pas prise en charge dans Bicep. Privilégiez l’[opérateur &&](../bicep/operators-logical.md#and-).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |boolean |La première valeur pour vérifier si c’est true. |
| arg2 |Oui |boolean |La deuxième valeur pour vérifier si c’est true. |
| arguments supplémentaires |Non |boolean |Arguments supplémentaires pour vérifier si les valeurs correspondent à true. |

### <a name="return-value"></a>Valeur de retour

Retourne **True** si toutes les valeurs sont true ; sinon, renvoie **False**.

### <a name="examples"></a>Exemples

L’exemple suivant montre comment utiliser des fonctions logiques.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/andornot.json":::

La sortie de l’exemple précédent est :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="bool"></a>bool

`bool(arg1)`

Convertit le paramètre en valeur booléenne.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |chaîne ou entier |La valeur à convertir en booléen. |

### <a name="return-value"></a>Valeur de retour

Valeur booléenne de la valeur convertie.

### <a name="remarks"></a>Remarques

Vous pouvez également utiliser [true()](#true) et [false()](#false) pour obtenir des valeurs booléennes.

### <a name="examples"></a>Exemples

L’exemple suivant montre comment utiliser le booléen avec une chaîne ou un entier.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/bool.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="false"></a>false

`false()`

Retourne false.

La fonction `false` n’est pas disponible dans Bicep.  Utilisez plutôt le mot clé `false`.

### <a name="parameters"></a>Paramètres

La fonction false n’accepte aucun paramètre.

### <a name="return-value"></a>Valeur de retour

Valeur booléenne qui correspond toujours à false.

### <a name="example"></a>Exemple

L’exemple suivant retourne une valeur de sortie false.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/false.json":::

La sortie de l’exemple précédent est :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| falseOutput | Bool | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Retourne une valeur indiquant si une condition est true ou false.

La fonction `if` n’est pas prise en charge dans Bicep. Utilisez plutôt [l’opérateur ?:](../bicep/operators-logical.md#conditional-expression--).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| condition |Oui |boolean |La valeur pour vérifier si c’est true ou false. |
| trueValue |Oui | chaîne, int, objet ou tableau |La valeur à retourner lorsque la condition est true. |
| falseValue |Oui | chaîne, int, objet ou tableau |La valeur à retourner lorsque la condition est false. |

### <a name="return-value"></a>Valeur de retour

Retourne le deuxième paramètre lorsque le premier paramètre est **True** ; sinon, retourne le troisième paramètre.

### <a name="remarks"></a>Remarques

Lorsque la condition est **True**, seule la valeur true est évaluée. Lorsque la condition est **False**, seule la valeur false est évaluée. Avec la fonction `if`, vous pouvez inclure des expressions qui sont uniquement valides de manière conditionnelle. Par exemple, vous pouvez référencer une ressource qui existe sous une condition, mais pas sous l’autre condition. Vous trouverez un exemple illustrant des expressions évaluant de manière conditionnelle dans la section suivante.

### <a name="examples"></a>Exemples

L’exemple suivant explique comment utiliser la fonction `if`.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/if.json":::

La sortie de l’exemple précédent est :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| yesOutput | String | Oui |
| noOutput | String | non |
| objectOutput | Object | { "test": "value1" } |

L’[exemple de modèle](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) suivant montre comment utiliser cette fonction avec des expressions qui sont uniquement valides de manière conditionnelle.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "logAnalytics": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
    {
      "condition": "[not(empty(parameters('logAnalytics')))]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "apiVersion": "2017-03-30",
      "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
      "location": "[parameters('location')]",
      "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
        },
        "protectedSettings": {
          "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
        }
      }
    }
  ],
  "outputs": {
    "mgmtStatus": {
      "type": "string",
      "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
    }
  }
}
```

## <a name="not"></a>not

`not(arg1)`

Convertit la valeur booléenne à sa valeur opposée.

La fonction `not` n’est pas prise en charge dans Bicep. Privilégiez l’[opérateur !](../bicep/operators-logical.md#not-).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |boolean |Valeur à convertir. |

### <a name="return-value"></a>Valeur de retour

Retourne **True** lorsque le paramètre est **False**. Retourne **False** lorsque le paramètre est **True**.

### <a name="examples"></a>Exemples

L’exemple suivant montre comment utiliser des fonctions logiques.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/andornot.json":::

La sortie de l’exemple précédent est :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

L’exemple suivant utilise `not` avec[égal à](template-functions-comparison.md#equals).

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/not-equals.json":::

La sortie de l’exemple précédent est :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="or"></a>ou

`or(arg1, arg2, ...)`

Vérifie si l’une des valeurs du paramètre est true.

La fonction `or` n’est pas prise en charge dans Bicep. Privilégiez l’[opérateur ||](../bicep/operators-logical.md#or-).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |boolean |La première valeur pour vérifier si c’est true. |
| arg2 |Oui |boolean |La deuxième valeur pour vérifier si c’est true. |
| arguments supplémentaires |Non |boolean |Arguments supplémentaires pour vérifier si les valeurs correspondent à true. |

### <a name="return-value"></a>Valeur de retour

Retourne **True** si l’une des valeurs est true ; sinon, renvoie **False**.

### <a name="examples"></a>Exemples

L’exemple suivant montre comment utiliser des fonctions logiques.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/andornot.json":::

La sortie de l’exemple précédent est :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="true"></a>true

`true()`

Retourne la valeur true.

La fonction `true` n’est pas disponible dans Bicep.  Utilisez plutôt le mot clé `true`.

### <a name="parameters"></a>Paramètres

La fonction true n’accepte aucun paramètre.

### <a name="return-value"></a>Valeur de retour

Valeur booléenne qui correspond toujours à true.

### <a name="example"></a>Exemple

L’exemple suivant retourne une valeur de sortie true.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/true.json":::

La sortie de l’exemple précédent est :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| trueOutput | Bool | True |

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une description des sections d’un modèle ARM, consultez [Comprendre la structure et la syntaxe des modèles ARM](./syntax.md).
