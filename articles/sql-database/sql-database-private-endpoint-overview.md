---
title: Private Link
description: Vue d’ensemble de la fonctionnalité Point de terminaison privé
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.service: sql-database
ms.topic: overview
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: dd717d653e57fbb8c540e4ef023011c64778a3b0
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628995"
---
# <a name="private-link-for-azure-sql-database-and-azure-synapse-analytics"></a>Liaison privée pour Azure SQL Database et Azure Synapse Analytics

Liaison privée vous permet de vous connecter à différents services PaaS dans Azure par le biais d’un **point de terminaison privé**. Pour obtenir la liste des services PaaS prenant en charge la fonctionnalité Liaison privée, accédez à la page [Documentation sur Liaison privée](../private-link/index.yml). Un point de terminaison privé est une adresse IP privée au sein d’un [réseau virtuel](../virtual-network/virtual-networks-overview.md) et d’un sous-réseau spécifiques. 

> [!IMPORTANT]
> Cet article s’applique au serveur SQL Azure ainsi qu’aux bases de données SQL Database et Azure Synapse Analytics créées sur le serveur SQL Azure. Par souci de simplicité, le nom « SQL Database » est utilisé pour faire référence à SQL Database et à Azure Synapse Analytics. Cet article *ne s’applique pas* au déploiement d’une **instance managée** dans Azure SQL Database.

## <a name="data-exfiltration-prevention"></a>Prévention de l’exfiltration de données

L’exfiltration de données dans Azure SQL Database se produit quand un utilisateur autorisé, tel qu’un administrateur de base de données, extrait des données d’un système et les déplace vers un autre emplacement ou système en dehors de l’organisation. C’est par exemple le cas quand un utilisateur déplace des données vers un compte de stockage détenu par un tiers.

Imaginez un scénario dans lequel un utilisateur exécute SSMS (SQL Server Management Studio) à l’intérieur d’une machine virtuelle Azure se connectant à une base de données SQL. Cette base de données SQL se trouve dans le centre de données USA Ouest. L’exemple ci-dessous montre comment utiliser des contrôles d’accès réseau pour limiter l’accès à la base de données SQL par le biais de points de terminaison publics.

1. Désactivez tout le trafic des services Azure à destination de la base de données SQL par le biais du point de terminaison public en affectant **OFF** à l’option Autoriser les services Azure. Vérifiez qu’aucune adresse IP n’est autorisée dans les règles de pare-feu au niveau du serveur et de la base de données. Pour plus d’informations, consultez [Contrôles d’accès réseau Azure SQL Database et Azure Synapse Analytics](sql-database-networkaccess-overview.md).
1. Autorisez uniquement le trafic à destination de la base de données SQL utilisant l’adresse IP privée de la machine virtuelle. Pour plus d’informations, consultez les articles sur le [point de terminaison de service](sql-database-vnet-service-endpoint-rule-overview.md) et les [règle de pare-feu du réseau virtuel](sql-database-firewall-configure.md).
1. Sur la machine virtuelle Azure, limitez l’étendue de la connexion sortante à l’aide de [groupes de sécurité réseau (NSG)](../virtual-network/manage-network-security-group.md) et d’étiquettes de service comme suit.
    - Spécifiez une règle NSG pour autoriser le trafic pour Service Tag = SQL.WestUs (autorise uniquement la connexion à la base de données SQL dans USA Ouest)
    - Spécifiez une règle NSG avec une **priorité plus élevée** pour refuser le trafic pour Service Tag = SQL (refuse les connexions à la base de données SQL dans toutes les régions)

À la fin de cette configuration, la machine virtuelle Azure peut uniquement se connecter aux bases de données SQL situées dans la région USA Ouest. Toutefois, la connectivité n’est pas limitée à une seule base de données SQL. La machine virtuelle peut toujours se connecter à n’importe quelle base de données SQL de la région USA Ouest, même si elle ne fait pas partie de l’abonnement. Bien que nous ayons réduit l’étendue de l’exfiltration de données dans le scénario ci-dessus à une région spécifique, nous ne l’avons pas encore totalement éliminée.

Grâce à Liaison privée, les clients peuvent désormais configurer des contrôles d’accès réseau comme des groupes de sécurité réseau pour restreindre l’accès au point de terminaison privé. Les ressources Azure PaaS individuelles sont ensuite mappées à des points de terminaison privés spécifiques. Un utilisateur interne malveillant peut uniquement accéder à la ressource PaaS mappée (par exemple, une base de données SQL). Il n’a accès à aucune autre ressource. 

## <a name="on-premises-connectivity-over-private-peering"></a>Connectivité locale sur un appairage privé

Quand des clients se connectent au point de terminaison public à partir de machines locales, leur adresse IP doit être ajoutée au pare-feu IP à l’aide d’une [règle de pare-feu au niveau du serveur](sql-database-server-level-firewall-rule.md). Bien que ce modèle fonctionne bien pour autoriser l’accès à des machines individuelles pour des charges de travail de développement ou de test, il est difficile à gérer dans un environnement de production.

Grâce à Liaison privée, les clients peuvent activer l’accès entre différents locaux au point de terminaison privé en utilisant [ ExpressRoute](../expressroute/expressroute-introduction.md), un appairage privé ou un tunneling VPN. Les clients peuvent ensuite désactiver tout accès par le biais du point de terminaison public et s’abstenir d’utiliser le pare-feu IP pour autoriser les adresses IP.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Comment configurer Liaison privée pour Azure SQL Database 

### <a name="creation-process"></a>Processus de création
Vous pouvez créer des points de terminaison privés avec le portail, PowerShell ou Azure CLI :
- [Portail](../private-link/create-private-endpoint-portal.md)
- [PowerShell](../private-link/create-private-endpoint-powershell.md)
- [INTERFACE DE LIGNE DE COMMANDE](../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Processus d’approbation
Une fois que l’administrateur réseau a créé le point de terminaison privé (PE), l’administrateur SQL peut gérer la connexion de point de terminaison privé (PEC) à la base de données.

1. Accédez à la ressource SQL Server dans le portail Azure en suivant les étapes figurant dans la capture d’écran ci-dessous.

    - (1) Sélectionner les connexions de point de terminaison privé dans le volet gauche
    - (2) Liste de toutes les connexions de point de terminaison privé (PEC)
    - (3) Point de terminaison privé (PE) correspondant créé ![Capture d’écran de tous les PEC][3]

1. Sélectionnez un PEC dans la liste.
![Capture d’écran du PEC sélectionné][6]

1. L’administrateur SQL peut choisir d’approuver ou de rejeter un PEC. Il peut aussi ajouter une brève réponse sous forme de texte.
![Capture d’écran de l’approbation du PEC][4]

1. Après approbation ou rejet, la liste reflète l’état approprié et le texte de réponse.
![Capture d’écran de tous les PEC après approbation][5]

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Cas d’usage de Liaison privée pour Azure SQL Database 

Les clients peuvent se connecter au point de terminaison privé à partir du même réseau virtuel, d’un réseau virtuel appairé dans la même région ou par le biais d’une connexion réseau virtuel à réseau virtuel entre régions. Les clients peuvent également se connecter localement avec ExpressRoute, un appairage privé ou un tunneling VPN. Vous trouverez ci-dessous un diagramme simplifié montrant les cas d’usage courants.

 ![Diagramme des options de connectivité][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network-vnet"></a>Tester la connectivité à SQL Database à partir d’une machine virtuelle Azure dans le même réseau virtuel

Ce scénario part du principe que vous avez créé une machine virtuelle Azure exécutant Windows Server 2016. 

1. [Démarrez une session Bureau à distance (RDP) et connectez-vous à la machine virtuelle](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. Vous pouvez ensuite procéder à des vérifications de base de la connectivité pour confirmer que la machine virtuelle se connecte à SQL Database par le biais du point de terminaison privé à l’aide des outils suivants :
    1. Telnet
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Vérifier la connectivité à l’aide de Telnet

[Telnet Client](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) est une fonctionnalité Windows que vous pouvez utiliser pour tester la connectivité. Selon la version de votre système d’exploitation Windows, vous devrez peut-être activer cette fonctionnalité de manière explicite. 

Après avoir installé Telnet, ouvrez une fenêtre d’invite de commandes. Exécutez la commande telnet et spécifiez l’adresse IP et le point de terminaison privé de la base de données SQL.

```
>telnet 10.1.1.5 1433
```

Quand Telnet parvient à se connecter, un écran vide semblable à celui de l’image ci-dessous apparaît dans la fenêtre de commandes :

 ![Diagramme de telnet][2]

### <a name="check-connectivity-using-psping"></a>Vérifier la connectivité à l’aide de Telnet

[Psping](/sysinternals/downloads/psping) peut être utilisé comme suit pour vérifier que la connexion de point de terminaison privé (PEC) écoute les connexions sur le port 1433.

Exécutez psping comme suit en fournissant le nom de domaine complet de votre serveur SQL Database et le port 1433 :

```
>psping.exe mysqldbsrvr.database.windows.net:1433

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.6.1.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.6.1.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49958: 2.28ms
```

La sortie indique que Psping a réussi à effectuer un test ping sur l’adresse IP privée associée au PEC.

### <a name="check-connectivity-using-nmap"></a>Vérifier la connectivité à l’aide de Nmap

Nmap (Network Mapper) est un outil gratuit et open source que vous povez utiliser pour découvrir des réseaux et auditer la sécurité. Pour obtenir plus d’informations et le lien de téléchargement, consultez https://nmap.org. Vous pouvez utiliser cet outil pour vérifier que le point de terminaison privé écoute les connexions sur le port 1433.

Exécutez Nmap comme suit en fournissant la plage d’adresses du sous-réseau qui héberge le point de terminaison privé.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

Le résultat indique qu’une adresse IP est active : il s’agit de l’adresse IP du point de terminaison privé.

### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>Vérifier la connectivité à l’aide de SSMS (SQL Server Management Studio)
> [!NOTE]
> Utilisez le **nom de domaine complet (FQDN)** du serveur dans les chaînes de connexion de vos clients. Toute tentative de connexion directe à l’adresse IP échoue. Ce comportement est normal dans la mesure où le point de terminaison privé route le trafic vers la passerelle SQL dans la région et où le nom de domaine complet doit être spécifié pour que les connexions réussissent.

Suivez les étapes décrites ici afin d’utiliser [SSMS pour vous connecter à la base de données SQL](sql-database-connect-query-ssms.md). Une fois que vous êtes connecté à la base de données SQL à l’aide de SSMS, exécutez la requête suivante pour vérifier que la connexion est établie à partir de l’adresse IP privée de la machine virtuelle Azure :

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="limitations"></a>Limites 
Les connexions au point de terminaison privé prennent uniquement en charge **Proxy** comme [stratégie de connexion](sql-database-connectivity-architecture.md#connection-policy).


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Connexion à partir d’une machine virtuelle Azure dans un réseau virtuel appairé 

Configurez l’[appairage de réseau virtuel](../virtual-network/tutorial-connect-virtual-networks-powershell.md) pour établir la connectivité à la base de données SQL à partir d’une machine virtuelle Azure dans un réseau virtuel appairé.

## <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Connexion à partir d’une machine virtuelle Azure dans un environnement de réseau virtuel à réseau virtuel

Configurez une [connexion de passerelle VPN de réseau virtuel à réseau virtuel](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) pour établir la connectivité à une base de données SQL à partir d’une machine virtuelle Azure dans une autre région ou un autre abonnement.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Connexion à partir d’un environnement local sur un VPN

Pour établir la connectivité entre un environnement local et la base de données SQL, choisissez et implémentez l’une des options suivantes :
- [Connexion point à site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Connexion VPN de site à site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [Circuit ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-azure-synapse-analytics-to-azure-storage-using-polybase"></a>Connexion d’Azure Synapse Analytics à Stockage Azure à l’aide de Polybase

La technologie PolyBase est couramment utilisée pour charger des données dans Azure Synapse Analytics à partir de comptes Stockage Azure. Si le compte Stockage Azure à partir duquel vous chargez des données limite l’accès à un ensemble de sous-réseaux de réseau virtuel par le biais de points de terminaison privés, de points de terminaison de service ou de pare-feu IP, la connectivité entre PolyBase et le compte est interrompue. Pour autoriser les scénarios d’importation et d’exportation PolyBase dans lesquels Azure Synapse Analytics se connecte de manière sécurisée à Stockage Azure au réseau virtuel, suivez les étapes indiquées [ici](sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). 

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble de la sécurité Azure SQL Database, consultez [Sécurisation de votre base de données](sql-database-security-overview.md)
- Pour obtenir une vue d’ensemble de la connectivité Azure SQL Database, consultez [Architecture de connectivité Azure SQL](sql-database-connectivity-architecture.md).

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/pe-connect-overview.png
[2]: ./media/sql-database-get-started-portal/telnet-result.png
[3]: ./media/sql-database-get-started-portal/pec-list-before.png
[4]: ./media/sql-database-get-started-portal/pec-approve.png
[5]: ./media/sql-database-get-started-portal/pec-list-after.png
[6]: ./media/sql-database-get-started-portal/pec-select.png
