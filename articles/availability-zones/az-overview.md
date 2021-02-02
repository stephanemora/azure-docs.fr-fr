---
title: Régions et zones de disponibilité dans Azure
description: Apprenez-en davantage sur les régions et les zones de disponibilité dans Azure pour répondre à vos exigences techniques et réglementaires.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 08/27/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: b19f5c3ae0666a0b0e9b0255f848f5924d9d3910
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874737"
---
# <a name="regions-and-availability-zones-in-azure"></a>Régions et zones de disponibilité dans Azure

Les services Microsoft Azure sont disponibles dans le monde entier pour gérer vos opérations cloud à un niveau optimal. Vous pouvez choisir la meilleure région pour vos besoins en fonction des considérations techniques et réglementaires : les fonctionnalités de service, la résidence des données, les exigences de conformité et la latence.

## <a name="terminology"></a>Terminologie

Pour mieux comprendre les régions et les zones de disponibilité dans Azure, il est utile de comprendre les principaux termes ou concepts.

| Terme ou concept | Description |
| --- | --- |
| region | Un ensemble de centres de données déployés dans un périmètre avec une latence définie et connectés via un réseau régional dédié à faible latence. |
| Geography | Une zone du monde contenant au moins une région Azure. Les zones géographiques définissent un marché discret qui préserve la résidence des données et les limites de conformité. Les zones géographiques permettent aux clients ayant des besoins spécifiques en conformité et résidence des données de conserver leurs données et leurs applications à proximité. Les zones géographiques sont tolérantes aux pannes et peuvent résister à une défaillance complète de la région, car elles sont connectées à notre infrastructure réseau haute capacité dédiée. |
| Zone de disponibilité | Emplacements physiques uniques dans une région. Chaque zone de disponibilité est composée d’un ou de plusieurs centres de données équipés d’une alimentation, d’un système de refroidissement et d’un réseau indépendants. |
| région recommandée | Une région qui fournit le plus grand nombre de fonctionnalités de service et est conçue pour prendre en charge les zones de disponibilité maintenant, ou à l’avenir. Celles-ci sont désignées dans le portail Azure avec le libellé **Recommandé**. |
| région alternative (ou autre région) | Une région qui étend l’empreinte d’Azure au sein d’une limite de résidence des données où une région recommandée existe également. Les autres régions vous aident à optimiser la latence et à fournir une deuxième région pour les besoins de récupération d’urgence. Elles ne sont pas conçues pour prendre en charge les zones de disponibilité (bien qu’Azure effectue une évaluation régulière de ces régions pour déterminer si elles doivent devenir des régions recommandées). Celles-ci sont désignées dans le portail Azure par le libellé **Autre**. |
| Services fondamentaux | Un service de base Azure est disponible dans toutes les régions lorsque la région est généralement disponible. |
| Service standard | Un service Azure qui est disponible dans toutes les régions recommandées dans un délai de 12 mois suivant la disponibilité générale de la région/du service ou la disponibilité basée sur la demande dans d’autres régions. |
| Service spécialisé | Un service Azure qui offre une disponibilité basée sur la demande dans les régions soutenues par du matériel personnalisé/spécialisé. |
| Service régional | Un service Azure qui est déployé de manière régionale et permet au client de spécifier la région dans laquelle le service sera déployé. Pour avoir une liste complète, voir [Régions disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| Service non régional | Un service Azure pour lequel il n’existe aucune dépendance vis-à-vis d’une région Azure spécifique. Les services non régionaux sont déployés dans deux régions ou plus et, en cas de défaillance régionale, l’instance du service dans une autre région continue de traiter les clients. Pour avoir une liste complète, voir [Régions disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?products=all). |

## <a name="regions"></a>Régions

Une région est constituée d’un ensemble de centres de données déployés dans un périmètre avec une latence définie et connectés via un réseau régional dédié à faible latence. Azure vous offre la possibilité de déployer des applications là où vous en avez besoin, y compris dans plusieurs régions pour fournir une résilience inter-régions. Pour plus d’informations, consultez la page [Vue d’ensemble du pilier de résilience](/azure/architecture/framework/resiliency/overview).

## <a name="availability-zones"></a>Zones de disponibilité

Une zone de disponibilité est une offre à haute disponibilité qui protège vos applications et vos données contre les défaillances des centres de données. Les Zones de disponibilité sont des emplacements physiques uniques au sein d’une région Azure. Chaque zone de disponibilité est composée d’un ou de plusieurs centres de données équipés d’une alimentation, d’un système de refroidissement et d’un réseau indépendants. Pour garantir la résilience, un minimum de trois zones distinctes sont activées dans toutes les régions. La séparation physique des Zones de disponibilité dans une région protège les applications et les données des défaillances dans le centre de données. Les services redondants interzone répliquent vos applications et données entre des Zones de disponibilité pour les protéger contre des points uniques de panne. Avec les Zones de disponibilité, Azure propose des contrats de niveau de service de durée de fonctionnement des machines virtuelles de pointe de 99,99 %. La version complète du [contrat SLA Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explique la disponibilité garantie d’Azure dans son ensemble.

Une zone de disponibilité dans une région Azure est une combinaison d’un domaine d’erreur et d’un domaine de mise à jour. Par exemple, si vous créez trois ou plusieurs machines virtuelles dans trois zones d’une région Azure, vos machines virtuelles sont efficacement réparties sur trois domaines d’erreur et trois domaines de mise à jour. La plateforme Azure reconnaît cette répartition entre les domaines de mise à jour pour vous assurer que les machines virtuelles des différentes zones ne sont pas planifiées pour être mises à jour en même temps.

Générez la haute disponibilité dans votre architecture d’applications par la colocalisation de vos ressources de calcul, de stockage, de mise en réseau et de données dans une zone et une réplication dans d’autres zones. Les services Azure qui prennent en charge les Zones de disponibilité sont classés en deux catégories :

- **Services zonaux** : lorsque vous épinglez la ressource à une zone spécifique (par exemple des machines virtuelles, des disques managés, des adresses IP standard) ou
- **Services redondants interzone** : lorsque la plateforme Azure effectue automatiquement la réplication entre des zones (par exemple, stockage redondant interzone, SQL Database).

Pour obtenir la continuité complète des activités sur Azure, générez votre architecture d’applications à l’aide de la combinaison des Zones de disponibilité et des paires de régions Azure. Vous pouvez effectuer une réplication synchrone de vos applications et données à l’aide des Zones de la disponibilité d’une région Azure pour répliquer en haute disponibilité et de façon asynchrone entre les régions Azure pour la protection de la récupération d’urgence.
 
![affichage conceptuel d’une zone devenant indisponible dans une région](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Les identificateurs de zones de disponibilité (les nombres 1, 2 et 3 dans l’image ci-dessus) sont mappés logiquement aux zones physiques réelles pour chaque abonnement indépendamment. Cela signifie que la zone de disponibilité 1 dans un abonnement donné peut correspondre à une zone physique différente de la zone de disponibilité 1 dans un autre abonnement. C’est pourquoi il est déconseillé de se fier aux identificateurs de zones de disponibilité entre différents abonnements pour le placement des machines virtuelles.

## <a name="region-and-service-categories"></a>Région et catégories de services

L’approche d’Azure en matière de disponibilité des services Azure dans les régions est décrite en exprimant les services mis à disposition dans les régions recommandées et les autres régions.

- **Région recommandée** - Une région qui fournit le plus grand nombre de fonctionnalités de service et est conçue pour prendre en charge les zones de disponibilité maintenant, ou à l’avenir. Celles-ci sont désignées dans le portail Azure avec le libellé **Recommandé**.
- **Région alternative (ou autre région)** - Une région qui étend l’empreinte d’Azure au sein d’une limite de résidence des données où une région recommandée existe également. Les autres régions vous aident à optimiser la latence et à fournir une deuxième région pour les besoins de récupération d’urgence. Elles ne sont pas conçues pour prendre en charge les zones de disponibilité (bien qu’Azure effectue une évaluation régulière de ces régions pour déterminer si elles doivent devenir des régions recommandées). Celles-ci sont désignées dans le portail Azure par le libellé **Autre**.

### <a name="comparing-region-types"></a>Comparaison des types de zone

Les services Azure sont regroupés en trois catégories : services fondamentaux, principaux et spécialisés. La stratégie générale d’Azure pour le déploiement de services dans une région donnée est principalement pilotée par le type de région, les catégories de service et la demande des clients :

- **De base** - Disponibles dans toutes les régions recommandées et alternatives lorsque la région est généralement disponible, ou dans les 12 mois suivant la disponibilité générale d’un nouveau service.
- **Standard** - Disponibles dans toutes les régions recommandées dans un délai de 12 mois suivant la disponibilité générale de la région/du service ; pilotés par la demande dans les autres régions (beaucoup sont déjà déployés dans un grand sous-ensemble de régions alternatives).
- **Spécialisés** - Ciblés, souvent axés sur le secteur ou sur du matériel personnalisé/spécialisé. Disponibilité basée sur la demande entre les régions (beaucoup sont déjà déployés dans un grand sous-ensemble de régions recommandées).

Pour savoir quels services sont déployés dans une région donnée, ainsi que la feuille de route à venir pour la préversion ou la disponibilité générale des services dans une région, consultez [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?products=all).

Si une offre de service n’est pas disponible dans une région spécifique, vous pouvez nous faire savoir votre intérêt en contactant votre représentant commercial Microsoft.

| Type de région | Région non précisée | De base | Standard | Spécialisée | Zones de disponibilité | Résidence des données |
| --- | --- | --- | --- | --- | --- | --- |
| Recommandé | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Piloté par la demande | :heavy_check_mark: | :heavy_check_mark: |
| Alterner | :heavy_check_mark: | :heavy_check_mark: | Piloté par la demande | Piloté par la demande | N/A | :heavy_check_mark: |

### <a name="services-by-category"></a>Services par catégorie

Comme mentionné précédemment, Azure classe les services en trois catégories : de base, standard et spécialisés. Les catégories de service sont attribuées lors de la mise en disponibilité générale. Souvent, les services démarrent leur cycle de vie en tant que services spécialisés, et les augmentations de la demande et de l’utilisation peuvent leur valoir une promotion au niveau général ou de base. Le tableau suivant répertorie les catégories attribuées aux services de base, standard et spécialisés. Notez les points suivants concernant le tableau :

- Certains services ne sont pas régionaux. Pour plus d’informations et pour obtenir la liste des services non régionaux, consultez les [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/).
- Les machines virtuelles de génération plus ancienne ne sont pas répertoriées. Pour plus d’informations, consultez la documentation sur [Tailles de machines virtuelles des générations précédentes](../virtual-machines/sizes-previous-gen.md).
- Aucune catégorie n’est attribuée aux services avant la disponibilité générale (GA). Pour plus d’informations et pour obtenir la liste des services disponibles en préversion, consultez [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/). 

> [!div class="mx-tableFixed"]
> | De base                          | Standard                                        | Spécialisée                                          |
> |---------------------------------------|---------------------------------------------------|------------------------------------------------------|
> | Comptes de stockage                      | Gestion des API                                    | API Azure pour FHIR                                   |
> | Application Gateway                   | Configuration d’application                                 | Azure Analysis Services                              |
> | Sauvegarde Azure                          | App Service                                       | Azure Cognitive Services : Le détecteur d’anomalies           |
> | Azure Cosmos DB                       | Automatisation                                        | Azure Cognitive Services : Vision personnalisée              |
> | Azure Data Lake Storage Gen2          | Azure Active Directory Domain Services            | Azure Cognitive Services : Form Recognizer            |
> | Azure ExpressRoute                    | Azure Bastion                                     | Azure Cognitive Services : Personalizer               |
> | Adresse IP publique                       | Cache Azure pour Redis                             | Azure Cognitive Services : QnA Maker                  |
> | Azure SQL Database                    | Recherche cognitive Azure                            | Azure Database for MariaDB                           |
> | Azure SQL : Instance gérée          | Azure Cognitive Services                          | Azure Database Migration Service                     |
> | Services cloud                        | Azure Cognitive Services : Vision par ordinateur         | Module de sécurité matériel (HSM) dédié Azure                                  |
> | Services cloud : Série Av2            | Azure Cognitive Services : Content Moderator       | Azure Digital Twins                                  |
> | Services cloud : Série Dv2            | Azure Cognitive Services : Face                    | Azure Health Bot                                     |
> | Services cloud : Série Dv3            | Azure Cognitive Services : Lecteur immersif        | Azure HPC Cache                                      |
> | Services cloud : Série Ev3            | Azure Cognitive Services : Language Understanding  | Azure Lab Services                                   |
> | Services cloud : IP de niveau d'instance    | Azure Cognitive Services : Services Speech         | Azure NetApp Files                                   |
> | Services cloud : Adresse IP réservée           | Azure Cognitive Services : Analyse de texte          | Service Azure SignalR                                |
> | Stockage sur disque                          | Azure Cognitive Services : Convertisseur              | Service Azure Spring Cloud                           |
> | Event Hubs                            | Explorateur de données Azure                               | Azure Time Series Insights                           |
> | Key Vault                             | Azure Data Share                                  | Azure VMware Solution                                |
> | Équilibrage de charge                         | Azure Database pour MySQL                          | Azure VMware Solution by CloudSimple                 |
> | Service Bus                           | Azure Database pour PostgreSQL                     | Services cloud : Série H                             |
> | Service Fabric                        | Azure Databricks                                  | Data Catalog                                         |
> | Stockage : Niveaux de stockage BLOB chaud/froid  | Azure DDoS Protection                             | Data Lake Analytics                                  |
> | Stockage : Disques managés                | Azure DevTest Labs                                | Azure Machine Learning Studio (classique)              |
> | Virtual Machine Scale Sets            | Pare-feu Azure                                    | Spatial Anchors                                      |
> | Virtual Machines                      | Azure Firewall Manager                            | Stockage : Stockage archive                             |
> | Machines virtuelles : Série Av2          | Azure Functions                                   | StorSimple                                           |
> | Machines virtuelles : Série Bs           | Azure IoT Hub                                     | Disque Ultra                                   |
> | Machines virtuelles : Série DSv2         | Azure Kubernetes Service (AKS)                    | Video Indexer                                        |
> | Machines virtuelles : Série DSv3         | Azure Machine Learning                            | Machines virtuelles : Série DASv4                       |
> | Machines virtuelles : Série Dv2          | Azure Monitor : Application Insights               | Machines virtuelles : Série Dav4                        |
> | Machines virtuelles : Série Dv3          | Azure Monitor : Log Analytics                      | Machines virtuelles : série DCsv2                       |
> | Machines virtuelles : Série ESv3         | Azure Private Link                                | Machines virtuelles : Série EASv4                       |
> | Machines virtuelles : Série Ev3          | Azure Red Hat OpenShift                           | Machines virtuelles : Série Eav4                        |
> | Machines virtuelles : IP de niveau d'instance  | Azure Site Recovery                               | Machines virtuelles : Série HBv1                        |
> | Machines virtuelles : Adresse IP réservée         | Azure Stream Analytics                            | Machines virtuelles : Série HBv2                        |
> | Réseau virtuel                       | Azure Synapse Analytics                           | Machines virtuelles : Série HCv1                        |
> | Passerelle VPN                           | Batch                                             | Machines virtuelles : Série H                           |
> |                                       | Services cloud : Série M                          | Machines virtuelles : Série LSv2                        |
> |                                       | Container Instances                               | Machines virtuelles : Série Mv2                         |
> |                                       | Container Registry                                | Machines virtuelles : Série NCv3                        |
> |                                       | Data Factory                                      | Machines virtuelles : Série NDv2                        |
> |                                       | Event Grid                                        | Machines virtuelles : Série NVv3                        |
> |                                       | HDInsight                                         | Machines virtuelles : Série NVv4                        |> 
> |                                       | Logic Apps                                        | Machines virtuelles : SAP HANA sur Azure (grandes instances)  |
> |                                       | Media Services                                    |                                                      |
> |                                       | Network Watcher                                   |                                                      |
> |                                       | Notification Hubs                                 |                                                      |
> |                                       | Stockage Blob Premium                              |                                                      |
> |                                       | Stockage de fichiers Premium                             |                                                      |
> |                                       | Machines virtuelles : Série Ddsv4                    |                                                      |
> |                                       | Machines virtuelles : Série Ddv4                     |                                                      |
> |                                       | Machines virtuelles : Série Dsv4                     |                                                      |
> |                                       | Machines virtuelles : Série Dv4                      |                                                      |
> |                                       | Machines virtuelles : Série Edsv4                    |                                                      |
> |                                       | Machines virtuelles : Série Edv4                     |                                                      |
> |                                       | Machines virtuelles : Série Esv4                     |                                                      |
> |                                       | Machines virtuelles : Série Ev4                      |                                                      |
> |                                       | Machines virtuelles : Série Fsv2                     |                                                      |
> |                                       | Machines virtuelles : Série M                        |                                                      |
> |                                       | WAN virtuel                                       |                                                      |


## <a name="next-steps"></a>Étapes suivantes

- [Régions prenant en charge les zones de disponibilité dans Azure](az-region.md)
- [Modèles de démarrage rapide](https://aka.ms/azqs)