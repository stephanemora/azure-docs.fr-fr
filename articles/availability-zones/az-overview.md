---
title: Que sont les zones de disponibilité Azure ?
description: Pour créer des applications hautement disponibles et résilientes dans Azure, les zones de disponibilité fournissent des emplacements physiquement distincts que vous pouvez utiliser pour exécuter vos ressources.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 10/17/2019
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: e7ba62a96a6fc76ad63960cfe57f5b1f85589c32
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085040"
---
# <a name="what-are-availability-zones-in-azure"></a>Que sont les zones de disponibilité dans Azure ?
Les Zones de disponibilité constituent une offre à haute disponibilité qui protège vos applications et données contre les pannes des centres de données. Les Zones de disponibilité sont des emplacements physiques uniques au sein d’une région Azure. Chaque zone de disponibilité est composée d’un ou de plusieurs centres de données équipés d’une alimentation, d’un système de refroidissement et d’un réseau indépendants. Pour garantir la résilience, un minimum de trois zones distinctes sont activées dans toutes les régions. La séparation physique des Zones de disponibilité dans une région protège les applications et les données des défaillances dans le centre de données. Les services redondants interzone répliquent vos applications et données entre des Zones de disponibilité pour les protéger contre des points uniques de panne. Avec les Zones de disponibilité, Azure propose des contrats de niveau de service de durée de fonctionnement des machines virtuelles de pointe de 99,99 %. La version complète du [contrat SLA Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explique la disponibilité garantie d’Azure dans son ensemble.

Une zone de disponibilité dans une région Azure est une combinaison d’un domaine d’erreur et d’un domaine de mise à jour. Par exemple, si vous créez trois ou plusieurs machines virtuelles dans trois zones d’une région Azure, vos machines virtuelles sont efficacement réparties sur trois domaines d’erreur et trois domaines de mise à jour. La plateforme Azure reconnaît cette répartition entre les domaines de mise à jour pour vous assurer que les machines virtuelles des différentes zones ne sont pas mises à jour en même temps.

Générez la haute disponibilité dans votre architecture d’applications par la colocalisation de vos ressources de calcul, de stockage, de mise en réseau et de données dans une zone et une réplication dans d’autres zones. Les services Azure qui prennent en charge les Zones de disponibilité sont classés en deux catégories :

- **Services zonaux** : vous épinglez la ressource à une zone spécifique (par exemple des machines virtuelles, des disques managés, des adresses IP standard) ou
- **Services redondants interzone** : la plateforme effectue automatiquement la réplication entre des zones (par exemple, stockage redondant interzone, SQL Database).

Pour obtenir la continuité complète des activités sur Azure, générez votre architecture d’applications à l’aide de la combinaison des Zones de disponibilité et des paires de régions Azure. Vous pouvez effectuer une réplication synchrone de vos applications et données à l’aide des Zones de la disponibilité d’une région Azure pour répliquer en haute disponibilité et de façon asynchrone entre les régions Azure pour la protection de la récupération d’urgence.
 
![affichage conceptuel d’une zone devenant indisponible dans une région](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Les identificateurs de zones de disponibilité (les nombres 1, 2 et 3 dans l’image ci-dessus) sont mappés logiquement aux zones physiques réelles pour chaque abonnement indépendamment. Cela signifie que la zone de disponibilité 1 dans un abonnement donné peut correspondre à une zone physique différente de la zone de disponibilité 1 dans un autre abonnement. C’est pourquoi il est déconseillé de se fier aux identificateurs de zones de disponibilité entre différents abonnements pour le placement des machines virtuelles.

## <a name="services-support-by-region"></a>Prise en charge des services par région

Les combinaisons de régions et de services Azure qui prennent en charge les zones de disponibilité sont les suivantes :


|                                 |Amérique |              |           |           | Europe |              |          |              | Asie-Pacifique |                 |                |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|-------------|
|          |USA Centre|USA Est|USA Est 2|USA Ouest 2|France Centre|Europe Nord|Sud du Royaume-Uni|Europe Ouest|Japon Est|Asie Sud-Est|Australie Est|
| **Calcul**                         |            |              |           |           |                |              |          |             |            |                |                |
| Machines virtuelles Linux          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Machines virtuelles Windows        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Virtual Machine Scale Sets      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| ILB Azure App Service Environments | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Azure Kubernetes Service        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Stockage**   |            |              |           |           |                |              |          |             |            |                |                |
| Disques managés                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Stockage redondant interzone          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Mise en réseau**                     |            |              |           |           |                |              |          |             |            |                |                |
| Adresse IP standard        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Standard Load Balancer     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Passerelle VPN            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Passerelle ExpressRoute   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Application Gateway (V2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Pare-feu Azure           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| **Bases de données**                     |            |              |           |           |                |              |          |             |            |                |                |
| Explorateur de données Azure                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |                |
| SQL Database                    | &#10003;   | &#10003;     | &#10003;  | &#10003;(Préversion) | &#10003;       | &#10003;(Préversion)     | &#10003; | &#10003;    | &#10003;       | &#10003;       |&#10003;        |
| Cache Azure pour Redis           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| Azure Cosmos DB                    | &#10003;   |  &#10003;  |  &#10003; | &#10003; |       |     | &#10003; |  &#10003;   |            | &#10003;       |                |
| **Analyse**                       |            |              |           |           |                |              |          |             |            |                |                |
| Event Hubs                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |                |
| **Intégration**                     |            |              |           |           |                |              |          |             |            |                |                |
| Service Bus (Niveau Premium uniquement) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| Event Grid | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| **Identité**                     |            |              |           |           |                |              |          |             |            |                |                |
| Services de domaine Azure AD | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |

## <a name="services-resiliency"></a>Résilience des services
Tous les services de gestion Azure sont conçus pour résister aux défaillances survenant au niveau de la région. En termes de portée, une ou plusieurs défaillances de zone de disponibilité survenant dans une région présentent un rayon de défaillance plus réduit qu’une région dans son ensemble. Azure peut récupérer suite à la défaillance de services de gestion au niveau de la zone au sein de la région, ou d’une autre région Azure. Azure procède à des opérations de maintenance critique dans une seule zone à la fois au sein d’une région, afin d’empêcher les défaillances d’affecter les ressources client déployées sur les différentes zones de disponibilité d’une région.

## <a name="pricing"></a>Tarifs
Il n’existe aucun coût supplémentaire pour les machines virtuelles déployées dans une Zone de disponibilité. Un contrat de niveau de service des machines virtuelles de 99,99 % est offert lorsque deux machines virtuelles ou plus sont déployées sur deux Zones de disponibilité ou plus au sein d’une région Azure. Il y aura des frais supplémentaires de transfert des données de machine virtuelle à machine virtuelle entre les Zones de disponibilité. Pour plus d'informations, consultez la page [Tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Prise en main des Zones de disponibilité
- [Créer une machine virtuelle](../virtual-machines/windows/create-portal-availability-zone.md)
- [Ajouter un disque géré à l’aide de PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Créer un groupe de machines virtuelles identiques redondant interzone](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Machines virtuelles de l’équilibreur dans des zones à l’aide de Load Balancer Standard avec un serveur frontal redondant interzone](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Machines virtuelles de l’équilibreur de charge dans une zone à l’aide de Load Balancer Standard avec un serveur frontal zonal](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Stockage redondant interzone](../storage/common/storage-redundancy-zrs.md)
- [Base de données SQL](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Géo-reprise d’activité après sinistre Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Géo-reprise d’activité après sinistre Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Créer une passerelle de réseau virtuel redondante interzone](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Ajouter une région redondante interzone pour Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Prise en main des zones de disponibilité du Cache Azure pour Redis](https://aka.ms/redis/az/getstarted)
- [Créer une instance Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Créer un cluster Azure Kubernetes Service (AKS) qui utilise des zones de disponibilité](../aks/availability-zones.md)

## <a name="next-steps"></a>Étapes suivantes
- [Modèles de démarrage rapide](https://aka.ms/azqs)
