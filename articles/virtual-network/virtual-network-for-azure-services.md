---
title: Réseau virtuel pour services Azure
titlesuffix: Azure Virtual Network
description: Découvrez les avantages du déploiement de ressources sur un réseau virtuel. Les ressources situées sur les réseaux virtuels peuvent communiquer les unes avec les autres, ainsi qu’avec les ressources locales, sans impliquer de trafic via Internet.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: 1702e6cb977cdb90cdd5a0d692e5516c10075529
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102769"
---
# <a name="virtual-network-integration-for-azure-services"></a>Intégration d’un réseau virtuel pour les services Azure

L’intégration des services Azure à un réseau virtuel Azure permet un accès privé au service à partir de machines virtuelles ou de ressources de calcul dans le réseau virtuel.
Vous pouvez intégrer des services Azure dans votre réseau virtuel, grâce aux options suivantes :
- Déploiement d’instances dédiées du service au sein d’un réseau virtuel. Les services sont alors accessibles de manière privée dans le réseau virtuel, et à partir des réseaux locaux.
- Extension d’un réseau virtuel au service à l’aide de points de terminaison de service. Les points de terminaison fournisseur permettent de sécuriser les ressources de service au sein du réseau virtuel.

Pour intégrer plusieurs services Azure à votre réseau virtuel, vous pouvez combiner un ou plusieurs des modèles ci-dessus. Par exemple, vous pouvez déployer HDInsight sur votre réseau virtuel et sécuriser un compte de stockage sur le sous-réseau HDInsight par le biais de points de terminaison de service.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Déployer des services Azure sur des réseaux virtuels

Quand vous déployez des services Azure dédiés sur un [réseau virtuel](virtual-networks-overview.md), vous pouvez communiquer avec les ressources de service de manière privée, par le biais d’adresses IP privées.

![Services déployés sur un réseau virtuel](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Le déploiement de services au sein d’un réseau virtuel fournit les fonctionnalités suivantes :

- Les ressources situées sur le réseau virtuel peuvent communiquer entre elles de manière privée, par le biais d’adresses IP privées. Exemple : le transfert direct des données entre HDInsight et SQL Server sur une machine virtuelle, au sein d’un réseau virtuel.
- Les ressources locales peuvent accéder aux ressources d’un réseau virtuel à l’aide d’adresses IP privées par le biais d’une connexion [VPN site à site (passerelle VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) ou [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Les réseaux virtuels peuvent être [appairés](virtual-network-peering-overview.md) pour permettre aux ressources situées sur les réseaux virtuels de communiquer entre elles, par le biais d’adresses IP privées.
- Les instances de service situées dans un réseau virtuel sont entièrement gérées par le service Azure, pour surveiller l’intégrité des instances et fournir une mise à l’échelle adaptée en fonction de la charge.
- Les instances de service sont déployées dans un sous-réseau d’un réseau virtuel. L’accès au réseau entrant ou sortant doit être ouvert par le biais de [groupes de sécurité réseau](security-overview.md#network-security-groups) du sous-réseau, selon les recommandations fournies par les services.
- Les services peuvent éventuellement nécessiter un [sous-réseau délégué](virtual-network-manage-subnet.md#add-a-subnet) comme identificateur explicit qu’un sous-réseau peut héberger un service particulier. La délégation de sous-réseau donne des autorisations explicites au service pour créer des ressources propres au service dans le sous-réseau.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Services pouvant être déployés dans un réseau virtuel

|Catégorie|de diffusion en continu|
|-|-|
| Calcul | Machines virtuelles : [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Groupes de machines virtuelles identiques](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Service cloud](https://msdn.microsoft.com/library/azure/jj156091) : Réseau virtuel (classique) uniquement<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)  |
| Réseau | [Application Gateway - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Pare-feu Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Appliances virtuelles réseau](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) 
|Données|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
Analytics | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |
| Identité | [Services de domaine Azure Active Directory](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |
| Containers | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Container Instances (ACI)](https://www.aka.ms/acivnet)<br/>[Moteur Azure Container Service](https://github.com/Azure/acs-engine) avec le [plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet) CNI Réseau virtuel Azure|
| Web | [Gestion des API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Environnement App Service](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|
| Hébergé | [Module de sécurité matériel (HSM) dédié Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|
|||



## <a name="service-endpoints-for-azure-services"></a>Points de terminaison de service pour les services Azure

Certains services Azure ne peuvent pas être déployés sur des réseaux virtuels. Vous pouvez restreindre l’accès aux ressources de service à certains sous-réseaux du réseau virtuel, en activant un point de terminaison de service de réseau virtuel.  En savoir plus sur les [points de terminaison de service de réseau virtuel](virtual-network-service-endpoints-overview.md), et les services pour lesquels activer des points de terminaison.
