---
title: 'Problèmes connus : Migrations en ligne vers Azure Database pour MySQL'
titleSuffix: Azure Database Migration Service
description: Découvrez les problèmes connus et les limitations de migration avec les migrations en ligne vers Azure Database pour MySQL à l’aide d’Azure Database Migration Service.
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
ms.date: 02/20/2020
ms.openlocfilehash: 8c3de28ea934302086a5b14e61482e6a4ab9a7ca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80235277"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Limitations et problèmes de migration en ligne vers Azure Database pour MySQL avec Azure Database Migration Service

Les sections suivantes décrivent les problèmes connus et limitations associés aux migrations en ligne de MySQL vers Azure Database pour MySQL.

## <a name="online-migration-configuration"></a>Configuration d’une migration en ligne


- La version du serveur MySQL source doit être 5.6.35, 5.7.18 ou une version ultérieure.
- Azure Database pour MySQL prend en charge les produits suivants :
  - MySQL Community Edition
  - Moteur InnoDB
- Migration vers une même version. La migration de MySQL 5.6 vers Azure Database pour MySQL 5.7 n’est pas prise en charge.
- Activez la journalisation binaire dans my.ini (Windows) ou my.cnf (Unix).
  - Pour Server_id, spécifiez un nombre supérieur ou égal à 1, par exemple, Server_id=1 (uniquement pour MySQL 5.6).
  - Définissez log-bin = \<path> (uniquement pour MySQL 5.6).
  - Définissez binlog_format = row.
  - Expire_logs_days = 5 (recommandé, uniquement pour MySQL 5.6)
- L’utilisateur doit avoir le rôle ReplicationAdmin.
- Les classements définis pour la base de données MySQL source sont les mêmes que ceux définis dans la base de données cible dans Azure Database pour MySQL.
- La base de données MySQL source et la base de données cible dans Azure Database pour MySQL doivent partager le même schéma.
- Le schéma de la base de données cible dans Azure Database pour MySQL ne doit pas inclure de clés étrangères. Pour supprimer les clés étrangères, utilisez la requête suivante :
    ```
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    Exécutez la clé étrangère Drop (deuxième colonne) dans le résultat de la requête.
- Le schéma de la base de données cible dans Azure Database pour MySQL ne doit pas inclure de déclencheurs. Pour supprimer les déclencheurs dans la base de données cible :
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Limitations relatives au type de données

- **Limitation** : si la base de données MySQL source inclut un type de données JSON, la migration échouera lors de la synchronisation continue.

    **Solution de contournement** : modifiez le type de données JSON dans la base de données MySQL source en choisissant le texte moyen ou le texte long.

- **Limitation** : En l’absence de clé primaire sur les tables, la synchronisation continue échouera.

    **Solution de contournement** : définissez temporairement une clé primaire pour la table afin de poursuivre la migration. Vous pouvez supprimer la clé primaire à l’issue de la migration de données.

## <a name="lob-limitations"></a>Limitations relatives aux objets LOB

Les colonnes LOB (Large Object) peuvent devenir volumineuses. Pour MySQL, les types de données LOB incluent notamment : texte moyen, texte long, Blob, Mediumblob, Longblob, etc.

- **Limitation** : Si des types de données LOB sont utilisés comme clés primaires, la migration échouera.

    **Solution de contournement** : remplacez la clé primaire par d’autres types de données ou par des colonnes qui ne sont pas de type LOB.

- **Limitation** : Si la longueur de la colonne LOB (Large Object) dépasse 32 Ko, les données peuvent être tronquées au niveau de la cible. Vous pouvez vérifier la longueur de la colonne LOB à l’aide de cette requête :
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Solution de contournement** : si vous disposez d’un objet LOB de plus de 32 Ko, contactez l’équipe d’ingénierie en cliquant ici : [Demander à l’équipe de migration de base de données Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Limitations lors de la migration en ligne depuis AWS RDS MySQL

Lorsque vous essayez d’effectuer une migration en ligne depuis AWS RDS MySQL vers Azure Database pour MySQL, vous pouvez rencontrer les erreurs suivantes.

- **Erreur :** La base de données '{0}' a une ou plusieurs clés étrangères sur la cible. Corrigez la cible et démarrez une nouvelle activité de migration des données. Exécutez le script ci-après sur la cible pour lister la ou les clés étrangères.

  **Limitation** : Si vous avez des clés étrangères dans votre schéma, la charge initiale et la synchronisation continue de la migration échouent.
  **Solution de contournement** : Exécutez le script suivant dans MySQL Workbench pour extraire le script de clé étrangère Drop et ajouter le script de clé étrangère :

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Erreur :** La base de données '{0}' n’existe pas sur le serveur. Le serveur source MySQL fourni respecte la casse. Vérifiez le nom de la base de données.

  **Limitation** : Quand vous migrez une base de données MySQL vers Azure à l’aide de l’interface de ligne de commande (CLI), les utilisateurs peuvent obtenir cette erreur. Le service n’a pas pu localiser la base de données sur le serveur source, ce qui peut être dû au fait que vous avez fourni un nom de base de données incorrect ou que la base de données n’existe pas sur le serveur indiqué. Notez que les noms de base de données respectent la casse.

  **Solution de contournement** : Indiquez le nom exact de la base de données, puis réessayez.

- **Erreur :** Des tables portent le même nom dans la base de données '{database}'. Azure Database pour MySQL ne prend pas en charge les tables respectant la casse.

  **Limitation** : Cette erreur se produit quand vous avez deux tables portant le même nom dans la base de données source. Azure Database pour MySQL ne prend pas en charge les tables respectant la casse.

  **Solution de contournement** : Mettez à jour les noms de table pour qu’ils soient uniques, puis réessayez.

- **Erreur :** La base de données cible {database} est vide. Migrez le schéma.

  **Limitation** : Cette erreur se produit quand la base de données Azure Database pour MySQL cible n’a pas le schéma requis. La migration de schéma est nécessaire pour activer la migration des données vers votre cible.

  **Solution de contournement** : [Migrez le schéma](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema) depuis votre base de données source vers la base de données cible.

## <a name="other-limitations"></a>Autres limitations

- Une chaîne de mot de passe précédée d’une accolade ouvrante et suivie d’une accolade fermante {  } n’est pas prise en charge. Cette limitation s’applique à la connexion à la base de données MySQL source et à la base de données cible dans Azure Database pour MySQL.
- Les DDL suivants ne sont pas pris en charge :
  - Tous les DDL de partition
  - Supprimer une table
  - Changement de nom de table
- L’utilisation de l’instruction *alter table <nom_table> add column <nom_colonne>* pour ajouter des colonnes au début ou au milieu d’une table n’est pas prise en charge. L’instruction *alter table <nom_table> add column <nom_colonne>* ajoute la colonne à la fin de la table.
- Les index créés sur une partie des données de colonne uniquement ne sont pas pris en charge. L’exemple d’instruction suivant crée un index en utilisant une partie des données de colonne uniquement :

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- Dans Azure Database Migration Service, le nombre de bases de données pouvant migrer dans le cadre d’une activité de migration unique est limité à quatre.

- **Erreur :** Taille de ligne trop grande (> 8126). La modification de certaines colonnes en TEXTE ou en objet BLOB peut être utile. Dans le format de ligne actuel, le préfixe BLOB de 0 octet est stocké inline.

  **Limitation** : Cette erreur se produit lorsque vous effectuez une migration vers Azure Database pour MySQL à l’aide du moteur de stockage InnoDB et que la taille de ligne de la table est trop grande (> 8 126 octets).

  **Solution de contournement** : Mettez à jour le schéma de la table dont la taille de ligne est supérieure à 8 126 octets. Nous vous déconseillons de modifier le mode strict, car les données seront tronquées. La modification de page_size n’est pas prise en charge.
