---
title: Mise à niveau par vidage et restauration – Azure Database pour PostgreSQL
description: Décrit les méthodes de mise à niveau hors connexion utilisant la sauvegarde et la restauration de bases de données pour opérer une migration vers une version plus récente d’Azure Database pour PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: 7e8e1db98ac79c2be6dbb399a14368ce3e2f898c
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123033494"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>Mettre à niveau votre base de données PostgreSQL par vidage et restauration

>[!NOTE]
> Les concepts expliqués dans cette documentation s’appliquent tant à Azure Database pour PostgreSQL – Serveur unique qu’à Azure Database pour PostgreSQL – Serveur flexible (préversion). 

Vous pouvez mettre à niveau votre serveur PostgreSQL déployé dans Azure Database pour PostgreSQL en migrant vos bases de données vers un serveur de version majeure plus récent à l’aide des méthodes suivantes.
* Méthode **hors connexion** utilisant les utilitaires PostgreSQL [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) et [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html), ce qui entraîne un temps d’arrêt pour la migration des données. Ce document traite de cette méthode de mise à niveau/de migration.
* Méthode **en ligne** utilisant [Database Migration Service](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) (DMS). Cette méthode permet de réduire les temps d’arrêt et de maintenir la synchronisation de la base de données cible avec la source, et vous pouvez choisir le moment du basculement. Toutefois, il existe quelques conditions préalables et restrictions à prendre en compte pour l’utilisation de DMS. Pour plus de détails, consultez la [documentation de DMS](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md). 

 Le tableau suivant fournit quelques recommandations basées sur les tailles et les scénarios de base de données.

| **Base de données/scénario** | **Vider/restaurer (hors connexion)** | **DMS (en ligne)** |
| ------ | :------: | :-----: |
| Vous avez une petite base de données et pouvez vous permettre un temps d’arrêt afin de la mettre à niveau  | X | |
| Petites bases de données (< 10 Go)  | X | X | 
| Petites et moyennes bases de données (10 Go – 100 Go) | X | X |
| Bases de données volumineuses (> 100 Go) |  | X |
| Peut prendre un temps d’arrêt pour la mise à niveau (quelle que soit la taille de la base de données) | X |  |
| Peut respecter les [prérequis](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md#prerequisites) de DMS, notamment un redémarrage ? |  | X |
| Peut éviter les DDL et les tables non journalisées pendant le processus de mise à niveau ? | |  X |

Ce guide fournit quelques méthodologies et exemples de migration hors connexion pour montrer la façon dont vous pouvez migrer de votre serveur source vers le serveur cible qui exécute une version plus récente de PostgreSQL.

> [!NOTE]
> Le vidage et la restauration PostgreSQL peuvent être effectués de plusieurs façons. Vous pouvez choisir de migrer à l’aide de l’une des méthodes fournies dans ce guide ou choisir d’autres méthodes pour répondre à vos besoins. Pour plus d’informations sur la syntaxe de vidage et de restauration avec des paramètres supplémentaires, consultez les articles [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) et [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-database-for-postgresql"></a>Prérequis pour l’utilisation de la sauvegarde et de la restauration avec Azure Database pour PostgreSQL
 
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :

- Un serveur de base de données PostgreSQL **source** exécutant une version antérieure du moteur que vous souhaitez mettre à niveau.
- Un serveur de base de données PostgreSQL **cible** avec la version majeure souhaitée d’[Azure Database pour PostgreSQL – Serveur unique](quickstart-create-server-database-portal.md) ou d’[Azure Database pour PostgreSQL – Serveur flexible](./flexible-server/quickstart-create-server-portal.md). 
- Un système client PostgreSQL pour exécuter les commandes de sauvegarde et de restauration. Il est recommandé d’utiliser la version de base de données la plus récente. Par exemple, si vous effectuez une mise à niveau de PostgreSQL version 9.6 vers 11, utilisez le client PostgreSQL version 11. 
  - Il peut s’agir d’un client Linux ou Windows sur lequel PostgreSQL et les utilitaires de ligne de commande [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) et [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) sont installés. 
  - Vous pouvez également utiliser [Azure Cloud Shell](https://shell.azure.com) ou cliquer sur Azure Cloud Shell dans la barre de menus en haut à droite du [portail Azure](https://portal.azure.com). Vous devez vous connecter à votre compte `az login` avant d’exécuter les commandes de vidage et de restauration.
- Votre client PostgreSQL s’exécute de préférence dans la même région que les serveurs source et cible. 


## <a name="additional-details-and-considerations"></a>Détails et considérations supplémentaires
- Vous pouvez trouver la chaîne de connexion aux bases de données source et cible en cliquant sur « Chaînes de connexion » dans le portail. 
- Il se peut que vous exécutiez plusieurs bases de données sur votre serveur. Vous pouvez trouver la liste des bases de données en vous connectant à votre serveur source et en exécutant `\l`.
- Créez les bases de données correspondantes dans le serveur de base de données cible ou ajoutez l'option `-C` à la commande `pg_dump` qui crée les bases de données.
- Vous ne devez pas mettre à niveau `azure_maintenance` ou les bases de données de modèles. Si vous avez apporté des modifications aux bases de données de modèles, vous pouvez choisir de migrer les modifications ou d’effectuer ces modifications dans la base de données cible.
- Reportez-vous aux tableaux ci-dessus pour déterminer si la base de données convient à ce mode de migration.
- Si vous souhaitez utiliser Azure Cloud Shell, notez que la session expire après 20 minutes. Si la taille de votre base de données est < 10 Go, vous pourrez peut-être terminer la mise à niveau sans dépasser le délai d’expiration de la session. Sinon, vous devrez peut-être conserver la session ouverte par d’autres moyens, par exemple en appuyant sur une touche une fois toutes les 10-15 minutes. 


## <a name="example-database-used-in-this-guide"></a>Exemple de base de données utilisé dans ce guide

Dans ce guide, les serveurs source et cible et les noms de base de données suivants sont utilisés pour illustrer des exemples.

 | **Description** | **Valeur** |
 | ------- | ------- |
 | Serveur source (v9.5) | pg-95.postgres.database.azure.com |
 | Base de données source | bench5gb |
 | Taille de la base de données source | 5 Go |
 | Nom d’utilisateur source | pg@pg-95 |
 | Serveur cible (v11) | pg-11.postgres.database.azure.com |
 | Base de données cible | bench5gb |
 | Nom d’utilisateur cible | pg@pg-11 |

>[!NOTE]
> Le serveur flexible prend en charge PostgreSQL versions 11 et ultérieures. Par ailleurs, le nom d’utilisateur du serveur flexible ne doit pas contenir @dbservername.

## <a name="upgrade-your-databases-using-offline-migration-methods"></a>Mettre à niveau vos bases de données à l’aide de méthodes de migration hors connexion
Vous pouvez choisir d’utiliser l’une des méthodes décrites dans cette section pour vos mises à niveau. Vous pouvez utiliser les conseils suivants lors de l’exécution des tâches.

- Si vous utilisez le même mot de passe pour la base de données source et la base de données cible, vous pouvez définir la variable d’environnement `PGPASSWORD=yourPassword`.  Vous n’avez alors pas besoin de fournir le mot de passe à chaque fois que vous exécutez des commandes telles que psql, pg_dump et pg_restore.  De même, vous pouvez configurer des variables supplémentaires comme `PGUSER`, `PGSSLMODE`, etc. Consultez [Variables d’environnement PostgreSQL](https://www.postgresql.org/docs/11/libpq-envars.html).
  
- Si votre serveur PostgreSQL nécessite des connexions TLS/SSL (qui sont activées par défaut sur les serveurs Azure Database pour PostgreSQL), définissez une variable d’environnement `PGSSLMODE=require` pour que l’outil pg_restore se connecte avec TLS. Sans connexion TLS, l’erreur suivante peut s’afficher : `FATAL:  SSL connection is required. Please specify SSL options and retry.`

- Sur la ligne de commande Windows, exécutez la commande `SET PGSSLMODE=require` avant d’exécuter la commande pg_restore. Dans Linux ou Bash, exécutez la commande `export PGSSLMODE=require` avant d’exécuter la commande pg_restore.

>[!Important]
> Les étapes et les méthodes fournies dans ce document donnent des exemples de commandes pg_dump/pg_restore et ne représentent pas toutes les façons possibles d’effectuer des mises à niveau. Il est conseillé de tester et valider les commandes dans un environnement de test avant de les utiliser en production.

### <a name="migrate-the-roles"></a>Migrer les rôles

Les rôles (utilisateurs) sont des objets globaux et doivent être migrés séparément vers le nouveau cluster avant de restaurer la base de données. Vous pouvez utiliser l’option `pg_dumpall` binaire avec -r (-roles-only) pour les vider.
Pour vider tous les rôles du serveur source :

```azurecli-interactive
pg_dumpall -r --host=mySourceServer --port=5432 --username=myUser -- dbname=mySourceDB > roles.sql
```

et restaurer à l’aide de psql sur le serveur cible :

```azurecli-interactive
psql -f roles.sql --host=myTargetServer --port=5432 --username=myUser
```

Le script de vidage ne doit pas être s’exécuter complètement sans erreurs. En particulier, étant donné que le script émet CREATE ROLE pour chaque rôle existant dans le cluster source, vous êtes assuré d’obtenir une erreur « le rôle existe déjà » pour le super-utilisateur de démarrage tel que azure_pg_admin ou azure_superuser. Cette erreur est inoffensive et peut être ignorée. L’utilisation de l'option `--clean` est susceptible de générer des messages d’erreur inoffensifs supplémentaires sur les objets inexistants, bien que vous puissiez les réduire en ajoutant `--if-exists`.


### <a name="method-1-using-pg_dump-and-psql"></a>Méthode 1 : utilisation de pg_dump et psql

Cette méthode implique deux étapes. Tout d’abord de vider un fichier de SQL du serveur source à l’aide de `pg_dump`. La deuxième étape consiste à importer le fichier sur le serveur cible à l’aide de `psql`. Pour plus d’informations, consultez la documentation [Migrer à l’aide de l’exportation et de l’importation](howto-migrate-using-export-and-import.md).

### <a name="method-2-using-pg_dump-and-pg_restore"></a>Méthode 2 : utilisation de pg_dump et pg_restore

Dans cette méthode de mise à niveau, vous créez d’abord un vidage du serveur source à l’aide de `pg_dump`. Vous restaurez ensuite ce fichier de vidage sur le serveur cible à l’aide de `pg_restore`. Pour plus d’informations, consultez la documentation [Migrer à l’aide du vidage et de la restauration](howto-migrate-using-dump-and-restore.md). 

### <a name="method-3-using-streaming-the-dump-data-to-the-target-database"></a>Méthode 3 : utilisation de la diffusion en continu des données de vidage vers la base de données cible

Si vous n’avez pas de client PostgreSQL ou si vous souhaitez utiliser Azure Cloud Shell, vous pouvez utiliser cette méthode. La sauvegarde de la base de données est transmise directement au serveur de base de données cible et n’est pas stockée dans le client. Par conséquent, elle peut être utilisée avec un client disposant d’un stockage limité et peut même être exécutée à partir d’Azure Cloud Shell. 

1. Assurez-vous que la base de données existe sur le serveur cible à l’aide de la commande `\l`. Si la base de données n’existe pas, créez-la.
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```SQL
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. Exécutez le vidage et la restauration en tant que ligne de commande unique à l’aide d’un canal. 
    ```azurecli-interactive
    pg_dump -Fc --host=mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore  --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    Par exemple,

    ```azurecli-interactive
    pg_dump -Fc --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. Une fois le processus de mise à niveau (migration) terminé, vous pouvez tester votre application avec le serveur cible. 
4. Répétez ce processus pour toutes les bases de données sur le serveur.

 À titre d’exemple, le tableau suivant illustre le temps nécessaire à la migration à l’aide de la méthode de diffusion en continu de la sauvegarde. Les exemples de données sont renseignés à l’aide de [pgbench](https://www.postgresql.org/docs/10/pgbench.html). Comme votre base de données peut avoir un nombre différent d’objets avec des tailles variées par rapport aux tables et index générés par pgbench, il est vivement recommandé de tester le vidage et la restauration de votre base de données pour comprendre le temps réel nécessaire à la mise à niveau de votre base de données. 

| **Taille de la base de données** | **Durée approx. nécessaire** | 
| ----- | ------ |
| 1 Go  | 1-2 minutes |
| 5 Go | 8-10 minutes |
| 10 Go | 15-20 minutes |
| 50 Go | 1-1,5 heures |
| 100 Go | 2,5-3 heures|
   
### <a name="method-4-using-parallel-dump-and-restore"></a>Méthode 4 : utilisation du vidage et de la restauration parallèles 

Vous pouvez envisager cette méthode si vous avez peu de tables volumineuses dans votre base de données et que vous souhaitez paralléliser le processus de sauvegarde et de restauration pour cette base de données. Vous avez également besoin d’un espace de stockage suffisant dans votre système client pour pouvoir effectuer des copies de sauvegarde. Ce processus de vidage et de restauration parallèles réduit le temps nécessaire pour mener à bien l’ensemble de la migration. Par exemple, la migration de la base de données pgbench 50 Go qui a nécessité entre 1 h et 1 h 30 en utilisant les méthodes 1 et 2 a été effectuée en moins de 30 minutes à l’aide de cette méthode.

1. Pour chaque base de données dans votre serveur source, créez une base de données correspondante sur le serveur cible.

    ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    ```

    ```SQL
    postgres> create database myDB;
   ```

   Par exemple,
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"
    psql (12.3 (Ubuntu 12.3-1.pgdg18.04+1), server 13.3)
    SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres> create database bench5gb;
    postgres> \q
    ```

2. Exécutez la commande pg_dump dans un format de répertoire avec le nombre de travaux = 4 (nombre de tables dans la base de données). Avec un niveau de calcul plus élevé et plus de tables, vous pouvez passer à un nombre plus élevé. Cette commande pg_dump crée un répertoire pour stocker les fichiers compressés pour chaque travail.

    ```azurecli-interactive
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    Par exemple,
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. Ensuite, restaurez la sauvegarde sur le serveur cible.
    ```azurecli-interactive
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    Par exemple,
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> Le processus mentionné dans ce document peut également être utilisé pour mettre à niveau votre Azure Database for PostgreSQL - Serveur flexible, qui est en préversion. La principale différence est que la chaîne de connexion pour le serveur flexible cible n’est pas `@dbName`.  Par exemple, si le nom d’utilisateur est `pg`, le nom d’utilisateur du serveur unique dans la chaîne de connexion est `pg@pg-95`, tandis qu’avec le serveur flexible, vous pouvez simplement utiliser `pg`.

## <a name="next-steps"></a>Étapes suivantes

- Une fois que vous êtes satisfait de la fonction de la base de données cible, vous pouvez supprimer votre ancien serveur de base de données. 
- Uniquement pour Azure Database pour PostgreSQL – Serveur unique. Si vous souhaitez utiliser le même point de terminaison de base de données que le serveur source, alors après avoir supprimé votre ancien serveur de base de données source, vous pouvez créer un réplica en lecture avec l’ancien nom du serveur de base de données. Une fois l’état de réplication stable établi, vous pouvez arrêter le réplica, ce qui permet de promouvoir le serveur réplica en tant que serveur indépendant. Pour plus d’informations, consultez [Réplication](./concepts-read-replicas.md).

>[!Important] 
> Il est vivement recommandé de tester la version de PostgreSQL mise à niveau avant de l’utiliser directement pour la production. Cela comprend la comparaison des paramètres de serveur entre la source de l’ancienne version et la cible de la nouvelle version. Assurez-vous que les paramètres sont identiques et vérifiez les éventuels nouveaux paramètres ajoutés à la nouvelle version. Les différences entre les versions sont indiquées [ici](https://www.postgresql.org/docs/release/).