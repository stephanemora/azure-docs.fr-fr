---
title: Vue d’ensemble des balises de service Azure
titlesuffix: Azure Virtual Network
description: Découvrez les balises de service. Les balises de service permettent de réduire la complexité de la création des règles de sécurité.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2019
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 152b9f3974f24644e55bed68f5ed65faa90d7fe7
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851653"
---
# <a name="virtual-network-service-tags"></a>Balises de service du réseau virtuel 
<a name="network-service-tags"></a>

Une balise de service représente un groupe de préfixes d’adresses IP d’un service Azure donné. Elle permet de réduire la complexité des mises à jour fréquentes relatives aux règles de sécurité réseau. Vous pouvez utiliser des balises de service pour définir des contrôles d’accès réseau sur les [Groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) ou le [Pare-feu Azure](https://docs.microsoft.com/azure/firewall/service-tags). Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de la balise de service (par exemple, **ApiManagement**) dans le champ *Source* ou *Destination* approprié d’une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent. 

## <a name="available-service-tags"></a>Balises de service disponibles
Le tableau suivant répertorie toutes les balises de service disponibles à utiliser dans les règles de [Groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules).

Les colonnes indiquent si la balise :

- Est adaptée aux règles couvrant le trafic entrant ou sortant
- Prend en charge l’étendue [régionale](https://azure.microsoft.com/regions) 
- Est utilisable dans les règles de [Pare-feu Azure](https://docs.microsoft.com/azure/firewall/service-tags)

Par défaut, les balises de service reflètent les plages pour l’ensemble du Cloud.  Certaines balises de service permettent également d’obtenir un contrôle plus précis en limitant les plages d’adresses IP correspondantes à une région spécifiée.  Par exemple, si la balise de service **Storage** représente le Stockage Azure pour l’ensemble du Cloud, **Storage.WestUS** limite uniquement aux plages d’adresses IP de stockage de la région WestUS.  Les descriptions des balises de service ci-dessous indiquent si elles prennent en charge une telle étendue régionale.  



| Tag | Objectif | Peut-elle utiliser le trafic entrant ou sortant ? | Peut-elle être étendue à une zone régionale ? | Peut-elle être utilisée avec le Pare-feu Azure ? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Trafic de gestion pour les déploiements dédiés d’APIM. | Les deux | Non | OUI |
| **AppService**    | Service App Service. Cette balise est recommandée pour les règles de sécurité sortantes vers les serveurs frontaux des applications web. | Règle de trafic sortant | OUI | OUI |
| **AppServiceManagement** | Trafic de gestion pour les déploiements dédiés d’App Service Environment. | Les deux | Non | OUI |
| **AzureActiveDirectory** | Service Azure Active Directory. | Règle de trafic sortant | Non | OUI |
| **AzureActiveDirectoryDomainServices** | Trafic de gestion pour les déploiements dédiés d’Azure Active Directory Domain Services. | Les deux | Non | OUI |
| **AzureBackup** |Service Sauvegarde Azure.<br/><br/>*Remarque :* Cette balise est dotée d’une dépendance par rapport aux balises **Storage** and **AzureActiveDirectory**. | Règle de trafic sortant | Non | OUI |
| **AzureCloud** | Toutes les [adresses IP publiques du centre de données](https://www.microsoft.com/download/details.aspx?id=41653). | Règle de trafic sortant | OUI | OUI |
| **AzureConnectors** | Connecteurs Logic Apps pour les connexions principales/de sonde. | Trafic entrant | OUI | OUI |
| **AzureContainerRegistry** | Service Azure Container Registry. | Règle de trafic sortant | OUI | OUI |
| **AzureCosmosDB** | Service de base de données Azure Cosmos. | Règle de trafic sortant | OUI | OUI |
| **AzureDataLake** | Service Azure Data Lake. | Règle de trafic sortant | Non | OUI |
| **AzureHDInsight** | Service Azure HDInsight. | Trafic entrant | OUI | Non |
| **AzureIoTHub** | Service IoT Hub. | Règle de trafic sortant | Non | Non |
| **AzureKeyVault** | Service Azure KeyVault.<br/><br/>*Remarque :* Cette balise est dotée d’une dépendance par rapport à la balise **AzureActiveDirectory**. | Règle de trafic sortant | OUI | OUI |
| **AzureLoadBalancer** | Équilibreur de charge de l’infrastructure Azure. Elle est translatée vers l’[adresse IP virtuelle de l’hôte](security-overview.md#azure-platform-considerations) (168.63.129.16) d’où proviennent les sondes d’intégrité d’Azure. Vous pouvez remplacer cette règle si vous n’utilisez pas l’équilibreur de charge Azure. | Les deux | Non | Non |
| **AzureMachineLearning** | Service Azure Machine Learning. | Règle de trafic sortant | Non | OUI |
| **AzureMonitor** | Log Analytics, App Insights, AzMon et métriques personnalisées (points de terminaison GiG).<br/><br/>*Remarque :* Pour Log Analytics, cette balise est dotée une dépendance par rapport à la balise **Storage**. | Règle de trafic sortant | Non | OUI |
| **AzurePlatformDNS** | Service DNS de l’infrastructure de base (par défaut).<br/><br>Vous pouvez utiliser cette balise pour désactiver le DNS par défaut. Nous vous recommandons de faire preuve de prudence lors de l’utilisation de cette balise. Il est recommandé de lire les [Considérations relatives à la plateforme Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Veillez à passer par une phase de test avant de l’utiliser. | Règle de trafic sortant | Non | Non |
| **AzurePlatformIMDS** | IMDS, qui est un service d’infrastructure de base.<br/><br/>Vous pouvez utiliser cette balise pour désactiver le point de terminaison IMDS par défaut.  Nous vous recommandons de faire preuve de prudence lors de l’utilisation de cette balise. Il est recommandé de lire les [Considérations relatives à la plateforme Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Veillez à passer par une phase de test avant de l’utiliser. | Règle de trafic sortant | Non | Non |
| **AzurePlatformLKM** | Gestionnaire de licences Windows ou service de gestion de clés.<br/><br/>Vous pouvez utiliser cette balise pour désactiver les paramètres par défaut des licences. Nous vous recommandons de faire preuve de prudence lors de l’utilisation de cette balise.  Il est recommandé de lire les [Considérations relatives à la plateforme Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Veillez à passer par une phase de test avant de l’utiliser. | Règle de trafic sortant | Non | Non |
| **AzureTrafficManaged** | Adresses IP de sonde Azure Traffic Manager.<br/><br/>Vous trouverez plus d’informations sur les adresses IP de sondage Traffic Manager dans les [Questions fréquentes (FAQ) sur Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Trafic entrant | Non | OUI |  
| **BatchNodeManagement** | Trafic de gestion pour les déploiements dédiés d’Azure Batch. | Les deux | Non | OUI |
| **CognitiveServicesManagement** | Plages d’adresses du trafic pour Cognitive Services | Règle de trafic sortant | Non | Non |
| **Dynamics365ForMarketingEmail** | Plages d’adresses pour le service de messagerie marketing de Dynamics 365. | Règle de trafic sortant | OUI | Non |
| **EventHub** | Service Azure EventHub. | Règle de trafic sortant | OUI | OUI |
| **GatewayManager** | Trafic de gestion pour les déploiements dédiés d’App Gateway/du réseau privé virtuel. | Trafic entrant | Non | Non |
| **Internet** | Espace d’adresse IP qui se trouve en dehors du réseau virtuel et est accessible sur les réseaux Internet publics.<br/><br/>La plage d’adresse inclut l’[espace de l’adresse IP public d’Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Les deux | Non | Non |
| **MicrosoftContainerRegistry** | Service Microsoft Container Registry. | Règle de trafic sortant | OUI | OUI |
| **ServiceBus** | Service Azure Service Bus utilisant le niveau de service Premium. | Règle de trafic sortant | OUI | OUI |
| **ServiceFabric** | Service Service Fabric. | Règle de trafic sortant | Non | Non |
| **Sql** | Services Azure SQL Database, Azure Database pour MySQL, Azure Database pour PostgreSQL et Azure SQL Data Warehouse.<br/><br/>*Remarque :* Cette balise représente le service, mais pas des instances spécifiques du service. Par exemple, la balise représente le service Azure SQL Database, mais pas une base de données ou un serveur SQL spécifique. | Règle de trafic sortant | OUI | OUI |
| **SqlManagement** | Trafic de gestion pour les déploiements dédiés de SQL. | Les deux | Non | OUI |
| **Stockage** | Service Stockage Azure. <br/><br/>*Remarque :* La balise représente le service, mais pas des instances du service. Par exemple, la balise représente le service Azure Storage, mais pas un compte Azure Storage spécifique. | Règle de trafic sortant | OUI | OUI |
| **VirtualNetwork** | L’espace d’adressage du réseau virtuel (toutes les plages d’adresses IP définies pour le réseau virtuel), tous les espaces d’adressage locaux connectés, les réseaux virtuels [appairés](virtual-network-peering-overview.md) ou connectés à une [passerelle de réseau virtuel](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), l’[adresse IP virtuelle de l’hôte](security-overview.md#azure-platform-considerations) et les préfixes d’adresse utilisés sur les [routes définies par l’utilisateur](virtual-networks-udr-overview.md). N’oubliez pas que cette balise peut également contenir des itinéraires par défaut. | Les deux | Non | Non |

>[!NOTE]
>Lorsque vous travaillez dans l’environnement *Classic* (pré-Azure Resource Manager), un ensemble sélectionné des balises ci-dessus est pris en charge.  Elles utilisent une autre orthographe :

| Orthographe classique | Balise Resource Manager équivalente |
|---|---|
| AZURE_LOADBALANCER | AzureLoadBalancer |
| INTERNET | Internet |
| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Les balises des services Azure indiquent les préfixes d’adresse du cloud spécifique utilisé. par exemple, les plages d’adresses IP sous-jacentes correspondant à la valeur de balise **Sql** peuvent différer entre le Cloud public Azure et le Cloud Azure China.

> [!NOTE]
> Si vous implémentez un [point de terminaison de service de réseau virtuel](virtual-network-service-endpoints-overview.md) pour un service, par exemple Stockage Azure ou Azure SQL Database, Azure ajoute un [itinéraire](virtual-networks-udr-overview.md#optional-default-routes) vers un sous-réseau de réseau virtuel pour le service. Les préfixes d’adresse de l’itinéraire sont les mêmes préfixes d’adresse ou plages CIDR que pour la balise de service correspondante.



## <a name="service-tags-in-on-premises"></a>Balises de service en local  
Vous pouvez obtenir la balise de service et les informations de plage actuelles à inclure dans le cadre de vos configurations du pare-feu local.  Ces informations forment la liste actuelle des plages d’adresses IP correspondant à chaque balise de service.  Les informations peuvent être obtenues par programmation ou via le téléchargement de fichiers JSON comme suit.

### <a name="service-tag-discovery-api-public-preview"></a>API Service Tag Discovery (préversion publique)
Vous pouvez récupérer par programmation la liste actuelle des balises de service avec les informations relatives aux plages d’adresses IP :

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> En préversion publique, l’API Discovery peut retourner des informations qui ne sont pas aussi récentes que les téléchargements JSON (voir ci-dessous).


### <a name="discover-service-tags-using-downloadable-json-files"></a>Détection de balises de service à l’aide de fichiers JSON téléchargeables 
Vous pouvez télécharger des fichiers JSON contenant la liste actuelle des balises de service avec les informations relatives aux plages d’adresses IP. Ces derniers sont mises à jour et publiés chaque semaine.  Les emplacements de chaque Cloud sont :

- [Azure public](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure Chine](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Allemagne](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>Un sous-ensemble de ces informations a déjà été publié dans des fichiers XML pour [Azure public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure Chine](https://www.microsoft.com/download/details.aspx?id=42064) et [Azure Allemagne](https://www.microsoft.com/download/details.aspx?id=54770). Ces téléchargements XML seront obsolètes à compter du 30 juin 2020 et ne seront plus disponibles après cette date. Effectuez une migration à l’aide de l’API Discovery ou des téléchargements de fichiers JSON, comme décrit ci-dessus.

### <a name="tips"></a>Conseils 
- Vous pouvez détecter les mises à jour d’une publication à l’autre en augmentant les valeurs *changeNumber* dans le fichier JSON. Chaque sous-section (par exemple, **Storage.WestUS**) a sa propre valeur *changeNumber* qui est incrémentée au fur et à mesure que des modifications sont effectuées.  Le niveau supérieur de la valeur *changeNumber* du fichier est incrémenté lorsque l’une des sous-sections est modifiée.
- Pour obtenir des exemples d’analyse des informations de balise de service (par exemple, obtenir toutes les plages d’adresses pour le stockage dans la région WestUS), reportez-vous à la documentation relative à l’[API Service Tag Discovery PowerShell](https://aka.ms/discoveryapi_powershell).

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [créer des groupes de sécurité réseau](tutorial-filter-network-traffic.md).

