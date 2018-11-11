---
title: Configurer la réplication des données entrantes pour répliquer des données dans Azure Database for MariaDB.
description: Cet article décrit comment configurer la réplication des données entrantes pour Azure Database for MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f75b3e33cbd5719211bb30b22ab8d53c344b5579
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212758"
---
# <a name="how-to-configure-azure-database-for-mariadb-data-in-replication"></a>Guide pratique pour configurer la réplication des données entrantes Azure Database for MariaDB

Dans cet article, vous allez apprendre à configurer la réplication des données entrantes dans le service Azure Database for MariaDB en configurant les serveurs réplica et maître. La réplication des données entrantes permet de synchroniser les données provenant d’un serveur MariaDB maître qui s’exécute en local, dans des machines virtuelles ou dans des services de base de données hébergés par d’autres fournisseurs cloud dans un réplica au sein du service Azure Database for MariaDB. 

Cet article suppose que vous ayez déjà utilisé des serveurs et des bases de données MariaDB.

## <a name="create-a-mariadb-server-to-be-used-as-replica"></a>Créer un serveur MariaDB à utiliser comme réplica

1. Créer un serveur Azure Database for MariaDB

   Créez un serveur MariaDB (par exemple, « replica.mariadb.database.azure.com »). Reportez-vous à [Créer un serveur Azure Database for MariaDB à l’aide du portail Azure](quickstart-create-mariadb-server-database-using-azure-portal.md) pour la création de serveurs. Ce serveur est le serveur « réplica » dans la réplication de données entrantes.

   > [!IMPORTANT]
   > Le serveur Azure Database for MariaDB doit être créé dans les niveaux tarifaires Usage général ou Mémoire optimisée.
   > 

2. Créer les mêmes comptes d’utilisateur et les privilèges correspondants

   Les comptes d’utilisateur ne sont pas répliqués à partir du serveur maître vers le serveur réplica. Si vous prévoyez de fournir aux utilisateurs un accès au serveur réplica, vous devez créer manuellement tous les comptes et privilèges correspondants sur ce nouveau serveur Azure Database for MariaDB.

## <a name="configure-the-master-server"></a>Configurer le serveur maître
Les étapes suivantes préparent et configurent le serveur MariaDB hébergé localement, dans une machine virtuelle ou par un service de base de données hébergé par d’autres fournisseurs de cloud pour la réplication des données entrantes. Ce serveur est le « maître » dans la réplication de données entrantes. 

1. Activer la journalisation binaire

   Vérifiez si la journalisation binaire a été activée sur le serveur maître en exécutant la commande suivante : 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Si la variable [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) est renvoyée avec la valeur « ON », la journalisation binaire est activée sur votre serveur. 

   Si `log_bin` est renvoyé avec la valeur « OFF », activez la journalisation binaire en modifiant votre fichier my.cnf ainsi que `log_bin=ON` et redémarrez le serveur pour que la modification prenne effet.

2. Paramètres du serveur maître

   La réplication des données entrantes requiert que le paramètre `lower_case_table_names` soit cohérent entre les serveurs maître et réplica. Par défaut, ce paramètre a la valeur 1 dans Azure Database for MariaDB. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Créer un nouveau rôle de réplication et définir une autorisation

   Créez un compte d’utilisateur sur le serveur maître configuré avec des privilèges de réplication. Cela est possible via des commandes SQL ou un outil tel que MySQL Workbench. Si vous prévoyez une réplication avec SSL, cela doit être spécifié lors de la création de l’utilisateur. Pour comprendre comment [ajouter des comptes d’utilisateur](https://mariadb.com/kb/en/library/create-user/) sur votre serveur maître, reportez-vous à la documentation MariaDB. 

   Dans les commandes ci-dessous, le nouveau rôle de réplication créé peut accéder au serveur maître à partir de n’importe quelle machine, et pas seulement de celle qui héberge le serveur maître. Pour cela, spécifiez « syncuser\@'%' » dans la commande CREATE USER. Consultez la documentation MariaDB pour en savoir plus sur la [définition des noms de compte](https://mariadb.com/kb/en/library/create-user/#account-names).

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

   Pour créer le rôle de réplication dans MySQL Workbench, ouvrez le panneau **Utilisateurs et privilèges** à partir du panneau **Gestion**. Cliquez ensuite sur **Ajouter un compte**. 
 
   ![Utilisateurs et privilèges](./media/howto-data-in-replication/users_privileges.png)

   Tapez le nom d’utilisateur dans le champ **Nom de connexion**. 

   ![Synchroniser l’utilisateur](./media/howto-data-in-replication/syncuser.png)
 
   Cliquez sur le panneau **Rôles administratifs**, puis sélectionnez **Subordonné de réplication** dans la liste **Privilèges globaux**. Cliquez ensuite sur **Appliquer** pour créer le rôle de réplication.

   ![Subordonné de réplication](./media/howto-data-in-replication/replicationslave.png)


4. Définir le serveur maître en mode en lecture seule

   Avant de commencer à vider la base de données, le serveur doit être placé en mode lecture seule. En mode lecture seule, le serveur maître ne peut traiter aucune transaction d’écriture. Évaluer l’impact sur votre entreprise et planifiez la fenêtre de lecture seule lors d’une période creuse, si nécessaire.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Obtenir le nom du fichier du journal binaire et le décalage

   Exécutez la commande [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) afin de déterminer le nom de fichier du journal binaire actuel et le décalage.
    
   ```sql
   show master status;
   ```
   Les résultats doivent être comme suit. Assurez-vous de noter le nom du fichier binaire, tel qu’il sera utilisé lors des étapes ultérieures.

   ![Résultats de l’état récapitulatif](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Vider et restaurer le serveur maître

1. Vider toutes les bases de données du serveur maître

   Vous pouvez utiliser mysqldump pour vider les bases de données de votre serveur maître. Pour plus d’informations, reportez-vous à [Dump & Restore](howto-migrate-dump-restore.md) (Vider et restaurer). Il n’est pas nécessaire de vider les bibliothèques MySQL et de test.

2. Définir le serveur maître en mode lecture/écriture

   Une fois la base de données sauvegardée, remettez le serveur MariaDB maître en mode de lecture/écriture.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restaurer le fichier de vidage sur le nouveau serveur

   Restaurez le fichier de vidage sur le serveur créé dans le service Azure Database for MariaDB. Reportez-vous à [Sauvegarder et restaurer](howto-migrate-dump-restore.md) pour savoir comment restaurer un fichier de vidage sur un serveur MariaDB. Si le fichier de vidage est volumineux, transférez-le vers une machine virtuelle dans Azure au sein de la même région que votre serveur réplica. Restaurez-le sur le serveur Azure Database for MariaDB à partir de la machine virtuelle.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Lier les serveurs maître et réplica pour démarrer la réplication des données entrantes

1. Définir un serveur maître

   Toutes les fonctions de réplication de données entrantes sont effectuées par des procédures stockées. Vous trouverez toutes les procédures dans [Data-in Replication Stored Procedures](reference-data-in-stored-procedures.md) (Procédures stockées de réplication de données entrantes). Les procédures stockées peuvent être exécutées dans l’interpréteur de commandes MySQL ou MySQL Workbench.

   Pour lier deux serveurs et démarrer une réplication, connectez-vous au serveur réplica cible dans le service Azure Database for MariaDB, puis définissez l’instance externe comme serveur maître. Pour ce faire, utilisez la procédure stockée `mysql.az_replication_change_master` sur le serveur Azure DB for MariaDB.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host : nom d’hôte du serveur maître
   - master_user : nom d’utilisateur pour le serveur maître
   - master_password : mot de passe pour le serveur maître
   - master_log_file : nom de fichier du journal binaire à partir de l’exécution de `show master status`
   - master_log_pos : position du journal binaire à partir de l’exécution de `show master status`
   - master_ssl_ca : contexte du certificat d’autorité de certification. Si vous n’utilisez pas le protocole SSL, transmettez une chaîne vide.
       - Il est recommandé de transmettre ce paramètre en tant que variable. Pour plus d’informations, consultez les exemples suivants.

   **Exemples**

   *Réplication avec SSL*

   La variable `@cert` est créée en exécutant les commandes suivantes : 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   La réplication avec SSL est définie entre un serveur maître hébergé dans le domaine « companya.com » et un serveur réplica hébergé dans Azure Database for MariaDB. Cette procédure stockée est exécutée sur le réplica. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
   ```
   *Réplication sans SSL*

   La réplication sans SSL est définie entre un serveur maître hébergé dans le domaine « companya.com » et un serveur réplica hébergé dans Azure Database for MariaDB. Cette procédure stockée est exécutée sur le réplica.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
   ```

2. Démarrer la réplication

   Appelez la procédure stockée `mysql.az_replication_start` pour amorcer la réplication.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Vérifier l’état de la réplication

   Appelez la commande [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) sur le serveur réplica pour afficher l’état de réplication.
    
   ```sql
   show slave status;
   ```

   Si l’état de `Slave_IO_Running` et `Slave_SQL_Running` est « yes » et la valeur de `Seconds_Behind_Master` est « 0 », cela signifie que la réplication fonctionne correctement. `Seconds_Behind_Master` indique que le retard du réplica. Si la valeur est différente de « 0 », cela signifie que le réplica traite les mises à jour. 

## <a name="other-stored-procedures"></a>Autres procédures stockées

### <a name="stop-replication"></a>Arrêter la réplication

Pour arrêter la réplication entre le serveur maître et le serveur réplica, utilisez la procédure stockée suivante :

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Supprimer la relation de réplication

Pour supprimer la relation entre le serveur maître et le serveur réplica, utilisez la procédure stockée suivante :

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Ignorer l’erreur de réplication

Pour ignorer une erreur de réplication et autoriser la poursuite de la réplication, utilisez la procédure stockée suivante :
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Étapes suivantes
- Découvrez plus en détail la [réplication des données entrantes](concepts-data-in-replication.md) pour Azure Database for MariaDB.
