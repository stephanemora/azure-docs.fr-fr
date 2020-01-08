---
title: 'Azure Resource Manager : Créer une base de données unique'
description: Créez une base de données unique dans Azure SQL Database à l’aide du modèle Azure Resource Manager.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
ms.date: 06/28/2019
ms.openlocfilehash: bc4a573ed81657eb39c27c5f2df68d12daf4009f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351373"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Démarrage rapide : Créer une base de données unique dans Azure SQL Database à l’aide du modèle Azure Resource Manager

La création d’une [base de données unique](sql-database-single-database.md) est l’option de déploiement la plus rapide et la plus simple pour créer une base de données dans Azure SQL Database. Ce guide de démarrage rapide montre comment créer une base de données unique à l’aide du modèle Azure Resource Manager. Pour plus d’informations, consultez [Documentation Azure Resource Manager](/azure/azure-resource-manager/).

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Créer une base de données unique

Une base de données comprend un ensemble défini de ressources (capacité de calcul, mémoire, E/S et stockage) qui varient selon l’un des deux [modèles d’achat](sql-database-purchase-models.md) choisi. Quand vous créez une base de données unique, vous définissez également un [serveur SQL Database](sql-database-servers.md) pour la gérer et la placer dans le [groupe de ressources Azure](../azure-resource-manager/management/overview.md) d’une région spécifiée.

Le fichier JSON suivant est le modèle utilisé dans cet article. Le modèle est stocké dans [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/SQLServerAndDatabase/azuredeploy.json). Vous trouverez d’autres exemples de modèles Azure SQL Database dans [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

[!code-json[create-azure-sql-database-server-and-database](~/resourcemanager-templates/SQLServerAndDatabase/azuredeploy.json)]

Sélectionnez **Essayer** à partir du bloc de code PowerShell suivant pour ouvrir Azure Cloud Shell.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" -projectName $projectName -adminUser $adminUser -adminPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

az group create --location $location --name $resourceGroupName

az group deployment create -g $resourceGroupName --template-uri "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" `
    --parameters 'projectName=' + $projectName \
                 'adminUser=' + $adminUser \
                 'adminPassword=' + $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

* * *

## <a name="query-the-database"></a>Interroger la base de données

Pour interroger la base de données, consultez [Interroger la base de données](./sql-database-single-database-get-started.md#query-the-database).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Conservez ce groupe de ressources, ce serveur SQL et cette base de données unique pour accéder aux [étapes suivantes](#next-steps). Les étapes suivantes vous montrent comment vous connecter à votre base de données et comment l’interroger via différentes méthodes.

Pour supprimer le groupe de ressources :

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

* * *

## <a name="next-steps"></a>Étapes suivantes

- Créez une règle de pare-feu au niveau du serveur pour vous connecter à la base de données unique à l’aide d’outils locaux ou à distance. Pour plus d’informations, consultez [Créer une règle de pare-feu au niveau du serveur](sql-database-server-level-firewall-rule.md).
- Après avoir créé une règle de pare-feu au niveau du serveur, [connectez et interrogez](sql-database-connect-query.md) votre base de données à l’aide de plusieurs outils et langues différents.
  - [Se connecter et effectuer des requêtes à l’aide de SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Se connecter et effectuer des requêtes à l’aide d’Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Pour créer une base de données unique à l’aide d’Azure CLI, consultez [Exemples d’interface de ligne de commande Azure](sql-database-cli-samples.md).
- Pour créer une base de données unique à l’aide d’Azure PowerShell, consultez [Exemples Azure PowerShell](sql-database-powershell-samples.md).
