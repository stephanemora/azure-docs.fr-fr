---
title: Passerelle VPN Azure dans Azure Australie
description: Implémentation de la passerelle VPN dans Azure Australia pour être conforme à l’ISM et protéger efficacement les agences gouvernementales australiennes
author: galey801
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 442ad6334a1775033018005d4a85875dbcb08ada
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571237"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Passerelle VPN Azure dans Azure Australie

La connexion sécurisée des ressources et services cloud aux systèmes locaux existants est un service critique pour n’importe quel cloud public.  Le service qui fournit cette fonctionnalité dans Azure est la passerelle VPN Azure. Cet article décrit les points clés à prendre en compte lors de la configuration de la passerelle VPN pour se conformer aux [contrôles du Manuel de sécurité de l’information](https://acsc.gov.au/infosec/ism/) de la Direction des transmissions australiennes (ASD).

Une passerelle VPN est utilisée pour envoyer le trafic chiffré entre un réseau virtuel dans Azure et un autre réseau.  Trois scénarios sont traités par les passerelles VPN :

- **Site à site** (S2S)
- **Point à site** (P2S)
- **Connexion entre deux réseaux virtuels**

Cet article se concentrera sur les passerelles VPN S2S. Le diagramme 1 illustre un exemple de configuration de passerelle VPN de site à site.

![Passerelle VPN avec connexions à plusieurs sites](media/vpngateway-multisite-connection-diagram.png)

*Diagramme 1 : passerelle VPN de site à site Azure*

## <a name="key-design-considerations"></a>Considérations clés en matière de conception

Il existe trois options de mise en réseau pour connecter Azure aux clients du secteur public australien :

- **ICON**
- **ExpressRoute**
- **Internet public**

Le [guide du consommateur pour Azure](https://servicetrust.microsoft.com/viewpage/Australia) de l’Australian Cyber Security Centre (Centre de la cyber-sécurité d’Australie) recommande que la passerelle VPN (ou un service tiers certifié au niveau PROTECTED (Protégé) équivalent) soit utilisée conjointement avec les trois options de mise en réseau pour s’assurer que le les connexions sont conformes aux contrôles de l’ISM pour le chiffrement et l’intégrité.

### <a name="encryption-and-integrity"></a>Chiffrement et intégrité

Par défaut, le VPN négocie les algorithmes de chiffrement et d’intégrité et les paramètres lors de l’établissement de la connexion dans le cadre des négociations IKE.  Pendant la négociation IKE, la configuration et l’ordre de préférence varient selon que la passerelle VPN est l’initiateur ou le répondeur (Remarque : ceci est contrôlé via le périphérique VPN).  La configuration finale de la connexion est contrôlée par la configuration de le périphérique VPN.  Pour plus d’informations sur les périphériques VPN validés et leur configuration, consultez la page suivante : [À propos des périphériques VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)

Les passerelles VPN peuvent contrôler le chiffrement et l’intégrité en configurant une stratégie IPsec/IKE personnalisée sur la connexion.

### <a name="resource-operations"></a>Opérations sur les ressources

Les passerelles VPN créent une connexion entre les environnements Azure et non-Azure via l’Internet public.  ISM a des contrôles qui se rapportent à l’autorisation explicite des connexions.  Par défaut, il est possible d’utiliser des passerelles VPN pour créer des tunnels non autorisés dans des environnements sécurisés.  Par conséquent, il est essentiel que les organisations utilisent le contrôle d’accès en fonction du rôle Azure pour contrôler qui peut créer et modifier des passerelles VPN et leurs connexions.  Azure n’a pas de rôle « intégré » pour gérer les passerelles VPN. Par conséquent, cela nécessite un rôle personnalisé.

L’accès aux rôles « propriétaire », « collaborateur » et « collaborateur réseau » est étroitement contrôlé.  Il est également recommandé d’utiliser Azure AD Privileged Identity Management pour un contrôle d’accès plus détaillé.

### <a name="high-availability"></a>Haute disponibilité

Les passerelles VPN Azure peuvent avoir plusieurs connexions (voir diagramme 1) et prendre en charge plusieurs périphériques VPN locaux dans le même environnement local.  

Les réseaux virtuels dans Azure peuvent avoir plusieurs passerelles VPN qui peuvent être déployées dans des configurations indépendantes, actives ou actives.

Il est recommandé de déployer toutes les passerelles VPN dans une [configuration hautement disponible](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable): par exemple, deux périphériques VPN locaux connectés à deux passerelles VPN en mode actif/passif ou actif/actif (voir le diagramme 2).

![Connexions redondantes de la passerelle VPN](media/dual-redundancy.png)

*Diagramme 2 : passerelles VPN en mode actif/actif et 2 périphériques VPN*

### <a name="forced-tunneling"></a>Tunneling forcé

Le tunneling (création de tunnel) forcé redirige ou « force » tout le trafic Internet vers l’environnement local via la passerelle VPN pour l’inspection et l’audit. Sans le tunneling forcé, le trafic Internet depuis les machines virtuelles dans Azure traverse l’infrastructure du réseau Azure directement vers l’Internet public, sans vous permettre d’inspecter ou de vérifier le trafic.  Cette situation est critique lorsque l’organisation est obligée d’utiliser une passerelle Secure Internet (SIG) pour un environnement.

## <a name="detailed-configuration"></a>Configuration détaillée

### <a name="service-attributes"></a>Attributs de service

Les passerelles VPN pour les connexions S2S configurées pour le secteur public australien doivent avoir les attributs suivants :

|Attribut | OBLIGATOIRE|
|--- | --- |
|gatewayType | « VPN »|
|

Les paramètres d’attribut requis pour se conformer aux contrôles ISM pour le niveau PROTECTED sont les suivants :

|Attribut | OBLIGATOIRE|
|--- |---|
|vpnType |« RouteBased »|
|vpnClientConfiguration/vpnClientProtocols | « IkeV2 »|
|

Les passerelles VPN Azure prennent en charge une série d’algorithmes de chiffrement issus des normes de protocole IPsec et IKE.  L’ensemble de stratégies par défaut permet de maximiser l’interopérabilité avec un large éventail d’appareils VPN tiers.  Par conséquent, il est possible que lors de la négociation IKE, une configuration non conforme soit négociée.  C’est pourquoi il est fortement recommandé d’appliquer les paramètres de [stratégie IPsec/IKE personnalisés](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell) à vpnClientConfiguration dans les passerelles VPN pour garantir que les connexions respectent les contrôles ISM pour les connexions d’environnement local à Azure.  Les attributs de clé sont les suivants :

|Attribut|RECOMMANDÉ|OBLIGATOIRE|
|---|---|---|
|saLifeTimeSeconds|14 400 secondes|300 secondes|
|saDataSizeKilobytes| |1 024 KO|
|ipsecEncryption| |AES256-GCMAES256|
|ipsecIntegrity| |SHA256-GCMAES256|
|ikeEncryption| |AES256-GCMAES256|
|ikeIntegrity| |SHA256-GCMAES256|
|dhGroup|DHGroup14, DHGroup24, ECP256, ECP384|DHGroup2|
|pfsGroup|PFS2048, PFS24, ECP256, ECP384||
|

*Pour dhGroup et pfsGroup dans la table ci-dessus, nous vous recommandons d’utiliser en priorité ECP256 et ECP384, même si d’autres paramètres peuvent être utilisés.*

### <a name="related-services"></a>Services connexes

Lors de la conception et de la configuration d’une passerelle VPN Azure, plusieurs services connexes doivent également exister et être configurés :

|de diffusion en continu | Action requise|
|--- | ---|
|Réseau virtuel | Les passerelles VPN sont jointes à un réseau virtuel.  Un réseau virtuel doit être créé avant la création d’une nouvelle passerelle VPN.|
|Adresse IP publique | Les passerelles VPN S2S ont besoin d’une adresse IP publique pour établir la connectivité entre le périphérique VPN local et la passerelle VPN.  Une adresse IP publique doit être créée avant la création d’une passerelle VPN S2S.|
|Subnet | Un sous-réseau du réseau virtuel doit être créé pour la passerelle VPN.|
|

## <a name="implementation-steps-using-powershell"></a>Étapes d’implémentation à l’aide de PowerShell

### <a name="role-based-access-control-rbac"></a>Contrôle d’accès en fonction du rôle

1. Créez un rôle personnalisé (par exemple, contributeur virtualNetworkGateway).  Créez un rôle à affecter aux utilisateurs qui seront autorisés à créer et à modifier des passerelles VPN. Le rôle personnalisé doit autoriser les opérations suivantes :

   Microsoft.Network/virtualNetworkGateways/*  
   Microsoft.Network/connections/*  
   Microsoft.Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft.Network/publicIPAddresses/*  
   Microsoft.Network/publicIPPrefixes/*  
   Microsoft.Network/routeTables/*  

2. Ajoutez un rôle personnalisé aux utilisateurs autorisés à créer et à gérer des passerelles VPN et des connexions à des environnements locaux.

### <a name="create-vpn-gateway"></a>Créer la passerelle VPN

*Ces étapes supposent qu’un réseau virtuel a déjà été créé.*

1. Adresse IP publique créée
2. Créer un sous-réseau de passerelle VPN
3. Créer une configuration IP de passerelle VPN
4. Créer une passerelle VPN
5. Créer une passerelle de réseau local pour le périphérique VPN local
6. Créer une stratégie IPsec (en partant du principe que des stratégies IPsec/IKE personnalisées sont utilisées)
7. Créer une connexion entre la passerelle VPN et la passerelle de réseau local à l’aide de la stratégie IPsec

### <a name="enforce-tunneling"></a>Appliquer le tunneling

Si le tunneling forcé est requis, avant de créer la passerelle VPN :

1. Créez la table de routage et la ou les règle(s) de routage
2. Associez une table de routage aux sous-réseaux appropriés

Après la création de la passerelle VPN :

1. Définissez GatewayDefaultSite sur l’environnement local sur la passerelle VPN

### <a name="example-powershell-script"></a>Exemple de script PowerShell

Exemple de script PowerShell pour la création d’une stratégie IPSEC/IKE personnalisée qui respecte les contrôles ISM pour la classification de sécurité protégée australienne.

Il suppose que le réseau virtuel, la passerelle VPN et les passerelles locales existent.

#### <a name="create-an-ipsecike-policy"></a>Créez une stratégie IPsec/IKE.

L’exemple de script ci-dessous crée une stratégie IPsec/IKE avec les paramètres et les algorithmes suivants :

- IKEv2 : AES256, SHA256, DHGroup ECP256
- IPsec : AES256, SHA256, PFS ECP256, Durée de vie de l’AS de 14,400 secondes et 102 400 000 Ko

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

Cet article a abordé la configuration spécifique de la passerelle VPN afin de répondre aux exigences spécifiées dans le manuel de sécurité des informations (ISM) pour la sécurisation des données en transit correspondant au niveau PROTECTED (Protégé) du secteur public australien. Pour obtenir des instructions détaillées sur la configuration de votre passerelle VPN :

- [Présentation de la passerelle de réseau virtuel Azure](https://docs.microsoft.com/azure/vpn-gateway/)  
- [Qu’est-ce qu’une passerelle VPN ?](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
- [Créer un réseau virtuel avec une connexion VPN de site à site à l’aide de PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
- [Créer et gérer une passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)