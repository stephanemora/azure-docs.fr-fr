---
title: Créer un commutateur virtuel pour Azure IoT Edge pour Linux sur Windows | Microsoft Docs
description: Installations pour créer un commutateur virtuel pour Azure IoT Edge pour Linux sur Windows
author: kgremban
ms.reviewer: fcabrera
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/12/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 5db37717356cd06b257867615623e24a1c36a335
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524787"
---
# <a name="azure-iot-edge-for-linux-on-windows-virtual-switch-creation"></a>Création d’un commutateur virtuel Azure IoT Edge pour Linux sur Windows
Azure IoT Edge pour Linux sur Windows utilise un commutateur virtuel sur l’ordinateur hôte pour communiquer avec la machine virtuelle. Les versions Windows Desktop sont fournies avec un commutateur par défaut qui peut être utilisé, mais pas Windows Server. Avant de pouvoir déployer IoT Edge pour Linux sur Windows sur un appareil Windows Server, vous devez créer un commutateur virtuel. Par ailleurs, vous pouvez utiliser ce guide pour créer votre commutateur virtuel personnalisé, le cas échéant. 

Cet article vous explique comment créer un commutateur virtuel sur un appareil Windows afin d’installer IoT Edge pour Linux sur Windows. Pour ce faire, effectuez les étapes suivantes :
- Créer un commutateur virtuel
- Créer une table NAT
- Installer et configurer un serveur DHCP

## <a name="prerequisites"></a>Configuration requise
- Un appareil Windows. Pour connaître les versions de Windows prises en charge, consultez [Systèmes d’exploitation](support.md#operating-systems).
- Rôle Hyper-V installé sur l’appareil Windows. Pour plus d’informations sur la façon d’activer Hyper-V, consultez [Installer et provisionner Azure IoT Edge pour Linux sur un appareil Windows](./how-to-install-iot-edge-on-windows.md?tabs=powershell#prerequisites).

## <a name="create-virtual-switch"></a>Créer un commutateur virtuel 
Les étapes suivantes de cette section sont un guide générique pour la création d’un commutateur virtuel. Vérifiez que la configuration du commutateur virtuel est compatible avec votre environnement réseau.

1. Ouvrez une session PowerShell avec élévation des privilèges.

2. Contrôlez les commutateurs virtuels sur l’hôte Windows et vérifiez que vous n’avez pas de commutateur virtuel pouvant être utilisé. Pour plus d’informations, consultez [Get-VMSwitch (Hyper-V)](/powershell/module/hyper-v/get-vmswitch). 

   ```powershell
   Get-VMSwitch
   ```

   Si un commutateur virtuel nommé **Commutateur par défaut** est déjà créé et que vous n’avez pas besoin d’un commutateur virtuel personnalisé, vous devez être en mesure d’installer IoT Edge pour Linux sur Windows sans effectuer les étapes restantes de ce guide.

3. Créez un commutateur de machine virtuelle avec un nom et le type **Interne** ou **Privé**. Pour créer un commutateur virtuel **externe**, spécifiez le paramètre **NetAdapterInterfaceDescription** ou **NetAdapterName**, qui définit implicitement le type de commutateur virtuel sur **Externe**. Pour obtenir des informations détaillées et des instructions supplémentaires, consultez [New-VMSwitch (Hyper-V)](/powershell/module/hyper-v/new-vmswitch) et [Créer un commutateur virtuel pour les machines virtuelles Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines).
   ```powershell
   New-VMSwitch -Name "{switchName}" -SwitchType {switchType}
   ```

4. Obtenez l’index d’interface du commutateur créé. Pour plus d’informations, consultez [Get-NetAdapter (NetAdapter)](/powershell/module/netadapter/get-netadapter). 
   ```powershell
   (Get-NetAdapter -Name '*{switchName}*').ifIndex
   ```

5. À l’aide de l’index d’interface obtenu à l’étape précédente, récupérez l’octet d’adresse IP de la carte réseau du commutateur créé. Pour plus d’informations, consultez [Get-NetIPAddress (NetTCPIP)](/powershell/module/nettcpip/get-netipaddress). 
   ```powershell
   Get-NetIPAddress -AddressFamily IPv4  -InterfaceIndex {ifIndex}
   ```

6. À l’aide de la famille d’adresses IP et de l’index d’interface obtenus aux étapes précédentes, créez et définissez l’adresse IP de la nouvelle passerelle.  Par exemple, si l’adresse IPv4 de l’adaptateur du commutateur de réseau virtuel est xxx.xxx.xxx.yyy, vous pouvez définir la valeur gatewayIp comme suit : xxx.xxx.xxx.1. Pour plus d’informations, consultez [New-NetIPAddress (NetTCPIP)](/powershell/module/nettcpip/new-netipaddress).
   ```powershell
   New-NetIPAddress -IPAddress {gatewayIp} -PrefixLength 24 -InterfaceIndex {ifIndex}
   ```

7. Créez un objet de traduction d’adresses réseau (NAT) qui traduit une adresse réseau interne en adresse réseau externe. Utilisez la même adresse de la famille IPv4 que celle utilisée aux étapes précédentes. Par exemple, si l’adresse IPv4 de l’adaptateur du commutateur de réseau virtuel est xxx.xxx.xxx.yyy, vous pouvez définir la valeur natIp comme suit : xxx.xxx.xxx.0. Pour plus d’informations, consultez [New-NetNat (NetNat)](/powershell/module/netnat/new-netnat). 
   ```powershell
   New-NetNat -Name "{switchName}" -InternalIPInterfaceAddressPrefix "{natIp}/24"
   ```

## <a name="create-dhcp-server"></a>Créer un serveur DHCP 

>[!WARNING]
>Une autorisation peut être nécessaire pour déployer un serveur DHCP dans un environnement de réseau d’entreprise. Vérifiez si la configuration du commutateur virtuel est conforme aux stratégies de votre réseau d’entreprise. Pour plus d’informations, consultez le guide [Déployer DHCP à l’aide de Windows PowerShell](/windows-server/networking/technologies/dhcp/dhcp-deploy-wps). 

1. Vérifiez si la fonctionnalité Serveur DHCP est installée sur l’appareil. Recherchez la colonne **État de l’installation**.
   ```powershell
   Get-WindowsFeature -Name 'DHCP'
   ```

2. Si elle n’est pas installée, installez-la en utilisant les commandes suivantes.
   ```powershell
   Install-WindowsFeature -Name 'DHCP' -IncludeManagementTools
   ```

3. Ajoutez le serveur DHCP aux groupes de sécurité locaux par défaut, puis redémarrez le serveur.
   ```powershell
   netsh dhcp add securitygroups
   Restart-Service dhcpserver
   ```

4. Configurez l’étendue du serveur DHCP. Pour plus d’informations, consultez [Add-DhcpServerv4Scope (DhcpServer)](/powershell/module/dhcpserver/add-dhcpserverv4scope).  La plage d’adresses IP du serveur DHCP est déterminée par **startIp** et **endIp**. Par exemple, si 100 adresses doivent être disponibles, si l’on considère l’adresse IPv4 xxx.xxx.xxx.yyy de l’adaptateur du commutateur de réseau virtuel indiqué à l’étape 5, startIp = xxx.xxx.xxx.100, endIp = xxx.xxx.xxx.200 et subnetMask = 255.255.255.0.
   ```powershell
   Add-DhcpServerV4Scope -Name "AzureIoTEdgeScope" -StartRange {startIp} -EndRange {endIp} -SubnetMask {subnetMask} -State Active
   ```

5. Enfin, affectez l’objet NAT et la valeur gatewayIp au serveur DHCP, puis redémarrez le serveur pour charger la configuration.
   ```powershell
   Set-DhcpServerV4OptionValue -ScopeID {natIp} -Router {gatewayIp}
   Restart-service dhcpserver
   ```

## <a name="next-steps"></a>Étapes suivantes
Pour configurer un appareil avec IoT Edge pour Linux sur Windows, Suivez les étapes décrites dans [installer et approvisionner Azure IoT Edge pour Linux sur un appareil Windows](how-to-install-iot-edge-on-windows.md).