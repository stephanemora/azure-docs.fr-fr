---
title: Migrer Amazon RDS pour MySQL vers Azure Database pour MySQL à l’aide de la réplication des données entrantes
description: Cet article explique comment migrer Amazon RDS pour MySQL vers Azure Database pour MySQL à l’aide de la réplication des données entrantes.
author: SudheeshGH
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: 9d425ce352472755729b34b750a19ce0c3e48c0c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362190"
---
# <a name="migrate-amazon-rds-for-mysql-to-azure-database-for-mysql-using-data-in-replication"></a>Migrer Amazon RDS pour MySQL vers Azure Database pour MySQL à l’aide de la réplication des données entrantes

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

> [!NOTE]
> Cet article contient des références au terme *esclave*, un terme que Microsoft n’utilise plus. Lorsque le terme sera supprimé du logiciel, nous le supprimerons de cet article.

Vous pouvez utiliser des méthodes telles que la sauvegarde et la restauration de MySQL, l’exportation et l’importation de MySQL Workbench ou Azure Database Migration Service pour migrer vos bases de données MySQL vers Azure Database pour MySQL. En utilisant une combinaison d’outils open source tels que mysqldump ou mydumper et myloader avec Réplication des données entrantes, vous pouvez migrer vos charges de travail avec un temps d’arrêt minimal.

Réplication des données entrantes est une technique qui réplique les modifications de données du serveur source vers le serveur de destination en fonction de la méthode de position de fichier journal binaire. Dans ce scénario, l’instance MySQL opérant en tant que source (sur laquelle les modifications sont apportées à la base de données) écrit les mises à jour et les modifications en tant qu’*événements* dans le journal binaire. Les informations contenues dans le journal binaire sont stockées dans différents formats de journalisation en fonction des modifications de la base de données en cours d’enregistrement. Les réplicas sont configurés pour lire le journal binaire à partir de la source et pour exécuter les événements du journal binaire sur la base de données locale du réplica.

Si vous configurez [Réplication des données entrantes](../mysql/flexible-server/concepts-data-in-replication.md) pour synchroniser les données d’un serveur MySQL source vers un serveur MySQL cible, vous pouvez effectuer un basculement sélectif de vos applications à partir de la base de données principale (ou base de données source) vers le réplica (ou base de données cible).

Dans ce tutoriel, vous allez apprendre à configurer Réplication des données entrantes entre un serveur source qui exécute Amazon Relational Database Service (RDS) pour MySQL et un serveur cible qui exécute Azure Database pour MySQL.

## <a name="performance-considerations"></a>Considérations relatives aux performances

Avant de commencer ce tutoriel, tenez compte des répercussions sur les performances de l’emplacement et de la capacité de l’ordinateur client que vous allez utiliser pour effectuer l’opération.

### <a name="client-location"></a>Emplacement du client

Effectuez des opérations de sauvegarde ou de restauration à partir d’un ordinateur client qui est lancé dans le même emplacement que le serveur de base de données :

- Pour les serveurs Azure Database pour MySQL, l’ordinateur client doit se trouver dans le même réseau virtuel et dans la même zone de disponibilité que le serveur de base de données cible.
- Pour les instances de base de données Amazon RDS sources, l’instance cliente doit exister dans le même cloud Amazon Virtual Private Cloud et dans la même zone de disponibilité que le serveur de base de données source.
Dans le cas précédent, vous pouvez déplacer les copies de sauvegarde entre des ordinateurs clients à l’aide de protocoles de transfert de fichiers tels que FTP ou SFTP ou les charger vers Stockage Blob Azure. Pour réduire la durée totale de la migration, compressez les fichiers avant de les transférer.

### <a name="client-capacity"></a>Capacité du client

Quel que soit l’emplacement de l’ordinateur client, celui-ci requiert des capacités de calcul, d’E/S et réseau adéquates pour effectuer les opérations demandées. Les recommandations générales sont les suivantes :

- Si la sauvegarde ou la restauration implique le traitement en temps réel des données, par exemple la compression ou la décompression, choisissez une classe d’instance avec au moins un cœur de processeur par thread de vidage ou de restauration.
- Assurez-vous que l’instance cliente dispose d’une bande passante réseau suffisante. Utilisez des types d’instance qui prennent en charge la fonctionnalité de performances réseau accélérées. Pour plus d’informations, consultez la section « Performances réseau accélérées » dans le [guide de mise en réseau de machines virtuelles Azure](../virtual-network/create-vm-accelerated-networking-cli.md).
- Assurez-vous que la couche de stockage de l’ordinateur client offre la capacité de lecture/écriture attendue. Nous vous recommandons d’utiliser une machine virtuelle Azure avec un stockage SSD Premium.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

- Installez [mysqlclient](https://dev.mysql.com/downloads/) sur votre ordinateur client pour créer une copie de sauvegarde, et effectuez une opération de restauration sur votre serveur Azure Database pour MySQL cible.
- Pour les bases de données plus volumineuses, installez [mydumper et myloader](https://centminmod.com/mydumper.html) pour effectuer des sauvegardes et des restaurations parallèles des bases de données.

    > [!NOTE]
    > Mydumper ne peut fonctionner que sur les distributions Linux. Pour plus d’informations, consultez [Comment installer mydumper](https://github.com/maxbube/mydumper#how-to-install-mydumpermyloader).

- Créez une instance de serveur Azure Database pour MySQL qui exécute la version 5.7 ou 8.0.

    > [!IMPORTANT]
    > Si votre cible est un serveur flexible Azure Database pour MySQL avec une haute disponibilité redondante interzone, notez que Réplication des données entrantes n’est pas prise en charge pour cette configuration. Pour une solution de contournement, lors de la création du serveur, configurez la haute disponibilité redondante interzone :
    >
    > 1. Créez le serveur avec la haute disponibilité redondante interzone.
    > 1. Désactivez la haute disponibilité.
    > 1. Suivez l’article pour configurer Réplication des données entrantes.
    > 1. Après le basculement, supprimez la configuration de Réplication des données entrantes.
    > 1. Activez la haute disponibilité.

Assurez-vous que plusieurs paramètres et fonctionnalités sont configurés et configurés correctement, comme décrit :

- Pour des raisons de compatibilité, ayez les serveurs de base de données source et cible sur la même version de MySQL.
- Avoir une clé primaire dans chaque table. L’absence de clés primaires sur les tables peut ralentir le processus de réplication.
- Assurez-vous que le jeu de caractères des bases de données source et cible est identique.
- Définissez le paramètre `wait_timeout` sur un délai raisonnable. Ce délai dépend de la quantité de données ou de la charge de travail que vous souhaitez importer ou migrer.
- Vérifiez que toutes vos tables utilisent InnoDB. Le serveur Azure Database pour MySQL ne prend en charge que le moteur de stockage InnoDB.
- Pour les tables comportant de nombreux index secondaires ou pour les tables volumineuses, les effets de la surcharge des performances sont visibles pendant la restauration. Modifiez les copies de sauvegarde afin que les instructions `CREATE TABLE` n’incluent pas les définitions de clé secondaires. Après avoir importé les données, recréez les index secondaires pour éviter de pénaliser les performances pendant le processus de restauration.

Enfin, pour préparer Réplication des données entrantes :

- Vérifiez que le serveur Azure Database pour MySQL cible peut se connecter au serveur Amazon RDS pour MySQL sur le port 3306.
- Assurez-vous que le serveur source Amazon RDS pour MySQL autorise le trafic entrant et sortant sur le port 3306.
- Assurez-vous de fournir une [connectivité site à site](../vpn-gateway/tutorial-site-to-site-portal.md) à votre serveur source en utilisant [Azure ExpressRoute](../expressroute/expressroute-introduction.md) ou une [passerelle VPN Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md). Pour plus d’informations sur la création d’un réseau virtuel, consultez la [documentation de Réseau virtuel Azure](/azure/virtual-network/). Consultez également les articles de démarrage rapide contenant des détails étape par étape.
- Configurez les groupes de sécurité réseau de votre serveur de base de données source pour autoriser l’adresse IP du serveur Azure Database pour MySQL cible.

> [!IMPORTANT]
> Si l’instance source d’Amazon RDS pour MySQL a GTID_mode défini sur ON, l’instance cible d’Azure Database for MySQL – Serveur flexible doit également avoir GTID_mode défini sur ON.

## <a name="configure-the-target-instance-of-azure-database-for-mysql"></a>Configurer l’instance cible Azure Database pour MySQL

Pour configurer l’instance cible d’Azure Database pour MySQL, qui est la cible de Réplication des données entrantes :

1. Définissez la valeur du paramètre `max_allowed_packet` au maximum de **1073741824**, soit 1 Go. Cette valeur permet d’éviter tout problème de dépassement lié aux longues lignes.
1. Définissez les paramètres `slow_query_log`, `general_log`, `audit_log_enabled` et `query_store_capture_mode` sur **OFF** pendant la migration afin d’éliminer toute surcharge liée à la journalisation des requêtes.
1. Effectuez un scale-up de la taille de calcul du serveur Azure Database pour MySQL cible jusqu’au maximum de 64 vCores. Cette taille fournit davantage de ressources de calcul lorsque vous restaurez la copie de sauvegarde de la base de données à partir du serveur source.

    Vous pouvez toujours mettre à l’échelle le calcul pour répondre aux demandes de votre application une fois la migration terminée.

1. Augmentez la taille du stockage pour obtenir plus d’E/S par seconde pendant la migration ou augmentez le nombre maximal d’E/S par seconde pour la migration.

   > [!NOTE]
   > Le nombre maximal d’IOPS disponibles est déterminé par la taille de calcul. Pour plus d’informations, consultez la section IOPS des [Options de calcul et de stockage dans Azure Database pour MySQL - Serveur flexible](../mysql/flexible-server/concepts-compute-storage.md#iops).

## <a name="configure-the-source-amazon-rds-for-mysql-server"></a>Configurer la source Amazon RDS pour MySQL Server

Pour préparer et configurer le serveur MySQL hébergé dans Amazon RDS, qui est la *source* dans Réplication des données entrantes :

1. Vérifiez que la journalisation binaire est activée sur le serveur Amazon RDS pour MySQL source. Vérifiez que les sauvegardes automatiques sont activées, ou assurez-vous qu’une réplica en lecture existe pour le serveur Amazon RDS pour MySQL source.

1. Assurez-vous que les fichiers journaux binaires sur le serveur source sont conservés jusqu’à ce que les modifications soient appliquées sur l’instance cible d’Azure Database pour MySQL.

    Grâce à Réplication des données entrantes, Azure Database pour MySQL ne gère pas le processus de réplication.

1. Pour vérifier la rétention des journaux binaires sur le serveur Amazon RDS source afin de déterminer le nombre d’heures pendant lesquelles les journaux binaires sont conservés, appelez la procédure stockée `mysql.rds_show_configuration` :

    ```
    mysql> call mysql.rds_show_configuration;
    +------------------------+-------+-----------------------------------------------------------------------------------------------------------+
    | name                   | value | description                                                                                               |
    +------------------------+-------+-----------------------------------------------------------------------------------------------------------+
    | binlog retention hours | 24    | binlog retention hours specifies the duration in hours before binary logs are automatically deleted.      |
    | source delay           | 0     | source delay specifies replication delay in seconds between current instance and its master.              |
    | target delay           | 0     | target delay specifies replication delay in seconds between current instance and its future read-replica. |
    +------------------------+-------            +-----------------------------------------------------------------------------------------------------------+
    3 rows in set (0.00 sec)
    ```
1. Pour configurer la période de rétention des journaux binaires, exécutez la procédure stockée `rds_set_configuration` pour vous assurer que les journaux binaires sont conservés sur le serveur source pendant la durée souhaitée. Par exemple :

    ```
    Mysql> Call mysql.rds_set_configuration(‘binlog retention hours', 96);
    ```

    Si vous créez une copie de sauvegarde, puis effectuez une restauration, la commande précédente vous aide à rattraper rapidement les modifications delta.

   > [!NOTE]
   > Assurez-vous qu’il y a suffisamment d’espace disque pour stocker les journaux binaires sur le serveur source en fonction de la période de rétention définie.

Il existe deux façons de capturer une copie de sauvegarde des données à partir du serveur Amazon RDS pour MySQL source. La première approche consiste à capturer une copie de sauvegarde des données directement à partir du serveur source. L’autre approche consiste à capturer une copie de sauvegarde à partir d’un réplica en lecture Amazon RDS pour MySQL.

- Pour capturer une copie de sauvegarde des données directement à partir du serveur source :

    1. Veillez à arrêter les écritures de l’application pendant quelques minutes pour obtenir une copie de sauvegarde des données cohérente du point de vue transactionnel.

       Vous pouvez également affecter temporairement la valeur **1** au paramètre `read_only` afin que les écritures ne soient pas traitées lorsque vous capturez une copie de sauvegarde des données.

    1. Après avoir arrêté les écritures sur le serveur source, récupérez le nom et le décalage du fichier journal binaire en exécutant la commande `Mysql> Show master status;`.
    1. Enregistrez ces valeurs pour démarrer la réplication à partir de votre serveur Azure Database pour MySQL.
    1. Pour créer une copie de sauvegarde des données, exécutez `mysqldump` à l’aide de la commande suivante :

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

- Si l’arrêt des écritures sur le serveur source n’est pas une option ou si les performances de la sauvegarde des données ne sont pas acceptables sur le serveur source, capturez une copie de sauvegarde sur un serveur réplica :

    1. Créez un réplica en lecture Amazon MySQL avec la même configuration que le serveur source. Ensuite, créez la copie de sauvegarde dans ce réplica.
    1. Laissez le réplica de lecture Amazon RDS pour MySQL rattraper le serveur Amazon RDS pour MySQL source.
    1. Lorsque le décalage du réplica atteint **0** sur le réplica en lecture, arrêtez la réplication en appelant la procédure stockée `mysql.rds_stop_replication`.

        ```
        Mysql> call mysql.rds_stop_replication;
        ```

    1. Une fois la réplication arrêtée, connectez-vous au réplica. Exécutez ensuite la commande `SHOW SLAVE STATUS` pour récupérer le nom actuel du fichier journal binaire dans le champ **Relay_Master_Log_File** et la position du fichier journal à partir du champ **Exec_Master_Log_Pos**.
    1. Enregistrez ces valeurs pour démarrer la réplication à partir de votre serveur Azure Database pour MySQL.
    1. Pour créer une copie de sauvegarde des données à partir du réplica en lecture Amazon RDS pour MySQL, exécutez `mysqldump` à l’aide de la commande suivante :

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

    > [!NOTE]
    > Vous pouvez également utiliser mydumper pour capturer une copie de sauvegarde mise en parallèle de vos données à partir de votre base de données Amazon RDS pour MySQL source. Pour plus d’informations, consultez [Migrer des bases de données volumineuses vers Azure Database pour MySQL avec mydumper/myloader](../mysql/concepts-migrate-mydumper-myloader.md).

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>Lier les serveurs source et réplica pour démarrer Réplication des données entrantes

1. Pour restaurer la base de données à l’aide de la restauration native MySQL, exécutez la commande suivante :

    ```
    $ mysql -h <target_server> -u <targetuser> -p < dumpname.sql
    ```

   > [!NOTE]
   > Si vous utilisez plutôt myloader, consultez [Migrer des bases de données volumineuses vers Azure Database pour MySQL avec mydumper/myloader](../mysql/concepts-migrate-mydumper-myloader.md).

1. Connectez-vous au serveur Amazon RDS pour MySQL source et configurez un utilisateur de réplication. Accordez ensuite les privilèges nécessaires à cet utilisateur.

    - Si vous utilisez SSL, exécutez les commandes suivantes :

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%' REQUIRE SSL; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    - Si vous n’utilisez pas SSL, exécutez les commandes suivantes :

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%'; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    Toutes les fonctions de réplication de données entrantes sont effectuées par des procédures stockées. Pour plus d’informations sur toutes les procédures, consultez [Procédures stockées de Réplication des données entrantes](../mysql/reference-stored-procedures.md#data-in-replication-stored-procedures). Vous pouvez exécuter ces procédures stockées dans le shell MySQL ou dans MySQL Workbench.

1. Pour relier le serveur source Amazon RDS pour MySQL et le serveur cible Azure Database pour MySQL, connectez-vous au serveur Azure Database pour MySQL cible. Définissez le serveur Amazon RDS pour MySQL en tant que serveur source en exécutant la commande suivante :

    ```
    CALL mysql.az_replication_change_master('source_server','replication_user_name','replication_user_password',3306,'<master_bin_log_file>',master_bin_log_position,'<master_ssl_ca>');
    ```

1. Pour démarrer la réplication entre le serveur Amazon RDS pour MySQL source et le serveur Azure Database pour MySQL cible, exécutez la commande suivante :

    ```
    Mysql> CALL mysql.az_replication_start;
    ```

1. Pour vérifier l’état de réplication, sur le serveur de réplication, exécutez la commande suivante :

    ```
    Mysql> show slave status\G
    ```

    Si l’état des paramètres `Slave_IO_Running` et `Slave_SQL_Running` est **Oui**, la réplication a démarré et est en cours d’exécution.

1. Vérifiez la valeur du paramètre `Seconds_Behind_Master` pour déterminer la façon dont le serveur cible est retardé.

    Si la valeur est **0**, la cible a traité toutes les mises à jour du serveur source. Si la valeur est différente de **0**, le serveur cible continue de traiter les mises à jour.

## <a name="ensure-a-successful-cutover"></a>S’assurer que le basculement est réussi

Pour assurer un basculement réussi :

1. Configurez les identifiants et les autorisations de niveau de base de données appropriés dans le serveur Azure Database pour MySQL cible.
1. Arrêter les écritures sur le serveur Amazon RDS pour MySQL source.
1. Assurez-vous que le serveur Azure Database pour MySQL cible a rattrapé le serveur source et que la valeur `Seconds_Behind_Master` est **0** pour `show slave status`.
1. Appelez la procédure stockée `mysql.az_replication_stop` pour arrêter la réplication, car toutes les modifications ont été répliquées sur le serveur Azure Database pour MySQL cible.
1. Appelez `mysql.az_replication_remove_master` pour supprimer la configuration de Réplication des données entrantes.
1. Rediriger les clients et les applications clientes vers l’instance cible du serveur Azure Database pour MySQL.

À ce stade, la migration est terminée. Vos applications sont connectées au serveur exécutant Azure Database pour MySQL.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la migration de bases de données vers Azure Database pour MySQL, consultez le [Guide de migration des bases de données](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
- Regardez la vidéo [Migrer facilement des applications MySQL/PostgreSQL vers le service géré Azure](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201). Elle contient une démonstration de la migration d’applications MySQL vers Azure Database pour MySQL.
