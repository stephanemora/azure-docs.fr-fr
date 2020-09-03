---
title: Créer des stratégies pour les propriétés de tableau sur des ressources
description: Apprenez à gérer des paramètres de tableau et des expressions de langage de tableau, à évaluer l’alias [*] et à ajouter des éléments avec des règles de définition de stratégie Azure.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 5b9392a943e264ae5eca989ee87eb9ff09b36972
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048480"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Créer des stratégies pour les propriétés de tableau sur des ressources Azure

Les propriétés Azure Resource Manager sont généralement définies comme des chaînes et des valeurs booléennes. Lorsqu’il existe une relation un-à-plusieurs, des propriétés complexes sont plutôt définies comme des tableaux. Dans Azure Policy, les tableaux sont utilisés de différentes manières :

- Le type d’un [paramètre de définition](../concepts/definition-structure.md#parameters), pour fournir plusieurs options
- Une partie d’une [règle de stratégie](../concepts/definition-structure.md#policy-rule) utilisant les conditions **In** ou **notIn**
- Il s’agit d’une partie d’une règle de stratégie qui évalue l’alias [\[\*\]](../concepts/definition-structure.md#understanding-the--alias) pour évaluer ce qui suit :
  - Scénarios tels que **None** (Aucun), **Any** (N’importe lequel) ou **All** (Tout)
  - Scénarios complexes avec **count**
- Dans [l’effet Append](../concepts/effects.md#append) pour remplacer ou ajouter à un tableau existant

Cet article traite chaque utilisation par Azure Policy et fournit plusieurs exemples de définitions.

## <a name="parameter-arrays"></a>Tableaux de paramètres

### <a name="define-a-parameter-array"></a>Définir un tableau de paramètres

La définition d’un paramètre sous forme de tableau permet une flexibilité de la stratégie lorsque plusieurs valeurs sont nécessaires.
Cette définition de stratégie permet de régler n’importe quel emplacement unique pour le paramètre **allowedLocations** et les valeurs par défaut sur _eastus2_:

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

Comme le **type** était _chaîne_, une seule valeur peut être définie lors de l’affectation de la stratégie. Si cette stratégie est affectée, les ressources dans l’étendue sont autorisées uniquement dans une seule région Azure. La plupart des définitions de stratégies doivent autoriser une liste des options approuvées, par exemple pour autoriser _eastus2_, _eastus_, et _westus2_.

Pour créer la définition de stratégie visant à autoriser plusieurs options, utilisez le **type** _tableau_. La même stratégie peut être réécrite comme suit :

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
> Lorsqu’une définition de stratégie est enregistrée, la propriété **type** sur un paramètre ne peut pas être modifiée.

Cette nouvelle définition de paramètre accepte plusieurs valeurs lors de l’affectation de stratégie. Avec la propriété de tableau **allowedValues** définie, les valeurs disponibles lors de l’affectation sont limitées à la liste prédéfinie de choix. L’utilisation de **allowedValues** est facultative.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Passer des valeurs à un tableau de paramètres lors de l’attribution

Lors de l’affectation de la stratégie via le portail Azure, un paramètre de **type** _tableau_ s’affiche sous la forme d’une zone de texte unique. L’indicateur indique « Utiliser ’;’ pour séparer des valeurs. (par exemple, Londres;New York) ». Pour passer les valeurs d’emplacement autorisées de _eastus2_, _eastus_ et _westus2_ au paramètre, utilisez la chaîne suivante :

`eastus2;eastus;westus2`

Le format de la valeur du paramètre est différent lorsque vous utilisez Azure CLI, Azure PowerShell ou l’API REST. Les valeurs sont transmises via une chaîne JSON qui inclut également le nom du paramètre.

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

Pour utiliser cette chaîne avec chaque kit SDK, utilisez les commandes suivantes :

- Azure CLI : Commande [az policy assignment create](/cli/azure/policy/assignment#az-policy-assignment-create) avec le paramètre **params**
- Azure PowerShell : Applet de commande [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) avec le paramètre **PolicyParameter**
- API REST : Dans l’opération _PUT_ [create](/rest/api/resources/policyassignments/create) en tant que partie intégrante du corps de la demande en tant que valeur d’une propriété **properties.parameters**

## <a name="policy-rules-and-arrays"></a>Règles de stratégie et tableaux

### <a name="array-conditions"></a>Conditions de tableau

La règle de stratégie [conditions](../concepts/definition-structure.md#conditions) avec laquelle un _type_
**tableau** du paramètre peut être utilisé est limitée à `in` et `notIn`. Prenez la définition de stratégie suivante avec la condition `equals` comme exemple :

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

Une tentative de création de cette définition de stratégie via le portail Azure entraîne une erreur telle que ce message d’erreur :

- « La stratégie '{GUID}' ne peut pas être paramétrée en raison d’erreurs de validation. Vérifiez si les paramètres de stratégie sont correctement définis. L’exception interne « Résultat d’évaluation de l’expression de langage '[parameters('allowedLocations')]' est de type « Tableau », le type attendu est « String ». »

Le **type** attendu de la condition `equals` est _chaîne_. Dans la mesure où **allowedLocations** est de **type** _tableau_, le moteur de stratégie évalue l’expression de langage et affiche l’erreur. Avec les conditions `in` et `notIn`, le moteur de stratégie attend le **type** _tableau_ dans l’expression de langage. Pour résoudre ce message d’erreur, remplacez `equals` par `in` ou `notIn`.

### <a name="evaluating-the--alias"></a>Évaluation de l’alias [*]

Les alias ayant **\[\*\]** attaché à leur nom indiquent que le **type** est un _tableau_. Au lieu d’évaluer la valeur de l’intégralité du tableau, **\[\*\]** permet d’évaluer chacun des éléments qu’il contient, avec un ET logique entre eux. Il existe trois scénarios standards au sein desquels cette évaluation par élément est utile : en cas de correspondance avec _aucun élément_, _n’importe quel élément_ ou _tous les éléments_. Pour les scénarios complexes, utilisez [count](../concepts/definition-structure.md#count).

Le moteur de stratégie déclenche l’**effet** dans **then** uniquement lorsque la règle **if** est évaluée comme True.
Il est important de comprendre ce point dans le contexte de la façon dont **\[\*\]** évalue chaque élément individuel du tableau.

L’exemple de règle de stratégie pour la table de scénario ci-dessous :

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

Pour la table de scénario ci-dessous, le tableau des **ipRules** se présente de la façon suivante :

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

Pour chaque exemple de condition ci-dessous, remplacez `<field>` par `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

Les résultats suivants viennent de la combinaison de la condition et de l’exemple de règle de stratégie et le tableau des valeurs existantes ci-dessus :

|Condition |Résultat | Scénario |Explication |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Rien |Aucun ne correspond |Un élément du tableau est évalué comme false (127.0.0.1 ! = 127.0.0.1) et un autre comme true (127.0.0.1 ! = 192.168.1.1), donc la condition **notEquals** est _false_ et l’effet n’est pas déclenché. |
|`{<field>,"notEquals":"10.0.4.1"}` |Effet de la stratégie |Aucun ne correspond |Les éléments du tableau sont évalués comme true (10.0.4.1 != 127.0.0.1 et 10.0.4.1 != 192.168.1.1), donc la condition **notEquals** est _true_ et l’effet est déclenché. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Effet de la stratégie |Un ou plusieurs correspondent |Un élément du tableau est évalué comme false (127.0.0.1 != 127.0.0.1) et un autre comme true (127.0.0.1 != 192.168.1.1), donc la condition **notEquals** est _false_. L’opérateur logique est évalué comme true (**non** _false_), de sorte que l’effet est déclenché. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Rien |Un ou plusieurs correspondent |Les deux éléments du tableau sont évalués comme false (10.0.4.1 != 127.0.0.1 et 10.0.4.1 != 192.168.1.1), donc la condition **notEquals** est _true_. L’opérateur logique est évalué comme false (**non** _true_), de sorte que l’effet n’est pas déclenché. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Effet de la stratégie |Tous ne correspondent pas |Un élément du tableau est évalué comme true (127.0.0.1 == 127.0.0.1) et un autre comme false (127.0.0.1 == 192.168.1.1), donc la condition **Equals** est _false_. L’opérateur logique est évalué comme true (**non** _false_), de sorte que l’effet est déclenché. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Effet de la stratégie |Tous ne correspondent pas |Les deux éléments du tableau sont évalués comme false (10.0.4.1 == 127.0.0.1 et 10.0.4.1 == 192.168.1.1), donc la condition **Equals** est _false_. L’opérateur logique est évalué comme true (**non** _false_), de sorte que l’effet est déclenché. |
|`{<field>,"Equals":"127.0.0.1"}` |Rien |Tous correspondent |Un élément du tableau est évalué comme true (127.0.0.1 == 127.0.0.1) et un autre comme true (127.0.0.1 == 192.168.1.1), donc la condition **Equals** est _false_ et l’effet n’est pas déclenché. |
|`{<field>,"Equals":"10.0.4.1"}` |Rien |Tous correspondent |Les deux éléments du tableau sont évalués comme false (10.0.4.1 == 127.0.0.1 et 10.0.4.1 == 192.168.1.1), donc la condition **Equals** est _false_ et l’effet n’est pas déclenché. |

## <a name="the-append-effect-and-arrays"></a>L’effet Append et les tableaux

L’[effet Append](../concepts/effects.md#append) se comporte différemment selon que **details.field** est un alias **\[\*\]** ou non.

- Lorsqu’il ne s’agit pas d’un alias **\[\*\]** , Append remplace le tableau entier avec la propriété **value**
- Lorsqu’il s’agit d’un alias **\[\*\]** , Append ajoute la propriété **value** au tableau existant ou crée le nouveau tableau

Pour plus d’informations, consultez ces [exemples Append](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Étapes suivantes

- Consultez des exemples à la page [Exemples Azure Policy](../samples/index.md).
- Consultez la [Structure de définition Azure Policy](../concepts/definition-structure.md).
- Consultez la page [Compréhension des effets de Policy](../concepts/effects.md).
- Découvrez comment [créer des stratégies par programmation](programmatically-create.md).
- Découvrez comment [corriger des ressources non conformes](remediate-resources.md).
- Pour en savoir plus sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../../management-groups/overview.md).
