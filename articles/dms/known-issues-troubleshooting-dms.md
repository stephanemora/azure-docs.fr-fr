---
title: Article sur connus dépanner les problèmes/problèmes courants liés à l’utilisation d’Azure Database Migration Service | Microsoft Docs
description: Découvrez comment résoudre les problèmes courants connus problèmes/erreurs associées à l’aide d’Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: dc8ba315d08f3a130ff0adf91afc90f545baf4e4
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604425"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Résoudre les erreurs et problèmes d’Azure Database Migration Service courants

Cet article décrit certains problèmes courants et les erreurs que les utilisateurs d’Azure Database Migration Service peuvent rencontrer. L’article inclut également des informations sur la façon de résoudre ces problèmes et des erreurs.

## <a name="migration-activity-in-queued-state"></a>Activité de migration en file d’attente

Lorsque vous créez de nouvelles activités dans un projet de Service de Migration de base de données Azure, les activités restent dans un état en file d’attente.

| Cause :         | Résolution : |
| ------------- | ------------- |
| Ce problème se produit lorsque l’instance Azure Database Migration Service a atteint la capacité maximale pour les tâches en cours qui exécutent simultanément. Toute nouvelle activité est en file d’attente jusqu'à ce que la capacité est disponible. | Valider le Data Migration Service instance possède des activités en cours d’exécution sur plusieurs projets. Vous pouvez continuer à créer des activités qui sont automatiquement ajoutées à la file d’attente pour l’exécution. Dès que les activités en cours d’exécution existantes est terminé, l’activité en file d’attente suivante commence à s’exécuter et l’état passe à l’état d’exécution automatiquement. Vous n’avez pas besoin de démarrer la migration de l’activité en file d’attente aucune action supplémentaire.<br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Nombre maximal de bases de données sélectionnées pour la migration

L’erreur suivante se produit lors de la création d’une activité pour un projet de migration de base de données de déplacement vers Azure SQL Database ou Azure SQL Database managed instance :

* **Erreur** : Erreur de validation de paramètres de migration », « errorDetail » : « objets nombre max de plus de '4' de 'Databases' a été sélectionné pour la migration. »

| Cause :         | Résolution : |
| ------------- | ------------- |
| Cette erreur s’affiche lorsque vous avez sélectionné plus de quatre bases de données pour une activité de migration unique. À l’heure actuelle, chaque activité de migration est limitée à quatre bases de données. | Sélectionnez moins de quatre bases de données par l’activité de migration. Si vous avez besoin migrer plus de quatre bases de données en parallèle, configurez une autre instance du Service de Migration de base de données Azure. Actuellement, chaque abonnement prend en charge jusqu'à deux instances de Service de Migration de base de données Azure.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Erreurs pour la migration de MySQL vers Azure pour MySQL avec des erreurs de récupération

Lorsque vous migrez à partir de MySQL vers Azure Database pour MySQL à l’aide d’Azure Database Migration Service, l’activité de migration échoue avec l’erreur suivante :

* **Erreur** : Erreur de migration de base de données - tâches 'TaskID' a été suspendue en raison d’échecs de récupération successives [n].

| Cause :         | Résolution : |
| ------------- | ------------- |
| Cette erreur peut se produire lorsque l’utilisateur qui effectue la migration n’a pas le rôle ReplicationAdmin et/ou des privilèges de CLIENT de la réplication, RÉPLICA de la réplication et SUPER (versions antérieures à MySQL 5.6.6).<br> <br><br><br> <br> <br> <br> <br> <br> <br> | Assurez-vous que le [des privilèges requis](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) pour l’utilisateur compte sont correctement configurés sur la base de données Azure pour l’instance MySQL. Par exemple, les étapes suivantes peuvent être suivis pour créer un utilisateur nommé 'migrateuser' avec des privilèges requis :<br>1. CRÉER un utilisateur migrateuser@'%' identifié par 'secret' ; <br>2. Accorder tous les privilèges sur db_name.* à 'migrateuser'@'%' identifié par 'secret' ; Répétez cette étape pour accorder l’accès sur plusieurs bases de données <br>3. Subordonné de réplication GRANT sur *.* à « migrateuser'@'%' identifié par 'secret' ;<br>4. Client de réplication GRANT sur *.* à « migrateuser'@'%' identifié par 'secret' ;<br>5. Vider les privilèges ; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Erreur lors de la tentative d’arrêt du Service de Migration de base de données Azure

Vous recevez après une erreur lors de l’arrêt de l’instance de Service de Migration de base de données Azure :

* **Erreur** : Échec du service à arrêter. Erreur : {« erreur » : {'code' : 'InvalidRequest', 'message' :' une ou plusieurs activités sont en cours d’exécution. Pour arrêter le service, patientez jusqu'à ce que les activités ou arrêtez-les manuellement, puis réessayez. »}}

| Cause :         | Résolution : |
| ------------- | ------------- |
| Cette erreur s’affiche lorsque vous tentez d’arrêter l’instance de service inclut des activités qui sont en cours d’exécution ou présentent dans les projets de migration. <br><br><br><br><br><br> | Assurez-vous qu’il n’y a aucune activité en cours d’exécution dans l’instance du Service de Migration de base de données Azure que vous tentez d’arrêter. Vous pouvez également supprimer les activités ou les projets avant de tenter d’arrêter le service. Les étapes suivantes expliquent comment supprimer des projets pour nettoyer l’instance de service de migration en supprimant toutes les tâches en cours d’exécution :<br>1. Install-Module -Name AzureRM.DataMigration <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription -SubscriptionName "<subName>" <br> 4. Remove-AzureRmDataMigrationProject -Name <projectName> -ResourceGroupName <rgName> -ServiceName <serviceName> -DeleteRunningTask |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Erreur de restauration de base de données tandis que SQL Database migration vers Azure SQL DB managed instance

Lorsque vous effectuez une migration en ligne de SQL Server vers une Azure SQL Database managed instance, le basculement échoue avec l’erreur suivante :

* **Erreur** : Opération de restauration a échoué pour l’Id 'operationId' de l’opération. Code « AuthorizationFailed », le Message « le client « clientId » avec l’id d’objet « objectId » n’a pas autorisé à effectuer l’action « Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read » sur l’étendue ' /subscriptions/ ID d’abonnement '.'.

| Cause :         | Résolution :    |
| ------------- | ------------- |
| Cette erreur indique que le principal d’application utilisé pour la migration en ligne de SQL Server vers une Azure SQL Database managed instance ont ne contribue pas l’autorisation sur l’abonnement. Certains appels d’API avec Managed Instance à l’heure actuelle nécessitent cette autorisation sur l’abonnement pour l’opération de restauration. <br><br><br><br><br><br><br><br><br><br> | Utilisez le `Get-AzureADServicePrincipal` applet de commande PowerShell avec `-ObjectId` disponible à partir du message d’erreur pour répertorier le nom complet de l’ID d’application utilisé.<br><br> Valider les autorisations pour cette application et vérifiez qu’il dispose du [rôle Collaborateur](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) au niveau de l’abonnement. <br><br> L’utilisation de l’équipe Azure Database Migration Service ingénierie pour restreindre les accès à partir de l’actuel contribuer rôle sur l’abonnement. Si vous avez un besoin de l’entreprise qui n’autorise pas l’utilisation de contribuer de rôle, contactez le support Azure pour obtenir une aide supplémentaire. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Erreur lors de la suppression de carte réseau associée avec le Service de Migration de base de données Azure

Lorsque vous essayez de supprimer une carte d’Interface réseau associée au Service de Migration de base de données Azure, la tentative de suppression échoue avec l’erreur :

* **Erreur** : Impossible de supprimer la carte réseau associée au Service de Migration de base de données Azure utilisant la carte réseau, le service DMS

| Cause :         | Résolution :    |
| ------------- | ------------- |
| Ce problème se produit lorsque l’instance Azure Database Migration Service peut toujours être présent et l’utilisation de la carte réseau. <br><br><br><br><br><br> | Pour supprimer cette carte réseau, supprimez l’instance de service DMS qui supprime automatiquement la carte réseau utilisée par le service.<br><br> **Important !** Vérifiez que l’instance Azure Database Migration Service en cours de suppression n’a aucune activité en cours d’exécution.<br><br> Une fois que tous les projets et les activités associées à l’instance de Service de Migration de base de données Azure sont supprimées, vous pouvez supprimer l’instance de service. La carte réseau utilisée par l’instance de service est automatiquement nettoyée en tant que partie de la suppression du service. |

## <a name="connection-error-when-using-expressroute"></a>Erreur de connexion lors de l’utilisation d’ExpressRoute

Lorsque vous essayez de vous connecter à la source dans l’Assistant de projet de service de Migration de base de données Azure, la connexion échoue après délai d’attente prolongée si la source est l’utilisation d’ExpressRoute pour la connectivité.

| Cause :         | Résolution :    |
| ------------- | ------------- |
| Lorsque vous utilisez [ExpressRoute](https://azure.microsoft.com/services/expressroute/), Azure Database Migration Service [requiert](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) trois points de terminaison de service sur le sous-réseau de réseau virtuel associé au service d’approvisionnement :<br> --Point de terminaison Service Bus<br> : Point de terminaison de stockage<br> --Cible de point de terminaison de base de données (par exemple, le point de terminaison SQL, le point de terminaison Cosmos DB)<br><br><br><br> | [Activer](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) les points de terminaison de service requis pour la connectivité ExpressRoute entre la source et Azure Database Migration Service. <br><br><br><br><br><br><br><br> |

## <a name="timeout-error-when-migrating-a-mysql-database-to-azure-mysql"></a>Erreur de délai d’attente lorsque vous migrez une base de données MySQL vers Azure MySQL

Lorsque vous migrez une base de données MySQL vers une base de données Azure pour l’instance MySQL via le Service de Migration de base de données Azure, la migration échoue avec l’erreur de délai d’expiration suivante :

* **Erreur** : Erreur de migration de base de données - échoué du chargement du fichier - Échec du démarrage des processus de chargement de fichier « n » et RETCODE contient : SQL_ERROR SqlState : HY000 NativeError : Message de 1205 : attente de verrouillage [MySQL] [pilote ODBC] [mysqld] délai d’expiration dépassé ; Essayez de redémarrer la transaction

| Cause :         | Résolution :    |
| ------------- | ------------- |
| Cette erreur se produit lors de la migration échoue en raison du délai d’attente de verrou pendant la migration.<br><br> | Envisagez d’augmenter la valeur du paramètre de serveur **'innodb_lock_wait_timeout'**. La valeur autorisée la plus élevée est 1073741824. |

## <a name="additional-known-issues"></a>Autres problèmes connus

* [Limitations de migration ou problèmes connus avec les migrations en ligne à la base de données SQL Azure](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Limitations de migration ou problèmes connus avec les migrations en ligne à la base de données Azure pour MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Limitations de migration ou problèmes connus avec les migrations en ligne vers Azure Database pour PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Étapes suivantes

* Afficher l’article [PowerShell pour Azure Database Migration Service](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Afficher l’article [comment configurer les paramètres du serveur dans Azure Database pour MySQL à l’aide du portail Azure](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Afficher l’article [vue d’ensemble des composants requis pour utiliser Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs).
* Consultez le [Forum aux questions sur l’utilisation d’Azure Database Migration Service](https://docs.microsoft.com/azure/dms/faq).
