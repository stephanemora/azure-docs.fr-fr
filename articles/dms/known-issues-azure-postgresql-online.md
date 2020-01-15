---
title: 'Problèmes connus : Migrations en ligne de PostgreSQL vers Azure Database pour PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Découvrez les problèmes connus et les limitations de migration avec les migrations en ligne de PostgreSQL vers Azure Database pour PostgreSQL – serveur unique – à l’aide d’Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: article
ms.date: 10/27/2019
ms.openlocfilehash: c5c0015c5034dd3b30b716264fd97e9881b3fe67
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437872"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql-single-server"></a>Problèmes connus/limitations de migration dans les migrations en ligne de PostgreSQL vers Azure DB pour PostgreSQL - Serveur unique

Les sections suivantes décrivent les problèmes connus et limitations associés aux migrations en ligne de PostgreSQL vers Azure Database pour PostgreSQL - Serveur unique.

## <a name="online-migration-configuration"></a>Configuration d’une migration en ligne

- Le serveur PostgreSQL source doit exécuter la version 9.5.11, 9.6.7 ou 10.3, ou ultérieure. Pour plus d’informations, consultez l’article [Versions de bases de données PostgreSQL prises en charge](../postgresql/concepts-supported-versions.md).
- Seules les migrations dans la même région sont prises en charge. Par exemple, une migration PostgreSQL 9.5.11 à Azure Database pour PostgreSQL 9.6.7 n’est pas prise en charge.

    > [!NOTE]
    > Pour PostgreSQL version 10, DMS prend uniquement en charge la migration de la version 10.3 vers Azure Database pour PostgreSQL. Nous prévoyons très prochainement de prendre en charge des versions plus récentes de PostgreSQL.

- Pour activer la réplication logique dans le fichier **postgresql.config PostgreSQL source**, définissez les paramètres suivants :
  - **wal_level** = logique
  - **max_replication_slots** = [max number of databases for migration] ; si vous voulez migrer quatre bases de données, définissez la valeur sur 4
  - **max_wal_senders** = [nombre de bases de données exécutées simultanément] ; la valeur recommandée est 10
- Ajouter une adresse IP d’agent DMS au fichier pg_hba.conf PostgreSQL source
  1. Notez l’adresse IP DMS après avoir terminé l’approvisionnement d’une instance de DMS.
  2. Ajoutez l’adresse IP dans le fichier pg_hba.conf comme indiqué :

        host    all     172.16.136.18/10    md5    host    replication postgres    172.16.136.18/10    md5

- L’utilisateur doit disposer de l’autorisation de super utilisateur sur le serveur hébergeant la base de données source
- Hormis ENUM dans le schéma de base de données source, les schémas de bases de données source et cible doivent correspondre.
- Le schéma de la base de données cible dans Azure Database pour PostgreSQL - Serveur unique ne doit pas inclure de clés étrangères. Pour supprimer les clés étrangères, utilisez la requête suivante :

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

- Le schéma de la base de données cible dans Azure Database pour PostgreSQL - Serveur unique ne doit pas inclure de déclencheurs. Utilisez la commande suivante pour désactiver les déclencheurs dans la base de données cible :

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Limitations relatives au type de données

- **Limitation** : Si la base de données PostgreSQL source inclut un type de données ENUM, la migration échoue lors de la synchronisation continue.

    **Solution de contournement** : Remplacez le type de données ENUM par un caractère différent dans Azure Database pour PostgreSQL.

- **Limitation** : En l’absence de clé primaire sur les tables, la synchronisation continue échouera.

    **Solution de contournement** : définissez temporairement une clé primaire pour la table afin de poursuivre la migration. Vous pouvez supprimer la clé primaire à l’issue de la migration de données.

- **Limitation** : Type de données JSONB non pris en charge pour la migration.

## <a name="lob-limitations"></a>Limitations relatives aux objets LOB

Les colonnes LOB (Large Object) peuvent devenir volumineuses. Pour PostgreSQL, les exemples de types de données LOB incluent XML, JSON, IMAGE, TEXTE, etc.

- **Limitation** : Si des types de données LOB sont utilisés comme clés primaires, la migration échouera.

    **Solution de contournement** : remplacez la clé primaire par d’autres types de données ou par des colonnes qui ne sont pas de type LOB.

- **Limitation** : Si la longueur de la colonne LOB (Large Object) dépasse 32 Ko, les données peuvent être tronquées au niveau de la cible. Vous pouvez vérifier la longueur de la colonne LOB à l’aide de cette requête :

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **Solution de contournement** : si vous disposez d’un objet LOB de plus de 32 Ko, contactez l’équipe d’ingénierie en cliquant ici : [Demander à l’équipe de migration de base de données Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

- **Limitation** : Si la table contient des colonnes LOB et si aucune clé primaire n’est définie pour la table, les données de cette table peuvent ne pas être migrées.

    **Solution de contournement** : Définissez temporairement une clé primaire pour la table afin de continuer la migration. Vous pouvez supprimer la clé primaire à l’issue de la migration de données.

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

  > [!NOTE]
  > Dans le script précédent, « PG_User » fait référence au nom d’utilisateur utilisé pour se connecter à la source de migration.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Limitations lors de la migration en ligne depuis AWS RDS PostgreSQL

Lorsque vous essayez d’effectuer une migration en ligne depuis AWS RDS PostgreSQL vers Azure Database pour PostgreSQL, vous pouvez rencontrer les erreurs suivantes.

- **Erreur** : La valeur par défaut de la colonne '{colonne}' dans la table '{table}' de la base de données '{base de données}' est différente sur les serveurs source et cible. Elle est '{valeur sur la source}' sur la source et '{valeur sur la cible}' sur la cible.

  **Limitation** : Cette erreur se produit quand la valeur par défaut sur un schéma de colonne est différente entre les bases de données source et cible.
  **Solution de contournement** : Vérifiez que le schéma sur la cible correspond au schéma sur la source. Pour en savoir plus sur la migration de schéma, reportez-vous à la [documentation sur la migration en ligne Azure PostgreSQL](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

- **Erreur** : La base de données cible '{base de données}' a '{nombre de tables}' tables alors que la base de données source '{base de données}' en a '{nombre de tables}'. Les bases de données source et cible doivent avoir le même nombre de tables.

  **Limitation** : Cette erreur se produit quand le nombre de tables est différent entre les bases de données source et cible.
  **Solution de contournement** : Vérifiez que le schéma sur la cible correspond au schéma sur la source. Pour en savoir plus sur la migration de schéma, reportez-vous à la [documentation sur la migration en ligne Azure PostgreSQL](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

- **Erreur :** La base de données source {database} est vide.

  **Limitation** : Cette erreur se produit quand la base de données source est vide. Il est fort probable que vous ayez sélectionné la mauvaise base de données comme source.
  **Solution de contournement** : Revérifiez la base de données source que vous avez sélectionnée pour la migration, puis réessayez.

- **Erreur :** La base de données cible {database} est vide. Migrez le schéma.

  **Limitation** : Cette erreur se produit quand il n’y a pas de schéma dans la base de données cible. Vérifiez que le schéma sur la cible correspond au schéma sur la source.
  **Solution de contournement** : Vérifiez que le schéma sur la cible correspond au schéma sur la source. Pour en savoir plus sur la migration de schéma, reportez-vous à la [documentation sur la migration en ligne Azure PostgreSQL](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

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
