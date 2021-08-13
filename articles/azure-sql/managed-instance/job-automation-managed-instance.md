---
title: Automatisation des travaux SQL Agent
titleSuffix: Azure SQL Managed Instance
description: Options d’automatisation pour exécuter des scripts Transact-SQL (T-SQL) dans Azure SQL Managed Instance
services: sql-database
ms.service: sql-db-mi
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1
dev_langs:
- TSQL
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 06/03/2021
ms.openlocfilehash: ef1dceba0c95729ccb824b1ad2f32641c92891e7
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111441907"
---
# <a name="automate-management-tasks-using-sql-agent-jobs-in-azure-sql-managed-instance"></a>Automatiser des tâches de gestion à l’aide de travaux SQL Agent dans Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

À l’aide de [SQL Server Agent](/sql/ssms/agent/sql-server-agent) dans SQL Server et [SQL Managed Instance](sql-managed-instance-paas-overview.md), vous pouvez créer et planifier des travaux qui s’exécutent régulièrement sur une ou plusieurs bases de données afin d’exécuter des requêtes T-SQL (Transact-SQL) et d’effectuer des tâches de maintenance. Cet article décrit l’utilisation de SQL Agent pour SQL Managed Instance.

> [!Note]
> SQL Agent n’est pas disponible dans Azure SQL Database ou Azure Synapse Analytics. Par conséquent, nous vous recommandons l’[automatisation de travaux avec des travaux élastiques](../database/job-automation-overview.md).

### <a name="sql-agent-job-limitations-in-sql-managed-instance"></a>Limitations des travaux SQL Agent dans SQL Managed instance

Il convient de noter les différences entre SQL Agent disponible dans SQL Server et dans le cadre de SQL Managed Instance. Pour plus d’informations sur les différences entre SQL Server et SQL Managed Instance au sujet des fonctionnalités prises en charge, consultez [Différences T-SQL entre Azure SQL Managed Instance et SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent). 

Certaines des fonctionnalités de SQL Agent disponibles dans SQL Server ne sont pas prises en charge dans SQL Managed Instance :

- Les paramètres de l’Agent SQL sont en lecture seule. 
    - La procédure stockée système `sp_set_agent_properties` n’est pas prise en charge.
- L’activation/la désactivation de SQL Agent n’est actuellement pas prise en charge. L’Agent SQL est toujours en cours d’exécution.
- Les notifications sont partiellement prises en charge :
  - Les récepteurs de radiomessagerie ne sont pas pris en charge.
  - NetSend n’est pas pris en charge.
  - Les alertes ne sont pas prises en charge.
- Les proxies ne sont pas pris en charge.
- EventLog n’est pas pris en charge.
- Le déclencheur de planification de travail basé sur un processeur inactif n’est pas pris en charge.

## <a name="when-to-use-sql-agent-jobs"></a>Quand utiliser des travaux SQL Agent 

Vous pouvez utiliser des travaux SQL Agent dans plusieurs scénarios :

- Automatiser des tâches de gestion et les planifier pour qu’elles s’exécutent tous les jours ouvrés, en dehors des heures d’ouverture, etc.
  - Déployer des changements de schéma, la gestion des informations d’identification, la collecte des données de performances ou la collecte des données de télémétrie du locataire (client).
  - Mettre à jour des données de référence (des informations communes à toutes les bases de données), charger des données depuis Stockage Blob Azure. Microsoft recommande d’utiliser l’authentification par [SIGNATURE D’ACCÈS PARTAGÉ](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage) pour s’authentifier auprès du Stockage Blob Azure.
  - Effectuer les tâches de maintenance courantes, notamment `DBCC CHECKDB` pour garantir l’intégrité des données ou la maintenance des index pour améliorer les performances des requêtes. Configurer des travaux de manière à ce qu’ils s’exécutent sur une collection de bases de données de façon récurrente, par exemple pendant les heures creuses.
  - Collecter les résultats de la requête à partir d'un ensemble de bases de données dans une table centrale sur une base continue. Les requêtes de performances peuvent être exécutées en permanence et configurées pour déclencher des tâches supplémentaires à exécuter.
- Collecter des données de création de rapports
  - Agréger des données provenant d’une collection de bases de données dans une table de destination unique.
  - Exécuter des requêtes de traitement de données avec un temps d’exécution plus long sur un grand ensemble de bases de données, par exemple, la collection de télémétrie de client. Les résultats sont rassemblés dans une table de destination unique pour une analyse ultérieure.
- Déplacements de données
  - Créez des travaux qui répliquent les modifications apportées à vos bases de données sur d’autres bases de données, ou qui collectent les mises à jour effectuées dans des bases de données distantes et appliquent les modifications à la base de données.
  - Créez des travaux qui chargent des données depuis ou vers vos bases de données avec SQL Server Integration Services (SSIS).

## <a name="sql-agent-jobs-in-sql-managed-instance"></a>Travaux SQL Agent dans Azure SQL Managed instance

Les travaux SQL Agent sont exécutés par le service SQL Agent qui continue à être utilisé pour l’automatisation des tâches dans SQL Server et SQL Managed Instance. 

Les travaux SQL Agent sont constitués de séries spécifiées de scripts T-SQL sur votre base de données. Utilisez des travaux pour définir une tâche d’administration qui peut être exécutée une ou plusieurs fois, et dont la réussite ou l’échec sont surveillés. 

Un travail peut s’exécuter sur un serveur local ou sur plusieurs serveurs distants. Travaux SQL Agent est un composant du moteur de base de données interne qui est exécuté au sein du service SQL Managed Instance.

Il existe plusieurs concepts clés dans les travaux SQL Agent :

- **Étapes de travail** : ensemble d’une ou plusieurs étapes qui doivent être exécutées au sein du travail. Pour chaque étape de travail, vous pouvez définir une stratégie de nouvelle tentative et l’action qui doit se produire si l’étape de travail réussit ou échoue.
- Les **planifications** définissent quand le travail doit être exécuté.
- Les **notifications** vous permettent de définir des règles qui sont utilisées pour notifier les opérateurs via des e-mails une fois le travail terminé.

### <a name="sql-agent-job-steps"></a>Étapes de travail SQL Agent

Les étapes de travail SQL Agent sont des séquences d’actions que SQL Agent doit s’exécuter. Pour chaque étape, vous définissez l’étape suivante à exécuter si elle réussit ou si elle échoue, ainsi que le nombre de nouvelles tentatives en cas d’échec.

SQL Agent vous permet de créer différents types d’étapes de travail, comme une étape de travail Transact-SQL qui exécute un lot Transact-SQL sur la base de données, ou des étapes de commande du système d’exploitation/PowerShell qui peuvent exécuter un script personnalisé du système d’exploitation, des [étapes de travail SSIS](../../data-factory/how-to-invoke-ssis-package-managed-instance-agent.md) qui vous permettent de charger des données avec le runtime SSIS, ou des étapes de [réplication](../managed-instance/replication-transactional-overview.md) qui peuvent publier les modifications de votre base de données auprès d’autres bases de données.

> [!Note]
> Pour plus d’informations sur l’utilisation d’Azure-SSIS Integration Runtime avec SSISDB hébergée par SQL Managed Instance, consultez [Utilisation d’Azure SQL Managed Instance avec SQL Server Integration Services (SSIS) dans Azure Data Factory](../../data-factory/how-to-use-sql-managed-instance-with-ir.md).

La [réplication transactionnelle](../managed-instance/replication-transactional-overview.md) permet de répliquer les modifications apportées aux tables dans d’autres bases de données SQL Managed Instance, Azure SQL Database ou SQL Server. Pour plus d’informations, consultez [Configurer la réplication dans Azure SQL Managed Instance](../../azure-sql/managed-instance/replication-between-two-instances-configure-tutorial.md). 

Les autres types d’étapes de travail ne sont pas pris en charge dans SQL Managed Instance, notamment :

- L’étape de travail de réplication de fusion n’est pas prise en charge.
- L’agent de lecture de la file d’attente n’est pas pris en charge.
- Analysis Services n’est pas pris en charge
 
### <a name="sql-agent-job-schedules"></a>Planifications de travaux SQL Agent

Une planification spécifie quand un travail s’exécute. Plusieurs travaux peuvent s’exécuter sur la même planification, et plusieurs planifications peuvent appliquer le même travail.

Une planification peut définir les conditions suivantes pour l’heure d’exécution d’un travail :

- À chaque démarrage de SQL Server Agent. Le travail est activé après chaque basculement.
- Une seule fois, à une date et heure spécifiques, ce qui est pratique pour l’exécution différée d’un travail.
- Selon une planification récurrente.

> [!Note]
> SQL Managed Instance ne permet pas le démarrage d’un travail quand le processeur est inactif.

### <a name="sql-agent-job-notifications"></a>Notifications de travaux SQL Agent

Les travaux SQL Agent vous permettent de recevoir des notifications quand le travail se termine avec succès ou quand il échoue. Vous pouvez recevoir des notifications par e-mail.

Si elle n’est pas déjà activée, vous devez d’abord configurer la fonctionnalité [Database Mail](/sql/relational-databases/database-mail/database-mail) sur SQL Managed Instance :

```sql
GO
EXEC sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
EXEC sp_configure 'Database Mail XPs', 1;
GO
RECONFIGURE
```

À titre d’exercice, configurez le compte e-mail à utiliser pour envoyer les notifications par e-mail. Affectez le compte au profil d’e-mail appelé `AzureManagedInstance_dbmail_profile`. Pour envoyer un e-mail à l’aide de travaux SQL Agent dans SQL Managed Instance, il doit exister un profil appelé `AzureManagedInstance_dbmail_profile`. Sinon, SQL Managed Instance n’est pas en mesure d’envoyer des e-mails par le biais de SQL Agent. Consultez l’exemple suivant :

```sql
-- Create a Database Mail account
EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name = 'SQL Agent Account',
    @description = 'Mail account for Azure SQL Managed Instance SQL Agent system.',
    @email_address = '$(loginEmail)',
    @display_name = 'SQL Agent Account',
    @mailserver_name = '$(mailserver)' ,
    @username = '$(loginEmail)' ,
    @password = '$(password)';

-- Create a Database Mail profile
EXECUTE msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @description = 'E-mail profile used for messages sent by Managed Instance SQL Agent.';

-- Add the account to the profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @account_name = 'SQL Agent Account',
    @sequence_number = 1;
```

Testez la configuration de Database Mail par le biais de T-SQL à l’aide de la procédure stockée système [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) :

```sql
DECLARE @body VARCHAR(4000) = 'The email is sent from ' + @@SERVERNAME;
EXEC msdb.dbo.sp_send_dbmail  
    @profile_name = 'AzureManagedInstance_dbmail_profile',  
    @recipients = 'ADD YOUR EMAIL HERE',  
    @body = 'Add some text',  
    @subject = 'Azure SQL Instance - test email';  
```

Vous pouvez notifier l’opérateur que quelque chose s’est produit avec vos travaux SQL Agent. Un opérateur définit les informations de contact d’une personne responsable de la maintenance d’une ou plusieurs instances dans SQL Managed Instance. Parfois, les responsabilités d’opérateur sont affectées à une seule personne.

Dans les systèmes avec plusieurs instances dans SQL Managed Instance ou SQL Server, les responsabilités d’opérateur peuvent être partagées entre de nombreuses personnes. Un opérateur ne contient pas d’informations de sécurité et ne définit pas de principal de sécurité. Dans l’idéal, un opérateur n’est pas une personne dont les responsabilités peuvent changer, mais un groupe de distribution par e-mail.   

Vous pouvez [créer des opérateurs](/sql/relational-databases/system-stored-procedures/sp-add-operator-transact-sql) avec SQL Server Management Studio (SSMS) ou le script Transact-SQL illustré dans l’exemple suivant :

```sql
EXEC msdb.dbo.sp_add_operator
    @name=N'AzureSQLTeam',
    @enabled=1,
    @email_address=N'AzureSQLTeamn@contoso.com';
```

Vérifiez la réussite ou l’échec de l’e-mail par le biais du [journal Database Mail](/sql/relational-databases/database-mail/database-mail-log-and-audits) dans SSMS.

Vous pouvez ensuite [modifier n’importe quel travail SQL Agent](/sql/relational-databases/system-stored-procedures/sp-update-job-transact-sql) et affecter les opérateurs qui sont notifiés par e-mail si le travail se termine, échoue ou réussit avec SSMS ou avec le script Transact-SQL suivant :

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS',
    @notify_level_email=3, -- Options are: 1 on succeed, 2 on failure, 3 on complete
    @notify_email_operator_name=N'AzureSQLTeam';
```

### <a name="sql-agent-job-history"></a>Historique des travaux SQL Agent

SQL Managed Instance ne permet actuellement pas de changer les propriétés SQL Agent. En effet, elles sont stockées dans les valeurs de registre sous-jacentes. Cela signifie que les options d’ajustement de la stratégie de conservation de l’agent pour les enregistrements de l’historique des travaux sont fixées aux valeurs par défaut de 1000 enregistrements en tout et de 100 enregistrements d’historique maximum par travail.

### <a name="sql-agent-fixed-database-role-membership"></a>Appartenance au rôle de base de données fixe de SQL Agent

Si des utilisateurs liés à des connexions autres que celles de l’administrateur système sont ajoutés à un des trois rôles de base de données fixe SQL Agent dans la base de données système msdb, un problème se pose du fait que des autorisations EXECUTE explicites doivent être accordées aux procédures stockées système dans la base de données MASTER. Si ce problème survient, le message d’erreur « L’autorisation EXECUTE a été refusée sur l’objet <object_name> (Microsoft SQL Server, erreur : 229) » s’affiche. 

Une fois que vous avez ajouté des utilisateurs à un rôle de base de données fixe SQL Agent (SQLAgentUserRole, SQLAgentReaderRole ou SQLAgentOperatorRole) dans msdb, pour chacune des connexions de l’utilisateur ajoutées à ces rôles, exécutez le script T-SQL ci-dessous pour accorder explicitement des autorisations EXECUTE aux procédures stockées système listées. Cet exemple suppose que le nom d’utilisateur et le nom de connexion sont identiques :

```sql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name];
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name];
```

## <a name="learn-more"></a>En savoir plus

- [Qu’est-ce qu’Azure SQL Managed Instance ?](../managed-instance/sql-managed-instance-paas-overview.md)
- [Nouveautés d’Azure SQL Database et de SQL Managed Instance](../../azure-sql/database/doc-changes-updates-release-notes.md?tabs=managed-instance)
- [Différences T-SQL d’Azure SQL Managed Instance par rapport à SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)
- [Comparaison des fonctionnalités : Azure SQL Database et Azure SQL Managed Instance](../../azure-sql/database/features-comparison.md)
