---
title: Fonctions de modèle - Logique
description: Décrit les fonctions à utiliser dans un modèle Azure Resource Manager pour déterminer les valeurs logiques.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: df8433d167a166fe94d965f81e42cd0b3e8f0e54
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150697"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Fonctions logiques pour les modèles Azure Resource Manager

Resource Manager fournit plusieurs fonctions pour effectuer des comparaisons dans vos modèles.

* [and](#and)
* [bool](#bool)
* [si](#if)
* [non](#not)
* [or](#or)

## <a name="and"></a>and

`and(arg1, arg2, ...)`

Vérifie si toutes les valeurs de paramètres sont true.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |OUI |boolean |La première valeur pour vérifier si c’est true. |
| arg2 |OUI |boolean |La deuxième valeur pour vérifier si c’est true. |
| arguments supplémentaires |Non |boolean |Arguments supplémentaires pour vérifier si les valeurs sont égales à true. |

### <a name="return-value"></a>Valeur de retour

Retourne **True** si toutes les valeurs sont true ; sinon, renvoie **False**.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) suivant montre comment utiliser des fonctions logiques.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
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

| Nom | type | Valeur |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="bool"></a>bool

`bool(arg1)`

Convertit le paramètre en valeur booléenne.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |OUI |chaîne ou entier |La valeur à convertir en booléen. |

### <a name="return-value"></a>Valeur de retour
Valeur booléenne de la valeur convertie.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) suivant montre comment utiliser bool avec une chaîne ou un entier.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Retourne une valeur indiquant si une condition est true ou false.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| condition |OUI |boolean |La valeur pour vérifier si c’est true ou false. |
| trueValue |OUI | chaîne, int, objet ou tableau |La valeur à retourner lorsque la condition est true. |
| falseValue |OUI | chaîne, int, objet ou tableau |La valeur à retourner lorsque la condition est false. |

### <a name="return-value"></a>Valeur de retour

Retourne le deuxième paramètre lorsque le premier paramètre est **True** ; sinon, retourne le troisième paramètre.

### <a name="remarks"></a>Remarques

Lorsque la condition est **True**, seule la valeur true est évaluée. Lorsque la condition est **False**, seule la valeur false est évaluée. Avec la fonction **si**, vous pouvez inclure des expressions qui sont uniquement valides de manière conditionnelle. Par exemple, vous pouvez référencer une ressource qui existe sous une condition, mais pas sous l’autre condition. Vous trouverez un exemple illustrant des expressions évaluant de manière conditionnelle dans la section suivante.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) suivant montre comment utiliser la fonction `if`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

| Nom | type | Valeur |
| ---- | ---- | ----- |
| yesOutput | Chaîne | Oui |
| noOutput | Chaîne | no |
| objectOutput | Object | { "test": "value1" } |

L’[exemple de modèle](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) suivant montre comment utiliser cette fonction avec des expressions qui sont uniquement valides de manière conditionnelle.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2017-03-30",
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

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |OUI |boolean |La valeur à convertir. |

### <a name="return-value"></a>Valeur de retour

Retourne **True** lorsque le paramètre est **False**. Retourne **False** lorsque le paramètre est **True**.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) suivant montre comment utiliser des fonctions logiques.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
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

| Nom | type | Valeur |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) suivant utilise **not** avec [equals](resource-group-template-functions-comparison.md#equals).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
```

La sortie de l’exemple précédent est :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="or"></a>or

`or(arg1, arg2, ...)`

Vérifie si l’une des valeurs du paramètre est true.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |OUI |boolean |La première valeur pour vérifier si c’est true. |
| arg2 |OUI |boolean |La deuxième valeur pour vérifier si c’est true. |
| arguments supplémentaires |Non |boolean |Arguments supplémentaires pour vérifier si les valeurs sont égales à true. |

### <a name="return-value"></a>Valeur de retour

Retourne **True** si l’une des valeurs est true ; sinon, renvoie **False**.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) suivant montre comment utiliser des fonctions logiques.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
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

| Nom | type | Valeur |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une description des sections d’un modèle Azure Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour fusionner plusieurs modèles, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).
* Pour itérer un nombre de fois spécifié lors de la création d'un type de ressource, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).
* Pour savoir comment déployer le modèle que vous avez créé, consultez [Déployer une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

