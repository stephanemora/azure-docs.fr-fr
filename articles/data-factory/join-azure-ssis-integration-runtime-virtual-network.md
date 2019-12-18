---
title: Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel
description: Découvrez comment joindre un runtime d’intégration Azure-SSIS à un réseau virtuel Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: e2ee1de9899dfe091e8f6f79bcd42c75fe67ed67
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74942191"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel
Lorsque vous utilisez SQL Server Integration Services (SSIS) dans Azure Data Factory , vous devez joindre votre runtime d’intégration Azure-SSIS IR à un réseau virtuel Azure dans les scénarios suivants : 

- Vous souhaitez vous connecter à des magasins de données locaux à partir de packages SSIS en cours d’exécution sur votre Azure-SSIS IR sans configurer/gérer un IR auto-hébergé en tant que proxy. 

- Vous souhaitez vous connecter à des ressources de service Azure prises en charge avec des points de terminaison de service de réseau virtuel à partir de packages SSIS exécutés sur votre runtime d’intégration Azure-SSIS IR.

- Vous hébergez une base de données du catalogue SSIS (SSISDB) dans Azure SQL Database avec des points de terminaison de service de réseau virtuel ou une instance managée dans un réseau virtuel. 

- Vous voulez vous connecter à des sources de données ou à des ressources uniquement accessibles à partir d’adresses IP publiques spécifiques provenant de packages SSIS qui s’exécutent sur votre Azure-SSIS IR.

Data Factory vous permet de joindre votre Azure-SSIS IR à un réseau virtuel créé via le modèle de déploiement classique ou via le modèle de déploiement Azure Resource Manager. 

> [!IMPORTANT]
> Le réseau virtuel classique est déconseillé. Par conséquent, utilisez plutôt le réseau virtuel Azure Resource Manager.  Si vous utilisez déjà le réseau virtuel classique, basculez dès que possible vers le réseau virtuel Azure Resource Manager.

## <a name="access-to-on-premises-data-stores"></a>Accéder aux magasins de données locaux
Si vos packages SSIS accèdent à des magasins de données locaux, vous pouvez joindre votre runtime d’intégration Azure-SSIS IR à un réseau virtuel qui est connecté au réseau local. Vous pouvez également configurer ou gérer un IR auto-hébergé en tant que proxy pour votre runtime d’intégration Azure-SSIS IR. Pour plus d’informations, consultez [Configurer l’IR auto-hébergé comme proxy pour Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

Lorsque vous joignez votre Azure-SSIS IR à un réseau virtuel, rappelez-vous ces points importants : 

- Si aucun réseau virtuel n’est connecté à votre réseau local, créez d’abord un réseau virtuel [Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) auquel votre runtime d’intégration Azure-SSIS IR pourra se joindre. Ensuite, configurez une [connexion de passerelle VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) de site à site ou une connexion [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) de ce réseau virtuel vers votre réseau local. 

- Si un réseau virtuel classique ou Azure Resource Manager est déjà connecté à votre réseau local, au même emplacement que votre runtime d’intégration Azure-SSIS IR, vous pouvez joindre ce dernier à ce réseau virtuel. 

- Si un réseau virtuel classique est déjà connecté à votre réseau local, à un autre emplacement que celui de votre runtime d’intégration Azure-SSIS IR, vous devez créer un [réseau virtuel Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) pour le runtime d’intégration Azure-SSIS IR à joindre. Ensuite, configurez une connexion du type [réseau virtuel classique vers Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md). 
 
- Si un réseau virtuel Azure Resource Manager est déjà connecté à votre réseau local, à un autre emplacement que celui de votre runtime d’intégration Azure-SSIS IR, vous devez commencer par créer un [réseau virtuel Azure Resource Manager](../virtual-network/quick-create-portal.md##create-a-virtual-network) pour le runtime d’intégration Azure-SSIS IR à joindre. Ensuite, configurez une connexion du type réseau virtuel Azure Resource Manager vers Azure Resource Manager. 

## <a name="access-to-azure-services"></a>Accès aux services Azure
Si vos packages SSIS accèdent à des ressources de service Azure prises en charge avec des [points de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md) et que vous souhaitez sécuriser ces ressources dans le runtime d’intégration Azure-SSIS IR, vous pouvez joindre votre runtime d’intégration Azure-SSIS IR au sous-réseau de réseau virtuel configuré avec un point de terminaison de service de réseau virtuel. En même temps, ajoutez une règle de réseau virtuel à la ressource de service Azure pour autoriser l’accès à partir du même sous-réseau.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Accès à des sources de données protégées par une règle de pare-feu IP

Si vous voulez sécuriser des sources de données ou des ressources en autorisant uniquement l’accès à partir d’adresses IP publiques spécifiques, vous pouvez apporter vos propres [adresses IP publiques](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address) tout en joignant votre Azure-SSIS IR au sous-réseau du réseau virtuel. Dans ce cas, les adresses IP pour Azure-SSIS IR sont remplacées par les valeurs que vous fournissez. Ensuite, ajoutez une règle de pare-feu d’adresse IP aux sources de données ou aux ressources pour autoriser l’accès à partir de ces adresses IP.

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Hébergement du catalogue SSIS dans SQL Database
Si vous hébergez votre catalogue SSIS dans Azure SQL Database avec des points de terminaison de service de réseau virtuel, assurez-vous de joindre votre runtime d’intégration Azure-SSIS aux mêmes réseau virtuel et sous-réseau.

Pour associer votre runtime d’intégration Azure-SSIS IR au même réseau virtuel que l’instance managée, vérifiez que le runtime d’intégration Azure-SSIS IR est dans un sous-réseau différent de celui de l’instance managée. Si vous associez votre runtime d’intégration Azure-SSIS IR à un réseau virtuel différent de celui de Managed Instance, nous recommandons le peering de réseau virtuel (qui est limité à la même région) ou une connexion de réseau virtuel à réseau virtuel. Pour plus d’informations, consultez [Connecter votre application à Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connect-app.md).

Dans tous les cas, le réseau virtuel peut être déployé uniquement par le biais du modèle de déploiement Azure Resource Manager.

Pour plus d’informations, lisez les sections suivantes. 

## <a name="virtual-network-configuration"></a>Configuration du réseau virtuel

Configurez votre réseau virtuel conformément à ce qui suit : 

-   Vérifiez que `Microsoft.Batch` est un fournisseur inscrit sous l’abonnement de votre sous-réseau de machine virtuelle qui héberge le runtime d’intégration Azure SSIS IR. Si vous utilisez un réseau virtuel classique, vous devez également joindre `MicrosoftAzureBatch` au rôle Collaborateur de machine virtuelle classique pour ce réseau virtuel. 

-   Assurez-vous de disposer des autorisations requises. Pour plus d’informations, consultez [Configuration d’autorisations](#perms).

-   Sélectionnez le sous-réseau approprié pour héberger le runtime d’intégration Azure SSIS. Pour plus d’informations, consultez [Sélectionner le sous-réseau](#subnet). 

-   Si vous apportez vos propres adresses IP publiques pour Azure-SSIS IR, consultez [Sélectionner les adresses IP publiques statiques](#publicIP).

-   Si vous utilisez votre propre serveur DNS (Domain Name System) sur le réseau virtuel, consultez [Configurer le serveur DNS](#dns_server). 

-   Si vous utilisez un groupe de sécurité réseau sur le sous-réseau, consultez [Configurer un groupe de sécurité réseau](#nsg). 

-   Si vous utilisez Azure ExpressRoute ou un itinéraire défini par l’utilisateur (UDR), consultez [Utiliser Azure ExpressRoute ou un UDR](#route). 

-   Vérifiez que le groupe de ressources du réseau virtuel (ou le groupe de ressources des adresses IP publiques si vous apportez vos propres adresses IP publiques) peut créer et supprimer certaines ressources réseau Azure. Pour plus d’informations, consultez [Configurer le groupe de ressources](#resource-group). 

-   Si vous personnalisez votre Azure-SSIS IR comme décrit dans [Custom setup for Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup) (Personnaliser la configuration de votre Azure-SSIS IR), vos nœuds Azure-SSIS IR reçoivent des adresse IP privées à partir d’une plage prédéfinie de 172.16.0.0 à 172.31.255.255. Vérifiez donc que les plages d’adresses IP privées de vos réseaux virtuels/locaux n’entrent pas en conflit avec cette plage.

Ce diagramme montre les connexions requises pour votre runtime d’intégration Azure-SSIS :

![Runtime d’intégration Azure SSIS](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a> Définir des autorisations

L’utilisateur qui crée le runtime d’intégration Azure-SSIS IR doit disposer des autorisations suivantes :

- Si vous associez votre runtime d’intégration SSIS à un réseau virtuel Azure Resource Manager, vous avez deux options :

  - Utilisez le rôle Contributeur de réseaux intégré. Ce rôle inclut l’autorisation _Microsoft.Network/\*_ , mais dispose d’une étendue plus large que nécessaire.

  - Créez un rôle personnalisé qui inclut uniquement l’autorisation _Microsoft.Network/virtualNetworks/\*/join/action_ nécessaire. Si vous voulez également apporter vos propres adresses IP publiques pour votre runtime d’intégration SSIS, en plus de le joindre à un réseau virtuel Azure Resource Manager, incluez également l’autorisation _Microsoft.Network/publicIPAddresses/*/join/action_ dans le rôle.

- Si vous associez votre runtime d’intégration SSIS à un réseau virtuel classique, nous vous recommandons d’utiliser le rôle intégré Contributeur de machines virtuelles classiques. Sinon, vous devez définir un rôle personnalisé qui inclut l’autorisation de rejoindre le réseau virtuel.

### <a name="subnet"></a> Sélectionner le sous-réseau

Lorsque vous choisissez un sous-réseau : 

-   Ne sélectionnez pas le GatewaySubnet pour déployer un runtime d’intégration Azure-SSIS IR. Il est réservé aux passerelles de réseau virtuel. 

-   Vérifiez que le sous-réseau sélectionné dispose de suffisamment d’espace d’adressage pour le runtime d’intégration Azure-SSIS IR. Les adresses IP disponibles doivent représenter au moins le double du nombre de nœuds de runtime d’intégration. Azure réserve des adresses IP dans chaque sous-réseau. Ces adresses ne peuvent pas être utilisées. Les première et dernière adresse IP des sous-réseaux sont réservées à la conformité du protocole, et trois adresses supplémentaires sont utilisées pour les services Azure. Pour plus d’informations, consultez [Existe-t-il des restrictions sur l’utilisation des adresses IP au sein de ces sous-réseaux ?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

-   N’utilisez pas un sous-réseau qui est exclusivement occupé par d’autres services Azure (par exemple, instance managée SQL Database, App Service, etc.). 

### <a name="publicIP"></a>Sélectionner les adresses IP publiques statiques
Si vous voulez apporter vos propres adresses IP publiques statiques pour Azure-SSIS IR tout en le joignant à un réseau virtuel, vérifiez qu’elles remplissent les conditions suivantes :

-   Fournissez deux adresses IP publiques statiques inutilisées, qui ne sont pas déjà associées à d’autres ressources des services Azure. L’adresse supplémentaire est utilisée lors de la mise à niveau de votre Azure-SSIS IR.

-   Les adresses IP publiques doivent être statiques et standard. Pour plus d’informations, reportez-vous à [SKU d’adresse IP publique](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku).

-   Les adresses IP publiques statiques doivent toutes les deux porter des noms DNS. Si vous n’avez pas configuré le nom DNS lors de la création de l’adresse IP publique, vous pouvez également le configurer dans le portail Azure.

![Runtime d’intégration Azure SSIS](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

-   Les adresses IP publiques statiques et le réseau virtuel doivent se trouver dans le même abonnement et dans la même région.

### <a name="dns_server"></a> Configurer le serveur DNS 
Si vous devez utiliser votre propre serveur DNS dans un réseau virtuel joint par votre runtime d’intégration Azure SSIS IR, vérifiez qu’il peut résoudre les noms d’hôte Azure globaux (par exemple, un nom d’objet blob de stockage Azure, `<your storage account>.blob.core.windows.net`). 

Les étapes suivantes sont recommandées : 

-   Configurez le serveur DNS personnalisé pour transférer des requêtes à Azure DNS. Vous pouvez transférer des enregistrements DNS non résolus à l’adresse IP des programmes de résolution récursifs d’Azure (168.63.129.16) sur votre propre serveur DNS. 

-   Configurez le serveur DNS personnalisé comme serveur DNS principal pour le réseau virtuel. Configurez Azure DNS comme serveur DNS secondaire. Inscrivez l’adresse IP des programmes de résolution récursifs d’Azure (168.63.129.16) comme serveur DNS secondaire au cas où votre propre serveur DNS ne serait pas disponible. 

Pour plus d’informations, consultez [Résolution de noms utilisant votre propre serveur DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a> Configurer un NSG
Si vous avez besoin d’implémenter un NSG pour le sous-réseau utilisé par votre Azure-SSIS IR, autorisez le trafic entrant et sortant par le biais des ports suivants : 

| Direction | Protocole de transfert | Source | Source port range (Plage de ports sources) | Destination | Destination port range | Commentaires |
|---|---|---|---|---|---|---|
| Trafic entrant | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (si vous joignez le runtime d’intégration à un réseau virtuel Azure Resource Manager) <br/><br/>10100, 20100, 30100 (si vous joignez le runtime d’intégration à un réseau virtuel classique)| Le service Data Factory utilise ces ports pour communiquer avec les nœuds de votre runtime d’intégration Azure SSIS IR sur le réseau virtuel. <br/><br/> Que vous créiez ou non un groupe de sécurité réseau au niveau du sous-réseau, Data Factory configure toujours un groupe de sécurité réseau au niveau des cartes d’interface réseau (NIC) connectées aux machines virtuelles qui hébergent le runtime d’intégration Azure-SSIS. Ce groupe de sécurité réseau au niveau de la carte réseau n’autorise que le trafic entrant provenant d’adresses IP Data Factory sur les ports spécifiés. Même si vous ouvrez ces ports pour le trafic Internet au niveau du sous-réseau, le trafic provenant d’adresses IP qui ne sont pas des adresses IP Data Factory est bloqué au niveau de la carte réseau. |
| Règle de trafic sortant | TCP | VirtualNetwork | * | AzureCloud | 443 | Les nœuds de votre runtime d’intégration Azure SSIS IR sur le réseau virtuel utilisent ce port pour accéder aux services Azure comme Stockage Azure et Azure Event Hubs. |
| Règle de trafic sortant | TCP | VirtualNetwork | * | Internet | 80 | Les nœuds de votre runtime d’intégration Azure-SSIS IR dans le réseau virtuel utilisent ce port pour télécharger une liste de révocation de certificats à partir d’Internet. |
| Règle de trafic sortant | TCP | VirtualNetwork | * | SQL | 1433, 11000-11999 | Les nœuds de votre runtime d’intégration Azure-SSIS IR sur le réseau virtuel utilisent ces ports pour accéder à la base de données SSISDB hébergée par le serveur SQL Database. Si votre stratégie de connexion de serveur SQL Database est définie sur **Proxy** au lieu de **Rediriger**, seul le port 1433 est nécessaire. Cette règle de sécurité de trafic sortant n’est pas applicable à SSISDB hébergé par votre instance managée dans le réseau virtuel. |
||||||||

### <a name="route"></a> Utiliser Azure ExpressRoute ou un UDR
Lorsque vous connectez un circuit [ExpressRoute Azure](https://azure.microsoft.com/services/expressroute/) à votre infrastructure de réseau virtuel pour étendre votre réseau local à Azure, l’une des configurations courantes consiste à utiliser le tunneling forcé (annoncer un itinéraire BGP, 0.0.0.0/0 vers le réseau virtuel). Ce tunneling force le trafic Internet sortant du flux de réseau virtuel vers l’appliance réseau locale à des fins d’inspection et de journalisation. 
 
Vous pouvez aussi définir des [UDR](../virtual-network/virtual-networks-udr-overview.md) pour forcer le trafic Internet sortant du sous-réseau qui héberge le runtime d’intégration Azure-SSIS IR vers un autre sous-réseau, qui héberge une appliance virtuelle réseau en tant que pare-feu ou Pare-feu Azure pour l’inspection et la journalisation. 

Dans les deux cas, l’itinéraire du trafic interrompt la connectivité entrante requise des services Azure Data Factory dépendants (les services de gestion Azure Batch plus spécifiquement) au runtime d’intégration Azure-SSIS IR dans le réseau virtuel. Pour éviter cela, définissez un ou plusieurs UDR sur le sous-réseau qui contient le runtime d’intégration Azure-SSIS IR. 

Vous pouvez appliquer un itinéraire 0.0.0.0/0 avec le type de tronçon suivant défini sur **Internet** sur le sous-réseau qui héberge le runtime d’intégration Azure-SSIS IR dans un scénario Azure ExpressRoute. Vous pouvez aussi modifier l’itinéraire 0.0.0.0/0 existant à partir du type de tronçon suivant en le faisant passer d’**appliance virtuelle** à **Internet** dans des scénarios d’appliance virtuelle réseau.

![Ajouter un itinéraire](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Si vous ne souhaitez pas risquer de perdre la possibilité d’inspecter le trafic Internet sortant de ce sous-réseau, vous pouvez définir des itinéraires définis par l’utilisateur spécifiques pour router uniquement le trafic entre les services de gestion Azure Batch et le runtime d’intégration Azure-SSIS IR avec un type de tronçon suivant défini sur **Internet**.

Par exemple, si votre runtime d’intégration Azure-SSIS IR se trouve dans `UK South`, vous devrez récupérer la liste des plages d’adresses IP d’étiquette de service `BatchNodeManagement.UKSouth` à partir du [lien de téléchargement des plages d’adresses IP d’étiquette de service](https://www.microsoft.com/en-us/download/details.aspx?id=56519) ou via l’[API de détection des étiquettes de service](https://aka.ms/discoveryapi). Appliquez ensuite les UDR suivants pour l’itinéraire de plage d’adresses IP associé au type de tronçon suivant défini sur **Internet**.

![Paramètres des UDR Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Cette approche nécessite une maintenance supplémentaire. Vous devez vérifier régulièrement la plage d’adresses IP et en ajouter une nouvelle à votre itinéraire défini par l’utilisateur pour éviter d’interrompre le runtime d’intégration Azure-SSIS IR. Nous vous recommandons de vérifier la plage d’adresses IP mensuellement car, lorsque la nouvelle adresse IP apparaît dans la balise de service, sa validation prend un mois supplémentaire. 

### <a name="resource-group"></a> Configurer le groupe de ressources
Le runtime d’intégration Azure-SSIS doit créer certaines ressources réseau sous le même groupe de ressources que le réseau virtuel. Ces ressources incluent :
   -   Un équilibreur de charge Azure, avec le nom *\<<GUID>-azurebatch-cloudserviceloadbalancer*.
   -   Une adresse IP publique Azure, avec le nom *\<<GUID>-azurebatch-cloudservicepublicip*.
   -   Un groupe de sécurité de travail réseau, avec le nom *\<<GUID>-azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> Vous pouvez maintenant apporter vos propres adresses IP publiques statiques pour votre Azure-SSIS IR. Dans ce scénario, nous créons uniquement l’équilibreur de charge Azure et le groupe de sécurité réseau pour vous. De plus, les ressources sont créées dans le même groupe de ressources que celui de vos adresses IP publiques plutôt que celui du réseau virtuel.

Ces ressources sont créées au démarrage du runtime d’intégration. Elles sont supprimées à l’arrêt de ce dernier. Notez que si vous apportez vos propres adresses IP publiques, celles-ci ne sont pas supprimées après l’arrêt du runtime d’intégration. Pour éviter de bloquer l’arrêt du runtime d’intégration, ne réutilisez pas ces ressources réseau dans vos autres ressources. 

Vérifiez que vous n’avez pas de verrou sur le groupe de ressources ou l’abonnement auquel appartient le réseau virtuel (ou les adresses IP publiques si vous apportez les vôtres). Si vous configurez un verrou en lecture seule ou un verrou de suppression, le démarrage et l’arrêt du runtime d’intégration peuvent échouer, faute de quoi le runtime d’intégration ne peut pas répondre. 

Vérifiez qu’aucune stratégie Azure n’empêche la création des ressources suivantes dans le groupe de ressources ou l’abonnement auquel appartient le réseau virtuel (ou les adresses IP publiques si vous apportez les vôtres) : 
   -   Microsoft.Network/LoadBalancers 
   -   Microsoft.Network/NetworkSecurityGroups 
   -   Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a> FAQ

- Comment protéger l’adresse IP publique exposée sur le runtime d’intégration Azure-SSIS IR pour la connexion entrante ? Est-il possible de supprimer une adresse IP publique ?
 
    À l’heure actuelle, une adresse IP publique est créée automatiquement lorsque le runtime d’intégration Azure-SSIS IR rejoint le réseau virtuel. Nous disposons d’un groupe de sécurité réseau au niveau de la carte réseau pour autoriser uniquement les services de gestion Azure Batch à établir une connexion entrante au runtime d’intégration Azure-SSIS IR. Vous pouvez également spécifier le groupe de sécurité réseau au niveau du sous-réseau pour la protection entrante.

    Si vous ne souhaitez pas que l’adresse IP publique soit exposée, vous pouvez envisager de [configurer le runtime d’intégration auto-hébergé en tant que proxy pour le runtime d’intégration Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) au lieu du réseau virtuel si cela s’applique à votre scénario.
 
- Puis-je ajouter l’adresse IP statique du runtime d’intégration Azure-SSIS IR dans la liste verte du pare-feu pour la source de données ?

    Vous pouvez maintenant apporter vos propres adresses IP publiques statiques pour Azure-SSIS IR. Dans ce cas, vous pouvez ajouter les adresses IP fournies aux listes vertes de sources de données du pare-feu. Vous pouvez également prendre en compte les options ci-dessous pour autoriser Azure-SSIS IR à accéder à votre source de données en fonction de votre scénario :

    - Si votre source de données est locale, une fois que vous avez connecté le réseau virtuel à votre réseau local et joint votre runtime d’intégration Azure-SSIS IR à ce sous-réseau de réseau virtuel, vous pouvez ajouter la plage d’adresses IP de ce sous-réseau dans la liste verte.
    - Si votre source de données est un service Azure pris en charge avec le point de terminaison de service de réseau virtuel, vous pouvez configurer ce dernier sur votre réseau virtuel et joindre votre runtime d’intégration Azure-SSIS IR à ce sous-réseau de réseau virtuel. Vous pouvez ensuite utiliser la règle de réseau virtuel des services Azure au lieu de la plage d’adresses IP pour autoriser l’accès.
    - Si votre source de données est une autre source de données cloud, vous pouvez utiliser un itinéraire défini par l’utilisateur pour router le trafic sortant du runtime d’intégration Azure-SSIS IR vers l’appliance virtuelle réseau ou le Pare-feu Azure avec une adresse IP publique statique. Vous pouvez donc ajouter l’adresse IP publique de l’appliance virtuelle réseau ou du Pare-feu Azure dans la liste verte.
    - Si les réponses précédentes ne satisfont pas vos besoins, pensez à fournir l’accès à la source de données [en configurant un runtime d’intégration auto-hébergé en tant que proxy pour le runtime d’intégration Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). Ensuite, vous pouvez ajouter l’adresse IP de l’ordinateur qui héberge le runtime d’intégration auto-hébergé à la liste verte au lieu de joindre le runtime d’intégration Azure-SSIS dans le réseau virtuel.

- Pourquoi dois-je fournir deux adresses publiques statiques si je veux apporter mes propres adresses IP publiques pour Azure-SSIS IR ?

    Azure-SSIS IR est automatiquement mis à jour de façon régulière. De nouveaux nœuds IR sont créés lors de la mise à niveau et les anciens nœuds sont supprimés. Toutefois, pour éviter un temps d’arrêt, les anciens nœuds ne sont pas supprimés tant que les nouveaux ne sont pas prêts. Ainsi, votre première adresse IP publique utilisée par les anciens nœuds ne peut pas être libérée immédiatement et nous avons donc besoin d’une autre adresse IP publique pour créer les nouveaux nœuds IR.
- J’ai apporté mes propres adresses IP publiques statiques pour Azure-SSIS IR, mais le runtime d’intégration ne peut toujours pas accéder aux sources de données ou ressources.

    - Vérifiez que les deux adresses IP publiques statiques sont ajoutées à la liste verte de vos sources de données ou ressources. Après la mise à niveau de Azure-SSIS IR, l’adresse IP publique du runtime d’intégration bascule vers l’adresse IP publique secondaire. Si vous ajoutez une seule d’entre elles à la liste verte, l’accès risque d’être interrompu après la mise à niveau.

    - Si votre source de données est un service Azure, vérifiez si vous avez configuré le sous-réseau de réseau virtuel avec un point de terminaison de service. Si des points de terminaison de service sont définis, le trafic de service change pour utiliser des adresses privées gérées par des services Azure en tant qu’adresses IP sources lors de l’accès au service Azure à partir d’un réseau virtuel. Dans ce cas, l’ajout de vos propres adresses IP publiques à la liste verte n’a aucun effet.

## <a name="azure-portal-data-factory-ui"></a>Portail Azure (interface utilisateur de Data Factory)
Cette section vous montre comment joindre un runtime d’intégration Azure-SSIS IR existant à un réseau virtuel (classique ou Azure Resource Manager) à l’aide du portail Azure et de l’interface utilisateur de Data Factory. 

Vous devez configurer le réseau virtuel de façon appropriée avant d’y joindre votre runtime d’intégration Azure SSIS IR. Effectuez les étapes de la section appropriée en fonction du type de votre réseau virtuel (classique ou Azure Resource Manager). Ensuite, effectuez les étapes de la troisième section pour joindre votre runtime d’intégration Azure SSIS IR au réseau virtuel. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Configurer un réseau virtuel Azure Resource Manager

Utilisez le portail pour configurer un réseau virtuel Azure Resource Manager avant de tenter d’y joindre un runtime d’intégration Azure-SSIS IR.

1. Démarrez Microsoft Edge ou Google Chrome. Actuellement, seuls les navigateurs web prennent en charge l’interface utilisateur. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 

1. Sélectionnez **Plus de services**. Filtrez et sélectionnez **Réseaux virtuels**. 

1. Filtrez et sélectionnez votre réseau virtuel dans la liste. 

1. Dans la page **Réseau virtuel**, sélectionnez **Propriétés**. 

1. Sélectionnez le bouton Copier au niveau de **ID DE RESSOURCE** pour copier l’ID de ressource du réseau virtuel dans le Presse-papiers. Enregistrez dans OneNote ou un fichier l’ID se trouvant dans le Presse-papiers. 

1. Dans le menu de gauche, sélectionnez **Sous-réseaux**. Vérifiez que le nombre d’adresses disponibles est supérieur à celui des nœuds dans votre runtime d’intégration Azure-SSIS IR. 

1. Vérifiez que le fournisseur Azure Batch est bien inscrit dans l’abonnement Azure qui contient le réseau virtuel. Si ce n’est pas le cas, inscrivez le fournisseur Azure Batch. Si vous possédez déjà un compte Azure Batch dans votre abonnement, ce dernier est inscrit pour Azure Batch. (Si vous créez le runtime d’intégration Azure SSIS dans le portail Data Factory, le fournisseur Azure Batch est inscrit automatiquement pour vous.) 

   a. Dans le menu de gauche du Portail Azure, sélectionnez **Abonnements**. 

   b. Sélectionnez votre abonnement. 

   c. À gauche, sélectionnez **Fournisseurs de ressources**, puis confirmez que **Microsoft.Batch** est un fournisseur inscrit. 

   ![Confirmation de l’état « Inscrit »](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Si **Microsoft.Batch** ne figure pas dans la liste, il vous faut l’inscrire. Pour cela, [créez un compte Azure Batch vide](../batch/batch-account-create-portal.md) dans votre abonnement. Vous pourrez le supprimer par la suite. 

### <a name="configure-a-classic-virtual-network"></a>Configurer un réseau virtuel classique
Utilisez le portail pour configurer un réseau virtuel classique avant de tenter d’y joindre un runtime d’intégration Azure-SSIS. 

1. Démarrez Microsoft Edge ou Google Chrome. Actuellement, seuls les navigateurs web prennent en charge l’interface utilisateur. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 

1. Sélectionnez **Plus de services**. Filtrez et sélectionnez **Réseaux virtuels (classiques)** . 

1. Filtrez et sélectionnez votre réseau virtuel dans la liste. 

1. Dans la page **Réseau virtuel (classique)** , sélectionnez **Propriétés**. 

   ![ID de ressource de réseau virtuel classique](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Sélectionnez le bouton Copier au niveau de **ID DE RESSOURCE** pour copier l’ID de ressource du réseau classique dans le Presse-papiers. Enregistrez dans OneNote ou un fichier l’ID se trouvant dans le Presse-papiers. 

1. Dans le menu de gauche, sélectionnez **Sous-réseaux**. Vérifiez que le nombre d’adresses disponibles est supérieur à celui des nœuds dans votre runtime d’intégration Azure-SSIS IR. 

   ![Nombre d’adresses disponibles sur le réseau virtuel](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Joignez **MicrosoftAzureBatch** au rôle **Contributeur de machines virtuelles classiques** pour le réseau virtuel. 

    a. Dans le menu de gauche, sélectionnez **Contrôle d’accès (IAM)** , puis sélectionnez l’onglet **Attributions de rôles**. 

    ![Boutons « Contrôle d’accès » et « Ajouter »](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Sélectionnez **Ajouter une attribution de rôle**.

    c. Dans la page **Ajouter une attribution de rôle**, sélectionnez **Contributeur de machines virtuelles classiques** pour **Rôle**. Dans la zone **Sélectionner**, collez **ddbf3205-c6bd-46ae-8127-60eb93363864**, puis sélectionnez **Microsoft Azure Batch** dans la liste des résultats de recherche. 

    ![Résultats de la recherche dans la page « Ajouter une attribution de rôle »](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. Sélectionnez **Enregistrer** pour enregistrer les paramètres et fermer la page. 

    ![Enregistrer les paramètres d’accès](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. Confirmez que **Microsoft Azure Batch** apparaît bien dans la liste des contributeurs. 

    ![Confirmer l’accès à Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Vérifiez que le fournisseur Azure Batch est bien inscrit dans l’abonnement Azure qui contient le réseau virtuel. Si ce n’est pas le cas, inscrivez le fournisseur Azure Batch. Si vous possédez déjà un compte Azure Batch dans votre abonnement, ce dernier est inscrit pour Azure Batch. (Si vous créez le runtime d’intégration Azure SSIS dans le portail Data Factory, le fournisseur Azure Batch est inscrit automatiquement pour vous.) 

   a. Dans le menu de gauche du Portail Azure, sélectionnez **Abonnements**. 

   b. Sélectionnez votre abonnement. 

   c. À gauche, sélectionnez **Fournisseurs de ressources**, puis confirmez que **Microsoft.Batch** est un fournisseur inscrit. 

   ![Confirmation de l’état « Inscrit »](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Si **Microsoft.Batch** ne figure pas dans la liste, il vous faut l’inscrire. Pour cela, [créez un compte Azure Batch vide](../batch/batch-account-create-portal.md) dans votre abonnement. Vous pourrez le supprimer par la suite. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Joindre le runtime d’intégration Azure-SSIS à un réseau virtuel

Après avoir configuré votre réseau virtuel Azure Resource Manager ou votre réseau virtuel classique, vous pouvez joindre le runtime d’intégration Azure-SSIS IR au réseau virtuel :

1. Démarrez Microsoft Edge ou Google Chrome. Actuellement, seuls les navigateurs web prennent en charge l’interface utilisateur. 

1. Dans le menu de gauche du [Portail Azure](https://portal.azure.com), sélectionnez **Fabriques de données**. Si vous ne voyez pas **Fabriques de données** dans le menu, sélectionnez **Autres services**, puis sélectionnez **Fabriques de données** dans la section **INTELLIGENCE + ANALYSE**. 

   ![Liste de fabriques de données](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Dans la liste, sélectionnez votre fabrique de données avec Azure-SSIS IR. La page d’accueil de votre fabrique de données apparaît. Sélectionnez la vignette **Créer et déployer**. L’interface utilisateur de Data Factory apparaît sous un onglet séparé. 

   ![Page d’accueil Data Factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Dans l’interface utilisateur de Data Factory, basculez vers l’onglet **Modifier**, sélectionnez **Connexions**, puis basculez vers l’onglet **Runtimes d’intégration**. 

   ![Onglet « Runtimes d’intégration »](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Si votre runtime d’intégration Azure-SSIS IR est en cours d’exécution, dans la liste **Runtimes d’intégration**, au niveau de la colonne **Actions**, sélectionnez le bouton **Arrêter** pour votre runtime d’intégration Azure-SSIS IR. Vous ne pouvez pas modifier un runtime d’intégration tant que vous ne l’arrêtez pas. 

   ![Arrêter le runtime d’intégration](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Dans la liste **Runtimes d’intégration**, au niveau de la colonne **Actions**, sélectionnez le bouton **Modifier** pour votre runtime d’intégration Azure-SSIS IR. 

   ![Modifier le runtime d’intégration](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Dans le panneau de **configuration du runtime d’intégration**, parcourez les pages **Paramètres généraux** et **Paramètres SQL** en sélectionnant le bouton **Suivant**. 

1. Sur la page **Paramètres avancés** : 

   a. Cochez la case en regard de **Select a VNet** (Sélectionner un réseau virtuel). 

   b. Pour **Abonnement**, sélectionnez votre abonnement Azure. Sous l’abonnement, vous pouvez sélectionner un réseau virtuel existant. 
  
   c. Pour **Nom du réseau virtuel**, sélectionnez votre réseau virtuel. 

   d. Pour **Nom du sous-réseau**, sélectionnez votre sous-réseau sur le réseau virtuel. 

   e. Si vous voulez apporter votre propre adresse IP publique statique pour Azure-SSIS IR, cochez la case **Apporter des adresses IP publiques statiques**. Ensuite, indiquez la première et la seconde adresse IP publique statique pour votre Azure-SSIS IR. Vous pouvez également cliquer sur le bouton **Créer** pour créer une adresse IP publique. Consultez [Sélectionner les adresses IP publiques statiques](#publicIP) pour connaître les exigences des adresses IP publiques.

   f. Si vous souhaitez également configurer ou gérer un IR auto-hébergé en tant que proxy pour votre Azure-SSIS IR, sélectionnez la case à cocher **Set-up Self-Hosted** (Configurer l’auto-hébergé). Pour plus d’informations, consultez [Configurer l’IR auto-hébergé comme proxy pour Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).

   g. Sélectionnez le bouton **Validation du réseau virtuel**. Si la validation réussit, sélectionnez le bouton **Suivant**. 

   ![Paramètres avancés pour la configuration du runtime d’intégration](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. Sur la page **Résumé**, passez en revue tous les paramètres de votre runtime d’intégration Azure-SSIS IR. Cliquez ensuite sur le bouton **Mettre à jour**.

1. Démarrez votre Azure-SSIS IR en sélectionnant le bouton **Démarrer** situé dans la colonne **Actions** en regard de votre Azure-SSIS IR. Le démarrage de votre Azure-SSIS IR qui joint un réseau virtuel prend 20 à 30 minutes environ. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>Configurer un réseau virtuel
Avant de joindre votre runtime d’intégration Azure-SSIS à un réseau virtuel, vous devez configurer ce dernier. Pour configurer automatiquement les paramètres et les autorisations du réseau virtuel pour le runtime d’intégration Azure-SSIS IR à joindre au réseau virtuel, ajoutez le script suivant :

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Créer un runtime d’intégration Azure-SSIS et le joindre à un réseau virtuel
Vous pouvez créer un runtime d’intégration Azure-SSIS et le joindre à un réseau virtuel en même temps. Pour obtenir le script complet et toutes les instructions, consultez [Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel
L’article [Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md) explique comment créer un runtime d’intégration Azure-SSIS IR et le joindre à un réseau virtuel dans le même script. Si vous disposez d’un runtime d’intégration Azure-SSIS IR, effectuez les opérations suivantes pour le joindre au réseau virtuel : 
1. Arrêtez le runtime d’intégration Azure-SSIS. 
1. Configurez le runtime d’intégration Azure-SSIS pour le joindre au réseau virtuel. 
1. Démarrez le runtime d’intégration Azure-SSIS. 

### <a name="define-the-variables"></a>Définir les variables
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Arrêter le runtime d’intégration Azure-SSIS
Vous devez arrêter le runtime d’intégration Azure-SSIS IR avant de le joindre à un réseau virtuel. Cette commande libère tous ses nœuds et arrête la facturation :

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Configurer les paramètres du réseau virtuel pour le runtime d’intégration Azure-SSIS à joindre

Pour configurer les paramètres du réseau virtuel auquel le runtime d’intégration Azure-SSIS va se joindre, utilisez ce script : 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Configurer le runtime d’intégration Azure-SSIS
Pour configurer le runtime d’intégration Azure-SSIS IR afin qu’il joigne le réseau virtuel, exécutez la commande `Set-AzDataFactoryV2IntegrationRuntime` : 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Démarrer le runtime d’intégration Azure-SSIS
Pour démarrer le runtime d’intégration Azure-SSIS IR, exécutez la commande suivante : 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

Cette commande prend de 20 à 30 minutes.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le runtime d’intégration Azure-SSIS IR, voir les articles suivants : 
- [Runtime d’intégration Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Cet article fournit des informations conceptuelles générales sur les runtimes d’intégration, notamment sur Azure-SSIS IR. 
- [Tutoriel : Déployer des packages SSIS vers Azure](tutorial-create-azure-ssis-runtime-portal.md). Ce didacticiel fournit des instructions pas à pas pour créer votre Azure-SSIS IR. Il utilise Azure SQL Database pour héberger le catalogue SSIS. 
- [Créez un runtime d’intégration Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Cet article s’appuie sur le didacticiel. Il fournit des instructions sur l’utilisation d’Azure SQL Database avec des points de terminaison de service de réseau virtuel ou une instance managée dans un réseau virtuel pour héberger le catalogue SSIS. Il explique comment joindre votre runtime d’intégration Azure-SSIS IR à un réseau virtuel. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article vous montre comment obtenir des informations sur votre runtime d’intégration Azure-SSIS IR. Il fournit des descriptions sur l’état des informations renvoyées. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer votre Azure-SSIS IR. Il vous montre également comment faire monter le runtime d’intégration Azure-SSIS en puissance en lui ajoutant des nœuds.
