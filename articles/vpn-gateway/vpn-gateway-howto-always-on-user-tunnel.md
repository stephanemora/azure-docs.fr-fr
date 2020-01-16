---
title: Configurer un tunnel utilisateur VPN Always On
titleSuffix: Azure VPN Gateway
description: Cet article explique comment configurer un tunnel utilisateur VPN Always On pour votre passerelle VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: bff2ed48a78bfbae984dea5e5474971817023bc6
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75729319"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Configurer un tunnel utilisateur VPN Always On

Le client VPN Windows 10, Always On, est doté d’une nouvelle fonctionnalité, qui permet de maintenir une connexion VPN. Always On permet au profil VPN actif de se connecter automatiquement et rester connecté en fonction de déclencheurs, tels qu’une connexion d’utilisateur, un changement de l’état du réseau ou l’activation de l’écran de l’appareil.

Vous pouvez utiliser les passerelles de réseau virtuel Azure avec Windows 10 Always On pour établir des tunnels d’utilisateur persistants et des tunnels d’appareil vers Azure. Dans cet article, vous apprendrez à configurer un tunnel utilisateur VPN Always On.

Les connexions VPN Always On incluent l’un de ces types de tunnels :

* **Tunnel de d’appareil** : se connecte à des serveurs VPN spécifiés avant que les utilisateurs ne se connectent à l’appareil. Les scénarios de connectivité de préconnexion et de gestion des appareils utilisent un tunnel d’appareil.

* **Tunnel utilisateur** : établit une connexion uniquement lorsque l’utilisateur se connecte à son appareil. En utilisant un tunnel d’utilisateur, vous pouvez accéder aux ressources de votre entreprise ou organisation via des serveurs VPN.

Le tunnel appareil et le tunnel utilisateur fonctionnent tous deux de façon indépendante vis-à-vis de leurs profils VPN. Ils peuvent être connectés simultanément et utiliser différentes méthodes d’authentification et d’autres paramètres de configuration VPN selon les besoins.

Dans les sections suivantes, vous allez configurer une passerelle VPN et un tunnel utilisateur.

## <a name="step-1-configure-a-vpn-gateway"></a>Étape 1 : Configurer une passerelle VPN

Pour configurer la passerelle VPN afin de d’utiliser le protocole IKEv2 et l’authentification basée sur les certificats, veuillez vous reporter à l’article [Configurez une connexion point à site à un réseau virtuel à l’aide de l’authentification par certificat Azure native : Portail Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="step-2-configure-a-user-tunnel"></a>Étape 2 : Configurer un tunnel utilisateur

1. Installez des certificats clients sur le client Windows 10 comme indiqué dans l’article [Installer des certificats client pour des connexions d’authentification par certificat P2S](point-to-site-how-to-vpn-client-install-azure-cert.md). Le certificat doit se trouver dans le magasin d’utilisateurs actuel.

1. Configurez le client VPN Always On par le biais de PowerShell, System Center Configuration Manager ou Intune en suivant les instructions de l’article [Configurer les connexions VPN Always On du client Windows 10 ](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

### <a name="example-configuration-for-the-user-tunnel"></a>Exemple de configuration pour le tunnel utilisateur

Après avoir configuré la passerelle de réseau virtuel et installé le certificat client dans le magasin de l’ordinateur local sur le client Windows 10, configurez un tunnel appareil client en procédant comme suit :

1. Copiez le texte suivant et enregistrez-le sous le nom *usercert.ps1* :

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
1. Copiez le texte suivant et enregistrez-le sous le nom *VPNProfile.xml* dans le même dossier que *usercert.ps1*. Modifiez le texte suivant pour correspondre à votre environnement :

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address>  <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address>  <= IP of resource in the vnet or the vnet address space`
   * `<PrefixSize>32</PrefixSize>     <= Subnet mask`

   ```
    <VPNProfile>  
      <NativeProfile>  
    <Servers>azuregateway-b115055e-0882-49bc-a9b9-7de45cba12c0-8e6946892333.vpn.azure.com</Servers>  
    <NativeProtocolType>IKEv2</NativeProtocolType>  
    <Authentication>  
    <UserMethod>Eap</UserMethod>
    <Eap>
    <Configuration>
    <EapHostConfig xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><EapMethod><Type xmlns="http://www.microsoft.com/provisioning/EapCommon">13</Type><VendorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorId><VendorType xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorType><AuthorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</AuthorId></EapMethod><Config xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><Eap xmlns="http://www.microsoft.com/provisioning/BaseEapConnectionPropertiesV1"><Type>13</Type><EapType xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV1"><CredentialsSource><CertificateStore><SimpleCertSelection>true</SimpleCertSelection></CertificateStore></CredentialsSource><ServerValidation><DisableUserPromptForServerValidation>false</DisableUserPromptForServerValidation><ServerNames></ServerNames></ServerValidation><DifferentUsername>false</DifferentUsername><PerformServerValidation xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</PerformServerValidation><AcceptServerName xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</AcceptServerName></EapType></Eap></Config></EapHostConfig>
    </Configuration>
    </Eap>
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
    <!-- traffic filters for the routes specified above so that only this traffic can go over the device tunnel --> 
      <TrafficFilter>  
    <RemoteAddressRanges>192.168.3.4, 192.168.3.5</RemoteAddressRanges>  
      </TrafficFilter>
    <!-- need to specify always on = true --> 
    <AlwaysOn>true</AlwaysOn>
    <RememberCredentials>true</RememberCredentials>
    <!--new node to register client IP address in DNS to enable manage out -->
    <RegisterDNS>true</RegisterDNS>
    </VPNProfile>
   ```
1. Exécutez PowerShell ISE en tant qu’administrateur.

1. Dans PowerShell, accédez au dossier où *usercert.ps1* et *VPNProfile.xml* se trouvent, et exécutez la commande suivante :

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. Sous **Paramètres VPN**, recherchez l’entrée **UserTest**, puis sélectionnez **Connecter**.

1. Si la connexion réussit, vous avez correctement configuré un tunnel utilisateur Always On.

## <a name="clean-up-your-resources"></a>Nettoyage des ressources

Pour supprimer le profil :

1. Exécutez la commande suivante :

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Arrêtez la connexion et désactivez la case à cocher **Se connecter automatiquement**.

![Nettoyage](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>Étapes suivantes

Pour résoudre les problèmes de connexion qui peuvent se produire, consultez l’article [Résolution des problèmes : problèmes de connexion point à site Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
