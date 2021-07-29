---
title: Fonctions de modèle - Logique
description: Décrit les fonctions à utiliser dans un modèle Azure Resource Manager (modèle ARM) pour déterminer les valeurs logiques.
ms.topic: conceptual
ms.date: 05/13/2021
ms.openlocfilehash: c69e10b660d5b7cbf768ea31fda6678d07053224
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111959627"
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

La fonction `and` n’est pas prise en charge dans Bicep. Utilisez l’ [opérateur &&](../bicep/operators-logical.md#and-) à la place.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |boolean |La première valeur pour vérifier si c’est true. |
| arg2 |Oui |boolean |La deuxième valeur pour vérifier si c’est true. |
| arguments supplémentaires |Non |boolean |Arguments supplémentaires pour vérifier si les valeurs sont égales à true. |

### <a name="return-value"></a>Valeur de retour

Retourne **True** si toutes les valeurs sont true ; sinon, renvoie **False**.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) suivant montre comment utiliser des fonctions logiques.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]"
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

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

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) suivant montre comment utiliser bool avec une chaîne ou un entier.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueString": {
      "type": "bool",
      "value": "[bool('true')]",
    },
    "falseString": {
      "type": "bool",
      "value": "[bool('false')]"
    },
    "trueInt": {
      "type": "bool",
      "value": "[bool(1)]"
    },
    "falseInt": {
      "type": "bool",
      "value": "[bool(0)]"
    }
  }
}
```

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

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "falseOutput": {
      "type": "bool",
      "value": "[false()]"
    }
  }
}
```

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

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) suivant montre comment utiliser la fonction `if`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "yesOutput": {
      "type": "string",
      "value": "[if(equals('a', 'a'), 'yes', 'no')]"
    },
    "noOutput": {
      "type": "string",
      "value": "[if(equals('a', 'b'), 'yes', 'no')]"
    },
    "objectOutput": {
      "type": "object",
      "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
    }
  }
}
```

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

La fonction `not` n’est pas prise en charge dans Bicep. Utilisez l’ [opérateur !](../bicep/operators-logical.md#not-) à la place.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |boolean |Valeur à convertir. |

### <a name="return-value"></a>Valeur de retour

Retourne **True** lorsque le paramètre est **False**. Retourne **False** lorsque le paramètre est **True**.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) suivant montre comment utiliser des fonctions logiques.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]",
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

La sortie de l’exemple précédent est :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) suivant utilise **not** avec [equals](template-functions-comparison.md#equals).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "checkNotEquals": {
      "type": "bool",
      "value": "[not(equals(1, 2))]"
    }
  }
}
```

La sortie de l’exemple précédent est :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="or"></a>ou

`or(arg1, arg2, ...)`

Vérifie si l’une des valeurs du paramètre est true.

La fonction `or` n’est pas prise en charge dans Bicep. Utilisez l’ [opérateur ||](../bicep/operators-logical.md#or-) à la place.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |boolean |La première valeur pour vérifier si c’est true. |
| arg2 |Oui |boolean |La deuxième valeur pour vérifier si c’est true. |
| arguments supplémentaires |Non |boolean |Arguments supplémentaires pour vérifier si les valeurs sont égales à true. |

### <a name="return-value"></a>Valeur de retour

Retourne **True** si l’une des valeurs est true ; sinon, renvoie **False**.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) suivant montre comment utiliser des fonctions logiques.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "value": "[and(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "orExampleOutput": {
      "value": "[or(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "notExampleOutput": {
      "value": "[not(bool('true'))]",
      "type": "bool"
    }
  }
}
```

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

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueOutput": {
      "type": "bool",
      "value": "[true()]"
    }
  }
}
```

La sortie de l’exemple précédent est :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| trueOutput | Bool | True |

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une description des sections d’un modèle ARM, consultez [Comprendre la structure et la syntaxe des modèles ARM](./syntax.md).