---
title: Structure de définition Azure Policy
description: Explique comment Azure Policy utilise une définition de stratégie de ressource afin d’établir des conventions pour les ressources de votre organisation en décrivant quand la stratégie est appliquée et la mesure à prendre.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 1937792290d973f3aee7fa3c0714f4667c21e79a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194646"
---
# <a name="azure-policy-definition-structure"></a>Structure de définition Azure Policy

Une définition de stratégie de ressource utilisée par Azure Policy vous permet d’établir des conventions pour les ressources de votre organisation en décrivant quand la stratégie est appliquée et la mesure à prendre. En définissant des conventions, vous pouvez contrôler les coûts et gérer plus facilement vos ressources. Par exemple, vous pouvez spécifier que seuls certains types de machines virtuelles sont autorisés. Vous pouvez aussi exiger que toutes les ressources soient marquées. Toutes les ressources enfants héritent des stratégies. Ainsi, si une stratégie est appliquée à un groupe de ressources, elle est applicable à toutes les ressources appartenant à ce groupe de ressources.

Le schéma utilisé par Azure Policy se trouve ici : [https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json](https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json)

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

Pour découvrir tous les exemples de modèles Azure Policy, consultez [Modèles pour Azure Policy](json-samples.md).

## <a name="mode"></a>Mode

Le **mode** détermine les types de ressources à évaluer pour une stratégie. Les modes pris en charge sont les suivants :

- `all` : évaluer les groupes de ressources et tous les types de ressources
- `indexed` : évaluer uniquement les types de ressources qui prennent en charge les balises et l’emplacement

Nous vous recommandons de définir **mode** sur `all` dans tous les cas. Toutes les définitions de stratégie créées via le portail utilisent le mode `all`. Si vous utilisez PowerShell ou Azure CLI, vous pouvez spécifier le paramètre **mode** manuellement. Si la définition de stratégie ne contient pas de valeur **mode**, sa valeur par défaut est `all` dans Azure PowerShell et `null` dans Azure CLI, ce qui équivaut à `indexed`, à des fins de compatibilité descendante.

`indexed` doit être utilisé lors de la création de stratégies qui appliqueront des balises ou des emplacements. Cela n’est pas nécessaire, mais empêche les ressources qui ne prennent pas en charge les balises et les emplacements de s’afficher comme des résultats non conformes dans les résultats de conformité. La seule exception est les **groupes de ressources**. Les stratégies qui tentent d’appliquer des emplacements ou des balises à un groupe de ressources doivent définir **mode** sur `all` et cibler spécifiquement le type `Microsoft.Resources/subscriptions/resourceGroup`. Pour exemple, consultez [Appliquer des balises au groupe de ressources](scripts/enforce-tag-rg.md).

## <a name="parameters"></a>parameters

Les paramètres permettent de simplifier la gestion des stratégies en réduisant le nombre de définitions de stratégies. Considérez les paramètres comme les champs d’un formulaire : `name`, `address`, `city`, `state`. Ces paramètres restent toujours les mêmes ; toutefois, leurs valeurs changent en fonction de la personne qui remplit le formulaire. Les paramètres fonctionnent de manière identique durant la création de stratégies. En incluant des paramètres dans une définition de stratégie, vous pouvez réutiliser cette stratégie pour différents scénarios avec des valeurs différentes.

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

Dans la propriété de métadonnées, vous pouvez utiliser **strongType** pour fournir une liste à choix multiple des options dans le portail Azure.  Les valeurs autorisées pour **strongType** incluent actuellement :

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

Dans la règle de stratégie, vous référencez des paramètres avec la syntaxe suivante :

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

Lorsque vous utilisez les conditions **j’aime** et **je n’aime pas**, vous pouvez utiliser un caractère générique (*) dans la valeur.

Lorsque vous utilisez les conditions **correspondance** et **non correspondance** entrez `#` pour représenter un chiffre, `?` pour une lettre et tout autre caractère pour représenter ce caractère réel. Pour obtenir des exemples, consultez [Autoriser plusieurs modèles de nom](scripts/allow-multiple-name-patterns.md).

### <a name="fields"></a>Champs

Les conditions sont formées à partir de champs. Un champ représente des propriétés dans la charge utile de la requête de ressource qui est utilisée pour décrire l'état de la ressource.  

Les champs suivants sont pris en charge :

- `name`
- `fullName`
  - Retourne le nom complet de la ressource, y compris les parents (par exemple « myServer/myDatabase »)
- `kind`
- `type`
- `location`
- `tags`
- `tags.tagName`
- `tags[tagName]`
  - Cette syntaxe en crochet prend en charge les noms de balise qui contiennent des points
- alias de propriété : pour en obtenir la liste, consultez [Alias](#aliases).

### <a name="alternative-accessors"></a>Autres accesseurs

**Champ** est l’accesseur principal utilisé dans les règles de stratégie. Il inspecte directement la ressource qui est évaluée. Toutefois, la stratégie prend en charge un autre accesseur, **source**.

```json
"source": "action",
"equals": "Microsoft.Compute/virtualMachines/write"
```

**Source** ne prend en charge qu’une seule valeur, **action**. Action retourne l’action d’autorisation de la requête en cours d’évaluation. Les actions d’autorisation sont exposées dans la section d’autorisation du [journal d’activité](../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

Quand la stratégie évalue les ressources existantes en arrière-plan, elle définit **action** sur une action d’autorisation `/write` sur le type de ressource.

### <a name="effect"></a>Résultat

La stratégie prend en charge les types d’effet suivants :

- **deny** : génère un événement dans le journal d’audit et fait échouer la requête.
- **audit** : génère un événement d’avertissement dans le journal d’audit, mais ne fait pas échouer la requête.
- **append** : ajoute l’ensemble de champs défini à la requête.
- **AuditIfNotExists** : active l’audit si une ressource n’existe pas.
- **DeployIfNotExists** : déploie une ressource si elle n’existe pas déjà. Actuellement, cet effet est uniquement pris en charge par le biais de stratégies intégrées.

Pour **append**, vous devez fournir les détails suivants :

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

La valeur peut être une chaîne ou un objet au format JSON.

Avec **AuditIfNotExists** et **DeployIfNotExists**, vous pouvez évaluer l’existence d’une ressource connexe et appliquer une règle et un effet correspondant quand cette ressource n’existe pas. Par exemple, vous pouvez exiger qu’un observateur réseau soit déployé pour tous les réseaux virtuels.
Pour obtenir un exemple d’audit quand une extension de machine virtuelle n’est pas déployée, consultez [Auditer si une extension n’existe pas](scripts/audit-ext-not-exist.md).

## <a name="aliases"></a>Alias

Les alias de propriété permettent d’accéder aux propriétés spécifiques d’un type de ressource. Les alias permettent de restreindre les valeurs ou les conditions autorisées pour la propriété d’une ressource. Chaque alias correspond aux chemins des différentes versions d’API d’un type de ressource donné. Lors de l’évaluation de la stratégie, le moteur de stratégie obtient le chemin de la propriété de cette version de l’API.

La liste des alias augmente toujours. Pour découvrir les alias actuellement pris en charge par Azure Policy, utilisez une des méthodes suivantes :

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzureRmAccount if not using Cloud Shell

  $azContext = Get-AzureRmContext
  $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
  $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
  $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
  $authHeader = @{
      'Content-Type'='application/json'
      'Authorization'='Bearer ' + $token.AccessToken
  }

  # Invoke the REST API
  $response = Invoke-RestMethod -Uri 'https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases' -Method Get -Headers $authHeader

  # Create an Array List to hold discovered aliases
  $aliases = New-Object System.Collections.ArrayList

  foreach ($ns in $response.value) {
      foreach ($rT in $ns.resourceTypes) {
          if ($rT.aliases) {
              foreach ($obj in $rT.aliases) {
                  $alias = [PSCustomObject]@{
                      Namespace       = $ns.namespace
                      resourceType    = $rT.resourceType
                      alias           = $obj.name
                  }
                  $aliases.Add($alias) | Out-Null
              }
          }
      }
  }

  # Output the list, sort, and format. You can customize with Where-Object to limit as desired.
  $aliases | Sort-Object -Property Namespace, resourceType, alias | Format-Table
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # Get Azure Policy aliases for a specific Namespace
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- API REST/ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

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

- Découvrez les exemples de modèles Azure Policy en consultant [Modèles pour Azure Policy](json-samples.md).