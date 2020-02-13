---
title: Qu’est-ce que Liaison privée Azure ?
description: Découvrez comment utiliser le service Liaison privée Azure pour accéder aux services PaaS Azure, comme Stockage Azure et SQL Database, ainsi qu’aux services de partenaires ou de clients hébergés par Azure sur un point de terminaison privé dans votre réseau virtuel.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 01/09/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 810f2b376e9983743617dae77d74d596a7fbe663
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084184"
---
# <a name="what-is-azure-private-link-preview"></a>Qu’est-ce que Liaison privée Azure ? (Préversion)
Azure Private Link vous permet d’accéder aux services PaaS Azure (par exemple Stockage Azure, Azure Cosmos DB et SQL Database) ainsi qu’aux services de partenaires ou de clients hébergés par Azure sur un [Point de terminaison privé](private-endpoint-overview.md) dans votre réseau virtuel. Le trafic entre votre réseau virtuel et le service transite par le réseau principal de Microsoft, éliminant ainsi toute exposition à l’Internet public. Vous pouvez également créer votre propre [service Liaison privée](private-link-service-overview.md) dans votre réseau virtuel et le distribuer en privé à vos clients. L’expérience de configuration et de consommation à l’aide du service Liaison privée Azure est cohérente entre les services PaaS Azure, appartenant au client et de partenaires partagés.

> [!IMPORTANT]
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Pour en savoir plus sur les limitations connues, consultez [Point de terminaison privé](private-endpoint-overview.md#limitations) et [Service Liaison privée](private-link-service-overview.md#limitations).


![Vue d’ensemble du point de terminaison privé](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Principaux avantages
Le service Liaison privée Azure offre les avantages suivants :  
- **Accès en privé aux services sur la plateforme Azure** : Connectez votre réseau virtuel à des services s’exécutant dans Azure en privé sans avoir besoin d’une adresse IP publique au niveau de la source ou de la destination. Les fournisseurs de services peuvent afficher leurs services en privé dans leur propre réseau virtuel et les consommateurs peuvent accéder à ces services en privé dans leur réseau virtuel local. La plateforme Liaison privée gère la connectivité entre le consommateur et les services sur le réseau principal Azure. 
 
- **Réseaux locaux et appairés** : Accédez aux services s’exécutant dans Azure à partir d’un réseau local par le biais de l’appairage privé ExpressRoute/tunnels VPN et de réseaux virtuels appairés à l’aide de points de terminaison privés. Il n’est pas nécessaire de configurer l’appairage public ni de transiter par Internet pour atteindre le service. Cette fonctionnalité offre un moyen sécurisé de migrer des charges de travail vers Azure.
 
- **Protection contre l’exfiltration de données** :  Avec le service Liaison privée Azure, le point de terminaison privé du réseau virtuel est mappé à une instance spécifique de la ressource PaaS du client, par opposition à l’ensemble du service. Le point de terminaison privé permet aux clients de se connecter uniquement à la ressource spécifique et non à une autre ressource du service. Ce mécanisme intégré offre une protection contre les risques liés à l’exfiltration des données. 
 
- **Global Reach** : Connectez-vous en privé à des services s’exécutant dans d’autres régions. Cela signifie que le réseau virtuel du consommateur peut se trouver dans la région A et qu’il peut se connecter aux services qui se trouvent derrière Liaison privée dans la région B.  
 
- **Extension à vos propres services** : Tirez parti des mêmes expérience et fonctionnalités pour afficher votre propre service en privé aux consommateurs dans Azure. En plaçant votre service derrière Standard Load Balancer, vous pouvez l’activer pour Liaison privée. Le consommateur peut alors se connecter directement à votre service à l’aide d’un point de terminaison privé dans son propre réseau virtuel. Vous pouvez gérer ces demandes de connexion à l’aide d’un simple flux d’appels d’approbation. Le service Liaison privée Azure fonctionne également pour les consommateurs et services appartenant à différents locataires Active Directory. 

## <a name="availability"></a>Disponibilité 
 Le tableau suivant liste les services Liaison privée et les régions où ils sont disponibles. 

|Scénario  |Services pris en charge  |Régions disponibles | Statut  |
|:---------|:-------------------|:-----------------|:--------|
|Liaison privée pour les services appartenant au client|Services Liaison privée derrière Standard Load Balancer | Toutes les régions publiques  | PRÉVERSION  |
|Liaison privée pour les services PaaS Azure   | Stockage Azure        |  Toutes les régions publiques      | PRÉVERSION <br/> [Plus d’informations](/azure/storage/common/storage-private-endpoints)  |
|  | Azure Data Lake Storage Gen2        |  Toutes les régions publiques      | PRÉVERSION <br/> [Plus d’informations](/azure/storage/common/storage-private-endpoints)  |
|  |  Azure SQL Database         | Toutes les régions publiques      |   PRÉVERSION      |
||Azure SQL Data Warehouse.| Toutes les régions publiques |PRÉVERSION|
||Azure Cosmos DB| USA Centre-Ouest, USA Ouest, USA Centre Nord |PRÉVERSION|
|  |  Azure Database pour PostgreSQL - Serveur unique         | Toutes les régions publiques      |   PRÉVERSION      |
|  |  Azure Database pour MySQL         | Toutes les régions publiques      |   PRÉVERSION      |
|  |  Azure Database for MariaDB         | Toutes les régions publiques      |   PRÉVERSION      |
|  |  Azure Key Vault         | Toutes les régions publiques      |   PRÉVERSION      |

Pour obtenir les notifications les plus récentes, consultez la [page relative aux mises à jour du réseau virtuel Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="logging-and-monitoring"></a>Enregistrement et surveillance

Le service Liaison privée Azure est intégré à Azure Monitor, ce qui vous permet d’archiver les journaux dans un compte de stockage, de transmettre des événements à votre hub d’événements ou de les envoyer à des journaux Azure Monitor. Vous pouvez accéder aux informations suivantes sur Azure Monitor : 
- **Point de terminaison privé** : Données traitées par le point de terminaison privé (entrée/sortie)
 
- **Service Liaison privée** :
    - Données traitées par le service Liaison privée (entrée/sortie)
    - Disponibilité du port NAT  
 
## <a name="pricing"></a>Tarifs   
Pour plus d’informations sur les tarifs, consultez [Tarification Liaison privée Azure](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>FAQ  
Pour examiner les questions fréquentes, consultez [FAQ sur Liaison privée Azure](private-link-faq.md).
 
## <a name="limits"></a>limites  
Pour connaître les limites, consultez [Limites de Liaison privée Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="next-steps"></a>Étapes suivantes
- [Créer un point de terminaison privé pour un serveur SQL Database à l’aide du portail](create-private-endpoint-portal.md)
- [Créer un point de terminaison privé pour un serveur SQL Database à l’aide de PowerShell](create-private-endpoint-powershell.md)
- [Créer un point de terminaison privé pour un serveur SQL Database à l’aide de l’interface CLI](create-private-endpoint-cli.md)
- [Créer un point de terminaison privé pour un compte de stockage à l’aide du portail](create-private-endpoint-storage-portal.md)
- [Créer un point de terminaison privé pour un compte Cosmos Azure à l’aide du portail](../cosmos-db/how-to-configure-private-endpoints.md)
- [Créer votre propre service Liaison privée à l’aide d’Azure PowerShell](create-private-link-service-powershell.md)


 
