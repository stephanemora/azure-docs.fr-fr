---
title: Exemples Azure PowerShell - Mettre à jour un service cloud Azure (support étendu)
description: Exemples de scripts pour la mise à jour de déploiements de service cloud Azure (support étendu)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 22d02ef7c395ae6f7cdbbf739ed5107436bd169c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751099"
---
# <a name="update-an-azure-cloud-service-extended-support"></a>Mettre à jour un service cloud Azure (support étendu)

Ces exemples couvrent différentes façons de mettre à jour un déploiement de service cloud Azure (support étendu) existant.

## <a name="add-an-extension-to-existing-cloud-service"></a>Ajouter une extension à un service cloud existant
L’ensemble de commandes ci-dessous ajoute une extension RDP au service cloud existant nommé ContosoCS qui appartient au groupe de ressources nommé ContosOrg.
```powershell
# Create RDP extension object
$rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name "RDPExtension" -Credential $credential -Expiration $expiration -TypeHandlerVersion "1.2.1"
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Add RDP extension to existing cloud service extension object
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension + $rdpExtension
# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="remove-all-extensions-from-a-cloud-service"></a>Supprimer toutes les extensions d’un service cloud
L’ensemble de commandes ci-dessous supprime toutes les extensions du service cloud existant nommé ContosoCS qui appartient au groupe de ressources nommé ContosOrg.
```powershell
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Set extension to empty list
$cloudService.ExtensionProfile.Extension = @()
# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="remove-the-remote-desktop-extension-from-cloud-service"></a>Supprimer l’extension Bureau à distance d’un service cloud
L’ensemble de commandes ci-dessous supprime l’extension RDP du service cloud existant nommé ContosoCS qui appartient au groupe de ressources nommé ContosOrg.
```powershell
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Remove extension by name RDPExtension
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension | Where-Object { $_.Name -ne "RDPExtension" }
# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="scale-out--scale-in-role-instances"></a>Effectuer un scale-out/scale-in d’instances de rôle
L’ensemble de commandes ci-dessous montre comment effectuer un scale-out et un scale-in du nombre d'instances de rôles pour le service cloud nommé ContosoCS qui appartient au groupe de ressources nommé ContosOrg.
```powershell
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Scale-out all role instance count by 1
$cloudService.RoleProfile.Role | ForEach-Object {$_.SkuCapacity += 1}

# Scale-in ContosoFrontend role instance count by 1
$role = $cloudService.RoleProfile.Role | Where-Object {$_.Name -eq "ContosoFrontend"}
$role.SkuCapacity -= 1

# Update cloud service configuration as per the new role instance count
$cloudService.Configuration = $configuration

# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur Azure Cloud Services (support étendu), consultez [Vue d’ensemble d’Azure Cloud Services (support étendu)](overview.md).
- Visitez le [dépôt d’exemples de Cloud Services (support étendu)](https://github.com/Azure-Samples/cloud-services-extended-support).
