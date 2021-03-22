---
title: 'API REST : synchronisation entre plusieurs bases de données'
description: Utilisez un exemple de script d’API REST pour effectuer une synchronisation entre plusieurs bases de données.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: REST API
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: ef2823b870f76922dd0dc157341aea9b502fb4bb
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564557"
---
# <a name="use-rest-api-to-sync-data-between-multiple-databases"></a>Utiliser une API REST pour synchroniser des données entre plusieurs bases de données 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Cet exemple d’API REST configure SQL Data Sync pour la synchronisation des données entre plusieurs bases de données.

Pour obtenir une vue d’ensemble de SQL Data Sync, consultez [Synchroniser des données entre plusieurs bases de données locales et cloud avec SQL Data Sync dans Azure](../sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> Pour le moment, SQL Data Sync ne prend pas en charge Azure SQL Managed Instance.

## <a name="create-sync-group"></a>Créer un groupe de synchronisation

Utilisez le modèle [Créer ou mettre à jour](https://docs.microsoft.com/rest/api/sql/syncgroups/createorupdate) pour créer un groupe de synchronisation.
 
Quand vous créez un groupe de synchronisation, ne transmettez pas le schéma de synchronisation (table\colonne) ni masterSyncMemberName, car à ce moment-là, le groupe de synchronisation n’a pas encore d’informations table\colonne.

Exemple de demande pour la création d’un groupe de synchronisation : 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Exemple de réponse pour la création d’un groupe de synchronisation :

Code d’état : 200
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

Code d’état : 201
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

## <a name="create-sync-member"></a>Créer un membre de synchronisation

Utilisez le modèle [Créer ou mettre à jour](https://docs.microsoft.com/rest/api/sql/syncmembers/createorupdate) pour créer un membre de synchronisation.

Exemple de demande pour la création d’un membre de synchronisation :

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```
Exemple de réponse pour la création d’un membre de synchronisation :

Code d’état : 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Code d’état : 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="refresh-schema"></a>Actualiser le schéma

Une fois que votre groupe de synchronisation est correctement créé, actualisez le schéma à l’aide des modèles suivants.

Utilisez le modèle [Actualiser le schéma du hub](https://docs.microsoft.com/rest/api/sql/syncgroups/refreshhubschema) pour actualiser le schéma de la base de données Hub. 

Exemple de demande pour l’actualisation d’un schéma de base de données Hub : 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/refreshHubSchema?api-version=2015-05-01-preview
```

Exemple de réponse pour l’actualisation d’un schéma de base de données Hub : 

Code d’état : 200

Code d’état : 202

Utilisez le modèle [Lister les schémas du hub](https://docs.microsoft.com/rest/api/sql/syncgroups/listhubschemas) pour lister le schéma de la base de données Hub. 

Utilisez le modèle [Actualiser le schéma du membre](https://docs.microsoft.com/rest/api/sql/syncmembers/refreshmemberschema) pour actualiser le schéma de la base de données membre. 

Utilisez le modèle [Lister le schéma du membre](https://docs.microsoft.com/rest/api/sql/syncmembers/listmemberschemas) pour lister le schéma de la base de données membre. 

Passez à l’étape suivante seulement si votre schéma a été actualisé avec succès. 

## <a name="update-sync-group"></a>Mettre à jour un groupe de synchronisation 

Utilisez le modèle [Créer ou mettre à jour](https://docs.microsoft.com/rest/api/sql/syncgroups/createorupdate) pour mettre à jour votre groupe de synchronisation.

Mettez à jour le groupe de synchronisation en spécifiant le schéma de synchronisation. Incluez votre schéma et masterSyncMemberName, qui est le nom contenant le schéma à utiliser. 

Exemple de demande pour la mise à jour du groupe de synchronisation : 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Exemple de réponse pour la mise à jour du groupe de synchronisation : 

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```
## <a name="update-sync-member"></a>Mettre à jour le membre de synchronisation

Utilisez le modèle [Créer ou mettre à jour](https://docs.microsoft.com/rest/api/sql/syncmembers/createorupdate) pour mettre à jour votre membre de synchronisation.

Exemple de demande pour la mise à jour d’un membre de synchronisation : 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```

Exemple de réponse pour la mise à jour d’un membre de synchronisation : 

Code d’état : 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Code d’état : 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="trigger-sync"></a>Déclencher une synchronisation

Utilisez le modèle [Déclencher une synchronisation](https://docs.microsoft.com/rest/api/sql/syncgroups/triggersync) pour déclencher une opération de synchronisation.

Exemple de demande pour le déclenchement d’une opération de synchronisation : 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/triggerSync?api-version=2015-05-01-preview
```

Exemple de réponse pour le déclenchement d’une opération de synchronisation : 

Code d’état : 200

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation d’Azure PowerShell](/powershell/azure/).

Vous trouverez des exemples supplémentaires de scripts SQL Database PowerShell sur la page [Scripts PowerShell Azure SQL Database](../powershell-script-content-guide.md).

Pour plus d’informations sur SQL Data Sync, consultez :

- Vue d’ensemble - [Synchroniser des données entre plusieurs bases de données cloud et locales avec SQL Data Sync dans Azure](../sql-data-sync-data-sql-server-sql-database.md)
- Configurer Data Sync
    - Utiliser le portail Azure - [Tutoriel : Configurer SQL Data Sync pour synchroniser les données entre Azure SQL Database et SQL Server](../sql-data-sync-sql-server-configure.md)
    - Utiliser PowerShell - [Utiliser PowerShell pour synchroniser des données entre une base de données dans Azure SQL Database et SQL Server](sql-data-sync-sync-data-between-azure-onprem.md)
- Data Sync Agent - [Data Sync Agent pour SQL Data Sync dans Azure](../sql-data-sync-agent-overview.md)
- Bonnes pratiques - [Bonnes pratiques pour SQL Data Sync dans Azure](../sql-data-sync-best-practices.md)
- Supervision – [Superviser SQL Data Sync avec les journaux d’activité Azure Monitor](../monitor-tune-overview.md)
- Résolution des problèmes - [Résoudre les problèmes liés à SQL Data Sync dans Azure](../sql-data-sync-troubleshoot.md)
- Mettre à jour le schéma de synchronisation
    - Utiliser Transact-SQL - [Automatiser la réplication des modifications de schéma dans SQL Data Sync dans Azure](../sql-data-sync-update-sync-schema.md)
    - Utiliser PowerShell - [Utiliser PowerShell pour mettre à jour le schéma de synchronisation dans un groupe de synchronisation existant](update-sync-schema-in-sync-group.md)

Pour plus d’informations sur SQL Database, consultez :

- [Vue d’ensemble de SQL Database](../sql-database-paas-overview.md)
- [Gestion du cycle de vie des bases de données](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
