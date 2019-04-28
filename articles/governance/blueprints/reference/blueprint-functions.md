---
title: Fonctions de plans Azure
description: Décrit les fonctions pour une utilisation avec les affectations et les définitions de plans d’Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/15/2019
ms.topic: reference
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 0de3e0add804290cdfe27e2e97d8b1a0f240e0a6
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769300"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Fonctions à utiliser avec des plans d’Azure

Plans Azure fournit des fonctions une définition de plan plus dynamique. Ces fonctions peuvent être utilisées avec les définitions de plan et artefacts de plan. Un artefact de modèle Resource Manager prend en charge l’utilisation complète de fonctions de Resource Manager en plus de l’obtention d’une valeur dynamique via un paramètre de plan.

Les fonctions suivantes sont prises en charge :

- [artifacts](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [abonnement](#subscription)

## <a name="artifacts"></a>Artefacts

`artifacts(artifactName)`

Retourne qu'un objet de propriétés rempli avec les artefacts de ce plan sorties.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| artifactName |Oui |string |Le nom d’un artefact de plan. |

### <a name="return-value"></a>Valeur de retour

Un objet de propriétés de sortie. Les propriétés de sortie dépendent du type d’artefact de plan référencé. Tous les types de suivent le format :

```json
{
  "output": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Artefact d’attribution de stratégie

```json
{
    "output": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Artefact de modèle Resource Manager

Le **sortie** propriétés de l’objet retourné sont définies dans le modèle Resource Manager et retournées par le déploiement.

#### <a name="role-assignment-artifact"></a>Artefact d’attribution de rôle

```json
{
    "output": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>Exemples

Un artefact de modèle Resource Manager avec l’ID _myTemplateArtifact_ propriété de sortie contenant l’exemple suivant :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

Quelques exemples de récupération de données à partir de la _myTemplateArtifact_ exemple sont :

| Expression | Type | Valeur |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").output.myArray]` | Tableau | \["first", "second"\] |
|`[artifacts("myTemplateArtifact").output.myArray[0]]` | String | « première » |
|`[artifacts("myTemplateArtifact").output.myString]` | String | « Mon valeur chaîne » |
|`[artifacts("myTemplateArtifact").output.myObject]` | Object | { "myproperty": "my value", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").output.myObject.myProperty]` | String | « Mon valeur » |
|`[artifacts("myTemplateArtifact").output.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Combine plusieurs valeurs de chaîne et renvoie la chaîne concaténée.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| string1 |Oui |string |La première valeur pour la concaténation. |
| arguments supplémentaires |Non  |string |Valeurs supplémentaires dans un ordre séquentiel pour la concaténation |

### <a name="return-value"></a>Valeur de retour

Une chaîne de valeurs concaténées.

### <a name="remarks"></a>Remarques

La fonction de la solution Azure Blueprint diffère de la fonction de modèle Azure Resource Manager, car il fonctionne uniquement avec des chaînes.

### <a name="example"></a>Exemples

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Retourne une valeur de paramètre de plan. Le nom de paramètre spécifié doit être défini dans la définition de plan ou dans les artefacts de plan.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| nom_paramètre |Oui |string |Nom du paramètre à retourner. |

### <a name="return-value"></a>Valeur de retour

La valeur du paramètre d’artefact de plan ou plan spécifié.

### <a name="remarks"></a>Remarques

La fonction de la solution Azure Blueprint diffère de la fonction de modèle Azure Resource Manager il fonctionne uniquement avec les paramètres de plan.

### <a name="example"></a>Exemples

Définir le paramètre _principalIds_ dans la définition de plan :

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the RBAC assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Utilisez ensuite _principalIds_ comme argument pour `parameters()` dans un artefact de plan :

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Renvoie un objet qui représente le groupe de ressources actuel.

### <a name="return-value"></a>Valeur de retour

L’objet renvoyé présente le format suivant :

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Remarques

La fonction de la solution Azure Blueprint diffère de la fonction de modèle Azure Resource Manager. Le `resourceGroup()` fonction ne peut pas être utilisée dans un artefact de niveau d’abonnement ou de la définition de plan. Il peut être utilisé uniquement dans les artefacts de plan qui font partie d’un objet de groupe de ressources.

Une utilisation courante de la `resourceGroup()` fonction consiste à créer des ressources dans le même emplacement que l’objet de groupe de ressources.

### <a name="example"></a>Exemples

Pour utiliser l’emplacement du groupe de ressources, la valeur dans la définition de plan ou lors de l’affectation, comme l’emplacement d’un autre artefact, déclarez un objet espace réservé de groupe de ressources dans votre définition de plan. Dans cet exemple, _NetworkingPlaceholder_ est le nom de l’espace réservé groupe de ressources.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Utilisez ensuite le `resourceGroup()` fonction dans le contexte d’un artefact de plan qui vise à un objet espace réservé de groupe de ressources. Dans cet exemple, l’artefact de modèle est déployé dans le _NetworkingPlaceholder_ groupe de ressources et fournit le paramètre _resourceLocation_ remplis dynamiquement avec la  _NetworkingPlaceholder_ emplacement du groupe de ressources pour le modèle. L’emplacement de la _NetworkingPlaceholder_ groupe de ressources ont pu été défini de manière statique sur la définition de plan ou défini dynamiquement lors de l’attribution. Dans les deux cas, l’artefact de modèle est fourni ces informations en tant que paramètre et l’utilise pour déployer les ressources dans l’emplacement approprié.

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

Retourne un objet qui représente l’objet de groupe de ressources spécifié. Contrairement à `resourceGroup()`, ce qui nécessite le contexte de l’artefact, cette fonction est utilisée pour obtenir les propriétés d’un espace réservé de groupe ressource spécifique lorsque dans le contexte de ce groupe de ressources.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| placeholderName |Oui |string |Le nom de l’espace réservé de l’artefact de groupe de ressources à retourner. |

### <a name="return-value"></a>Valeur de retour

L’objet renvoyé présente le format suivant :

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Exemples

Pour utiliser l’emplacement du groupe de ressources, la valeur dans la définition de plan ou lors de l’affectation, comme l’emplacement d’un autre artefact, déclarez un objet espace réservé de groupe de ressources dans votre définition de plan. Dans cet exemple, _NetworkingPlaceholder_ est le nom de l’espace réservé groupe de ressources.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Utilisez ensuite le `resourceGroups()` (fonction) à partir du contexte d’un artefact de plan pour obtenir une référence à l’objet espace réservé de groupe de ressources. Dans cet exemple, l’artefact de modèle est déployé en dehors de la _NetworkingPlaceholder_ groupe de ressources et fournit le paramètre _artifactLocation_ remplis dynamiquement avec la  _NetworkingPlaceholder_ emplacement du groupe de ressources pour le modèle. L’emplacement de la _NetworkingPlaceholder_ groupe de ressources ont pu été défini de manière statique sur la définition de plan ou défini dynamiquement lors de l’attribution. Dans les deux cas, l’artefact de modèle est fourni ces informations en tant que paramètre et l’utilise pour déployer les ressources dans l’emplacement approprié.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>subscription

`subscription()`

Retourne des détails concernant l’abonnement pour l’affectation de plan actuel.

### <a name="return-value"></a>Valeur de retour

L’objet renvoyé présente le format suivant :

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Exemples

Utiliser le nom d’affichage de l’abonnement et le `concat()` fonction permettant de créer une convention d’affectation de noms passée comme paramètre _resourceName_ à l’artefact de modèle.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>Étapes suivantes

- Découvrir le [cycle de vie d’un blueprint](../concepts/lifecycle.md).
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../how-to/update-existing-assignments.md).
- Résoudre les problèmes durant l’affectation d’un blueprint en suivant les étapes de [dépannage général](../troubleshoot/general.md).