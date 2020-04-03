---
title: Exemple de modèle - Créer une instance managée dans Azure SQL Database
description: Utilisez cet exemple de script Azure PowerShell pour créer une instance managée dans Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: seo-dt-2019
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: a349be9ada756742f5fd5ba4819caa1d2a2d3268
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373165"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>Utiliser PowerShell avec un modèle Azure Resource Manager pour créer une instance managée dans Azure SQL Database

Vous pouvez créer une instance managée Azure SQL Database à l’aide de la bibliothèque Azure PowerShell et des modèles Azure Resource Manager.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous avez besoin de la version 1.4.0 d’AZ PowerShell ou d’une version ultérieure pour suivre ce tutoriel. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

Les commandes Azure PowerShell peuvent démarrer le déploiement à l’aide du modèle Azure Resource Manager prédéfini. Vous pouvez spécifier les propriétés suivantes dans le modèle :

- Nom de l’instance
- Le nom d’utilisateur et le mot de passe administrateur SQL
- La taille de l’instance (nombre de cœurs et taille de stockage maximale)
- Le réseau virtuel et le sous-réseau où l’instance doit être placée
- Le classement de l’instance au niveau du serveur (en préversion)

Les éléments que vous ne pouvez pas modifier ultérieurement sont le nom de l’instance, le nom d’utilisateur de l’administrateur SQL, le réseau virtuel et le sous-réseau. Les autres propriétés d’instance peuvent être modifiées.

## <a name="prerequisites"></a>Prérequis

Cet exemple suppose que vous avez [créé un environnement réseau valide](../sql-database-managed-instance-create-vnet-subnet.md) ou [modifié un réseau virtuel existant](../sql-database-managed-instance-configure-vnet-subnet.md) pour votre instance gérée. L’exemple utilise les applets de commande [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) et [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork). Vous devez donc vérifier que vous avez installé les modules PowerShell suivants :

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager

Le contenu suivant doit être placé dans un fichier utilisé comme modèle pour créer l’instance :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen4",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen4",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

Il est supposé que vous disposez déjà d’un réseau virtuel Azure avec un sous-réseau correctement configuré. Si vous ne disposez pas d’un sous-réseau configuré correctement, préparez l’environnement réseau en utilisant un [modèle Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) distinct qui peut être exécuté indépendamment ou dans ce modèle.

Enregistrez le contenu de ce fichier au format .json, insérez le chemin du fichier dans le script PowerShell suivant, puis renommez les objets dans le script :

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed Instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy Instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

Une fois que le script a été exécuté avec succès, l’instance SQL Database est accessible à partir de tous les services Azure et l’adresse IP configurée.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts SQL Database PowerShell dans [Scripts PowerShell Azure SQL Database](../sql-database-powershell-samples.md).
