---
title: Exemple - Auditer le chiffrement transparent des données de SQL Database
description: Cet exemple de définition de stratégie vérifie si le chiffrement transparent des données n’est pas activé pour les bases de données SQL.
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: e8ee800ff9f286f901a84a039e3c433442ae11b2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789576"
---
# <a name="sample---audit-sql-database-encryption"></a>Exemple - Audit du chiffrement des bases de données SQL

Cette stratégie intégrée vérifie si la base de données SQL n’a pas de chiffrement transparent des données activé.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Exemple de modèle

[!code-json[main](../../../../policy-templates/samples/SQL/audit-sql-db-tde-status/azurepolicy.json "Audit TDE for SQL Database")]

Vous pouvez déployer ce modèle en utilisant le [portail Azure](#deploy-with-the-portal), avec [PowerShell](#deploy-with-powershell) ou avec [Azure CLI](#deploy-with-azure-cli). Pour obtenir la stratégie intégrée, utilisez l’ID `17k78e20-9358-41c9-923c-fb736d382a12`.

## <a name="deploy-with-the-portal"></a>Déployer avec le portail

Lorsque vous assignez une stratégie, sélectionnez **État du chiffrement transparent des données de l’audit** dans les définitions intégrées disponibles.

## <a name="deploy-with-powershell"></a>Déployer avec PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/17k78e20-9358-41c9-923c-fb736d382a12

New-AzPolicyAssignment -name "SQL TDE Audit" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Nettoyer un déploiement PowerShell

Exécutez la commande suivante pour supprimer l’attribution de stratégie.

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name "SQL TDE Audit" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Déploiement avec l’interface de ligne de commande Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "SQL TDE Audit" --policy 17k78e20-9358-41c9-923c-fb736d382a12
```

### <a name="clean-up-azure-cli-deployment"></a>Nettoyer un déploiement Azure CLI

Exécutez la commande suivante pour supprimer l’attribution de stratégie.

```azurecli-interactive
az policy assignment delete --name "SQL TDE Audit" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

- Consulter d’autres exemples dans [Exemples Azure Policy](index.md)