---
title: Sauvegarder et récupérer une base de données Oracle Database 19c sur une machine virtuelle Linux Azure à l'aide du service Sauvegarde Azure
description: Apprenez à sauvegarder et récupérer une base de données Oracle Database 19c à l'aide du service Sauvegarde Azure.
author: cro27
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: ac045694e8975509635e03221a8cb9cc84446b55
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806407"
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

Cette étape part du principe que vous disposez d'une instance d'Oracle (*test*) exécutée sur une machine virtuelle nommée *vmoracle19c*.

1. Basculez vers l'utilisateur *oracle* :
 
   ```bash
    sudo su - oracle
    ```
    
2. Avant de vous connecter, vous devez définir la variable d'environnement ORACLE_SID :
    
    ```bash
    export ORACLE_SID=test;
    ```

    Vous devez également ajouter la variable ORACLE_SID au fichier `.bashrc` des utilisateurs `oracle` pour les prochaines connexions à l'aide de la commande suivante :

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
3. Si ce n'est déjà fait, démarrez l'écouteur Oracle :

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

4.  Créez l'emplacement de la zone de récupération rapide (FRA) :

    ```bash
    mkdir /u02/fast_recovery_area
    ```

5.  Connectez-vous à la base de données :

    ```bash
    SQL> sqlplus / as sysdba
    ```

6.  Si ce n'est déjà fait, démarrez la base de données :

    ```bash
    SQL> startup
    ```

7.  Définissez les variables d'environnement de la base de données pour la zone de récupération rapide :

    ```bash
    SQL> alter system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
8.  Assurez-vous que la base de données est en mode journal d'archivage pour activer les sauvegardes en ligne.

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

9.  Créez une table pour tester les opérations de sauvegarde et de restauration :

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

10. Configurez RMAN de manière à effectuer la sauvegarde dans la zone de récupération rapide située sur le disque de la machine virtuelle :

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/u02/fast_recovery_area/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s'; 
    ```

11. Confirmez les détails du changement de configuration :

    ```bash
    RMAN> show all;
    ```    

12.  Exécutez la sauvegarde. La commande suivante permet d'effectuer une sauvegarde complète de la base de données, fichiers journaux d'archivage compris, sous forme de jeu de sauvegarde au format compressé :

     ```bash
     RMAN> backup as compressed backupset database plus archivelog;
     ```

## <a name="using-azure-backup"></a>Utilisation du service Sauvegarde Azure

Le service de sauvegarde Azure fournit des solutions simples, sécurisées et rentables pour sauvegarder vos données et les récupérer à partir du cloud Microsoft Azure. Le service Sauvegarde Azure fournit des sauvegardes indépendantes et isolées pour éviter une destruction accidentelle des données d’origine. Les sauvegardes sont stockées dans un coffre Recovery Services avec gestion intégrée des points de récupération. La configuration et la scalabilité sont simples : les sauvegardes sont optimisées, et vous pouvez facilement effectuer des restaurations en fonction des besoins.

Le service Sauvegarde Azure fournit une [infrastructure](../../../backup/backup-azure-linux-app-consistent.md) permettant d'assurer la cohérence des applications lors des sauvegardes de machines virtuelles Windows et Linux pour diverses applications comme Oracle, MySQL, Mongo DB, SAP HANA et PostGreSQL. Cela implique l'appel d'un pré-script (pour suspendre les applications) avant la capture instantanée des disques, et l'appel d'un post-script (commandes permettant de libérer les applications) au terme de la capture instantanée, pour rétablir les applications en mode normal. Des exemples de pré-scripts et de post-scripts sont fournis sur GitHub, mais la création et la maintenance de ceux-ci relèvent de votre responsabilité. 

Le service Sauvegarde Azure fournit désormais une infrastructure améliorée qui offre des pré-scripts et post-scripts empaquetés pour certaines applications. Il suffit aux utilisateurs du service Sauvegarde Azure de nommer l'application, après quoi la fonctionnalité de sauvegarde des machines virtuelles Azure appelle automatiquement les pré/post-scripts appropriés. Les pré-scripts et post-scripts empaquetés sont gérés par l'équipe du service Sauvegarde Azure. Les utilisateurs sont ainsi assurés de la prise en charge, de la propriété et de la validité de ces scripts. Les applications actuellement prises en charge pour l'infrastructure améliorée sont *Oracle* et *MySQL*.

Dans cette section, vous allez utiliser l'infrastructure améliorée du service Sauvegarde Azure pour prendre des captures instantanées cohérentes avec les applications de votre machine virtuelle et de la base de données Oracle exécutée. La base de données est placée en mode de sauvegarde, ce qui permet d'effectuer une sauvegarde en ligne cohérente sur le plan transactionnel pendant que le service Sauvegarde Azure prend une capture instantanée des disques de la machine virtuelle. La capture instantanée étant une copie complète du stockage, et non un instantané incrémentiel ou de copie sur écriture, il s'agit d'un support efficace à partir duquel restaurer votre base de données. L'avantage des captures instantanées cohérentes avec les applications Sauvegarde Azure est qu'elles sont prises extrêmement rapidement, quelle que soit la taille de votre base de données, et une capture instantanée peut être utilisée pour les opérations de restauration dès qu'elle est prise, sans avoir à attendre qu'elle soit transférée vers le coffre Recovery Services.

Pour utiliser le service Sauvegarde Azure afin de sauvegarder la base de données, procédez comme suit :

1. Préparez l'environnement pour une sauvegarde cohérente avec les applications.
1. Configurez des sauvegardes cohérentes avec les applications.
1. Déclenchez une sauvegarde cohérente avec les applications de la machine virtuelle.

### <a name="prepare-the-environment-for-an-application-consistent-backup"></a>Préparer l'environnement pour une sauvegarde cohérente avec les applications

1. Basculez vers l'utilisateur *racine* :

   ```bash
   sudo su -
   ```

1. Créez un utilisateur de sauvegarde :

   ```bash
   useradd -G backupdba azbackup
   ```
   
2. Configurez l'environnement de l'utilisateur de sauvegarde :

   ```bash
   echo "export ORACLE_SID=test" >> ~azbackup/.bashrc
   echo export ORACLE_HOME=/u01/app/oracle/product/19.0.0/dbhome_1 >> ~azbackup/.bashrc
   echo export PATH='$ORACLE_HOME'/bin:'$PATH' >> ~azbackup/.bashrc
   ```
   
3. Configurez l'authentification externe pour le nouvel utilisateur de sauvegarde. L'utilisateur de sauvegarde doit avoir accès à la base de données à l'aide de l'authentification externe, afin de ne pas utiliser de mot de passe.

   Tout d'abord, rebasculez vers l'utilisateur *oracle* :

   ```bash
   su - oracle
   ```

   Connectez-vous à la base de données à l'aide de sqlplus et vérifiez les paramètres par défaut de l'authentification externe :
   
   ```bash
   sqlplus / as sysdba
   SQL> show parameter os_authent_prefix
   SQL> show parameter remote_os_authent
   ```
   
   La sortie doit être semblable à l'exemple suivant : 

   ```output
   NAME                                 TYPE        VALUE
   ------------------------------------ ----------- ------------------------------
   os_authent_prefix                    string      ops$
   remote_os_authent                    boolean     FALSE
   ```

   Créez un utilisateur de base de données *azbackup* authentifié en externe et accordez-lui le privilège sysbackup :
   
   ```bash
   SQL> CREATE USER ops$azbackup IDENTIFIED EXTERNALLY;
   SQL> GRANT CREATE SESSION, ALTER SESSION, SYSBACKUP TO ops$azbackup;
   ```

   > [!IMPORTANT] 
   > Si vous rencontrez une erreur `ORA-46953: The password file is not in the 12.2 format.` lors de l'exécution de l'instruction `GRANT`, procédez comme suit pour migrer le fichier orapwd vers le format 12.2 :
   >
   > 1. Quittez sqlplus.
   > 1. Déplacez le fichier de mot de passe disposant de l'ancien format vers un nouveau nom.
   > 1. Migrez le fichier de mot de passe.
   > 1. Supprimez l'ancien fichier.
   > 1. Exécutez la commande suivante :
   >
   >    ```bash
   >    mv $ORACLE_HOME/dbs/orapwtest $ORACLE_HOME/dbs/orapwtest.tmp
   >    orapwd file=$ORACLE_HOME/dbs/orapwtest input_file=$ORACLE_HOME/dbs/orapwtest.tmp
   >    rm $ORACLE_HOME/dbs/orapwtest.tmp
   >    ```
   >
   > 1. Réexécutez l'opération `GRANT` dans sqlplus.
   >
   
4. Créez une procédure stockée pour consigner les messages de sauvegarde dans le journal des alertes de la base de données :

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

1. Basculez vers l'utilisateur *racine* :

   ```bash
   sudo su -
   ```

2. Créez un répertoire pour les sauvegardes cohérentes avec les applications :

   ```bash
   if [ ! -d "/etc/azure" ]; then
      sudo mkdir /etc/azure
   fi
   ```

3. Créez dans le répertoire */etc/azure* un fichier nommé *workload.conf* avec le contenu suivant, qui doit commencer par `[workload]`. La commande suivante crée le fichier et renseigne le contenu :

   ```bash
   echo "[workload]
   workload_name = oracle
   command_path = /u01/app/oracle/product/19.0.0/dbhome_1/bin/
   timeout = 90
   linux_user = azbackup" > /etc/azure/workload.conf
   ```

4. Téléchargez les scripts preOracleMaster.sql et postOracleMaster.sql à partir du [référentiel GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts) et copiez-les dans le répertoire */etc/azure*.

5. Modifiez les autorisations des fichiers.

```bash
   chmod 744 workload.conf preOracleMaster.sql postOracleMaster.sql 
   ```

### <a name="trigger-an-application-consistent-backup-of-the-vm"></a>Déclencher une sauvegarde cohérente avec les applications de la machine virtuelle

# <a name="portal"></a>[Portail](#tab/azure-portal)

1.  Sur le portail Azure, accédez à votre groupe de ressources **rg-oracle** et cliquez sur votre machine virtuelle **vmoracle19c**.

2.  Sur le panneau **Sauvegarde**, créez un nouveau **Coffre Recovery Services** dans le groupe de ressources **rg-oracle** et nommez-le **myVault**.
    Sous **Choisir une stratégie de sauvegarde**, utilisez **(Nouveau) DailyPolicy**. Si vous souhaitez modifier la fréquence de sauvegarde ou la durée de rétention, sélectionnez **Créer une nouvelle stratégie**.

    ![Page d’ajout de coffres Recovery Services](./media/oracle-backup-recovery/recovery-service-01.png)

3.  Pour continuer, cliquez sur **Activer la sauvegarde**.

    > [!IMPORTANT]
    > Après que vous avez cliqué sur **Activer la sauvegarde**, le processus de sauvegarde ne démarre pas avant l’expiration de l’heure planifiée. Pour configurer une sauvegarde immédiate, procédez comme suit.

4. Sur la page du groupe de ressources, cliquez sur le coffre Recovery Services **myVault** que vous venez de créer. Conseil : il peut être nécessaire d'actualiser la page pour le voir.

5.  Dans le panneau **myVault - Éléments de sauvegarde**, sous **NOMBRE D’ÉLÉMENTS DE SAUVEGARDE**, sélectionnez le nombre d’éléments de sauvegarde.

    ![Page de détails myVault d’Archivages de Recovery Services](./media/oracle-backup-recovery/recovery-service-02.png)

6.  Sur le côté droit du panneau **Éléments de sauvegarde (Machine virtuelle Azure)**, cliquez sur le bouton de sélection (**…**), puis sur **Sauvegarder maintenant**.

    ![Commande Sauvegarder maintenant des coffres Recovery Services](./media/oracle-backup-recovery/recovery-service-03.png)

7.  Acceptez la valeur par défaut du champ Conserver la sauvegarde jusqu'au, puis cliquez sur le bouton **OK**. Attendez que le processus de sauvegarde se termine. 

    Pour consulter l'état du travail de sauvegarde, cliquez sur **Travaux de sauvegarde**.

    ![Page de travail des coffres Recovery Services](./media/oracle-backup-recovery/recovery-service-04.png)

    L’état du travail de sauvegarde s’affiche dans l’image suivante :

    ![Page de travail d’Archivages de Recovery Services avec état](./media/oracle-backup-recovery/recovery-service-05.png)
    
    Notez que bien que l'exécution de la capture instantanée ne prenne que quelques secondes, son transfert vers le coffre peut prendre un certain temps, et le travail de sauvegarde n'est pas terminé tant que le transfert n'est pas allé jusqu'à son terme.

8. Pour une sauvegarde cohérente avec les applications, résolvez les éventuelles erreurs présentes dans le fichier journal. Le fichier journal se trouve sous /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/extension.log.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Créez un coffre Recovery Services :

   ```azurecli
   az backup vault create --location eastus --name myVault --resource-group rg-oracle
   ```

2. Activez la protection de la sauvegarde pour la machine virtuelle :

   ```azurecli
   az backup protection enable-for-vm \
      --resource-group rg-oracle \
      --vault-name myVault \
      --vm vmoracle19c \
      --policy-name DefaultPolicy
   ```

3. Déclenchez une sauvegarde pour qu'elle s'exécute maintenant plutôt que d'attendre qu'elle se déclenche selon la planification par défaut (5h00 UTC) : 

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

1.  Arrêtez l'instance d'Oracle :

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Supprimez les fichiers de données et les sauvegardes :

    ```bash
    sudo su - oracle
    cd /u02/oradata/TEST
    rm -f *.dbf
    cd /u02/fast_recovery_area
    rm -f *
    ```

### <a name="generate-a-restore-script-from-the-recovery-services-vault"></a>Générer un script de restauration à partir du coffre Recovery Services

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Sur le portail Azure, recherchez l'élément *myVault* et sélectionnez-le.

    ![Éléments de sauvegarde myVault des coffres Recovery Services](./media/oracle-backup-recovery/recovery-service-06.png)

2. Sur le panneau **Vue d'ensemble**, sélectionnez les **Éléments de sauvegarde** et la **Machine virtuelle Azure**, qui doit comporter un nombre d'éléments de sauvegarde non nul.

    ![Nombre d’éléments de sauvegarde de machine virtuelle Azure des coffres Recovery Services](./media/oracle-backup-recovery/recovery-service-07.png)

3. Votre machine virtuelle **vmoracle19c** est répertoriée sur la page Éléments de sauvegarde (Machines virtuelles Azure). Cliquez sur les points de suspension de droite pour afficher le menu, puis sélectionnez **Récupération de fichier**.

    ![Capture d’écran de la page de récupération de fichiers des coffres Recovery Services](./media/oracle-backup-recovery/recovery-service-08.png)

4. Dans le volet **Récupération de fichier (aperçu)**, cliquez sur **Télécharger le script**. Enregistrez ensuite le fichier de téléchargement (.py) dans un dossier de l'ordinateur client. Un mot de passe est généré pour exécuter le script. Copiez le mot de passe dans un fichier pour une utilisation ultérieure. 

    ![Options d’enregistrement de fichier de script téléchargé](./media/oracle-backup-recovery/recovery-service-09.png)

5. Copiez le fichier. py sur la machine virtuelle.

    L’exemple suivant montre comment utiliser une commande de copie sécurisée (scp) pour déplacer le fichier vers la machine virtuelle. Vous pouvez également copier le contenu vers le Presse-papiers, puis le coller dans un nouveau fichier configuré sur la machine virtuelle.

    > [!IMPORTANT]
    > Dans l’exemple suivant, veillez à mettre à jour les valeurs d’adresse IP et de dossier. Les valeurs doivent mapper au dossier d’enregistrement du fichier.
    >

    ```bash
    $ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour répertorier les points de récupération de votre machine virtuelle, utilisez la commande az backup recoverypoint list. Dans cet exemple, nous sélectionnons le point de récupération le plus récent pour la machine virtuelle nommée myVM et qui est protégée dans myRecoveryServicesVault :

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

Pour obtenir le script qui connecte ou monte le point de récupération sur votre machine virtuelle, utilisez az backup restore files mount-rp. L’exemple suivant obtient le script pour la machine virtuelle nommée myVM et qui est protégée dans myRecoveryServicesVault.

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

1. Créez un point de montage de restauration et copiez-y le script.

    Dans l'exemple suivant, créez un répertoire */restore* pour la capture instantanée à monter, déplacez le fichier vers le répertoire, puis modifiez le fichier afin qu'il appartienne à l'utilisateur racine et qu'il devienne exécutable.

    ```bash 
    ssh azureuser@<publicIpAddress>
    sudo su -
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

2. L’accès aux volumes montés est confirmé.

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

1. Copiez les fichiers de sauvegarde manquants dans la zone de récupération rapide :

    ```bash
    cd /restore/vmoracle19c-2020XXXXXXXXXX/Volume1/fast_recovery_area/TEST
    cp * /u02/fast_recovery_area/TEST
    cd /u02/fast_recovery_area/TEST
    chown -R oracle:oinstall *
    ```

2. Les commandes suivantes utilisent RMAN pour restaurer les fichiers de données manquants et récupérer la base de données :

    ```bash
    sudo su - oracle
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
3. Vérifiez que le contenu de la base de données a été entièrement récupéré :

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```

4. Démontez le point de restauration.

   ```bash
   umount /restore/vmoracle19c-20210107110037/Volume*
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

2. Supprimez la machine virtuelle. Lorsque vous y êtes invité, entrez « y » :

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

2. Sur le portail Azure, recherchez l'élément *myVault* et cliquez dessus.

    ![Éléments de sauvegarde myVault des coffres Recovery Services](./media/oracle-backup-recovery/recovery-service-06.png)
    
3.  Sur le panneau **Vue d'ensemble**, sélectionnez les **Éléments de sauvegarde** et la **Machine virtuelle Azure**, qui doit comporter un nombre d'éléments de sauvegarde non nul.

    ![Nombre d’éléments de sauvegarde de machine virtuelle Azure des coffres Recovery Services](./media/oracle-backup-recovery/recovery-service-07.png)

4.  Votre machine virtuelle **vmoracle19c** est répertoriée sur la page Éléments de sauvegarde (Machines virtuelles Azure). Cliquez sur le nom de la machine virtuelle.

    ![Page de récupération de machine virtuelle](./media/oracle-backup-recovery/recover-vm-02.png)

5.  Sur le panneau **vmoracle19c**, choisissez un point de restauration bénéficiant du type de cohérence **Cohérence avec les applications**, puis cliquez sur les points de suspension ( **...** ), à droite, pour afficher le menu.  Dans le menu, cliquez sur **Restaurer la machine virtuelle**.

    ![Commande Restaurer la machine virtuelle](./media/oracle-backup-recovery/recover-vm-03.png)

6.  Sur le panneau **Restaurer la machine virtuelle**, sélectionnez **Créer** et **Créer une machine virtuelle**. Entrez le nom de la machine virtuelle, **vmoracle19c**, et sélectionnez le réseau virtuel **vmoracle19cVNET** ; le sous-réseau sera automatiquement renseigné en fonction du réseau virtuel que vous sélectionnerez. Le processus de restauration de la machine virtuelle requiert un compte de stockage Azure situé dans le même groupe de ressources et dans la même région. Vous pouvez choisir le compte de stockage **orarestore** que vous avez configuré précédemment.

    ![Restaurer les valeurs de configuration](./media/oracle-backup-recovery/recover-vm-04.png)

7.  Pour restaurer la machine virtuelle, cliquez sur le bouton **Restaurer**.

8.  Pour afficher l’état du processus de restauration, cliquez sur **Travaux**, puis cliquez sur **Travaux de sauvegarde**.

    ![Commande d’état des travaux de sauvegarde](./media/oracle-backup-recovery/recover-vm-05.png)

    Cliquez sur l'opération de restauration **En cours** pour afficher l'état du processus de restauration :

    ![État du processus de restauration](./media/oracle-backup-recovery/recover-vm-06.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour configurer votre compte de stockage et le partage de fichiers, exécutez les commandes suivantes dans Azure CLI.

1. Créez le compte de stockage dans le même groupe de ressources et au même emplacement que votre machine virtuelle :

   ```azurecli
   az storage account create -n orarestore -g rg-oracle -l eastus --sku Standard_LRS
   ```

2. Récupérez la liste des points de récupération disponibles. 

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

3. Restaurez le point de récupération sur le compte de stockage. Remplacez `<myRecoveryPointName>` par un point de récupération de la liste générée à l'étape précédente :

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

4. Récupérez les détails du travail de restauration. La commande suivante permet d'obtenir plus de détails sur le travail restauré déclenché, y compris son nom, qui est nécessaire pour récupérer l'URI modèle. 

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

5. Récupérez les détails de l'URI à utiliser pour recréer la machine virtuelle. Remplacez le nom du travail de restauration de l'étape précédente par `<RestoreJobName>`.

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

2.  Arrêtez la machine virtuelle.

    ![Créer une adresse IP](./media/oracle-backup-recovery/create-ip-02.png)

3.  Accédez à **Mise en réseau**.

    ![Associer une adresse IP](./media/oracle-backup-recovery/create-ip-03.png)

4.  Cliquez sur **Attacher l'interface réseau**, choisissez la carte réseau d'origine (**vmoracle19cVMNic) à laquelle l'adresse IP publique d'origine est toujours associée, puis cliquez sur **OK**.

    ![Sélectionner le type de ressource et les valeurs de carte réseau](./media/oracle-backup-recovery/create-ip-04.png)

5.  Vous devez maintenant détacher la carte réseau qui a été créée avec l'opération de restauration de la machine virtuelle, car elle est configurée en tant qu'interface principale. Cliquez sur **Détacher l'interface réseau** et choisissez la nouvelle carte réseau de type **vmoracle19c-NIC-XXXXXXXXXXXX**, puis cliquez sur **OK**.

    ![Capture d'écran montrant où sélectionner l’interface réseau Détacher.](./media/oracle-backup-recovery/create-ip-05.png)
    
    La machine virtuelle recréée disposera alors de la carte réseau d'origine, qui est associée à l'adresse IP et aux règles de groupe de sécurité réseau d'origine.
    
    ![Valeur d’adresse IP](./media/oracle-backup-recovery/create-ip-06.png)
    
6.  Revenez à la **Vue d’ensemble** et cliquez sur **Démarrer**. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Arrêtez et libérez la machine virtuelle :

   ```azurecli
   az vm deallocate --resource-group rg-oracle --name vmoracle19c
   ```

2. Répertoriez les cartes réseau de machine virtuelle actuelles, restaurées et générées.

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

3. Attachez la carte réseau d'origine, dont le nom doit être de type `<VMName>VMNic`, en l'occurrence `vmoracle19cVMNic`. L'adresse IP publique d'origine, qui est toujours attachée à cette carte réseau, sera restaurée sur la machine virtuelle lorsque la carte réseau sera rattachée. 

   ```azurecli
   az vm nic add --nics vmoracle19cVMNic --resource-group rg-oracle --vm-name vmoracle19c
   ```

4. Détacher la carte réseau générée par la restauration

   ```azurecli
   az vm nic remove --nics vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf --resource-group rg-oracle --vm-name vmoracle19c
   ```

5. Démarrez la machine virtuelle :

   ```azurecli
   az vm start --resource-group rg-oracle --name vmoracle19c
   ```

---

### <a name="connect-to-the-vm"></a>Connexion à la machine virtuelle

Pour vous connecter à la machine virtuelle, utilisez le script suivant :

```azurecli
ssh <publicIpAddress>
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

## <a name="delete-the-vm"></a>Supprimer la machine virtuelle

Quand vous n’avez plus besoin de la machine virtuelle, vous pouvez utiliser la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées :

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>Étapes suivantes

[Tutoriel : Créer des machines virtuelles hautement disponibles](../../linux/create-cli-complete.md)

[Explorer des exemples Azure CLI de déploiement de machines virtuelles](../../linux/cli-samples.md)
