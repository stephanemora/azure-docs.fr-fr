---
title: Mise à niveau par vidage et restauration - Azure Database pour PostgreSQL - Serveur unique
description: Décrit deux méthodes pour vider et restaurer des bases de données afin de migrer vers une version plus récente d’Azure Database pour PostgreSQL - Serveur unique.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 26154f4501daba373f1f8b108f1ee7105b1b194f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93294199"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>Mettre à niveau votre base de données PostgreSQL par vidage et restauration

Dans Azure Database pour PostgreSQL - Serveur unique, il est recommandé de mettre à niveau le moteur de base de données PostgreSQL vers une version majeure supérieure à l’aide de l’une des méthodes suivantes :
* Méthode hors connexion utilisant PostgreSQL [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) et [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). Dans cette méthode, vous effectuez tout d’abord le vidage à partir de votre serveur source, puis vous restaurez ce vidage sur le serveur cible.
* Méthode en ligne utilisant [**Database Migration Service**](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal) (DMS). Cette méthode permet de synchroniser la base de données cible avec la source, et vous pouvez choisir le moment du basculement. Toutefois, il existe des prérequis et des restrictions à prendre en compte. 

Vous pouvez utiliser la recommandation suivante pour choisir entre les méthodes en ligne et hors connexion pour effectuer des mises à niveau de version majeures.

| **Base de données** | **Vider/restaurer (hors connexion)** | **DMS (en ligne)** |
| ------ | :------: | :-----: |
| Vous avez une petite base de données et pouvez vous permettre un temps d’arrêt afin de la mettre à niveau  | X | |
| Petites bases de données (< 10 Go)  | X | X | 
| Petites et moyennes bases de données (10 Go – 100 Go) | X | X |
| Bases de données volumineuses (> 100 Go) |  | X |
| Peut prendre un temps d’arrêt pour la mise à niveau (quelle que soit la taille de la base de données) | X |  |
| Peut respecter les [prérequis](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal#prerequisites) DMS, notamment un redémarrage ? |  | X |
| Peut éviter les DDL et les tables non journalisées pendant le processus de mise à niveau ? | |  X |

Ce guide pratique fournit deux exemples de méthodes pour mettre à niveau vos bases de données à l’aide des commandes pg_dump et pg_restore de PostgreSQL. Le processus de ce document est appelé **mise à niveau** bien que la base de données soit **migrée** du serveur source vers le serveur cible. 

> [!NOTE]
> Le vidage et la restauration PostgreSQL peuvent être effectués de plusieurs façons. Vous pouvez choisir d’utiliser des méthodes différentes de celles mentionnées dans ce document. Par exemple, pour effectuer un vidage suivi d’une restauration à partir d’un client PostgreSQL, consultez la [documentation](./howto-migrate-using-dump-and-restore.md) pour obtenir des informations détaillées sur la procédure et les meilleures pratiques. Pour plus d’informations sur la syntaxe de vidage et de restauration avec des paramètres supplémentaires, consultez les articles [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) et [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-postgresql"></a>Prérequis pour l’utilisation du vidage et de la restauration avec Azure PostgreSQL
 
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- Une base de données source exécutant 9.5, 9.6 ou 10 (Azure Database pour PostgreSQL – Serveur unique)
- Un serveur de base de données cible avec un [serveur Azure Database pour PostgreSQL](quickstart-create-server-database-portal.md) de la version majeure PostgreSQL requise. 
- Un système client (Linux) sur lequel PostgreSQL est installé et qui a les utilitaires de ligne de commande [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) et [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) installés. 
- Vous pouvez également utiliser [Azure Cloud Shell](https://shell.azure.com) ou cliquer sur Azure Cloud Shell dans la barre de menus en haut à droite du [portail Azure](https://portal.azure.com). Vous devez vous connecter à votre compte `az login` avant d’exécuter les commandes de vidage et de restauration.
- L’emplacement de votre client PostgreSQL, comme une machine virtuelle s’exécutant de préférence dans la même région que les serveurs source et cible). 

## <a name="additional-details-and-considerations"></a>Détails et considérations supplémentaires
- Vous pouvez trouver la chaîne de connexion aux bases de données source et cible en cliquant sur « Chaînes de connexion » dans le portail. 
- Il se peut que vous exécutiez plusieurs bases de données sur votre serveur. Vous pouvez trouver la liste des bases de données en vous connectant à votre serveur source et en exécutant `\l`.
- Créez les bases de données correspondantes dans le serveur de base de données cible.
- Vous pouvez ignorer la mise à niveau de `azure_maintenance` ou des bases de données modèles.
- Reportez-vous aux tableaux ci-dessus pour déterminer si la base de données convient à ce mode de migration.
- Si vous souhaitez utiliser Azure Cloud Shell, la session expire après 20 minutes. Si la taille de votre base de données est < 10 Go, il est conseillé de terminer la mise à niveau sans dépasser le délai d’attente. Sinon, vous devrez peut-être conserver la session ouverte par d’autres moyens, par exemple en appuyant sur la touche <Enter> une fois toutes les 10-15 minutes. 

> [!TIP] 
> - Si vous utilisez le même mot de passe pour la base de données source et la base de données cible, vous pouvez définir la variable d’environnement `PGPASSWORD=yourPassword`.  Vous n’avez alors pas besoin de fournir le mot de passe à chaque fois que vous exécutez des commandes telles que psql, pg_dump et pg_restore.  De même, vous pouvez configurer des variables supplémentaires comme `PGUSER`, `PGSSLMODE`, etc. Consultez [Variables d’environnement PostgreSQL](https://www.postgresql.org/docs/11/libpq-envars.html).
>  
> - Si votre serveur PostgreSQL nécessite des connexions TLS/SSL (qui sont activées par défaut sur les serveurs Azure Database pour PostgreSQL), définissez une variable d’environnement `PGSSLMODE=require` pour que l’outil pg_restore se connecte avec TLS. Sans connexion TLS, l’erreur suivante peut s’afficher : `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> - Sur la ligne de commande Windows, exécutez la commande `SET PGSSLMODE=require` avant d’exécuter la commande pg_restore. Dans Linux ou Bash, exécutez la commande `export PGSSLMODE=require` avant d’exécuter la commande pg_restore.

## <a name="example-database-used-in-this-guide"></a>Exemple de base de données utilisé dans ce guide

 | **Description** | **Valeur** |
 | ------- | ------- |
 | Serveur source (v9.5) | pg-95.postgres.database.azure.com |
 | Base de données source | bench5gb |
 | Taille de la base de données source | 5 Go |
 | Nom d’utilisateur source | pg@pg-95 |
 | Serveur cible (v11) | pg-11.postgres.database.azure.com |
 | Base de données cible | bench5gb |
 | Nom d’utilisateur cible | pg@pg-11 |

## <a name="method-1-upgrade-with-streaming-backups-to-the-target"></a>Méthode 1 : Mettre à niveau avec le streaming de sauvegardes vers la cible 

Dans cette méthode, le vidage entier de la base de données est transmis directement au serveur de base de données cible et ne stocke pas le vidage dans le client. Par conséquent, elle peut être utilisée avec un client disposant d’un stockage limité et peut même être exécutée à partir d’Azure Cloud Shell. 

1. Assurez-vous que la base de données existe sur le serveur cible à l’aide de la commande `\l`. Si la base de données n’existe pas, créez-la.
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```bash
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. Exécutez le vidage et la restauration en tant que ligne de commande unique à l’aide d’un canal. 
    ```azurecli-interactive
    pg_dump -Fc -v --mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    Par exemple,

    ```azurecli-interactive
    pg_dump -Fc -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. Une fois le processus de mise à niveau (migration) terminé, vous pouvez tester votre application avec le serveur cible. 
4. Répétez ce processus pour toutes les bases de données sur le serveur.

 Le tableau suivant illustre le temps nécessaire à la mise à niveau avec cette méthode. Les données sont renseignées à l’aide de [pgbench](https://www.postgresql.org/docs/10/pgbench.html). Comme votre base de données peut avoir un nombre différent d’objets avec des tailles variées par rapport aux tables et index générés par pgbench, il est vivement recommandé de tester le vidage et la restauration de votre base de données pour comprendre le temps réel nécessaire à la mise à niveau de votre base de données. 

| **Taille de la base de données** | **Durée approx. nécessaire** | 
| ----- | ------ |
| 1 Go  | 1-2 minutes |
| 5 Go | 8-10 minutes |
| 10 Go | 15-20 minutes |
| 50 Go | 1-1,5 heures |
| 100 Go | 2,5-3 heures|
   
## <a name="method-2-upgrade-with-parallel-dump-and-restore"></a>Méthode 2 : Mettre à niveau avec un vidage et une restauration parallèles 

Cette méthode est utile si vous avez peu de tables volumineuses dans la base de données et que vous souhaitez paralléliser le processus de vidage et de restauration pour cette base de données. Vous avez besoin d’un espace de stockage sur disque local suffisant pour prendre en charge les vidages de sauvegarde de vos bases de données. Ce processus de vidage et de restauration parallèles réduit le temps nécessaire pour effectuer l’ensemble de la migration/mise à niveau. Par exemple, la migration de la base de données pgbench 50 Go qui a nécessité entre 1 h et 1,5 h a été effectuée en moins de 30 minutes.

1. Pour chaque base de données dans votre serveur source, créez une base de données correspondante sur le serveur cible.

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   Par exemple,
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. Exécutez la commande pg_dump dans un format de répertoire avec le nombre de travaux = 4 (nombre de tables dans la base de données). Avec un niveau de calcul plus élevé et plus de tables, vous pouvez passer à un nombre plus élevé. Cette commande pg_dump crée un répertoire pour stocker les fichiers compressés pour chaque travail.

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    Par exemple,
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. Ensuite, restaurez la sauvegarde sur le serveur cible.
    ```bash
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
- Si vous souhaitez utiliser le même point de terminaison de base de données que le serveur source, alors après avoir supprimé votre ancien serveur de base de données source, vous pouvez créer un réplica en lecture avec l’ancien nom du serveur de base de données. Une fois l’état stable établi, vous pouvez arrêter le réplica, ce qui permet de promouvoir le réplica du serveur en tant que serveur indépendant. Pour plus d’informations, consultez [Réplication](./concepts-read-replicas.md).
- Pensez à tester et valider ces commandes dans un environnement de test avant de les utiliser en production.
