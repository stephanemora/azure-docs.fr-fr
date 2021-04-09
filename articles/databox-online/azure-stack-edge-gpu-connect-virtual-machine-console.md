---
title: Connectez-vous à la console de machine virtuelle sur l’appareil GPU Azure Stack Edge Pro
description: Décrit comment se connecter à la console de la machine virtuelle sur une machine virtuelle exécutée sur l’appareil GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/22/2021
ms.author: alkohli
ms.openlocfilehash: 68cf157a512e9b1f6caee4734869c5bb4b836e2f
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962364"
---
# <a name="connect-to-a-virtual-machine-console-on-an-azure-stack-edge-pro-gpu-device"></a>Se connecter à la console de la machine virtuelle sur l’appareil GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

La solution GPU Azure Stack Edge Pro exécute des charges de travail pas en conteneur via les machines virtuelles. Cet article explique comment se connecter à la console d’une machine virtuelle déployée sur votre appareil. 

La console de la machine virtuelle vous permet d’accéder à vos machines virtuelles avec les fonctionnalités du clavier, de la souris et de l’écran à l’aide des outils de bureau à distance couramment disponibles. Vous pouvez accéder à la console et résoudre les problèmes rencontrés lors du déploiement d’une machine virtuelle sur votre appareil. Vous pouvez vous connecter à la console de la machine virtuelle, même si votre machine virtuelle n’a pas pu être configurée.


## <a name="workflow"></a>Flux de travail

Le workflow de haut niveau présente les étapes suivantes :

1. Connectez-vous à l’interface PowerShell de votre appareil.
1. Activez l’accès de la console à la machine virtuelle.
1. Connectez-vous à la machine virtuelle à l’aide du protocole du bureau à distance (RDP).
1. Révoquez l’accès de la console à la machine virtuelle.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous avez suivi les configurations requises suivantes :

#### <a name="for-your-device"></a>Pour votre appareil

Vous devez avoir accès à un appareil GPU Azure Stack Edge Pro qui est activé. Une ou plusieurs machines virtuelles doivent être déployées sur l’appareil. Vous pouvez déployer des machines virtuelles via Azure PowerShell, via les modèles ou via le Portail Azure.

#### <a name="for-client-accessing-the-device"></a>Pour un client accédant à l’appareil

Assurez-vous que vous avez accès à un système client qui :

- Peut avoir accès à l'interface PowerShell de l'appareil. Le client exécute un [système d'exploitation pris en charge](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).
- Le client exécute PowerShell 7.0 ou une version ultérieure. Cette version de PowerShell fonctionne pour les clients Windows, Mac et Linux. Consultez les instructions pour [installer PowerShell 7.0](/powershell/scripting/whats-new/what-s-new-in-powershell-70?view=powershell-7.1&preserve-view=true).
- A des fonctionnalités de bureau à distance. Selon que vous utilisez Windows, macOS ou Linux, vous devez installer l’un de ces [clients de bureau à distance](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). Cet article fournit des instructions sur le [Bureau à distance Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop#install-the-client) et [FreeRDP](https://www.freerdp.com/). <!--Which version of FreeRDP to use?-->


## <a name="connect-to-vm-console"></a>Se connecter à la console de la machine virtuelle

Procédez comme suit pour vous connecter à la console de la machine virtuelle sur votre appareil.

### <a name="connect-to-the-powershell-interface-on-your-device"></a>Se connecter à l’interface PowerShell de votre appareil

La première étape consiste à se [connecter à l’interface PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) de votre appareil. 

### <a name="enable-console-access-to-the-vm"></a>Activer l’accès de la console à la machine virtuelle

1.  Dans l’interface PowerShell, exécutez la commande suivante pour activer l’accès à la console de la machine virtuelle.

    ```powershell
    Grant-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
    ```
2. Dans l’exemple de sortie, prenez note de l’ID de la machine virtuelle. Vous en aurez besoin par la suite.

    ```powershell
    [10.100.10.10]: PS>Grant-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1
        
    VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
    VirtualMachineHostName : 3V78B03
    ResourceGroupName      : mywindowsvm1rg
    VirtualMachineName     : mywindowsvm1
    Id                     : 81462e0a-decb-4cd4-96e9-057094040063
    [10.100.10.10]: PS>
    ```

### <a name="connect-to-the-vm"></a>Connexion à la machine virtuelle

Vous pouvez maintenant utiliser un client Bureau à distance pour vous connecter à la console de la machine virtuelle.

#### <a name="use-windows-remote-desktop"></a>Utiliser le Bureau à distance Windows

1. Créez un nouveau fichier texte et insérez-y le texte suivant.

    ```
    pcb:s:<VM ID from PowerShell>;EnhancedMode=0
    full address:s:<IP address of the device>   
    server port:i:2179
    username:s:EdgeARMUser
    negotiate security layer:i:0
    ```
1. Enregistrez le fichier au format * *.rdp* sur votre système client. Vous allez utiliser ce profil pour vous connecter à la machine virtuelle.
1. Double-cliquez sur le profil pour vous connecter à la machine virtuelle. Fournissez les informations d’identification suivantes :

    - **Nom d’utilisateur** : connectez-vous en tant que EdgeARMUser.
    - **Mot de passe** : indiquez le mot de passe Azure Resource Manager local de votre appareil. Si vous avez oublié le mot de passe, [Réinitialisez le mot de passe Azure Resource Manager via le Portail Azure](azure-stack-edge-gpu-set-azure-resource-manager-password.md#reset-password-via-the-azure-portal). 

#### <a name="use-freerdp"></a>Utiliser FreeRDP

Si vous utilisez FreeRDP sur votre client Linux, exécutez la commande suivante : 

```powershell
./wfreerdp /u:EdgeARMUser /vmconnect:<VM ID from PowerShell> /v:<IP address of the device>
```

## <a name="revoke-vm-console-access"></a>Révoquer l’accès à la console de machine virtuelle

Pour révoquer l’accès à la console de la machine virtuelle, revenez à l’interface PowerShell de votre appareil. Exécutez la commande suivante :

```
Revoke-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
```
Voici un exemple de sortie :

```powershell
[10.100.10.10]: PS>Revoke-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1

VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
VirtualMachineHostName : 3V78B03
ResourceGroupName      : mywindowsvm1rg
VirtualMachineName     : mywindowsvm1
Id                     : 81462e0a-decb-4cd4-96e9-057094040063

[10.100.10.10]: PS>
```
> [!NOTE] 
> Une fois que vous avez fini d’utiliser la console de la machine virtuelle, nous vous recommandons de révoquer l’accès ou de fermer la fenêtre PowerShell pour quitter la session. 

## <a name="next-steps"></a>Étapes suivantes

- Résoudre des problèmes [Azure Stack Edge Pro](azure-stack-edge-gpu-troubleshoot.md) dans le Portail Azure.