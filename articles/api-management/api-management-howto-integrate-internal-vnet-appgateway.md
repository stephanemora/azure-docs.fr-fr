---
title: Utilisation du service Gestion des API dans un réseau virtuel avec Azure Application Gateway
titleSuffix: Azure API Management
description: Installez et configurez le service Gestion des API Azure dans un réseau virtuel interne avec Application Gateway (pare-feu d’applications web) comme serveur frontal.
services: api-management
documentationcenter: ''
author: solankisamir
ms.service: api-management
ms.topic: how-to
ms.author: sasolank
ms.date: 06/10/2021
ms.custom: devx-track-azurepowershell,contperf-fy21q4
ms.openlocfilehash: 734dc2a8a2300f2fcccf5780a7ccbd9dfdcae6d4
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111986369"
---
# <a name="integrate-api-management-in-an-internal-virtual-network-with-application-gateway"></a>Intégrer le service Gestion des API dans un réseau virtuel interne avec Application Gateway

Vous pouvez configurer le service Gestion des API dans un [réseau virtuel en mode interne](api-management-using-with-internal-vnet.md) afin qu’il ne soit accessible qu’au sein du réseau virtuel. [Azure Application Gateway](../application-gateway/overview.md) est un service PaaS faisant office d’équilibreur de charge de couche 7. Il agit comme un service proxy inverse incluant un pare-feu d’applications web (WAF).

La combinaison du service Gestion des API approvisionné dans un réseau virtuel interne avec le service frontal Application Gateway offre les possibilités suivantes :

* Utiliser la même ressource de gestion des API pour la consommation à la fois par les consommateurs internes et externes.
* Utiliser une seule ressource de gestion des API et mettre à disposition un sous-ensemble d’API défini dans la gestion des API pour les consommateurs externes.
* Fournir un moyen clé en main d’activer et désactiver l’accès au service Gestion des API à partir de l’Internet public.

> [!NOTE]
> Cet article a été mis à jour pour utiliser la [référence SKU Application Gateway WAF_v2](../application-gateway/application-gateway-autoscaling-zone-redundant.md).

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pour suivre les étapes décrites dans cet article, vous devez disposer des éléments suivants :

* Un abonnement Azure actif.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certificats
     - Fichiers PFX pour les noms d’hôte personnalisés du service Gestion des API : passerelle, portail des développeurs et point de terminaison de gestion. 
     - Fichier CER pour le certificat racine des certificats PFX. 
     
    Pour plus d’informations, consultez [Créer des certificats pour autoriser le serveur back-end dans Azure Application Gateway](../application-gateway/certificates-for-backend-authentication.md). À des fins de test, vous pouvez générer des [certificats auto-signés](../application-gateway/self-signed-certificates.md).
* La version la plus récente d’Azure PowerShell. Si ce n’est déjà fait, installez [Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="scenario"></a>Scénario

Cet article montre comment utiliser un seul et même service Gestion des API pour les consommateurs internes et externes, faisant office de serveur frontal unique pour les API locales et cloud. Il explique également comment exposer uniquement un sous-ensemble de vos API (dans cet exemple, mises en évidence en vert) pour une consommation externe, à l’aide de la fonctionnalité disponible dans Application Gateway.

Dans le premier exemple de configuration, toutes vos API sont gérées uniquement à partir de votre réseau virtuel. Les consommateurs internes (mis en surbrillance en orange) peuvent accéder à toutes vos API internes et externes. Le trafic ne sort jamais vers Internet. Une connectivité haute performance peut être fournie via des circuits ExpressRoute.

![itinéraire d’URL](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

### <a name="what-is-required-to-integrate-api-management-and-application-gateway"></a>Que faut-il pour intégrer les services Gestion des API et Application Gateway ?

* **Pool de serveurs principaux :** adresse IP virtuelle interne du service Gestion des API.
* **Paramètres du pool de serveurs principaux** : chaque pool comporte des paramètres comme le port, le protocole et une affinité basée sur les cookies. Ces paramètres sont appliqués à tous les serveurs du pool.
* **Port front-end :** Il s’agit du port public ouvert sur la passerelle d’application. Le trafic l’atteignant est redirigé vers l’un des serveurs principaux.
* **Écouteur :** L’écouteur dispose d’un port front-end, d’un protocole (HTTP ou HTTPS ; valeurs sensibles à la casse) et du nom du certificat TLS/SSL (en cas de configuration du déchargement TLS).
* **Règle :** relie un écouteur à un pool de serveurs principaux.
* **Sonde d’intégrité personnalisée :** par défaut, Application Gateway, utilise des sondes basées sur des adresses IP pour déterminer les serveurs actifs dans le BackendAddressPool. Le service Gestion des API répond uniquement aux requêtes avec l’en-tête d’hôte est correct. C’est pourquoi les sondes par défaut échouent. Vous définissez une sonde d’intégrité personnalisée pour aider la passerelle applicative à déterminer que le service est actif et qu’il doit transférer les demandes.
* **Certificat de domaine personnalisé :** Pour accéder au service Gestion des API à partir d’Internet, vous devez créer un mappage CNAME de son nom d’hôte au nom DNS frontal d’Application Gateway. Cela garantit que l’en-tête de nom d’hôte et le certificat envoyé à Application Gateway et transféré au service Gestion des API sont reconnus par celui-ci. Dans cet exemple, nous utilisons trois certificats : pour la passerelle du service Gestion des API (serveur principal), pour le portail des développeurs et pour le point de terminaison de gestion.  

## <a name="steps-required-to-integrate-api-management-and-application-gateway"></a>Étapes requises pour intégrer les services Gestion des API et Application Gateway

1. Créer un groupe de ressources pour Resource Manager
1. Créer un réseau virtuel, un sous-réseau et une adresse IP publique pour l’équilibreur de charge Application Gateway. Créer un autre sous-réseau pour le service Gestion des API
1. Créer un service Gestion des API à l’intérieur du sous-réseau de réseau virtuel créé à l’étape précédente. Veillez à utiliser le mode interne.
1. Configurer des noms de domaine personnalisés dans le service Gestion des API.
1. Configurer une zone DNS privée pour la résolution DNS dans le réseau virtuel.
1. Créer un objet de configuration de passerelle Application Gateway
1. Créer une ressource Application Gateway
1. Créer un CNAME à partir du nom DNS public de la passerelle Application Gateway pour le nom d’hôte proxy du service Gestion des API

### <a name="expose-the-developer-portal-and-management-endpoint-externally-through-application-gateway"></a>Exposer le portail des développeurs et le point de terminaison de gestion en externe via Application Gateway

Dans ce guide, nous allons également exposer le **portail des développeurs** et le **point de terminaison de gestion** à un public extérieur via la passerelle applicative. Des étapes supplémentaires sont nécessaires pour créer un écouteur, une sonde, des paramètres et des règles pour chaque point de terminaison. Tous les détails sont fournis dans les étapes respectives.

> [!WARNING]
> Si vous utilisez Azure AD ou une authentification tierce, veuillez activer la fonctionnalité d’[affinité de session basée sur les cookies](../application-gateway/features.md#session-affinity) dans Application Gateway.

> [!WARNING]
> Pour empêcher le WAF Application Gateway d’interrompre le téléchargement des spécifications OpenAPI dans le portail des développeurs, vous devez désactiver la règle de pare-feu `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"`.
> 
> Les règles du WAF Application Gateway susceptibles de nuire à la fonctionnalité du portail sont les suivantes :
> 
> - `920300`, `920330`, `931130`, `942100`, `942110`, `942180`, `942200`, `942260`, `942340`, `942370` pour le mode administratif
> - `942200`, `942260`, `942370`, `942430`, `942440` pour le portail publié

## <a name="create-a-resource-group-for-resource-manager"></a>Créer un groupe de ressources pour Resource Manager

### <a name="step-1"></a>Étape 1

Connexion à Azure

```powershell
Connect-AzAccount
```

Authentifiez-vous à l’aide de vos informations d’identification.

### <a name="step-2"></a>Étape 2

Sélectionnez l’abonnement souhaité.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>Étape 3 :

Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant).

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d'une passerelle Application Gateway utiliseront le même groupe de ressources.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway

L’exemple suivant montre comment créer un réseau virtuel à l’aide de Resource Manager. Dans cet exemple, le réseau virtuel se compose de sous-réseaux distincts pour les services Application Gateway et Gestion des API.

### <a name="step-1"></a>Étape 1

Créez des groupes de sécurité réseau (NSG) et des règles de NSG pour les sous-réseaux des services Application Gateway et Gestion des API.

```powershell
$appGwRule1 = New-AzNetworkSecurityRuleConfig -Name appgw-in -Description "AppGw inbound" `
    -Access Allow -Protocol * -Direction Inbound -Priority 100 -SourceAddressPrefix `
    GatewayManager -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 65200-65535
$appGwNsg = New-AzNetworkSecurityGroup -ResourceGroupName $resGroupName -Location $location -Name `
    "NSG-APPGW" -SecurityRules $appGwRule1

$apimRule1 = New-AzNetworkSecurityRuleConfig -Name apim-in -Description "APIM inbound" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix `
    ApiManagement -SourcePortRange * -DestinationAddressPrefix VirtualNetwork -DestinationPortRange 3443
$apimNsg = New-AzNetworkSecurityGroup -ResourceGroupName $resGroupName -Location $location -Name `
    "NSG-APIM" -SecurityRules $apimRule1
```

### <a name="step-2"></a>Étape 2

Affectez la plage d’adresses 10.0.0.0/24 à la variable de sous-réseau à utiliser pour le service Application Gateway lors de la création d’un réseau virtuel.

```powershell
$appGatewaySubnet = New-AzVirtualNetworkSubnetConfig -Name "appGatewaySubnet" -NetworkSecurityGroup $appGwNsg -AddressPrefix "10.0.0.0/24"
```

### <a name="step-3"></a>Étape 3

Affectez la plage d’adresses 10.0.1.0/24 à la variable de sous-réseau à utiliser pour le service Gestion des API lors de la création d’un réseau virtuel.

```powershell
$apimSubnet = New-AzVirtualNetworkSubnetConfig -Name "apimSubnet" -NetworkSecurityGroup $apimNsg -AddressPrefix "10.0.1.0/24"
```

### <a name="step-4"></a>Étape 4

Créez un réseau virtuel nommé **appgwvnet** dans le groupe de ressources **apim-appGw-RG** pour la région USA Ouest. Utilisez le préfixe 10.0.0.0/16 avec les sous-réseaux 10.0.0.0/24 et 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName `
  -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appGatewaySubnet,$apimSubnet
```

### <a name="step-5"></a>Étape 5

Attribuez des variables de sous-réseau pour les étapes suivantes

```powershell
$appGatewaySubnetData = $vnet.Subnets[0]
$apimSubnetData = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-virtual-network-configured-in-internal-mode"></a>Créez un service Gestion des API dans un réseau virtuel configuré en mode interne.

L’exemple ci-dessous montre comment créer un service Gestion des API dans un réseau virtuel configuré pour un accès interne uniquement.

### <a name="step-1"></a>Étape 1

Créez un objet réseau virtuel du service Gestion des API à l’aide du sous-réseau `$apimSubnetData` créé plus haut.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimSubnetData.Id
```

### <a name="step-2"></a>Étape 2

Créez un service Gestion des API à l’intérieur du réseau virtuel. Cet exemple crée le service dans le niveau de service Développeur. Spécifiez un nom unique pour votre service Gestion des API.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name, must be globally unique
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

La création et l’activation d’un service Gestion des API à ce niveau peuvent prendre entre 30 et 40 minutes. Une fois la commande exécutée, pour confirmer l’accès, reportez-vous à [Configuration DNS requise pour accéder au service Gestion des API du réseau virtuel interne](api-management-using-with-internal-vnet.md#apim-dns-configuration). 

## <a name="set-up-custom-domain-names-in-api-management"></a>Configurer des noms de domaine personnalisés dans le service Gestion des API

### <a name="step-1"></a>Étape 1

Initialisez les variables suivantes avec les détails des certificats incluant des clés privées pour les domaines et le certificat racine approuvé. Dans cet exemple, nous utilisons `api.contoso.net`, `portal.contoso.net` et `management.contoso.net`.  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$managementHostname = "management.contoso.net"               # API management endpoint host
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$managementCertPfxPath = "C:\Users\Contoso\management.pfx"   # full path to management.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate
$managementCertPfxPassword = "certificatePassword123"    # password for management.contoso.net pfx certificate
# Path to trusted root CER file used in Application Gateway HTTP settings
$trustedRootCertCerPath = "C:\Users\Contoso\trustedroot.cer" # full path to contoso.net trusted root .cer file

$certGatewayPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
$certManagementPwd = ConvertTo-SecureString -String $managementCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>Étape 2

Créez et définissez les objets de configuration de nom d’hôte pour les points de terminaison de Gestion des API.  

```powershell
$gatewayHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname `
  -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certGatewayPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname `
  -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd
$managementHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $managementHostname `
  -HostnameType Management -PfxPath $managementCertPfxPath -PfxPassword $certManagementPwd

$apimService.ProxyCustomHostnameConfiguration = $gatewayHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
$apimService.ManagementCustomHostnameConfiguration = $managementHostnameConfig

Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Pour configurer la connectivité au portail des développeurs hérité, vous devez remplacer `-HostnameType DeveloperPortal` par `-HostnameType Portal` .

## <a name="configure-a-private-zone-for-dns-resolution-in-the-virtual-network"></a>Configurer une zone privée pour la résolution DNS dans le réseau virtuel

### <a name="step-1"></a>Étape 1

Créez une zone DNS privée et liez le réseau virtuel.

```powershell
$myZone = New-AzPrivateDnsZone -Name "contoso.net" -ResourceGroupName $resGroupName 
$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Name "mylink" `
  -VirtualNetworkId $vnet.id
```

### <a name="step-2"></a>Étape 2

Créez des enregistrements A pour les noms d’hôte de domaine personnalisés, mappant à l’adresse IP privée du service Gestion des API :

```powershell
$apimIP = $apimService.PrivateIPAddresses[0]

New-AzPrivateDnsRecordSet -Name api -RecordType A -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Ttl 3600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $apimIP)
New-AzPrivateDnsRecordSet -Name portal -RecordType A -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Ttl 3600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $apimIP)
New-AzPrivateDnsRecordSet -Name management -RecordType A -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Ttl 3600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $apimIP)
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Création d'une adresse IP publique pour la configuration frontale

Créez une ressource IP publique standard, **publicIP01**, dans le groupe de ressources.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName `
  -name "publicIP01" -location $location -AllocationMethod Static -Sku Standard
```

Une adresse IP est affectée à la passerelle Application Gateway au démarrage du service.

## <a name="create-application-gateway-configuration"></a>Créer une configuration de passerelle Application Gateway

Tous les éléments de configuration doivent être installés avant de créer la passerelle Application Gateway. Les étapes suivantes permettent de créer les éléments de configuration nécessaires à une ressource Application Gateway.

### <a name="step-1"></a>Étape 1

Créez une configuration IP de passerelle Application Gateway nommée **gatewayIP01**. Au démarrage, Application Gateway sélectionne une adresse IP du sous-réseau configuré, et achemine le trafic réseau vers les adresses IP du pool d’adresses IP principal. Gardez à l’esprit que chaque instance utilise une adresse IP unique.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appGatewaySubnetData
```

### <a name="step-2"></a>Étape 2

Configurez le port IP frontal pour le point de terminaison IP public. Ce port est le port auquel les utilisateurs finaux se connectent.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Étape 3 :

Configurez l’adresse IP frontale avec un point de terminaison IP public.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Étape 4

Configurez le certificat pour la passerelle Application Gateway, laquelle sera utilisée pour déchiffrer et rechiffrer le trafic transitant par celle-ci.

> [!NOTE]
> Application Gateway prend en charge la définition d’options TLS personnalisées, la désactivation de certaines versions du protocole TLS et la spécification de suites de chiffrement et de l’ordre de préférence. Pour en savoir plus sur les options TLS configurables, consultez cette [vue d’ensemble de la stratégie TLS](../application-gateway/application-gateway-ssl-policy-overview.md).

```powershell
$certGateway = New-AzApplicationGatewaySslCertificate -Name "gatewaycert" `
  -CertificateFile $gatewayCertPfxPath -Password $certGatewayPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "portalcert" `
  -CertificateFile $portalCertPfxPath -Password $certPortalPwd
$certManagement = New-AzApplicationGatewaySslCertificate -Name "managementcert" `
  -CertificateFile $managementCertPfxPath -Password $certManagementPwd
```

### <a name="step-5"></a>Étape 5

Créez les écouteurs HTTP pour la passerelle Application Gateway. Affectez-leur la configuration IP front-end, le port et les certificats TLS/SSL.

```powershell
$gatewayListener = New-AzApplicationGatewayHttpListener -Name "gatewaylistener" `
  -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 `
  -SslCertificate $certGateway -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "portallistener" `
  -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 `
  -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
$managementListener = New-AzApplicationGatewayHttpListener -Name "managementlistener" `
  -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 `
  -SslCertificate $certManagement -HostName $managementHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Étape 6

Créez des sondes personnalisées sur le point de terminaison de domaine de la passerelle `ContosoApi` du service Gestion des API. Le chemin d’accès `/status-0123456789abcdef` est un point de terminaison d’intégrité par défaut hébergé sur tous les services de gestion des API. Définissez `api.contoso.net` comme nom d’hôte de sonde personnalisée pour la sécuriser à l’aide du certificat TLS/SSL.

> [!NOTE]
> Le nom d’hôte `contosoapi.azure-api.net` est le nom d’hôte du proxy par défaut configuré lorsqu’un service nommé `contosoapi` est créé dans la version publique d’Azure.
>

```powershell
$apimGatewayProbe = New-AzApplicationGatewayProbeConfig -Name "apimgatewayprobe" `
  -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" `
  -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" `
  -Protocol "Https" -HostName $portalHostname -Path "/signin" `
  -Interval 60 -Timeout 300 -UnhealthyThreshold 8
$apimManagementProbe = New-AzApplicationGatewayProbeConfig -Name "apimmanagementprobe" `
  -Protocol "Https" -HostName $managementHostname -Path "/ServiceStatus" `
  -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Étape 7

Téléchargez le certificat racine approuvé à configurer sur les paramètres HTTP.

```powershell
$trustedRootCert = New-AzApplicationGatewayTrustedRootCertificate -Name "whitelistcert1" -CertificateFile $trustedRootCertCerPath
```

### <a name="step-8"></a>Étape 8

Configurez les paramètres du serveur principal HTTP pour Application Gateway, y compris une limite de délai d’expiration pour les demandes du serveur principal, au-delà de laquelle elles sont annulées. Cette valeur diffère du délai d’expiration de la sonde.

```powershell
$apimPoolGatewaySetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolGatewaySetting" `
  -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimGatewayProbe `
  -TrustedRootCertificate $trustedRootCert -PickHostNameFromBackendAddress -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" `
  -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe `
  -TrustedRootCertificate $trustedRootCert -PickHostNameFromBackendAddress -RequestTimeout 180
$apimPoolManagementSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolManagementSetting" `
  -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimManagementProbe `
  -TrustedRootCertificate $trustedRootCert -PickHostNameFromBackendAddress -RequestTimeout 180
```

### <a name="step-9"></a>Étape 9

Configurez un pool d’adresses IP principales pour chaque point de terminaison de Gestion des API, utilisant son nom de domaine respectif.

```powershell
$apimGatewayBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "gatewaybackend" `
  -BackendFqdns $gatewayHostname
$apimPortalBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "portalbackend" `
  -BackendFqdns $portalHostname
$apimManagementBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "managementbackend" `
  -BackendFqdns $managementHostname
```

### <a name="step-10"></a>Étape 10

Créez des règles pour la passerelle applicative afin d’utiliser le routage de base.

```powershell
$gatewayRule = New-AzApplicationGatewayRequestRoutingRule -Name "gatewayrule" `
  -RuleType Basic -HttpListener $gatewayListener -BackendAddressPool $apimGatewayBackendPool `
  -BackendHttpSettings $apimPoolGatewaySetting
$portalRule = New-AzApplicationGatewayRequestRoutingRule -Name "portalrule" `
  -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimPortalBackendPool `
  -BackendHttpSettings $apimPoolPortalSetting
$managementRule = New-AzApplicationGatewayRequestRoutingRule -Name "managementrule" `
  -RuleType Basic -HttpListener $managementListener -BackendAddressPool $apimManagementBackendPool `
  -BackendHttpSettings $apimPoolManagementSetting
```

> [!TIP]
> Modifiez la valeur `-RuleType` et le routage afin de limiter l’accès à certaines pages du portail des développeurs.

### <a name="step-11"></a>Étape 11

Configurez le nombre d’instances et taille de la passerelle Application Gateway. Dans cet exemple, nous utilisons la [référence (SKU) WAF_v2](../web-application-firewall/ag/ag-overview.md) pour une sécurité accrue de la ressource Gestion des API.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_v2" -Tier "WAF_v2" -Capacity 2
```

### <a name="step-12"></a>Étape 12

Configurez WAF pour passer en mode Prévention.

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Créer une passerelle Application Gateway

Créez une passerelle Application Gateway avec tous les objets de configuration des étapes précédentes.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location `
  -BackendAddressPools $apimGatewayBackendPool,$apimPortalBackendPool,$apimManagementBackendPool `
  -BackendHttpSettingsCollection $apimPoolGatewaySetting, $apimPoolPortalSetting, $apimPoolManagementSetting `
  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 `
  -HttpListeners $gatewayListener,$portalListener,$managementListener `
  -RequestRoutingRules $gatewayRule,$portalRule,$managementRule `
  -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $certGateway,$certPortal,$certManagement `
  -TrustedRootCertificate $trustedRootCert -Probes $apimGatewayProbe,$apimPortalProbe,$apimManagementProbe
```

Une fois le déploiement de la passerelle applicative terminé, vérifiez l’état d’intégrité des serveurs principaux de Gestion des API dans le portail ou en exécutant la commande suivante :

```powershell
Get-AzApplicationGatewayBackendHealth -Name $appgwName -ResourceGroupName $resGroupName
```

Assurez-vous que l’état d’intégrité de chaque pool principal est sain. Si vous devez dépanner un serveur principal non sain ou un serveur principal dont l’état d’intégrité est inconnu, consultez [Résoudre les problèmes d’intégrité des back-ends dans Application Gateway](../application-gateway/application-gateway-backend-health-troubleshooting.md).

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Définition du CNAME du nom d’hôte du proxy du service Gestion des API sur le nom DNS public de la ressource Application Gateway

Une fois la passerelle créée, configurez le serveur frontal pour la communication. Lorsque vous utilisez une adresse IP publique, Application Gateway requiert un nom DNS attribué de façon dynamique, qui risque de ne pas être facile à utiliser.

Utilisez le nom DNS d’Application Gateway pour créer un enregistrement CNAME pointant le nom d’hôte de la passerelle de Gestion des API (`api.contoso.net` dans les exemples précédents) vers ce nom DNS. Pour configurer l’enregistrement CNAME d’adresses IP frontales, récupérez les détails de la passerelle Application Gateway et de son nom IP/DNS associé à l’aide de l’élément `PublicIPAddress`. Nous vous déconseillons d’utiliser des enregistrements A, car l’adresse IP virtuelle peut changer lors du redémarrage de la passerelle.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

À des fins de test, vous pouvez mettre à jour le fichier hosts sur votre ordinateur local avec des entrées qui mappent l’adresse IP publique d’Application Gateway à chacun des noms d’hôte de point de terminaison de Gestion des API que vous avez configurés (par exemple, `api.contoso.net`, `portal.contoso.net`, `management.contoso.net`).

## <a name="summary"></a>Récapitulatif

Le service Gestion des API Azure configuré dans un réseau virtuel fournit une interface de passerelle unique pour l’ensemble des API configurées, qu’elles soient hébergées localement ou dans le cloud. L’intégration d’Application Gateway avec le service Gestion des API vous permet d’activer facilement l’accessibilité d’API particulières sur Internet, tout en fournissant un pare-feu d’applications web en tant que pare-feu frontal pour votre instance de Gestion des API.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur Azure Application Gateway
  * [Vue d’ensemble d’Application Gateway](../application-gateway/overview.md)
  * [Pare-feu d’applications web sur Application Gateway](../web-application-firewall/ag/ag-overview.md)
  * [Application Gateway à l’aide du routage basé sur le chemin](../application-gateway/tutorial-url-route-powershell.md)
* En savoir plus sur la Gestion des API et les réseaux virtuels
  * [Utilisation de la Gestion des API avec un réseau virtuel interne](api-management-using-with-internal-vnet.md)
  * [Utilisation de la Gestion des API avec des réseaux virtuels](api-management-using-with-vnet.md)
