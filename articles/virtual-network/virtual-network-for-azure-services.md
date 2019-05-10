---
title: Réseau virtuel pour services Azure
titlesuffix: Azure Virtual Network
description: Découvrez les avantages du déploiement de ressources sur un réseau virtuel. Les ressources situées sur les réseaux virtuels peuvent communiquer les unes avec les autres, ainsi qu’avec les ressources locales, sans impliquer de trafic via Internet.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop;kumud
ms.openlocfilehash: e5481b0e262021e28a398b72b5ad022673947609
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409505"
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
- En règle générale, les instances de service dans un réseau virtuel sont entièrement gérées par le service Azure. Cela inclut la surveillance de l’intégrité des ressources et de mise à l’échelle avec la charge.
- Les instances de service sont déployées dans un sous-réseau d’un réseau virtuel. Trafic entrant et un accès réseau sortant pour le sous-réseau doivent être ouverts via [groupes de sécurité réseau](security-overview.md#network-security-groups), selon les recommandations fournies par le service.
- Certains services imposent également des restrictions sur le sous-réseau, qu'ils sont déployés, la limitation de l’application des stratégies, des itinéraires ou combinaison de machines virtuelles et des ressources de service au sein du même sous-réseau. Vérifiez auprès de chaque service sur les restrictions spécifiques, car elles peuvent changer au fil du temps. Azure Files de NetApp, HSM dédié, Azure Container Instances, App Service sont des exemples de ces services. 
- Les services peuvent éventuellement nécessiter un [sous-réseau délégué](virtual-network-manage-subnet.md#add-a-subnet) comme identificateur explicit qu’un sous-réseau peut héberger un service particulier. En déléguant, les services obtiennent des autorisations explicites pour créer des ressources de service spécifique dans le sous-réseau de délégué.
- Voir un exemple d’une réponse de l’API REST sur un [réseau virtuel avec un sous-réseau délégué](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get_virtual_network_with_a_delegated_subnet). Une liste complète des services qui utilisent le modèle de sous-réseau déléguée peut être obtenue la [délégations disponible](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) API.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Services pouvant être déployés dans un réseau virtuel

|Catégorie|de diffusion en continu| Sous-réseau de Dedicated¹
|-|-|-|
| Calcul | Machines virtuelles : [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Groupes de machines virtuelles identiques](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Service cloud](https://msdn.microsoft.com/library/azure/jj156091) : Réseau virtuel (classique) uniquement<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Non <br/> Non  <br/> Non <br/> No²
| Réseau | [Application Gateway - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Pare-feu Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Appliances virtuelles réseau](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Oui <br/> OUI <br/> Oui <br/> Non
|Données|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Oui <br/> Oui <br/> 
|Analyse | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No² <br/> No² <br/> 
| Identité | [Services de domaine Azure Active Directory](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Non <br/>
| Containers | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Container Instances (ACI)](https://www.aka.ms/acivnet)<br/>[Moteur Azure Container Service](https://github.com/Azure/acs-engine) avec le [plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet) CNI Réseau virtuel Azure|No²<br/> Oui <br/><br/> Non
| Web | [Gestion des API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Environnement App Service](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Oui <br/> OUI <br/> Oui
| Hébergé | [Module de sécurité matériel (HSM) dédié Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Oui <br/> Oui <br/>
| | |

¹ « dédiée » implique que seules des ressources spécifiques service peuvent être déployés dans ce sous-réseau et qu’il ne peut pas être combinés avec le client/VMSSs machine virtuelle <br/> ² recommandé, mais pas une obligation imposée par le service.


## <a name="service-endpoints-for-azure-services"></a>Points de terminaison de service pour les services Azure

Certains services Azure ne peuvent pas être déployés sur des réseaux virtuels. Vous pouvez restreindre l’accès aux ressources de service à certains sous-réseaux du réseau virtuel, en activant un point de terminaison de service de réseau virtuel.  En savoir plus sur les [points de terminaison de service de réseau virtuel](virtual-network-service-endpoints-overview.md), et les services pour lesquels activer des points de terminaison.
