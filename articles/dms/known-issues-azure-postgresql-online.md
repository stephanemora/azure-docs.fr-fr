---
title: Article sur les problèmes connus/limitations de migration dans le cadre des migrations en ligne vers Azure Database pour MySQL | Microsoft Docs
description: Apprenez-en plus sur les problèmes connus/limitations de migration dans le cadre des migrations en ligne vers Azure Database pour MySQL.
services: database-migration
author: HJToland3
ms.author: scphang
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/22/2018
ms.openlocfilehash: b83c889e72acb320c308c3ad5ee6243e715fd523
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52282874"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-postgresql"></a>Problèmes connus/limitations de migration dans le cadre des migrations en ligne vers Azure DB pour PostgreSQL

Les sections suivantes décrivent les problèmes connus et limitations associés aux migrations en ligne de PostgreSQL vers Azure Database pour PostgreSQL. 

## <a name="online-migration-configuration"></a>Configuration d’une migration en ligne
- Le serveur PostgreSQL source doit exécuter la version 9.5.11, 9.6.7 ou 10.3, ou une version ultérieure. Pour plus d’informations, consultez l’article [Versions de bases de données PostgreSQL prises en charge](../postgresql/concepts-supported-versions.md).
- Seules les migrations dans la même région sont prises en charge. Par exemple, une migration PostgreSQL 9.5.11 à Azure Database pour PostgreSQL 9.6.7 n’est pas prise en charge.
- Pour activer la réplication logique dans le fichier **postgresql.config PostgreSQL source**, définissez les paramètres suivants :
    - **wal_level** = logique
    - **max_replication_slots** = [nombre maximum de bases de données pour la migration] ; si vous souhaitez migrer 4 bases de données, définissez la valeur sur 4
    - **max_wal_senders** = [nombre de bases de données exécutées simultanément] ; la valeur recommandée est 10
- Ajouter une adresse IP d’agent DMS au fichier pg_hba.conf PostgresSQL source
    1. Notez l’adresse IP DMS après avoir terminé l’approvisionnement d’une instance de DMS.
    2. Ajoutez l’adresse IP dans le fichier pg_hba.conf comme indiqué :

        host    all     172.16.136.18/10    md5  host    replication postgres    172.16.136.18/10    md5

- L’utilisateur doit disposer de l’autorisation de super utilisateur sur le serveur hébergeant la base de données source
- Hormis ENUM dans le schéma de base de données source, les schémas de bases de données source et cible doivent correspondre.
- Le schéma de la base de données cible dans Azure Database pour PostgreSQL ne doit pas inclure de clés étrangères. Pour supprimer les clés étrangères, utilisez la requête suivante :

    ```
                                SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    Exécutez la clé étrangère Drop (deuxième colonne) dans le résultat de la requête.

- Le schéma de la base de données cible dans Azure Database pour PostgreSQL ne doit pas inclure de déclencheurs. Utilisez la commande suivante pour désactiver les déclencheurs dans la base de données cible :

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Limitations relatives au type de données

- **Limitation** : si la base de données PostgreSQL source inclut un type de données ENUM, la migration échoue lors de la synchronisation continue.

    **Solution de contournement** : remplacez le type de données ENUM par un caractère différent dans Azure Database pour PostgreSQL.

- **Limitation** : en l’absence de clé primaire sur les tables, la synchronisation continue échouera.

    **Solution de contournement** : définissez temporairement une clé primaire pour la table afin de continuer la migration. Vous pouvez supprimer la clé primaire à l’issue de la migration de données.

## <a name="lob-limitations"></a>Limitations relatives aux objets LOB
Les colonnes LOB (Large Object) peuvent devenir volumineuses. Pour PostgreSQL, les exemples de types de données LOB incluent XML, JSON, IMAGE, TEXTE, etc.

- **Limitation** : si des types de données LOB sont utilisés comme clés primaires, la migration échouera.

    **Solution de contournement** : remplacez la clé primaire par d’autres types de données ou par des colonnes qui ne sont pas de type LOB.

- **Limitation** : si la longueur de la colonne LOB (Large Object) dépasse 32 Ko, les données peuvent être tronquées au niveau de la cible. Vous pouvez vérifier la longueur de la colonne LOB à l’aide de cette requête :

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **Solution de contournement** : si vous disposez d’un objet LOB de plus de 32 Ko, contactez l’équipe d’ingénierie à l’adresse [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com).

- **Limitation** : si la table contient des colonnes LOB et si aucune clé primaire n’est définie pour la table, les données de cette table peuvent ne pas être migrées.

    **Solution de contournement** : définissez temporairement une clé primaire pour la table afin de continuer la migration. Vous pouvez supprimer la clé primaire à l’issue de la migration de données.

## <a name="postgresql10-workaround"></a>Solution de contournement PostgreSQL10
PostgreSQL 10.x apporte différentes modifications de noms de dossiers pg_xlog, la migration ne fonctionne donc pas comme prévu. Si vous effectuez une migration depuis PostgreSQL 10.x vers Azure Database pour PostgreSQL 10.3, exécutez le script suivant sur la base de données PostgreSQL source pour créer la fonction wrapper autour des fonctions pg_xlog.

```
BEGIN;
CREATE SCHEMA IF NOT EXISTS fnRenames;
CREATE OR REPLACE FUNCTION fnRenames.pg_switch_xlog() RETURNS pg_lsn AS $$ 
   SELECT pg_switch_wal(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_pause() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_pause(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_resume() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_resume(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_is_xlog_replay_paused() RETURNS boolean AS $$ 
   SELECT pg_is_wal_replay_paused(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name(lsn pg_lsn) RETURNS TEXT AS $$ 
   SELECT pg_walfile_name(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_replay_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_replay_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_receive_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_receive_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_flush_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_flush_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_insert_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_insert_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_location_diff(lsn1 pg_lsn, lsn2 pg_lsn) RETURNS NUMERIC AS $$ 
   SELECT pg_wal_lsn_diff(lsn1, lsn2); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name_offset(lsn pg_lsn, OUT TEXT, OUT INTEGER) AS $$ 
   SELECT pg_walfile_name_offset(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_create_logical_replication_slot(slot_name name, plugin name, 
   temporary BOOLEAN DEFAULT FALSE, OUT slot_name name, OUT xlog_position pg_lsn) RETURNS RECORD AS $$ 
   SELECT slot_name::NAME, lsn::pg_lsn FROM pg_catalog.pg_create_logical_replication_slot(slot_name, plugin, 
   temporary); $$ LANGUAGE SQL;
ALTER USER PG_User SET search_path = fnRenames, pg_catalog, "$user", public;

-- DROP SCHEMA fnRenames CASCADE;
-- ALTER USER PG_User SET search_path TO DEFAULT;
COMMIT;
```

## <a name="other-limitations"></a>Autres limitations
- Le nom de la base de données ne peut pas contenir un point-virgule (;).
- Une chaîne de mot de passe contenant des accolades ouvrantes et fermantes { } n’est pas prise en charge. Cette limitation s’applique à la connexion à la base de données PostgreSQL source et à la base de données cible dans Azure Database pour PostgreSQL.
- Une table capturée doit avoir une clé primaire. Si une table n’a pas de clé primaire, le résultat des opérations d’enregistrement DELETE et UPDATE est imprévisible.
- La mise à jour d’un segment de clé primaire est ignorée. Dans ce cas, l’application d’une telle mise à jour sera identifiée par la cible comme une mise à jour n’ayant mis à jour aucune ligne et un enregistrement sera écran dans la table des exceptions.
- Le comportement d’une migration de plusieurs tables portant le même nom mais selon une casse différente (par exemple, table1, TABLE1 et Table1) peut-être imprévisible, elle n’est donc pas prise en charge.
- Le traitement des modifications de DDL de table [CREATE | ALTER | DROP] est possible, sauf si elles se trouvent dans un bloc de corps de fonction/procédure interne ou dans d’autres constructions imbriquées. Par exemple, la modification suivante n’est pas capturée :

    ```
    CREATE OR REPLACE FUNCTION pg.create_distributors1() RETURNS void
    LANGUAGE plpgsql
    AS $$
    BEGIN
    create table pg.distributors1(did serial PRIMARY KEY,name varchar(40)
    NOT NULL);
    END;
    $$;
    ```

- Le traitement des modifications (synchronisation continue) d’opérations TRUNCATE n’est pas pris en charge. La migration de tables partitionnées n’est pas prise en charge. Lorsqu’une table partitionnée est détectée, les événements suivants se produisent :
    - La base de données signale une liste de tables parent et enfant.
    - La table est créée sur la cible comme une table normale avec les mêmes propriétés que les tables sélectionnées.
    - Si la table parent dans la base de données source a la même valeur de clé primaire que ses tables enfants, une erreur « clé en double » est renvoyée.
- Dans DMS, le nombre de bases de données pouvant migrer dans le cadre d’une activité de migration unique est limité à quatre.