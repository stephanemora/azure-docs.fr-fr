---
title: Fichier include
description: Fichier include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2021
ms.author: cherylmc
ms.custom: include file, devx-track-azurepowershell
ms.openlocfilehash: d41b443a076e303d96588a27285b671123593b57
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111556433"
---
### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Combien de points de terminaison clients VPN puis-je avoir dans ma configuration point à site ?

Cela dépend de la référence SKU de passerelle. Pour plus d’informations sur le nombre de connexions prises en charge, consultez [Références SKU de passerelle](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a><a name="supportedclientos"></a>Quels systèmes d’exploitation client puis-je utiliser avec une connexion point à site ?

Les systèmes d’exploitation clients pris en charge sont les suivants :

* Windows 7 (32 bits et 64 bits)
* Windows Server 2008 R2 (64 bits uniquement)
* Windows 8.1 (32 bits et 64 bits)
* Windows Server 2012 (64 bits uniquement)
* Windows Server 2012 R2 (64 bits uniquement)
* Windows Server 2016 (64 bits uniquement)
* Windows Server 2019 (64 bits uniquement)
* Windows 10
* macOS version 10.11 ou supérieure
* Linux (StrongSwan)
* iOS

[!INCLUDE [TLS](vpn-gateway-tls-updates.md)]

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Puis-je parcourir les serveurs proxy et les pare-feu à l’aide de la fonctionnalité point à site ?

Azure prend en charge trois types d’options de VPN point à site :

* Protocole SSTP (Secure Socket Tunneling Protocol). SSTP est une solution SSL propriétaire de Microsoft qui peut pénétrer les pare-feu, car la plupart des pare-feu ouvrent le port TCP 443 sortant utilisé par SSL.

* OpenVPN. OpenVPN est une solution SSL qui peut pénétrer les pare-feu, car la plupart des pare-feu ouvrent le port TCP 443 sortant utilisé par SSL.

* VPN IKEv2. Le VPN IKEv2 est une solution VPN IPsec basée sur des normes qui utilise les ports UDP 500 et 4500 sortants ainsi que le protocole IP no. 50. Les pare-feux n’ouvrent pas toujours ces ports. Il est donc possible que le VPN IKEv2 ne soit pas en mesure de parcourir les proxies et pare-feux.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Si je redémarre un ordinateur client avec une configuration point à site, le réseau VPN va-t-il se reconnecter automatiquement ?

Par défaut, l'ordinateur client ne rétablit pas automatiquement la connexion VPN.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>La configuration point à site prend-elle en charge la reconnexion automatique et DDNS sur les clients VPN ?

La reconnexion automatique et DDNS ne sont actuellement pas pris en charge dans les configurations VPN point à site.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Puis-je avoir des configurations coexistantes site à site et point à site pour un même réseau virtuel ?

Oui. Pour le modèle de déploiement Resource Manager, vous devez disposer d’un type de VPN basé sur le routage pour votre passerelle. Pour le modèle de déploiement Classic, vous avez besoin d’une passerelle dynamique. Nous ne prenons pas en charge la configuration point à site pour les passerelles VPN à routage statique ou les passerelles VPN basée sur une stratégie.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-network-gateways-at-the-same-time"></a>Puis-je configurer un client point à site pour me connecter à plusieurs passerelles de réseaux virtuels en même temps ?

En fonction du logiciel VPN Client utilisé, vous pouvez vous connecter à plusieurs passerelles de réseaux virtuels, à condition que les réseaux virtuels qui y sont connectés n’aient pas d’espaces d’adressage en conflit entre eux ou avec le réseau à partir duquel le client se connecte.  Azure VPN Client prend en charge de nombreuses connexions VPN, mais une seule connexion peut être connectée à un moment M.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Puis-je configurer un client point à site pour me connecter à plusieurs réseaux virtuels en même temps ?

Oui, les connexions client point à site à une passerelle de réseau virtuel déployée dans un réseau virtuel appairé à d’autres réseaux virtuels peuvent avoir accès à d’autres réseaux virtuels appairés. Les clients point à site sont en mesure de se connecter à des réseaux virtuels appairés tant que ceux-ci utilisent les fonctionnalités UseRemoteGateway/AllowGatewayTransit. Pour plus d’informations, consultez [À propos du routage point à site](../articles/vpn-gateway/vpn-gateway-about-point-to-site-routing.md).

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Quel débit puis-je attendre des connexions site à site ou point à site ?

Il est difficile de maintenir le débit exact des tunnels VPN. IPsec et SSTP sont des protocoles VPN de chiffrement lourd. Le débit est également limité par la latence et la bande passante entre vos locaux et Internet. Pour une passerelle VPN ne disposant que des connexions VPN point à site IKEv2, le débit total que vous obtiendrez dépend de la référence SKU de passerelle. Pour plus d’informations sur le débit, consultez [Références SKU de passerelle](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>Puis-je utiliser un client VPN logiciel pour une connexion point à site prenant en charge SSTP et/ou IKEv2 ?

Non. Vous ne pouvez utiliser le client VPN natif sur Windows que pour SSTP et pour le client VPN natif sur Mac pour IKEv2. Toutefois, vous pouvez utiliser le client OpenVPN sur toutes les plateformes pour vous connecter via le protocole OpenVPN. Consultez la liste des [systèmes d’exploitation client pris en charge](#supportedclientos).

### <a name="can-i-change-the-authentication-type-for-a-point-to-site-connection"></a>Puis-je modifier le type d’authentification pour une connexion point à site ?

Oui. Dans le portail, accédez à la page **Passerelle VPN -> Configuration point à site**. Pour **Type d’authentification**, sélectionnez les types d’authentification à utiliser. Notez qu’une fois que vous avez modifié un type d’authentification, il se peut que des clients actuels ne puissent pas se connecter tant qu’un nouveau profil de configuration de client VPN n’a pas été généré, téléchargé et appliqué à chaque client VPN.

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>Azure prend-elle en charge le VPN IKEv2 avec Windows ?

Le protocole IKEv2 est pris en charge sur Windows 10 et Server 2016. Toutefois, pour pouvoir utiliser le protocole IKEv2, vous devez installer les mises à jour et définir une valeur de clé de Registre localement. Les versions de système d’exploitation antérieures à Windows 10 ne sont pas prises en charge. Elles peuvent uniquement utiliser le protocole SSTP ou **OpenVPN®**.

Pour préparer Windows 10 ou Server 2016 pour IKEv2 :

1. Installez la mise à jour.

   | Version du SE | Date | Nombre/lien |
   |---|---|---|
   | Windows Server 2016<br>Windows 10 version 1607 | 17 janvier 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10 version 1703 | 17 janvier 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 version 1709 | 22 mars 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |

2. Définissez la valeur de clé de Registre. Créer ou de définir la clé REG_DWORD « HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload » sur 1 dans le Registre.

### <a name="what-happens-when-i-configure-both-sstp-and-ikev2-for-p2s-vpn-connections"></a>Que se passe-t-il lorsque je configure SSTP et IKEv2 pour les connexions VPN P2S ?

Lorsque vous configurez SSTP et IKEv2 dans un environnement mixte (composé d’appareils Windows et Mac), le client VPN Windows essaiera toujours le tunnel IKEv2 d’abord, mais il reviendra à SSTP si la connexion IKEv2 n’a pas abouti. MacOSX se connecte uniquement via le protocole IKEv2.

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>À part Windows et Mac, quelles autres plateformes sont prises en charge par Azure pour le réseau VPN P2S ?

Azure prend en charge Windows, Mac et Linux pour les VPN point à site (P2S).

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enable-radius-andor-ikev2-vpn-on-it"></a>J’ai déjà une passerelle VPN Azure déployée. Puis-je activer RADIUS et/ou le réseau VPN IKEv2 sur celle-ci ?

Oui, si la référence (SKU) de passerelle que vous utilisez prend en charge RADIUS ou IKEv2, vous pouvez activer ces fonctionnalités sur des passerelles que vous avez déjà déployées à l’aide de PowerShell ou du portail Azure. Notez que la référence (SKU) De base ne prend en charge ni RADIUS, ni IKEv2.

### <a name="how-do-i-remove-the-configuration-of-a-p2s-connection"></a><a name="removeconfig"></a>Comment supprimer la configuration d’une connexion P2S ?

Vous pouvez supprimer une configuration P2S avec Azure CLI et PowerShell en utilisant les commandes suivantes :

#### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$gw=Get-AzVirtualNetworkGateway -name <gateway-name>`  
$gw.VPNClientConfiguration = $null`  
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw`
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network vnet-gateway update --name <gateway-name> --resource-group <resource-group name> --remove "vpnClientConfiguration"
```
