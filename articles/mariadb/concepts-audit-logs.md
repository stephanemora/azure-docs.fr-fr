---
title: Journaux d’audit - Azure Database for MariaDB
description: Décrit les journaux d’audit disponibles dans Azure Database for MariaDB et les paramètres disponibles pour l’activation de différents niveaux de journalisation.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 9c5f6aa2900570aa00ddbc50ec8be4dbb0d16a34
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978047"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Journaux d’audit dans Azure Database for MariaDB

Dans Azure Database for MariaDB, le journal d’audit est accessible aux utilisateurs. Le journal d’audit peut être utilisé pour suivre l’activité au niveau de la base de données et est couramment utilisé à des fins de conformité.

> [!IMPORTANT]
> Pour l’instant, la fonctionnalité Journal d’audit n’existe qu’en préversion.

## <a name="configure-audit-logging"></a>Configurer l’enregistrement d’audit

Par défaut, le journal d’audit est désactivé. Pour l’activer, affectez la valeur ON à `audit_log_enabled`.

Les autres paramètres que vous pouvez ajuster incluent :

- `audit_log_events` : contrôle les événements à enregistrer. Consultez le tableau ci-dessous pour des événements d’audit spécifiques.
- `audit_log_include_users`: Utilisateurs MariaDB à inclure pour la journalisation. La valeur par défaut de ce paramètre est vide, ce qui inclut tous les utilisateurs pour la journalisation. Il a une plus grande priorité que `audit_log_exclude_users`. La longueur maximale du paramètre est de 512 caractères.
> [!Note]
> `audit_log_include_users` a une priorité plus élevée que `audit_log_exclude_users`. Par exemple, si `audit_log_include_users` = `demouser` et `audit_log_exclude_users` = `demouser`, l’utilisateur sera inclus dans les journaux d’audit, car `audit_log_include_users` a une priorité plus élevée.
- `audit_log_exclude_users`: utilisateurs MariaDB à exclure de l’enregistrement. Quatre utilisateurs maximum sont autorisés. La longueur maximale du paramètre est de 256 caractères.

| **Event** | **Description** |
|---|---|
| `CONNECTION` | - Lancement de la connexion (réussite ou échec) <br> - Réauthentification d’utilisateur avec un autre utilisateur/mot de passe en cours de session <br> - Arrêt de la connexion |
| `DML_SELECT`| Requêtes SELECT |
| `DML_NONSELECT` | Requêtes INSERT/DELETE/UPDATE |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Requêtes telles que « DROP DATABASE » |
| `DCL` | Requêtes telles que « GRANT PERMISSION » |
| `ADMIN` | Requêtes telles que « SHOW STATUS » |
| `GENERAL` | Tout dans DML_SELECT, DML_NONSELECT, DML, DDL, DCL et ADMIN |

## <a name="access-audit-logs"></a>Accéder aux journaux d’audit

Les journaux d’audit sont intégrés aux journaux de diagnostic Azure Monitor. Une fois que vous avez activé les journaux d’audit sur votre serveur MariaDB, vous pouvez les transmettre vers des journaux Azure Monitor, des hubs d’événements ou le Stockage Azure. Pour en savoir plus sur l’activation des journaux de diagnostic dans le portail Azure, consultez l’[article sur le portail de journal d’audit](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Schémas des journaux de diagnostic

Les sections suivantes décrivent la sortie des journaux d’audit MariaDB en fonction du type d’événement. En fonction de la méthode de sortie, les champs inclus et l’ordre dans lequel ils apparaissent peuvent varier.

### <a name="connection"></a>Connexion

| **Propriété** | **Description** |
|---|---|
| `TenantId` | Votre ID d’abonné |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Horodatage du moment où le journal a été enregistré en UTC |
| `Type` | Type de journal. Toujours `AzureDiagnostics` |
| `SubscriptionId` | GUID de l’abonnement auquel appartient le serveur |
| `ResourceGroup` | Nom du groupe de ressources auquel le serveur appartient |
| `ResourceProvider` | Nom du fournisseur de ressources. Toujours `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de ressource |
| `Resource` | Nom du serveur |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | ID de connexion unique généré par MariaDB |
| `host_s` | Vide |
| `ip_s` | Adresse IP du client qui se connecte à MariaDB |
| `user_s` | Nom de l’utilisateur qui exécute la requête |
| `db_s` | Nom de la base de données connectée |
| `\_ResourceId` | URI de ressource |

### <a name="general"></a>Généralités

Le schéma ci-dessous s’applique aux types d’événements GENERAL, DML_SELECT, DML_NONSELECT, DML, DDL, DCL et ADMIN.

| **Propriété** | **Description** |
|---|---|
| `TenantId` | Votre ID d’abonné |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Horodatage du moment où le journal a été enregistré en UTC |
| `Type` | Type de journal. Toujours `AzureDiagnostics` |
| `SubscriptionId` | GUID de l’abonnement auquel appartient le serveur |
| `ResourceGroup` | Nom du groupe de ressources auquel le serveur appartient |
| `ResourceProvider` | Nom du fournisseur de ressources. Toujours `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de ressource |
| `Resource` | Nom du serveur |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nom du serveur |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Secondes de démarrage de requête en horodatage UNIX |
| `error_code_d` | Code d’erreur en cas d’échec de la requête. `0` signifie aucune erreur |
| `thread_id_d` | ID du thread qui a exécuté la requête |
| `host_s` | Vide |
| `ip_s` | Adresse IP du client qui se connecte à MariaDB |
| `user_s` | Nom de l’utilisateur qui exécute la requête |
| `sql_text_s` | Texte de la requête complète |
| `\_ResourceId` | URI de ressource |

### <a name="table-access"></a>Accès à la table

| **Propriété** | **Description** |
|---|---|
| `TenantId` | Votre ID d’abonné |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Horodatage du moment où le journal a été enregistré en UTC |
| `Type` | Type de journal. Toujours `AzureDiagnostics` |
| `SubscriptionId` | GUID de l’abonnement auquel appartient le serveur |
| `ResourceGroup` | Nom du groupe de ressources auquel le serveur appartient |
| `ResourceProvider` | Nom du fournisseur de ressources. Toujours `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de ressource |
| `Resource` | Nom du serveur |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nom du serveur |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE` ou `DELETE` |
| `connection_id_d` | ID de connexion unique généré par MariaDB |
| `db_s` | Nom de la base de données sollicitée |
| `table_s` | Nom de la table sollicitée |
| `sql_text_s` | Texte de la requête complète |
| `\_ResourceId` | URI de ressource |

## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour configurer les journaux d’audit dans le portail Azure](howto-configure-audit-logs-portal.md)