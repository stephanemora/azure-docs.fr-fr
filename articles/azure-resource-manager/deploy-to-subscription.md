---
title: Déployer des ressources sur un abonnement Azure | Microsoft Docs
description: Décrit comment créer un modèle Azure Resource Manager qui déploie des ressources au niveau de l’abonnement.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: tomfitz
ms.openlocfilehash: 766534bfa02146e894916e2f9c953ef631913764
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40024677"
---
# <a name="deploy-resources-to-an-azure-subscription"></a>Déployer des ressources sur un abonnement Azure

En règle générale, vous déployez des ressources sur un groupe de ressources dans votre abonnement Azure. Toutefois, certaines ressources peuvent être déployées au niveau de votre abonnement Azure. Ces ressources s’appliquent à votre abonnement. Les [stratégies](../azure-policy/azure-policy-introduction.md), le [contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md) et [Azure Security Center](../security-center/security-center-intro.md) sont des services que vous souhaiterez peut-être appliquer au niveau de l’abonnement plutôt qu’au niveau du groupe de ressources.

Cet article utilise Azure CLI pour déployer les modèles. Actuellement, PowerShell ne prend en charge le déploiement d’un modèle sur un abonnement.

## <a name="assign-policy"></a>Attribuer la stratégie

L’exemple suivant assigne une définition de stratégie existante à l’abonnement. Si la stratégie utilise des paramètres, fournissez-les en tant qu’objet. Si la stratégie n’utilise pas de paramètres, utilisez l’objet vide par défaut.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "policyDefinitionID": {
            "type": "string"
        },
        "policyName": {
            "type": "string"
        },
        "policyParameters": {
            "type": "object",
            "defaultValue": {}
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "[parameters('policyName')]",
            "apiVersion": "2018-03-01",
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[parameters('policyDefinitionID')]",
                "parameters": "[parameters('policyParameters')]"
            }
        }
    ]
}
```

Pour appliquer une stratégie intégrée à votre abonnement Azure, utilisez les commandes Azure CLI suivantes. Dans cet exemple, la stratégie n’a pas de paramètres

```azurecli-interactive
# Built-in policy that does not accept parameters
definition=$(az policy definition list --query "[?displayName=='Audit resource location matches resource group location'].id" --output tsv)

az deployment create \
  -n policyassign \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=auditRGLocation
```

Pour appliquer une stratégie intégrée à votre abonnement Azure, utilisez les commandes Azure CLI suivantes. Dans cet exemple, la stratégie a des paramètres.

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  -n policyassign \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

## <a name="define-and-assign-policy"></a>Définir et assigner une stratégie

Vous pouvez [définir](../azure-policy/policy-definition.md) et assigner une stratégie dans le même modèle.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "locationpolicy",
            "apiVersion": "2018-03-01",
            "properties": {
                "policyType": "Custom",
                "parameters": {},
                "policyRule": {
                    "if": {
                        "field": "location",
                        "equals": "northeurope"
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2018-03-01",
            "dependsOn": [
                "locationpolicy"
            ],
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
            }
        }
    ]
}
```

Pour créer la définition de stratégie dans votre abonnement et l’appliquer à l’abonnement, utilisez la commande CLI suivante.

```azurecli-interactive
az deployment create \
  -n definePolicy \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="assign-role"></a>Affecter le rôle

L’exemple suivant affecte un rôle à un utilisateur ou un groupe.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string"
        },
        "roleDefinitionId": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "[guid(parameters('principalId'), deployment().name)]",
            "apiVersion": "2017-05-01",
            "properties": {
                "roleDefinitionId": "[resourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Pour affecter un groupe Active Directory à un rôle pour votre abonnement, utilisez les commandes Azure CLI suivantes.

```azurecli-interactive
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group tomfitzexample --query objectId --output tsv)

az deployment create \
  -n demoRole \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json \
  --parameters principalId=$principalid roleDefinitionId=$role
```

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir un exemple de déploiement des paramètres d’espace de travail pour Azure Security Center, consultez [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Pour en savoir plus sur la création de modèles Azure Resource Manager, consultez [Création de modèles](resource-group-authoring-templates.md). 
* Pour obtenir la liste des fonctions disponibles dans un modèle, consultez [Fonctions de modèle](resource-group-template-functions.md).

