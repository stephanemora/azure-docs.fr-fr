---
title: Obtenir des notifications de maintenance de machines virtuelles Azure à l’aide de PowerShell
description: Affichez les notifications de maintenance pour les machines virtuelles s’exécutant dans Azure et commencez la maintenance en libre-service à l’aide de PowerShell.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: c23a06c502bd43b52cb35220b711084c7c8c1c7a
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121840"
---
# <a name="handling-planned-maintenance-using-powershell"></a>Gestion de la maintenance planifiée à l’aide de PowerShell

**Cet article s’applique aux machines virtuelles exécutant Linux et Windows.**

Vous pouvez utiliser Azure Powershell pour voir quand la [maintenance](maintenance-notifications.md) des machines virtuelles est planifiée. Les informations de maintenance planifiée sont disponibles à partir de la cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) lorsque vous utilisez le paramètre `-status`.
  
Les informations de maintenance sont retournées uniquement en cas de maintenance planifiée. Si aucune maintenance planifiée n’impacte la machine virtuelle, l’applet de commande ne retourne pas d’informations de maintenance. 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

Les propriétés suivantes sont retournées sous MaintenanceRedeployStatus : 

| Valeur | Description   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indique si vous pouvez démarrer la maintenance sur la machine virtuelle maintenant |
| PreMaintenanceWindowStartTime         | Début de la fenêtre de maintenance en libre-service lorsque vous pouvez lancer la maintenance sur votre machine virtuelle |
| PreMaintenanceWindowEndTime           | Fin de la fenêtre de maintenance en libre-service lorsque vous pouvez lancer la maintenance sur votre machine virtuelle |
| MaintenanceWindowStartTime            | Début de la maintenance planifiée pendant laquelle Azure lance la maintenance sur votre machine virtuelle |
| MaintenanceWindowEndTime              | Fin de la fenêtre de maintenance planifiée pendant laquelle Azure lance la maintenance sur votre machine virtuelle |
| LastOperationResultCode               | Résultat de la dernière tentative de lancement de la maintenance sur la machine virtuelle |



Vous pouvez également obtenir l'état de maintenance de toutes les machines virtuelles d'un groupe de ressources à l'aide de [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) sans spécifier de machine virtuelle.
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

L’exemple PowerShell suivant prend votre ID d’abonnement et retourne la liste des machines virtuelles dont la maintenance est planifiée.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Démarrer la maintenance sur votre machine virtuelle à l’aide de PowerShell

À l’aide des informations de la fonction de la section précédente, la commande suivante démarre la maintenance sur une machine virtuelle si **IsCustomerInitiatedMaintenanceAllowed** est défini sur true.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Déploiements Classic

Si vous avez encore des machines virtuelles qui ont été déployées à l’aide du modèle de déploiement Classic, vous pouvez utiliser PowerShell pour interroger ces machines virtuelles et démarrer la maintenance.

Pour obtenir l’état de la maintenance d’une machine virtuelle, tapez :

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Pour démarrer la maintenance sur votre machine virtuelle classique, tapez :

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également gérer la maintenance planifiée à l’aide d’[Azure CLI](maintenance-notifications-cli.md) ou du [portail](maintenance-notifications-portal.md).
