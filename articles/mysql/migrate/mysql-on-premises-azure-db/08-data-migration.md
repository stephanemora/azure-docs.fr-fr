---
title: Guide de migration de MySQL local vers Azure Database pour MySQL – Migration des données
description: Par prudence, avant de mettre à niveau ou de migrer des données, exportez la base de données avant la mise à niveau en utilisant MySQL Workbench ou manuellement via la commande mysqldump.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/11/2021
ms.openlocfilehash: 44b2c8aa22944cdf31c9f111a32856e5095d369d
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112082766"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-data-migration"></a>Guide de migration de MySQL local vers Azure Database pour MySQL – Migration des données

## <a name="prerequisites"></a>Prérequis

[Bases de référence des performances](07-performance-baselines.md)

## <a name="back-up-the-database"></a>Sauvegarder la base de données

Par prudence, avant de mettre à niveau ou de migrer des données, exportez la base de données avant la mise à niveau en utilisant MySQL Workbench ou manuellement via la commande `mysqldump`.

## <a name="offline-vs-online"></a>Hors connexion ou en ligne

Avant de sélectionner un outil de migration, vous devez déterminer si la migration doit être en ligne ou hors connexion.

  - Les **migrations hors connexion** entraînent l’arrêt du système pendant la migration. Cette option garantit qu’aucune transaction ne se produit et que l’état des données correspond exactement à ce qui est attendu une fois restaurées dans Azure.

  - Les **migrations en ligne** migrent les données en quasi-temps réel. Cette option est appropriée quand il y a très peu de temps d’arrêt pour les utilisateurs ou l’application qui consomment la charge de travail de données. Le processus implique la réplication des données en utilisant une méthode de réplication comme `binlog` ou une méthode similaire.

Dans le cas de WWI, leur environnement a des exigences complexes quant au réseau et à la sécurité, qui n’autorisent pas l’application des modifications appropriées pour la connectivité entrante et sortante dans la période de temps de la migration cible. Ces complexités et ces exigences font que l’approche en ligne n’est pas envisagée.

> [!NOTE]
> Pour plus d’informations sur la migration hors connexion et en ligne, consultez les sections Planification et Évaluation.

## <a name="data-drift"></a>Dérive de données

Les stratégies de migration hors connexion peuvent entraîner une dérive des données. Une dérive des données se produit quand les données sources récemment modifiées deviennent non synchronisées avec les données migrées. Dans ce cas, une exportation complète ou une exportation différentielle est nécessaire. Vous pouvez limiter ce problème en arrêtant tout le trafic vers la base de données, puis en effectuant votre exportation. Si l’arrêt de tout le trafic de modification des données n’est pas possible, vous devrez prendre en compte la dérive.

La détermination des modifications peut devenir compliquée si les tables de base de données n’ont pas de colonnes comme une clé primaire numérique, ou bien un type de date de modification et de création, dans chaque table à migrer.

Par exemple, si une clé primaire de type numérique est présente et que la migration effectue l’importation selon l’ordre de tri, il est relativement simple de déterminer où l’importation s’est arrêtée et de la redémarrer à partir de cette position. Si aucune clé numérique n’est présente, il est peut-être possible d’utiliser les dates de modification et de création, puis de réimporter selon l’ordre de tri pour pouvoir redémarrer la migration à partir de la dernière date visible dans la cible.

## <a name="performance-recommendations"></a>Recommandations en matière de performances

### <a name="exporting"></a>Exportation

  - Utilisez un outil d’exportation qui peut s’exécuter en mode multithread, comme mydumper.

  - Quand vous utilisez MySQL 8,0, utilisez des [tables partitionnées](https://dev.mysql.com/doc/refman/8.0/en/partitioning-overview.html) quand c’est approprié pour accroître la rapidité des exportations.

### <a name="importing"></a>Importation en cours

  - Créez des index cluster et des clés primaires après avoir chargé les données. Chargez les données dans l’ordre de la clé primaire, ou dans un autre ordre si la clé primaire est une colonne de date (comme une date de modification ou une date de création) classée par ordre.

  - Créez les index secondaires seulement une fois que les données sont chargées. Créez tous les index secondaires après le chargement.

  - Désactivez les contraintes de clé étrangère avant le chargement. La désactivation des vérifications de clé étrangère offre d’importants gains de performances. Activez les contraintes et vérifiez les données après le chargement pour garantir l’intégrité référentielle.

  - Chargez les données en parallèle. Évitez tout parallélisme excessif, qui pourrait provoquer une contention des ressources et supervisez les ressources en utilisant les métriques disponibles dans le portail Azure.

## <a name="perform-the-migration"></a>Effectuer la migration

  - Sauvegarder la base de données

  - Créer et vérifier la zone d’atterrissage Azure

  - Configurer les paramètres du serveur source

  - Configurer les paramètres du serveur cible

  - Exporter les objets de base de données (schéma, utilisateurs, etc.)

  - Exporter les données

  - Importer les objets de base de données

  - Importer les données

  - Validation

  - Réinitialiser les paramètres du serveur cible

  - Migrer la ou les applications

## <a name="common-steps"></a>Étapes courantes

Quel que soit le chemin emprunté, vous devez effectuer les étapes communes suivantes :

  - Effectuer une mise à niveau vers une version d’Azure MySQL prise en charge

  - Inventorier les objets de base de données

  - Exporter les utilisateurs et les autorisations

## <a name="migrate-to-latest-mysql-version"></a>Migrer vers la dernière version de MySQL

Comme la base de données Conference de WWI s’exécute sous la version 5.5, il est nécessaire d’effectuer une mise à niveau. Le directeur informatique a demandé qu’une mise à niveau vers la dernière version de MySQL (actuellement 8.0) soit effectuée.

La mise à niveau vers la version  8.0 peut être effectuée de deux façons :

  - Sur place

  - Exporter/Importer

Quand vous décidez de procéder à une mise à niveau, il est important d’exécuter l’outil de **vérification des mises à niveau** pour déterminer s’il existe des conflits. Par exemple, lors de la mise à niveau vers MySQL Server 8.0, l’outil recherche s’il y a les conflits suivants :

  - Des noms d’objets de base de données qui sont en conflit avec des mots réservés dans MySQL 8.0

  - L’utilisation du jeu de caractères utf8mb3

  - L’utilisation des attributs de type de longueur ZEROFILL/display

  - Des noms de table qui sont en conflit avec des tables dans 8.0

  - L’utilisation du type « temporel »

  - Des noms de contrainte de clé étrangère de plus de 64 caractères

Si le vérificateur des mises à niveau ne signale aucun problème, il est possible de procéder à une mise à niveau sur place en remplaçant les fichiers binaires de MySQL. Les bases de données pour lesquelles des problèmes ont été relevés doivent être exportées et les problèmes doivent être résolus.

## <a name="wwi-scenario"></a>Scénario WWI

Après avoir réussi la migration de l’instance MySQL vers 8.0, l’équipe de migration WWI a réalisé que le chemin de migration de [Database Migration Service (DMS)](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql?step=1) d’origine ne pouvait plus être utilisé, car l’outil DMS ne prend actuellement en charge que 5.6 et 5.7. DMS nécessitait un accès réseau. L’équipe de migration WWI n’était pas prête à gérer ses problèmes réseau complexes. Ces problèmes liés à l’environnement ont réduit le choix de l’outil de migration à MySQL Workbench.

## <a name="database-objects"></a>Objets de base de données

Comme indiqué dans la section Plans de test, un inventaire des objets de base de données doit être effectué avant et après la migration pour vérifier que vous avez bien tout migré.

Si vous voulez exécuter une procédure stockée pour générer ces informations, vous pouvez quelque chose de similaire à ceci :

```
DELIMITER // 
CREATE PROCEDURE `Migration_PerformInventory`(IN schemaName CHAR(64)) 
BEGIN 

        DECLARE finished INTEGER DEFAULT 0; 
          DECLARE tableName varchar(100) DEFAULT ""; 

        #get all tables 
            DECLARE curTableNames 
                CURSOR FOR 
                    SELECT TABLE_NAME FROM information_schema.tables where TABL
E_SCHEMA = schemaName; 
        
            -- declare NOT FOUND handler 
            DECLARE CONTINUE HANDLER 
                FOR NOT FOUND SET finished = 1; 
        
            DROP TABLE IF EXISTS MIG_INVENTORY; 

                CREATE TABLE MIG_INVENTORY 
                ( 
                      REPORT_TYPE VARCHAR(1000), 
                      OBJECT_NAME VARCHAR(1000), 
                  PARENT_OBJECT_NAME VARCHAR (1000), 
                      OBJECT_TYPE VARCHAR(1000), 
                      COUNT INT
                ) 
                ROW_FORMAT=DYNAMIC, 
                ENGINE='InnoDB';
              INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT
                     'OBJECTCOUNT', 'TABLES', 'TABLES', COUNT(*)
              FROM 
                     information_schema.tables 
                where 
                     TABLE_SCHEMA = schemaName;
                #### Constraints
              INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                      'OBJECTCOUNT', 'STATISTICS', 'STATISTICS', COUNT(*) 
                FROM 
                      information_schema.STATISTICS 
                WHERE 
                      TABLE_SCHEMA = schemaName; 
                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                      'OBJECTCOUNT', 'VIEWS', 'VIEWS', COUNT(*) 
                FROM 
                      information_schema.VIEWS 
                WHERE 
                      ROUTINE_TYPE = 'FUNCTION' and 
                      ROUTINE_SCHEMA = schemaName;

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                      'OBJECTCOUNT', 'PROCEDURES', 'PROCEDURES', COUNT(*) 
                FROM 
                      information_schema.ROUTINES 
                WHERE 
                      ROUTINE_TYPE = 'PROCEDURE' and 
                      ROUTINE_SCHEMA = schemaName; 

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                       'OBJECTCOUNT', 'EVENTS', 'EVENTS', COUNT(*) 
                FROM 
                       information_schema.EVENTS 
                WHERE 
                       EVENT_SCHEMA = schemaName; 

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                       'OBJECTCOUNT', 'USER DEFINED FUNCTIONS', 'USER DEFINED FUNCTIONS'
        , COUNT(*) 
                FROM 
                        mysql.func; 

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                        'OBJECTCOUNT', 'USERS', 'USERS', COUNT(*) 
                FROM 
                        mysql.user 
                WHERE 
                        user <> '' order by user; 

                OPEN curTableNames; 
        
                getTableName: LOOP 
                        FETCH curTableNames INTO tableName; 
                        IF finished = 1 THEN 
                              LEAVE getTableName; 
                        END IF; 

                   SET @s = CONCAT('SELECT COUNT(*) into @TableCount FROM ', schemaName, 
'.', tableName); 
        #SELECT @s; 
            PREPARE stmt FROM @s; 
        EXECUTE stmt;
        INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
            
                SELECT 
                    'TABLECOUNT', tableName, 'TABLECOUNT', @TableCount; 
        DEALLOCATE PREPARE stmt; 
        
     END LOOP getTableName; 
     CLOSE curTableNames; 
          
   SELECT * FROM MIG_INVENTORY; 
END // 

DELIMITER ; 

CALL Migration_PerformInventory('reg_app');
```

  - L’appel de cette procédure sur la base de données source révèle les éléments suivants (sortie tronquée) :

![sortie tronquée](./media/image4.jpg)

  - Le résultat de la procédure sur la base de données cible doit se présenter comme dans l’image ci-dessous une fois la migration terminée. Notez qu’il n’existe pas de fonctions dans la base de la base. Les fonctions ont été éliminées avant la migration.

![Fonctions de base de données](./media/image5.jpg)

## <a name="users-and-permissions"></a>Utilisateurs et autorisations

Pour réussir une migration, vous devez migrer les utilisateurs et les autorisations associés vers l’environnement cible.

Exportez tous les utilisateurs et leurs autorisations en utilisant le script PowerShell suivant :

```
$username = "yourusername"; 
$password = "yourpassword"; 
mysql -u$username -p$password --skip-column-names -A -e"SELECT CONCAT('SHOW G
RANTS FOR ''',user,'''@''',host,''';') FROM mysql.user WHERE user<>''" > Show
Grants.sql; 

$lines = get-content "ShowGrants.sql" 

foreach ($line in $lines) 
{ 
mysql -u$username -p$password --skip-column-names -A -e"$line" >> AllGrants.sql 
}
```

  - Créez un script PowerShell en utilisant PowerShell ISE (reportez-vous au document sur l’installation)

  - Définissez **yourusername** sur root et **yourpassword** sur le mot de passe de l’utilisateur racine.

Vous pouvez ensuite exécuter le script `AllGrants.sql` ciblant la nouvelle base de données Azure pour MySQL :

```
$username = "yourusername"; 
$password = "yourpassword"; 
$server = "serverDNSname"; 
$lines = get-content "AllGrants.sql" 

foreach ($line in $lines)
{ 
mysql -u$username -p$password -h$server --ssl-ca=c:\temp\BaltimoreCyberTrus
tRoot.crt.cer --skip-column-names -A -e"$line" 
}
```

Vous pouvez également créer des utilisateurs dans Azure Database pour MySQL en utilisant PowerShell : /en-us/azure/mysql/howto-create-users

## <a name="execute-migration"></a>Exécuter la migration

Avec les composants de migration de base en place, il est maintenant possible de procéder à la migration des données. Plusieurs outils et méthodes ont été présentés précédemment. Concernant WWI, ils vont utiliser le chemin MySQL Workbench pour exporter les données, puis les importer dans Azure Database pour MySQL.

## <a name="data-migration-checklist"></a>Check-list pour la migration des données

  - Identifier la complexité de l’environnement et si une approche en ligne est faisable.

  - Prendre en compte la dérive des données. L’arrêt du service de base de données peut éliminer une dérive potentielle des données.

  - Configurer les paramètres de la source pour une exportation rapide.

  - Configurer les paramètres de la cible pour une importation rapide.

  - Tester les migrations où la version de la source est différente de celle de la cible.

  - Migrer tous les objets non basés sur des données, comme les noms d’utilisateur et les privilèges.

  - Vérifier que toutes les tâches sont documentées et pointées dans la liste au fil de l’exécution de la migration.  


> [!div class="nextstepaction"]
> [Migration des données avec MySQL Workbench](./09-data-migration-with-mySQL-workbench.md)