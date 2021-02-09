---
title: Services Azure prenant en charge les zones de disponibilité
description: Pour créer des applications hautement disponibles et résilientes dans Azure, les zones de disponibilité fournissent des emplacements physiquement distincts que vous pouvez utiliser pour exécuter vos ressources.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 01/26/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 3916cd43a42ef273a45a9992b45d8b9f043c9364
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226622"
---
# <a name="azure-services-that-support-availability-zones"></a>Services Azure prenant en charge les zones de disponibilité

L’infrastructure globale de Microsoft Azure est conçue et construite à chaque niveau pour fournir les plus hauts niveaux de redondance et de résilience aux clients. L’infrastructure Azure est composée de zones géographiques, de régions et de Zones de disponibilité, qui limitent le rayon d’impact d’une défaillance et, par conséquent, l’incidence potentielle de celle-ci sur les applications et les données des clients. La construction Zones de disponibilité Azure a été développée pour fournir une solution logicielle et de mise en réseau permettant de se protéger contre des défaillances de centre de données, et d’offrir une haute disponibilité accrue à nos clients.

Les Zones de disponibilité sont des emplacements physiques uniques au sein d’une région Azure. Chaque zone est composée d’un ou de plusieurs centres de données équipés d’une alimentation, d’un système de refroidissement et d’un réseau indépendants. La séparation physique des Zones de disponibilité au sein d’une région limite l’impact sur les applications et les données de défaillances de zone, telles que des inondations à grande échelle, des ouragans et raz de marée majeurs, et d’autres événements susceptibles de perturber l’accès au site, le passage en toute sécurité, la durée de bon fonctionnement des utilitaires étendus et la disponibilité des ressources. Les Zones de disponibilité et leurs centres de centres associés sont conçus de telle sorte que, si une zone est compromise, les services, la capacité et la disponibilité sont assurés par les autres Zones de disponibilité de la région.

Tous les services de gestion Azure sont conçus pour résister aux défaillances survenant au niveau de la région. En termes de portée, une ou plusieurs défaillances de zone de disponibilité survenant dans une région présentent un rayon de défaillance plus réduit qu’une région dans son ensemble. Azure peut récupérer suite à la défaillance de services de gestion au niveau de la zone au sein d’une région. Azure procède à des opérations de maintenance critique dans une seule zone à la fois au sein d’une région, afin d’empêcher les défaillances d’affecter les ressources client déployées sur les différentes zones de disponibilité d’une région.


![affichage conceptuel d’une région Azure avec 3 zones](./media/az-region/azure-region-availability-zones.png)


Les services Azure qui prennent en charge les Zones de disponibilité se répartissent en trois catégories : **zonaux**, **redondants interzone** et **non régionaux**. Les charges de travail client peuvent être catégorisées pour utiliser un de ces scénarios d’architecture afin de répondre aux exigences de performances et de durabilité des applications.

- **Services par zone** : une ressource peut être déployée dans une Zone de disponibilité spécifique auto-sélectionnée pour répondre à des exigences plus rigoureuses en matière de latence et de performances.  La résilience est auto-architecturée en répliquant des applications et des données dans une ou plusieurs zones à l’intérieur de la région.  Les ressources peuvent être affectées à une zone en particulier. Vous pouvez par exemple épingler machines virtuelles, des disques managés ou des adresses IP standard à une zone, ce qui permet d’obtenir une résilience accrue en faisant en sorte qu’une ou plusieurs instances de ressources soient réparties entre les zones.

- **Services redondants dans une zone**  : la plateforme Azure réplique la ressource et les données entre les zones.  Microsoft assure la haute disponibilité, car Azure réplique et distribue automatiquement les instances au sein de la région.  Par exemple, ZRS réplique les données dans trois zones, de sorte que la défaillance d’une zone n’a pas d’incidence sur la haute disponibilité des données. 

- **Services non régionaux** : les services sont toujours disponibles à partir de zones géographiques Azure et sont résilients aux pannes à l’échelle de la zone ainsi qu’aux pannes à l’échelle de la région. 


Pour obtenir la continuité complète des activités sur Azure, générez votre architecture d’applications à l’aide de la combinaison des Zones de disponibilité et des paires de régions Azure. Vous pouvez effectuer une réplication synchrone de vos applications et données à l’aide des Zones de la disponibilité d’une région Azure pour répliquer en haute disponibilité et de façon asynchrone entre les régions Azure pour la protection de la récupération d’urgence. Pour en savoir plus, consultez [Création de solutions pour la haute disponibilité à l’aide de Zones de disponibilité](/azure/architecture/high-availability/building-solutions-for-high-availability). 

## <a name="azure-services-supporting-availability-zones"></a>Services Azure prenant en charge Zones de disponibilité

 - Les machines virtuelles de génération antérieure ne sont pas répertoriées. Pour plus d’informations, voir [Tailles de machines virtuelles des générations précédentes](../virtual-machines/sizes-previous-gen.md).
 - Comme mentionné dans [Régions et Zones de disponibilité dans Azure](az-overview.md), certains services ne sont pas régionaux. Ces services ne dépendant pas d’une région Azure spécifique, ils sont résilients aux pannes au niveau de la zone ainsi qu’aux pannes au niveau de la région.  La liste des services non régionaux se trouve dans [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/).


## <a name="azure-regions-with-availability-zones"></a>Régions Azure avec Zones de disponibilité


| Amérique           | Europe               | Afrique              | Asie-Pacifique   |
|--------------------|----------------------|---------------------|----------------|
|                    |                      |                     |                |
| Centre du Canada     | France Centre       | Afrique du Sud Nord* | Japon Est     |
| USA Centre         | Allemagne Centre-Ouest |                     | Asie Sud-Est |
| USA Est            | Europe Nord         |                     | Australie Est |
| USA Est 2          | Sud du Royaume-Uni             |                     |                |
| USA Centre Sud | Europe Ouest          |                     |                |
| US Gov Virginie* |                      |                     |                |
| USA Ouest 2        |                      |                     |                |


\* Pour en savoir plus sur Zones de disponibilité et sur la prise en charge des services disponibles dans ces régions, contactez votre représentant commercial ou client Microsoft. Pour les régions à venir qui prendront en charge Zones de disponibilité, consultez [Zones géographiques Azure](https://azure.microsoft.com/en-us/global-infrastructure/geographies/).


## <a name="azure-services-supporting-availability-zones"></a>Services Azure prenant en charge les Zones de disponibilité

- Les machines virtuelles de génération plus ancienne ne sont pas répertoriées ici. Pour plus d’informations, consultez [Tailles de machines virtuelles des générations précédentes](../virtual-machines/sizes-previous-gen.md).

- Certains services ne sont pas de niveau régional, consultez [Régions et zones de disponibilité dans Azure](az-overview.md) pour plus d’informations. Ces services qui ne dépendent pas d’une région Azure spécifique, ce qui les rend résilients aux pannes au niveau de la zone et aux pannes au niveau de la région.  La liste des services non régionaux se trouve dans [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/).


### <a name="zone-resilient-services"></a>Services résilients dans une zone 

:globe_with_meridians: Services non régionaux : les services sont toujours disponibles à partir de zones géographiques Azure et sont résilients aux pannes à l’échelle de la zone ainsi qu’aux pannes à l’échelle de la région.

:large_blue_diamond:   Résilience aux pannes à l’échelle de la zone 

**Services fondamentaux**

|     Produits                                                    | Résilience             |
|-----------------------------------------------------------------|:----------------------------:|
|     Compte de stockage                                           | :large_blue_diamond:  |
|     Application Gateway (v2)                                  | :large_blue_diamond:  |
|     Sauvegarde Azure                                                | :large_blue_diamond:  |
|     Azure Cosmos DB                                           | :large_blue_diamond:  |
|     Azure Data Lake Storage Gen2                             | :large_blue_diamond:  |
|     Azure Express Route                                       | :large_blue_diamond:  |
|     IP publique                                           | :large_blue_diamond:  |
|     Azure SQL Database (niveau Usage général)                 | :large_blue_diamond:  |
|     Azure SQL Database (niveaux Critique pour l’entreprise et Premium)     | :large_blue_diamond:  |
|     Stockage sur disque                                                | :large_blue_diamond:  |
|     Event Hubs                                                  | :large_blue_diamond:  |
|     Key Vault                                                   | :large_blue_diamond:  |
|     Load Balancer                                               | :large_blue_diamond:  |
|     Service Bus                                                 | :large_blue_diamond:  |
|     Service Fabric                                            | :large_blue_diamond:  |
|     Stockage :   Niveaux de stockage BLOB chaud/froid                      | :large_blue_diamond:  |
|     Stockage :   Disques managés                                    | :large_blue_diamond:  |
|     Virtual Machine Scale Sets                               | :large_blue_diamond:  |
|     Machines Virtuelles                                          | :large_blue_diamond:  |
|     Machines virtuelles : Série Av2                              | :large_blue_diamond:  |
|     Machines virtuelles : Série Bs                               | :large_blue_diamond:  |
|     Machines virtuelles : Série DSv2                             | :large_blue_diamond:  |
|     Machines virtuelles : Série DSv3                             | :large_blue_diamond:  |
|     Machines virtuelles : Série Dv2                              | :large_blue_diamond:  |
|     Machines virtuelles : Série Dv3                              | :large_blue_diamond:  |
|     Machines virtuelles : Série ESv3                             | :large_blue_diamond:  |
|     Machines virtuelles : Série Ev3                              | :large_blue_diamond:  |
|     Réseau virtuel                                           | :large_blue_diamond:  |
|     Passerelle VPN                                                 | :large_blue_diamond:  |


**Services standard**

| Produits                                        | Résilience |
|-------------------------------------------------|:------------:|
| Environnements App Service                        |      :large_blue_diamond:  |
| Azure Active Directory Domain Services          |      :large_blue_diamond:  |
| Azure Bastion                                   |      :large_blue_diamond:  |
| Cache Azure pour Redis                           |      :large_blue_diamond:  |
| Azure Cognitive Services : Analyse de texte        |      :large_blue_diamond:  |
| Explorateur de données Azure                             |      :large_blue_diamond:  |
| Azure Database pour MySQL – Serveur flexible      |      :large_blue_diamond:  |
| Azure Database pour PostgreSQL – Serveur flexible |      :large_blue_diamond:  |
| Azure DDoS Protection                           |      :large_blue_diamond:  |
| Pare-feu Azure                                  |      :large_blue_diamond:  |
| Azure Firewall Manager                          |      :large_blue_diamond:  |
| Azure Kubernetes Service (AKS)                  |      :large_blue_diamond:  |
| Azure Private Link                              |      :large_blue_diamond:  |
| Azure Red Hat OpenShift                         |      :large_blue_diamond:  |
| Azure Site Recovery                             |      :large_blue_diamond:  |
| Container Registry                              |      :large_blue_diamond:  |
| Event Grid                                      |      :large_blue_diamond:  |
| Network Watcher                                 |      :large_blue_diamond:  |
| Power BI Embedded                               |      :large_blue_diamond:  |
| Stockage Blob Premium                            |      :large_blue_diamond:  |
| Machines virtuelles : Série Ddsv4                  |      :large_blue_diamond:  |
| Machines virtuelles : Série Ddv4                   |      :large_blue_diamond:  |
| Machines virtuelles : Série Dsv4                   |      :large_blue_diamond:  |
| Machines virtuelles : Série Dv4                    |      :large_blue_diamond:  |
| Machines virtuelles : Série Edsv4                  |      :large_blue_diamond:  |
| Machines virtuelles : Série Edv4                   |      :large_blue_diamond:  |
| Machines virtuelles : Série Esv4                   |      :large_blue_diamond:  |
| Machines virtuelles : Série Ev4                    |      :large_blue_diamond:  |
| Machines virtuelles : Série Fsv2                   |      :large_blue_diamond:  |
| Machines virtuelles : Série M                      |      :large_blue_diamond:  |
| Virtual WAN                                     |      :large_blue_diamond:  |


**Région non précisée**

|     Produits                                  |     Résilience    |
|-----------------------------------------------|:-------------------:|
|     Azure DNS                                 |     :globe_with_meridians:             |
|     Azure Active Directory                  |     :globe_with_meridians:             |
|     Azure Advisor                             |     :globe_with_meridians:             |
|     Azure Bot Service                        |     :globe_with_meridians:             |
|     Azure Defender pour IoT                  |     :globe_with_meridians:             |
|     Azure Information Protection            |     :globe_with_meridians:             |
|     Azure Lighthouse                        |     :globe_with_meridians:             |
|     Applications managées Azure              |     :globe_with_meridians:             |
|     Azure Maps                                |     :globe_with_meridians:             |
|     Azure Policy                              |     :globe_with_meridians:             |
|     Azure Resource Graph                    |     :globe_with_meridians:             |
|     Azure Stack                               |     :globe_with_meridians:             |
|     Azure Stack Edge                        |     :globe_with_meridians:             |
|     Cloud Shell                               |     :globe_with_meridians:             |
|     Customer Lockbox pour Microsoft Azure    |     :globe_with_meridians:             |
|     Microsoft Azure Peering Service         |     :globe_with_meridians:             |
|     Portail Microsoft Azure                  |     :globe_with_meridians:             |
|     Security Center                         |     :globe_with_meridians:             |
|     Traffic Manager                         |     :globe_with_meridians:             |


## <a name="pricing-for-vms-in-availability-zones"></a>Tarification des machines virtuelles dans les zones de disponibilité

Il n’existe aucun coût supplémentaire pour les machines virtuelles déployées dans une Zone de disponibilité. Pour plus d’informations, consultez la page [Tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Prise en main des Zones de disponibilité

- [Créer une machine virtuelle](../virtual-machines/windows/create-portal-availability-zone.md)
- [Ajouter un disque géré à l’aide de PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Créer un groupe de machines virtuelles identiques redondant interzone](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Machines virtuelles de l’équilibreur dans des zones à l’aide de Load Balancer Standard avec un serveur frontal redondant interzone](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Machines virtuelles de l’équilibreur de charge dans une zone à l’aide de Load Balancer Standard avec un serveur frontal zonal](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Stockage redondant interzone](../storage/common/storage-redundancy.md)
- [Niveau Usage général de SQL Database](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Géo-reprise d’activité après sinistre Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Géo-reprise d’activité après sinistre Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Créer une passerelle de réseau virtuel redondante interzone](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Ajouter une région redondante interzone pour Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Prise en main des zones de disponibilité du Cache Azure pour Redis](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Créer une instance Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Créer un cluster Azure Kubernetes Service (AKS) qui utilise des zones de disponibilité](../aks/availability-zones.md)


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Régions et Zones de disponibilité dans Azure](az-overview.md)
