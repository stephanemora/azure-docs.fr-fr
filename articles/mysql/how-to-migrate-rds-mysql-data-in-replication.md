---
title: Migrer Amazon RDS pour MySQL vers Azure Database pour MySQL à l’aide de la réplication des données entrantes
description: Cet article explique comment migrer Amazon RDS pour MySQL vers Azure Database pour MySQL à l’aide de la réplication des données entrantes.
author: SudheeshGH
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: d05806b9d69543fdd4f5584661a6b8b74ac1f01e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647154"
---
# <a name="migrate-amazon-rds-for-mysql-to-azure-database-for-mysql-using-data-in-replication"></a>Migrer Amazon RDS pour MySQL vers Azure Database pour MySQL à l’aide de la réplication des données entrantes

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

> [!NOTE]
> Cet article contient des références au terme *esclave*, un terme que Microsoft n’utilise plus. Quand le terme sera supprimé du logiciel, nous le supprimerons de cet article.

Vous pouvez utiliser différentes méthodes, comme le vidage et la restauration, l’exportation/importation de MySQL Workbench ou Azure Database Migration Service (DMS) pour migrer Amazon RDS pour MySQL vers Azure Database pour MySQL. Toutefois, en utilisant une combinaison d’outils open source comme le vidage et la restauration MySQL ou mydumper/myLoader avec la réplication de données, vous pouvez migrer vos charges de travail avec un temps d’arrêt minimal.

La réplication de données est une technique qui réplique les modifications de données du serveur source vers le serveur de destination en fonction de la méthode de position de fichier journal binaire. Dans ce scénario, l’instance MySQL fonctionnant comme source (sur laquelle les modifications sont apportées à la base de données) écrit des mises à jour et des modifications en tant que « événements » dans le journal binaire. Les informations contenues dans le journal binaire sont stockées dans différents formats de journalisation en fonction des modifications de la base de données en cours d’enregistrement. Les réplicas sont configurés pour lire le journal binaire à partir de la source et pour exécuter les événements dans le journal binaire sur la base de données locale du réplica.

Si vous configurez la [réplication des données entrantes](/azure/mysql/flexible-server/concepts-data-in-replication) pour synchroniser les données d’un serveur MySQL source à un serveur MySQL cible, vous pouvez effectuer un basculement sélectif de vos applications à partir de la base de données principale (ou de la base de données source) vers le réplica (ou la base de données cible).

Dans ce tutoriel, vous allez apprendre à configurer la réplication de données entre un serveur source exécutant Amazon RDS pour MySQL et un serveur cible exécutant Azure Database pour MySQL.

## <a name="performance-considerations"></a>Considérations relatives aux performances

Avant de commencer ce tutoriel, veillez à prendre en compte les implications en matière de performances de l’emplacement et de la capacité de l’ordinateur client que vous allez utiliser pour effectuer l’opération.

### <a name="client-location"></a>Emplacement du client

Effectuez des opérations de vidage/restauration à partir d’un ordinateur client qui est lancé dans le même emplacement que le serveur de base de données :

- Pour les serveurs de base de données Azure pour MySQL, l’ordinateur client doit se trouver dans le même réseau virtuel et dans la même zone de disponibilité que le serveur de base de données cible.
- Pour les instances de base de données Amazon RDS source, l’instance cliente doit exister dans le même cloud virtuel privé Amazon (Amazon VPC) et dans la même zone de disponibilité que le serveur de base de données source.
Dans le cas ci-dessus, vous pouvez déplacer des fichiers de vidage entre des ordinateurs clients à l’aide de protocoles de transfert de fichiers (tels que FTP ou SFTP) ou les télécharger vers le Stockage Blob Azure. Pour réduire la durée totale de la migration, vous pouvez compresser les fichiers avant de les transférer.

### <a name="client-capacity"></a>Capacité du client

Quel que soit l’emplacement de l’ordinateur client, celui-ci requiert des capacités de calcul, d’E/S et réseau adéquates pour effectuer les opérations demandées. Les recommandations générales sont les suivantes :

- Si le vidage ou la restauration implique le traitement en temps réel des données, par exemple la compression ou la décompression, choisissez une classe d’instance avec au moins un cœur de processeur par thread de vidage/restauration.
- Vérifiez que la bande passante réseau disponible pour l’instance du client est suffisante. Nous vous recommandons d’utiliser des types d’instance qui prennent en charge la fonctionnalité de mise en réseau accélérée. Pour plus d’informations, consultez la section mise en réseau accélérée dans le [Guide de mise en réseau de machines virtuelles Azure](/azure/virtual-network/create-vm-accelerated-networking-cli).
- Assurez-vous que la couche de stockage de la machine cliente fournit la capacité de lecture/écriture attendue, et nous vous recommandons d’utiliser une machine virtuelle Azure avec stockage SSD Premium.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

- Installez [mysqlclient](https://dev.mysql.com/downloads/) sur votre ordinateur client pour créer un vidage et effectuer une opération de restauration sur votre serveur Azure Database pour MySQL cible.
- Pour les bases de données plus volumineuses, installez [mydumper/myloader](https://centminmod.com/mydumper.html) pour le vidage parallèle et la restauration des bases de données.

    > [!NOTE]
    > mydumper ne peut s’exécuter que sur les distributions Linux, consultez [Comment installer Mydumper](https://github.com/maxbube/mydumper#how-to-install-mydumpermyloader).

- Créez une instance d’Azure Database pour serveur MySQL exécutant la version 5.7 ou 8.0.

    > [!IMPORTANT]
    > Si votre cible est un serveur flexible Azure Database pour MySQL avec une haute disponibilité redondante interzone, notez que la réplication des données n’est pas prise en charge pour cette configuration. Pour une solution de contournement, lors de la création du serveur, configurez la haute disponibilité redondante interzone en procédant comme suit :
    >
    > 1. Créez le serveur avec la haute disponibilité redondante interzone.
    > 2. Désactivez la haute disponibilité.
    > 3. Suivez l’article pour configurer les données dans la réplication.
    > 4. Après le basculement, supprimez les données dans la configuration de la réplication.
    > 5. Activez la haute disponibilité.

Il est également recommandé de s’assurer que plusieurs paramètres et fonctionnalités sont configurés correctement, comme décrit ci-dessous.

- Pour des raisons de compatibilité, nous vous recommandons d’avoir les serveurs de base de données source et cible sur la même version de MySQL.
- Avoir une clé primaire dans chaque table. L’absence de clés primaires sur les tables peut ralentir le processus de réplication.
- Assurez-vous que le jeu de caractères des bases de données source et cible est identique.
- Définissez le paramètre `wait_timeout` sur une valeur raisonnable en fonction de la quantité de données ou de la charge de travail que vous souhaitez importer ou migrer.
- Vérifiez que toutes vos tables utilisent InnoDB, car le serveur Azure Database pour MySQL ne prend en charge que le moteur de stockage InnoDB.
- Pour les tables comportant de nombreux index secondaires ou pour les tables de très grande taille, les effets de la surcharge de performance sont particulièrement visibles lors de la restauration. Modifiez les fichiers de vidage afin que les instructions `CREATE TABLE` n’incluent pas les définitions de clés secondaires, et après avoir importé les données, recréez les index secondaires pour éviter la pénalité de performance pendant le processus de restauration.

Enfin, pour préparer la réplication des données entrantes :

- Vérifiez que le serveur Azure Database pour MySQL cible peut se connecter au serveur Amazon RDS pour MySQL sur le port 3306.
- Assurez-vous que le serveur source Amazon RDS pour MySQL autorise le trafic entrant et sortant sur le port 3306.
- Assurez-vous de fournir une [connectivité de site à site](/azure/vpn-gateway/tutorial-site-to-site-portal) à votre serveur source en utilisant [ExpressRoute](/azure/expressroute/expressroute-introduction) ou une [Passerelle VPN](/azure/vpn-gateway/vpn-gateway-about-vpngateways). Pour plus d’informations sur la création d’un réseau virtuel, consultez la [documentation sur le réseau virtuel](/azure/virtual-network/), en particulier les articles sur le démarrage rapide, qui fournissent des informations pas à pas.
- Configurez les groupes de sécurité réseau de votre serveur de base de données source pour autoriser l’adresse IP du serveur de la base de données Azure pour MySQL.

> [!IMPORTANT]
> Si l’instance Amazon RDS pour MySQL de la source a GTID_mode défini sur ON, l’instance cible du serveur flexible Azure Database pour MySQL doit également avoir GTID_mode défini sur ON.

## <a name="configure-the-target-instance-of-azure-database-for-mysql"></a>Configurer l’instance cible Azure Database pour MySQL

Pour configurer l’instance cible d’Azure Database pour MySQL (la cible pour la réplication de données), procédez comme suit :

1. Définissez la valeur du paramètre `max_allowed_packet` à la valeur maximale de **1073741824** (1 Go), ce qui empêchera tout problème de dépassement de capacité lié aux longues lignes.
2. Définissez les paramètres `slow_query_log`, `general_log`, `audit_log_enabled` et `query_store_capture_mode` sur **OFF** pendant la migration afin d’éliminer toute surcharge liée à la journalisation des requêtes.
3. Mettez à l’échelle la taille de calcul du serveur Azure Database pour MySQL jusqu’au maximum de 64 vCores pour fournir des ressources de calcul supplémentaires lors de la restauration du vidage de la base de données à partir du serveur source.

    Vous pouvez toujours mettre à l’échelle le calcul pour répondre aux demandes de votre application une fois la migration terminée.

4. Augmentez la taille du stockage pour obtenir plus d’E/S par seconde pendant la migration ou augmentez le nombre maximal d’E/S par seconde pour la migration.

   > [!NOTE]
   > Le nombre maximal d’E/S par seconde disponibles est déterminé par la taille de calcul. Pour plus d’informations, consultez la section IOPS des [Options de calcul et de stockage dans Azure Database pour MySQL - Serveur flexible](/azure/mysql/flexible-server/concepts-compute-storage#iops).

## <a name="configure-the-source-amazon-rds-for-mysql-server"></a>Configurer la source Amazon RDS pour MySQL Server

Pour préparer et configurer le serveur MySQL hébergé dans Amazon RDS (la « source » pour la réplication des données entrantes), procédez comme suit.

1. Vérifiez que la journalisation binaire est activée sur la source Amazon RDS pour MySQL Server en vérifiant que les sauvegardes automatisées sont activées, ou assurez-vous qu’il existe un réplica de lecture pour le serveur Amazon RDS pour MySQL.

2. Assurez-vous que les fichiers journaux binaires sur le serveur source sont conservés jusqu’à ce que les modifications aient été appliquées sur l’instance cible de la base de données Azure pour MySQL.

    Avec la réplication des données, Azure Database pour MySQL ne gère pas le processus de réplication.

3. Pour vérifier la rétention des journaux binaires sur le serveur Amazon RDS source afin de déterminer le nombre d’heures pendant lesquelles les journaux binaires sont conservés, appelez la procédure stockée « mysql.rds_show_configuration » :

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
4. Pour configurer la période de rétention des journaux binaires, exécutez la procédure stockée `rds_set_configuration` pour vous assurer que les journaux binaires sont conservés sur le serveur source pendant la durée souhaitée, par exemple :

    ```
    Mysql> Call mysql.rds_set_configuration(‘binlog retention hours’, 96);
    ```

    Cela est utile si vous créez un vidage, puis que vous restaurez, pour rattraper rapidement les modifications delta.

   > [!NOTE]
   > Assurez-vous qu’il y a suffisamment d’espace disque pour stocker les journaux binaires sur le serveur source, en fonction de la période de rétention définie.

Il existe deux façons de sauvegarder des données à partir du serveur Amazon RDS pour MySQL source. Une approche consiste à prendre un vidage de données directement à partir du serveur source, et une autre consiste à capturer un vidage à partir d’un réplica en lecture Amazon RDS pour MySQL.

- Si vous capturez un vidage de données directement à partir du serveur source, procédez comme suit :

    1. Veillez à arrêter les écritures de l’application pendant quelques minutes pour obtenir un vidage transactionnel cohérent des données.

       Vous pouvez également affecter temporairement la valeur **1** au paramètre `read_only` afin que les écritures ne soient pas traitées lors de la capture d’un vidage de données.

    2. Après avoir arrêté les écritures sur le serveur source, récupérez le nom et le décalage du fichier journal binaire en exécutant la commande `Mysql> Show master status;`.
    3. Enregistrez ces valeurs pour démarrer la réplication à partir de votre serveur Azure Database pour MySQL.
    4. Pour créer un vidage de données, exécutez mysqldump, puis exécutez la commande suivante :

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

- Si l’arrêt des écritures sur le serveur source n’est pas une option ou si les performances des données de vidage ne sont pas acceptables sur le serveur source, capturez un vidage sur un serveur de réplication en procédant comme suit :

    1. Créez un réplica en lecture Amazon MySQL avec la même configuration que le serveur source, puis créez le vidage dessus.
    2. Laissez le réplica de lecture Amazon RDS pour MySQL rattraper le serveur Amazon RDS pour MySQL source.
    3. Lorsque le décalage du réplica atteint **0** sur le réplica en lecture, arrêtez la réplication en appelant la procédure stockée `mysql.rds_stop_replication`.

        ```
        Mysql> call mysql.rds_stop_replication;
        ```

    4. Une fois la réplication arrêtée, connectez-vous au réplica, puis exécutez la commande `SHOW SLAVE STATUS` pour récupérer le nom du fichier journal binaire actuel dans le champ **Relay_Master_Log_File** et la position du fichier journal à partir du champ **Exec_Master_Log_Pos**.
    5. Enregistrez ces valeurs pour démarrer la réplication à partir de votre serveur Azure Database pour MySQL.
    6. Pour créer un vidage de données à partir du réplica en lecture Amazon RDS pour MySQL, exécutez mysqldump en exécutant la commande suivante :

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

    > [!NOTE]
    > Vous pouvez également utiliser mydumper pour effectuer une sauvegarde en parallèle de vos données à partir de votre serveur Amazon RDS pour MySQL source. Pour plus d’informations, consultez [Migration d’une base de données volumineuse vers Azure Database pour MySQL à l’aide de mydumper/myloader](/azure/mysql/concepts-migrate-mydumper-myloader).

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>Lier les serveurs source et réplica pour démarrer Réplication des données entrantes

1. Pour restaurer la base de données à l’aide de la restauration native MySQL, exécutez la commande suivante :

    ```
    $ mysql -h <target_server> -u <targetuser> -p < dumpname.sql
    ```

   > [!NOTE]
   > Si vous utilisez plutôt myloader, consultez [Migrer une grande base de données vers Azure Database pour MySQL en utilisant mydumper/myloader](/azure/mysql/concepts-migrate-mydumper-myloader).

2. Connectez-vous au serveur Amazon RDS pour MySQL source, configurez un utilisateur de réplication, puis accordez les privilèges nécessaires à cet utilisateur.

    - Si vous utilisez SSL, exécutez les commandes suivantes :

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%' REQUIRE SSL; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    - Si vous n'utilisez pas SSL, exécutez la commande suivante :

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%'; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    Toutes les fonctions de réplication de données entrantes sont effectuées par des procédures stockées. Pour plus d’informations sur toutes les procédures, consultez [Procédures stockées de réplication de données](/azure/mysql/reference-stored-procedures#data-in-replication-stored-procedures). Vous pouvez exécuter ces procédures stockées dans le shell MySQL ou dans MySQL Workbench.

3. Pour lier le serveur Amazon RDS pour MySQL serveur et le serveur Azure Database pour MySQL cible, connectez-vous au serveur Azure Database pour MySQL cible et définissez le serveur Amazon RDS pour MySQL en tant que serveur source en exécutant la commande suivante :

    ```
    CALL mysql.az_replication_change_master('source_server','replication_user_name','replication_user_password',3306,'<master_bin_log_file>',master_bin_log_position,'<master_ssl_ca>');
    ```

4. Pour démarrer la réplication entre le serveur Amazon RDS pour MySQL source et le serveur Azure Database pour MySQL cible, exécutez la commande suivante :

    ```
    Mysql> CALL mysql.az_replication_start;
    ```

5. Pour vérifier l’état de réplication, sur le serveur de réplication, exécutez la commande suivante :

    ```
    Mysql> show slave status\G
    ```

    Si l’état des paramètres `Slave_IO_Running` et `Slave_SQL_Running` est **Oui**, la réplication a démarré et est dans un état d’exécution.

6. Vérifiez la valeur du paramètre `Seconds_Behind_Master` pour déterminer la façon dont le serveur cible est retardé.

    Si la valeur est **0**, la cible a traité toutes les mises à jour du serveur source. Si la valeur est différente de **0**, le serveur cible continue de traiter les mises à jour.

## <a name="ensure-a-successful-cutover"></a>S’assurer que le basculement est réussi

Pour garantir la réussite du basculement, effectuez les tâches suivantes :

1. Configurez les identifiants et les autorisations de niveau de base de données appropriés dans le serveur cible Azure Database pour MySQL.
2. Arrêter les écritures sur le serveur Amazon RDS pour MySQL source.
3. Assurez-vous que le serveur Azure Database pour MySQL cible a rattrapé le serveur source et que la valeur `Seconds_Behind_Master` est **0** pour `show slave status`.
4. Appelez la procédure stockée `mysql.az_replication_stop` pour arrêter la réplication, car toutes les modifications ont été répliquées sur le serveur Azure Database pour MySQL cible.
5. Appelez `mysql.az_replication_remove_master` pour supprimer la configuration de réplication des données entrantes.
6. Rediriger les clients et les applications clientes vers l’instance cible du serveur Azure Database pour MySQL.

À ce stade, la migration est terminée, et vos applications sont connectées au serveur exécutant Azure Database pour MySQL.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la migration de bases de données vers Azure Database pour MySQL, consultez le [Guide de migration des bases de données](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
- Regardez la vidéo [Easily migrate MySQL/PostgreSQL apps to Azure managed service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201) (Migration facile des applications MySQL/PostgreSQL vers un service géré Azure) qui comprend une démonstration sur la migration des applications MySQL vers Azure Database pour MySQL.
