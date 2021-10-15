---
title: Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel à l’aide d’Azure PowerShell
description: Découvrez comment joindre un runtime d’intégration Azure-SSIS à un réseau virtuel Azure à l’aide d’Azure PowerShell.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b9d264bf4e52ec79a70d6efcd326f395685d12b2
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403171"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network-with-azure-powershell"></a>Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel à l’aide d’Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="define-the-variables"></a>Définir les variables

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

## <a name="configure-a-virtual-network"></a>Configurer un réseau virtuel

Avant de joindre votre runtime d’intégration Azure-SSIS à un réseau virtuel, vous devez configurer ce dernier. Pour configurer automatiquement les paramètres et les autorisations du réseau virtuel pour le runtime d’intégration Azure-SSIS IR à joindre au réseau virtuel, ajoutez le script suivant :

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

## <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Créer un runtime d’intégration Azure-SSIS et le joindre à un réseau virtuel

Vous pouvez créer un runtime d’intégration Azure-SSIS et le joindre à un réseau virtuel en même temps. Pour obtenir le script complet et toutes les instructions, consultez [Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime-powershell.md).

## <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel

L’article [Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md) explique comment créer un runtime d’intégration Azure-SSIS IR et le joindre à un réseau virtuel dans le même script. Si vous disposez d’un runtime d’intégration Azure-SSIS IR, effectuez les opérations suivantes pour le joindre au réseau virtuel : 
1. Arrêtez le runtime d’intégration Azure-SSIS. 
1. Configurez le runtime d’intégration Azure-SSIS pour le joindre au réseau virtuel. 
1. Démarrez le runtime d’intégration Azure-SSIS. 

## <a name="stop-the-azure-ssis-ir"></a>Arrêter le runtime d’intégration Azure-SSIS

Vous devez arrêter le runtime d’intégration Azure-SSIS IR avant de le joindre à un réseau virtuel. Cette commande libère tous ses nœuds et arrête la facturation :

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

## <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Configurer les paramètres du réseau virtuel pour le runtime d’intégration Azure-SSIS à joindre

Pour configurer les paramètres du réseau virtuel auquel le runtime d’intégration Azure-SSIS va se joindre, utilisez ce script : 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

## <a name="configure-the-azure-ssis-ir"></a>Configurer le runtime d’intégration Azure-SSIS

Pour joindre votre Azure-SSIS IR à un réseau virtuel, exécutez la commande `Set-AzDataFactoryV2IntegrationRuntime` : 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

## <a name="start-the-azure-ssis-ir"></a>Démarrer le runtime d’intégration Azure-SSIS

Pour démarrer le runtime d’intégration Azure-SSIS IR, exécutez la commande suivante : 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

Cette commande prend de 20 à 30 minutes.

## <a name="next-steps"></a>Étapes suivantes
- [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel – Présentation](join-azure-ssis-integration-runtime-virtual-network.md)
- [Détails de la configuration de réseau virtuel pour un runtime d’intégration Azure-SSIS](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel avec l’interface utilisateur d’Azure Data Factory studio](join-azure-ssis-integration-runtime-virtual-network-ui.md)

Pour plus d’informations sur le runtime d’intégration Azure-SSIS IR, voir les articles suivants : 
- [Runtime d’intégration Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Cet article fournit des informations conceptuelles générales sur les runtimes d’intégration, notamment sur Azure-SSIS IR. 
- [Tutoriel : Déployer des packages SSIS vers Azure](./tutorial-deploy-ssis-packages-azure.md). Ce didacticiel fournit des instructions pas à pas pour créer votre Azure-SSIS IR. Il utilise Azure SQL Database pour héberger le catalogue SSIS. 
- [Créez un runtime d’intégration Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Cet article s’appuie sur le didacticiel. Il fournit des instructions sur l’utilisation d’Azure SQL Database avec des points de terminaison de service de réseau virtuel ou SQL Managed Instance dans un réseau virtuel pour héberger le catalogue SSIS. Il explique comment joindre votre runtime d’intégration Azure-SSIS IR à un réseau virtuel. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article vous montre comment obtenir des informations sur votre runtime d’intégration Azure-SSIS IR. Il fournit des descriptions sur l’état des informations renvoyées. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer votre Azure-SSIS IR. Il vous montre également comment effectuer un scale-out du runtime d’intégration Azure-SSIS en lui ajoutant des nœuds.
