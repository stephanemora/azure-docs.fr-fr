---
title: Configurer la réplication des données entrantes - Serveur flexible Azure Database pour MySQL
description: Cet article décrit la procédure de configuration de la réplication des données entrantes pour Serveur flexible Azure Database pour MySQL.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: 041e6e2b3a79fa639a00506c81fc3e7ab0a98cec
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111746768"
---
# <a name="how-to-configure-azure-database-for-mysql-flexible-server-data-in-replication"></a>Procédure de configuration de la réplication des données entrantes pour Serveur flexible Azure Database pour MySQL

Cet article décrit la procédure de configuration de la [Réplication des données entrantes](concepts-data-in-replication.md) dans Serveur flexible Azure Database pour MySQL en configurant les serveurs source et réplica. Cet article suppose que vous avez déjà utilisé des serveurs et des bases de données MySQL.

> [!NOTE]
> Cet article contient des références au terme _esclave_, un terme que Microsoft n’utilise plus. Lorsque le terme sera supprimé du logiciel, nous le supprimerons de cet article.
>

Pour créer un réplica dans le service Flexible Azure Database pour MySQL, [Réplication des données entrantes](concepts-data-in-replication.md) synchronise les données provenant d’un serveur MySQL source qui s’exécute localement dans des machines virtuelles ou des services de base de données cloud. La réplication des données entrantes est basée sur la position du fichier journal binaire (binlog). Pour en savoir plus sur la réplication binlog, consultez la [vue d’ensemble de la réplication binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Passez en revue les [limitations et conditions requises](concepts-data-in-replication.md#limitations-and-considerations) de la Réplication des données entrantes avant de suivre les étapes décrites dans cet article.

## <a name="create-an-azure-database-for-mysql-flexible-server-instance-to-use-as-a-replica"></a>Créer une instance de Serveur flexible Azure Database pour MySQL à utiliser comme réplica

1. Créez une instance de Serveur flexible Azure Database pour MySQL (par exemple, « replica.mysql.database.azure.com »). Reportez-vous à [Création d’un Serveur flexible Azure Database pour MySQL à l’aide du portail Azure](quickstart-create-server-portal.md) pour la création de serveurs. Ce serveur est le serveur « réplica » pour la réplication des données entrantes.

2. Créez les mêmes comptes d’utilisateur et les privilèges correspondants.

   Les comptes d’utilisateur ne sont pas répliqués à partir du serveur source vers le serveur réplica. Si vous prévoyez de donner accès au serveur réplica aux utilisateurs, vous devez créer manuellement tous les comptes et les privilèges correspondants sur ce nouveau Serveur flexible Azure Database pour MySQL.

## <a name="configure-the-source-mysql-server"></a>Configurer le serveur MySQL source

Les étapes suivantes servent à préparer et à configurer le serveur MySQL hébergé localement, dans une machine virtuelle ou par un service de base de données hébergé par d’autres fournisseurs de cloud pour la réplication de données entrantes. Ce serveur est la « source » pour la réplication des données entrantes.

1. Avant de continuer, passez en revue les [prérequis du serveur source](concepts-data-in-replication.md#requirements).

2. Configuration réseau requise 
    * Vérifiez que le serveur source autorise le trafic entrant et sortant sur le port 3306, qu’il dispose d’une **adresse IP publique** et que le DNS est accessible publiquement, ou qu’il a un nom de domaine complet (FQDN).

    * Si l’accès privé est utilisé, assurez-vous que vous disposez d’une connectivité entre le serveur source et le réseau virtuel dans lequel le serveur réplica est hébergé. 
    * Veillez à fournir une connectivité de site à site à vos serveurs sources locaux par le biais d'[ExpressRoute](../../expressroute/expressroute-introduction.md) ou d'un [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Pour plus d’informations sur la création d’un réseau virtuel, consultez la [documentation sur le réseau virtuel](../../virtual-network/index.yml), en particulier les articles sur le démarrage rapide, qui fournissent des informations pas à pas.
    * Si l’accès privé est utilisé dans le serveur réplica et que votre source est une machine virtuelle Azure, assurez-vous que la connectivité de réseau virtuel à réseau virtuel est établie. Le peering de réseau virtuel à réseau virtuel au sein des régions est pris en charge. Le **Peering mondial n’est pas pris en charge actuellement.** Vous devez utiliser d’autres méthodes de connectivité pour communiquer entre les réseaux virtuels dans différentes régions, telles que la connexion de réseau virtuel à réseau virtuel. Pour plus d’informations, vous pouvez consulter [Passerelle VPN de réseau virtuel à réseau virtuel](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
    * Assurez-vous que les règles de groupe de sécurité réseau de votre réseau virtuel ne bloquent pas le port sortant 3306 (également entrant si MySQL est en cours d’exécution sur une machine virtuelle Azure). Pour plus d’informations sur le filtrage du trafic de groupe de sécurité réseau de réseau virtuel, consultez l’article [Filtrer le trafic avec les groupes de sécurité réseau](../../virtual-network/virtual-network-vnet-plan-design-arm.md).
    * Configurez les règles de pare-feu de votre serveur source pour autoriser l’adresse IP du serveur réplica.

  
3. Activez la journalisation binaire.

   Vérifiez si la journalisation binaire a été activée sur le serveur source en exécutant la commande suivante :

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Si la variable [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) est renvoyée avec la valeur « ON », la journalisation binaire est activée sur votre serveur.

   Si `log_bin` est retourné avec la valeur « OFF » et que votre serveur source s’exécute localement ou sur des machines virtuelles où vous pouvez accéder au fichier de configuration (my.cnf), vous pouvez procéder comme suit :
   1. recherchez votre fichier de configuration MySQL (my.cnf) dans le serveur source. Par exemple : /etc/my.cnf
   2. Ouvrez le fichier de configuration pour le modifier, puis recherchez-y la section **mysqld**.
   3. Dans la section mysqld, ajoutez la ligne suivante :

       ```bash
       log-bin=mysql-bin.log
       ```

   4. Redémarrez le service MySQL sur le serveur source (ou redémarrez) pour appliquer les modifications.
   5. Une fois le serveur redémarré, vérifiez que la journalisation binaire est activée en exécutant la même requête que précédemment :

      ```sql
      SHOW VARIABLES LIKE 'log_bin';
      ```

4. Configurez les paramètres du serveur source.

   La réplication des données entrantes nécessite que le paramètre `lower_case_table_names` soit cohérent entre les serveurs source et réplica. Par défaut, ce paramètre est défini sur 1 dans Serveur flexible Azure Database pour MySQL.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

5. Créez un rôle de réplication et définissez une autorisation.

   Créez un compte d’utilisateur sur le serveur source configuré avec des privilèges de réplication. Vous pouvez pour cela utiliser des commandes SQL ou un outil tel que MySQL Workbench. Si vous prévoyez une réplication avec SSL, cela doit être spécifié lors de la création de l’utilisateur. Pour comprendre comment [ajouter des comptes d’utilisateur](https://dev.mysql.com/doc/refman/5.7/en/user-names.html) sur votre serveur source, reportez-vous à la documentation MySQL.

   Dans les commandes suivantes, le nouveau rôle de réplication créé peut accéder à la source à partir de n’importe quel ordinateur, et pas seulement celui qui héberge la source. Cela s’effectue en spécifiant « syncuser@’%’ » dans la commande de création d’un utilisateur. Consultez la documentation MySQL pour en savoir plus sur la [définition des noms de compte](https://dev.mysql.com/doc/refman/5.7/en/account-names.html).

   **Commande SQL**

   *Réplication avec SSL*

   Pour exiger le protocole SSL pour toutes les connexions utilisateur, utilisez la commande suivante pour créer un utilisateur :

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Réplication sans SSL*

   Si le protocole SSL n’est pas requis pour toutes les connexions, utilisez la commande suivante pour créer un utilisateur :

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Pour créer le rôle de réplication dans MySQL Workbench, ouvrez le panneau **Utilisateurs et privilèges** à partir du panneau **Gestion**, puis sélectionnez **Ajouter un compte**.

   :::image type="content" source="./media/how-to-data-in-replication/users-privileges.png" alt-text="Utilisateurs et privilèges":::

   Tapez le nom d’utilisateur dans le champ **Nom de connexion**.

   :::image type="content" source="./media/how-to-data-in-replication/sync-user.png" alt-text="Synchroniser l’utilisateur":::

   Sélectionnez le panneau **Rôles administratifs**, puis **Esclave de réplication** dans la liste **Privilèges globaux**. Sélectionnez ensuite **Appliquer** pour créer le rôle de réplication.

   :::image type="content" source="./media/how-to-data-in-replication/replication-slave.png" alt-text="Subordonné de réplication":::

6. Définissez le serveur source en mode lecture seule.

   Avant de commencer à vider la base de données, le serveur doit être placé en mode lecture seule. En mode lecture seule, le serveur source ne peut traiter aucune transaction d’écriture. Évaluer l’impact sur votre entreprise et planifiez la fenêtre de lecture seule lors d’une période creuse, si nécessaire.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. Récupérez le nom du fichier journal binaire et le décalage.

   Exécutez la commande [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) afin de déterminer le nom de fichier du journal binaire actuel et le décalage.

   ```sql
    show master status;
   ```
   Les résultats doivent se présenter de la façon suivante. Veillez à noter le nom du fichier binaire, car vous en aurez besoin par la suite.

   :::image type="content" source="./media/how-to-data-in-replication/master-status.png" alt-text="Résultats de l’état récapitulatif":::

## <a name="dump-and-restore-the-source-server"></a>Vider et restaurer le serveur source

1. Déterminez les bases de données et les tables que vous souhaitez répliquer dans Serveur flexible Azure Database pour MySQL et effectuez la copie de sauvegarde à partir du serveur source.

    Vous pouvez utiliser mysqldump pour vider les bases de données à partir de votre serveur principal. Pour plus d’informations, reportez-vous à [Dump & Restore](../concepts-migrate-dump-restore.md) (Vider et restaurer). Il n’est pas nécessaire de vider la bibliothèque MySQL ni la bibliothèque de test.


3. Définissez le serveur source en mode lecture/écriture.

   Une fois la base de données vidée, remettez le serveur MySQL source en mode lecture/écriture.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

4. Restaurez le fichier de vidage sur le nouveau serveur.

   Restaurez le fichier de sauvegarde sur le serveur créé dans le service Serveur flexible Azure Database pour MySQL. Reportez-vous à [Dump & Restore](../concepts-migrate-dump-restore.md) (Vider et restaurer) pour savoir comment restaurer un fichier de vidage sur un serveur MySQL. Si le fichier de vidage est volumineux, transférez-le vers une machine virtuelle dans Azure au sein de la même région que votre serveur réplica. Restaurez-le sur le Serveur flexible Azure Database pour MySQL à partir de la machine virtuelle.


## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>Lier les serveurs source et réplica pour démarrer Réplication des données entrantes

1. Définissez le serveur source.

   Toutes les fonctions de réplication de données entrantes sont effectuées par des procédures stockées. Vous trouverez toutes les procédures dans [Procédures stockées de réplication de données entrantes](../reference-stored-procedures.md). Les procédures stockées peuvent être exécutées dans l’interpréteur de commandes MySQL ou MySQL Workbench.

   Pour lier deux serveurs et démarrer une réplication, connectez-vous au serveur réplica cible dans le service Azure Database pour MySQL, et définissez l’instance externe en tant que serveur source. Pour ce faire, utilisez la procédure stockée `mysql.az_replication_change_master` sur le serveur Azure DB pour MySQL.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', <master_port>, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

 
   - master_host : nom d’hôte du serveur source
   - master_user : nom d’utilisateur pour le serveur source
   - master_password : mot de passe pour le serveur source
   - master_port : numéro de port sur lequel le serveur source écoute les connexions. (3306 est le port par défaut sur lequel MySQL écoute)
   - master_log_file : nom de fichier du journal binaire à partir de l’exécution de `show master status`
   - master_log_pos : position du journal binaire à partir de l’exécution de `show master status`
   - master_ssl_ca : contexte du certificat d’autorité de certification. Si vous n’utilisez pas le protocole SSL, transmettez une chaîne vide.

     Il est recommandé de transmettre ce paramètre comme variable. Pour plus d'informations, consultez les exemples suivants.

   > [!NOTE]
   > Si le serveur source est hébergé dans une machine virtuelle Azure, activez l’option « Autoriser l’accès aux services Azure » pour autoriser les serveurs sources et de réplica à communiquer entre eux. Ce paramètre peut être modifié dans les options de **sécurité de la connexion**. Pour plus d’informations, consultez [Gestion des règles de pare-feu avec le portail](how-to-manage-firewall-portal.md).

   **Exemples**

   *Réplication avec SSL*

   La variable `@cert` est créée en exécutant les commandes MySQL suivantes :

      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```

   La réplication avec SSL est définie entre un serveur source hébergé dans le domaine « companya.com » et un serveur réplica hébergé dans Serveur flexible Azure Database pour MySQL. Cette procédure stockée est exécutée sur le réplica.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```

   *Réplication sans SSL*

   La réplication sans SSL est définie entre un serveur source hébergé dans le domaine « companya.com » et un serveur réplica hébergé dans Serveur flexible Azure Database pour MySQL. Cette procédure stockée est exécutée sur le réplica.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```

2. Configurez le filtrage.

   Si vous souhaitez ignorer la réplication de certaines tables à partir de votre serveur maître, mettez à jour le paramètre serveur `replicate_wild_ignore_table` sur votre serveur de réplication. Vous pouvez fournir plusieurs modèles de table à l'aide d'une liste séparée par des virgules.

   Consultez la [documentation MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) pour en savoir plus sur ce paramètre.

   Pour mettre à jour le paramètre, utilisez le [portail Azure](how-to-configure-server-parameters-portal.md) ou [Azure CLI](how-to-configure-server-parameters-cli.md).

3. Démarrez la réplication.

   Appelez la procédure stockée `mysql.az_replication_start` pour commencer la réplication.

   ```sql
   CALL mysql.az_replication_start;
   ```

4. Vérifiez l’état de la réplication.

   Appelez la commande [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) sur le serveur réplica pour afficher l’état de réplication.

   ```sql
   show slave status;
   ```

   Si l’état de `Slave_IO_Running` et `Slave_SQL_Running` est « yes » et que la valeur de `Seconds_Behind_Master` est « 0 », cela signifie que la réplication fonctionne correctement. `Seconds_Behind_Master` indique que le retard du réplica. Si la valeur est différente de « 0 », cela signifie que le réplica est en train de traiter les mises à jour.

## <a name="other-useful-stored-procedures-for-data-in-replication-operations"></a>Autres procédures stockées utiles pour les opérations de réplication des données entrantes

### <a name="stop-replication"></a>Arrêter la réplication

Pour arrêter la réplication entre le serveur source et le serveur réplica, utilisez la procédure stockée suivante :

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Supprimer la relation de réplication

Pour supprimer la relation entre le serveur source et le serveur réplica, utilisez la procédure stockée suivante :

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Ignorer l’erreur de réplication

Pour ignorer une erreur de réplication et autoriser la poursuite de la réplication, utilisez la procédure stockée suivante :

```sql
CALL mysql.az_replication_skip_counter;
```

```sql
SHOW BINLOG EVENTS [IN 'log_name'] [FROM pos][LIMIT [offset,] row_count]
```

  :::image type="content" source="./media/how-to-data-in-replication/show-binary-log.png" alt-text="Afficher les résultats du journal binaire":::

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [Réplication des données entrantes](concepts-data-in-replication.md) pour Serveur flexible Azure Database pour MySQL.
