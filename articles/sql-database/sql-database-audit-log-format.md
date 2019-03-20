---
title: Format du journal d’Audit de base de données SQL | Microsoft Docs
description: Comprendre comment sont structurées les journaux d’audit de base de données SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 0fefe01e413e30e4aa3c1fa90de77cbdece39c38
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58001686"
---
# <a name="sql-database-audit-log-format"></a>Format du journal d’Audit de base de données SQL

[Audit de base de données SQL Azure](sql-database-auditing.md) effectue le suivi des événements de base de données et les écrit dans un audit de journal dans votre compte de stockage Azure, ou les envoie au concentrateur d’événements ou Log Analytique pour traitement en aval et l’analyse.

## <a name="naming-conventions"></a>Conventions d’affectation de noms

### <a name="blob-audit"></a>Audit d’objets BLOB

Les journaux d’audit stockés dans le stockage d’objets Blob sont stockés dans un conteneur nommé `sqldbauditlogs` dans le compte de stockage Azure. La hiérarchie de répertoires dans le conteneur est sous la forme `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`. Le format de nom de fichier Blob est `<CreationTime>_<FileNumberInSession>.xel`, où `CreationTime` est au format UTC `hh_mm_ss_ms` format, et `FileNumberInSession` est en cours d’exécution d’index en cas d’étendues de journaux de session entre plusieurs fichiers Blob.

Par exemple, pour la base de données `Database1` sur `Server1` ce qui suit est un chemin d’accès valide possible :

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

### <a name="event-hub"></a>Event Hub

Événements d’audit sont écrits dans le hub d’événements et d’espace de noms qui a été défini pendant la configuration de l’audit et est capturé dans le corps de [Apache Avro](https://avro.apache.org/) événements et stockées à l’aide de la mise en forme de JSON avec encodage UTF-8. Pour lire les journaux d’audit, vous pouvez utiliser les [outils Avro](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) ou des outils similaires qui traitent ce format.

### <a name="log-analytics"></a>Log Analytics

Événements d’audit sont écrits dans l’espace de travail Analytique de journal définie lors de la configuration de l’audit, à la `AzureDiagnostics` table avec la catégorie `SQLSecurityAuditEvents`. Pour plus d’informations sur le langage et les commandes de recherche Log Analytics, consultez [Référence sur les recherches Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search).

## <a id="subheading-1"></a>Champs de journal d’audit

| Nom (Blob) | Nom (Event Hubs/Log Analytique) | Description | Type d'objet BLOB | Type d’événement Hubs/Log Analytique |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID de l’action | varchar(4) | string |
| action_name | action_name_s | Nom de l’action | S.O. | string |
| additional_information | additional_information_s | Des informations supplémentaires sur l’événement, stockée au format XML | nvarchar(4000) | string |
| affected_rows | affected_rows_d | Nombre de lignes affectées par la requête | bigint | int |
| application_name | application_name_s| Nom de l’application cliente | nvarchar(128) | string |
| audit_schema_version | audit_schema_version_d | Toujours 1 | int | int |
| class_type | class_type_s | Type d’entité pouvant être auditée qui se produit de l’audit sur | varchar(2) | string |
| class_type_desc | class_type_description_s | Description de l’entité pouvant être auditée qui se produit de l’audit sur | S.O. | string |
| client_ip | client_ip_s | Adresse IP de l’application cliente source | nvarchar(128) | string |
| connection_id | S.O. | ID de la connexion dans le serveur | GUID | S.O. |
| data_sensitivity_information | data_sensitivity_information_s | Types d’informations et des étiquettes de sensibilité retournés par la requête auditée, basée sur les colonnes classifiées dans la base de données. En savoir plus sur [découvrir des données Azure SQL Database et classification](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | string |
| database_name | database_name_s | Le contexte de base de données dans laquelle l’action s’est produite. | sysname | string |
| database_principal_id | database_principal_id_d | ID du contexte d’utilisateur de base de données que l’action est effectuée dans | int | int |
| database_principal_name | database_principal_name_s | Nom du contexte d’utilisateur de base de données dans lequel l’action est effectuée. | sysname | string |
| duration_milliseconds | duration_milliseconds_d | Durée d’exécution de requête en millisecondes | bigint | int |
| event_time | event_time_t | Date et heure lorsque l’action pouvant être auditée est déclenchée | datetime2 | Datetime |
| HOST_NAME | S.O. | Nom d’hôte client | string | S.O. |
| is_column_permission | is_column_permission_s | Indicateur précisant s’il s’agit d’une autorisation de niveau colonne. 1 = true, 0 = false | bit | string |
| S.O. | is_server_level_audit_s | Indicateur qui spécifie si cet audit est au niveau du serveur | S.O. | string |
| object_ id | object_id_d | L’ID de l’entité sur laquelle l’audit s’est produite. Cela inclut la : objets server, bases de données, les objets de base de données et objets de schéma. 0 si l’entité est le serveur lui-même ou si l’audit n’est pas effectuée au niveau de l’objet | int | int |
| object_name | object_name_s | Le nom de l’entité sur laquelle l’audit s’est produite. Cela inclut la : objets server, bases de données, les objets de base de données et objets de schéma. 0 si l’entité est le serveur lui-même ou si l’audit n’est pas effectuée au niveau de l’objet | sysname | string |
| permission_bitmask | permission_bitmask_s | Le cas échéant, affiche les autorisations qui ont été accordées, refusées ou révoquées | varbinary(16) | string |
| response_rows | response_rows_d | Nombre de lignes retournées dans le jeu de résultats | bigint | int |
| schema_name | schema_name_s | Le contexte de schéma dans lequel l’action s’est produite. NULL pour les audits qui se produisent en dehors d’un schéma | sysname | string |
| S.O. | securable_class_type_s | Objet sécurisable mappé au class_type audité | S.O. | string |
| sequence_group_id | sequence_group_id_g | Identificateur unique | varbinary | GUID |
| sequence_number | sequence_number_d | Effectue le suivi de la séquence d’enregistrements au sein d’un enregistrement d’audit unique qui était trop grande pour tenir dans le tampon d’écriture pour les audits | int | int |
| server_instance_name | server_instance_name_s | Nom de l’instance de serveur où l’audit s’est produit | sysname | string |
| server_principal_id | server_principal_id_d | ID du contexte de connexion dans lequel l’action est effectuée. | int | int |
| server_principal_name | server_principal_name_s | Connexion actuelle | sysname | string |
| server_principal_sid | server_principal_sid_s | SID de connexion en cours | varbinary | string |
| session_id | session_id_d | ID de la session sur laquelle l’événement s’est produite. | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Principal de serveur pour la session | sysname | string |
| instruction | statement_s | Instruction T-SQL qui a été exécutée (le cas échéant) | nvarchar(4000) | string |
| réussite | succeeded_s | Indique si l’action qui a déclenché l’événement a réussi. Pour les événements autres que la connexion et de traitement par lots, cet argument signale uniquement si la vérification des autorisations a réussi ou a échoué, pas l’opération. 1 = succès, 0 = Échec | bit | string |
| target_database_principal_id | target_database_principal_id_d | Le principal de base de données, l’opération GRANT/DENY/REVOKE est effectuée sur. 0 si non applicable | int | int |
| target_database_principal_name | target_database_principal_name_s | Utilisateur cible de l’action. NULL si non applicable | string | string |
| target_server_principal_id | target_server_principal_id_d | Serveur principal l’opération GRANT/DENY/REVOKE sur lequel est effectuée. Retourne 0 si non applicable | int | int |
| target_server_principal_name | target_server_principal_name_s | Connexion de la cible d’action. NULL si non applicable | sysname | string |
| target_server_principal_sid | target_server_principal_sid_s | SID de connexion cible. NULL si non applicable | varbinary | string |
| transaction_id | transaction_id_d | SQL Server uniquement (à partir de 2016) - 0 pour la base de données SQL Azure | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Id d’événement transmis en tant qu’argument à sp_audit_write défini par l’utilisateur. NULL pour les événements système (valeur par défaut) et différent de zéro pour l’événement défini par l’utilisateur. Pour plus d’informations, consultez [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | Les informations transmises en tant qu’argument au sp_audit_write défini par l’utilisateur. NULL pour les événements système (valeur par défaut) et différent de zéro pour l’événement défini par l’utilisateur. Pour plus d’informations, consultez [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | string |

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [l’audit de base de données SQL Azure](sql-database-auditing.md).