---
title: Sauvegarder et récupérer une base de données Oracle Database 19c sur une machine virtuelle Linux Azure à l'aide du service Sauvegarde Azure
description: Apprenez à sauvegarder et récupérer une base de données Oracle Database 19c à l'aide du service Sauvegarde Azure.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 9ed157dad020c69f3243db591ddf494853d793eb
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110087379"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-backup"></a>Sauvegarder et récupérer une base de données Oracle Database 19c sur une machine virtuelle Linux Azure à l'aide du service Sauvegarde Azure

Cet article explique comment utiliser le service Sauvegarde Azure pour prendre des captures instantanées des disques d'une machine virtuelle, ainsi que des fichiers de base de données et de la zone de récupération rapide. Le service Sauvegarde Azure vous permet de prendre des captures instantanées complètes des disques. Celles-ci font office de sauvegardes et sont stockées dans un [coffre Recovery Services](../../../backup/backup-azure-recovery-services-vault-overview.md).  Le service Sauvegarde Azure fournit également des sauvegardes cohérentes avec les applications, ce qui garantit qu'aucun correctif supplémentaire n'est requis pour restaurer les données. La restauration de données cohérentes avec les applications réduit le délai de restauration, ce qui permet de rétablir rapidement le fonctionnement normal.

> [!div class="checklist"]
>
> * Sauvegardez la base de données en bénéficiant d'une sauvegarde cohérente avec les applications
> * Restaurez et récupérez la base de données à partir d'un point de récupération
> * Restaurez la machine virtuelle à partir d'un point de récupération

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

Pour bénéficier du processus de sauvegarde et de récupération, vous devez commencer par créer une machine virtuelle Linux sur laquelle une instance d'Oracle Database 19c est installée. L'image de la Place de marché actuellement utilisée pour créer la machine virtuelle est **Oracle:oracle-database-19-3:oracle-database-19-0904:latest**. Suivez les étapes du [Guide de démarrage rapide Créer une base de données Oracle](./oracle-database-quick-create.md) afin de créer une base de données Oracle qui vous permettra d'utiliser ce tutoriel.


## <a name="prepare-the-environment"></a>Préparer l’environnement

Pour préparer l'environnement, procédez comme suit :

1. Connectez-vous à la machine virtuelle.
1. Préparez la base de données.

### <a name="connect-to-the-vm"></a>Connexion à la machine virtuelle

1. Pour créer une session Secure Shell (SSH) avec la machine virtuelle, utilisez la commande suivante. Remplacez la combinaison d’adresse IP et de nom d’hôte par la valeur `<publicIpAddress>` pour votre machine virtuelle.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
1. Basculez vers l'utilisateur *racine* :

   ```bash
   sudo su -
   ```
    
1. Ajoutez l'utilisateur oracle au fichier */etc/sudoers* :

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

### <a name="prepare-the-database"></a>Préparation de la base de données

Cette étape part du principe que vous disposez d’une instance Oracle nommée `test` exécutée sur une machine virtuelle nommée `vmoracle19c`.

1. Basculez vers l'utilisateur *oracle* :
 
   ```bash
    sudo su - oracle
    ```
    
1. Avant de vous connecter, vous devez définir la variable d'environnement ORACLE_SID :
    
    ```bash
    export ORACLE_SID=test;
    ```

    Vous devez également ajouter la variable ORACLE_SID au fichier `.bashrc` des utilisateurs `oracle` pour les prochaines connexions à l'aide de la commande suivante :

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
1. Si ce n'est déjà fait, démarrez l'écouteur Oracle :

    ```output
    $ lsnrctl start
    ```

    La sortie doit être semblable à l’exemple suivant :

    ```bash
    LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 18-SEP-2020 03:23:49

    Copyright (c) 1991, 2019, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    System parameter file is /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))

    Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    Start Date                18-SEP-2020 03:23:49
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Parameter File   /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
    The listener supports no services
    The command completed successfully
    ```

1.  Créez l’emplacement de la zone de récupération rapide (FRA) de la base de données. La FRA est un emplacement de stockage centralisé pour les fichiers de sauvegarde et de récupération :

    ```bash
    mkdir /u02/fast_recovery_area
    ```

1. Créer un partage de fichiers Azure Files pour les fichiers journaux de restauration archivés Oracle

   Les fichiers journaux de restauration de l’archivage de la base de données Oracle jouent un rôle essentiel dans la récupération de la base de données, car ils stockent les transactions validées nécessaires à la restauration par progression à partir d’un instantané de la base de données pris dans le passé. En mode archivelog, la base de données archive le contenu des fichiers journaux de restauration en ligne lorsqu’ils sont saturés et basculés. En association avec une sauvegarde, ils sont nécessaires pour obtenir une récupération jusqu’à une date et heure lorsque la base de données a été perdue.  
   
   Oracle permet d’archiver les fichiers journaux de restauration dans différents emplacements. La meilleure pratique métier recommande qu’au moins l’une de ces destinations se trouve sur un stockage à distance, afin qu’elle soit distincte du stockage hôte et protégée par des instantanés indépendants. Azure Files est un outil adapté à ces exigences.

   Un partage de fichiers Azure Files est un stockage qui peut être attaché à une machine virtuelle Linux ou Windows en tant que composant standard du système de fichiers, à l’aide des protocoles SMB ou NFS (préversion). Pour configurer un partage de fichiers Azure Files sur Linux, à l’aide du protocole SMB 3.0 (recommandé), pour une utilisation en tant que stockage des journaux d’archivage, suivez le [guide Utiliser Azure Files avec Linux](../../../storage/files/storage-how-to-use-files-linux.md). 
   
   Une fois que le partage Azure Files est configuré et monté sur la machine virtuelle Linux, par exemple dans un répertoire de point de montage nommé `/backup`, il peut être ajouté comme destination de fichier journal d’archivage supplémentaire dans la base de données comme suit :

   Vérifiez d’abord le nom du SID Oracle.
   ```bash
   echo $ORACLE_SID
   test
   ```

   Créez un sous-répertoire nommé d’après le SID de votre base de données. Dans cet exemple, le nom du point de montage est `/backup` et le SID retourné par la commande précédente est `test` donc nous allons créer un sous-répertoire `/backup/test` et accorder la propriété à l’utilisateur Oracle. Remplacez **/backup/SID** par le nom de votre point de montage et le SID de votre base de données. Remarque : si vous avez plusieurs bases de données sur la machine virtuelle, créez un sous-répertoire pour chacune d’elles et modifiez la propriété :

   ```bash
   sudo mkdir /backup/test
   sudo chown oracle:oinstall /backup/test
   ```

1.  Connectez-vous à la base de données :

    ```bash
    sqlplus / as sysdba
    ```
    Remarque : si vous avez plusieurs bases de données installées sur la machine virtuelle, vous devez exécuter les étapes 6 à 15 sur chaque base de données :

1.  Si ce n’est déjà fait, démarrez la base de données. 
   
    ```bash
    SQL> startup
    ```
   
1. Définissez la première destination du journal d’archivage de la base de données sur le répertoire de partage de fichiers que vous avez créé à l’étape 5 :

   ```bash
   sqlplus / as sysdba
   SQL> alter system set log_archive_dest_1='LOCATION=/backup/test';
   SQL> 
   ```


1.  Définissez les variables d'environnement de la base de données pour la zone de récupération rapide :

    ```bash
    SQL> alter system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```


1. Définissez l’objectif de point de récupération (RPO) de la base de données.

    Pour obtenir un RPO cohérent, vous devez prendre en compte la fréquence à laquelle les fichiers journaux de restauration en ligne seront archivés. La fréquence de génération du journal d’archivage est contrôlée par ce qui suit :
    - La taille des fichiers journaux de restauration en ligne. Lorsqu’un fichier journal en ligne est plein, il est basculé et archivé. Plus le fichier journal en ligne est volumineux, plus il faut de temps pour le remplir, ce qui diminue la fréquence de génération de l’archive.
    - La valeur du paramètre ARCHIVE_LAG_TARGET contrôle le nombre maximal de secondes autorisées avant que le fichier journal en ligne actuel ne soit basculé et archivé. 

    Pour réduire la fréquence de basculement et d’archivage, ainsi que l’opération de point de contrôle associée, les fichiers journaux de restauration en ligne Oracle sont généralement de taille importante (1 024 M, 4 096 M, 8 192 M, etc.). Dans un environnement de base de données très actif, les journaux sont susceptibles d’être basculés et archivés toutes les quelques secondes ou minutes, mais dans une base de données moins active, il peut se passer des heures ou des jours avant que les transactions les plus récentes soient archivées, ce qui réduit considérablement la fréquence d’archivage. Il est donc recommandé de définir ARCHIVE_LAG_TARGET pour garantir la cohérence du RPO. Un paramètre de 5 minutes (300 secondes) est une valeur prudente pour ARCHIVE_LAG_TARGET, garantissant ainsi que toutes les opérations de récupération de base de données peuvent être récupérées dans un délai de 5 minutes ou moins après l’échec.

    Pour définir ARCHIVE_LAG_TARGET :

    ```bash 
    sqlplus / as sysdba
    SQL> alter system set archive_lag_target=300 scope=both;
    ```

    Pour mieux comprendre comment déployer des bases de données Oracle à haut niveau de disponibilité dans Azure avec un RPO égal à zéro, consultez [Architectures de référence pour Oracle Database](./oracle-reference-architecture.md).

1.  Assurez-vous que la base de données est en mode journal d'archivage pour activer les sauvegardes en ligne.

     Commencez par vérifier l'état de l'archivage des journaux :

     ```bash
     SQL> SELECT log_mode FROM v$database;

     LOG_MODE
     ------------
     NOARCHIVELOG
     ```

     S'il est en mode NOARCHIVELOG, exécutez les commandes suivantes :

     ```bash
     SQL> SHUTDOWN IMMEDIATE;
     SQL> STARTUP MOUNT;
     SQL> ALTER DATABASE ARCHIVELOG;
     SQL> ALTER DATABASE OPEN;
     SQL> ALTER SYSTEM SWITCH LOGFILE;
     ```

1.  Créez une table pour tester les opérations de sauvegarde et de restauration :

     ```bash
     SQL> create user scott identified by tiger quota 100M on users;
     SQL> grant create session, create table to scott;
     SQL> connect scott/tiger
     SQL> create table scott_table(col1 number, col2 varchar2(50));
     SQL> insert into scott_table VALUES(1,'Line 1');
     SQL> commit;
     SQL> quit
     ```

1. Configurez RMAN pour sauvegarder la base de données vers la Zone de récupération rapide située sur le disque de la machine virtuelle. Notez que la configuration du fichier de contrôle de l’instantané n’accepte pas la substitution de nom de base de données %d. Vous devez donc inclure explicitement le SID de la base de données dans le nom de fichier pour que plusieurs bases de données puissent effectuer une sauvegarde vers le même emplacement. Remplacez `<ORACLE_SID>` par le nom de votre base de données :

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/u02/fast_recovery_area/snapcf_<ORACLE_SID>.f';
    RMAN> configure channel 1 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s'; 
    ```

1. Confirmez les détails du changement de configuration :

    ```bash
    RMAN> show all;
    ```    

1.  Exécutez la sauvegarde. La commande suivante permet d'effectuer une sauvegarde complète de la base de données, fichiers journaux d'archivage compris, sous forme de jeu de sauvegarde au format compressé :

     ```bash
     RMAN> backup as compressed backupset database plus archivelog;
     ```

## <a name="using-azure-backup-preview"></a>Utilisation de Sauvegarde Azure (Préversion)

Le service de sauvegarde Azure fournit des solutions simples, sécurisées et rentables pour sauvegarder vos données et les récupérer à partir du cloud Microsoft Azure. Le service Sauvegarde Azure fournit des sauvegardes indépendantes et isolées pour éviter une destruction accidentelle des données d’origine. Les sauvegardes sont stockées dans un coffre Recovery Services avec gestion intégrée des points de récupération. La configuration et la scalabilité sont simples : les sauvegardes sont optimisées, et vous pouvez facilement effectuer des restaurations en fonction des besoins.

Le service Sauvegarde Azure fournit un [framework](../../../backup/backup-azure-linux-app-consistent.md) qui permet d’assurer la cohérence des applications lors de sauvegardes de machines virtuelles Windows et Linux pour diverses applications comme Oracle, MySQL, Mongo DB et PostGreSQL. Cela implique l'appel d'un pré-script (pour suspendre les applications) avant la capture instantanée des disques, et l'appel d'un post-script (commandes permettant de libérer les applications) au terme de la capture instantanée, pour rétablir les applications en mode normal. Des exemples de pré-scripts et de post-scripts sont fournis sur GitHub, mais la création et la maintenance de ceux-ci relèvent de votre responsabilité.

Désormais, le service Sauvegarde Azure propose un framework enrichi de pré-scripts et de post-scripts (**actuellement en préversion**), qui consiste à fournir des pré-scripts et post-scripts empaquetés pour des applications sélectionnées. Il suffit aux utilisateurs du service Sauvegarde Azure de nommer l'application, après quoi la fonctionnalité de sauvegarde des machines virtuelles Azure appelle automatiquement les pré/post-scripts appropriés. Les pré-scripts et post-scripts empaquetés sont gérés par l'équipe du service Sauvegarde Azure. Les utilisateurs sont ainsi assurés de la prise en charge, de la propriété et de la validité de ces scripts. Les applications actuellement prises en charge pour l'infrastructure améliorée sont *Oracle* et *MySQL*.

Dans cette section, vous allez utiliser l'infrastructure améliorée du service Sauvegarde Azure pour prendre des captures instantanées cohérentes avec les applications de votre machine virtuelle et de la base de données Oracle exécutée. La base de données est placée en mode de sauvegarde, ce qui permet d'effectuer une sauvegarde en ligne cohérente sur le plan transactionnel pendant que le service Sauvegarde Azure prend une capture instantanée des disques de la machine virtuelle. La capture instantanée étant une copie complète du stockage, et non un instantané incrémentiel ou de copie sur écriture, il s'agit d'un support efficace à partir duquel restaurer votre base de données. L'avantage des captures instantanées cohérentes avec les applications Sauvegarde Azure est qu'elles sont prises extrêmement rapidement, quelle que soit la taille de votre base de données, et une capture instantanée peut être utilisée pour les opérations de restauration dès qu'elle est prise, sans avoir à attendre qu'elle soit transférée vers le coffre Recovery Services.

Pour utiliser le service Sauvegarde Azure afin de sauvegarder la base de données, procédez comme suit :

1. Préparez l'environnement pour une sauvegarde cohérente avec les applications.
1. Configurez des sauvegardes cohérentes avec les applications.
1. Déclenchez une sauvegarde cohérente avec les applications de la machine virtuelle.

### <a name="prepare-the-environment-for-an-application-consistent-backup"></a>Préparer l'environnement pour une sauvegarde cohérente avec les applications

> [!IMPORTANT] 
> La base de données Oracle utilise la séparation des rôles de travail pour assurer la séparation des tâches avec le moindre privilège. Cela est possible en associant des groupes de systèmes d’exploitation distincts à des rôles d’administration de base de données distincts. Des privilèges de base de données différents peuvent être accordés aux utilisateurs du système d’exploitation en fonction de leur appartenance aux groupes de systèmes d’exploitation. 
>
> Le rôle de base de données `SYSBACKUP`, (nom générique OSBACKUPDBA), est utilisé pour fournir des privilèges limités afin d’effectuer des opérations de sauvegarde dans la base de données, et est requis par Sauvegarde Azure.
>
> Lors de l’installation d’Oracle, le nom du groupe de systèmes d’exploitation recommandé à associer au rôle SYSBACKUP est `backupdba`, mais vous pouvez utiliser n’importe quel nom pour déterminer le nom du groupe de systèmes d’exploitation représentant le rôle Oracle SYSBACKUP en premier.

1. Passez à l’utilisateur *oracle* :
   ```bash
   sudo su - oracle
   ```

1. Définissez l’environnement Oracle :
   ```bash
   export ORACLE_SID=test
   export ORAENV_ASK=NO
   . oraenv
   ```

1. Déterminez le nom du groupe de systèmes d’exploitation représentant le rôle Oracle SYSBACKUP :
   ```bash
   grep "define SS_BKP" $ORACLE_HOME/rdbms/lib/config.c
   ```
   Le résultat ressemble à ce qui suit : 
   ```output
   #define SS_BKP_GRP "backupdba"
   ```

   Dans la sortie, la valeur placée entre guillemets doubles, dans cet exemple `backupdba`, est le nom du groupe de systèmes d’exploitation Linux sur lequel le rôle Oracle SYSBACKUP est authentifié en externe. Notez cette valeur.

1. Vérifiez si le groupe de systèmes d’exploitation existe en exécutant la commande suivante. Remplacez \<group name\> par la valeur retournée par la commande précédente (sans les guillemets) :
   ```bash
   grep <group name> /etc/group
   ```
   La sortie doit ressembler à ceci, dans notre exemple `backupdba` est utilisé : 
   ```output
   backupdba:x:54324:oracle
   ```

   > [!IMPORTANT] 
   > Si la sortie ne correspond pas à la valeur du groupe de systèmes d’exploitation Oracle récupérée à l’étape 3, vous devez créer le groupe de systèmes d’exploitation représentant le rôle Oracle SYSBACKUP. Remplacez `<group name>` par le nom du groupe récupéré à l’étape 3 :
   >   ```bash
   >   sudo groupadd <group name>
   >   ```

1. Créez un utilisateur de sauvegarde `azbackup` qui appartient au groupe de systèmes d’exploitation que vous avez vérifié ou créé au cours des étapes précédentes. Remplacez \<group name\> par le nom du groupe vérifié :

   ```bash
   sudo useradd -G <group name> azbackup
   ```

1. Configurez l'authentification externe pour le nouvel utilisateur de sauvegarde. 

   L’utilisateur de sauvegarde `azbackup` doit avoir accès à la base de données à l’aide de l’authentification externe, afin de ne pas utiliser de mot de passe. Pour ce faire, vous devez créer un utilisateur de base de données qui s’authentifie en externe via `azbackup`. La base de données utilise un préfixe pour le nom d’utilisateur que vous devez chercher.
   Sur chaque base de données installée sur la machine virtuelle, procédez comme suit :
 
   Connectez-vous à la base de données à l'aide de sqlplus et vérifiez les paramètres par défaut de l'authentification externe :
   
   ```bash
   sqlplus / as sysdba
   SQL> show parameter os_authent_prefix
   SQL> show parameter remote_os_authent
   ```
   
   La sortie doit ressembler à cet exemple qui affiche `ops$` comme préfixe du nom d’utilisateur de la base de données : 

   ```output
   NAME                                 TYPE        VALUE
   ------------------------------------ ----------- ------------------------------
   os_authent_prefix                    string      ops$
   remote_os_authent                    boolean     FALSE
   ```

   Créez un utilisateur de base de données ***ops$azbackup*** pour l’authentification externe de l’utilisateur `azbackup` et accordez-lui des privilèges sysbackup :
   
   ```bash
   SQL> CREATE USER ops$azbackup IDENTIFIED EXTERNALLY;
   SQL> GRANT CREATE SESSION, ALTER SESSION, SYSBACKUP TO ops$azbackup;
   ```

   > [!IMPORTANT] 
   > Si vous recevez une erreur `ORA-46953: The password file is not in the 12.2 format.`  lors de l’exécution de l’instruction `GRANT`, procédez comme suit pour migrer le fichier orapwd au format 12.2. Notez que vous devrez effectuer cette opération pour chaque base de données Oracle sur la machine virtuelle :
   >
   > 1. Quittez sqlplus.
   > 1. Déplacez le fichier de mot de passe disposant de l'ancien format vers un nouveau nom.
   > 1. Migrez le fichier de mot de passe.
   > 1. Supprimez l'ancien fichier.
   > 1. Exécutez les commandes suivantes :
   >
   >    ```bash
   >    mv $ORACLE_HOME/dbs/orapwtest $ORACLE_HOME/dbs/orapwtest.tmp
   >    orapwd file=$ORACLE_HOME/dbs/orapwtest input_file=$ORACLE_HOME/dbs/orapwtest.tmp
   >    rm $ORACLE_HOME/dbs/orapwtest.tmp
   >    ```
   >
   > 1. Réexécutez l'opération `GRANT` dans sqlplus.
   >
   
1. Créez une procédure stockée pour consigner les messages de sauvegarde dans le journal des alertes de la base de données :

   ```bash
   sqlplus / as sysdba
   SQL> GRANT EXECUTE ON DBMS_SYSTEM TO SYSBACKUP;
   SQL> CREATE PROCEDURE sysbackup.azmessage(in_msg IN VARCHAR2)
   AS
     v_timestamp     VARCHAR2(32);
   BEGIN
     SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS')
     INTO v_timestamp FROM DUAL;
     DBMS_OUTPUT.PUT_LINE(v_timestamp || ' - ' || in_msg);
     SYS.DBMS_SYSTEM.KSDWRT(SYS.DBMS_SYSTEM.ALERT_FILE, in_msg);
   END azmessage;
   /
   SQL> SHOW ERRORS
   SQL> QUIT
   ```

### <a name="set-up-application-consistent-backups"></a>Configurer des sauvegardes cohérentes avec les applications  

1. Basculez d’abord vers l’utilisateur racine :
   ```bash
   sudo su -
   ```

1. Recherchez le dossier « / etc/azure ». S’il est absent, créez un répertoire de travail pour les sauvegardes de cohérence des applications :

   ```bash
   if [ ! -d "/etc/azure" ]; then
      mkdir /etc/azure
   fi
   ```

1. Recherchez « workload.conf » dans le dossier. S’il est absent, créez dans le répertoire */etc/azure* un fichier nommé *workload.conf* avec le contenu suivant, qui doit commencer par `[workload]`. Si le fichier est déjà présent, modifiez les champs de manière à le faire correspondre au contenu suivant. Sinon, la commande suivante peut créer le fichier et renseigner le contenu :

   ```bash
   echo "[workload]
   workload_name = oracle
   configuration_path = /etc/oratab
   timeout = 90
   linux_user = azbackup" > /etc/azure/workload.conf
   ```

   > [!IMPORTANT]
   > Le format utilisez par workload.conf est le suivant :
   > * Le paramètre **workload_name** est utilisé par Sauvegarde Azure pour déterminer le type de charge de travail de base de données. Dans ce cas, le paramètre est défini sur Oracle, ce qui permet à Sauvegarde Azure d’exécuter les commandes de pré- et de post-cohérence appropriées pour les bases de données Oracle.
   > * Le paramètre **timeout** indique la durée maximale en secondes nécessaire à chaque base de données pour effectuer des instantanés de stockage.
   > * Le paramètre **linux_user** indique le compte d’utilisateur Linux qui sera utilisé par Sauvegarde Azure pour exécuter les opérations de mise en suspens de la base de données. Vous avez créé cet utilisateur, `azbackup`, précédemment.
   > * Le paramètre **configuration_path** indique le nom de chemin d’accès absolu d’un fichier texte sur la machine virtuelle où chaque ligne répertorie une instance de la base de données en cours d’exécution sur la machine virtuelle. Il s’agit généralement du fichier `/etc/oratab` généré par Oracle au cours de l’installation de la base de données, mais il peut s’agir de n’importe quel fichier portant le nom de votre choix. Toutefois, il doit respecter les règles de format suivantes :
   >   * Un fichier texte avec chaque champ délimité par le caractère deux-points `:`
   >   * Le premier champ de chaque ligne est le nom d’un ORACLE_SID
   >   * Le deuxième champ de chaque ligne correspond au nom de chemin d’accès absolu pour ORACLE_HOME pour cet ORACLE_SID
   >   * Tout le texte qui suit ces deux premiers champs sera ignoré
   >   * Si la ligne commence par un caractère dièse/de hachage `#`, la ligne entière est ignorée en tant que commentaire
   >   * Si le premier champ a la valeur `+ASM` indiquant qu’une instance Gestion du stockage automatique est présente, il est ignoré. 


### <a name="trigger-an-application-consistent-backup-of-the-vm"></a>Déclencher une sauvegarde cohérente avec les applications de la machine virtuelle

# <a name="portal"></a>[Portail](#tab/azure-portal)

1.  Sur le portail Azure, accédez à votre groupe de ressources **rg-oracle** et cliquez sur votre machine virtuelle **vmoracle19c**.

1.  Sur le panneau **Sauvegarde**, créez un nouveau **Coffre Recovery Services** dans le groupe de ressources **rg-oracle** et nommez-le **myVault**.
    Sous **Choisir une stratégie de sauvegarde**, utilisez **(Nouveau) DailyPolicy**. Si vous souhaitez modifier la fréquence de sauvegarde ou la durée de rétention, sélectionnez **Créer une nouvelle stratégie**.

    ![Page d’ajout de coffres Recovery Services](./media/oracle-backup-recovery/recovery-service-01.png)

1.  Pour continuer, cliquez sur **Activer la sauvegarde**.

    > [!IMPORTANT]
    > Après que vous avez cliqué sur **Activer la sauvegarde**, le processus de sauvegarde ne démarre pas avant l’expiration de l’heure planifiée. Pour configurer une sauvegarde immédiate, procédez comme suit.

1. Sur la page du groupe de ressources, cliquez sur le coffre Recovery Services **myVault** que vous venez de créer. Conseil : il peut être nécessaire d'actualiser la page pour le voir.

1.  Dans le panneau **myVault - Éléments de sauvegarde**, sous **NOMBRE D’ÉLÉMENTS DE SAUVEGARDE**, sélectionnez le nombre d’éléments de sauvegarde.

    ![Page de détails myVault d’Archivages de Recovery Services](./media/oracle-backup-recovery/recovery-service-02.png)

1.  Sur le côté droit du panneau **Éléments de sauvegarde (Machine virtuelle Azure)**, cliquez sur le bouton de sélection (**…**), puis sur **Sauvegarder maintenant**.

    ![Commande Sauvegarder maintenant des coffres Recovery Services](./media/oracle-backup-recovery/recovery-service-03.png)

1.  Acceptez la valeur par défaut du champ Conserver la sauvegarde jusqu'au, puis cliquez sur le bouton **OK**. Attendez que le processus de sauvegarde se termine. 

    Pour consulter l'état du travail de sauvegarde, cliquez sur **Travaux de sauvegarde**.

    ![Page de travail des coffres Recovery Services](./media/oracle-backup-recovery/recovery-service-04.png)

    L’état du travail de sauvegarde s’affiche dans l’image suivante :

    ![Page de travail d’Archivages de Recovery Services avec état](./media/oracle-backup-recovery/recovery-service-05.png)
    
    Notez que bien que l'exécution de la capture instantanée ne prenne que quelques secondes, son transfert vers le coffre peut prendre un certain temps, et le travail de sauvegarde n'est pas terminé tant que le transfert n'est pas allé jusqu'à son terme.

1. Pour une sauvegarde cohérente avec les applications, résolvez les éventuelles erreurs présentes dans le fichier journal. Le fichier journal se trouve sous /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/extension.log.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Créez un coffre Recovery Services :

   ```azurecli
   az backup vault create --location eastus --name myVault --resource-group rg-oracle
   ```

1. Activez la protection de la sauvegarde pour la machine virtuelle :

   ```azurecli
   az backup protection enable-for-vm \
      --resource-group rg-oracle \
      --vault-name myVault \
      --vm vmoracle19c \
      --policy-name DefaultPolicy
   ```

1. Déclenchez une sauvegarde pour qu'elle s'exécute maintenant plutôt que d'attendre qu'elle se déclenche selon la planification par défaut (5h00 UTC) : 

   ```azurecli
   az backup protection backup-now \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c 
   ```

   Vous pouvez surveiller la progression du travail de sauvegarde à l'aide des commandes `az backup job list` et `az backup job show`.

---

## <a name="recovery"></a>Récupération

Pour récupérer la base de données, procédez comme suit :

1. Supprimez les fichiers de base de données.
1. Générez un script de restauration à partir du coffre Recovery Services.
1. Montez le point de restauration.
1. Procédez à la récupération.

### <a name="remove-the-database-files"></a>Suppression des fichiers de base de données 

Plus loin dans cet article, vous allez apprendre à tester le processus de récupération. Avant de tester le processus de récupération, vous devez supprimer les fichiers de base de données.

1.  Revenez à l’utilisateur Oracle :
    ```bash
    su - oracle
    ```

1. Arrêtez l'instance d'Oracle :

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

1.  Supprimez les fichiers de données de la base de données et les fichiers contolfiles pour simuler une défaillance :

    ```bash
    cd /u02/oradata/TEST
    rm -f *.dbf *.ctl
    ```

### <a name="generate-a-restore-script-from-the-recovery-services-vault"></a>Générer un script de restauration à partir du coffre Recovery Services

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Sur le portail Azure, recherchez l'élément *myVault* et sélectionnez-le.

    ![Éléments de sauvegarde myVault des coffres Recovery Services](./media/oracle-backup-recovery/recovery-service-06.png)

1. Sur le panneau **Vue d'ensemble**, sélectionnez les **Éléments de sauvegarde** et la **Machine virtuelle Azure**, qui doit comporter un nombre d'éléments de sauvegarde non nul.

    ![Nombre d’éléments de sauvegarde de machine virtuelle Azure des coffres Recovery Services](./media/oracle-backup-recovery/recovery-service-07.png)

1. Votre machine virtuelle **vmoracle19c** est répertoriée sur la page Éléments de sauvegarde (Machines virtuelles Azure). Cliquez sur les points de suspension de droite pour afficher le menu, puis sélectionnez **Récupération de fichier**.

    ![Capture d’écran de la page de récupération de fichiers des coffres Recovery Services](./media/oracle-backup-recovery/recovery-service-08.png)

1. Dans le volet **Récupération de fichier (aperçu)**, cliquez sur **Télécharger le script**. Enregistrez ensuite le fichier de téléchargement (.py) dans un dossier de l'ordinateur client. Un mot de passe est généré pour exécuter le script. Copiez le mot de passe dans un fichier pour une utilisation ultérieure. 

    ![Options d’enregistrement de fichier de script téléchargé](./media/oracle-backup-recovery/recovery-service-09.png)

1. Copiez le fichier. py sur la machine virtuelle.

    L’exemple suivant montre comment utiliser une commande de copie sécurisée (scp) pour déplacer le fichier vers la machine virtuelle. Vous pouvez également copier le contenu vers le Presse-papiers, puis le coller dans un nouveau fichier configuré sur la machine virtuelle.

    > [!IMPORTANT]
    > Dans l’exemple suivant, veillez à mettre à jour les valeurs d’adresse IP et de dossier. Les valeurs doivent mapper au dossier d’enregistrement du fichier.
    >

    ```bash
    $ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour répertorier les points de récupération de votre machine virtuelle, utilisez la commande az backup recoverypoint list. Dans cet exemple, nous sélectionnons le point de récupération le plus récent pour la machine virtuelle nommée vmoracle19c qui est protégée dans le coffre Recovery Services Vault nommé myVault :

```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
```

Pour obtenir le script qui connecte ou monte le point de récupération sur votre machine virtuelle, utilisez az backup restore files mount-rp. L’exemple suivant obtient le script pour la machine virtuelle nommée vmoracle19c qui est protégée dans le coffre Recovery Services Vault nommé myVault.

Remplacez myRecoveryPointName par le nom du point de récupération que vous avez obtenu dans la commande précédente :

```azurecli
   az backup restore files mount-rp \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --rp-name myRecoveryPointName
```

Le script est téléchargé et un mot de passe s’affiche, comme dans l’exemple suivant :

```bash
   File downloaded: vmoracle19c_eus_4598131610710119312_456133188157_6931c635931f402eb543ee554e1cf06f102c6fc513d933.py. Use password c4487e40c760d29
```

Copiez le fichier. py sur la machine virtuelle.

L’exemple suivant montre comment utiliser une commande de copie sécurisée (scp) pour déplacer le fichier vers la machine virtuelle. Vous pouvez également copier le contenu vers le Presse-papiers, puis le coller dans un nouveau fichier configuré sur la machine virtuelle.

> [!IMPORTANT]
> Dans l’exemple suivant, veillez à mettre à jour les valeurs d’adresse IP et de dossier. Les valeurs doivent mapper au dossier d’enregistrement du fichier.
>

```bash
$ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
```
---

### <a name="mount-the-restore-point"></a>Monter le point de restauration

1. Basculez vers l'utilisateur racine :
   ```bash
   sudo su -
   ``````
1. Créez un point de montage de restauration et copiez-y le script.

    Dans l'exemple suivant, créez un répertoire */restore* pour la capture instantanée à monter, déplacez le fichier vers le répertoire, puis modifiez le fichier afin qu'il appartienne à l'utilisateur racine et qu'il devienne exécutable.

    ```bash 
    mkdir /restore
    chmod 777 /restore
    cd /restore
    cp /tmp/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py /restore
    chmod 755 /restore/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```
    
    Exécutez le script pour restaurer la sauvegarde. Vous serez invité à fournir le mot de passe généré sur le portail Azure. 
  
   ```bash
    ./vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```

    L’exemple suivant montre ce que vous devriez voir après avoir exécuté le script précédent. Lorsque vous êtes invité à continuer, entrez **Y**.

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : b1ad68e16dfafc6

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /restore/vmoracle19c-20201215123912/Volume1

    2)  | /dev/sdd  |  /dev/sdd1  |  /restore/vmoracle19c-20201215123912/Volume2

    3)  | /dev/sdd  |  /dev/sdd2  |  /restore/vmoracle19c-20201215123912/Volume3

    4)  | /dev/sdd  |  /dev/sdd15  |  /restore/vmoracle19c-20201215123912/Volume5

    The following partitions failed to mount since the OS couldn't identify the filesystem.

    ************ Volumes from unknown filesystem ************

    Sr.No.  |  Disk  |  Volume  |  Partition Type

    1)  | /dev/sdb  |  /dev/sdb14  |  BIOS Boot partition

    Please refer to '/restore/vmoracle19c-2020XXXXXXXXXX/Scripts/MicrosoftAzureBackupILRLogFile.log' for more details.

    ************ Open File Explorer to browse for files. ************

    After recovery, remove the disks and close the connection to the recovery point by clicking the 'Unmount Disks' button from the portal or by using the relevant unmount command in case of powershell or CLI.

    After unmounting disks, run the script with the parameter 'clean' to remove the mount paths of the recovery point from this machine.

    Please enter 'q/Q' to exit...
    ```

1. L’accès aux volumes montés est confirmé.

    Pour quitter, entrez **q**, puis recherchez les volumes montés. Pour créer la liste des volumes ajoutés, à l'invite de commandes, entrez **df -h**.
    
    ```
    [root@vmoracle19c restore]# df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        3.8G     0  3.8G   0% /dev
    tmpfs           3.8G     0  3.8G   0% /dev/shm
    tmpfs           3.8G   17M  3.8G   1% /run
    tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
    /dev/sdd2        30G  9.6G   18G  36% /
    /dev/sdb1       126G  736M  119G   1% /u02
    /dev/sda1       497M  199M  298M  41% /boot
    /dev/sda15      495M  9.7M  486M   2% /boot/efi
    tmpfs           771M     0  771M   0% /run/user/54322
    /dev/sdc1       126G  2.9G  117G   3% /restore/vmoracle19c-20201215123912/Volume1
    /dev/sdd1       497M  199M  298M  41% /restore/vmoracle19c-20201215123912/Volume2
    /dev/sdd2        30G  9.6G   18G  36% /restore/vmoracle19c-20201215123912/Volume3
    /dev/sdd15      495M  9.7M  486M   2% /restore/vmoracle19c-20201215123912/Volume5
    ```

### <a name="perform-recovery"></a>Exécution d'une récupération

1. Restaurez les fichiers de base de données manquants à leur emplacement :

    ```bash
    cd /restore/vmoracle19c-2020XXXXXXXXXX/Volume1/oradata/TEST
    cp * /u02/oradata/TEST
    cd /u02/oradata/TEST
    chown -R oracle:oinstall *
    ```
1. Revenez à l’utilisateur Oracle
   ```bash
   sudo su - oracle
   ```
1. Démarrez l’instance de base de données et montez le fichier controlfile pour la lecture :
   ```bash
   sqlplus / as sysdba
   SQL> startup mount
   SQL> quit
   ```

1. Connectez-vous à la base de données avec sysbackup :
   ```bash
   sqlplus / as sysbackup
   ```
1. Lancez la récupération de base de données automatique :

   ```bash
   SQL> recover automatic database until cancel using backup controlfile;
   ```
   > [!IMPORTANT]
   > Notez qu’il est important de spécifier la syntaxe USING BACKUP CONTROLFILE pour informer la commande RECOVER AUTOMATIC DATABASE que la récupération ne doit pas s’arrêter au numéro de changement de système (SCN) Oracle enregistré dans le fichier de contrôle de la base de données restaurée. Le fichier de contrôle de la base de données restaurée était un instantané, ainsi que le reste de la base de données, et le SCN stocké dans ce fichier est issu du point dans le temps de l’instantané. Il peut y avoir des transactions enregistrées après ce point et nous voulons récupérer jusqu’au moment de la dernière transaction validée dans la base de données.

   Une fois la récupération terminée, le message `Media recovery complete` s’affiche. Toutefois, lors de l’utilisation de la clause BACKUP CONTROLFILE, la commande de récupération ignore les fichiers journaux en ligne et il est possible qu’il y ait des modifications dans le journal de restauration en ligne actuel requises pour effectuer la récupération à un moment dans le temps. Dans ce cas, vous pouvez voir des messages similaires à ceux-ci :

   ```output
   SQL> recover automatic database until cancel using backup controlfile;
   ORA-00279: change 2172930 generated at 04/08/2021 12:27:06 needed for thread 1
   ORA-00289: suggestion :
   /u02/fast_recovery_area/TEST/archivelog/2021_04_08/o1_mf_1_13_%u_.arc
   ORA-00280: change 2172930 for thread 1 is in sequence #13
   ORA-00278: log file
   '/u02/fast_recovery_area/TEST/archivelog/2021_04_08/o1_mf_1_13_%u_.arc' no
   longer needed for this recovery
   ORA-00308: cannot open archived log
   '/u02/fast_recovery_area/TEST/archivelog/2021_04_08/o1_mf_1_13_%u_.arc'
   ORA-27037: unable to obtain file status
   Linux-x86_64 Error: 2: No such file or directory
   Additional information: 7

   Specify log: {<RET>=suggested | filename | AUTO | CANCEL}
   ```
   
   > [!IMPORTANT]
   > Notez que si le journal de restauration en ligne actuel a été perdu ou endommagé et ne peut pas être utilisé, vous pouvez annuler la récupération à ce stade. 

   Pour corriger cela, vous pouvez identifier le journal en ligne actuel qui n’a pas été archivé et fournir le nom de fichier complet à l’invite.


   Ouvrir une nouvelle connexion SSH 
   ```bash
   ssh azureuser@<IP Address>
   ```
   Basculez vers l’utilisateur Oracle et définissez le SID Oracle
   ```bash
   sudo su - oracle
   export ORACLE_SID=test
   ```
   
   Connectez-vous à la base de données et exécutez la requête suivante pour rechercher le fichier journal en ligne. 
   ```bash
   sqlplus / as sysdba
   SQL> column member format a45
   SQL> set linesize 500  
   SQL> select l.SEQUENCE#, to_char(l.FIRST_CHANGE#,'999999999999999') as CHK_CHANGE, l.group#, l.archived, l.status, f.member
   from v$log l, v$logfile f
   where l.group# = f.group#;
   ```

   Le résultat ressemble à ce qui suit. 
   ```output
   SEQUENCE#  CHK_CHANGE           GROUP# ARC STATUS            MEMBER
   ---------- ---------------- ---------- --- ---------------- ---------------------------------------------
           13          2172929          1 NO  CURRENT          /u02/oradata/TEST/redo01.log
           12          2151934          3 YES INACTIVE         /u02/oradata/TEST/redo03.log
           11          2071784          2 YES INACTIVE         /u02/oradata/TEST/redo02.log
   ```
   Copiez le chemin d’accès et le nom du fichier journal pour le journal en ligne ACTUEL. Dans cet exemple, il s’agit de `/u02/oradata/TEST/redo01.log`. Revenez à la session SSH exécutant la commande de récupération, entrez les informations du fichier journal et appuyez sur Entrée :

   ```bash
   Specify log: {<RET>=suggested | filename | AUTO | CANCEL}
   /u02/oradata/TEST/redo01.log
   ```

   Vous devez voir que le fichier journal est appliqué et que la récupération est terminée. Entrez ANNULER pour quitter la commande de récupération :
   ```output
   Specify log: {<RET>=suggested | filename | AUTO | CANCEL}
   /u02/oradata/TEST/redo01.log
   Log applied.
   Media recovery complete.
   ```

1. Ouvrir la base de données
   ```bash
   SQL> alter database open resetlogs;
   ```
   > [!IMPORTANT]
   > L’option RESETLOGS est requise lorsque la commande RECOVER utilise l’option USING BACKUP CONTROLFILE. L’option RESETLOGS crée une nouvelle incarnation de la base de données en réinitialisant l’historique de restauration au point de départ, car il n’existe aucun moyen de déterminer la part de la base de données précédente ignorée dans la récupération.
   
1. Vérifiez que le contenu de la base de données a été entièrement récupéré :

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```

1. Démontez le point de restauration.

   ```bash
   sudo umount /restore/vmoracle19c-20210107110037/Volume*
   ```

    Dans le panneau **Récupération de fichier (aperçu)** du portail Azure, cliquez sur **Démonter les disques**.

    ![Commande Démonter les disques](./media/oracle-backup-recovery/recovery-service-10.png)
    
    Vous pouvez également démonter les volumes de récupération en exécutant à nouveau le script Python à l'aide de l'option **-clean**.

## <a name="restore-the-entire-vm"></a>Restaurer la machine virtuelle entière

Au lieu de restaurer les fichiers supprimés des coffres Recovery Services, vous pouvez restaurer la machine virtuelle dans son intégralité.

Pour restaurer la machine virtuelle dans son intégralité, procédez comme suit :

1. Arrêtez et supprimez vmoracle19c.
1. Récupérez la machine virtuelle.
1. Définissez l'adresse IP publique.
1. Connectez-vous à la machine virtuelle.
1. Démarrez la base de données jusqu'au stade du montage et procédez à la récupération.

### <a name="stop-and-delete-vmoracle19c"></a>Arrêter et supprimer vmoracle19c

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Sur le portail Azure, accédez à la machine virtuelle **vmoracle19c**, puis sélectionnez **Arrêter**.

1. Une fois la machine virtuelle arrêtée, sélectionnez **Supprimer** puis **Oui**.

   ![Commande Supprimer du coffre](./media/oracle-backup-recovery/recover-vm-01.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Arrêtez et libérez la machine virtuelle :

    ```azurecli
    az vm deallocate --resource-group rg-oracle --name vmoracle19c
    ```

1. Supprimez la machine virtuelle. Lorsque vous y êtes invité, entrez « y » :

    ```azurecli
    az vm delete --resource-group rg-oracle --name vmoracle19c
    ```

---

### <a name="recover-the-vm"></a>Récupération de la machine virtuelle

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Créez un compte de stockage pour la mise en lots sur le portail Azure.

   1. Sur le portail Azure, sélectionnez **+ Créer une ressource**, puis recherchez et sélectionnez **Compte de stockage**.
    
      ![Capture d’écran montrant où créer une ressource.](./media/oracle-backup-recovery/storage-1.png)
    
    
   1. Sur la page Créer un compte de stockage, choisissez votre groupe de ressources existant (**rg-oracle**), nommez votre compte de stockage **oracrestore** et sélectionnez **Stockage v2 (generalpurpose v2)** sous Type de compte. Remplacez la réplication par **Stockage localement redondant (LRS)** et définissez Performances sur **Standard**. Assurez-vous que l'Emplacement est défini sur la même région que toutes les autres ressources du groupe de ressources. 
    
      ![Page d'ajout d'un compte de stockage](./media/oracle-backup-recovery/recovery-storage-1.png)
   
   1. Cliquez sur Vérifier + créer, puis sur Créer.

1. Sur le portail Azure, recherchez l'élément *myVault* et cliquez dessus.

    ![Éléments de sauvegarde myVault des coffres Recovery Services](./media/oracle-backup-recovery/recovery-service-06.png)
    
1.  Sur le panneau **Vue d'ensemble**, sélectionnez les **Éléments de sauvegarde** et la **Machine virtuelle Azure**, qui doit comporter un nombre d'éléments de sauvegarde non nul.

    ![Nombre d’éléments de sauvegarde de machine virtuelle Azure des coffres Recovery Services](./media/oracle-backup-recovery/recovery-service-07.png)

1.  Votre machine virtuelle **vmoracle19c** est répertoriée sur la page Éléments de sauvegarde (Machines virtuelles Azure). Cliquez sur le nom de la machine virtuelle.

    ![Page de récupération de machine virtuelle](./media/oracle-backup-recovery/recover-vm-02.png)

1.  Sur le panneau **vmoracle19c**, choisissez un point de restauration bénéficiant du type de cohérence **Cohérence avec les applications**, puis cliquez sur les points de suspension ( **...** ), à droite, pour afficher le menu.  Dans le menu, cliquez sur **Restaurer la machine virtuelle**.

    ![Commande Restaurer la machine virtuelle](./media/oracle-backup-recovery/recover-vm-03.png)

1.  Sur le panneau **Restaurer la machine virtuelle**, sélectionnez **Créer** et **Créer une machine virtuelle**. Entrez le nom de la machine virtuelle, **vmoracle19c**, et sélectionnez le réseau virtuel **vmoracle19cVNET** ; le sous-réseau sera automatiquement renseigné en fonction du réseau virtuel que vous sélectionnerez. Le processus de restauration de la machine virtuelle requiert un compte de stockage Azure situé dans le même groupe de ressources et dans la même région. Vous pouvez choisir le compte de stockage **orarestore** que vous avez configuré précédemment.

    ![Restaurer les valeurs de configuration](./media/oracle-backup-recovery/recover-vm-04.png)

1.  Pour restaurer la machine virtuelle, cliquez sur le bouton **Restaurer**.

1.  Pour afficher l’état du processus de restauration, cliquez sur **Travaux**, puis cliquez sur **Travaux de sauvegarde**.

    ![Commande d’état des travaux de sauvegarde](./media/oracle-backup-recovery/recover-vm-05.png)

    Cliquez sur l'opération de restauration **En cours** pour afficher l'état du processus de restauration :

    ![État du processus de restauration](./media/oracle-backup-recovery/recover-vm-06.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour configurer votre compte de stockage et le partage de fichiers, exécutez les commandes suivantes dans Azure CLI.

1. Créez le compte de stockage dans le même groupe de ressources et au même emplacement que votre machine virtuelle :

   ```azurecli
   az storage account create -n orarestore -g rg-oracle -l eastus --sku Standard_LRS
   ```

1. Récupérez la liste des points de récupération disponibles. 

   ```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
   ```

1. Restaurez le point de récupération sur le compte de stockage. Remplacez `<myRecoveryPointName>` par un point de récupération de la liste générée à l'étape précédente :

   ```azurecli
   az backup restore restore-disks \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --storage-account orarestore \
      --rp-name <myRecoveryPointName> \
      --target-resource-group rg-oracle
   ```

1. Récupérez les détails du travail de restauration. La commande suivante permet d'obtenir plus de détails sur le travail restauré déclenché, y compris son nom, qui est nécessaire pour récupérer l'URI modèle. 

   ```azurecli
   az backup job list \
       --resource-group rg-oracle \
       --vault-name myVault \
       --output table
   ```

   La sortie est semblable à ce `(Note down the name of the restore job)` :

   ```output
   Name                                  Operation        Status     Item Name    Start Time UTC                    Duration
   ------------------------------------  ---------------  ---------  -----------  --------------------------------  --------------
   c009747a-0d2e-4ac9-9632-f695bf874693  Restore          Completed  vmoracle19c  2021-01-10T21:46:07.506223+00:00  0:03:06.634177
   6b779c98-f57a-4db1-b829-9e8eab454a52  Backup           Completed  vmoracle19c  2021-01-07T10:11:15.784531+00:00  0:21:13.220616
   502bc7ae-d429-4f0f-b78e-51d41b7582fc  ConfigureBackup  Completed  vmoracle19c  2021-01-07T09:43:55.298755+00:00  0:00:30.839674
   ```

1. Récupérez les détails de l'URI à utiliser pour recréer la machine virtuelle. Remplacez le nom du travail de restauration de l'étape précédente par `<RestoreJobName>`.

    ```azurecli
      az backup job show \
        -v myVault \
        -g rg-oracle \
        -n <RestoreJobName> \
        --query properties.extendedInfo.propertyBag
    ```

   La sortie est semblable à ce qui suit :

   ```output
   {
   "Config Blob Container Name": "vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2",
   "Config Blob Name": "config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Config Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Job Type": "Recover disks",
   "Recovery point time ": "1/7/2021 10:11:19 AM",
   "Target Storage Account Name": "orarestore",
   "Target resource group": "rg-oracle",
   "Template Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json"
   }
   ```

   Le nom du modèle, situé à la fin de l'URI de l'objet blob modèle, qui, dans cet exemple, est `azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json`, et le nom du conteneur d'objets blob, à savoir `vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2`, sont répertoriés. 

   Utilisez ces valeurs dans la commande suivante pour attribuer des variables en vue de la création de la machine virtuelle. Une clé SAS est générée pour le conteneur de stockage avec une durée de 30 minutes.  


   ```azurecli
   expiretime=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
   connection=$(az storage account show-connection-string \
    --resource-group rg-oracle \
    --name orarestore \
    --query connectionString)
   token=$(az storage blob generate-sas \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
   url=$(az storage blob url \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --connection-string $connection \
    --output tsv)
   ```

   Déployez maintenant le modèle pour créer la machine virtuelle.

   ```azurecli
   az deployment group create \
      --resource-group rg-oracle \
      --template-uri $url?$token
   ```

   Vous serez invité à fournir un nom de machine virtuelle.

---

### <a name="set-the-public-ip-address"></a>Définition de l’adresse IP publique

Une fois la machine virtuelle restaurée, vous devez réattribuer l'adresse IP d'origine à la nouvelle machine virtuelle.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1.  Sur le portail Azure, accédez à votre machine virtuelle **vmoracle19c**. Vous remarquerez qu'une nouvelle adresse IP publique et une carte réseau de type vmoracle19c-NIC-XXXXXXXXXXXX ont été attribuées, mais que celles-ci n'ont pas d'adresse DNS. Lorsque la machine virtuelle d'origine a été supprimée, son adresse IP publique et sa carte réseau ont été conservées. Les étapes suivantes consisteront à les rattacher à la nouvelle machine virtuelle.

    ![Liste d’adresses IP publiques](./media/oracle-backup-recovery/create-ip-01.png)

1.  Arrêtez la machine virtuelle.

    ![Créer une adresse IP](./media/oracle-backup-recovery/create-ip-02.png)

1.  Accédez à **Mise en réseau**.

    ![Associer une adresse IP](./media/oracle-backup-recovery/create-ip-03.png)

1.  Cliquez sur **Attacher l'interface réseau**, choisissez la carte réseau d'origine (**vmoracle19cVMNic) à laquelle l'adresse IP publique d'origine est toujours associée, puis cliquez sur **OK**.

    ![Sélectionner le type de ressource et les valeurs de carte réseau](./media/oracle-backup-recovery/create-ip-04.png)

1.  Vous devez maintenant détacher la carte réseau qui a été créée avec l'opération de restauration de la machine virtuelle, car elle est configurée en tant qu'interface principale. Cliquez sur **Détacher l'interface réseau** et choisissez la nouvelle carte réseau de type **vmoracle19c-NIC-XXXXXXXXXXXX**, puis cliquez sur **OK**.

    ![Capture d'écran montrant où sélectionner l’interface réseau Détacher.](./media/oracle-backup-recovery/create-ip-05.png)
    
    La machine virtuelle recréée disposera alors de la carte réseau d'origine, qui est associée à l'adresse IP et aux règles de groupe de sécurité réseau d'origine.
    
    ![Valeur d’adresse IP](./media/oracle-backup-recovery/create-ip-06.png)
    
1.  Revenez à la **Vue d’ensemble** et cliquez sur **Démarrer**. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Arrêtez et libérez la machine virtuelle :

   ```azurecli
   az vm deallocate --resource-group rg-oracle --name vmoracle19c
   ```

1. Répertoriez les cartes réseau de machine virtuelle actuelles, restaurées et générées.

   ```azurecli
   az vm nic list --resource-group rg-oracle --vm-name vmoracle19c
   ```

   La sortie est semblable à ce qui suit, `vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf` correspondant au nom de la carte réseau générée par la restauration.

   ```output
   {
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/rg-oracle/providers/Microsoft.Network/networkInterfaces/vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf",
    "primary": true,
    "resourceGroup": "rg-oracle"
   }
   ```

1. Attachez la carte réseau d'origine, dont le nom doit être de type `<VMName>VMNic`, en l'occurrence `vmoracle19cVMNic`. L'adresse IP publique d'origine, qui est toujours attachée à cette carte réseau, sera restaurée sur la machine virtuelle lorsque la carte réseau sera rattachée. 

   ```azurecli
   az vm nic add --nics vmoracle19cVMNic --resource-group rg-oracle --vm-name vmoracle19c
   ```

1. Détacher la carte réseau générée par la restauration

   ```azurecli
   az vm nic remove --nics vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf --resource-group rg-oracle --vm-name vmoracle19c
   ```
1. Démarrez la machine virtuelle :

   ```azurecli
   az vm start --resource-group rg-oracle --name vmoracle19c
   ```

---

### <a name="connect-to-the-vm"></a>Connexion à la machine virtuelle

Pour vous connecter à la machine virtuelle :

```azurecli
ssh azureuser@<publicIpAddress>
```

### <a name="start-the-database-to-mount-stage-and-perform-recovery"></a>Démarrer la base de données jusqu'au stade du montage et effectuer la récupération

1. Vous pouvez constater que l'instance est en cours d'exécution car la fonctionnalité de démarrage automatique a tenté de lancer la base de données au démarrage de la machine virtuelle. Toutefois, la base de données doit être restaurée et n'en est probablement qu'au stade du montage, donc un arrêt préparatoire est exécuté en premier.

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> shutdown immediate
    SQL> startup mount
    SQL> recover automatic database;
    SQL> alter database open;
    ```
    
1. Vérifiez que le contenu de la base de données a été récupéré :

    ```bash
    SQL> select * from scott.scott_table;
    ```

Le processus de sauvegarde et de récupération de la base de données Oracle Database 19c sur une machine virtuelle Linux Azure est maintenant terminé.

Vous trouverez plus d’informations sur les commandes et les concepts Oracle dans la documentation Oracle, notamment :

   * [Exécution de sauvegardes gérées par les utilisateurs Oracle de l’ensemble de la base de données](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/user-managed-database-backups.html#GUID-65C5E03A-E906-47EB-92AF-6DC273DBD0A8)
   * [Exécution d’une récupération de base de données complète gérée par les utilisateurs](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/user-managed-flashback-dbpitr.html#GUID-66D07694-533F-4E3A-BA83-DD461B68DB56)
   * [Commande Oracle STARTUP](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqpug/STARTUP.html#GUID-275013B7-CAE2-4619-9A0F-40DB71B61FE8)
   * [Commande Oracle RECOVER](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/user-managed-flashback-dbpitr.html#GUID-54B59888-8683-4CD9-B144-B0BB68887572)
   * [Commande Oracle ALTER DATABASE](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/ALTER-DATABASE.html#GUID-8069872F-E680-4511-ADD8-A4E30AF67986)
   * [Paramètre Oracle LOG_ARCHIVE_DEST_n](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/LOG_ARCHIVE_DEST_n.html#GUID-10BD97BF-6295-4E85-A1A3-854E15E05A44)
   * [Paramètre Oracle ARCHIVE_LAG_TARGET](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/ARCHIVE_LAG_TARGET.html#GUID-405D335F-5549-4E02-AFB9-434A24465F0B)


## <a name="delete-the-vm"></a>Supprimer la machine virtuelle

Quand vous n’avez plus besoin de la machine virtuelle, vous pouvez utiliser les commandes suivantes pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées :

1. Désactiver la suppression réversible des sauvegardes dans le coffre

    ```azurecli
    az backup vault backup-properties set --name myVault --resource-group rg-oracle --soft-delete-feature-state disable
    ```

1. Arrêter la protection de la machine virtuelle et supprimer les sauvegardes

    ```azurecli
    az backup protection disable --resource-group rg-oracle --vault-name myVault --container-name vmoracle19c --item-name vmoracle19c --delete-backup-data true --yes
    ```

1. Supprimer le groupe de ressources, y compris toutes les ressources

    ```azurecli
    az group delete --name rg-oracle
    ```

## <a name="next-steps"></a>Étapes suivantes

[Tutoriel : Créer des machines virtuelles hautement disponibles](../../linux/create-cli-complete.md)

[Explorer des exemples Azure CLI de déploiement de machines virtuelles](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)