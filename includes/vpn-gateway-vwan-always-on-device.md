---
title: Fichier include
description: Fichier include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: feaf72de1d2c578d2b2d0df9e86ec0fbe0b49445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371730"
---
Les conditions suivantes doivent être remplies afin d’établir un tunnel d’appareil :

* L’appareil doit être un ordinateur joint au domaine exécutant Windows 10 Entreprise ou Éducation version 1809 ou ultérieure.
* Le tunnel est configurable uniquement pour la solution VPN intégrée Windows et est établi à l’aide d’IKEv2 avec authentification par certificat d’ordinateur.
* Un seul tunnel d’appareil peut être configuré par appareil.

1. Installez des certificats clients sur le client Windows 10 comme indiqué dans cet [article sur les clients VPN point à site](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md). Le certificat doit se trouver dans le magasin de l’ordinateur local.
1. Créez un profil VPN et configurez un tunnel d’appareil dans le contexte du compte SYSTÈME LOCAL en suivant [ces instructions](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration).

### <a name="configuration-example-for-device-tunnel"></a>Exemple de configuration pour le tunnel d’appareil

Après avoir configuré la passerelle de réseau virtuel et installé le certificat client dans le magasin de l’ordinateur local sur le client Windows 10, utilisez les exemples suivants pour configurer un tunnel d’appareil client :

1. Copiez le texte suivant et enregistrez-le sous le nom ***devicecert.ps1***.

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. Copiez le texte suivant et enregistrez-le sous le nom ***VPNProfile.xml*** dans le même dossier que **devicecert.ps1**. Modifiez le texte suivant pour correspondre à votre environnement.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers> <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address> <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address> <= IP of resource in the vnet or the vnet address space`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. Téléchargez **PsExec** de [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) et extrayez les fichiers dans **C:\PSTools**.
1. À partir d’une invite de commandes d’administration, lancez PowerShell en exécutant :

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![powershell](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. Dans PowerShell, accédez au dossier où **devicecert.ps1** et **VPNProfile.xml** se trouvent, et exécutez la commande suivante :

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. Exécutez **rasphone**.

   ![rasphone](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. Recherchez l’entrée **MachineCertTest**, puis cliquez sur **Se connecter**.

   ![Se connecter](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. Si la connexion réussit, redémarrez l’ordinateur. Le tunnel se connectera automatiquement.
