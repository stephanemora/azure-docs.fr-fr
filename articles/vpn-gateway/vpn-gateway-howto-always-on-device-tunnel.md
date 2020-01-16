---
title: Configurer un tunnel VPN Always On
titleSuffix: Azure VPN Gateway
description: Étapes de configuration d’un tunnel VPN Always On pour la passerelle VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: cherylmc
ms.openlocfilehash: 6f0c33ee7fd5790a060574230f1156c569a63936
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425667"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Configurer un tunnel de périphérique VPN Always On

L’une des nouvelles fonctionnalités du client de réseau privé virtuel (VPN) Windows 10 est la capacité de gérer une connexion VPN. Always On est une fonctionnalité de Windows 10 qui permet au profil VPN actif de se connecter automatiquement et rester connecté en fonction de déclencheurs, à savoir connexion de l’utilisateur, changement de l’état du réseau ou écran de l’appareil actif.

Les passerelles de réseau virtuel Azure peuvent être utilisées avec Windows 10 Always On pour établir des tunnels d’utilisateur persistants ainsi que des tunnels d’appareil vers Azure. Cet article vous aidera à configurer un tunnel d’appareil VPN Always On.

Les connexions VPN Always On incluent deux types de tunnels :

* Le **tunnel d’appareil** se connecte à des serveurs VPN spécifiés avant que les utilisateurs ne se connectent à l’appareil. Les scénarios de connectivité de préconnexion et la gestion des appareils utilisent un tunnel d’appareil.

* Le **tunnel d’utilisateur** se connecte uniquement après la connexion d’un utilisateur à l’appareil. Le tunnel d’utilisateur permet aux utilisateurs d’accéder aux ressources de l’organisation via les serveurs VPN.

Le tunnel d’appareil et le tunnel d’utilisateur fonctionnent tous deux de façon indépendante avec leurs profils VPN. Ils peuvent être connectés en même temps et utiliser différentes méthodes d’authentification et d’autres paramètres de configuration VPN selon les besoins.

## <a name="1-configure-the-gateway"></a>1. Configurer la passerelle

Configurez la passerelle VPN pour utiliser IKEv2 et l’authentification basée sur les certificats à l’aide de cet [article sur la connexion point à site](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-device-tunnel"></a>2. Configurer le tunnel d’appareil

Les conditions suivantes doivent être remplies afin d’établir un tunnel d’appareil :

* L’appareil doit être un ordinateur joint au domaine exécutant Windows 10 Entreprise ou Éducation version 1809 ou ultérieure.
* Le tunnel est configurable uniquement pour la solution VPN intégrée Windows et est établi à l’aide d’IKEv2 avec authentification par certificat d’ordinateur. 
* Un seul tunnel d’appareil peut être configuré par appareil.

1. Installez des certificats clients sur le client Windows 10 comme indiqué dans cet [article sur un client VPN point à site](point-to-site-how-to-vpn-client-install-azure-cert.md). Le certificat doit se trouver dans le magasin de l’ordinateur local.
1. Utilisez [ces instructions](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration) pour créer un profil VPN et configurer un tunnel d’appareil dans le contexte du compte SYSTÈME LOCAL.

### <a name="configuration-example-for-device-tunnel"></a>Exemple de configuration pour le tunnel d’appareil

Après avoir configuré la passerelle de réseau virtuel et installé le certificat client dans le magasin de l’ordinateur local sur le client Windows 10, utilisez les exemples suivants pour configurer un tunnel d’appareil client.

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

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

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

   ![powershell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. Dans PowerShell, accédez au dossier où **devicecert.ps1** et **VPNProfile.xml** se trouvent, et exécutez la commande suivante :

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. Exécutez **rasphone**.

   ![rasphone](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. Recherchez l’entrée **MachineCertTest**, puis cliquez sur **Se connecter**.

   ![Se connecter](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. Si la connexion réussit, redémarrez l’ordinateur. Le tunnel se connectera automatiquement.

## <a name="cleanup"></a>Nettoyage

Pour supprimer le profil, exécutez la commande suivante :

![Nettoyage](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Étapes suivantes

Pour résoudre les problèmes, consultez [Problèmes de connexion point à site Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
