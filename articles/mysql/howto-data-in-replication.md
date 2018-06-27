---
title: Configurer la réplication de données entrantes pour répliquer des données dans Azure Database pour MySQL.
description: Cet article décrit comment configurer la réplication des données entrantes pour Azure Database pour MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/20/2018
ms.openlocfilehash: e099597eae419653a2a40c7f01ee7abbbc4657f0
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294419"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Comment configurer Azure Database pour MySQL pour la réplication de données entrantes MySQL

Dans cet article, vous allez apprendre à configurer la réplication de données entrantes dans le service Azure Database pour MySQL en configurant les serveurs réplicas et principaux. La réplication des données entrantes permet de synchroniser les données provenant d’un serveur MySQL principal qui s’exécute en local, dans des machines virtuelles ou des services de base de données hébergés par d’autres fournisseurs cloud, dans un réplica du service Azure Database pour MySQL. 

Cet article suppose que vous ayez déjà utilisé les serveurs et base de données MySQL.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Créer un serveur MySQL à utiliser en tant que réplica

1. Créer un nouveau serveur Azure Database pour MySQL

   Créez un nouveau serveur MySQL (par exemple, « replica.mysql.database.azure.com »). Reportez-vous à [Création d’un serveur Azure Database pour MySQL à l’aide du portail Azure](quickstart-create-mysql-server-database-using-azure-portal.md) pour la création de serveurs. Ce serveur est le serveur « réplica » dans la réplication de données entrantes.

   > [!IMPORTANT]
   > Ce serveur Azure Database pour MySQL doit être créé dans les niveaux de tarification à usage général ou à mémoire optimisée.
   > 

2. Créer les mêmes comptes d’utilisateur et les privilèges correspondants

   Les comptes d’utilisateurs ne sont pas répliqués à partir du serveur principal vers le serveur réplica. Si vous prévoyez de fournir aux utilisateurs un accès au serveur réplica, vous devez créer manuellement tous les comptes et privilèges correspondants sur ce nouveau serveur Azure Database pour MySQL.

## <a name="configure-the-primary-server"></a>Configurer le serveur principal
Les étapes suivantes servent à préparer et à configurer le serveur MySQL hébergé localement, dans une machine virtuelle ou par un service de base de données hébergé par d’autres fournisseurs de cloud pour la réplication de données entrantes. Ce serveur est le serveur « principal » dans la réplication de données entrantes. 

1. Activer la journalisation binaire

   Vérifiez si la journalisation binaire a été activée sur le serveur principal en exécutant la commande suivante : 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Si la variable [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) est renvoyée avec la valeur « ON », la journalisation binaire est activée sur votre serveur. 

   Si `log_bin` est renvoyé avec la valeur « OFF », activez la journalisation binaire en modifiant votre fichier my.cnf ainsi que `log_bin=ON` et redémarrez le serveur pour que la modification prenne effet.

2. Paramètres du serveur principal

   La réplication de données entrantes requiert que le paramètre `lower_case_table_names` soit cohérent entre les serveurs principaux et réplica. Par défaut, ce paramètre est 1 dans Azure Database pour MySQL. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Créer un nouveau rôle de réplication et définir une autorisation

   Créer un compte d’utilisateur sur le serveur principal configuré avec des privilèges de réplication. Cela est possible via des commandes SQL ou un outil tel que MySQL Workbench. Si vous prévoyez une réplication avec SSL, cela doit être spécifié lors de la création de l’utilisateur. Reportez-vous à la documentation MySQL pour comprendre comment [ajouter des comptes d’utilisateur](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html) sur votre serveur principal. 

   Dans les commandes ci-dessous, le nouveau rôle de réplication créé peut accéder au serveur principal à partir de n’importe quelle machine, et pas seulement de celle qui héberge le serveur principal lui-même. Cela s’effectue en spécifiant « syncuser@’%’ » dans la commande de création d’un utilisateur. Consultez la documentation MySQL pour en savoir plus sur la [définition des noms de compte](https://dev.mysql.com/doc/refman/5.7/en/account-names.html).

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


4. Définir le serveur principal sur le mode lecture seule

   Avant de commencer à vider la base de données, le serveur doit être placé en mode lecture seule. En mode lecture seule, le serveur principal ne pourra pas traiter toutes les transactions d’écriture. Évaluer l’impact sur votre entreprise et planifiez la fenêtre de lecture seule lors d’une période creuse, si nécessaire.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Obtenir le nom du fichier du journal binaire et le décalage

   Exécutez la commande [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) afin de déterminer le nom de fichier du journal binaire actuel et le décalage.
    
   ```sql
   show master status;
   ```
   Les résultats doivent être comme suit. Assurez-vous de noter le nom du fichier binaire, tel qu’il sera utilisé lors des étapes ultérieures.

   ![Résultats de l’état récapitulatif](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-primary-server"></a>Vider et restaurer le serveur principal

1. Vider de toutes les bases de données à partir du serveur principal

   Vous pouvez utiliser mysqldump pour vider les bases de données à partir de votre serveur principal. Pour plus d’informations, reportez-vous à [Dump & Restore](concepts-migrate-dump-restore.md) (Vider et restaurer). Il n’est pas nécessaire de vider les bibliothèques MySQL et de test.

2. Définir le serveur principal en mode de lecture/écriture

   Une fois que la base de données a été vidée, repassez le serveur MySQL principal en mode de lecture/écriture.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restaurer le fichier de vidage sur le nouveau serveur

   Restaurez le fichier de vidage sur le serveur créé dans le service Azure Database pour MySQL. Reportez-vous à [Dump & Restore](concepts-migrate-dump-restore.md) (Vider et restaurer) pour savoir comment restaurer un fichier de vidage sur un serveur MySQL. Si le fichier de vidage est volumineux, transférez-le vers une machine virtuelle dans Azure au sein de la même région que votre serveur réplica. Restaurez-le sur le serveur Azure Database pour MySQL à partir de la machine virtuelle.

## <a name="link-primary-and-replica-servers-to-start-data-in-replication"></a>Lier des serveurs principaux et réplicas pour démarrer la réplication de données entrantes

1. Définir le serveur principal

   Toutes les fonctions de réplication de données entrantes sont effectuées par des procédures stockées. Vous trouverez toutes les procédures dans [Data-in Replication Stored Procedures](reference-data-in-stored-procedures.md) (Procédures stockées de réplication de données entrantes). Les procédures stockées peuvent être exécutées dans l’interpréteur de commandes MySQL ou MySQL Workbench. 

   Pour lier deux serveurs et démarrer la réplication, connectez-vous au serveur réplica cible dans la base de données Azure pour le service MySQL et définissez l’instance externe comme serveur principal. Pour ce faire, utilisez la procédure stockée `mysql.az_replication_change_primary` sur le serveur Azure DB pour MySQL.

   ```sql
   CALL mysql.az_replication_change_primary('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host : nom d’hôte du serveur principal
   - master_user : nom d’utilisateur du serveur principal
   - master_password : mot de passe du serveur principal
   - master_log_file : nom de fichier du journal binaire à partir de l’exécution de `show master status`
   - master_log_pos : position du journal binaire à partir de l’exécution de `show master status`
   - master_ssl_ca : contexte du certificat d’autorité de certification. Si vous n’utilisez pas le protocole SSL, transmettez une chaîne vide.
       - Il est recommandé de transmettre ce paramètre en tant que variable. Pour plus d’informations, consultez les exemples suivants.

   **Exemples**

   *Réplication avec SSL*

   La variable `@cert` est créée en exécutant les commandes MySQL suivantes : 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   La réplication avec SSL est définie entre un serveur principal hébergé dans le domaine « companya.com » et un serveur réplica hébergé dans Azure Database pour MySQL. Cette procédure stockée est exécutée sur le réplica. 

   ```sql
   CALL mysql.az_replication_change_primary('primary.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *Réplication sans SSL*

   La réplication sans SSL est définie entre un serveur principal hébergé dans le domaine « companya.com » et un serveur réplica hébergé dans Azure Database pour MySQL. Cette procédure stockée est exécutée sur le réplica.

   ```sql
   CALL mysql.az_replication_change_primary('primary.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

2. Démarrer la réplication

   Appelez la procédure stockée `mysql.az_replication_start` pour amorcer la réplication.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Vérifier l’état de la réplication

   Appelez la commande [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) sur le serveur réplica pour afficher l’état de réplication.
    
   ```sql
   show slave status;
   ```

   Si l’état de `Slave_IO_Running` et `Slave_SQL_Running` est « yes » et la valeur de `Seconds_Behind_Master` est « 0 », cela signifie que la réplication fonctionne correctement. `Seconds_Behind_Master` indique que le retard du réplica. Si la valeur est différente de « 0 », cela signifie que le réplica traite les mises à jour. 

## <a name="other-stored-procedures"></a>Autres procédures stockées

### <a name="stop-replication"></a>Arrêter la réplication

Pour arrêter la réplication entre le serveur principal et le serveur réplica, utilisez la procédure stockée suivante :

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Supprimer la relation de réplication

Pour arrêter la réplication entre le serveur principal et le serveur réplica, utilisez la procédure stockée suivante :

```sql
CALL mysql.az_replication_remove_primary;
```

### <a name="skip-replication-error"></a>Ignorer l’erreur de réplication

Pour ignorer une erreur de réplication et autoriser la poursuite de la réplication, utilisez la procédure stockée suivante :
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur la [Réplication des données entrantes](concepts-data-in-replication.md) pour Azure Database pour MySQL. 