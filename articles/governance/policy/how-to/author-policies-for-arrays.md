---
title: Créer des stratégies pour les propriétés du groupe de ressources Azure
description: Apprenez à créer des paramètres de tableau, de créer des expressions de langage de règles pour le tableau, d’évaluer l’alias [*] et pour ajouter des éléments à un tableau existant avec des règles de définition Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/06/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 38cf6decb8e61768faa9680058f6366e1550ba40
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59274721"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Créer des stratégies pour les propriétés du groupe de ressources Azure

Azure Resource Manager propriétés sont généralement définies comme des chaînes et des valeurs booléennes. Lorsqu’une relation un-à-plusieurs existe, propriétés complexes sont définies à la place en tant que tableaux. Dans Azure Policy, les tableaux sont utilisées de différentes manières :

- Le type d’un [paramètre de définition de](../concepts/definition-structure.md#parameters), pour fournir plusieurs options
- Partie d’un [règle de stratégie](../concepts/definition-structure.md#policy-rule) à l’aide de conditions **dans** ou **notIn**
- Partie d’une règle de stratégie qui évalue le [ \[ \* \] alias](../concepts/definition-structure.md#understanding-the--alias) pour évaluer les scénarios spécifiques, telles que **aucun**, **tout**, ou  **Tous les**
- Dans le [Ajouter effet](../concepts/effects.md#append) pour remplacer ou ajouter à un tableau existant

Cet article traite chaque utilisation par Azure Policy et fournit plusieurs exemples de définitions.

## <a name="parameter-arrays"></a>Tableaux de paramètres

### <a name="define-a-parameter-array"></a>Définir un tableau de paramètres

Définition d’un paramètre sous forme de tableau permet de la flexibilité de la stratégie lorsque plusieurs valeurs est nécessaire.
Cette définition de stratégie permet à n’importe quel emplacement unique pour le paramètre **allowedLocations** et la valeur par défaut est _eastus2_:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

En tant que **type** a été _chaîne_uniquement une seule valeur peut être définie lorsque l’affectation de la stratégie. Si cette stratégie est affectée, les ressources dans l’étendue sont autorisés uniquement dans une seule région Azure. La plupart des définitions de stratégies doivent autoriser pour obtenir la liste des options approuvées, par exemple pour autoriser _eastus2_, _eastus_, et _westus2_.

Pour créer la définition de stratégie pour autoriser plusieurs options, utilisez le _tableau_ **type**. La même stratégie peut être réécrit comme suit :

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> Une fois une définition de stratégie est enregistrée, le **type** propriété sur un paramètre ne peut pas être modifiée.

Cette nouvelle définition de paramètre accepte plusieurs valeurs lors de l’attribution de stratégie. Avec la propriété du tableau **allowedValues** défini, les valeurs disponibles lors de l’affectation sont encore plus limité à la liste prédéfinie de choix. Utilisation de **allowedValues** est facultatif.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Passer des valeurs à un tableau de paramètres lors de l’attribution

Lors de l’affectation de la stratégie via le portail Azure, un paramètre de **type** _tableau_ s’affiche comme une zone de texte unique. L’indicateur indique « utiliser ; pour séparer les valeurs. (par exemple, Londres ; New York) ». Pour passer les valeurs d’emplacement autorisées de _eastus2_, _eastus_, et _westus2_ au paramètre, utilisez la chaîne suivante :

`eastus2;eastus;westus2`

Le format de la valeur du paramètre est différent lorsque vous utilisez Azure CLI, Azure PowerShell ou l’API REST. Les valeurs sont transmises à une chaîne JSON qui inclut également le nom du paramètre.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Pour utiliser cette chaîne avec chaque kit de développement logiciel, utilisez les commandes suivantes :

- Interface de ligne de commande Azure : Commande [créer d’attribution de stratégie az](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) avec le paramètre **params**
- Azure PowerShell : Applet de commande [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) avec le paramètre **PolicyParameter**
- API REST : Dans le _PUT_ [créer](/rest/api/resources/policyassignments/create) opération en tant que partie intégrante du corps de demande comme valeur de la **properties.parameters** propriété

## <a name="policy-rules-and-arrays"></a>Tableaux et les règles de stratégie

### <a name="array-conditions"></a>Conditions de tableau

La règle de stratégie [conditions](../concepts/definition-structure.md#conditions) qui un _tableau_
**type** du paramètre peut être utilisé avec est limité à `in` et `notIn`. Prendre la définition de stratégie suivante avec la condition `equals` par exemple :

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

Tentative de création de cette définition de stratégie via le portail Azure de prospects à une erreur telle que ce message d’erreur :

- « La stratégie '{GUID}' Impossible de paramétrer en raison d’erreurs de validation. Vérifiez si les paramètres de stratégie sont correctement définies. L’exception interne 'd’évaluation de résultat de l’expression de langage '[parameters('allowedLocations')]' est de type « Array », type attendu : 'String' '. »

Attendu **type** de condition `equals` est _chaîne_. Dans la mesure où **allowedLocations** est défini comme **type** _tableau_, le moteur de stratégie évalue l’expression de langage et génère l’erreur. Avec le `in` et `notIn` condition, le moteur de stratégie attend le **type** _tableau_ dans l’expression de langage. Pour résoudre ce message d’erreur, modifiez `equals` soit `in` ou `notIn`.

### <a name="evaluating-the--alias"></a>L’évaluation de l’alias [*]

Alias ayant **[\*]** attaché à leur nom d’indiquer le **type** est un _tableau_. Au lieu d’évaluer la valeur de l’intégralité du tableau, **[\*]** rend possible évaluer chaque élément du tableau. Il existe trois scénarios dans que cela par évaluation de l’élément est utile : None, une et tous.

Les déclencheurs de moteur de stratégie le **effet** dans **puis** uniquement lorsque le **si** règle est évaluée sur true.
Ce point est important de comprendre dans le contexte de la façon **[\*]** évalue chaque élément individuel du tableau.

La règle de stratégie d’exemple pour la table de scénario ci-dessous :

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Le **ipRules** tableau est comme suit pour le tableau de scénario ci-dessous :

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Pour chaque exemple de condition ci-dessous, remplacez `<field>` avec `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

Les résultats suivants sont le résultat de la combinaison de la condition et l’exemple de règle de stratégie et le tableau de valeurs existantes ci-dessus :

|Condition |Résultat |Explication |
|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nothing |Un élément de tableau est évaluée comme fausse (127.0.0.1 ! = 127.0.0.1) et l’autre comme true (127.0.0.1 ! = 192.168.1.1), la **notEquals** condition est _false_ et l’effet n’est pas déclenché. |
|`{<field>,"notEquals":"10.0.4.1"}` |Effet de la stratégie |Les deux éléments de tableau est évaluée comme vraie (10.0.4.1 ! = 127.0.0.1 et 10.0.4.1 ! = 192.168.1.1), la **notEquals** condition est _true_ et l’effet est déclenchée. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Effet de la stratégie |Un élément de tableau est évaluée sur true (127.0.0.1 == 127.0.0.1) et l’autre comme false (127.0.0.1 == 192.168.1.1), la **est égal à** condition est _false_. L’opérateur logique est évaluée sur true (**pas** _false_), de sorte que l’effet est déclenchée. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Effet de la stratégie |Les deux éléments de tableau est évaluée comme false (10.0.4.1 == 127.0.0.1 et 10.0.4.1 == 192.168.1.1), la **est égal à** condition est _false_. L’opérateur logique est évaluée sur true (**pas** _false_), de sorte que l’effet est déclenchée. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Effet de la stratégie |Un élément de tableau est évaluée comme fausse (127.0.0.1 ! = 127.0.0.1) et l’autre comme true (127.0.0.1 ! = 192.168.1.1), la **notEquals** condition est _false_. L’opérateur logique est évaluée sur true (**pas** _false_), de sorte que l’effet est déclenchée. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nothing |Les deux éléments de tableau est évaluée comme vraie (10.0.4.1 ! = 127.0.0.1 et 10.0.4.1 ! = 192.168.1.1), la **notEquals** condition est _true_. L’opérateur logique est évaluée comme fausse (**pas** _true_), de sorte que l’effet n’est pas déclenché. |
|`{<field>,"Equals":"127.0.0.1"}` |Nothing |Un élément de tableau est évaluée sur true (127.0.0.1 == 127.0.0.1) et l’autre comme false (127.0.0.1 == 192.168.1.1), la **est égal à** condition est _false_ et l’effet n’est pas déclenché. |
|`{<field>,"Equals":"10.0.4.1"}` |Nothing |Les deux éléments de tableau est évaluée comme false (10.0.4.1 == 127.0.0.1 et 10.0.4.1 == 192.168.1.1), la **est égal à** condition est _false_ et l’effet n’est pas déclenché. |

## <a name="the-append-effect-and-arrays"></a>L’effet de l’ajout et les tableaux

Le [Ajouter effet](../concepts/effects.md#append) se comporte différemment selon que le **details.field** est un **[\*]** alias ou non.

- Lorsque pas un **[\*]** alias, ajoutez remplace l’intégralité du tableau avec la **valeur** propriété
- Quand un **[\*]** ajouter des alias, ajoute le **valeur** propriété à l’objet de tableau ou crée le tableau

Pour plus d’informations, consultez le [append, exemples](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Étapes suivantes

- Consulter des exemples à la page [Exemples Azure Policy](../samples/index.md)
- Consulter la page [Structure de définition Azure Policy](../concepts/definition-structure.md)
- Consulter la page [Compréhension des effets d’Azure Policy](../concepts/effects.md)
- Savoir comment [créer des stratégies par programmation](programmatically-create.md)
- Découvrir comment [remédier à la non-conformité des ressources](remediate-resources.md)
- Pour en savoir plus sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../../management-groups/overview.md).