---
title: Configurer la réplication des données entrantes - Azure Database pour MySQL
description: Cet article décrit comment configurer la réplication des données entrantes pour Azure Database pour MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 04/08/2021
ms.openlocfilehash: 5f418867a2f22a16304d16c8889fff9a27a37ab3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312159"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Comment configurer Azure Database pour MySQL pour la réplication de données entrantes MySQL

Cet article décrit comment configurer [Réplication des données entrantes](concepts-data-in-replication.md) dans Azure Database pour MySQL en configurant les serveurs source et réplica. Cet article suppose que vous avez déjà utilisé des serveurs et des bases de données MySQL.

> [!NOTE]
> Cet article contient des références au terme _esclave_, un terme que Microsoft n’utilise plus. Lorsque le terme sera supprimé du logiciel, nous le supprimerons de cet article.
>

Pour créer un réplica dans le service Azure Database pour MySQL, [Réplication des données entrantes](concepts-data-in-replication.md) synchronise les données provenant d’un serveur MySQL source qui s’exécute localement dans des machines virtuelles ou des services de base de données cloud. La réplication des données repose sur la réplication basée sur le GTID ou sur la position du fichier journal binaire (binlog) native à MySQL. Pour en savoir plus sur la réplication binlog, consultez la [vue d’ensemble de la réplication binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Passez en revue les [limitations et conditions requises](concepts-data-in-replication.md#limitations-and-considerations) de la Réplication des données entrantes avant de suivre les étapes décrites dans cet article.

## <a name="create-an-azure-database-for-mysql-single-server-instance-to-use-as-a-replica"></a>Créer une instance de serveur unique Azure Database pour MySQL à utiliser comme réplica

1. Créez une instance de serveur unique Azure Database pour MySQL (par exemple, « replica.mysql.database.azure.com »). Reportez-vous à [Création d’un serveur Azure Database pour MySQL à l’aide du portail Azure](quickstart-create-mysql-server-database-using-azure-portal.md) pour la création de serveurs. Ce serveur est le serveur « réplica » pour la réplication des données entrantes.

   > [!IMPORTANT]
   > Le serveur Azure Database pour MySQL doit être créé dans le niveau tarifaire Usage général ou Mémoire optimisée, car la réplication des données est prise en charge uniquement dans ces niveaux.

2. Créez les mêmes comptes d’utilisateur et les privilèges correspondants.

   Les comptes d’utilisateur ne sont pas répliqués à partir du serveur source vers le serveur réplica. Si vous prévoyez de donner accès au serveur réplica aux utilisateurs, vous devez créer manuellement tous les comptes et les privilèges correspondants sur ce nouveau serveur Azure Database pour MySQL.

3. Ajoutez l’adresse IP du serveur source aux règles de pare-feu du réplica.

   Mettez à jour les règles de pare-feu à l’aide du [portail Azure](howto-manage-firewall-using-portal.md) ou d’[Azure CLI](howto-manage-firewall-using-cli.md).

4. **Facultatif** : Si vous souhaitez utiliser la [réplication basée sur le GTID](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html) du serveur source vers le serveur de réplication Azure Database pour MySQL, vous devez activer les paramètres de serveur suivants sur le serveur Azure Database pour MySQL, comme indiqué dans l’image du portail ci-dessous :

   :::image type="content" source="./media/howto-data-in-replication/enable-gtid.png" alt-text="Activer le GTID sur le serveur Azure Database pour MySQL":::

## <a name="configure-the-source-mysql-server"></a>Configurer le serveur MySQL source

Les étapes suivantes servent à préparer et à configurer le serveur MySQL hébergé localement, dans une machine virtuelle ou par un service de base de données hébergé par d’autres fournisseurs de cloud pour la réplication de données entrantes. Ce serveur est la « source » pour la réplication des données entrantes.

1. Avant de continuer, passez en revue les [prérequis du serveur source](concepts-data-in-replication.md#requirements).

2. Vérifiez que le serveur source autorise le trafic entrant et sortant sur le port 3306, qu’il dispose d’une **adresse IP publique** et que le DNS est accessible publiquement, ou qu’il a un nom de domaine complet (FQDN).

   Testez la connectivité au serveur source en tentant de vous connecter avec un outil comme la ligne de commande MySQL, hébergé sur un autre ordinateur, ou avec [Azure Cloud Shell](../cloud-shell/overview.md), accessible sur le Portail Azure.

   Si votre organisation a des stratégies de sécurité strictes et n’autorise pas la communication d’Azure vers votre serveur source pour toutes les adresses IP du serveur source, vous pouvez éventuellement utiliser la commande ci-dessous pour déterminer l’adresse IP de votre serveur MySQL.

   1. Connectez-vous à votre serveur Azure Database pour MySQL avec un outil comme la ligne de commande MySQL.

   2. Exécutez la requête suivante.

      ```bash
      mysql> SELECT @@global.redirect_server_host;
      ```

      Voici un exemple de sortie :

      ```bash
      +-----------------------------------------------------------+
      | @@global.redirect_server_host                             |
      +-----------------------------------------------------------+
      | e299ae56f000.tr1830.westus1-a.worker.database.windows.net |
       +-----------------------------------------------------------+
      ```

   3. Quittez la ligne de commande MySQL.
   4. Pour récupérer l’adresse IP, exécutez la commande suivante dans l’utilitaire Ping :

      ```bash
      ping <output of step 2b>
      ```

      Par exemple :

      ```bash
      C:\Users\testuser> ping e299ae56f000.tr1830.westus1-a.worker.database.windows.net
      Pinging tr1830.westus1-a.worker.database.windows.net (**11.11.111.111**) 56(84) bytes of data.
      ```

   5. Configurez les règles de pare-feu de votre serveur source pour inclure l’adresse IP de sortie de l’étape précédente sur le port 3306.

      > [!NOTE]
      > Cette adresse IP peut changer en raison d’opérations de maintenance ou de déploiement. Cette méthode de connectivité concerne uniquement les clients qui ne peuvent pas se permettre d’autoriser toutes les adresses IP sur le port 3306.
  
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

   4. Redémarrez le serveur source MySQL pour appliquer les modifications.
   5. Une fois le serveur redémarré, vérifiez que la journalisation binaire est activée en exécutant la même requête que précédemment :

      ```sql
      SHOW VARIABLES LIKE 'log_bin';
      ```

4. Configurez les paramètres du serveur source.

   Réplication des données entrantes nécessite que le paramètre `lower_case_table_names` soit cohérent entre les serveurs source et réplica. Par défaut, ce paramètre est 1 dans Azure Database pour MySQL.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

   **Facultatif** : Si vous souhaitez utiliser la [réplication basée sur le GTID](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html), vous devez vérifier si le GTID est activé sur le serveur source. Vous pouvez exécuter la commande suivante sur votre serveur MySQL source pour voir si gtid_mode est activé.

   ```sql
   show variables like 'gtid_mode';
   ```

   >[!IMPORTANT]
   > La valeur par défaut de gtid_mode est OFF pour tous les serveurs. Vous n’avez pas besoin d’activer le GTID sur le serveur MySQL source spécifiquement pour configurer Réplication des données entrantes. Si le GTID est déjà activé sur le serveur source, vous pouvez éventuellement utiliser la réplication basée sur le GTID afin de configurer Réplication des données entrantes également avec Azure Database pour MySQL - Serveur unique. Vous pouvez utiliser la réplication basée sur les fichiers afin de configurer la réplication des données pour tous les serveurs, quelle que soit la configuration de gitd_mode sur le serveur source.

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

   :::image type="content" source="./media/howto-data-in-replication/users_privileges.png" alt-text="Utilisateurs et privilèges":::

   Tapez le nom d’utilisateur dans le champ **Nom de connexion**.

   :::image type="content" source="./media/howto-data-in-replication/syncuser.png" alt-text="Synchroniser l’utilisateur":::

   Sélectionnez le panneau **Rôles administratifs**, puis **Esclave de réplication** dans la liste **Privilèges globaux**. Sélectionnez ensuite **Appliquer** pour créer le rôle de réplication.

   :::image type="content" source="./media/howto-data-in-replication/replicationslave.png" alt-text="Subordonné de réplication":::

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

   :::image type="content" source="./media/howto-data-in-replication/masterstatus.png" alt-text="Résultats de l’état récapitulatif":::

## <a name="dump-and-restore-the-source-server"></a>Vider et restaurer le serveur source

1. Déterminez les bases de données et les tables que vous souhaitez répliquer dans Azure Database pour MySQL et effectuez la copie de sauvegarde à partir du serveur source.

    Vous pouvez utiliser mysqldump pour vider les bases de données à partir de votre serveur principal. Pour plus d’informations, reportez-vous à [Dump & Restore](concepts-migrate-dump-restore.md) (Vider et restaurer). Il n’est pas nécessaire de vider la bibliothèque MySQL ni la bibliothèque de test.

2. **Facultatif** : Si vous souhaitez utiliser la [réplication basée sur le GTID](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html), vous devez identifier le GTID de la dernière transaction exécutée sur le serveur principal. Vous pouvez utiliser la commande suivante pour noter le GTID de la dernière transaction exécutée sur le serveur maître.

   ```sql
   show global variables like 'gtid_executed';
   ```

3. Définissez le serveur source en mode lecture/écriture.

   Une fois la base de données vidée, remettez le serveur MySQL source en mode lecture/écriture.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

4. Restaurez le fichier de vidage sur le nouveau serveur.

   Restaurez le fichier de vidage sur le serveur créé dans le service Azure Database pour MySQL. Reportez-vous à [Dump & Restore](concepts-migrate-dump-restore.md) (Vider et restaurer) pour savoir comment restaurer un fichier de vidage sur un serveur MySQL. Si le fichier de vidage est volumineux, transférez-le vers une machine virtuelle dans Azure au sein de la même région que votre serveur réplica. Restaurez-le sur le serveur Azure Database pour MySQL à partir de la machine virtuelle.

5. **Facultatif** : Notez le GTID du serveur restauré sur Azure Database pour MySQL afin de garantir qu’il est identique à celui du serveur principal. Vous pouvez utiliser la commande suivante pour noter la valeur de GTID purgée sur le serveur de réplication Azure Database pour MySQL. La valeur de gtid_purged doit être identique à gtid_executed sur le maître, notée à l’étape 2, pour que la réplication basée sur le GTID fonctionne.

   ```sql
   show global variables like 'gtid_purged';
   ```

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>Lier les serveurs source et réplica pour démarrer Réplication des données entrantes

1. Définissez le serveur source.

   Toutes les fonctions de réplication de données entrantes sont effectuées par des procédures stockées. Vous trouverez toutes les procédures dans [Data-in Replication Stored Procedures](./reference-stored-procedures.md) (Procédures stockées de réplication de données entrantes). Les procédures stockées peuvent être exécutées dans l’interpréteur de commandes MySQL ou MySQL Workbench.

   Pour lier deux serveurs et démarrer une réplication, connectez-vous au serveur réplica cible dans le service Azure Database pour MySQL, et définissez l’instance externe en tant que serveur source. Pour ce faire, utilisez la procédure stockée `mysql.az_replication_change_master` sur le serveur Azure DB pour MySQL.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', <master_port>, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   **Facultatif** : Si vous souhaitez utiliser la [réplication basée sur le GTID](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html), vous devez utiliser la commande suivante pour lier les deux serveurs

    ```sql
   call mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', <master_port>, '<master_ssl_ca>');
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
   > Si le serveur source est hébergé dans une machine virtuelle Azure, activez l’option « Autoriser l’accès aux services Azure » pour autoriser les serveurs sources et de réplica à communiquer entre eux. Ce paramètre peut être modifié dans les options de **sécurité de la connexion**. Pour plus d’informations, consultez [Gestion des règles de pare-feu avec le portail](howto-manage-firewall-using-portal.md).

   **Exemples**

   *Réplication avec SSL*

   La variable `@cert` est créée en exécutant les commandes MySQL suivantes :

      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```

   La réplication avec SSL est définie entre un serveur source hébergé dans le domaine « companya.com » et un serveur réplica hébergé dans Azure Database pour MySQL. Cette procédure stockée est exécutée sur le réplica.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```

   *Réplication sans SSL*

   La réplication sans SSL est définie entre un serveur source hébergé dans le domaine « companya.com » et un serveur réplica hébergé dans Azure Database pour MySQL. Cette procédure stockée est exécutée sur le réplica.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```

2. Configurez le filtrage.

   Si vous souhaitez ignorer la réplication de certaines tables à partir de votre serveur maître, mettez à jour le paramètre serveur `replicate_wild_ignore_table` sur votre serveur de réplication. Vous pouvez fournir plusieurs modèles de table à l'aide d'une liste séparée par des virgules.

   Consultez la [documentation MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) pour en savoir plus sur ce paramètre.

   Pour mettre à jour le paramètre, utilisez le [portail Azure](howto-server-parameters.md) ou [Azure CLI](howto-configure-server-parameters-using-cli.md).

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

 **Facultatif** : Si vous souhaitez utiliser la [réplication basée sur le GTID](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html), utilisez la procédure stockée suivante pour ignorer une transaction

```sql
call mysql. az_replication_skip_gtid_transaction(‘<transaction_gtid>’)
```

La procédure peut ignorer la transaction pour le GTID donné. Si le format du GTID n’est pas correct ou que la transaction GTID a déjà été exécutée, la procédure ne pourra pas s’exécuter. Le GTID d’une transaction peut être déterminé en analysant le journal binaire pour vérifier les événements de transaction. MySQL fournit un utilitaire nommé [mysqlbinlog](https://dev.mysql.com/doc/refman/5.7/en/mysqlbinlog.html) afin d’analyser les journaux binaires et d’afficher leur contenu au format texte, ce qui peut servir à identifier le GTID de la transaction.

Pour ignorer la transaction suivante après la position de réplication actuelle, utilisez la commande suivante pour identifier le GTID de la transaction suivante comme indiqué ci-dessous.

```sql
SHOW BINLOG EVENTS [IN 'log_name'] [FROM pos][LIMIT [offset,] row_count]
```

  :::image type="content" source="./media/howto-data-in-replication/show-binary-log.png" alt-text="Afficher les résultats du journal binaire":::

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [Réplication des données entrantes](concepts-data-in-replication.md) pour Azure Database pour MySQL.
