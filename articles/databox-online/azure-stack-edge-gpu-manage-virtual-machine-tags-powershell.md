---
title: Gérer les étiquettes de machines virtuelles sur un appareil GPU Azure Stack Edge Pro via Azure PowerShell
description: Décrit comment créer et gérer des étiquettes de machines virtuelles pour des machines virtuelles s’exécutant dans Azure Stack Edge à l’aide d’Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: 566ca1598857f67e25faea9f19d134c4a91bfb18
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106555928"
---
# <a name="manage-vm-tags-on-azure-stack-edge-via-azure-powershell"></a>Gérer les étiquettes de machines virtuelles sur Azure Stack Edge via Azure PowerShell

Cet article explique comment étiqueter des machines virtuelles s’exécutant sur vos appareils GPU Azure Stack Edge Pro à l’aide d’Azure PowerShell.

## <a name="about-tags"></a>À propos des étiquettes

Les étiquettes sont des paires clé-valeur définies par l’utilisateur qui peuvent être attribuées à une ressource ou à un groupe de ressources. Vous pouvez appliquer des étiquettes à des machines virtuelles s’exécutant sur votre appareil pour les organiser de façon logique dans une taxonomie. Vous pouvez placer les étiquettes sur une ressource au moment de la création ou les ajouter à une ressource existante. Par exemple, vous pouvez appliquer le nom `Organization` et la valeur `Engineering` à toutes les machines virtuelles utilisées par le service d’ingénierie de votre organisation.

Pour plus d’informations sur les étiquettes, consultez [Gérer les étiquettes via AzureRM PowerShell](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true).

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir déployer une machine virtuelle sur votre appareil via PowerShell, vérifiez les éléments suivants :

- Vous avez accès à un client que vous utiliserez pour vous connecter à votre appareil.
    - Votre client exécute un [système d’exploitation pris en charge](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).
    - Votre client est configuré pour se connecter à l’instance Azure Resource Manager locale de votre appareil, conformément aux instructions fournies dans [Se connecter à Azure Resource Manager pour votre appareil](azure-stack-edge-gpu-connect-resource-manager.md).


## <a name="verify-connection-to-local-azure-resource-manager"></a>Vérifier la connexion à l’instance Azure Resource Manager locale

Assurez-vous que les étapes suivantes peuvent être utilisées pour accéder à l’appareil à partir de votre client.

Vérifiez que votre client peut se connecter à l’instance Azure Resource Manager locale. 

1. Appelez les API de l’appareil local pour l’authentification :

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

1. Indiquez le nom d’utilisateur `EdgeArmUser` et le mot de passe pour vous connecter via Azure Resource Manager. Si vous avez oublié le mot de passe, consultez [Réinitialiser le mot de passe pour Azure Resource Manager](azure-stack-edge-gpu-set-azure-resource-manager-password.md) et utilisez le nouveau mot de passe pour vous connecter.


## <a name="add-a-tag-to-a-vm"></a>Ajouter une étiquette à une machine virtuelle

1. Définissez certains paramètres.

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
    $TagValue = <Tag Value>   
    ```

    Voici un exemple de sortie :

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```

2. Obtenez l’objet de machine virtuelle et ses étiquettes.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $tags = $VirtualMachine.Tags
   ```

3. Ajoutez l’étiquette et mettez à jour la machine virtuelle. La mise à jour de la machine virtuelle peut prendre plusieurs minutes.

    Vous pouvez utiliser l’indicateur **-Force** facultatif pour exécuter la commande sans confirmation de l’utilisateur.

    ```powershell
    $tags.Add($TagName, $TagValue)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```

    Voici un exemple de sortie :

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $tags = $VirtualMachine.Tags
    PS C:\WINDOWS\system32> $tags.Add($TagName, $TagValue)
    PS C:\WINDOWS\system32> Set-AzureRmResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    
    Name              : myasetestvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
                        lMachines/myasetestvm1
    ResourceName      : myasetestvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg2
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {Organization}
    Properties        : @{vmId=958c0baa-e143-4d8a-82bd-9c6b1ba45e86; hardwareProfile=; storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    
    PS C:\WINDOWS\system32>
    ```

Pour plus d’informations, consultez [Add-AzureRMTag](/powershell/module/azurerm.tags/remove-azurermtag?view=azurermps-6.13.0&preserve-view=true).

## <a name="view-tags-of-a-vm"></a>Afficher les étiquettes d’une machine virtuelle

Vous pouvez afficher les étiquettes appliquées à une machine virtuelle spécifique s’exécutant sur votre appareil. 

1. Définissez les paramètres associés à la machine virtuelle dont vous voulez afficher les étiquettes.

   ```powershell
   $VMName = <VM Name>
   $VMRG = <VM Resource Group>
   ```
    Voici un exemple de sortie :

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```
1. Obtenez l’objet de machine virtuelle et affichez ses étiquettes.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $VirtualMachine.Tags
   ```
    Voici un exemple de sortie :

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $VirtualMachine

    ResourceGroupName : myaserg2
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
    lMachines/myasetestvm1
    VmId              : 958c0baa-e143-4d8a-82bd-9c6b1ba45e86
    Name              : myasetestvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Sales"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
    
    PS C:\WINDOWS\system32>
    ```
## <a name="view-tags-for-all-resources"></a>Afficher les étiquettes pour toutes les ressources

Pour afficher la liste actuelle des étiquettes pour toutes les ressources de l’abonnement Azure Resource Manager local (différent de votre abonnement Azure) de votre appareil, utilisez la commande `Get-AzureRMTag`.


Voici un exemple de sortie lorsque plusieurs machines virtuelles s’exécutent sur votre appareil et que vous souhaitez afficher toutes les étiquettes de toutes les machines virtuelles.

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag

Name         Count
----         -----
Organization 3

PS C:\WINDOWS\system32>
```

La sortie précédente indique qu’il existe trois étiquettes `Organization` sur les machines virtuelles s’exécutant sur votre appareil.

Pour afficher des détails supplémentaires, utilisez le paramètre `-Detailed`.

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag -Detailed |fl

Name        : Organization
ValuesTable :
              Name         Count
              ===========  =====
              Engineering  2
              Sales        1

Count       : 3
Values      : {Engineering, Sales}

PS C:\WINDOWS\system32>
```

La sortie précédente indique que parmi les trois étiquettes, 2 machines virtuelles sont étiquetées comme `Engineering` et 1 est étiquetée comme appartenant à `Sales`.

## <a name="remove-a-tag-from-a-vm"></a>Supprimer une étiquette d’une machine virtuelle

1. Définissez certains paramètres. 

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
   ``` 
    Voici un exemple de sortie :

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myaselinuxvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg1"
    PS C:\WINDOWS\system32> $TagName = "Organization" 
    ```
2. Obtenez l’objet machine virtuelle.

    ```powershell
    $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
    $VirtualMachine   
    ```   

    Voici un exemple de sortie :

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    ResourceGroupName : myaserg1
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselinuxvm1
    VmId              : 290b3fdd-0c99-4905-9ea1-cf93cd6f25ee
    Name              : myaselinuxvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Engineering"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
                                                                              PS C:\WINDOWS\system32> 
    ```
3. Supprimez l’étiquette et mettez à jour la machine virtuelle. Utilisez l’indicateur `-Force` facultatif pour exécuter la commande sans confirmation de l’utilisateur.

    ```powershell
    $tags = $VirtualMachine.Tags
    $tags.Remove($TagName)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```
    Voici un exemple de sortie :

    ```powershell
    PS C:\WINDOWS\system32> $tags = $Virtualmachine.Tags
    PS C:\WINDOWS\system32> $tags
    Key          Value
    ---          -----
    Organization Engineering
    PS C:\WINDOWS\system32> $tags.Remove($TagName)
    True
    PS C:\WINDOWS\system32> Set-AzureRMResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    Name              : myaselinuxvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGrou
                        ps/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselin
                        uxvm1
    ResourceName      : myaselinuxvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg1
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {}
    Properties        : @{vmId=290b3fdd-0c99-4905-9ea1-cf93cd6f25ee; hardwareProfile=;
                        storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    PS C:\WINDOWS\system32>
    ```


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [Gérer les étiquettes via AzureRM PowerShell](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true).
