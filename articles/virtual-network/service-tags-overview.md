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
ms.openlocfilehash: 1fec2778ce8c839c5bac0c1d74085db0f8b283ce
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76903003"
---
# <a name="virtual-network-service-tags"></a>Balises de service du réseau virtuel 
<a name="network-service-tags"></a>

Une balise de service représente un groupe de préfixes d’adresses IP d’un service Azure donné. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent, ce qui réduit la complexité des mises à jour fréquentes relatives aux règles de sécurité réseau. 

Vous pouvez utiliser des balises de service pour définir des contrôles d’accès réseau sur les  [Groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)  ou le  [Pare-feu Azure](https://docs.microsoft.com/azure/firewall/service-tags). Utilisez des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de la balise de service (par exemple,  **ApiManagement**) dans le champ  *Source* ou *Destination*  approprié d'une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. 

Vous pouvez utiliser des balises de service pour isoler le réseau et protéger vos ressources Azure de l’Internet général tout en accédant aux services Azure qui ont des points de terminaison publics. Créez des règles de groupe de sécurité de réseau entrant/sortant pour refuser le trafic vers/depuis **Internet** et autoriser le trafic vers/depuis **AzureCloud** ou d’autres [balises de service disponibles](#available-service-tags) de services Azure spécifiques. 

## <a name="available-service-tags"></a>Balises de service disponibles
Le tableau suivant répertorie les balises de service disponibles à utiliser dans les règles [Groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules).

Les colonnes indiquent si la balise :

- Est adaptée aux règles couvrant le trafic entrant ou sortant.
- Prend en charge l’étendue [régionale](https://azure.microsoft.com/regions).
- Est utilisable dans les règles de [Pare-feu Azure](https://docs.microsoft.com/azure/firewall/service-tags).

Par défaut, les balises de service reflètent les plages pour l’ensemble du Cloud. Certaines balises de service permettent également d’obtenir un contrôle plus précis en limitant les plages d’adresses IP correspondantes à une région spécifiée. Par exemple, la balise de service **Storage** représente le Stockage Azure pour l’ensemble du cloud, alors que **Storage.WestUS** limite la sélection aux plages d’adresses IP de stockage de la région WestUS. Le tableau suivant indique si chaque balise de service prend en charge cette étendue régionale.  

| Tag | Objectif | Peut-elle utiliser le trafic entrant ou sortant ? | Peut-elle être étendue à une zone régionale ? | Peut-elle être utilisée avec le Pare-feu Azure ? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Trafic de gestion pour les déploiements dédiés de Gestion des API. | Les deux | Non | Oui |
| **ApplicationInsightsAvailability** | Disponibilité d’Application Insights. | Trafic entrant | Non | Non |
| **AppService**    | Azure App Service Cette balise est recommandée pour les règles de sécurité sortantes vers les serveurs frontaux des applications web. | Règle de trafic sortant | Oui | Oui |
| **AppServiceManagement** | Trafic de gestion pour les déploiements dédiés vers App Service Environment. | Les deux | Non | Oui |
| **AzureActiveDirectory** | Azure Active Directory | Règle de trafic sortant | Non | Oui |
| **AzureActiveDirectoryDomainServices** | Trafic de gestion pour les déploiements dédiés vers Azure Active Directory Domain Services. | Les deux | Non | Oui |
| **AzureAdvancedThreatProtection** | Azure Advanced Threat Protection. | Règle de trafic sortant | Non | Non |
| **AzureBackup** |Sauvegarde Azure.<br/><br/>*Remarque :* Cette balise est dotée d’une dépendance par rapport aux balises **Storage** and **AzureActiveDirectory**. | Règle de trafic sortant | Non | Oui |
| **AzureBotService** | Service Azure Bot. | Règle de trafic sortant | Non | Non |
| **AzureCloud** | Toutes les [adresses IP publiques du centre de données](https://www.microsoft.com/download/details.aspx?id=56519). | Règle de trafic sortant | Oui | Oui |
| **AzureCognitiveSearch** | Recherche cognitive Azure (si vous utilisez des indexeurs avec un ensemble de compétences). | Les deux | Non | Non |
| **AzureConnectors** | Connecteurs Azure Logic Apps pour les connexions principales/de sonde. | Trafic entrant | Oui | Oui |
| **AzureContainerRegistry** | Azure Container Registry. | Règle de trafic sortant | Oui | Oui |
| **AzureCosmosDB** | Azure Cosmos DB. | Règle de trafic sortant | Oui | Oui |
| **AzureDatabricks** | Azure Databricks. | Les deux | Non | Non |
| **AzureDataExplorerManagement** | Gestion d’Azure Data Explorer. | Trafic entrant | Non | Non |
| **AzureDataLake** | Azure Data Lake. | Règle de trafic sortant | Non | Oui |
| **AzureEventGrid** | Azure Event Grid. <br/><br/>*Remarque :* Cette balise couvre les points de terminaison Azure Event Grid dans les régions USA Centre Sud, USA Est, Usa Est 2, USA Ouest 2 et USA Centre uniquement. | Les deux | Non | Non |
| **AzureFrontDoor** | Azure Front Door. | Les deux | Non | Non |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>*Remarque :* Cette balise est dotée d’une dépendance par rapport à la balise **AzureActiveDirectory** et **AzureFrontDoor.Frontend**. Veuillez également supprimer les adresses IP suivantes de la liste verte (cette dépendance sera bientôt supprimée) : 13.107.6.181 et 13.107.9.181 | Règle de trafic sortant | Non | Non |
| **AzureIoTHub** | Azure IoT Hub. | Règle de trafic sortant | Non | Non |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Remarque :* Cette balise est dotée d’une dépendance par rapport à la balise **AzureActiveDirectory**. | Règle de trafic sortant | Oui | Oui |
| **AzureLoadBalancer** | Équilibrage de charge de l’infrastructure Azure. Elle est translatée vers l’[adresse IP virtuelle de l’hôte](security-overview.md#azure-platform-considerations) (168.63.129.16) d’où proviennent les sondes d’intégrité d’Azure. Cela n’inclut pas le trafic vers votre ressource Azure Load Balancer. Vous pouvez remplacer cette règle si vous n’utilisez pas l’équilibreur de charge Azure. | Les deux | Non | Non |
| **AzureMachineLearning** | Azure Machine Learning. | Les deux | Non | Oui |
| **AzureMonitor** | Métriques Log Analytics, Application Insights, AzMon et métriques personnalisées (points de terminaison GiG).<br/><br/>*Remarque :* Pour Log Analytics, cette balise est dotée une dépendance par rapport à la balise **Storage**. | Règle de trafic sortant | Non | Oui |
| **AzurePlatformDNS** | Service DNS de l’infrastructure de base (par défaut).<br/><br>Vous pouvez utiliser cette balise pour désactiver le DNS par défaut. Utilisez cette balise avec prudence. Nous vous recommandons de lire les [considérations sur la plateforme Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Nous vous recommandons également d’effectuer des tests avant d’utiliser cette balise. | Règle de trafic sortant | Non | Non |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS), qui est un service d’infrastructure de base.<br/><br/>Vous pouvez utiliser cette balise pour désactiver le point de terminaison IMDS par défaut. Utilisez cette balise avec prudence. Nous vous recommandons de lire les [considérations sur la plateforme Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Nous vous recommandons également d’effectuer des tests avant d’utiliser cette balise. | Règle de trafic sortant | Non | Non |
| **AzurePlatformLKM** | Gestionnaire de licences Windows ou service de gestion de clés.<br/><br/>Vous pouvez utiliser cette balise pour désactiver les paramètres par défaut des licences. Utilisez cette balise avec prudence. Nous vous recommandons de lire les [considérations sur la plateforme Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations).  Nous vous recommandons également d’effectuer des tests avant d’utiliser cette balise. | Règle de trafic sortant | Non | Non |
| **AzureResourceManager** | Azure Resource Manager. | Règle de trafic sortant | Non | Non |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Remarque :* Cette balise est dotée d’une dépendance par rapport aux balises **Storage**, **AzureActiveDirectory** et **EventHub**. | Règle de trafic sortant | Non | Non |
| **AzureTrafficManager** | Adresses IP de sonde Azure Traffic Manager.<br/><br/>Pour plus d’informations sur les adresses IP de sondage Traffic Manager, consultez les [Questions fréquentes (FAQ) sur Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Trafic entrant | Non | Oui |  
| **BatchNodeManagement** | Trafic de gestion pour les déploiements dédiés d’Azure Batch. | Les deux | Non | Oui |
| **CognitiveServicesManagement** | Plages d’adresses du trafic pour Azure Cognitive Services. | Règle de trafic sortant | Non | Non |
| **Dynamics365ForMarketingEmail** | Plages d’adresses pour le service de messagerie marketing de Dynamics 365. | Règle de trafic sortant | Oui | Non |
| **ElasticAFD** | Azure Front Door élastique. | Les deux | Non | Non |
| **EventHub** | Azure Event Hubs. | Règle de trafic sortant | Oui | Oui |
| **GatewayManager** | Trafic de gestion pour les déploiements dédiés à la passerelle VPN Azure et Application Gateway. | Trafic entrant | Non | Non |
| **GuestAndHybridManagement** | Azure Automation et Guest Configuration. | Règle de trafic sortant | Non | Oui |
| **HDInsight** | Azure HDInsight | Trafic entrant | Oui | Non |
| **Internet** | Espace d’adresse IP qui se trouve en dehors du réseau virtuel et est accessible sur les réseaux Internet publics.<br/><br/>La plage d’adresse inclut l’[espace de l’adresse IP public d’Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Les deux | Non | Non |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | Règle de trafic sortant | Non | Non |
| **MicrosoftContainerRegistry** | Registre de conteneurs pour les images de conteneur Microsoft. <br/><br/>*Remarque :* Veuillez également mettre en liste verte les adresses IP suivantes (cette dépendance sera bientôt supprimée) : 204.79.197.219. | Règle de trafic sortant | Oui | Oui |
| **ServiceBus** | Trafic Azure Service Bus qui utilise le niveau de service Premium. | Règle de trafic sortant | Oui | Oui |
| **ServiceFabric** | Azure Service Fabric. | Règle de trafic sortant | Non | Non |
| **Sql** | Azure SQL Database, Azure Database pour MySQL, Azure Database pour PostgreSQL et Azure SQL Data Warehouse.<br/><br/>*Remarque :* Cette balise représente le service, mais pas des instances spécifiques du service. Par exemple, la balise représente le service Azure SQL Database, mais pas une base de données ou un serveur SQL spécifique. | Règle de trafic sortant | Oui | Oui |
| **SqlManagement** | Trafic de gestion pour les déploiements dédiés de SQL. | Les deux | Non | Oui |
| **Stockage** | Stockage Azure. <br/><br/>*Remarque :* Cette balise représente le service, mais pas des instances spécifiques du service. Par exemple, la balise représente le service Azure Storage, mais pas un compte Azure Storage spécifique. | Règle de trafic sortant | Oui | Oui |
| **VirtualNetwork** | L’espace d’adressage du réseau virtuel (toutes les plages d’adresses IP définies pour le réseau virtuel), tous les espaces d’adressage locaux connectés, les réseaux virtuels [appairés](virtual-network-peering-overview.md) ou connectés à une [passerelle de réseau virtuel](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), l’[adresse IP virtuelle de l’hôte](security-overview.md#azure-platform-considerations) et les préfixes d’adresse utilisés sur les [routes définies par l’utilisateur](virtual-networks-udr-overview.md). Cette balise peut également contenir des itinéraires par défaut. | Les deux | Non | Non |

>[!NOTE]
>Dans le modèle de déploiement classique (antérieur à Azure Resource Manager), un sous-ensemble des balises répertoriées dans le tableau précédent est pris en charge. Ces balises sont orthographiées différemment :
>
>| Orthographe classique | Balise Resource Manager équivalente |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Les balises des services Azure indiquent les préfixes d’adresse du cloud spécifique utilisé. Par exemple, les plages IP sous-jacentes qui correspondent à la valeur de balise **Sql** sur le cloud public Azure seront différentes des plages sous-jacentes sur le cloud Azure Chine.

> [!NOTE]
> Si vous implémentez un [point de terminaison de service de réseau virtuel](virtual-network-service-endpoints-overview.md) pour un service, par exemple Stockage Azure ou Azure SQL Database, Azure ajoute un [itinéraire](virtual-networks-udr-overview.md#optional-default-routes) vers un sous-réseau de réseau virtuel pour le service. Les préfixes d’adresse de l’itinéraire sont les mêmes préfixes d’adresse ou plages CIDR que pour la balise de service correspondante.

## <a name="service-tags-on-premises"></a>Balises de service locales  
Vous pouvez obtenir la balise de service et les informations de plage actuelles à inclure dans le cadre de vos configurations du pare-feu local. Ces informations forment la liste actuelle des plages d’adresses IP qui correspondent à chaque balise de service. Vous pouvez obtenir les informations par programmation ou par le biais d’un téléchargement de fichier JSON, comme décrit dans les sections suivantes.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Utiliser l’API Service Tag Discovery (préversion publique)
Vous pouvez récupérer par programmation la liste actuelle des balises de service ainsi que les informations relatives aux plages d’adresses IP :

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> Bien qu’elle soit en préversion publique, l’API Discovery peut renvoyer des informations qui sont moins actuelles que celles renvoyées par les téléchargements JSON. (Voir la section suivante.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Détection de balises de service à l’aide de fichiers JSON téléchargeables 
Vous pouvez télécharger des fichiers JSON qui contiennent la liste actuelle des balises de service avec les informations relatives aux plages d’adresses IP. Ces listes sont mises à jour et publiées chaque semaine. Les emplacements de chaque Cloud sont :

- [Azure public](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure Chine](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Allemagne](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>Un sous-ensemble de ces informations a été publié dans des fichiers XML pour [Azure public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure Chine](https://www.microsoft.com/download/details.aspx?id=42064) et [Azure Allemagne](https://www.microsoft.com/download/details.aspx?id=54770). Ces téléchargements XML seront obsolètes à compter du 30 juin 2020 et ne seront plus disponibles après cette date. Vous devez effectuer une migration à l’aide de l’API Discovery ou des téléchargements de fichiers JSON, comme décrit dans les sections précédentes.

### <a name="tips"></a>Conseils 
- Vous pouvez détecter les mises à jour d’une publication à l’autre en notant les valeurs *changeNumber* augmentées dans le fichier JSON. Chaque sous-section (par exemple, **Storage.WestUS**) a sa propre valeur *changeNumber* qui est incrémentée au fur et à mesure que des modifications sont effectuées. Le niveau supérieur de la valeur *changeNumber* du fichier est incrémenté lorsque l’une des sous-sections est modifiée.
- Pour obtenir des exemples d’analyse des informations de balise de service (par exemple, obtenir toutes les plages d’adresses pour le stockage dans la région WestUS), reportez-vous à la documentation relative à l’[API Service Tag Discovery PowerShell](https://aka.ms/discoveryapi_powershell).

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [créer des groupes de sécurité réseau](tutorial-filter-network-traffic.md).
