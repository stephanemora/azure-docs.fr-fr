---
title: Dépréciation des paramètres IP Config pour la cmdlet New-AzRecoveryServicesAsrVMNicConfig | Microsoft Docs
description: Détails sur la dépréciation des paramètres IP Config de la cmdlet New-AzRecoveryServicesAsrVMNicConfig et informations sur l’utilisation de la nouvelle cmdlet New-AzRecoveryServicesAsrVMNicIPConfig
services: site-recovery
author: rishjai-msft
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/30/2021
ms.author: rishjai
ms.openlocfilehash: 138dd9d576638cda52ca62e45cdb353920e9b00b
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111968547"
---
# <a name="deprecation-of-ip-config-parameters-for-the-cmdlet-new-azrecoveryservicesasrvmnicconfig"></a>Dépréciation des paramètres IP Config pour la cmdlet New-AzRecoveryServicesAsrVMNicConfig

Cet article décrit la dépréciation, les implications correspondantes et les options alternatives disponibles pour les clients dans le scénario suivant :

Configuration des paramètres « Primary IP Config » pour le basculement ou le test de basculement. 

Cette cmdlet a un impact sur tous les clients du scénario Reprise d’activité Azure sur Azure utilisant la cmdlet New-AzRecoveryServicesAsrVMNicConfig dans la version _Az Powershell 5.9.0 et ultérieure_.

> [!IMPORTANT]
> Les clients sont invités à prendre les mesures correctives au plus tôt pour éviter toute interruption dans leur environnement. 

## <a name="what-changes-should-you-expect"></a>À quelles modifications devez-vous vous attendre ?

La cmdlet New-AzRecoveryServicesAsrVMNicConfig utilise les paramètres suivants pour configurer les valeurs d’IP Config pour FO/TFO :
- RecoveryVMSubnetName
- RecoveryNicStaticIPAddress
- RecoveryPublicIPAddressId
- RecoveryLBBackendAddressPoolId
- TfoVMSubnetName
- TfoNicStaticIPAddress
- TfoPublicIPAddressId
- TfoLBBackendAddressPoolId

Ces paramètres ne seront plus acceptés par la cmdlet.

- À partir du 4 mai 2021, vous recevrez des notifications sur le portail Azure et des communications par e-mail concernant la dépréciation des paramètres IP Config dans la cmdlet New-AzRecoveryServicesAsrVMNicConfig.

- Si l’un de vos scripts l’utilise, il ne sera plus pris en charge.
 
## <a name="alternatives"></a>Autres solutions 

En guise d’alternative, une nouvelle cmdlet [New-AzRecoveryServicesAsrVMNicIPConfig](/powershell/module/az.recoveryservices/new-azrecoveryservicesasrvmnicipconfig) est introduite pour la configuration des paramètres IP Config FO/TFO. 


## <a name="remediation-steps"></a>Étapes de correction

Vous devez modifier vos scripts pour supprimer ces paramètres. Au lieu de cela, commencez à utiliser la nouvelle cmdlet **New-AzRecoveryServicesAsrVMNicIPConfig** pour créer un objet IP Config. Voici une illustration :

Vos **scripts existants** auraient été écrits comme ceci :
```azurepowershell
# Fetching the Protected Item Object (for the Protected VM)
$protectedItemObject = Get-AsrReplicationProtectedItem -ProtectionContainer $primaryContainerObject | where { $_.FriendlyName -eq $VMName };$protectedItemObject

# ID of the NIC whose settings are to be updated.
$nicId = $protectedItemObject.NicDetailsList[0].NicId

$nic1 = New-AzRecoveryServicesAsrVMNicConfig -NicId $nicId -ReplicationProtectedItem $protectedItemObject -RecoveryVMNetworkId <networkArmId> -TfoVMNetworkId <networkArmId> -RecoveryVMSubnetName "default" -TfoVMSubnetName "default" -RecoveryNicStaticIPAddress "10.1.40.223" -TfoNicStaticIPAddress "10.33.0.223"

$nics = @($nic1)
Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $protectedItemObject -ASRVMNicConfiguration $nics
```

Modifiez vos scripts comme **ci-dessous** :
```azurepowershell
# Fetching the Protected Item Object (for the Protected VM)
$protectedItemObject = Get-AsrReplicationProtectedItem -ProtectionContainer $primaryContainerObject | where { $_.FriendlyName -eq $VMName };$protectedItemObject

# Create the config object for Primary IP Config
$ipConfig = New-AzRecoveryServicesAsrVMNicIPConfig  -IpConfigName <ipConfigName> -RecoverySubnetName "default" -TfoSubnetName "default" -RecoveryStaticIPAddress "10.1.40.223" -TfoStaticIPAddress "10.33.0.223"

$ipConfigs = @($ipConfig)

# ID of the NIC whose settings are to be updated.
$nicId = $protectedItemObject.NicDetailsList[0].NicId

$nic1 = New-AzRecoveryServicesAsrVMNicConfig -NicId $nicId -ReplicationProtectedItem $protectedItemObject -RecoveryVMNetworkId <networkArmId> -TfoVMNetworkId <networkArmId> -IPConfig $ipConfigs

$nics = @($nic1)
Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $protectedItemObject -ASRVMNicConfiguration $nics
```

## <a name="next-steps"></a>Étapes suivantes
Modifiez vos scripts comme illustré ci-dessus. Si vous avez des questions à ce sujet, contactez Support Microsoft.