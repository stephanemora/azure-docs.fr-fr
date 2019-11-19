---
title: Configurer un VPN point à site (P2S) sous Linux pour une utilisation avec Azure Files | Microsoft Docs
description: Configuration d'un VPN point à site (P2S) sous Linux pour une utilisation avec Azure Files
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b988435fc6928d52321cb427e2412e7ca81680d2
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73126465"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Configurer un VPN point à site (P2S) sous Linux pour une utilisation avec Azure Files
Vous pouvez utiliser une connexion VPN point à site (P2S) pour monter vos partages de fichiers Azure via SMB en dehors d'Azure, sans ouvrir le port 445. Une connexion VPN point à site est une connexion VPN entre Azure et un client individuel. Pour utiliser une connexion VPN P2S avec Azure Files, une connexion VPN P2S doit être configurée pour chaque client qui souhaite se connecter. Si de nombreux clients doivent se connecter à vos partages de fichiers Azure depuis votre réseau local, vous pouvez utiliser une connexion VPN site à site (S2S) au lieu d'une connexion point à site pour chaque client. Pour en savoir plus, consultez [Configurer un VPN site à site pour une utilisation avec Azure Files](storage-files-configure-s2s-vpn.md).

Avant de poursuivre cet article, nous vous recommandons vivement de lire [Vue d'ensemble de la mise en réseau Azure Files](storage-files-networking-overview.md) car vous y trouverez une présentation complète des options de mise en réseau disponibles pour Azure Files.

L'article décrit en détail la procédure à suivre pour configurer un VPN point à site sous Linux afin de monter des partages de fichiers Azure localement. Si vous souhaitez acheminer le trafic Azure File Sync via un VPN, consultez [Configurer les paramètres de proxy et de pare-feu d'Azure File Sync](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Prérequis
- Version la plus récente de l'interface Azure CLI. Pour plus d'informations sur l'installation d'Azure CLI, consultez [Installer l'interface CLI Azure PowerShell](https://docs.microsoft.com/cli/azure/install-azure-cli) et sélectionnez votre système d'exploitation. Si vous préférez utiliser le module Azure PowerShell sous Linux, rien ne vous en empêche. Sachez toutefois que les instructions ci-dessous s'appliquent à Azure CLI.

- Partage de fichiers Azure que vous souhaitez monter localement. Vous pouvez utiliser un partage de fichiers Azure [standard](storage-how-to-create-file-share.md) ou [premium](storage-how-to-create-premium-fileshare.md) avec votre VPN point à site.

## <a name="install-required-software"></a>Installer les logiciels nécessaires
La passerelle de réseau virtuel Azure peut fournir des connexions VPN par le biais de plusieurs protocoles VPN, dont IPsec et OpenVPN. Ce guide explique comment utiliser IPsec et utilise le package strongSwan pour assurer la prise en charge sous Linux. 

> Vérifié avec Ubuntu 18.10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Déployer un réseau virtuel 
Pour accéder à votre partage de fichiers Azure et à d'autres ressources Azure depuis un environnement local via un VPN point à site, vous devez créer un réseau virtuel. La connexion VPN point à site que vous allez créer automatiquement constitue un pont entre votre ordinateur Linux local et ce réseau virtuel Azure.

Le script suivant crée un réseau virtuel Azure doté de trois sous-réseaux : un pour le point de terminaison de service de votre compte de stockage, un pour le point de terminaison privé de votre compte de stockage (celui-ci est nécessaire pour accéder localement au compte de stockage sans créer de routage personnalisé pour l'adresse IP publique du compte de stockage, qui peut changer), et un pour votre passerelle de réseau virtuel qui fournit le service VPN. 

N'oubliez pas de remplacer `<region>`, `<resource-group>` et `<desired-vnet-name>` par les valeurs correspondant à votre environnement.

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="restrict-the-storage-account-to-the-virtual-network"></a>Limiter le compte de stockage au réseau virtuel
Par défaut, lorsque vous créez un compte de stockage, vous pouvez y accéder de n'importe où dans le monde, à condition que vous disposiez des moyens nécessaires pour authentifier votre demande (avec votre identité Active Directory ou la clé de votre compte de stockage, par exemple). Pour limiter l'accès à ce compte de stockage au réseau virtuel que vous venez de créer, vous devez créer un ensemble de règles réseau qui autorise l'accès au sein du réseau virtuel et refuse tous les autres accès.

Cette limitation au réseau virtuel requiert l'utilisation d'un point de terminaison de service. Le point de terminaison de service est une construction de mise en réseau par laquelle le DNS public ou l'adresse IP publique n'est accessible qu'à partir du réseau virtuel. Comme il n'est pas garanti que l'adresse IP publique reste la même, nous préférons finalement utiliser un point de terminaison privé plutôt qu'un point de terminaison de service pour le compte de stockage. Toutefois, il n'est pas possible de limiter le compte de stockage, sauf si un point de terminaison de service est également exposé.

N'oubliez pas de remplacer `<storage-account-name>` par le compte de stockage auquel vous souhaitez accéder.

```bash
storageAccountName="<storage-account-name>"

az storage account network-rule add \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --subnet $serviceEndpointSubnet > /dev/null

az storage account update \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" > /dev/null
```

## <a name="create-a-private-endpoint-preview"></a>Créer un point de terminaison privé (préversion)
La création d'un point de terminaison privé pour votre compte de stockage attribue à ce dernier une adresse IP dans l'espace d'adressage IP de votre réseau virtuel. Lorsque vous montez votre partage de fichiers Azure à partir d'un emplacement local en utilisant cette adresse IP privée, les règles d'acheminement définies automatiquement par l'installation VPN acheminent votre demande de montage vers le compte de stockage via le VPN. 

```bash
zoneName="privatelink.file.core.windows.net"

storageAccount=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "id" | tr -d '"')

az resource update \
    --ids $privateEndpointSubnet \
    --set properties.privateEndpointNetworkPolicies=Disabled > /dev/null

az network private-endpoint create \
    --resource-group $resourceGroupName \
    --name "$storageAccountName-PrivateEndpoint" \
    --location $region \
    --subnet $privateEndpointSubnet \
    --private-connection-resource-id $storageAccount \
    --group-ids "file" \
    --connection-name "privateEndpointConnection" > /dev/null

az network private-dns zone create \
    --resource-group $resourceGroupName \
    --name $zoneName > /dev/null

az network private-dns link vnet create \
    --resource-group $resourceGroupName \
    --zone-name $zoneName \
    --name "$virtualNetworkName-link" \
    --virtual-network $virtualNetworkName \
    --registration-enabled false > /dev/null

networkInterfaceId=$(az network private-endpoint show \
    --name "$storageAccountName-PrivateEndpoint" \
    --resource-group $resourceGroupName \
    --query 'networkInterfaces[0].id' | tr -d '"')
 
storageAccountPrivateIP=$(az resource show \
    --ids $networkInterfaceId \
    --api-version 2019-04-01 \
    --query "properties.ipConfigurations[0].properties.privateIPAddress" | tr -d '"')

fqdnQuery="properties.ipConfigurations[0].properties.privateLinkConnectionProperties.fqdns[0]"
fqdn=$(az resource show \
    --ids $networkInterfaceId \
    --api-version 2019-04-01 \
    --query $fqdnQuery | tr -d '"')

az network private-dns record-set a create \
    --name $storageAccountName \
    --zone-name $zoneName \
    --resource-group $resourceGroupName > /dev/null
```

## <a name="create-certificates-for-vpn-authentication"></a>Créer des certificats pour l'authentification VPN
Pour que les connexions VPN de vos ordinateurs Linux locaux soient authentifiées afin d'accéder à votre réseau virtuel, vous devez créer deux certificats : un certificat racine, qui sera fourni à la passerelle de machine virtuelle, et un certificat client, qui sera signé avec le certificat racine. Le script suivant crée les certificats requis.

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>Déployer une passerelle de réseau virtuel
La passerelle de réseau virtuel Azure correspond au service auquel vos ordinateurs Linux locaux se connecteront. Le déploiement de ce service requiert deux composants de base : une adresse IP publique qui identifiera la passerelle auprès de vos clients où qu'ils se trouvent dans le monde, et un certificat racine que vous avez précédemment créé et qui sera utilisé pour authentifier vos clients.

N'oubliez pas de remplacer `<desired-vpn-name-here>` par le nom que vous souhaitez donner à ces ressources.

> [!Note]  
> Le déploiement de la passerelle de réseau virtuel Azure peut prendre jusqu'à 45 minutes. Pendant le déploiement de cette ressource, ce script bash sera bloqué pour permettre l'exécution du déploiement. Ceci est normal.

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>Configurer le client VPN
La passerelle de réseau virtuel Azure crée un package téléchargeable avec les fichiers de configuration nécessaires à l'initialisation de la connexion VPN sur votre ordinateur Linux local. Le script suivant place les certificats que vous avez créés à l'emplacement approprié et configure le fichier `ipsec.conf` avec les valeurs qui conviennent à partir du fichier de configuration contenu dans le package téléchargeable.

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>Monter le partage de fichiers Azure
Maintenant que vous avez configuré votre VPN point à site, vous pouvez monter votre partage de fichiers Azure. L'exemple suivant permet de monter le partage de manière non persistante. Pour un montage persistant, consultez [Utiliser un partage de fichiers Azure avec Linux](storage-how-to-use-files-linux.md). 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>Voir aussi
- [Vue d'ensemble de la mise en réseau Azure Files](storage-files-networking-overview.md)
- [Configurer un VPN point à site (P2S) sous Windows pour une utilisation avec Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Configurer un VPN site à site (S2S) pour une utilisation avec Azure Files](storage-files-configure-s2s-vpn.md)