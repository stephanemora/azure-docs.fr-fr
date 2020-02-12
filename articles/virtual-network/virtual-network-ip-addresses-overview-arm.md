---
title: Types d’adresses IP dans Azure
titlesuffix: Azure Virtual Network
description: Apprenez-en plus sur les adresses IP publiques et privées dans Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: kumud
ms.openlocfilehash: 176cd9b0bf72a123bc644ebc27ee0e091aa54e97
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024617"
---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>Types d’adresses IP et méthodes d’allocation dans Azure

Vous pouvez affecter des adresses IP à des ressources Azure pour communiquer avec d’autres ressources Azure, votre réseau local et Internet. Les adresses IP que vous pouvez utiliser dans Azure sont de deux types :

* **Adresses IP publiques** : utilisées pour la communication avec Internet, notamment les services Azure accessibles au public.
* **Adresses IP privées** : utilisées pour la communication au sein d’un réseau virtuel Azure et de votre réseau local quand vous faites appel à une passerelle VPN ou à un circuit ExpressRoute pour étendre votre réseau à Azure.

Vous pouvez également créer une plage contiguë d’adresses IP publiques statiques par le biais d’un préfixe d’adresse IP publique. [Découvrez ce qu’est un préfixe d’adresse IP publique.](public-ip-address-prefix.md)

> [!NOTE]
> Azure a deux modèles de déploiement différents pour créer et utiliser des ressources :  [Resource Manager et classique](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  Cet article traite de l’utilisation du modèle de déploiement Resource Manager que Microsoft recommande pour la plupart des nouveaux déploiements à la place du [modèle de déploiement classique](virtual-network-ip-addresses-overview-classic.md).
> 

Si vous êtes familiarisé avec le modèle de déploiement classique, vérifiez les [différences d’adressage IP entre les déploiements classiques et Resource Manager](/previous-versions/azure/virtual-network/virtual-network-ip-addresses-overview-classic#differences-between-resource-manager-and-classic-deployments).

## <a name="public-ip-addresses"></a>Adresses IP publiques

Les adresses IP publiques permettent aux ressources Internet de communiquer avec les ressources Azure (communication entrante). Les adresses IP publiques permettent également aux ressources Azure de communiquer avec Internet et les services Azure publics (communication sortante) quand une adresse IP est attribuée à la ressource. L’adresse est dédiée à la ressource jusqu’à ce que vous annuliez son attribution. Si aucune adresse IP publique n’est attribuée à une ressource, la ressource peut toujours communiquer avec Internet (communication sortante), mais Azure attribue dynamiquement une adresse IP disponible qui n’est pas dédiée à la ressource. Pour plus d’informations sur les connexions sortantes dans Azure, consultez [Comprendre les connexions sortantes dans Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Dans Azure Resource Manager, une [adresse IP publique](virtual-network-public-ip-address.md) est une ressource ayant ses propres propriétés. Voici quelques unes des ressources auxquelles vous pouvez associer une ressource d’adresse IP publique :

* Interfaces réseau de machine virtuelle
* Équilibreurs de charge accessibles sur Internet
* Passerelles VPN
* Passerelles d’application
* Pare-feu Azure

### <a name="ip-address-version"></a>Version de l’adresse IP

Les adresses IP publiques sont créées avec une adresse IPv4 et IPv6. 

### <a name="sku"></a>SKU

Les adresses IP publiques sont créées avec l’une des références SKU suivantes :

>[!IMPORTANT]
> Les références SKU qui correspondent doivent être utilisées pour les ressources de Load Balancer et d’adresse IP publique. Vous ne pouvez pas avoir à la fois des ressources de référence SKU De base et de référence SKU Standard. Vous ne pouvez pas joindre des machines virtuelles autonomes, des machines virtuelles dans une ressource de groupe à haute disponibilité ou des ressources de groupe de machines virtuelles identiques aux deux références SKU simultanément.  De nouvelles conceptions doivent être envisagées à l’aide des ressources de référence SKU Standard.  Veuillez consulter [Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour plus d’informations.

#### <a name="basic"></a>De base

Toutes les adresses IP publiques créées avant l’introduction de références SKU sont des adresses IP publiques de référence SKU de base. Depuis l’introduction des références SKU, vous avez la possibilité de spécifier quelle référence SKU attribuer à l’adresse IP publique. Les adresses de référence SKU de base sont :

- Sont assignées à l’aide de la méthode d’allocation statique ou dynamique.
- Dotées d’un délai d’inactivité du flux entrant réglable de 4 à 30 minutes, avec une valeur par défaut de 4 minutes et d’un délai d’inactivité du flux sortant fixe de 4 minutes.
- Ouvertes par défaut.  Il est recommandé d’utiliser des groupes de sécurité réseau, mais cela est facultatif pour restreindre le trafic entrant ou sortant.
- Sont assignées aux ressources Azure acceptant une adresse IP publique, comme les interfaces réseau, les passerelles VPN, les passerelles Application Gateway et les équilibreurs de charge accessibles sur Internet.
- Ne gèrent pas les scénarios de zone de disponibilité.  Vous devez utiliser l’adresse IP publique de la référence SKU Standard pour les scénarios de zone de disponibilité. Pour en savoir plus sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Équilibreur de charge standard et zones de disponibilité](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="standard"></a>standard

Les adresses IP publiques de référence SKU standard :

- Utilisent toujours la méthode d’allocation statique.
- Dotées d’un délai d’inactivité du flux entrant réglable de 4 à 30 minutes, avec une valeur par défaut de 4 minutes et d’un délai d’inactivité du flux sortant fixe de 4 minutes.
- Sont sécurisées par défaut et fermées au trafic entrant. Vous devez expliciter le trafic entrant autorisé sur liste verte avec un [groupe de sécurité réseau](security-overview.md#network-security-groups).
- Sont assignées à des interfaces réseau, des équilibreurs de charge publics standard ou des passerelles d’application. Pour plus d’informations sur Standard Load Balancer, consultez [Azure Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Sont par défaut redondantes interzones et éventuellement zonales (vous pouvez les créer zonales et les garantir dans une zone de disponibilité spécifique). Pour en savoir plus sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Équilibreur de charge standard et zones de disponibilité](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
 
> [!NOTE]
> La communication entrante avec la ressource de référence SKU standard est possible uniquement si vous créez et associez un [groupe de sécurité réseau](security-overview.md#network-security-groups) et que vous autorisez explicitement le trafic entrant prévu.

> [!NOTE]
> Seules les adresses IP publiques avec la référence SKU De base sont disponibles lorsque vous utilisez [le service de métadonnées d’instance (IMDS, instance metadata service)](../virtual-machines/windows/instance-metadata-service.md). La référence SKU Standard n’est pas prise en charge.

### <a name="allocation-method"></a>Méthode d’allocation

Les adresses IP publiques de référence SKU de base et standard prennent en charge la méthode d’allocation *statique*.  La ressource est affectée à une adresse IP lors de sa création et l’adresse IP est libérée lorsque la ressource est supprimée.

Les adresses IP publiques de référence SKU de base prennent également en charge la méthode d’allocation *dynamique*, qui est la méthode par défaut si aucune méthode d’allocation n’est spécifiée.  Si vous sélectionnez la méthode d’allocation *dynamique* pour une ressource d’adresse IP publique de base, l’adresse IP n’est **pas** allouée au moment de la création de la ressource.  L’adresse IP publique est allouée lorsque vous associez l’adresse IP publique à une machine virtuelle, ou lorsque vous placez la première instance de machine virtuelle dans le pool principal d’un équilibreur de charge de base.   L’adresse IP est libérée lorsque vous arrêtez (ou supprimez) la ressource.  Après avoir été libérée de la ressource A, par exemple, l’adresse IP peut être assignée à une ressource différente. Si l’adresse IP est assignée à une ressource différente lorsque la ressource A est arrêtée, une adresse IP différente est assignée lors du redémarrage de la ressource A. Si vous modifiez la méthode d’allocation d’une ressource d’adresse IP publique de base de *statique* à *dynamique*, l’adresse est libérée. Pour vous assurer que l’adresse IP de la ressource associée ne change pas, vous pouvez définir explicitement à la méthode d’allocation sur *statique*. Une adresse IP statique est affectée immédiatement.

> [!NOTE]
> Même lorsque vous définissez la méthode d’allocation sur *statique*, vous ne pouvez pas spécifier l’adresse IP réelle affectée à la ressource IP publique. Azure assigne l’adresse IP à partir d’un pool d’adresses IP disponibles dans l’emplacement Azure dans lequel la ressource est créée.
>

Des adresses IP publiques statiques sont fréquemment utilisées dans les cas suivants :

* Lorsque vous devez mettre à jour les règles de pare-feu pour communiquer avec vos ressources Azure.
* La résolution de noms DNS est telle qu’une modification de l’adresse IP nécessiterait une mise à jour des enregistrements A.
* Vos ressources Azure communiquent avec d’autres applications ou services qui utilisent un modèle de sécurité basé sur une adresse IP.
* Vous utilisez des certificats SSL liés à une adresse IP.

> [!NOTE]
> Azure alloue des adresses IP publiques d’une plage unique à chaque région dans chaque Cloud Azure. Vous pouvez télécharger la liste des plages (préfixes) pour les clouds Azure [Public](https://www.microsoft.com/download/details.aspx?id=56519), [Gouvernement américain](https://www.microsoft.com/download/details.aspx?id=57063), [Chine](https://www.microsoft.com/download/details.aspx?id=57062), et [Allemagne](https://www.microsoft.com/download/details.aspx?id=57064).
>

### <a name="dns-hostname-resolution"></a>Résolution de nom d’hôte DNS
Vous pouvez spécifier une étiquette de nom de domaine DNS pour une ressource IP publique, qui crée un mappage pour l’élément *domainnamelabel*.*location*.cloudapp.azure.com vers l’adresse IP publique dans les serveurs DNS gérés par Azure. Par exemple, si vous créez une ressource IP publique avec **contoso** en tant que *domainnamelabel* dans **l’emplacement** Azure *USA Ouest*, le nom de domaine complet (FQDN) **contoso.westus.cloudapp.azure.com** est associé à l’adresse IP publique de la ressource.

> [!IMPORTANT]
> Chaque étiquette de nom de domaine créée doit être unique dans son emplacement Azure.  
>

### <a name="dns-best-practices"></a>DNS : meilleures pratiques
Si vous avez besoin de migrer vers une autre région, vous ne pouvez pas migrer le nom de domaine complet de votre adresse IP publique. La meilleure pratique consiste à utiliser le nom de domaine complet pour créer un enregistrement CNAME de domaine personnalisé qui pointe vers l’adresse IP publique dans Azure. Si vous devez migrer vers une adresse IP publique différente, le processus nécessitera une mise à jour de l’enregistrement CNAME. Vous n’aurez pas à mettre à jour manuellement le nom de domaine complet selon la nouvelle adresse. Vous pouvez utiliser [Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) ou un fournisseur DNS externe pour votre enregistrement DNS. 

### <a name="virtual-machines"></a>Machines virtuelles

Vous pouvez associer une adresse IP publique à une machine virtuelle [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) en l’affectant à son **interface réseau**. Vous pouvez affecter une adresse IP publique dynamique ou statique à une machine virtuelle. Apprenez-en davantage sur l’[assignation d’adresses IP à des interfaces réseau](virtual-network-network-interface-addresses.md).

### <a name="internet-facing-load-balancers"></a>Équilibreurs de charge accessibles sur Internet

Vous pouvez associer une adresse IP publique créée avec l’une des deux [références SKU](#sku) à un [équilibreur de charge Azure](../load-balancer/load-balancer-overview.md) en l’assignant à la configuration **frontale** de l’équilibreur de charge. L’adresse IP publique sert d’adresse IP virtuelle (VIP) à charge équilibrée. Vous pouvez affecter une adresse IP publique dynamique ou statique à un équilibreur de charge frontal. Vous pouvez également affecter plusieurs adresses IP publiques à un équilibreur de charge frontal, ce qui permet des scénarios avec [plusieurs adresses IP virtuelles](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tels qu’un environnement mutualisé avec des sites web SSL. Pour plus d’informations sur les références SKU de l’équilibreur de charge Azure, consultez [Référence SKU standard de l’équilibreur de charge Azure](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="vpn-gateways"></a>Passerelles VPN

Une [passerelle VPN Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) connecte un réseau virtuel Azure (VNet) à d’autres réseaux virtuels Azure ou à un réseau local. Une adresse IP publique est assignée à une passerelle VPN pour lui permettre de communiquer avec le réseau distant. Vous pouvez uniquement affecter une adresse IP publique de base *dynamique* à une passerelle VPN.

### <a name="application-gateways"></a>Passerelles d’application

Vous pouvez associer une adresse IP publique à une [Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)Azure en l’affectant à la configuration **frontale** de la passerelle. Cette adresse IP publique sert d’adresse IP virtuelle à équilibrage de charge. Vous pouvez uniquement attribuer une adresse IP publique de base *dynamique* à une configuration front-end V1 de passerelle d’application et uniquement une adresse de référence SKU standard *statique* à une configuration front-end V2.

### <a name="at-a-glance"></a>Aperçu
Le tableau ci-dessous présente la propriété spécifique par le biais de laquelle une adresse IP publique peut être associée à une ressource de niveau supérieur, ainsi que les méthodes d’allocation possibles (dynamique ou statique) utilisables.

| Ressources de niveau supérieur | Association d’adresse IP | Dynamique | statique |
| --- | --- | --- | --- |
| Machine virtuelle |interface réseau |Oui |Oui |
| Équilibreur de charge accessible sur Internet |Configuration frontale |Oui |Oui |
| passerelle VPN |Configuration IP de la passerelle |Oui |Non |
| passerelle d’application |Configuration frontale |Oui (V1 uniquement) |Oui (V2 uniquement) |

## <a name="private-ip-addresses"></a>Adresses IP privées
Les adresses IP privées permettent aux ressources Azure de communiquer avec d’autres ressources dans un [réseau virtuel](virtual-networks-overview.md) ou dans un réseau local par le biais d’une passerelle VPN ou d’un circuit ExpressRoute, sans utiliser d’adresse IP accessible via Internet.

Dans le modèle de déploiement Azure Resource Manager, une adresse IP privée est associée aux types ressources Azure suivants.

* Interfaces réseau de machine virtuelle
* Équilibreurs de charge interne (ILB)
* Passerelles d’application

### <a name="allocation-method"></a>Méthode d’allocation

Une adresse IP privée est allouée à partir de la plage d’adresses du sous-réseau de la machine virtuelle dans lequel la ressource est déployée. Azure réserve les quatre premières adresses de la plage d’adresses de chaque sous-réseau, qui ne peuvent donc pas être attribuées à des ressources. Par exemple, si la plage d’adresses du sous-réseau est 10.0.0.0/16, les adresses 10.0.0.0-10.0.0.3 et 10.0.255.255 ne peuvent pas être attribuées à des ressources. Les adresses IP de la plage d’adresses du sous-réseau peuvent uniquement être attribuées à une ressource à la fois. 

Il existe deux méthodes d’allocation d’adresses IP :

- **Dynamique** : Azure attribue la première adresse IP non attribuée ou non réservée de la plage d’adresses du sous-réseau. Par exemple, Azure attribue l’adresse 10.0.0.10 à une nouvelle ressource si les adresses 10.0.0.4 à 10.0.0.9 sont déjà attribuées à d’autres ressources. La méthode d’allocation par défaut est dynamique. Une fois assignées, les adresses IP dynamiques ne sont libérées que si l’interface réseau est supprimée, assignée à un sous-réseau différent au sein du même réseau virtuel ou bien si la méthode d’allocation devient statique et qu’une adresse IP différente est spécifiée. Par défaut, Azure définit l’adresse statique sur l’adresse dynamique attribuée précédemment quand vous sélectionnez la méthode d’allocation statique à la place de la méthode dynamique.
- **Statique** : vous sélectionnez et attribuez n’importe quelle adresse IP non attribuée ou non réservée de la plage d’adresses du sous-réseau. Par exemple, si la plage d’adresses d’un sous-réseau est 10.0.0.0/16 et que les adresses 10.0.0.4 à 10.0.0.9 sont déjà attribuées à d’autres ressources, vous pouvez attribuer n’importe quelle adresse de la plage 10.0.0.10 - 10.0.255.254. Les adresses statiques ne sont libérées que si l’interface réseau est supprimée. Si vous changez la méthode d’allocation en dynamique, Azure définit l’adresse dynamique sur l’adresse IP statique précédemment attribuée, même si celle-ci n’est pas la première adresse disponible de la plage d’adresses du sous-réseau. L’adresse change aussi si l’interface réseau est assignée à un autre sous-réseau dans le même réseau virtuel. Pour l’assigner à un autre sous-réseau, vous devez d’abord modifier la méthode d’allocation statique en dynamique. Une fois que l’interface réseau est assignée à un autre sous-réseau, vous pouvez redéfinir la méthode d’allocation en statique, et assigner une adresse IP de la nouvelle plage d’adresses du sous-réseau.

### <a name="virtual-machines"></a>Machines virtuelles

Une ou plusieurs adresses IP privées sont assignées à une ou plusieurs **interfaces réseau** d’une machine virtuelle [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Vous pouvez spécifier la méthode d’allocation dynamique ou statique pour chaque adresse IP privée.

#### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Résolution de nom d’hôte DNS interne (pour les machines virtuelles)

Toutes les machines virtuelles Azure sont configurées avec des [serveurs DNS gérés par Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) par défaut, sauf si vous configurez explicitement des serveurs DNS personnalisés. Ces serveurs DNS assurent la résolution de noms interne pour les machines virtuelles qui résident dans le même réseau virtuel.

Lorsque vous créez une machine virtuelle, un mappage du nom d’hôte à son adresse IP privée est ajouté aux serveurs DNS gérés par Azure. Dans le cas d’une machine virtuelle dotée de plusieurs interfaces réseau ou de plusieurs configurations IP pour une interface réseau, le nom d’hôte est mappé sur l’adresse IP privée de la configuration IP principale de l’interface réseau principale.

Les machines virtuelles configurées avec des serveurs DNS gérés par Azure peuvent résoudre les noms d’hôtes de toutes les machines virtuelles figurant au sein du même réseau virtuel pour les adresses IP privées. Pour résoudre les noms d’hôte de machines virtuelles dans des réseaux virtuels connectés, vous devez utiliser un serveur DNS personnalisé.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Équilibreurs de charge internes (ILB) et Application Gateway

Vous pouvez attribuer une adresse IP privée à la configuration **frontale** d’un [équilibreur de charge interne Azure](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB) ou d’une [passerelle Azure Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Cette adresse IP privée sert de point de terminaison interne, accessible uniquement aux ressources de son réseau virtuel, et de réseaux distants connectés au réseau virtuel. Vous pouvez affecter une adresse IP privée statique ou dynamique à la configuration frontale.

### <a name="at-a-glance"></a>Aperçu
Le tableau ci-dessous présente la propriété spécifique par le biais de laquelle une adresse IP publique peut être associée à une ressource de niveau supérieur, ainsi que les méthodes d’allocation possibles (dynamique ou statique) utilisables.

| Ressources de niveau supérieur | Association d’adresse IP | Dynamique | statique |
| --- | --- | --- | --- |
| Machine virtuelle |interface réseau |Oui |Oui |
| Équilibrage de charge |Configuration frontale |Oui |Oui |
| passerelle d’application |Configuration frontale |Oui |Oui |

## <a name="limits"></a>limites
Les limites imposées pour l’adressage IP sont indiquées dans l’ensemble des [limites pour la mise en réseau](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) dans Azure. Ces limites sont exprimées par région et par abonnement. Vous pouvez [contacter le support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour augmenter les limites par défaut jusqu’aux limites maximum en fonction des besoins de votre entreprise.

## <a name="pricing"></a>Tarifs
Les adresses IP publiques peuvent avoir un coût nominal. Pour en savoir plus sur la tarification des adresses IP dans Azure, lisez la page [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="next-steps"></a>Étapes suivantes
* [Déployer une machine virtuelle avec une adresse IP publique statique à l’aide du portail Azure](virtual-network-deploy-static-pip-arm-portal.md)
* [Déployer une machine virtuelle avec une adresse IP privée statique à l’aide du portail Azure](virtual-networks-static-private-ip-arm-pportal.md)
