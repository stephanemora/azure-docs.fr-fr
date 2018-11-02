---
title: Structure de définition Azure Policy
description: Explique comment Azure Policy utilise une définition de stratégie de ressource afin d’établir des conventions pour les ressources de votre organisation en décrivant quand la stratégie est appliquée et la mesure à prendre.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: f5906a93e92691cb6046fb04a9fd83f3484e17b8
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427397"
---
# <a name="azure-policy-definition-structure"></a>Structure de définition Azure Policy

Une définition de stratégie de ressource utilisée par Azure Policy vous permet d’établir des conventions pour les ressources de votre organisation en décrivant quand la stratégie est appliquée et la mesure à prendre. En définissant des conventions, vous pouvez contrôler les coûts et gérer plus facilement vos ressources. Par exemple, vous pouvez spécifier que seuls certains types de machines virtuelles sont autorisés. Vous pouvez aussi exiger que toutes les ressources soient marquées. Toutes les ressources enfants héritent des stratégies. Ainsi, si une stratégie est appliquée à un groupe de ressources, elle est applicable à toutes les ressources appartenant à ce groupe de ressources.

Le schéma utilisé par Azure Policy se trouve ici : [https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json](https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json)

Vous devez utiliser JSON pour créer une définition de stratégie. La définition de stratégie contient des éléments pour :

- le mode
- parameters
- le nom d’affichage
- description
- la règle de stratégie
  - évaluation logique
  - effet

Par exemple, le code JSON suivant illustre une stratégie qui limite les emplacements où les ressources sont déployées :

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

Tous les exemples Azure Policy se trouvent dans [Exemples de stratégies](../samples/index.md).

## <a name="mode"></a>Mode

Le **mode** détermine les types de ressources à évaluer pour une stratégie. Les modes pris en charge sont les suivants :

- `all` : évaluer les groupes de ressources et tous les types de ressources
- `indexed` : évaluer uniquement les types de ressources qui prennent en charge les balises et l’emplacement

Nous vous recommandons de définir **mode** sur `all` dans tous les cas. Toutes les définitions de stratégie créées via le portail utilisent le mode `all`. Si vous utilisez PowerShell ou Azure CLI, vous pouvez spécifier le paramètre **mode** manuellement. Si la définition de stratégie ne contient pas de valeur **mode**, sa valeur par défaut est `all` dans Azure PowerShell et `null` dans Azure CLI, ce qui équivaut à `indexed`, à des fins de compatibilité descendante.

`indexed` doit être utilisé lors de la création de stratégies qui appliqueront des balises ou des emplacements. Cela n’est pas nécessaire, mais empêche les ressources qui ne prennent pas en charge les balises et les emplacements de s’afficher comme des résultats non conformes dans les résultats de conformité. La seule exception est les **groupes de ressources**. Les stratégies qui tentent d’appliquer des emplacements ou des balises à un groupe de ressources doivent définir **mode** sur `all` et cibler spécifiquement le type `Microsoft.Resources/subscriptions/resourceGroup`. Pour exemple, consultez [Appliquer des balises au groupe de ressources](../samples/enforce-tag-rg.md).

## <a name="parameters"></a>parameters

Les paramètres permettent de simplifier la gestion des stratégies en réduisant le nombre de définitions de stratégies. Considérez les paramètres comme les champs d’un formulaire : `name`, `address`, `city`, `state`. Ces paramètres restent toujours les mêmes ; toutefois, leurs valeurs changent en fonction de la personne qui remplit le formulaire.
Les paramètres fonctionnent de manière identique durant la création de stratégies. En incluant des paramètres dans une définition de stratégie, vous pouvez réutiliser cette stratégie pour différents scénarios avec des valeurs différentes.

> [!NOTE]
> La définition de paramètres pour une définition de stratégie ou d’initiative ne peut être configurée que pendant la création initiale de la stratégie ou de l’initiative. La définition de paramètres ne peut être modifiée ultérieurement.
> Cela empêche les affectations de stratégie ou d’initiative déjà existantes d’être indirectement invalidées.

Par exemple, vous pouvez définir une stratégie pour une propriété de ressource afin de limiter les emplacements sur lesquels les ressources peuvent être déployées. Dans ce cas, vous déclarez les paramètres suivants quand vous créez votre stratégie :

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        }
    }
}
```

Le type d’un paramètre peut être une chaîne ou un tableau. La propriété de métadonnées est utilisée pour des outils comme le portail Azure pour afficher des informations conviviales.

Dans la propriété de métadonnées, vous pouvez utiliser **strongType** pour fournir une liste à choix multiple des options dans le portail Azure. Les valeurs autorisées pour **strongType** incluent actuellement :

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

Dans la règle de stratégie, vous référencez des paramètres avec la syntaxe suivante de valeur de déploiement `parameters` :

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="definition-location"></a>Emplacement de la définition

Quand vous créez une définition d’initiative ou de stratégie, il est important de spécifier son emplacement.

L’emplacement de la définition détermine l’étendue à laquelle la définition d’initiative ou de stratégie peut être affectée. L’emplacement peut être spécifié en tant que groupe d’administration ou d’abonnement.

> [!NOTE]
> Si vous envisagez d’appliquer cette définition de stratégie à plusieurs abonnements, l’emplacement doit correspondre à un groupe d’administration qui contient les abonnements auxquels vous allez affecter l’initiative ou la stratégie.

## <a name="display-name-and-description"></a>Nom d’affichage et description

Vous pouvez utiliser **displayName** et **description** pour distinguer la définition de stratégie et préciser le contexte d’utilisation.

## <a name="policy-rule"></a>Règle de stratégie

La règle de stratégie se compose de blocs **if** et **then**. Dans le bloc **if**, vous définissez une ou plusieurs conditions qui spécifient à quel moment la stratégie est mise en œuvre. Vous pouvez appliquer des opérateurs logiques à ces conditions pour définir avec précision le scénario d’une stratégie.

Dans le bloc **then**, vous définissez l’effet qui se produit lorsque les conditions de **si** sont remplies.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists"
    }
}
```

### <a name="logical-operators"></a>Opérateurs logiques

Les opérateurs logiques pris en charge sont les suivants :

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

La syntaxe **not** inverse le résultat de la condition. La syntaxe **allOf** (semblable à l’opération logique **And**) nécessite que toutes les conditions soient remplies. La syntaxe **anyOf** (semblable à l’opération logique **Of**) nécessite qu’au moins une des conditions soit remplie.

Vous pouvez imbriquer des opérateurs logiques. L’exemple suivant illustre une opération **not** imbriquée dans une opération **allOf**.

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>Conditions

Une condition évalue si un champ (**field**) répond à certains critères. Les conditions prises en charge sont les suivantes :

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"notMatch": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"exists": "bool"`

Avec les conditions **like** et **notLike**, vous pouvez utiliser un caractère générique (`*`) dans la valeur.
Celle-ci ne doit pas en comporter plus d’un (`*`).

Lorsque vous utilisez les conditions **correspondance** et **non-correspondance**, entrez `#` pour représenter un chiffre, `?` pour une lettre, `.`pour tous les caractères, et tout autre caractère pour représenter ce caractère réel. Pour obtenir des exemples, consultez [Autoriser plusieurs modèles de nom](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Champs

Les conditions sont formées à partir de champs. Un champ représente des propriétés dans la charge utile de la requête de ressource qui est utilisée pour décrire l'état de la ressource.

Les champs suivants sont pris en charge :

- `name`
- `fullName`
  - Retourne le nom complet de la ressource, soit le nom de la ressource précédé du nom de toutes les ressources parentes (par exemple, « monServeur/maBaseDeDonnées »).
- `kind`
- `type`
- `location`
- `tags`
- `tags.<tagName>`
  - Où **\<tagName\>** est le nom de l’étiquette pour laquelle vérifier la condition.
  - Exemple : `tags.CostCenter` où **CostCenter** est le nom de l’étiquette.
- `tags[<tagName>]`
  - Cette syntaxe avec des crochets prend en charge les noms d’étiquette qui contiennent des points.
  - Où **\<tagName\>** est le nom de l’étiquette pour laquelle vérifier la condition.
  - Exemple : `tags[Acct.CostCenter]` où **Acct.CostCenter** est le nom de l’étiquette.
- alias de propriété : pour en obtenir la liste, consultez [Alias](#aliases).

### <a name="effect"></a>Résultat

La stratégie prend en charge les types d’effet suivants :

- **deny** : génère un événement dans le journal d’audit et fait échouer la requête.
- **audit** : génère un événement d’avertissement dans le journal d’audit, mais ne fait pas échouer la requête.
- **append** : ajoute l’ensemble de champs défini à la requête.
- **AuditIfNotExists** : active l’audit si une ressource n’existe pas.
- **DeployIfNotExists** : déploie une ressource si elle n’existe pas déjà.

Pour **append**, vous devez fournir les détails suivants :

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

La valeur peut être une chaîne ou un objet au format JSON.

Avec **AuditIfNotExists** et **DeployIfNotExists**, vous pouvez évaluer l’existence d’une ressource connexe et appliquer une règle et un effet correspondant quand cette ressource n’existe pas. Par exemple, vous pouvez exiger qu’un observateur réseau soit déployé pour tous les réseaux virtuels. Pour obtenir un exemple d’audit quand une extension de machine virtuelle n’est pas déployée, consultez [Auditer si une extension n’existe pas](../samples/audit-ext-not-exist.md).

Pour plus d’informations sur chaque effet, l’ordre d’évaluation, les propriétés et des exemples, voir [Présentation des effets des stratégies](effects.md).

### <a name="policy-functions"></a>Fonctions de stratégie

Une partie des [fonctions de modèle Resource Manager](../../../azure-resource-manager/resource-group-template-functions.md) peut être utilisée dans une règle de stratégie. Les fonctions prises en charge sont les suivantes :

- [parameters](../../../azure-resource-manager/resource-group-template-functions-deployment.md#parameters)
- [concat](../../../azure-resource-manager/resource-group-template-functions-array.md#concat)
- [resourceGroup](../../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)
- [abonnement](../../../azure-resource-manager/resource-group-template-functions-resource.md#subscription)

De plus, la fonction `field` est disponible pour les règles de stratégie. Cette fonction est principalement utilisée avec **AuditIfNotExists** et **DeployIfNotExists** pour référencer les champs d’une ressource actuellement évaluée. Vous pouvez en voir une illustration dans [l’exemple DeployIfNotExists](effects.md#deployifnotexists-example).

#### <a name="policy-function-examples"></a>Exemples de fonctions de stratégie

Cet exemple de règle de stratégie utilise la fonction de ressource `resourceGroup` pour obtenir la propriété **name**, combinée au tableau `concat` et à la fonction d’objet, pour créer une condition `like` selon laquelle le nom de ressource commence par le nom du groupe de ressources.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

Cet exemple de règle de stratégie utilise la fonction de ressource `resourceGroup` pour obtenir la valeur de tableau de propriétés **tags** de la balise **CostCenter** du groupe de ressources, et l’ajouter à la balise **CostCenter** de la nouvelle ressource.

```json
{
    "if": {
        "field": "tags.CostCenter",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "tags.CostCenter",
            "value": "[resourceGroup().tags.CostCenter]"
        }]
    }
}
```

## <a name="aliases"></a>Alias

Les alias de propriété permettent d’accéder aux propriétés spécifiques d’un type de ressource. Les alias permettent de restreindre les valeurs ou les conditions autorisées pour la propriété d’une ressource. Chaque alias correspond aux chemins des différentes versions d’API d’un type de ressource donné. Lors de l’évaluation de la stratégie, le moteur de stratégie obtient le chemin de la propriété de cette version de l’API.

La liste des alias augmente toujours. Pour découvrir les alias actuellement pris en charge par Azure Policy, utilisez une des méthodes suivantes :

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzureRmAccount if not using Cloud Shell

  # Use Get-AzureRmPolicyAlias to list available providers
  Get-AzureRmPolicyAlias -ListAvailable

  # Use Get-AzureRmPolicyAlias to list aliases for a Namespace (such as Azure Automation -- Microsoft.Automation)
  Get-AzureRmPolicyAlias -NamespaceMatch 'automation'
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Automation -- Microsoft.Automation)
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- API REST/ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Comprendre l’alias [*]

Plusieurs alias disponibles possèdent une version qui apparaîtra avec un nom « normal » et une autre avec **[\*]** ajouté. Par exemple : 

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Le premier exemple est utilisé pour évaluer l’intégralité du tableau, dans lequel l’alias **[\*]** évalue chaque élément du tableau.

Examinons une règle de stratégie comme exemple. Cette stratégie **refusera** un compte de stockage qui possède un ipRules configuré et si **aucun** des ipRules n’a une valeur « 127.0.0.1 ».

```json
"policyRule": {
    "if": {
        "allOf": [{
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "127.0.0.1"
            }
        ]
    },
    "then": {
        "effect": "deny",
    }
}
```

Pour l’exemple, le tableau des **ipRules** se présente de la façon suivante :

```json
"ipRules": [{
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Voici comment est traité cet exemple :

- `networkAcls.ipRules` : vérifie que le tableau est non null. Cette valeur est évaluée comme true, l’évaluation peut donc continuer.

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
    "exists": "true"
  }
  ```

- `networkAcls.ipRules[*].value` : vérifie chaque _valeur_ de propriété dans le tableau des **ipRules**.

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
    "notEquals": "127.0.0.1"
  }
  ```

  - Puisqu’il s’agit d’un tableau, tous les éléments seront traités.

    - « 127.0.0.1 » != « 127.0.0.1 » est évalué comme false.
    - « 127.0.0.1 » != « 192.168.1.1 » est évalué comme true.
    - Au moins une _valeur_ de propriété dans le tableau des **ipRules** est évaluée comme false, l’évaluation sera donc arrêtée.

Puisque la condition est évaluée comme false, l’effet **Refuser** n’est pas déclenché.

## <a name="initiatives"></a>Initiatives

Les initiatives vous permettent de regrouper en un seul élément plusieurs définitions de stratégies associées pour simplifier les affectations et la gestion. Par exemple, vous pouvez regrouper toutes les définitions de stratégies de balisage associées en une même initiative. Au lieu d’attribuer chaque stratégie individuellement, vous appliquez l’initiative.

L’exemple suivant montre comment créer une initiative pour gérer deux balises : `costCenter` et `productName`. Il utilise deux stratégies intégrées pour appliquer la valeur de balise par défaut.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>Étapes suivantes

- Consulter des exemples à la page [Exemples Azure Policy](../samples/index.md)
- Consulter la page [Compréhension des effets d’Azure Policy](effects.md)
- Savoir comment [créer des stratégies par programmation](../how-to/programmatically-create.md)
- Découvrir comment [obtenir des données de conformité](../how-to/getting-compliance-data.md)
- Découvrir comment [remédier à la non conformité des ressources](../how-to/remediate-resources.md)
- Pour en savoir plus sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../../management-groups/overview.md).
