---
title: Configurer un réseau virtuel Azure pour un runtime d’intégration Azure-SSIS
description: Découvrez comment configurer un réseau virtuel Azure pour un runtime d’intégration Azure-SSIS.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b4274144e8437bd47adad0854fec5c3ed9550ffb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229405"
---
# <a name="virtual-network-configuration-for-an-azure-ssis-integration-runtime"></a>Configuration de réseau virtuel pour un runtime d’intégration Azure-SSIS

Configurez votre réseau virtuel conformément à ce qui suit : 

- Vérifiez que `Microsoft.Batch` est un fournisseur inscrit sous l’abonnement de votre sous-réseau de machine virtuelle qui héberge le runtime d’intégration Azure SSIS IR. Si vous utilisez un réseau virtuel classique, vous devez également joindre `MicrosoftAzureBatch` au rôle Collaborateur de machine virtuelle classique pour ce réseau virtuel. 

- Assurez-vous de disposer des autorisations requises. Pour plus d’informations, consultez [Configuration d’autorisations](#perms).

- Sélectionnez le sous-réseau approprié pour héberger le runtime d’intégration Azure SSIS. Pour plus d’informations, consultez [Sélectionner le sous-réseau](#subnet). 

- Si vous apportez vos propres adresses IP publiques pour Azure-SSIS IR, consultez [Sélectionner les adresses IP publiques statiques](#publicIP).

- Si vous utilisez votre propre serveur DNS (Domain Name System) sur le réseau virtuel, consultez [Configurer le serveur DNS](#dns_server). 

- Si vous utilisez un groupe de sécurité réseau sur le sous-réseau, consultez [Configurer un groupe de sécurité réseau](#nsg). 

- Si vous utilisez Azure ExpressRoute ou un itinéraire défini par l’utilisateur (UDR), consultez [Utiliser Azure ExpressRoute ou un UDR](#route). 

- Vérifiez que le groupe de ressources du réseau virtuel (ou le groupe de ressources des adresses IP publiques si vous apportez vos propres adresses IP publiques) peut créer et supprimer certaines ressources réseau Azure. Pour plus d’informations, consultez [Configurer le groupe de ressources](#resource-group). 

- Si vous personnalisez votre runtime Azure-SSIS IR conformément à [Personnalisation de la configuration d’Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md), votre processus interne de gestion de ses nœuds consommera des adresse IP privées à partir de la plage prédéfinie 172.16.0.0-172.31.255.255. Vérifiez que les plages d’adresses IP privées de vos réseaux virtuels et locaux n’entrent pas en conflit avec cette plage.

Ce diagramme montre les connexions requises pour votre runtime d’intégration Azure-SSIS :

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png" alt-text="Diagramme montrant les connexions requises pour votre runtime d’intégration Azure-SSIS.":::

## <a name="set-up-permissions"></a><a name="perms"></a> Définir des autorisations

L’utilisateur qui crée le runtime d’intégration Azure-SSIS IR doit disposer des autorisations suivantes :

- Si vous associez votre runtime d’intégration SSIS à un réseau virtuel Azure Resource Manager, vous avez deux options :

  - Utilisez le rôle Contributeur de réseaux intégré. Ce rôle inclut l’autorisation _Microsoft.Network/\*_ , mais dispose d’une étendue plus large que nécessaire.

  - Créez un rôle personnalisé qui inclut uniquement l’autorisation _Microsoft.Network/virtualNetworks/\*/join/action_ nécessaire. Si vous voulez également apporter vos propres adresses IP publiques pour Azure-SSIS IR tout en le joignant à un réseau virtuel Azure Resource Manager, incluez également l’autorisation _Microsoft.Network/publicIPAddresses/*/join/action_ dans le rôle.

- Si vous associez votre runtime d’intégration SSIS à un réseau virtuel classique, nous vous recommandons d’utiliser le rôle intégré Contributeur de machines virtuelles classiques. Sinon, vous devez définir un rôle personnalisé qui inclut l’autorisation de rejoindre le réseau virtuel.

## <a name="select-the-subnet"></a><a name="subnet"></a> Sélectionner le sous-réseau

Lorsque vous choisissez un sous-réseau : 

- Ne sélectionnez pas le GatewaySubnet pour déployer un runtime d’intégration Azure-SSIS IR. Il est réservé aux passerelles de réseau virtuel. 

- Vérifiez que le sous-réseau sélectionné dispose de suffisamment d’espace d’adressage pour le runtime d’intégration Azure-SSIS IR. Les adresses IP disponibles doivent représenter au moins le double du nombre de nœuds de runtime d’intégration. Azure réserve des adresses IP dans chaque sous-réseau. Ces adresses ne peuvent pas être utilisées. Les première et dernière adresse IP des sous-réseaux sont réservées à la conformité du protocole, et trois adresses supplémentaires sont utilisées pour les services Azure. Pour plus d’informations, consultez [Existe-t-il des restrictions sur l’utilisation des adresses IP au sein de ces sous-réseaux ?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- N'utilisez pas un sous-réseau qui est exclusivement occupé par d'autres services Azure (par exemple, SQL Database SQL Managed Instance, App Service, etc.). 

## <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>Sélectionner les adresses IP publiques statiques

Si vous voulez apporter vos propres adresses IP publiques statiques pour Azure-SSIS IR tout en le joignant à un réseau virtuel, vérifiez qu’elles remplissent les conditions suivantes :

- Vous devez en fournir exactement deux non utilisées qui ne soient pas déjà associées à d’autres ressources Azure. L’adresse supplémentaire est utilisée lors de la mise à niveau régulière de votre Azure-SSIS IR. Notez qu’une adresse IP publique ne peut pas être partagée parmi vos instances Azure-SSIS IR actives.

- Il doit s’agir de deux adresses statiques de type standard. Pour plus d’informations, reportez-vous à [SKU d’adresse IP publique](../virtual-network/ip-services/public-ip-addresses.md#sku).

- Toutes deux doivent avoir un nom DNS. Si vous n’avez pas fourni de nom DNS lors de leur création, vous pouvez le faire sur le portail Azure.

:::image type="content" source="media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png" alt-text="Runtime d’intégration Azure SSIS":::

- Les adresses IP et le réseau virtuel doivent se trouver dans le même abonnement et dans la même région.

## <a name="set-up-the-dns-server"></a><a name="dns_server"></a> Configurer le serveur DNS 
Si vous devez utiliser votre propre serveur DNS dans un réseau virtuel joint par votre runtime d’intégration Azure SSIS IR pour résoudre votre nom d’hôte privé, vérifiez qu’il peut également résoudre les noms d’hôte Azure globaux (par exemple, un nom d’objet blob de stockage Azure, `<your storage account>.blob.core.windows.net`). 

Une approche recommandée est la suivante : 

-   Configurez le serveur DNS personnalisé pour transférer des requêtes à Azure DNS. Vous pouvez transférer des enregistrements DNS non résolus à l’adresse IP des programmes de résolution récursifs d’Azure (168.63.129.16) sur votre propre serveur DNS. 

Pour plus d’informations, consultez [Résolution de noms utilisant votre propre serveur DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

> [!NOTE]
> Utilisez un nom de domaine complet (FQDN) pour votre nom d’hôte privé (par exemple, utilisez `<your_private_server>.contoso.com` au lieu de `<your_private_server>`). Vous pouvez également utiliser une installation personnalisée standard dans Azure-SSIS IR pour ajouter automatiquement votre propre suffixe DNS (par exemple `contoso.com`) à un nom de domaine à étiquette unique non qualifié, et le convertir en nom de domaine complet avant de l’utiliser dans des requêtes DNS. Pour plus d’informations, consultez [Exemples d’installation personnalisée standard](./how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples). 

## <a name="set-up-an-nsg"></a><a name="nsg"></a> Configurer un NSG
Si vous avez besoin d’implémenter un NSG pour le sous-réseau utilisé par votre Azure-SSIS IR, autorisez le trafic entrant et sortant par le biais des ports suivants : 

-   **Exigence en entrée d’Azure-SSIS IR**

| Sens | Protocole de transfert | Source | Source port range (Plage de ports sources) | Destination | Destination port range | Commentaires |
|---|---|---|---|---|---|---|
| Trafic entrant | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (si vous joignez le runtime d’intégration à un réseau virtuel Azure Resource Manager) <br/><br/>10100, 20100, 30100 (si vous joignez le runtime d’intégration à un réseau virtuel classique)| Le service Data Factory utilise ces ports pour communiquer avec les nœuds de votre runtime d’intégration Azure SSIS IR sur le réseau virtuel. <br/><br/> Que vous créiez ou non un groupe de sécurité réseau au niveau du sous-réseau, Data Factory configure toujours un groupe de sécurité réseau au niveau des cartes d’interface réseau (NIC) connectées aux machines virtuelles qui hébergent le runtime d’intégration Azure-SSIS. Ce groupe de sécurité réseau au niveau de la carte réseau n’autorise que le trafic entrant provenant d’adresses IP Data Factory sur les ports spécifiés. Même si vous ouvrez ces ports pour le trafic Internet au niveau du sous-réseau, le trafic provenant d’adresses IP qui ne sont pas des adresses IP Data Factory est bloqué au niveau de la carte réseau. |
| Trafic entrant | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | (Facultatif) Cette règle est obligatoire uniquement lorsque le support technique de Microsoft demande une ouverture pour le dépannage avancé d’un client qui peut être fermé juste après le dépannage. La balise de service **CorpNetSaw** autorise uniquement les stations de travail à accès sécurisé sur le réseau d’entreprise Microsoft à utiliser le bureau à distance. Par ailleurs, cette balise de service ne peut pas être sélectionnée à partir du portail et n’est disponible que via Azure PowerShell ou Azure CLI. <br/><br/> Dans le groupe de sécurité réseau au niveau carte réseau, le port 3389 est ouvert par défaut et nous vous autorisons à contrôler le port 3389 dans le groupe de sécurité réseau au niveau sous-réseau, tandis qu’Azure-SSIS IR a interdit le port 3389 sortant par défaut dans la règle de pare-feu Windows sur chaque nœud IR pour la protection. |
||||||||

-   **Exigence en sotie d’Azure-SSIS IR**

| Sens | Protocole de transfert | Source | Source port range (Plage de ports sources) | Destination | Destination port range | Commentaires |
|---|---|---|---|---|---|---|
| Règle de trafic sortant | TCP | VirtualNetwork | * | AzureCloud | 443 | Les nœuds de votre runtime d’intégration Azure SSIS IR sur le réseau virtuel utilisent ce port pour accéder aux services Azure comme Stockage Azure et Azure Event Hubs. |
| Règle de trafic sortant | TCP | VirtualNetwork | * | Internet | 80 | (Facultatif) Les nœuds de votre Azure-SSIS IR dans le réseau virtuel utilisent ce port pour télécharger une liste de révocation de certificats à partir d’Internet. Si vous bloquez ce trafic, vous risquez de constater une dégradation des performances lors du démarrage d’IR et de perdre la possibilité de vérifier la liste de révocation de certificats pour l’utilisation des certificats. Si vous souhaitez restreindre la destination à certains noms de domaine complets (FQDN), reportez-vous à la section **Utiliser Azure ExpressRoute ou un itinéraire défini par l’utilisateur (UDR)**|
| Règle de trafic sortant | TCP | VirtualNetwork | * | SQL | 1433, 11000-11999 | (Facultatif) Cette règle n’est obligatoire que lorsque les nœuds de votre Azure-SSIS IR dans le réseau virtuel accèdent à un une SSISDB hébergée par votre serveur. Si votre stratégie de connexion de serveur est définie sur **Proxy** au lieu de **Rediriger**, seul le port 1433 est nécessaire. <br/><br/> Cette règle de sécurité en sortie n’est pas applicable à une SSISDB hébergée par votre instance SQL Managed Instance dans le réseau virtuel ou SQL Database configuré avec un point de terminaison privé. |
| Règle de trafic sortant | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | (Facultatif) Cette règle n’est obligatoire que quand les nœuds de votre Azure-SSIS IR dans le réseau virtuel accèdent à une SSISDB hébergée par votre instance géréeSQL Managed Instance dans le réseau virtuel ou SQL Database configuré avec un point de terminaison privé. Si votre stratégie de connexion de serveur est définie sur **Proxy** au lieu de **Rediriger**, seul le port 1433 est nécessaire. |
| Règle de trafic sortant | TCP | VirtualNetwork | * | Stockage | 445 | (Facultatif) Cette règle n’est obligatoire que quand vous souhaitez exécuter un package SSIS stocké dans Azure Files. |
||||||||

## <a name="use-azure-expressroute-or-udr"></a><a name="route"></a> Utiliser Azure ExpressRoute ou un itinéraire défini par l’utilisateur (UDR)
Si vous souhaitez inspecter le trafic sortant d’Azure-SSIS IR, vous pouvez acheminer le trafic partant d’Azure-SSIS IR vers une appliance de pare-feu local via un tunneling forcé [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) (publication d’un itinéraire BGP, 0.0.0.0/0, vers le réseau virtuel) ou vers une appliance virtuelle réseau telle qu’un pare-feu ou le [Pare-feu Azure](../firewall/index.yml) via des [Itinéraires définis par l’utilisateur (UDR).](../virtual-network/virtual-networks-udr-overview.md) 

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png" alt-text="Scénario d’appliance virtuelle réseau pour Azure-SSIS IR":::

Vous devez effectuer les opérations suivantes pour faire fonctionner le scénario entier
   -   Le trafic entrant entre les services de gestion Azure Batch et l’Azure-SSIS IR ne peut pas être acheminé via une appliance de pare-feu.
   -   L’appliance de pare-feu autorise le trafic sortant requis par Azure-SSIS IR.

Le trafic entrant entre les services de gestion Azure Batch et l’Azure-SSIS IR ne peut pas être acheminé via une appliance de pare-feu, au risque d’être interrompu en raison d’un problème de routage asymétrique. Des itinéraires doivent être définis pour le trafic entrant afin que le trafic puisse repartir comme il est venu. Vous pouvez établir des itinéraires définis par l’utilisateur spécifiques pour router le trafic entre les services de gestion Azure Batch et l’Azure-SSIS IR avec un type de tronçon suivant tel qu’**Internet**.

Par exemple, si votre Azure-SSIS IR se trouve dans la région `UK South` et que vous souhaitez inspecter le trafic sortant via le Pare-feu Azure, vous devez commencer par obtenir une liste de plages d’adresses IP de la balise de service `BatchNodeManagement.UKSouth` à partir du [lien de téléchargement de plage d’adresses IP des balises de service](https://www.microsoft.com/download/details.aspx?id=56519) ou via l’[API de découverte de balise de service](../virtual-network/service-tags-overview.md#service-tags-on-premises). Appliquez ensuite les itinéraires définis par l’utilisateur suivants des itinéraires d’adresses IP avec le type de tronçon suivant **Internet** en même temps que l’itinéraire 0.0.0.0/0 avec le type de tronçon suivant **appliance virtuelle**.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png" alt-text="Paramètres des UDR Azure Batch":::

> [!NOTE]
> Cette approche nécessite une maintenance supplémentaire. Vous devez vérifier régulièrement la plage d’adresses IP et en ajouter une nouvelle à votre itinéraire défini par l’utilisateur pour éviter d’interrompre le runtime d’intégration Azure-SSIS IR. Nous vous recommandons de vérifier la plage d’adresses IP mensuellement car, lorsque la nouvelle adresse IP apparaît dans la balise de service, sa validation prend un mois supplémentaire. 

Pour faciliter la configuration des règles UDR, vous pouvez exécuter le script PowerShell suivant afin d’ajouter des règles UDR pour les services de gestion d’Azure Batch :
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

Pour que l’appliance de pare-feu autorise le trafic sortant, vous devez autoriser la sortie vers les ports ci-dessous comme exigences dans les règles de trafic sortant du groupe de sécurité réseau.
-   Port 443 avec pour destination des services cloud Azure.

    Si vous utilisez le Pare-feu Azure, vous pouvez spécifier la règle de réseau avec une balise de service AzureCloud. Pour les pare-feu des autres types, vous pouvez simplement autoriser la destination pour le port 443 ou autoriser les noms de domaine complets en fonction du type de votre environnement Azure :

    | Environnement Azure | Points de terminaison                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure (public)      | <ul><li><b>Azure Data Factory (gestion)</b><ul><li>\*.frontend.clouddatahub.net</li></ul></li><li><b>Stockage Azure (gestion)</b><ul><li>\*.blob.core.windows.net</li><li>\*.table.core.windows.net</li></ul></li><li><b>Azure Container Registry (installation personnalisée)</b><ul><li>\*.azurecr.io</li></ul></li><li><b>Event Hub (journalisation)</b><ul><li>\*.servicebus.windows.net</li></ul></li><li><b>Service de journalisation Microsoft (utilisation interne)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Azure Data Factory (gestion)</b><ul><li>\*.frontend.datamovement.azure.us</li></ul></li><li><b>Stockage Azure (gestion)</b><ul><li>\*.blob.core.usgovcloudapi.net</li><li>\*.table.core.usgovcloudapi.net</li></ul></li><li><b>Azure Container Registry (installation personnalisée)</b><ul><li>\*.azurecr.us</li></ul></li><li><b>Event Hub (journalisation)</b><ul><li>\*.servicebus.usgovcloudapi.net</li></ul></li><li><b>Service de journalisation Microsoft (utilisation interne)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure China 21Vianet     | <ul><li><b>Azure Data Factory (gestion)</b><ul><li>\*.frontend.datamovement.azure.cn</li></ul></li><li><b>Stockage Azure (gestion)</b><ul><li>\*.blob.core.chinacloudapi.cn</li><li>\*.table.core.chinacloudapi.cn</li></ul></li><li><b>Azure Container Registry (installation personnalisée)</b><ul><li>\*.azurecr.cn</li></ul></li><li><b>Event Hub (journalisation)</b><ul><li>\*.servicebus.chinacloudapi.cn</li></ul></li><li><b>Service de journalisation Microsoft (utilisation interne)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    Comme pour les noms de domaine complets de Stockage Azure, Azure Container Registry et Event Hub, vous pouvez également choisir d’activer les points de terminaison de service suivants pour votre réseau virtuel afin que le trafic réseau vers ces points de terminaison passe par le réseau principal Azure au lieu d’être routé vers votre appliance de pare-feu :
    -  Microsoft.Storage
    -  Microsoft.ContainerRegistry
    -  Microsoft.EventHub


-   Port 80 avec pour destination des sites de téléchargement de liste de révocation de certificats.

    Vous devez autoriser les noms de domaine complets ci-dessous qui sont utilisés comme sites de téléchargement de liste de révocation de certificats pour les certificats destinés à la gestion d’Azure-SSIS IR :
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Si vous utilisez des certificats ayant une liste de révocation de certificats différente, vous pouvez également les inclure. Vous pouvez en apprendre davantage sur la [liste de révocation de certificats](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx).

    Si vous interdisez ce trafic, vous risquez de constater une dégradation des performances lors du démarrage d’Azure-SSIS IR, et de perdre la possibilité de vérifier la liste de révocation de certificats pour l’utilisation du certificat, ce qui n’est pas recommandé du point de vue de la sécurité.

-   Port 1433, 11000-11999 avec pour destination Azure SQL Database (obligatoire uniquement lorsque les nœuds de votre Azure-SSIS IR dans le réseau virtuel accèdent à une SSISDB hébergée par votre serveur).

    Si vous utilisez Pare-feu Azure, vous pouvez spécifier une règle de réseau avec une étiquette de service Azure SQL ou autoriser comme destination une URL Azure SQL spécifique dans l’appliance de pare-feu.

-   Port 445 avec pour destination Stockage Azure (obligatoire uniquement lorsque vous exécutez un package SSIS stocké dans Azure Files).

    Si vous utilisez le Pare-feu Azure, vous pouvez spécifier une règle de réseau avec une balise de service de stockage, ou autoriser comme destination une URL de partage de fichier Azure spécifique dans l’appliance de pare-feu.

> [!NOTE]
> Pour Azure SQL et Stockage Azure, si vous configurez des points de terminaison de service de réseau virtuel sur votre sous-réseau, le trafic entre Azure-SSIS IR et Azure SQL dans la même région, ou Stockage Azure dans la même région ou une région appairée, est acheminé directement vers le réseau principal Microsoft Azure au lieu de votre appliance de pare-feu.

Si vous n’avez pas besoin de pouvoir inspecter le trafic sortant d’Azure-SSIS IR, vous pouvez simplement appliquer un itinéraire pour forcer tout le trafic vers le type de tronçon suivant **Internet** :

-   Dans un scénario Azure ExpressRoute, Vous pouvez appliquer un itinéraire 0.0.0.0/0 avec le type de tronçon suivant **Internet** sur le sous-réseau hébergeant l’Azure-SSIS IR. 
-   Dans un scénario d’appliance virtuelle réseau, vous pouvez modifier l’itinéraire 0.0.0.0/0 existant appliqué sur le sous-réseau hébergeant l’Azure-SSIS IR du type de tronçon suivant **Appliance virtuelle** à **Internet**.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png" alt-text="Ajouter un itinéraire":::

> [!NOTE]
> Spécifier un itinéraire avec le type de tronçon suivant **Internet** ne signifie pas que tout le trafic passe par Internet. Du moment que l’adresse de destination correspond à l’un des services d’Azure, Azure achemine le trafic directement vers le service via le réseau principal d’Azure, plutôt que vers Internet.

## <a name="set-up-the-resource-group"></a><a name="resource-group"></a> Configurer le groupe de ressources

Le runtime d’intégration Azure-SSIS doit créer certaines ressources réseau sous le même groupe de ressources que le réseau virtuel. Ces ressources incluent :
- Un équilibreur de charge Azure, avec le nom *\<Guid>-azurebatch-cloudserviceloadbalancer*.
- Une adresse IP publique Azure, avec le nom *\<Guid>-azurebatch-cloudservicepublicip*.
- Un groupe de sécurité de travail réseau, avec le nom *\<Guid>-azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> Vous pouvez maintenant apporter vos propres adresses IP publiques statiques pour Azure-SSIS IR. Dans ce scénario, nous allons créer uniquement l’équilibreur de charge Azure et le groupe de sécurité réseau sous le même groupe de ressources que vos adresses IP publiques statiques au lieu du réseau virtuel.

Ces ressources sont créées au démarrage de votre Azure-SSIS IR. Elles sont supprimées lorsqu’il est arrêté. Si vous apportez vos propres adresses IP publiques statiques pour Azure-SSIS IR, elles ne seront pas supprimées lors de l’arrêt de votre Azure-SSIS IR. Pour éviter de bloquer l’arrêt du runtime d’intégration Azure-SSIS IR, ne réutilisez pas ces ressources réseau dans vos autres ressources.

Vérifiez que vous n’avez pas de verrou sur le groupe de ressources ou l’abonnement auquel appartiennent le réseau virtuel ou vos adresses IP publiques statiques. Si vous configurez un verrou en lecture seule ou de suppression, le démarrage et l’arrêt du runtime d’intégration Azure-SSIS IR échoueront ou celui-ci ne répondra plus.

Vérifiez qu’aucune affectation Azure Policy n’empêche la création des ressources suivantes dans le groupe de ressources ou l’abonnement auquel appartiennent le réseau virtuel ou vos adresses IP publiques statiques : 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

Vérifiez que le quota de ressources de votre abonnement est suffisant pour les trois ressources réseau ci-dessus. Plus précisément, pour chaque instance Azure-SSIS IR créée sur le réseau virtuel, vous devez réserver deux quotas gratuits pour chacune des trois ressources réseau ci-dessus. Le quota supplémentaire est utilisé lors de la mise à niveau régulière de votre instance Azure-SSIS IR.

## <a name="faq"></a><a name="faq"></a> FAQ

- Comment protéger l’adresse IP publique exposée sur mon runtime d’intégration Azure-SSIS IR pour la connexion entrante ? Est-il possible de supprimer une adresse IP publique ?
 
  À l’heure actuelle, une adresse IP publique est créée automatiquement lorsque votre runtime d’intégration Azure-SSIS IR rejoint un réseau virtuel. Nous disposons d’un groupe de sécurité réseau au niveau de la carte réseau pour autoriser uniquement les services de gestion Azure Batch à établir une connexion entrante à votre Azure-SSIS IR. Vous pouvez également spécifier le groupe de sécurité réseau au niveau du sous-réseau pour la protection entrante.

  Si vous ne souhaitez pas que les adresses IP publiques soient exposées, vous pouvez envisager de [configurer un runtime d’intégration auto-hébergé en tant que proxy pour votre Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md) plutôt que de joindre celui-ci à un réseau virtuel si cela s’applique à votre scénario.
 
- Puis-je ajouter l’IP publique de mon runtime d’intégration Azure-SSIS à la liste d’autorisation du pare-feu pour mes sources de données ?

  Vous pouvez maintenant apporter vos propres adresses IP publiques statiques pour Azure-SSIS IR. Dans ce cas, vous pouvez ajouter vos adresses IP à la liste d’autorisation du pare-feu pour vos sources de données. Vous pouvez également envisager les options suivantes pour sécuriser l’accès aux données à partir de votre Azure-SSIS IR en fonction de votre scénario :

  - Si votre source de données est locale, une fois que vous avez connecté un réseau virtuel à votre réseau local et joint votre runtime d’intégration Azure-SSIS à ce sous-réseau de réseau virtuel, vous pouvez ajouter la plage d’adresses IP privées de ce sous-réseau à la liste d’autorisation du pare-feu pour votre source de données.
  - Si votre source de données est un service Azure qui prend en charge les points de terminaison de service de réseau virtuel, vous pouvez configurer un point de terminaison de service de réseau virtuel sur votre sous-réseau de réseau virtuel et joindre votre Azure-SSIS IR à ce sous-réseau. Vous pouvez ensuite ajouter une règle de réseau virtuel avec ce sous-réseau au pare-feu pour votre source de données.
  - Si votre source de données est un service cloud autre qu’Azure, vous pouvez utiliser une UDR pour acheminer le trafic sortant de votre Azure-SSIS IR vers une appliance virtuelle réseau/un Pare-feu Azure via une adresse IP publique statique. Vous pouvez ensuite ajouter l’IP publique statique de votre appliance virtuelle réseau/Pare-feu Azure à la liste d’autorisation du pare-feu pour votre source de données.
  - Si aucune de ces options ne correspond à vos besoins, vous pouvez envisager de [configurer un IR auto-hébergé en tant que proxy pour votre Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md). Vous pouvez ensuite ajouter l’IP publique statique de l’ordinateur qui héberge votre IR auto-hébergé à la liste d’autorisation du pare-feu pour votre source de données.

- Pourquoi dois-je fournir deux adresses publiques statiques si je veux apporter mes propres adresses IP publiques pour Azure-SSIS IR ?

  Azure-SSIS IR est automatiquement mis à jour de façon régulière. De nouveaux nœuds sont créés lors de la mise à niveau et les anciens nœuds sont supprimés. Toutefois, pour éviter un temps d’arrêt, les anciens nœuds ne sont pas supprimés tant que les nouveaux ne sont pas prêts. Ainsi, votre première adresse IP publique statique utilisée par les anciens nœuds ne peut pas être libérée immédiatement et nous avons donc besoin de votre deuxième adresse IP publique statique pour créer les nouveaux nœuds.

- J’ai apporté mes propres adresses IP publiques statiques pour Azure-SSIS IR. Pourquoi le runtime d’intégration ne peut-il toujours pas accéder à mes sources de données ?

  - Vérifiez que les deux IP publiques statiques sont ajoutées à la liste d’autorisation du pare-feu pour vos sources de données. À chaque mise à niveau de votre Azure-SSIS IR, son adresse IP publique statique alterne entre les deux adresses que vous avez fournies. Si vous n’en ajoutez qu’une seule à la liste d’autorisation, l’accès aux données pour votre runtime d’intégration Azure-SSIS sera interrompu après sa mise à niveau.
  - Si votre source de données est un service Azure, vérifiez que vous l’avez configurée avec des points de terminaison de service de réseau virtuel. Le cas échéant, le trafic d’Azure-SSIS IR vers votre source de données permutera pour utiliser les adresses IP privées gérées par les services Azure, et l’ajout de vos propres IP publiques statiques à la liste d’autorisation du pare-feu pour votre source de données ne prendra pas effet.

## <a name="next-steps"></a>Étapes suivantes
- [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel – Présentation](join-azure-ssis-integration-runtime-virtual-network.md)
- [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel avec l’interface utilisateur d’Azure Data Factory studio](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel à l’aide d’Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Pour plus d’informations sur le runtime d’intégration Azure-SSIS IR, voir les articles suivants : 
- [Runtime d’intégration Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Cet article fournit des informations conceptuelles générales sur les runtimes d’intégration, notamment sur Azure-SSIS IR. 
- [Tutoriel : Déployer des packages SSIS vers Azure](./tutorial-deploy-ssis-packages-azure.md). Ce didacticiel fournit des instructions pas à pas pour créer votre Azure-SSIS IR. Il utilise Azure SQL Database pour héberger le catalogue SSIS. 
- [Créez un runtime d’intégration Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Cet article s’appuie sur le didacticiel. Il fournit des instructions sur l’utilisation d’Azure SQL Database avec des points de terminaison de service de réseau virtuel ou SQL Managed Instance dans un réseau virtuel pour héberger le catalogue SSIS. Il explique comment joindre votre runtime d’intégration Azure-SSIS IR à un réseau virtuel. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article vous montre comment obtenir des informations sur votre runtime d’intégration Azure-SSIS IR. Il fournit des descriptions sur l’état des informations renvoyées. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer votre Azure-SSIS IR. Il vous montre également comment effectuer un scale-out du runtime d’intégration Azure-SSIS en lui ajoutant des nœuds.