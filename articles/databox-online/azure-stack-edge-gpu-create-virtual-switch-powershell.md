---
title: Créer un commutateur virtuel dans Azure Stack Edge via PowerShell
description: Décrit comment créer un commutateur virtuel sur un appareil Azure Stack Edge à l’aide de PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: 1ad86695510a8fe93bbeeab27db53f5afbef92fd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106555922"
---
# <a name="create-a-new-virtual-switch-in-azure-stack-edge-pro-gpu-via-powershell"></a>Créer un commutateur virtuel dans Azure Stack Edge Pro avec GPU via PowerShell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment créer un nouveau commutateur virtuel votre appareil Azure Stack Edge Pro avec GPU. Par exemple, vous créez un commutateur virtuel si vous souhaitez que vos machines virtuelles se connectent via un autre port réseau physique.

## <a name="vm-deployment-workflow"></a>Workflow du déploiement de machine virtuelle

1. Connectez-vous à l’interface PowerShell de votre appareil.
2. Interrogez les interfaces réseau physiques disponibles.
3. créer un commutateur virtuel.
4. Vérifiez le réseau virtuel et le sous-réseau qui sont créés automatiquement.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que :

- Vous avez accès à une machine cliente qui peut accéder à l’interface PowerShell de votre appareil. Consultez [Connectez-vous à l’interface PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

    La machine cliente doit exécuter un [système d’exploitation pris en charge](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).

- Utilisez l’interface utilisateur locale pour activer le calcul sur l’une des interfaces réseau physiques sur votre appareil, conformément aux instructions fournies dans [Activer le réseau de calcul](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) sur votre appareil. 


## <a name="connect-to-the-powershell-interface"></a>Connexion à l’interface PowerShell

[Connectez-vous à l’interface PowerShell de votre appareil](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

## <a name="query-available-network-interfaces"></a>Interroger les interfaces réseau disponibles

1. Utilisez la commande suivante pour afficher la liste des interfaces réseau physiques sur lesquelles vous pouvez créer un commutateur virtuel. Vous allez sélectionner l’une de ces interfaces réseau.

    ```powershell
    Get-NetAdapter -Physical
    ```
    Voici un exemple de sortie :
    
    ```powershell
        [10.100.10.10]: PS>Get-NetAdapter -Physical
        
        Name                      InterfaceDescription                    ifIndex Status       MacAddress       LinkSpeed
        ----                      --------------------                    ------- ------       ----------        -----
        Port2                     QLogic 2x1GE+2x25GE QL41234HMCU NIC ...      12 Up           34-80-0D-05-26-EA ...ps
        Ethernet                  Remote NDIS Compatible Device                11 Up           F4-02-70-CD-41-39 ...ps
        Port1                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#3       9 Up           34-80-0D-05-26-EB ...ps
        Port5                     Mellanox ConnectX-4 Lx Ethernet Ad...#2       8 Up           0C-42-A1-C0-E3-99 ...ps
        Port3                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#4       7 Up           34-80-0D-05-26-E9 ...ps
        Port6                     Mellanox ConnectX-4 Lx Ethernet Adapter       6 Up           0C-42-A1-C0-E3-98 ...ps
        Port4                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#2       4 Up           34-80-0D-05-26-E8 ...ps
        
        [10.100.10.10]: PS>
    ```
2. Choisissez une interface réseau :

    - Qui a le statut **Up**. 
    - Non utilisée par des commutateurs virtuels existants. Actuellement, un seul commutateur virtuel peut être configuré par interface réseau. 
    
    Pour vérifier les associations existantes de commutateur virtuel et d’interface réseau, exécutez la commande `Get-HcsExternalVirtualSwitch`.
 
    Voici un exemple de sortie :

    ```powershell
    [10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

    Name                          : vSwitch1
    InterfaceAlias                : {Port2}
    EnableIov                     : True
    MacAddressPools               :
    IPAddressPools                : {}
    ConfigurationSource           : Dsc
    EnabledForCompute             : True
    SupportsAcceleratedNetworking : False
    DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
    Type                          : External
    
    [10.100.10.10]: PS>
    ```
    Dans ce cas, le port 2 est associé à un commutateur virtuel existant et ne doit pas être utilisé.

## <a name="create-a-virtual-switch"></a>Créer un commutateur virtuel

Utilisez l’applet de commande suivante pour créer un commutateur virtuel sur l’interface réseau spécifiée. Une fois cette opération terminée, vos instances de calcul peuvent utiliser le nouveau réseau virtuel.

```powershell
Add-HcsExternalVirtualSwitch -InterfaceAlias <Network interface name> -WaitForSwitchCreation $true
```

Utilisez la commande `Get-HcsExternalVirtualSwitch` pour identifier le commutateur nouvellement créé. Le nouveau commutateur créé est nommé `vswitch-<InterfaceAlias>`. 

Voici un exemple de sortie :

```powershell
[10.100.10.10]: P> Add-HcsExternalVirtualSwitch -InterfaceAlias Port5 -WaitForSwitchCreation $true
[10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

Name                          : vSwitch1
InterfaceAlias                : {Port2}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                : {}
ConfigurationSource           : Dsc
EnabledForCompute             : True
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
Type                          : External

Name                          : vswitch-Port5
InterfaceAlias                : {Port5}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                :
ConfigurationSource           : Dsc
EnabledForCompute             : False
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 9b301c40-3daa-49bf-a20b-9f7889820129
Type                          : External

[10.100.10.10]: PS>
```

## <a name="verify-network-subnet"></a>Vérifier le réseau, le sous-réseau 

Une fois que vous avez créé le commutateur virtuel, Azure Stack Edge Pro avec GPU crée automatiquement un réseau virtuel et un sous-réseau correspondant. Vous pouvez utiliser ce réseau virtuel lors de la création de machines virtuelles.

<!--To identify the virtual network and subnet associated with the new switch that you created, use the `Get-HcsVirtualNetwork` command. This cmdlet will be released in April some time. -->

## <a name="next-steps"></a>Étapes suivantes

- [Déployer des machines virtuelles sur votre appareil Azure Stack Edge Pro avec GPU via Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)

- [Déployer des machines virtuelles sur Azure Stack Edge Pro avec GPU via le portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
