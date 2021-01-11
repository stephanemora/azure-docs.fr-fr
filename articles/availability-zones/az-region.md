---
title: Services Azure prenant en charge les zones de disponibilité
description: Pour créer des applications hautement disponibles et résilientes dans Azure, les zones de disponibilité fournissent des emplacements physiquement distincts que vous pouvez utiliser pour exécuter vos ressources.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 12/17/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 0365a60317538ba31f39928cd30a57e2c969c832
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723034"
---
# <a name="azure-services-that-support-availability-zones"></a>Services Azure prenant en charge les zones de disponibilité

Les zones de disponibilité sont une offre à haute disponibilité qui protège vos applications et vos données contre les défaillances des centres de données. Pour la liste des régions existantes et à venir qui sont compatibles avec Zones de disponibilité, consultez [Régions et Zones de disponibilité dans Azure](az-overview.md).  

Cette section répertorie les services Azure qui prennent en charge les Zones de disponibilité. 

Pour voir les services disponibles dans chaque région, ainsi que la feuille de route à venir pour la disponibilité, consultez [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/).

Tous les services de gestion Azure sont conçus pour résister aux défaillances survenant au niveau de la région. En termes de portée, une ou plusieurs défaillances de zone de disponibilité survenant dans une région présentent un rayon de défaillance plus réduit qu’une région dans son ensemble. Azure peut récupérer suite à la défaillance de services de gestion au niveau de la zone au sein d’une région. Azure procède à des opérations de maintenance critique dans une seule zone à la fois au sein d’une région, afin d’empêcher les défaillances d’affecter les ressources client déployées sur les différentes zones de disponibilité d’une région.


![affichage conceptuel d’une région Azure avec 3 zones](./media/az-region/azure-region-availability-zones.png)


Les services Azure qui prennent en charge les Zones de disponibilité se répartissent en trois catégories : **zonaux**, **redondants interzone** et **non régionaux**. Les charges de travail client peuvent être catégorisées pour utiliser un de ces scénarios d’architecture afin de répondre aux exigences de performances et de durabilité des applications.

- **Services par zone** : une ressource peut être déployée dans une Zone de disponibilité spécifique auto-sélectionnée pour répondre à des exigences plus rigoureuses en matière de latence et de performances.  La résilience est auto-architecturée en répliquant des applications et des données dans une ou plusieurs zones à l’intérieur de la région.  Les ressources peuvent être affectées à une zone en particulier. Vous pouvez par exemple épingler machines virtuelles, des disques managés ou des adresses IP standard à une zone, ce qui permet d’obtenir une résilience accrue en faisant en sorte qu’une ou plusieurs instances de ressources soient réparties entre les zones.

- **Services redondants dans une zone**  : la plateforme Azure réplique la ressource et les données entre les zones.  Microsoft assure la haute disponibilité, car Azure réplique et distribue automatiquement les instances au sein de la région.  Par exemple, ZRS réplique les données dans trois zones, de sorte que la défaillance d’une zone n’a pas d’incidence sur la haute disponibilité des données. 

- **Services non régionaux**  : services qui ne dépendent pas d’une région Azure spécifique, ce qui les rend résilients aux pannes au niveau de la zone et aux pannes au niveau de la région.


Pour obtenir la continuité complète des activités sur Azure, générez votre architecture d’applications à l’aide de la combinaison des Zones de disponibilité et des paires de régions Azure. Vous pouvez effectuer une réplication synchrone de vos applications et données à l’aide des Zones de la disponibilité d’une région Azure pour répliquer en haute disponibilité et de façon asynchrone entre les régions Azure pour la protection de la récupération d’urgence. Pour en savoir plus, consultez [Création de solutions pour la haute disponibilité à l’aide de Zones de disponibilité](https://docs.microsoft.com/azure/architecture/high-availability/building-solutions-for-high-availability). 


### <a name="azure-services-supporting-availability-zones"></a>Services Azure prenant en charge les Zones de disponibilité

- Les machines virtuelles de génération plus ancienne ne sont pas répertoriées ici. Pour plus d’informations, consultez [Tailles de machines virtuelles des générations précédentes](../virtual-machines/sizes-previous-gen.md).

- Certains services ne sont pas de niveau régional, consultez [Régions et zones de disponibilité dans Azure](az-overview.md) pour plus d’informations. Ces services qui ne dépendent pas d’une région Azure spécifique, ce qui les rend résilients aux pannes au niveau de la zone et aux pannes au niveau de la région.  La liste des services non régionaux se trouve dans [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/).



## <a name="americas"></a>Amérique

| **Produits** | **USA Centre** | **USA Est** | **USA Est 2** | **USA Ouest 2** | **Centre du Canada** |
|--|--|--|--|--|--|
| **Calcul** |  |  |  |  |  |
| [App Service Environments (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Groupes de machines virtuelles identiques](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Machines virtuelles](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 
| **Containers** |  |  |  |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Container Registry](../container-registry/zone-redundancy.md) |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  | 
| **Stockage** |  |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Stockage de fichiers Premium](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Stockage Blob](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Disques managés](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Mise en réseau** |  |  |  |  |  |
| [Application Gateway V2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express Route](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Pare-feu Azure](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Adresse IP standard](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Équilibreur de charge](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Réseau virtuel](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service NAT de réseau virtuel](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Passerelle VPN](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bases de données** |  |  |  |  |  |
| [Cache Azure pour Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Explorateur de données Azure](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Serveurs flexibles Azure Database pour MySQL](../mysql/flexible-server/concepts-high-availability.md) | :x: | :x: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| [Azure Database pour PostgreSQL - Serveur flexible](../postgresql/flexible-server/overview.md) | :x: | :x: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| [Azure SQL Database (niveau Usage général)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | :x: | :heavy_check_mark:(Preview) | :heavy_check_mark:(Preview) | :heavy_check_mark:(Preview) | :x: |
| [Azure SQL Database (niveaux Critique pour l'entreprise et Premium)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analyse** |  |  |  |  |  |
| [Hubs d'événements](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Intégration** |  |  |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Gestion et gouvernance** |  |  |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| **Sécurité** |  |  |  |  |  |
| [Services de domaine Azure Active Directory](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="europe"></a>Europe

| **Produits** | **France Centre** | **Europe Nord** | **Sud du Royaume-Uni** | **Europe Ouest** |
|--|--|--|--|--|
| **Calcul** |  |  |  |  |
| [App Service Environments (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Groupes de machines virtuelles identiques](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Machines virtuelles](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Stockage** |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Stockage de fichiers Premium](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Stockage Blob](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Disques managés](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Mise en réseau** |  |  |  |  |
| [Application Gateway V2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express Route](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Pare-feu Azure](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Adresse IP standard](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Équilibreur de charge](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Réseau virtuel](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service NAT de réseau virtuel](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Passerelle VPN](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bases de données** |  |  |  |  |
| [Cache Azure pour Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Explorateur de données Azure](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Serveurs flexibles Azure Database pour MySQL](../mysql/flexible-server/concepts-high-availability.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database pour PostgreSQL - Serveur flexible](../postgresql/flexible-server/overview.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database (niveau Usage général)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | :x: | :heavy_check_mark:(Preview) | :x: | :heavy_check_mark:(Preview) |
| [Azure SQL Database (niveaux Critique pour l'entreprise et Premium)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analyse** |  |  |  |  |
| [Hubs d'événements](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Intégration** |  |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Gestion et gouvernance** |  |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | :heavy_check_mark: | :x: | :heavy_check_mark: |
| **Sécurité** |  |  |  |  |
| [Services de domaine Azure Active Directory](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="asia-pacific"></a>Asie-Pacifique



| **Produits** | **Japon Est** | **Asie Sud-Est** | **Australie Est** |
|--|--|--|--|
| **Calcul** |  |  |  |
| [App Service Environments (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Groupes de machines virtuelles identiques](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Machines virtuelles](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Stockage** |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Stockage de fichiers Premium](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |
| [Stockage Blob](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Disques managés](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Mise en réseau** |  |  |  |
| [Application Gateway V2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express Route](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Pare-feu Azure](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Adresse IP standard](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Équilibreur de charge](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Réseau virtuel](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service NAT de réseau virtuel](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Passerelle VPN](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bases de données** |  |  |  |
| [Cache Azure pour Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Explorateur de données Azure](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Serveurs flexibles Azure Database pour MySQL](../mysql/flexible-server/concepts-high-availability.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database pour PostgreSQL - Serveur flexible](../postgresql/flexible-server/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database (niveau Usage général)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | :x: | :heavy_check_mark:(Preview) | :heavy_check_mark:(Preview) |
| [Azure SQL Database (niveaux Critique pour l'entreprise et Premium)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analyse** |  |  |  |
| [Hubs d'événements](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Intégration** |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Gestion et gouvernance** |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | :x: | :x: |
| **Sécurité** |  |  |  |
| [Services de domaine Azure Active Directory](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: |  |


## <a name="upcoming-availability-zones"></a>Zones de disponibilité à venir 

Azure propose les zones de disponibilité dans les régions suivantes :
- Gouvernement américain - Virginie
- Afrique du Sud Nord
- États-Unis - partie centrale méridionale
- Allemagne Centre-Ouest

La liste des régions existantes et à venir compatibles avec les Zones de disponibilité est disponible [ici](https://azure.microsoft.com/global-infrastructure/geographies/).    

Pour en savoir plus sur la prise en charge de Zones de disponibilité dans ces régions, contactez votre représentant commercial ou client Microsoft.


## <a name="pricing-for-vms-in-availability-zones"></a>Tarification des machines virtuelles dans les zones de disponibilité

Il n’existe aucun coût supplémentaire pour les machines virtuelles déployées dans une Zone de disponibilité. Pour plus d’informations, consultez la page [Tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Prise en main des Zones de disponibilité

- [Créer une machine virtuelle](../virtual-machines/windows/create-portal-availability-zone.md)
- [Ajouter un disque géré à l’aide de PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Créer un groupe de machines virtuelles identiques redondant interzone](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Machines virtuelles de l’équilibreur dans des zones à l’aide de Load Balancer Standard avec un serveur frontal redondant interzone](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-cli?tabs=option-1-create-load-balancer-standard)
- [Machines virtuelles de l’équilibreur de charge dans une zone à l’aide de Load Balancer Standard avec un serveur frontal zonal](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-cli?tabs=option-1-create-load-balancer-standard)
- [Stockage redondant interzone](../storage/common/storage-redundancy.md)
- [Niveau Usage général de SQL Database](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Géo-reprise d’activité après sinistre Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Géo-reprise d’activité après sinistre Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Créer une passerelle de réseau virtuel redondante interzone](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Ajouter une région redondante interzone pour Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Prise en main des zones de disponibilité du Cache Azure pour Redis](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Créer une instance Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)
- [Créer un cluster Azure Kubernetes Service (AKS) qui utilise des zones de disponibilité](../aks/availability-zones.md)


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Régions et Zones de disponibilité dans Azure](az-overview.md)
