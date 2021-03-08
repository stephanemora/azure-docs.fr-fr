---
title: Migrer des bases de données vers SQL Managed Instance à l’aide du service LRS
description: Découvrez comment migrer des bases de données de SQL Server vers SQL Managed Instance à l’aide du service LRS
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: bc0dc72c7547c8f74aec53b7153fc5384c6b634b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690785"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service-preview"></a>Migrer des bases de données de SQL Server vers SQL Managed Instance à l’aide du service LRS (préversion)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Cet article explique comment configurer manuellement la migration de la base de données de SQL Server 2008-2019 vers SQL Managed Instance à l’aide du service LRS actuellement disponible en préversion publique. Il s’agit d’un service cloud activé pour Managed Instance basé sur la technologie de copie des journaux de transaction de SQL Server. Le service LRS doit être utilisé dans les cas où il existe des migrations personnalisées complexes et des architectures hybrides, lorsque davantage de contrôle est nécessaire, lorsqu’il existe une faible tolérance aux temps d’arrêt ou quand Azure Data Migration Service (DMS) ne peut pas être utilisé.

DMS et LRS utilisent la même technologie de migration sous-jacente et les mêmes API. Avec la publication du service LRS, nous offrons la possibilité d’utiliser des migrations personnalisées complexes et une architecture hybride entre des environnements locaux. SQL Server et SQL Managed Instance.

## <a name="when-to-use-log-replay-service"></a>Quand utiliser le service LRS

Dans les cas où [Azure DMS](/azure/dms/tutorial-sql-server-to-managed-instance.md) ne peut pas être utilisé pour la migration, le service cloud LRS peut être utilisé directement avec PowerShell, les cmdlets CLI ou l’API pour créer et orchestrer manuellement des migrations de base de données vers SQL Managed instance. 

Vous pouvez envisager d’utiliser le service cloud LRS dans certains des cas suivants :
- Davantage de contrôle est nécessaire pour votre projet de migration de base de données
- Il existe une faible tolérance aux temps d’arrêt lors du basculement de la migration
- L’exécutable DMS ne peut pas être installé dans votre environnement
- L’exécutable DMS ne dispose pas d’un accès de niveau fichier aux sauvegardes de bases de données
- Aucun accès au système d’exploitation hôte ou aucun privilège d’administrateur n’est disponible
- Il est impossible d’ouvrir des ports de mise en réseau à partir de votre environnement vers Azure
- Les sauvegardes sont stockées directement dans le Stockage Blob Azure à l’aide de l’option « TO URL »
- L’utilisation de sauvegardes différentielles est nécessaire

> [!NOTE]
> La méthode automatisée recommandée pour migrer des bases de données de SQL Server vers SQL Managed Instance consiste à utiliser Azure DMS. Ce service utilise le même service cloud LRS sur le back-end avec la copie des journaux de transaction en mode NORECOVERY. Vous devriez envisager d’utiliser manuellement le service LRS pour orchestrer les migrations dans les cas où Azure DMS ne prend pas entièrement en charge vos scénarios.

## <a name="how-does-it-work"></a>Fonctionnement

La création d’une solution personnalisée utilisant le service LRS pour migrer des bases de données vers le cloud nécessite plusieurs étapes d’orchestration présentées dans le diagramme et décrites dans le tableau ci-dessous.

La migration consiste à effectuer des sauvegardes complètes de la base de données sur SQL Server avec CHECKSUM activé, et à copier les fichiers de sauvegarde dans le Stockage Blob Azure. Le service LRS est utilisé pour restaurer les fichiers de sauvegarde à partir du Stockage Blob Azure vers SQL Managed Instance. Le Stockage Blob Azure est utilisé comme stockage intermédiaire entre SQL Server et SQL Managed Instance.

Le service LRS supervise dans le Stockage Blob Azure la présence de nouvelles sauvegardes de fichier journal ou sauvegardes différentielles, ajoutées après la restauration de la sauvegarde complète, et il restaure automatiquement tous les nouveaux fichiers ajoutés. La progression de la restauration des fichiers de sauvegarde sur SQL Managed Instance peut être supervisée à l’aide du service, et le processus peut également être abandonné si nécessaire.

Le service LRS ne nécessite pas une convention d’affectation de noms de fichier de sauvegarde spécifique, car il analyse tous les fichiers placés dans le Stockage Blob Azure et construit la chaîne de sauvegarde à partir de la lecture des en-têtes de fichier uniquement. Les bases de données sont dans l’état « restauration » pendant le processus de migration, car elles sont restaurées en mode [NORECOVERY](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) et ne peuvent pas être utilisées à des fins de lecture ou d’écriture tant que le processus de migration n’est pas complètement terminé. 

Lors de la migration de plusieurs bases de données, les sauvegardes de chaque base de données doivent être placées dans un dossier distinct dans le Stockage Blob Azure. Le service LRS doit être démarré séparément pour chaque base de données et différents chemins d’accès à des dossiers du Stockage Blob Azure doivent être spécifiés. 

Le service LRS peut être démarré en mode d’autocomplétion ou en mode continu. Lorsqu’il est démarré en mode d’autocomplétion, la migration se termine automatiquement lorsque le nom du dernier fichier de sauvegarde spécifié a été restauré. Lorsqu’il est démarré en mode continu, le service restaure en continu les nouveaux fichiers de sauvegarde ajoutés et la migration se termine uniquement lors du basculement manuel. Il est recommandé d’exécuter le basculement manuel uniquement après que la dernière sauvegarde de la fin de journal a été effectuée et affichée comme restauré dans SQL Managed Instance. Lors de l’étape finale du basculement, la base de données est mise en ligne et disponible à des fins de lecture et d’écriture dans SQL Managed Instance.

Une fois le service LRS arrêté, soit automatiquement avec l’autocomplétion, soit manuellement lors du basculement, le processus de restauration ne peut pas reprendre pour une base de données qui a été mise en ligne dans SQL Managed Instance. Pour restaurer des fichiers de sauvegarde supplémentaires une fois la migration terminée via l’autocomplétion, ou manuellement lors du basculement, la base de données doit être supprimée et la totalité de la chaîne de sauvegarde doit être restaurée à partir de zéro via le redémarrage du service LRS.

![Étapes de l’orchestration du service LRS expliquées pour SQL Managed Instance](./media/log-replay-service-migrate/log-replay-service-conceptual.png)
    
| Opération | Détails |
| :----------------------------- | :------------------------- |
| **1. Copier les sauvegardes de base de données de SQL Server vers le Stockage Blob Azure**. | - Copiez les sauvegardes complètes, différentielles et de journal de SQL Server vers le conteneur Stockage Blob Azure à l’aide de [Azcopy](/azure/storage/common/storage-use-azcopy-v10)ou de l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/). <br />- Utilisez n’importe quels noms de fichiers, car le service LRS ne requiert pas de convention d’affectation de noms spécifique.<br />- Lors de la migration de plusieurs bases de données, un dossier distinct est requis pour chaque base de données. |
| **2. Démarrer le service LRS dans le cloud**. | - Le service peut être démarré au moyen de diverses cmdlets : <br /> PowerShell [start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_start cmdlets](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start). <br /> - Démarrez le service LRS séparément pour chaque base de données qui pointe vers un dossier de sauvegarde différent dans le Stockage Blob Azure. <br />- Une fois démarré, le service prend les sauvegardes dans le conteneur Stockage Blob Azure et commence à les restaurer dans SQL Managed Instance.<br /> - Si le service LRS a été démarré en mode continu, une fois que toutes les sauvegardes initialement chargées ont été restaurées, le service surveille les nouveaux fichiers chargés dans le dossier et applique en continu des journaux basés sur la chaîne LSN, jusqu’à ce que le service soit arrêté. |
| **2.1. Superviser la progression de l’opération**. | - Il est possible de superviser la progression de l’opération de restauration au moyen de diverses cmdlets : <br /> PowerShell [get-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_show cmdlets](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show). |
| **2.2. Arrêter\abandonner l’opération si nécessaire**. | - Si le processus de migration doit être abandonné, l’opération peut être arrêtée au moyen de diverses cmdlets : <br /> PowerShell [stop-azsqlinstancedatabaselogreplay]/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) cmdlets. <br /><br />- Cela entraîne la suppression de la base de données en cours de restauration dans SQL Managed Instance. <br />- Une fois arrêté, le service LRS ne peut pas reprendre pour une base de données. Le processus de migration doit être redémarré à partir de zéro. |
| **3. Basculement vers le cloud lorsque vous êtes prêt**. | - Arrêtez l’application et la charge de travail. Chargez la dernière sauvegarde de la fin de journal dans le Stockage Blob Azure.<br /> - Effectuez le basculement en lançant l’opération de fin du service LRS au moyen de diverses cmdlets : <br />PowerShell [complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) cmdlets. <br /><br />- Cela entraînera l’arrêt du service LRS et la mise en ligne de la base de données à des fins de lecture et d’écriture sur SQL Managed Instance.<br /> - Redirigez la chaîne de connexion de l’application de SQL Server vers SQL Managed Instance. |

## <a name="requirements-for-getting-started"></a>Conditions requises pour bien démarrer

### <a name="sql-server-side"></a>Côté SQL Server
- SQL Server 2008-2019
- Sauvegarde complète des bases de données (un ou plusieurs fichiers)
- Sauvegarde différentielle (un ou plusieurs fichiers)
- Sauvegarde de journal (non fractionnée pour le fichier journal des transactions)
- **CHECKSUM doit être activé** pour les sauvegardes (obligatoire)

### <a name="azure-side"></a>Côté Azure
- Module PowerShell Az.SQL version 2.16.0, ou supérieure, ([installer](https://www.powershellgallery.com/packages/Az.Sql/) ou utiliser Azure [Cloud Shell](/azure/cloud-shell/))
- CLI version 2.19.0, ou supérieure, ([installer](/cli/azure/install-azure-cli))
- Conteneur Stockage Blob Azure provisionné
- Jeton de sécurité SAS avec uniquement les autorisations **Lecture** et **Liste** généré pour le conteneur Stockage Blob

### <a name="migrating-multiple-databases"></a>Migration de plusieurs bases de données
- Les fichiers de sauvegarde de différentes bases de données doivent être placés dans des dossiers distincts dans le Stockage Blob Azure.
- Le service LRS doit être démarré séparément pour chaque base de données qui pointe vers un dossier approprié dans le Stockage Blob Azure.
- Le service LRS peut prendre en charge jusqu’à 100 processus de restauration simultanés pour une seule instance SQL Managed Instance.

### <a name="azure-rbac-permissions-required"></a>Autorisations Azure RBAC requises
L’exécution du service LRS via les clients fournis requiert l’un des rôles Azure suivants :
- Rôle Propriétaire de l’abonnement ; ou
- Rôle [Contributeur Managed Instance](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor), ou
- Rôle personnalisé avec l’autorisation suivante :
  - `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Meilleures pratiques

Les meilleures pratiques suivantes sont fortement recommandées :
- Exécutez l’[Assistant Migration de données](/sql/dma/dma-overview) pour confirmer que vos bases de données sont prêtes à être migrées vers SQL Managed instance. 
- Fractionnez les sauvegardes complètes et différentielles en plusieurs fichiers, au lieu d’un seul fichier.
- Activez la compression de la sauvegarde.
- Utilisez Cloud Shell pour exécuter les scripts, car il dispose toujours des cmdlets les plus récentes.
- Planifiez la fin de la migration dans un délai de 47 heures après le démarrage du service LRS. Il s’agit d’une période de grâce empêchant les correctifs logiciels gérés par le système une fois que le service LRS a démarré.

> [!IMPORTANT]
> - Une base de données en cours de restauration à l’aide du service LRS ne peut pas être utilisée tant que le processus de migration n’est pas terminé.
> - L’accès en lecture seule aux bases de données pendant la migration n’est pas pris en charge par le service LRS.
> - Une fois la migration terminée, le processus de migration est finalisé, car le service LRS ne prend pas en charge la reprise de la restauration.

## <a name="steps-to-execute"></a>Étapes à exécuter

### <a name="make-backups-on-the-sql-server"></a>Effectuer des sauvegardes sur SQL Server

Les sauvegardes sur SQL Server peuvent être effectuées à l’aide de l’une des deux options suivantes :

- Sauvegardez dans le stockage sur disque local, puis chargez les fichiers dans le Stockage Blob Azure, si votre environnement est restrictif envers une sauvegarde directe dans le Stockage Blob Azure.
- Sauvegardez directement dans le Stockage Blob Azure avec l’option « TO URL » dans T-SQL, si votre environnement et vos procédures de sécurité vous permettent de le faire. 

Configurez les bases de données que vous souhaitez migrer sur le mode de récupération complète pour autoriser les sauvegardes de journaux.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Pour effectuer manuellement une sauvegarde complète, différentielle et de journal de votre base de données dans le stockage local, utilisez les exemples de scripts T-SQL fournis ci-dessous. Vérifiez que l’option CHECKSUM est activée, car il s’agit d’une condition obligatoire pour le service LRS.

```SQL
-- Example on how to make full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to the locak disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-azure-blob-storage"></a>Créer un Stockage Blob Azure

Le Stockage Blob Azure est utilisé comme stockage intermédiaire pour les fichiers de sauvegarde entre SQL Server et SQL Managed Instance. Pour créer un compte de stockage et un conteneur Blob dans le compte de stockage, procédez comme suit :

1. [Créez un compte de stockage](../../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Créer un conteneur d’objets blob](../../storage/blobs/storage-quickstart-blobs-portal.md) dans le compte de stockage

### <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Copier les sauvegardes de SQL Server vers le Stockage Blob Azure

Certaines des approches suivantes peuvent être utilisées pour charger des sauvegardes dans le stockage d’objets blob lors de la migration de bases de données vers Managed Instance à l’aide du service LRS :
- Utilisez [Azcopy](/azure/storage/common/storage-use-azcopy-v10) ou de l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer) pour charger des sauvegardes dans un conteneur d’objets blob.
- Utilisez l’Explorateur Stockage dans le portail Azure.

### <a name="make-backups-from-sql-server-directly-to-azure-blob-storage"></a>Effectuer des sauvegardes de SQL Server directement dans le Stockage Blob Azure

Si votre stratégie d’entreprise et de mise en réseau l’autorise, l’alternative consiste à effectuer des sauvegardes de SQL Server directement dans le Stockage Blob Azure à l’aide de l’option [BACKUP TO URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) native de SQL Server. Si vous pouvez utiliser cette option, il n’est pas nécessaire d’effectuer des sauvegardes dans le stockage local et de les charger dans le Stockage Blob Azure.

La première étape consiste à générer un jeton d’authentification SAS pour le Stockage Blob Azure. Le jeton doit être importé dans SQL Server. La deuxième étape consiste à effectuer des sauvegardes avec l’option « TO URL » dans T-SQL. Assurez-vous que toutes les sauvegardes sont effectuées avec l’option CHEKSUM activée.

À titre de référence, vous trouverez ci-dessous un exemple de code pour effectuer des sauvegardes dans le Stockage Blob Azure. Cet exemple n’inclut pas d’instructions sur l’importation du jeton SAS. Des instructions détaillées, notamment sur la façon de générer et d’importer le jeton SAS dans SQL Server sont fournies dans le tutoriel suivant : [Utiliser le service Stockage Blob Azure avec SQL Server](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). 

```SQL
-- Example on how to make full database backup to URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the transactional log backup to URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-azure-blob-storage-sas-authentication-for-lrs"></a>Générer l’authentification SAS du Stockage Blob Azure pour le service LRS

Le Stockage Blob Azure est utilisé comme stockage intermédiaire pour les fichiers de sauvegarde entre SQL Server et SQL Managed Instance. Le jeton d’authentification SAS avec uniquement les autorisations Liste et Lecture doit être généré pour une utilisation par le service LRS. Cela permet au service LRS d’accéder au Stockage Blob Azure et d’utiliser les fichiers de sauvegarde pour les restaurer dans SQL Managed Instance. Afin de générer l’authentification SAS pour le service LRS, procédez comme suit :

1. Accéder à l’Explorateur Stockage à partir du portail Azure
2. Développer Conteneurs d'objets blob
3. Cliquez avec le bouton droit sur le conteneur d’objets blob et sélectionnez Obtenir une signature d’accès partagé  ![Le service LRS génère le jeton d’authentification SAS](./media/log-replay-service-migrate/lrs-sas-token-01.png)
4. Sélectionnez le délai d’expiration du jeton. Assurez-vous que le jeton est valide pour la durée de votre migration.
5. Sélectionnez le fuseau horaire pour le jeton : UTC ou votre heure locale
    - Le fuseau horaire du jeton et votre SQL Managed Instance peuvent ne pas correspondre. Assurez-vous que le jeton SAS a la validité de temps appropriée en tenant compte des fuseaux horaires. Si possible, définissez le fuseau horaire à une heure antérieure et ultérieure par rapport à celles de la fenêtre de votre migration planifiée.
6. Sélectionner uniquement les autorisations Lecture et Liste
    - Aucune autre autorisation ne doit être sélectionnée, sinon le service LRS ne pourra pas démarrer. Il s’agit d’une exigence de sécurité par défaut.
7. Cliquez sur le bouton Créer  ![Le service LRS génère le jeton d’authentification SAS](./media/log-replay-service-migrate/lrs-sas-token-02.png)

L’authentification SAS est générée avec la durée de validité que vous avez spécifiée précédemment. Vous aurez besoin de la version d’URI du jeton généré, comme indiqué dans la capture d’écran ci-dessous.

![Exemple d’URI de l’authentification SAS générée par le service LRS](./media/log-replay-service-migrate/lrs-generated-uri-token.png)

### <a name="copy-parameters-from-sas-token-generated"></a>Copier les paramètres du jeton SAS généré

Pour pouvoir utiliser correctement le jeton SAS afin de démarrer le service LRS, nous devons comprendre sa structure. L’URI du jeton SAS généré se compose de deux parties :
- StorageContainerUri et 
- StorageContainerSasToken, séparés par un point d’interrogation (?), comme illustré dans l’image ci-dessous.

    ![Exemple d’URI de l’authentification SAS générée par le service LRS](./media/log-replay-service-migrate/lrs-token-structure.png)

- La première partie commençant par « https:// » jusqu’au point d’interrogation (?) est utilisée pour le paramètre StorageContainerURI qui est alimenté comme entrée au service LRS. Cela donne au service LRS des informations sur le dossier dans lequel les fichiers de sauvegarde de base de données sont stockés.
- La deuxième partie, commençant après le point d’interrogation (?), dans l’exemple « sp= » et jusqu’à la fin de la chaîne, est le paramètre StorageContainerSasToken. Il s’agit du jeton d’authentification signé lui-même, valide pour la durée spécifiée. Cette partie n’a pas nécessairement besoin de commencer par « sp= » comme cela est illustré.

Copiez les paramètres comme suit :

1. Copiez la première partie du jeton à partir de « https:// » jusqu’au point d’interrogation (?) et utilisez-la en tant que paramètre StorageContainerUri dans PowerShell ou CLI pour démarrer le service LRS, comme illustré dans la capture d’écran ci-dessous.

    ![Copie du paramètre StorageContainerUri pour le service LRS](./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png)

2. Copiez la deuxième partie du jeton à partir du point d’interrogation (?) jusqu’à la fin de la chaîne et utilisez-la en tant que paramètre StorageContainerSasToken dans PowerShell ou CLI pour démarrer le service LRS, comme illustré dans la capture d’écran ci-dessous.

    ![Copie du paramètre StorageContainerSasToken pour le service LRS](./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png)

> [!IMPORTANT]
> - Les autorisations du jeton SAS pour le Stockage Blob Azure doivent être uniquement Lecture et Liste. Si d’autres autorisations sont accordées au jeton d’authentification SAS, le démarrage du service LRS échoue. Il s’agit d’exigences de sécurité par défaut.
> - Le jeton doit avoir une validité de temps appropriée. Vérifiez que les fuseaux horaires entre le jeton et Managed Instance sont pris en compte.
> - Assurez-vous que le paramètre StorageContainerUri pour PowerShell ou CLI est copié à partir de l’URI du jeton généré, depuis « https:// » jusqu’au point d’interrogation (?). N’incluez pas le point d’interrogation.
> Assurez-vous que le paramètre StorageContainerSasToken pour PowerShell ou CLI est copié à partir de l’URI du jeton généré, depuis le point d’interrogation (?) jusqu’à la fin de la chaîne. N’incluez pas le point d’interrogation.

### <a name="log-in-to-azure-and-select-subscription"></a>Se connecter à Azure et sélectionner un abonnement

Utilisez la cmdlet PowerShell suivante pour vous connecter à Azure :

```powershell
Login-AzAccount
```

Sélectionnez l’abonnement approprié dans lequel se trouve votre SQL Managed Instance à l’aide de la cmdlet PowerShell suivante :

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Démarrer la migration

Vous lancez la migration en démarrant le service LRS. Le service peut être démarré en mode d’autocomplétion ou en mode continu. Lorsqu’il est démarré en mode d’autocomplétion, la migration se termine automatiquement lorsque le dernier fichier de sauvegarde spécifié a été restauré. Cette option requiert que la commande de démarrage spécifie le nom de fichier du dernier fichier de sauvegarde. Lorsque le service LRS est démarré en mode continu, il restaure en continu les nouveaux fichiers de sauvegarde ajoutés et la migration se termine uniquement lors du basculement manuel. 

### <a name="start-lrs-in-autocomplete-mode"></a>Démarrer le service LRS en mode d’autocomplétion

Pour démarrer le service LRS en mode d’autocomplétion, utilisez les commandes PowerShell ou CLI suivantes. Spécifiez le nom du dernier fichier de sauvegarde avec le paramètre -LastBackupName. Lors de la restauration du nom du dernier fichier de sauvegarde spécifié, le service lance automatiquement le basculement.

Démarrer le service LRS en mode d’autocomplétion - Exemple PowerShell :

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Démarrer le service LRS en mode d’autocomplétion - Exemple CLI :

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Démarrer le service LRS en mode continu

Démarrer le service LRS en mode continu - Exemple PowerShell :

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Démarrer le service LRS en mode continu - Exemple CLI :

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="scripting-lrs-start-in-continuous-mode"></a>Script de démarrage du service LRS en mode continu

Les clients PowerShell et CLI utilisés pour démarrer le service LRS en mode continu sont synchrones. Cela signifie que les clients attendent la réponse de l’API signalant la réussite ou l’échec du démarrage du travail. Pendant cette attente, la commande ne rend pas le contrôle à l’invite de commandes. Si vous créez un script pour l’expérience de migration et que vous avez besoin que la commande de démarrage du service LRS rende immédiatement le contrôle afin continuer avec le reste du script, vous pouvez exécuter PowerShell en tant que travail en arrière-plan avec le commutateur -AsJob. Par exemple :

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

Quand vous démarrez un travail en arrière-plan, un objet de travail est immédiatement retourné, même si le travail prend plus de temps que prévu. Vous pouvez continuer à travailler dans la session sans interruption pendant l'exécution de la tâche. Pour plus d’informations sur l’exécution de PowerShell en tant que travail en arrière-plan, consultez la documentation [PowerShell Start-Job](/powershell/module/microsoft.powershell.core/start-job#description).

De même, pour démarrer une commande CLI sur Linux en tant que processus en arrière-plan, utilisez le signe « esperluette » (&) à la fin de la commande de démarrage du service LRS.

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> Une fois que le service LRS a été démarré, tous les correctifs logiciels gérés par le système sont arrêtés pendant les 47 heures suivantes. Au-delà de ce délai, le prochain correctif logiciel automatisé arrête automatiquement le service LRS en cours d’exécution. Dans ce cas, la migration ne peut pas reprendre. Elle doit être redémarrée à partir de zéro. 

## <a name="monitor-the-migration-progress"></a>Superviser la progression de la migration

Pour superviser la progression de l’opération de migration, utilisez la commande PowerShell suivante :

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Pour superviser la progression de l’opération de migration, utilisez la commande CLI suivante :

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Arrêter la migration

Si vous devez arrêter la migration, utilisez les cmdlets suivantes. L’arrêt de la migration entraîne la suppression de la base de données en cours de restauration sur SQL Managed Instance. Donc, il n’est pas possible de reprendre la migration.

Pour arrêter\abandonner le processus de migration, utilisez la commande PowerShell suivante :

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Pour arrêter\abandonner le processus de migration, utilisez la commande CLI suivante :

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Terminer la migration (mode continu)

Dans le cas où le service LRS est démarré en mode continu, une fois que vous avez vérifié que toutes les sauvegardes ont été restaurées, le fait de lancer le basculement termine la migration. Une fois le basculement terminé, la base de données est migrée et prête à des fins de lecture et d’écriture.

Pour terminer le processus de migration en mode continu du service LRS, utilisez la commande PowerShell suivante :

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Pour terminer le processus de migration en mode continu du service LRS, utilisez la commande CLI suivante :

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>Limitations fonctionnelles

Les limitations fonctionnelles du service LRS sont les suivantes :
- La base de données en cours de restauration ne peut pas être utilisée pour l’accès en lecture seule pendant le processus de migration.
- Les correctifs logiciels gérés par le système sont bloqués pendant 47 heures après le démarrage du service LRS. Au-delà de ce délai, la mise à jour logicielle suivante arrête le service LRS. Dans ce cas, le service LRS doit être redémarré à partir de zéro.
- Le service LRS requiert que les bases de données sur SQL Server soient sauvegardées avec l’option CHECKSUM activée.
- Le jeton SAS utilisé par le service LRS doit être généré pour l’ensemble du conteneur de Stockage Blob Azure et ne doit avoir que les autorisations Lecture et Liste.
- Les fichiers de sauvegarde de différentes bases de données doivent être placés dans des dossiers distincts dans le Stockage Blob Azure.
- Le service LRS doit être démarré séparément pour chaque base de données qui pointe vers des dossiers distincts contenant des fichiers de sauvegarde dans le Stockage Blob Azure.
- Le service LRS peut prendre en charge jusqu’à 100 processus de restauration simultanés pour une seule instance SQL Managed Instance.

## <a name="troubleshooting"></a>Résolution des problèmes

Une fois que vous avez démarré le service LRS, utilisez les cmdlets de supervision (get-azsqlinstancedatabaselogreplay ou az_sql_midb_log_replay_show) pour afficher l’état de l’opération. Si, après un certain temps, le service LRS ne parvient pas à démarrer et présente une erreur, effectuez une recherche parmi les problèmes les plus courants :
- Existe-t-il déjà une base de données portant le même nom sur SQL MI que vous essayez de migrer à partir de SQL Server ? Résolvez ce conflit en renommant l’une des bases de données.
- La sauvegarde de base de données sur SQL Server a-t-elle été effectuée avec l’option **CHECKSUM** ?
- Les autorisations du jeton SAS sont-elles **Lecture** et **Liste** uniquement pour le service LRS ?
- Le jeton SAS pour le service LRS a-t-il été copié à partir du point d’interrogation « ? » avec un contenu commençant comme celui-ci : « sv=2020-02-10... » ? 
- Le délai de **validité du jeton** SAS est-il correct en fonction de la fenêtre de temps de démarrage et de fin de la migration ? Il peut y avoir des décalages en raison des différents **fuseaux horaires** utilisés pour SQL Managed instance et le jeton SAS. Essayez de regénérer le jeton SAS en étendant la validité de la fenêtre de temps avant et après la date actuelle.
- Le nom de la base de données, le nom du groupe de ressources et le nom de l’instance gérée sont-ils correctement orthographiés ?
- Si le service LRS a été démarré en mode d’autocomplétion, le nom du dernier fichier de sauvegarde spécifié était-il valide ?

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [Migrer SQL Server vers SQL Managed Instance](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- En savoir plus sur [Différences entre SQL Server et Azure SQL Managed Instance](transact-sql-tsql-differences-sql-server.md).
- En savoir plus sur [Meilleures pratiques en termes de coût et de taille des charges de travail migrées vers Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
