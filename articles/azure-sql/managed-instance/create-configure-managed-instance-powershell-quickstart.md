---
title: Créer une instance Azure SQL Managed Instance - Démarrage rapide
description: Créez une instance Azure SQL Managed Instance à l’aide d’Azure PowerShell.
services: sql-managed-instance
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: contperf-fy21q1, devx-track-azurecli, devx-track-azurepowershell
ms.devlang: ''
ms.topic: quickstart
author: MashaMSFT
ms.author: mathoma
ms.reviewer: ''
ms.date: 06/25/2021
ms.openlocfilehash: 6697cb9b89d3cb960f2def9ff4dcf478080b7b07
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113220983"
---
# <a name="quickstart-create-a-managed-instance-using-azure-powershell"></a>Démarrage rapide : Créer une instance gérée à l’aide d’Azure PowerShell

Dans ce démarrage rapide, apprenez à créer une instance [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) à l’aide d’Azure PowerShell. 


## <a name="prerequisite"></a>Configuration requise

- Un abonnement Azure actif. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/).
- La version la plus récente [d’Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="set-variables"></a>Définition des variables

La création d’une instance SQL Managed Instance nécessite la création de plusieurs ressources dans Azure, et dès lors, les commandes Azure PowerShell s’appuient sur des variables pour simplifier l’expérience. Définissez les variables, puis exécutez les cmdlets dans chaque section au sein de la même session PowerShell. 

```azurepowershell-interactive
$NSnetworkModels = "Microsoft.Azure.Commands.Network.Models"
$NScollections = "System.Collections.Generic"
# The SubscriptionId in which to create these objects
$SubscriptionId = ''
# Set the resource group name and location for your managed instance
$resourceGroupName = "myResourceGroup-$(Get-Random)"
$location = "eastus2"
# Set the networking values for your managed instance
$vNetName = "myVnet-$(Get-Random)"
$vNetAddressPrefix = "10.0.0.0/16"
$miSubnetName = "myMISubnet-$(Get-Random)"
$miSubnetAddressPrefix = "10.0.0.0/24"
#Set the managed instance name for the new managed instance
$instanceName = "myMIName-$(Get-Random)"
# Set the admin login and password for your managed instance
$miAdminSqlLogin = "SqlAdmin"
$miAdminSqlPassword = "ChangeYourAdminPassword1"
# Set the managed instance service tier, compute level, and license mode
$edition = "General Purpose"
$vCores = 4
$maxStorage = 128
$computeGeneration = "Gen5"
$license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server licence that can be used for AHB discount
```

## <a name="create-resource-group"></a>Créer un groupe de ressources 

Dans un premier temps, connectez-vous à Azure, définissez votre contexte d’abonnement et créez votre groupe de ressources. 

Pour ce faire, exécutez ce script PowerShell : 

```azurepowershell-interactive

## Connect to Azure
Connect-AzAccount

# Set subscription context
Set-AzContext -SubscriptionId $SubscriptionId 

# Create a resource group
$resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
```

## <a name="configure-networking"></a>Configurer la mise en réseau

Une fois votre groupe de ressources créé, configurez les ressources réseau, telles que le réseau virtuel, les sous-réseaux, le groupe de sécurité réseau ainsi que la table de routage. Cet exemple illustre l’utilisation du script **Sous-réseau délégué pour le déploiement de Managed Instance**, disponible sur GitHub en tant que [delegate-subnet.ps1](https://github.com/microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet).

Pour ce faire, exécutez ce script PowerShell : 

```azurepowershell-interactive

# Configure virtual network, subnets, network security group, and routing table
$virtualNetwork = New-AzVirtualNetwork `
                      -ResourceGroupName $resourceGroupName `
                      -Location $location `
                      -Name $vNetName `
                      -AddressPrefix $vNetAddressPrefix

                  Add-AzVirtualNetworkSubnetConfig `
                      -Name $miSubnetName `
                      -VirtualNetwork $virtualNetwork `
                      -AddressPrefix $miSubnetAddressPrefix |
                  Set-AzVirtualNetwork
                  
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = $SubscriptionId
    resourceGroupName = $resourceGroupName
    virtualNetworkName = $vNetName
    subnetName = $miSubnetName
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters

$virtualNetwork = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $resourceGroupName
$miSubnet = Get-AzVirtualNetworkSubnetConfig -Name $miSubnetName -VirtualNetwork $virtualNetwork
$miSubnetConfigId = $miSubnet.Id
```

## <a name="create-managed-instance"></a>Créer une instance gérée 

Pour plus de sécurité, créez un mot de passe complexe et aléatoire pour vos informations d’identification SQL Managed Instance : 

```azurepowershell-interactive
# Create credentials
$secpassword = ConvertTo-SecureString $miAdminSqlPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential ($miAdminSqlLogin, $secpassword)
```

Créez ensuite votre instance SQL Managed Instance : 

```azurepowershell-interactive
# Create managed instance
New-AzSqlInstance -Name $instanceName `
                      -ResourceGroupName $resourceGroupName -Location $location -SubnetId $miSubnetConfigId `
                      -AdministratorCredential $credential `
                      -StorageSizeInGB $maxStorage -VCore $vCores -Edition $edition `
                      -ComputeGeneration $computeGeneration -LicenseType $license
```

Cette opération peut prendre un certain temps. Pour plus d’informations, consultez [Opérations de gestion](management-operations-overview.md).


## <a name="clean-up-resources"></a>Nettoyer les ressources

Conservez le groupe de ressources et l’instance gérée pour les étapes suivantes et découvrez comment vous connecter à votre instance SQL Managed Instance à l’aide d’une machine virtuelle cliente. 

Lorsque vous avez terminé d’utiliser ces ressources, vous pouvez supprimer le groupe de ressources que vous avez créé, ce qui supprimera également le serveur et la base de données unique qu’il contient.

```azurepowershell-interactive
# Clean up deployment 
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```


## <a name="next-steps"></a>Étapes suivantes

Une fois votre instance SQL Managed Instance créée, déployez une machine virtuelle cliente pour vous connecter à votre instance SQL Managed Instance et restaurer un exemple de base de données. 

> [!div class="nextstepaction"]
> [Créer une machine virtuelle cliente](connect-vm-instance-configure.md)
> [Restaurer une base de données](restore-sample-database-quickstart.md)


