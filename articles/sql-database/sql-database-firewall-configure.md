---
title: Règles de pare-feu IP Azure SQL Database et Data Warehouse | Microsoft Docs
description: Apprenez à configurer un pare-feu Azure SQL Database ou SQL Data Warehouse avec des règles de pare-feu IP au niveau du serveur pour gérer les accès, et à configurer une base de données unique ou mise en pool avec des règles de pare-feu IP au niveau de la base de données.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 513836257a292069da709ad7a71e480f2b4d069d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66158280"
---
# <a name="azure-sql-database-and-sql-data-warehouse-ip-firewall-rules"></a>Règles de pare-feu IP Azure SQL Database et SQL Data Warehouse

Microsoft Azure [SQL Database](sql-database-technical-overview.md) et [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) fournissent un service de base de données relationnelle pour Azure et d’autres applications Internet. Pour aider à protéger vos données, le pare-feu empêche tout accès à votre serveur de base de données jusqu’à ce que vous spécifiiez les ordinateurs qui disposent d’autorisations. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.

> [!NOTE]
> Cet article s’applique à un serveur SQL Azure et aux bases de données SQL Database et SQL Data Warehouse créées sur le serveur SQL Azure. Par souci de simplicité, la base de données SQL est utilisée pour faire référence à SQL Database et SQL Data Warehouse.
> [!IMPORTANT]
> Cet article ne s’applique *pas* à **Azure SQL Database Managed Instance**. Consultez l’article suivant sur la [connexion à une instance Managed Instance](sql-database-managed-instance-connect-app.md) pour en savoir plus sur la configuration réseau nécessaire.

## <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Règles de réseau virtuel comme alternatives aux règles d’adresses IP

Outre les règles d’adresses IP, le pare-feu gère également *les règles de réseau virtuel*. Les règles de réseau virtuel dépendent des points de terminaison de service de réseau virtuel. Les règles de réseau virtuel peuvent être préférables aux règles d’adresses IP dans certains cas. Pour plus d’informations, voir [Points de terminaison de service de réseau virtuel et règles dans Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Présentation

À la base, tout accès à votre serveur SQL Azure est bloqué par le pare-feu SQL Database. Pour accéder à votre serveur de base de données, vous devez spécifier une ou plusieurs règles de pare-feu IP au niveau du serveur qui permettent l’accès à votre serveur Azure SQL. Utilisez les règles de pare-feu IP pour spécifier les plages d’adresses IP Internet qui sont autorisées, et si les applications Azure peuvent essayer de se connecter à votre serveur Azure SQL.

Pour accorder l’accès de manière sélective à l’une des bases de données de votre serveur SQL Azure, vous devez créer une règle de niveau de la base de données pour la base de données requise. Spécifiez, pour la règle de pare-feu IP au niveau de la base de données, une plage d’adresses IP qui se situe au-delà de la plage d’adresses IP spécifiée dans la règle de pare-feu IP au niveau du serveur, et vérifiez que l’adresse IP du client appartient à la plage spécifiée dans la règle au niveau de la base de données.

> [!IMPORTANT]
> SQL Data Warehouse prend en charge uniquement les règles de pare-feu IP au niveau du serveur et ne prend pas en charge les règles de pare-feu IP au niveau de la base de données.

Les tentatives de connexion à partir d’Internet et d’Azure doivent franchir le pare-feu avant de pouvoir atteindre votre serveur Azure SQL ou votre base de données SQL, comme illustré dans le diagramme suivant :

   ![Diagramme décrivant la configuration de pare-feu.][1]

- **Règles de pare-feu IP au niveau du serveur :**

  Ces règles permettent aux clients d’accéder à la totalité de votre serveur Azure SQL, c’est-à-dire à toutes les bases de données du même serveur SQL Database. Ces règles sont stockées dans la base de données **principale** . Les règles de pare-feu IP au niveau du serveur peuvent être configurées en utilisant le portail ou des instructions Transact-SQL. Pour créer des règles de pare-feu IP au niveau du serveur à l’aide du portail Azure ou de PowerShell, vous devez être le propriétaire de l’abonnement ou un de ses contributeurs. Pour créer une règle de pare-feu IP au niveau du serveur à l’aide de Transact-SQL, vous devez vous connecter à l’instance SQL Database en utilisant la connexion du principal au niveau du serveur ou comme administrateur Azure Active Directory (cela signifie qu’un utilisateur avec des autorisations Azure doit d’abord créer la règle de pare-feu IP au niveau du serveur).

- **Règles de pare-feu IP au niveau de la base de données :**

  Ces règles permettent aux clients d’accéder à certaines bases de données (sécurisées) au sein du même serveur SQL Database. Vous pouvez créer ces règles pour chaque base de données (dont la base de données **MASTER**) et elles sont stockées dans les bases de données individuelles. Les règles de pare-feu IP pour les bases de données master et utilisateur peuvent uniquement être créées et gérées à l’aide d’instructions Transact-SQL et seulement après avoir configuré le premier pare-feu au niveau du serveur. Si vous spécifiez dans la règle de pare-feu IP au niveau de la base de données une plage d’adresses IP en dehors de la plage spécifiée dans la règle de pare-feu IP au niveau du serveur, seuls les clients dont les adresses IP appartiennent à la plage au niveau de la base de données peuvent accéder à la base de données. Vous pouvez avoir un maximum de 128 règles de pare-feu IP au niveau de la base de données par base de données. Pour plus d’informations sur la configuration des règles de pare-feu IP au niveau de la base de données, consultez l’exemple plus loin dans cet article et [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendation"></a>Recommandation

Microsoft recommande d’utiliser quand c’est possible des règles de pare-feu IP au niveau de la base de données pour renforcer la sécurité et améliorer la portabilité de la base de données. Utilisez des règles de pare-feu IP au niveau du serveur pour les administrateurs quand plusieurs bases de données ont les mêmes exigences d’accès et que vous ne voulez pas les configurer une par une.

> [!IMPORTANT]
> Microsoft Azure SQL Database prend en charge un maximum de 128 règles de pare-feu IP.
> [!NOTE]
> Pour plus d’informations sur les bases de données portables dans le cadre de la continuité d’activité, consultez [Exigences d’authentification pour la récupération d’urgence](sql-database-geo-replication-security-config.md).

### <a name="connecting-from-the-internet"></a>Connexion à partir d’Internet

Quand un ordinateur tente de se connecter à votre serveur de base de données à partir d’Internet, le pare-feu vérifie d’abord l’adresse IP d’origine de la demande par rapport aux règles de pare-feu IP au niveau de la base de données, pour la base de données demandée par la connexion :

- Si l’adresse IP de la demande appartient à une des plages spécifiées dans les règles de pare-feu IP au niveau de la base de données, la connexion est accordée à la base de données SQL contenant la règle.
- Si l’adresse IP de la demande n’appartient pas à une des plages spécifiées dans la règle de pare-feu IP au niveau de la base de données, les règles de pare-feu IP au niveau du serveur sont vérifiées. Si l’adresse IP de la demande appartient à une des plages spécifiées dans les règles de pare-feu IP au niveau du serveur, la connexion est accordée. Les règles de pare-feu IP au niveau du serveur s’appliquent à toutes les bases de données SQL sur le serveur Azure SQL.  
- Si l’adresse IP de la demande n’appartient pas aux plages spécifiées dans les règles de pare-feu IP au niveau du serveur ou de la base de données, la demande de connexion échoue.

> [!NOTE]
> Pour accéder à Azure SQL Database à partir de votre ordinateur local, vérifiez que le pare-feu sur votre réseau et l’ordinateur local autorise les communications sortantes sur le port TCP 1433.

### <a name="connecting-from-azure"></a>Connexion à partir d’Azure

Pour autoriser des applications d’Azure à se connecter à Azure SQL Server, les connexions Azure doivent être activées. Quand une application à partir d’Azure tente de se connecter à votre serveur de base de données, le pare-feu vérifie que les connexions Azure sont autorisées. Un paramètre de pare-feu avec des adresses de début et de fin égales à 0.0.0.0 indique que les connexions Azure sont autorisées. Si la tentative de connexion n’est pas autorisée, la demande n’atteint pas le serveur Azure SQL Database.

> [!IMPORTANT]
> Cette option configure le pare-feu pour autoriser toutes les connexions à partir d’Azure, notamment les connexions issues des abonnements d’autres clients. Lorsque vous sélectionnez cette option, vérifiez que votre connexion et vos autorisations utilisateur limitent l’accès aux seuls utilisateurs autorisés.

## <a name="creating-and-managing-ip-firewall-rules"></a>Création et gestion des règles de pare-feu IP

Le premier paramètre de pare-feu au niveau du serveur peut être créé à l’aide du [portail Azure](https://portal.azure.com/) ou par programmation avec [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql), [l’interface de ligne de commande Azure](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) ou [l’API REST](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate). Les règles de pare-feu IP au niveau du serveur suivantes peuvent être créées et gérées à l’aide de ces méthodes, et avec Transact-SQL.

> [!IMPORTANT]
> Les règles de pare-feu IP au niveau de la base de données peuvent être créées et gérées seulement avec Transact-SQL.

Pour améliorer les performances, les règles de pare-feu IP au niveau du serveur sont temporairement mises en cache au niveau de la base de données. Pour actualiser le cache, consultez [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> Vous pouvez utiliser l’[Audit Azure SQL Database](sql-database-auditing.md) pour vérifier des modifications de pare-feu au niveau serveur et au niveau base de données.

## <a name="manage-server-level-ip-firewall-rules-using-the-azure-portal"></a>Gérer les règles de pare-feu IP au niveau du serveur avec le portail Azure

Pour définir une règle de pare-feu IP au niveau du serveur dans le portail Azure, vous pouvez accéder à la page Vue d’ensemble de votre base de données Azure SQL ou de votre serveur SQL Database.

> [!TIP]
> Pour obtenir un didacticiel, consultez [Créer une base de données à l’aide du portail Azure](sql-database-single-database-get-started.md).

### <a name="from-database-overview-page"></a>À partir de la page de vue d’ensemble de la base de données

1. Pour définir une règle de pare-feu IP au niveau du serveur à partir de la page Vue d’ensemble de la base de données, cliquez sur **Définir le pare-feu du serveur** dans la barre d’outils, comme illustré dans l’image suivante : La page **Paramètres de pare-feu** du serveur de base de données SQL s’ouvre.

      ![règle de pare-feu IP de serveur](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Cliquez sur **Ajouter une adresse IP cliente** dans la barre d’outils pour ajouter l’adresse IP de l’ordinateur que vous utilisez, puis cliquez sur **Enregistrer**. Une règle de pare-feu IP au niveau du serveur est créée pour votre adresse IP actuelle.

      ![règle de pare-feu IP au niveau du serveur définie](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

### <a name="from-server-overview-page"></a>À partir de la page de vue d’ensemble du serveur

La page de présentation de votre serveur s’ouvre, affiche le nom de serveur complet (tel que **mynewserver20170403.database.windows.net**) et fournit des options pour poursuivre la configuration.

1. Pour définir une règle au niveau du serveur à partir de la page de présentation du serveur, cliquez sur **Pare-feu** dans le menu de gauche sous Paramètres :

2. Cliquez sur **Ajouter une adresse IP cliente** dans la barre d’outils pour ajouter l’adresse IP de l’ordinateur que vous utilisez, puis cliquez sur **Enregistrer**. Une règle de pare-feu IP au niveau du serveur est créée pour votre adresse IP actuelle.

## <a name="manage-ip-firewall-rules-using-transact-sql"></a>Gérer les règles de pare-feu IP avec Transact-SQL

| Vue de catalogue ou procédure stockée | Niveau | Description |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Serveur |Affiche les règles de pare-feu IP au niveau du serveur actuelles |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Serveur |Crée ou met à jour les règles de pare-feu IP au niveau du serveur |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Serveur |Supprime des règles de pare-feu IP au niveau du serveur |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Base de données |Affiche les règles de pare-feu IP actuelles au niveau de la base de données |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Base de données |Crée ou met à jour les règles de pare-feu IP au niveau de la base de données |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Bases de données |Supprime des règles de pare-feu IP au niveau de la base de données |

Les exemples suivants examinent les règles existantes, activent une plage d’adresses IP sur le serveur Contoso et suppriment une règle de pare-feu IP :

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Ensuite, ils ajoutent une règle de pare-feu IP au niveau du serveur.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Pour supprimer une règle de pare-feu IP au niveau du serveur, exécutez la procédure stockée sp_delete_firewall_rule. L’exemple suivant supprime la règle nommée ContosoFirewallRule :

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

## <a name="manage-server-level-ip-firewall-rules-using-azure-powershell"></a>Gérer des règles de pare-feu IP au niveau du serveur avec Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont pour le module Az.Sql. Pour ces applets de commande, consultez [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments pour les commandes dans le module Az et dans les modules AzureRm sont sensiblement identiques.

| Applet de commande | Niveau | Description |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Serveur |Retourne les règles de pare-feu au niveau du serveur actuelles |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Serveur |Crée une règle de pare-feu au niveau du serveur |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Serveur |Met à jour les propriétés d’une règle de pare-feu au niveau du serveur existante |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Serveur |Supprime des règles de pare-feu au niveau du serveur |

L’exemple suivant définit une règle de pare-feu IP au niveau du serveur à l’aide de PowerShell :

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> Pour obtenir des exemples PowerShell dans le contexte d’un démarrage rapide, consultez [Créer une base de données - PowerShell](sql-database-powershell-samples.md) et [Utiliser PowerShell pour créer une base de données unique et configurer une règle de pare-feu IP au niveau du serveur SQL Database](scripts/sql-database-create-and-configure-database-powershell.md)

## <a name="manage-server-level-ip-firewall-rules-using-azure-cli"></a>Gérer des règles de pare-feu IP au niveau du serveur avec Azure CLI

| Applet de commande | Niveau | Description |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Serveur|Crée une règle de pare-feu IP de serveur|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Serveur|Liste les règles de pare-feu IP sur un serveur|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Serveur|Affiche les détails d’une règle de pare-feu IP|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Serveur|Met à jour une règle de pare-feu IP|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Serveur|Supprime une règle de pare-feu IP|

L’exemple suivant définit une règle de pare-feu IP au niveau du serveur à l’aide d’Azure CLI :

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Pour obtenir un exemple Azure CLI dans le contexte d’un démarrage rapide, consultez [Créer une base de données - Azure CLI](sql-database-cli-samples.md) et [Utiliser Azure CLI pour créer une base de données unique et configurer une règle de pare-feu IP SQL Database](scripts/sql-database-create-and-configure-database-cli.md)

## <a name="manage-server-level-ip-firewall-rules-using-rest-api"></a>Gérer des règles de pare-feu IP au niveau du serveur avec l’API REST

| API | Niveau | Description |
| --- | --- | --- |
| [Répertorier les règles de pare-feu](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Serveur |Affiche les règles de pare-feu IP au niveau du serveur actuelles |
| [Créer ou mettre à jour une règle de pare-feu](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Serveur |Crée ou met à jour les règles de pare-feu IP au niveau du serveur |
| [Supprimer une règle de pare-feu](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Serveur |Supprime des règles de pare-feu IP au niveau du serveur |
| [Obtenir les règles de pare-feu](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Serveur | Obtient les règles de pare-feu IP au niveau du serveur |

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Règles de pare-feu IP au niveau du serveur et au niveau de la base de données

Q. Les utilisateurs d’une base de données doivent-ils être totalement isolés d’une autre base de données ?
Si oui, accordez l’accès à l’aide de règles de pare-feu IP au niveau de la base de données. Cela évite d’utiliser des règles de pare-feu IP au niveau du serveur, qui autorisent l’accès à travers le pare-feu à toutes les bases de données et réduit le champ de vos défenses.

Q. Les utilisateurs à l’adresse IP ont-ils besoin d’accéder à toutes les bases de données ?
Utilisez des règles de pare-feu IP au niveau du serveur pour réduire le nombre de règles de pare-feu IP à configurer.

Q. La personne ou l’équipe qui configure les règles de pare-feu IP a-t-elle seulement accès par le biais du portail Azure, de PowerShell ou de l’API REST ?
Vous devez utiliser des règles de pare-feu IP au niveau du serveur. Les règles de pare-feu IP au niveau de la base de données peuvent être configurées seulement avec Transact-SQL.  

Q. La personne ou l’équipe qui configure les règles de pare-feu IP a-t-elle l’interdiction d’avoir une autorisation générale au niveau de la base de données ?
Utilisez des règles de pare-feu IP au niveau du serveur. La configuration des règles de pare-feu IP au niveau de la base de données avec Transact-SQL nécessite au moins l’autorisation `CONTROL DATABASE` au niveau de la base de données.  

Q. La personne ou l’équipe qui configure ou vérifie les règles de pare-feu IP gère-t-elle de façon centralisée les règles de pare-feu IP pour un grand nombre de bases de données (plusieurs centaines) ?
Cette sélection dépend de vos besoins et de votre environnement. Les règles de pare-feu IP au niveau du serveur peuvent être plus faciles à configurer, mais des scripts peuvent configurer les règles au niveau de la base de données. Par ailleurs, même si vous utilisez des règles de pare-feu IP au niveau du serveur, vous devez peut-être vérifier les règles de pare-feu IP au niveau de la base de données pour voir si les utilisateurs avec l’autorisation `CONTROL` sur la base de données ont créé des règles de pare-feu IP au niveau de la base de données.

Q. Puis-je utiliser à la fois des règles de pare-feu IP au niveau du serveur et au niveau de la base de données ?
Oui. Certains utilisateurs, comme les administrateurs, peuvent avoir besoin de règles de pare-feu IP au niveau du serveur. D’autres utilisateurs, comme les utilisateurs d’une application de base de données, peuvent avoir besoin de règles de pare-feu IP au niveau de la base de données.

## <a name="troubleshooting-the-database-firewall"></a>Dépannage du pare-feu de base de données

Considérez les points suivants quand l’accès au service Microsoft Azure SQL Database est anormal :

- **Configuration du pare-feu local :**

  Pour que votre ordinateur puisse accéder à Azure SQL Database, vous devez créer une exception de pare-feu sur votre ordinateur pour le port TCP 1433. Vous devrez peut-être ouvrir des ports supplémentaires si vous effectuez des connexions dans la limite du cloud Azure. Pour plus d’informations, consultez la section **SQL Database : exécution externe ou exécution interne** de [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Traduction d’adresses réseau (NAT) :**

  En raison de la traduction d’adresses réseau, l’adresse IP utilisée par votre ordinateur pour se connecter à Azure SQL Database peut être différente de l’adresse IP affichée dans les paramètres de configuration IP de votre ordinateur. Pour afficher l’adresse IP qu’utilise votre ordinateur pour se connecter à Azure, connectez-vous au portail et accédez à l’onglet **Configurer** sur le serveur qui héberge votre base de données. Dans la section **Adresses IP autorisées**, l’**adresse IP du client actif** s’affiche. Cliquez sur **Ajouter** **aux adresses IP autorisées** pour que cet ordinateur puisse accéder au serveur.

- **Les modifications apportées à la liste verte n’ont pas encore pris effet :**

  Jusqu’à cinq minutes peuvent s’écouler avant que les modifications apportées à la configuration du pare-feu Azure SQL Database ne soient effectives.

- **La connexion n’est pas autorisée ou un mot de passe incorrect a été utilisé :**

  Si une connexion n’a pas d’autorisations sur le serveur Azure SQL Database ou que le mot de passe est incorrect, la connexion au serveur Azure SQL Database est refusée. Créer un paramètre de pare-feu permet uniquement aux clients de tenter de se connecter à votre serveur ; chaque client doit fournir les informations d’identification de sécurité nécessaires. Pour plus d’informations sur la préparation des connexions, consultez [Gestion des bases de données, des connexions et des utilisateurs dans Azure SQL Database](sql-database-manage-logins.md).

- **Adresse IP dynamique :**

  Si vous avez une connexion Internet avec un adressage IP dynamique et que le pare-feu demeure infranchissable, vous pouvez essayer une des solutions suivantes :
  
  - Demandez à votre fournisseur de services Internet (ISP) pour la plage d’adresses IP affectée à vos ordinateurs clients qui accèdent au serveur de base de données SQL Azure et ajoutez la plage d’adresses IP en tant qu’une règle de pare-feu IP.
  - Obtenez un adressage IP statique à la place pour vos ordinateurs clients, puis ajoutez les adresses IP dans des règles de pare-feu IP.

## <a name="next-steps"></a>Étapes suivantes

- Confirmez que votre environnement de réseau d’entreprise autorise les communications entrantes issues les plages d’adresses IP de calcul (y compris les plages SQL) utilisées par les centres de données Microsoft Azure. Il peut être nécessaire à la liste verte ces adresses IP, consultez [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653)  
- Pour obtenir un guide de démarrage rapide sur la création d’une règle de pare-feu IP au niveau du serveur, consultez [Créer une base de données Azure SQL](sql-database-single-database-get-started.md).
- Pour de l’aide afin de vous connecter à une base de données Azure SQL à partir d’applications open source ou tierces, consultez [Exemples de code de démarrage rapide client pour Azure SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Pour plus d’informations sur les autres ports que vous devrez peut-être ouvrir, consultez la section **SQL Database : exécution externe ou exécution interne** de [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Pour obtenir une vue d’ensemble de la sécurité Azure SQL Database, consultez [Sécurisation de votre base de données](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
