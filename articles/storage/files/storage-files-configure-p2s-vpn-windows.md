---
title: Configurer un VPN point à site (P2S) sur Windows pour l’utiliser avec Azure Files | Microsoft Docs
description: Guide pratique pour configurer un VPN point à site (P2S) sur Windows pour une utilisation avec Azure Files
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 90995b1c9d10c7b589706f5abf37f92d76e4362b
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75560349"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Configurer un VPN point à site (P2S) sur Windows pour l’utiliser avec Azure Files
Vous pouvez utiliser une connexion VPN point à site (P2S) pour monter vos partages de fichiers Azure sur SMB en dehors d’Azure, sans ouvrir le port 445. Une connexion VPN point à site est une connexion VPN entre Azure et un client individuel. Pour utiliser une connexion VPN P2S avec Azure Files, une connexion VPN P2S doit être configurée pour chaque client qui souhaite se connecter. Si de nombreux clients doivent se connecter à vos partages de fichiers Azure depuis votre réseau local, vous pouvez utiliser une connexion VPN site à site (S2S) au lieu d’une connexion point à site pour chaque client. Pour plus d’informations, consultez [Configurer un VPN site à site pour une utilisation avec Azure Files](storage-files-configure-s2s-vpn.md).

Nous vous recommandons fortement de lire [Considérations sur le réseau pour l’accès direct à un partage de fichiers Azure](storage-files-networking-overview.md) avant de poursuivre avec cet article de procédure pour une présentation complète des options réseau disponibles pour Azure Files.

L’article décrit en détail la procédure à suivre pour configurer un VPN point à site sur Windows (client Windows et Windows Server) afin de monter directement des partages de fichiers Azure locaux. Si vous souhaitez router le trafic Azure File Sync via un VPN, consultez [Configuration les paramètres de proxy et de pare-feu d’Azure File Sync](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Conditions préalables requises
- La version la plus récente du module Azure PowerShell. Pour plus d’informations sur l’installation d’Azure PowerShell, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) et sélectionnez votre système d’exploitation. Si vous préférez utiliser Azure CLI sur Windows, vous pouvez le faire ; les instructions ci-dessous s’appliquent cependant à Azure PowerShell.

- Le module PowerShell Azure Private DNS. Comme il n’est pas actuellement distribué dans le cadre du module Azure PowerShell, il peut être installé avec la méthode suivante :
    ```PowerShell
    if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
        Install-Module -Name Az.PrivateDns -AllowClobber -AllowPrerelease
    } else {
        Install-Module -Name Az.PrivateDns -RequiredVersion "0.1.3"
    }

    Import-Module -Name Az.PrivateDns
    ```  

- Un partage de fichiers Azure que vous voulez monter localement. Vous pouvez utiliser un partage de fichiers Azure [standard](storage-how-to-create-file-share.md) ou [premium](storage-how-to-create-premium-fileshare.md) avec votre VPN point à site.

## <a name="deploy-a-virtual-network"></a>Déployer un réseau virtuel
Pour accéder à votre partage de fichiers Azure et à d’autres ressources Azure depuis un environnement local via un VPN point à site, vous devez créer un réseau virtuel. La connexion VPN point à site que vous allez créer automatiquement constitue un pont entre votre machine Windows locale et ce réseau virtuel Azure.

Le script PowerShell suivant crée un réseau virtuel Azure avec trois sous-réseaux : un pour le point de terminaison de service de votre compte de stockage, un pour le point de terminaison privé de votre compte de stockage (celui-ci est nécessaire pour accéder localement au compte de stockage sans créer de routage personnalisé pour l’adresse IP publique du compte de stockage, qui peut changer), et un pour votre passerelle de réseau virtuel qui fournit le service VPN. 

N’oubliez pas de remplacer `<region>`, `<resource-group>` et `<desired-vnet-name>` par les valeurs correspondant à votre environnement.

```PowerShell
$region = "<region>"
$resourceGroupName = "<resource-group>" 
$virtualNetworkName = "<desired-vnet-name>"

$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName `
    -Location $region `
    -AddressPrefix "192.168.0.0/16"

Add-AzVirtualNetworkSubnetConfig `
    -Name "ServiceEndpointSubnet" `
    -AddressPrefix "192.168.0.0/24" `
    -VirtualNetwork $virtualNetwork `
    -ServiceEndpoint "Microsoft.Storage" `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "PrivateEndpointSubnet" `
    -AddressPrefix "192.168.1.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "GatewaySubnet" `
    -AddressPrefix "192.168.2.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

$virtualNetwork | Set-AzVirtualNetwork | Out-Null
$virtualNetwork = Get-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName

$serviceEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "ServiceEndpointSubnet" }
$privateEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "PrivateEndpointSubnet" }
$gatewaySubnet = $virtualNetwork.Subnets | ` 
    Where-Object { $_.Name -eq "GatewaySubnet" }
```

## <a name="restrict-the-storage-account-to-the-virtual-network"></a>Limiter le compte de stockage au réseau virtuel
Par défaut, quand vous créez un compte de stockage, vous pouvez y accéder de n’importe où dans le monde, à condition que vous disposiez des moyens nécessaires pour authentifier votre demande (par exemple avec votre identité Active Directory ou la clé de votre compte de stockage). Pour limiter l’accès à ce compte de stockage au réseau virtuel que vous venez de créer, vous devez créer un ensemble de règles réseau qui autorise l’accès au sein du réseau virtuel et refuse tous les autres accès.

Cette limitation au réseau virtuel nécessite l’utilisation d’un point de terminaison de service. Le point de terminaison de service est une construction réseau par laquelle le DNS public ou l’adresse IP publique n’est accessible qu’à partir du réseau virtuel. Comme il n’est pas garanti que l’adresse IP publique reste la même, nous préférons finalement utiliser un point de terminaison privé plutôt qu’un point de terminaison de service pour le compte de stockage. Toutefois, il n’est pas possible de limiter le compte de stockage, sauf si un point de terminaison de service est également exposé.

N’oubliez pas de remplacer `<storage-account-name>` par le compte de stockage auquel vous souhaitez accéder.

```PowerShell
$storageAccountName = "<storage-account-name>"

$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName

$networkRule = Add-AzStorageAccountNetworkRule `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -VirtualNetworkResourceId $serviceEndpointSubnet.Id

Update-AzStorageAccountNetworkRuleSet `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Bypass AzureServices `
    -DefaultAction Deny `
    -VirtualNetworkRule $networkRule | Out-Null
``` 

## <a name="create-a-private-endpoint-preview"></a>Créer un point de terminaison privé (préversion)
La création d’un point de terminaison privé pour votre compte de stockage attribue à ce dernier une adresse IP dans l’espace d’adressage IP de votre réseau virtuel. Lorsque vous montez votre partage de fichiers Azure à partir d’un emplacement local en utilisant cette adresse IP privée, les règles d’acheminement définies automatiquement par l’installation VPN acheminent votre demande de montage vers le compte de stockage via le VPN. 

```PowerShell
$internalVnet = Get-AzResource `
    -ResourceId $virtualNetwork.Id `
    -ApiVersion "2019-04-01"

$internalVnet.Properties.subnets[1].properties.privateEndpointNetworkPolicies = "Disabled"
$internalVnet | Set-AzResource -Force | Out-Null

$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
    -Name "myConnection" `
    -PrivateLinkServiceId $storageAccount.Id `
    -GroupId "file"

$privateEndpoint = New-AzPrivateEndpoint `
    -ResourceGroupName $resourceGroupName `
    -Name "$storageAccountName-privateEndpoint" `
    -Location $region `
    -Subnet $privateEndpointSubnet `
    -PrivateLinkServiceConnection $privateEndpointConnection

$zone = Get-AzPrivateDnsZone -ResourceGroupName $resourceGroupName
if ($null -eq $zone) {
    $zone = New-AzPrivateDnsZone `
        -ResourceGroupName $resourceGroupName `
        -Name "privatelink.file.core.windows.net"
} else {
    $zone = $zone[0]
}

$link = New-AzPrivateDnsVirtualNetworkLink `
    -ResourceGroupName $resourceGroupName `
    -ZoneName $zone.Name `
    -Name ($virtualNetwork.Name + "-link") `
    -VirtualNetworkId $virtualNetwork.Id

$internalNic = Get-AzResource `
    -ResourceId $privateEndpoint.NetworkInterfaces[0].Id `
    -ApiVersion "2019-04-01"

foreach($ipconfig in $internalNic.Properties.ipConfigurations) {
    foreach($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.', 2)[0]
        $dnsZone = $fqdn.split('.', 2)[1]
        New-AzPrivateDnsRecordSet `
            -ResourceGroupName $resourceGroupName `
            -Name $recordName `
            -RecordType A `
            -ZoneName $zone.Name `
            -Ttl 600 `
            -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
                -IPv4Address $ipconfig.properties.privateIPAddress) | Out-Null
    }
}
```

## <a name="create-root-certificate-for-vpn-authentication"></a>Créer un certificat racine pour l’authentification VPN
Pour que les connexions VPN de vos ordinateurs Windows locaux soient authentifiées afin d’accéder à votre réseau virtuel, vous devez créer deux certificats : un certificat racine, qui sera fourni à la passerelle de machine virtuelle, et un certificat client, qui sera signé avec le certificat racine. Le code PowerShell suivant crée le certificat racine ; le certificat client est créé après la création de la passerelle de réseau virtuel Azure avec les informations de la passerelle. 

```PowerShell
$rootcertname = "CN=P2SRootCert"
$certLocation = "Cert:\CurrentUser\My"
$vpnTemp = "C:\vpn-temp\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
    Install-Module WindowsCompatibility
    Import-WinModule PKI
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}
```

## <a name="deploy-virtual-network-gateway"></a>Déployer une passerelle de réseau virtuel
La passerelle de réseau virtuel Azure est le service auquel vos machines Windows locales se connecteront. Le déploiement de ce service nécessite deux composants de base : une adresse IP publique qui identifiera la passerelle auprès de vos clients où qu’ils se trouvent dans le monde, et un certificat racine que vous avez précédemment créé et qui sera utilisé pour authentifier vos clients.

N’oubliez pas de remplacer `<desired-vpn-name-here>` par le nom que vous souhaitez donner à ces ressources.

> [!Note]  
> Le déploiement de la passerelle de réseau virtuel Azure peut prendre jusqu’à 45 minutes. Pendant le déploiement de cette ressource, ce script PowerShell est bloqué pour permettre l’exécution du déploiement. Ceci est normal.

```PowerShell
$vpnName = "<desired-vpn-name-here>" 
$publicIpAddressName = "$vpnName-PublicIP"

$publicIPAddress = New-AzPublicIpAddress `
    -ResourceGroupName $resourceGroupName ` 
    -Name $publicIpAddressName `
    -Location $region `
    -Sku Basic `
    -AllocationMethod Dynamic

$gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig `
    -Name "vnetGatewayConfig" `
    -SubnetId $gatewaySubnet.Id `
    -PublicIpAddressId $publicIPAddress.Id

$azRootCertificate = New-AzVpnClientRootCertificate `
    -Name "P2SRootCert" `
    -PublicCertData $rootCertificate

$vpn = New-AzVirtualNetworkGateway `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -Location $region `
    -GatewaySku VpnGw1 `
    -GatewayType Vpn `
    -VpnType RouteBased `
    -IpConfigurations $gatewayIpConfig `
    -VpnClientAddressPool "172.16.201.0/24" `
    -VpnClientProtocol IkeV2 `
    -VpnClientRootCertificates $azRootCertificate
```

## <a name="create-client-certificate"></a>Créer un certificat client
Le certificat client est créé avec l’URI de la passerelle de réseau virtuel. Ce certificat est signé avec le certificat racine que vous avez créé précédemment.

```PowerShell
$clientcertpassword = "1234"

$vpnClientConfiguration = New-AzVpnClientConfiguration `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -AuthenticationMethod EAPTLS

Invoke-WebRequest `
    -Uri $vpnClientConfiguration.VpnProfileSASUrl `
    -OutFile "$vpnTemp\vpnclientconfiguration.zip"

Expand-Archive `
    -Path "$vpnTemp\vpnclientconfiguration.zip" `
    -DestinationPath "$vpnTemp\vpnclientconfiguration"

$vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"
$vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

$exportedclientcertpath = $vpnTemp + "P2SClientCert.pfx"
$clientcertname = "CN=" + $vpnProfile.VpnServer

$clientcert = New-SelfSignedCertificate `
    -Type Custom `
    -DnsName $vpnProfile.VpnServer `
    -KeySpec Signature `
    -Subject $clientcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -Signer $rootcert `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

$mypwd = ConvertTo-SecureString -String $clientcertpassword -Force -AsPlainText

Export-PfxCertificate `
    -FilePath $exportedclientcertpath `
    -Password $mypwd `
    -Cert $clientcert | Out-Null
```

## <a name="configure-the-vpn-client"></a>Configurer le client VPN
La passerelle de réseau virtuel Azure crée un package téléchargeable avec les fichiers de configuration nécessaires à l’initialisation de la connexion VPN sur votre machine Windows locale. Nous allons configurer la connexion VPN avec la fonctionnalité [VPN Always On](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/) de Windows 10/Windows Server 2016+. Ce package contient également des packages exécutables qui vont configurer si vous le souhaitez le client VPN Windows hérité. Ce guide utilise VPN Always On au lieu du client VPN Windows hérité, car le client VPN Always On permet aux utilisateurs finaux de se connecter au VPN Azure et de s’en déconnecter sans avoir d’autorisations d’administrateur sur leur machine. 

Le script suivant installe le certificat client nécessaire pour l’authentification auprès de la passerelle de réseau virtuel, et télécharge et installe le package VPN. N’oubliez pas de remplacer `<computer1>` et `<computer2>` par les ordinateurs appropriés. Vous pouvez exécuter ce script sur autant de machines que vous le souhaitez en ajoutant des sessions PowerShell au tableau `$sessions`. Votre compte d’utilisation doit être un administrateur sur chacune de ces machines. Si une de ces machines est la machine locale à partir de laquelle vous exécutez le script, vous devez exécuter le script à partir d’une session PowerShell avec élévation de privilèges. 

```PowerShell
$sessions = [System.Management.Automation.Runspaces.PSSession[]]@()
$sessions += New-PSSession -ComputerName "<computer1>"
$sessions += New-PSSession -ComputerName "<computer2>"

foreach ($session in $sessions) {
    Invoke-Command -Session $session -ArgumentList $vpnTemp -ScriptBlock { 
        $vpnTemp = $args[0]
        if (-Not (Test-Path $vpnTemp)) {
            New-Item `
                -ItemType Directory `
                -Force `
                -Path "C:\vpn-temp" | Out-Null
        }
    }

    Copy-Item `
        -Path $exportedclientcertpath, $exportedrootcertpath, "$vpnTemp\vpnclientconfiguration.zip" `
        -Destination $vpnTemp `
        -ToSession $session

    Invoke-Command `
        -Session $session `
        -ArgumentList `
            $mypwd, `
            $vpnTemp, `
            $virtualNetworkName
        -ScriptBlock { 
            $mypwd = $args[0] 
            $vpnTemp = $args[1]
            $virtualNetworkName = $args[2]

            Import-PfxCertificate `
                -Exportable `
                -Password $mypwd `
                -CertStoreLocation "Cert:\LocalMachine\My" `
                -FilePath "$vpnTemp\P2SClientCert.pfx" | Out-Null

            Import-Certificate `
                -FilePath "$vpnTemp\P2SRootCert.cer" `
                -CertStoreLocation "Cert:\LocalMachine\Root" | Out-Null

            Expand-Archive `
                -Path "$vpnTemp\vpnclientconfiguration.zip" `
                -DestinationPath "$vpnTemp\vpnclientconfiguration"
            $vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"

            $vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

            Add-VpnConnection `
                -Name $virtualNetworkName `
                -ServerAddress $vpnProfile.VpnServer ` 
                -TunnelType Ikev2 `
                -EncryptionLevel Required `
                -AuthenticationMethod MachineCertificate `
                -SplitTunneling `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.Routes `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.VpnClientAddressPool `
                -AllUserConnection

            rasdial $virtualNetworkName
        }
}

Remove-Item -Path $vpnTemp -Recurse
```

## <a name="mount-azure-file-share"></a>Monter le partage de fichiers Azure
Maintenant que vous avez configuré votre VPN de point à site, vous pouvez l’utiliser pour monter le partage de fichiers Azure sur les ordinateurs que vous configurez via PowerShell. L’exemple suivant va monter le partage, lister le contenu du répertoire racine du partage pour prouver que le partage est réellement, puis démonter le partage. Malheureusement, il n’est pas possible de monter le partage de façon persistante via PowerShell à distance. Pour un montage persistant, consultez [Utiliser un partage de fichiers Azure avec Windows](storage-how-to-use-files-windows.md). 

```PowerShell
$myShareToMount = "<file-share>"

$storageAccountKeys = Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName
$storageAccountKey = ConvertTo-SecureString `
    -String $storageAccountKeys[0].Value `
    -AsPlainText `
    -Force

$nic = Get-AzNetworkInterface -ResourceId $privateEndpoint.NetworkInterfaces[0].Id
$storageAccountPrivateIP = $nic.IpConfigurations[0].PrivateIpAddress

Invoke-Command `
    -Session $sessions `
    -ArgumentList  `
        $storageAccountName, `
        $storageAccountKey, `
        $storageAccountPrivateIP, `
        $myShareToMount `
    -ScriptBlock {
        $storageAccountName = $args[0]
        $storageAccountKey = $args[1]
        $storageAccountPrivateIP = $args[2]
        $myShareToMount = $args[3]

        $credential = [System.Management.Automation.PSCredential]::new(
            "AZURE\$storageAccountName", 
            $storageAccountKey)

        New-PSDrive `
            -Name Z `
            -PSProvider FileSystem `
            -Root "\\$storageAccountPrivateIP\$myShareToMount" `
            -Credential $credential `
            -Persist | Out-Null
        Get-ChildItem -Path Z:\
        Remove-PSDrive -Name Z
    }
```

## <a name="see-also"></a>Voir aussi
- [Considérations réseau pour l’accès direct à un partage réseau Azure](storage-files-networking-overview.md)
- [Configurer un VPN point à site (P2S) sous Linux pour une utilisation avec Azure Files](storage-files-configure-p2s-vpn-linux.md)
- [Configurer un VPN site à site (S2S) pour une utilisation avec Azure Files](storage-files-configure-s2s-vpn.md)
