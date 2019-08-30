---
title: Passerelle VPN Azure dans Azure Australie
description: Implémentation de la passerelle VPN dans Azure Australia pour être conforme à l’ISM et protéger efficacement les agences gouvernementales australiennes
author: galey801
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 00588042fa11ace51eef40cdedbae14c1bd99801
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575423"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Passerelle VPN Azure dans Azure Australie

La connexion sécurisée des ressources et services cloud aux systèmes locaux existants est un service critique pour n’importe quel cloud public. Le service qui fournit cette fonctionnalité dans Azure est la passerelle VPN Azure. Cet article décrit les points clés à prendre en compte lors de la configuration de la passerelle VPN pour se conformer aux [contrôles du Manuel sur la sécurité des informations](https://acsc.gov.au/infosec/ism/) de la Direction des transmissions australiennes (ASD).

Une passerelle VPN est utilisée pour envoyer le trafic chiffré entre un réseau virtuel dans Azure et un autre réseau. Trois scénarios sont traités par les passerelles VPN :

- Site à site (S2S)
- Point à site (P2S)
- Réseau à réseau

Cet article se concentre sur les passerelles VPN S2S. Le diagramme 1 montre un exemple de configuration de passerelle VPN S2S.

![Passerelle VPN avec connexions multisites](media/vpngateway-multisite-connection-diagram.png)

*Diagramme 1 – Passerelle VPN S2S Azure*

## <a name="key-design-considerations"></a>Considérations clés en matière de conception

Il existe trois options de mise en réseau pour connecter Azure aux clients du secteur public australien :

- ICON
- Azure ExpressRoute
- Internet public

Le [guide du consommateur pour Azure](https://servicetrust.microsoft.com/viewpage/Australia) de l’Australian Cyber Security Centre (Centre de la cyber-sécurité d’Australie) recommande que la passerelle VPN (ou un service tiers certifié au niveau PROTECTED (Protégé) équivalent) soit utilisée conjointement avec les trois options de mise en réseau. Cette recommandation vise à garantir que les connexions sont conformes aux contrôles de l’ISM pour le chiffrement et l’intégrité.

### <a name="encryption-and-integrity"></a>Chiffrement et intégrité

Par défaut, le VPN négocie les algorithmes de chiffrement et d’intégrité et les paramètres lors de l’établissement de la connexion dans le cadre des négociations IKE. Pendant la négociation IKE, la configuration et l’ordre de préférence varient selon que la passerelle VPN est l’initiateur ou le répondeur. Cette désignation est contrôlée via le périphérique VPN. La configuration finale de la connexion est contrôlée par la configuration du périphérique VPN. Pour plus d’informations sur les périphériques VPN validés et leur configuration, consultez [À propos des services VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

Les passerelles VPN peuvent contrôler le chiffrement et l’intégrité en configurant une stratégie IPsec/IKE personnalisée sur la connexion.

### <a name="resource-operations"></a>Opérations sur les ressources

Les passerelles VPN créent une connexion entre les environnements Azure et non-Azure via l’Internet public. ISM a des contrôles qui se rapportent à l’autorisation explicite des connexions. Par défaut, il est possible d’utiliser des passerelles VPN pour créer des tunnels non autorisés dans des environnements sécurisés. Il est essentiel que les organisations utilisent le contrôle d’accès en fonction du rôle Azure pour contrôler qui peut créer et modifier des passerelles VPN et leurs connexions. Azure n’a pas de rôle intégré pour gérer les passerelles VPN. Par conséquent, cela nécessite un rôle personnalisé.

L’accès aux rôles Propriétaire, Contributeur et Contributeur de réseaux est étroitement contrôlé. Nous vous recommandons également d’utiliser Azure Active Directory Privileged Identity Management pour un contrôle d’accès plus précis.

### <a name="high-availability"></a>Haute disponibilité

Les passerelles VPN Azure peuvent avoir plusieurs connexions et prendre en charge plusieurs périphériques VPN locaux dans le même environnement local. Voir le diagramme 1.

Les réseaux virtuels dans Azure peuvent avoir plusieurs passerelles VPN qui peuvent être déployées dans des configurations indépendantes, active/passive ou active/active.

Nous vous conseillons de déployer toutes les passerelles VPN dans une [configuration hautement disponible](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable). C’est le cas, par exemple, de deux périphériques VPN locaux connectés à deux passerelles VPN en mode actif/passif ou actif/actif. Voir le diagramme 2.

![Connexions redondantes de la passerelle VPN](media/dual-redundancy.png)

*Diagramme 2 – Passerelles VPN en mode actif/actif et deux périphériques VPN*

### <a name="forced-tunneling"></a>Tunneling forcé

Le tunneling (création de tunnel) forcé redirige ou force tout le trafic Internet vers l’environnement local via la passerelle VPN pour l’inspection et l’audit. Sans le tunneling forcé, le trafic Internet depuis les machines virtuelles dans Azure traverse l’infrastructure du réseau Azure directement vers l’Internet public, sans vous permettre d’inspecter ou de vérifier le trafic. Le tunneling forcé est essentiel lorsqu’une organisation est obligée d’utiliser une passerelle SIG (Secure Internet Gateway) pour un environnement.

## <a name="detailed-configuration"></a>Configuration détaillée

### <a name="service-attributes"></a>Attributs de service

Les passerelles VPN pour les connexions S2S configurées pour le secteur public australien doivent avoir les attributs suivants :

|Attribut | Obligatoire|
|--- | --- |
|gatewayType | « VPN »|
|

Les paramètres d’attribut requis pour se conformer aux contrôles de l’ISM pour le niveau PROTECTED sont les suivants :

|Attribut | Obligatoire|
|--- |---|
|vpnType |« RouteBased »|
|vpnClientConfiguration/vpnClientProtocols | « IkeV2 »|
|

Les passerelles VPN Azure prennent en charge une série d’algorithmes de chiffrement issus des normes de protocole IPsec et IKE. La stratégie par défaut définit l’interopérabilité maximale avec un large éventail d’appareils VPN tiers. Par conséquent, lors de la négociation IKE, il est possible qu’une configuration non conforme soit négociée. Il est fortement recommandé d’appliquer les paramètres de [stratégie IPsec/IKE personnalisée](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell) à vpnClientConfiguration dans les passerelles VPN pour garantir que les connexions respectent les contrôles de l’ISM pour les connexions d’environnement local à Azure. Les attributs clés sont présentés dans le tableau suivant.

|Attribut|Recommandé|Obligatoire|
|---|---|---|
|saLifeTimeSeconds|<14 400 s|300 secondes|
|saDataSizeKilobytes| |>1 024 Ko|
|ipsecEncryption| |AES256-GCMAES256|
|ipsecIntegrity| |SHA256-GCMAES256|
|ikeEncryption| |AES256-GCMAES256|
|ikeIntegrity| |SHA256-GCMAES256|
|dhGroup|DHGroup14, DHGroup24, ECP256, ECP384|DHGroup2|
|pfsGroup|PFS2048, PFS24, ECP256, ECP384||
|

Pour dhGroup et pfsGroup dans le tableau précédent, nous vous recommandons d’utiliser en priorité ECP256 et ECP384, même si d’autres paramètres peuvent être utilisés.

### <a name="related-services"></a>Services connexes

Lorsque vous concevez et configurez une passerelle VPN Azure, plusieurs services connexes doivent également exister et être configurés.

|de diffusion en continu | Action requise|
|--- | ---|
|Réseau virtuel | Les passerelles VPN sont attachées à un réseau virtuel. Créez un réseau virtuel avant de créer une passerelle VPN.|
|Adresse IP publique | Les passerelles VPN S2S ont besoin d’une adresse IP publique pour établir la connectivité entre le périphérique VPN local et la passerelle VPN. Créez une adresse IP publique avant de créer une passerelle VPN S2S.|
|Subnet | Créez un sous-réseau du réseau virtuel pour la passerelle VPN.|
|

## <a name="implementation-steps-using-powershell"></a>Étapes d’implémentation à l’aide de PowerShell

### <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

1. Créez un rôle personnalisé. Le contributeur virtualNetworkGateway en est un exemple. Créez un rôle à affecter aux utilisateurs qui seront autorisés à créer et à modifier des passerelles VPN. Le rôle personnalisé doit autoriser les opérations suivantes :

   Microsoft.Network/virtualNetworkGateways/*  
   Microsoft.Network/connections/*  
   Microsoft.Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft.Network/publicIPAddresses/*  
   Microsoft.Network/publicIPPrefixes/*  
   Microsoft.Network/routeTables/*  

2. Ajoutez le rôle personnalisé aux utilisateurs autorisés à créer et à gérer des passerelles VPN et des connexions à des environnements locaux.

### <a name="create-a-vpn-gateway"></a>Créer une passerelle VPN

Ces étapes supposent que vous avez déjà créé un réseau virtuel.

1. Créez une adresse IP publique.
2. Créez un sous-réseau de passerelle VPN.
3. Créez un fichier de configuration IP de passerelle VPN.
4. Créez une passerelle VPN.
5. Créez une passerelle de réseau local pour le périphérique VPN local.
6. Créez une stratégie IPsec. Cette étape suppose que vous utilisez des stratégies IPsec/IKE personnalisées.
7. Créez une connexion entre la passerelle VPN et une passerelle de réseau local à l’aide de la stratégie IPsec.

### <a name="enforce-tunneling"></a>Appliquer le tunneling

Si le tunneling forcé est requis, avant de créer la passerelle VPN :

1. Créez une table de routage et des règles de routage.
2. Associez une table de routage aux sous-réseaux appropriés.

Après avoir créé la passerelle VPN :

- Définissez GatewayDefaultSite sur l’environnement local sur la passerelle VPN.

### <a name="example-powershell-script"></a>Exemple de script PowerShell

Un exemple de script PowerShell utilisé pour créer une stratégie IPsec/IKE personnalisée respecte les contrôles de l’ISM pour la classification de sécurité au niveau PROTECTED (Protégé) australienne.

Il suppose que le réseau virtuel, la passerelle VPN et les passerelles locales existent.

#### <a name="create-an-ipsecike-policy"></a>Créez une stratégie IPsec/IKE.

L’exemple de script ci-dessous crée une stratégie IPsec/IKE avec les paramètres et les algorithmes suivants :

- IKEv2 : AES256, SHA256, DHGroup ECP256
- IPsec : AES256, SHA256, PFS ECP256, Durée de vie de l’AS de 14 400 secondes et 102 400 000 Ko

```powershell
$custompolicy = New-AzIpsecPolicy `
                    -IkeEncryption AES256 `
                    -IkeIntegrity SHA256 `
                    -DhGroup ECP256 `
                    -IpsecEncryption AES256 `
                    -IpsecIntegrity SHA256 `
                    -PfsGroup ECP256 `
                    -SALifeTimeSeconds 14400 `
                    -SADataSizeKilobytes 102400000
```

#### <a name="create-a-s2s-vpn-connection-with-the-custom-ipsecike-policy"></a>Créer une connexion VPN S2S avec une stratégie IPsec/IKE personnalisée

```powershell
$vpngw = Get-AzVirtualNetworkGateway `
                    -Name "<yourVPNGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"
$localgw = Get-AzLocalNetworkGateway  `
                    -Name "<yourLocalGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"

New-AzVirtualNetworkGatewayConnection `
                    -Name "ConnectionName" `
                    -ResourceGroupName "<yourResourceGroupName>" `
                    -VirtualNetworkGateway1 $vpngw `
                    -LocalNetworkGateway2 $localgw `
                    -Location "Australia Central" `
                    -ConnectionType IPsec `
                    -IpsecPolicies $custompolicy `
                    -SharedKey "AzureA1b2C3"
```

## <a name="next-steps"></a>Étapes suivantes

Cet article a abordé la configuration spécifique de la passerelle VPN afin de répondre aux exigences spécifiées dans le Manuel sur la sécurité des informations pour la sécurisation des données en transit correspondant au niveau PROTECTED (Protégé) du secteur public australien. Pour savoir comment configurer votre passerelle VPN, consultez :

- [Présentation de la passerelle de réseau virtuel Azure](https://docs.microsoft.com/azure/vpn-gateway/)  
- [Qu’est-ce qu’une passerelle VPN ?](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
- [Créer un réseau virtuel avec une connexion VPN de site à site à l’aide de PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
- [Créer et gérer une passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)