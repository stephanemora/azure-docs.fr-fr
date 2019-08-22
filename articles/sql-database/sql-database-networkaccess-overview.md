---
title: Contrôles d’accès réseau Azure SQL Database et Data Warehouse | Microsoft Docs
description: Vue d’ensemble des contrôles d’accès réseau pour Azure SQL Database et Data Warehouse pour gérer l’accès et configurer une base de données unique ou mise en pool.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 4722cda0506493671ff872d75e7376f3de74b1b8
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886464"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Contrôles d’accès réseau Azure SQL Database et Data Warehouse

> [!NOTE]
> Cet article s’applique à un serveur SQL Azure et aux bases de données SQL Database et SQL Data Warehouse créées sur le serveur SQL Azure. Par souci de simplicité, SQL Database est utilisé pour faire référence à la fois à SQL Database et à SQL Data Warehouse.

> [!IMPORTANT]
> Cet article ne s’applique *pas* à **Azure SQL Database Managed Instance**. Pour plus d’informations sur la configuration réseau, consultez [Connexion à une instance managée](sql-database-managed-instance-connect-app.md).

Lorsque vous créez un serveur SQL Server Azure à partir du [portail Azure](sql-database-single-database-get-started.md), le résultat est un point de terminaison public au format *votrenomdeserveur.basededonnées.windows.net*. Par défaut, tout accès au point de terminaison public est refusé. Vous pouvez alors utiliser les contrôles d’accès réseau suivants pour autoriser de manière sélective l’accès à SQL Database via le point de terminaison public.
- Autoriser les services Azure : Si défini sur ACTIVÉ, d’autres ressources dans la limite Azure, par exemple une machine virtuelle Azure, peuvent accéder à SQL Database

- Règles de pare-feu IP : Utilisez cette fonctionnalité pour autoriser explicitement les connexions à partir d’une adresse IP spécifique, par exemple à partir d’ordinateurs locaux.

- Règles de pare-feu de réseau virtuel : Utilisez cette fonctionnalité pour autoriser le trafic à partir d’un réseau virtuel spécifique au sein de la limite Azure.


## <a name="allow-azure-services"></a>Autoriser les services Azure 
Lors de la création d’un serveur Azure SQL Server à partir du [portail Azure](sql-database-single-database-get-started.md), ce paramètre est laissé désactivé.

 ![Capture d’écran de la création d’un serveur][1]

Vous pouvez aussi changer ce paramètre via le volet du pare-feu une fois le serveur Azure SQL Server créé, comme suit.
  
 ![Capture d’écran de la gestion du pare-feu du serveur][2]

Quand la valeur est définie sur **ACTIVÉ**, Azure SQL Server autorise les communications à partir de toutes les ressources situées dans la limite Azure, qu’elles fassent partie de votre abonnement ou non.

Dans de nombreux cas, le paramètre **ACTIVÉ** est plus permissif que ce que souhaite la plupart des clients, donc ceux-ci voudront peut-être définir ce paramètre sur **DÉSACTIVÉ** et le remplacer par des règles de pare-feu IP ou des règles de pare-feu de réseau virtuel plus restrictives. Procéder ainsi affecte les fonctionnalités suivantes :

### <a name="import-export-service"></a>Service d’importation/exportation

Le service d’importation/exportation Azure SQL Database s’exécute sur des machines virtuelles dans Azure. Ces machines virtuelles, qui ne se trouvent pas dans votre réseau virtuel, obtiennent une adresse IP Azure lors de la connexion à votre base de données. Si vous supprimez la règle **Autoriser l’accès des services Azure au serveur**, ces machines virtuelles ne seront plus en mesure d’accéder à vos bases de données.
Vous pouvez contourner le problème en exécutant le service d’importation/exportation BACPAC directement dans votre code à l’aide de l’API DACFx.

### <a name="sql-database-query-editor"></a>Éditeur de requêtes SQL Database

L’éditeur de requêtes Azure SQL Database est déployé sur des machines virtuelles dans Azure. Ces machines virtuelles ne se trouvent pas dans votre réseau virtuel. Elles obtiennent donc une adresse IP Azure lors de la connexion à votre base de données. Si vous supprimez la règle **Autoriser l’accès des services Azure au serveur**, ces machines virtuelles ne seront plus en mesure d’accéder à vos bases de données.

### <a name="table-auditing"></a>Audit de table

Il existe actuellement deux façons d’activer l’audit sur votre instance SQL Database. L’audit de table échoue une fois que vous avez activé des points de terminaison de service sur votre instance Azure SQL Server. Pour contourner le problème, vous pouvez utiliser un audit d’objets blob.

### <a name="impact-on-data-sync"></a>Impact sur la synchronisation de données

Azure SQL Database dispose de la fonctionnalité de synchronisation de données qui se connecte à vos bases de données à l’aide d’adresses IP Azure. Lorsque vous utilisez des points de terminaison de service, vous désactivez l’accès **Autoriser l’accès des services Azure au serveur** à votre serveur SQL Database et casser la fonctionnalité Synchronisation des données.

## <a name="ip-firewall-rules"></a>Règles de pare-feu IP
Le pare-feu basé sur IP est une fonctionnalité Azure SQL Server qui empêche tout accès à votre serveur de base de données tant que vous n’avez pas [ajouté les adresses IP](sql-database-server-level-firewall-rule.md) des ordinateurs clients de manière explicite.


## <a name="virtual-network-firewall-rules"></a>Règles de pare-feu de réseau virtuel

En plus des règles IP, le pare-feu Azure SQL Server vous permet de définir des *règles de réseau virtuel*.  
Pour plus d’informations, voir [Points de terminaison de service de réseau virtuel et règles dans Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

 ### <a name="azure-networking-terminology"></a>Terminologie des réseaux Azure  
Apprenez à connaître les termes de réseau Azure suivants quand vous explorez les règles de pare-feu de réseau virtuel

**Réseau virtuel :** Vous pouvez avoir des réseaux virtuels associés à votre abonnement Azure. 

**Sous-réseau :** Un réseau virtuel contient des **sous-réseaux**. Toutes les machines virtuelles Azure que vous avez sont assignées à des sous-réseaux. Un sous-réseau peut contenir plusieurs machines virtuelles ou d’autres nœuds de calcul. Les nœuds de calcul qui se trouvent en dehors de votre réseau virtuel ne peuvent pas accéder à ce dernier, sauf si vous configurez votre sécurité pour leur en donner l’accès.

**Point de terminaison de service de réseau virtuel :** Un [Point de terminaison de service de réseau virtuel][vm-virtual-network-service-endpoints-overview-649d] est un sous-réseau dont les valeurs de propriétés incluent un ou plusieurs noms de type de service Azure officiels. Dans cet article, nous nous intéressons au nom de type de **Microsoft.Sql**, qui fait référence au service Azure nommé SQL Database.

**Règle de réseau virtuel :** une règle de réseau virtuel pour votre serveur SQL Database est un sous-réseau qui figure dans la liste de contrôle d’accès (ACL) de votre serveur SQL Database. Pour figurer dans l’ACL pour le serveur SQL Database, le sous-réseau doit contenir le nom de type **Microsoft.Sql**. Une règle de réseau virtuel donne l’instruction au serveur SQL Database d’accepter les communications provenant de tout nœud se trouvant sur le sous-réseau.


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP ou règles de pare-feu de réseau virtuel

Le pare-feu Azure SQL Server vous permet de spécifier des plages d’adresses IP à partir desquelles les communications sont acceptées sur SQL Database. Cette approche est indiquée pour les adresses IP stables qui se trouvent en dehors du réseau privé Azure. Toutefois, les machines virtuelles à l’intérieur du réseau privé Azure sont configurées avec des adresses IP *dynamiques*. Les adresses IP dynamiques peuvent changer lorsque votre machine virtuelle est redémarrée et ainsi invalider la règle de pare-feu basée sur IP. Spécifier une adresse IP dynamique dans une règle de pare-feu au sein d’un environnement de production serait inimaginable.

Vous pouvez contourner cette limitation en obtenant une adresse IP *statique* pour votre machine virtuelle. Pour plus d’informations, consultez [Configurer des adresses IP privées pour une machine virtuelle en utilisant le portail Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]. Toutefois, l’approche de l’adresse IP statique peut devenir non seulement difficile à gérer, mais aussi coûteuse lorsqu’elle est appliquée à grande échelle. 

Les règles de réseau virtuel constituent une alternative plus facile pour établir et gérer l’accès à partir d’un sous-réseau spécifique qui contient vos machines virtuelles.

> [!NOTE]
> Vous ne pouvez pas encore avoir SQL Database dans un sous-réseau. Si votre serveur Azure SQL Database était un nœud sur un sous-réseau de votre réseau virtuel, tous les nœuds situés dans le réseau virtuel pourraient communiquer avec le serveur SQL Database. Dans ce cas, vos machines virtuelles pourraient communiquer avec le serveur SQL Database sans avoir à utiliser de règles de réseau virtuel ni de règles IP.

## <a name="next-steps"></a>Étapes suivantes

- Pour un guide de démarrage rapide sur la création d’une règle de pare-feu IP au niveau du serveur, consultez [Créer une base de données Azure SQL](sql-database-single-database-get-started.md).

- Pour un guide de démarrage rapide sur la création d’une règle de pare-feu de réseau virtuel au niveau du serveur, consultez [Points de terminaison et règles de service de réseau virtuel pour Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

- Pour de l’aide sur la connexion à une base de données Azure SQL à partir d’applications open source ou tierces, consultez [Exemples de code de démarrage rapide client pour SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- Pour plus d’informations sur les autres ports que vous devrez peut-être ouvrir, consultez la section **SQL Database : exécution externe ou exécution interne** de [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

- Pour une vue d’ensemble de la connectivité Azure SQL Database, consultez [Architecture de connectivité Azure SQL](sql-database-connectivity-architecture.md).

- Pour obtenir une vue d’ensemble de la sécurité Azure SQL Database, consultez [Sécurisation de votre base de données](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png
