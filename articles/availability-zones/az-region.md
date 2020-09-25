---
title: Régions prenant en charge les Zones de disponibilité dans Azure
description: Pour créer des applications hautement disponibles et résilientes dans Azure, les zones de disponibilité fournissent des emplacements physiquement distincts que vous pouvez utiliser pour exécuter vos ressources.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 09/18/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: d7a158d91295aedc14f1f913ae152c496066fab5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90891686"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>Régions prenant en charge les Zones de disponibilité dans Azure

Les Zones de disponibilité constituent une offre à haute disponibilité qui protège vos applications et données contre les pannes des centres de données. Pour plus d’informations sur les Zones de disponibilité, consultez [Régions et Zones de disponibilité dans Azure](az-overview.md).

Cette section répertorie les régions et services Azure qui prennent en charge les Zones de disponibilité.

Pour voir les services disponibles dans chaque région, ainsi que la feuille de route à venir pour la disponibilité, consultez [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/).

## <a name="americas"></a>Amérique

| Service | USA Centre | USA Est | USA Est 2 | USA Ouest 2 | Centre du Canada
| --- | :---: | :---: | :---: | :---: | :---: |
| **Calcul** |  |  |  |  |
| Machines virtuelles Linux             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Machines virtuelles Windows           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Virtual Machine Scale Sets         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ILB Azure App Service Environments | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Kubernetes Service           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Stockage** |  |  |  |  |
| Disques managés                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Stockage redondant interzone             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Mise en réseau** |  |  |  |  |
| Adresse IP standard                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Standard Load Balancer             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Passerelle VPN                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Passerelle ExpressRoute               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Application Gateway (v2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Pare-feu Azure                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bases de données** |  |  |  |  |
| Explorateur de données Azure                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| SQL Database                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: (Préversion) | :heavy_check_mark: |
| Cache Azure pour Redis              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analyse** |  |  |  |  |
| Event Hubs                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Intégration** |  |  |  |  |
| Service Bus (Niveau Premium uniquement)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Event Grid                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Identité** |  |  |  |  |
| Services de domaine Azure AD           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |   |

## <a name="europe"></a>Europe

| Service | France Centre | Europe Nord | Sud du Royaume-Uni | Europe Ouest |
| --- | :---: | :---: | :---: | :---: |
| **Calcul** |  |  |  |  |
| Machines virtuelles Linux             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Machines virtuelles Windows           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Virtual Machine Scale Sets         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ILB Azure App Service Environments | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Kubernetes Service           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Stockage** |  |  |  |  |
| Disques managés                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Stockage redondant interzone             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Mise en réseau** |  |  |  |  |
| Adresse IP standard                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Standard Load Balancer             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Passerelle VPN                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Passerelle ExpressRoute               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Application Gateway (v2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Pare-feu Azure                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bases de données** |  |  |  |  |
| Explorateur de données Azure                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| SQL Database                       | :heavy_check_mark: | :heavy_check_mark: (Préversion) | :heavy_check_mark: | :heavy_check_mark: |
| Cache Azure pour Redis              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analyse** |  |  |  |  |
| Event Hubs                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Intégration**  |  |  |  |  |
| Service Bus (Niveau Premium uniquement)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Event Grid                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Identité** |  |  |  |  |
| Services de domaine Azure AD           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="asia-pacific"></a>Asie-Pacifique

| Service | Japon Est | Asie Sud-Est | Australie Est |
| --- | :---: | :---: | :---: |
| **Calcul** |  |  |  |
| Machines virtuelles Linux             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Machines virtuelles Windows           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Virtual Machine Scale Sets         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ILB Azure App Service Environments | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Kubernetes Service           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Stockage** |  |  |  |
| Disques managés                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Stockage redondant interzone             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Mise en réseau** |  |  |  |
| Adresse IP standard                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Standard Load Balancer             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Passerelle VPN                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Passerelle ExpressRoute               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Application Gateway (v2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Pare-feu Azure                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bases de données** |  |  |  |
| Explorateur de données Azure                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| SQL Database                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Cache Azure pour Redis              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analyse** |  |  |  |
| Event Hubs                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Intégration** |  |  |  |
| Service Bus (Niveau Premium uniquement)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Event Grid                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Identité** |  |  |  |
| Services de domaine Azure AD           | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="other"></a>Autres

Azure offre également une prise en charge Zones de disponibilité dans les régions suivantes :

- Gouvernement américain - Virginie
- Afrique du Sud Nord
- États-Unis - partie centrale méridionale

Pour en savoir plus sur la prise en charge de Zones de disponibilité dans ces trois régions, contactez votre représentant commercial ou client Microsoft.

## <a name="next-steps"></a>Étapes suivantes

- [Régions et Zones de disponibilité dans Azure](az-overview.md)
