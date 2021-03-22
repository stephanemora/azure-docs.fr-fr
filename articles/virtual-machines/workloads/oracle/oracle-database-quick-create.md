---
title: Créer une base de données Oracle dans une machine virtuelle Azure | Microsoft Docs
description: Configurez et exécutez rapidement une base de données Oracle Database 12c dans votre environnement Azure.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: kegorman
ms.openlocfilehash: 8964248bb23b2b615c7e73e26d730fbd79b4e9e7
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102184455"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Créer une base de données Oracle dans une machine virtuelle Azure

Ce guide explique comment utiliser Azure CLI pour déployer une machine virtuelle Azure depuis l’[image Oracle de la galerie de la Place de marché](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) afin de créer une base de données Oracle 19c. Une fois que le serveur est déployé, vous vous connectez par le biais d’une connexion SSH pour configurer la base de données Oracle. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans ce guide de démarrage rapide. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé *rg-oracle* à l’emplacement *eastus*.

```azurecli-interactive
az group create --name rg-oracle --location eastus
```

## <a name="create-virtual-machine"></a>Créer une machine virtuelle

Pour créer une machine virtuelle, utilisez la commande [az vm create](/cli/azure/vm). 

L’exemple suivant permet de créer une machine virtuelle nommée `vmoracle19c`. Il crée également des clés SSH si elles n’existent pas déjà à un emplacement de clé par défaut. Pour utiliser un ensemble spécifique de clés, utilisez l’option `--ssh-key-value`.  

```azurecli-interactive 
az vm create ^
    --resource-group rg-oracle ^
    --name vmoracle19c ^
    --image Oracle:oracle-database-19-3:oracle-database-19-0904:latest ^
    --size Standard_DS2_v2 ^
    --admin-username azureuser ^
    --generate-ssh-keys ^
    --public-ip-address-allocation static ^
    --public-ip-address-dns-name vmoracle19c

```

Une fois que vous avez créé la machine virtuelle, Azure CLI affiche des informations similaires à l’exemple suivant. Notez la valeur pour `publicIpAddress`. Vous utilisez cette adresse pour accéder à la machine virtuelle.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/rg-oracle/providers/Microsoft.Compute/virtualMachines/vmoracle19c",
  "location": "eastus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "rg-oracle"
}
```
## <a name="create-and-attach-a-new-disk-for-oracle-datafiles-and-fra"></a>Créer et attacher un disque pour les fichiers de données Oracle et FRA

```bash
az vm disk attach --name oradata01 --new --resource-group rg-oracle --size-gb 128 --sku StandardSSD_LRS --vm-name vmoracle19c
```

## <a name="open-ports-for-connectivity"></a>Ouverture des ports pour la connectivité
Dans cette tâche, vous devez configurer certains points de terminaison externes utilisés par l’écouteur de base de données et EM Express. Pour ce faire, vous allez configurer le groupe de sécurité réseau Azure qui protège la machine virtuelle. 

1. Pour ouvrir le point de terminaison qui vous permet d’accéder à la base de données Oracle à distance, créez une règle de groupe de sécurité réseau comme indiqué ci-dessous :
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle ^
       --protocol tcp ^
       --priority 1001 ^
       --destination-port-range 1521
   ```
2. Pour ouvrir le point de terminaison que vous utilisez afin d’accéder à Oracle EM Express à distance, créez une règle de groupe de sécurité réseau avec la commande az network nsg rule create, comme suit :
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle-EM ^
       --protocol tcp ^
       --priority 1002 ^
       --destination-port-range 5502
   ```
3. En cas de besoin, obtenez à nouveau l’adresse IP publique de la machine virtuelle avec la commande az network public-ip show, comme suit :

   ```bash
   az network public-ip show ^
       --resource-group rg-oracle ^
       --name vmoracle19cPublicIP ^
       --query [ipAddress] ^
       --output tsv
   ```

## <a name="prepare-the-vm-environment"></a>Préparer l’environnement de la machine virtuelle

1. Connexion à la machine virtuelle

   Pour créer une session SSH avec la machine virtuelle, utilisez la commande suivante. Remplacez l’adresse IP par la valeur de `publicIpAddress` pour votre machine virtuelle.

   ```bash
   ssh azureuser@<publicIpAddress>
   ```

2. Passez à l’utilisateur racine

   ```bash
   sudo su -
   ```

3. Recherchez le dernier périphérique de disque créé. Nous allons le formater pour qu’il contienne les fichiers de données Oracle

   ```bash
   ls -alt /dev/sd*|head -1
   ```

   La sortie doit ressembler à ceci :
   ```output
   brw-rw----. 1 root disk 8, 16 Dec  8 22:57 /dev/sdc
   ```

4. Formatez le périphérique. 
   En tant qu’utilisateur racine, exécutez parted sur le périphérique 
   
   Créez d’abord une étiquette de disque :
   ```bash
   parted /dev/sdc mklabel gpt
   ```
   Créez ensuite une partition principale couvrant l’ensemble du disque :
   ```bash
   parted -a optimal /dev/sdc mkpart primary 0GB 64GB   
   ```
   Enfin, vérifiez les détails du périphérique en imprimant ses métadonnées :
   ```bash
   parted /dev/sdc print
   ```
   La sortie doit être semblable à ce qui suit :
   ```bash
   # parted /dev/sdc print
   Model: Msft Virtual Disk (scsi)
   Disk /dev/sdc: 68.7GB
   Sector size (logical/physical): 512B/4096B
   Partition Table: gpt
   Disk Flags:
   Number  Start   End     Size    File system  Name     Flags
    1      1049kB  64.0GB  64.0GB  ext4         primary
   ```

5. Créez un système de fichiers sur la partition de périphérique

   ```bash
   mkfs -t ext4 /dev/sdc1
   ```

6. Créer un point de montage
   ```bash
   mkdir /u02
   ```

7. Monter le disque

   ```bash
   mount /dev/sdc1 /u02
   ```

8. Changez les autorisations sur le point de montage

   ```bash
   chmod 777 /u02
   ```

9. Ajoutez le montage au fichier /etc/fstab. 

   ```bash
   echo "/dev/sdc1               /u02                    ext4    defaults        0 0" >> /etc/fstab
   ```
   
10. Mettez à jour le fichier ***/etc/hosts*** avec l’IP publique et le nom d’hôte.

    Changez ***« Public IP » et « VMname »*** en les remplaçant par les valeurs réelles :
  
    ```bash
    echo "<Public IP> <VMname>.eastus.cloudapp.azure.com <VMname>" >> /etc/hosts
    ```
11. Mettez à jour le fichier de nom d’hôte
    
    Utilisez la commande suivante pour ajouter le nom de domaine de la machine virtuelle au fichier **/etc/hostname**. Cela suppose que vous avez créé votre groupe de ressources et votre machine virtuelle dans la région **eastus** :
    
    ```bash
    sed -i 's/$/\.eastus\.cloudapp\.azure\.com &/' /etc/hostname
    ```

12. Ouvrez les ports du pare-feu
    
    Dans la mesure où SELinux est activé par défaut sur l’image de la Place de marché, nous devons autoriser le trafic via le pare-feu pour le port d’écoute de base de données 1521 et le port Enterprise Manager Express 5502. Exécutez les commandes suivantes en tant qu’utilisateur racine :

    ```bash
    firewall-cmd --zone=public --add-port=1521/tcp --permanent
    firewall-cmd --zone=public --add-port=5502/tcp --permanent
    firewall-cmd --reload
    ```
   

## <a name="create-the-database"></a>Création de la base de données

Le logiciel Oracle est déjà installé sur l’image Place de marché. Créer une base de données comme suit. 

1.  Passez à l’utilisateur **oracle** :

    ```bash
    sudo su - oracle
    ```
2. Démarrer l’écouteur de bases de données

   ```bash
   lsnrctl start
   ```
   Le résultat ressemble à ce qui suit :
  
   ```output
   LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 20-OCT-2020 01:58:18

   Copyright (c) 1991, 2019, Oracle.  All rights reserved.

   Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))

   Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
   STATUS of the LISTENER
   ------------------------
   Alias                     LISTENER
   Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Start Date                20-OCT-2020 01:58:18
   Uptime                    0 days 0 hr. 0 min. 0 sec
   Trace Level               off
   Security                  ON: Local OS Authentication
   SNMP                      OFF
   Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
   The listener supports no services
   The command completed successfully
   ```
3. Créez un répertoire de données pour les fichiers de données Oracle :

   ```bash
   mkdir /u02/oradata
   ```
    

3.  Exécutez l’outil Database Creation Assistant (Assistant Création de base de données) :

    ```bash
    dbca -silent \
       -createDatabase \
       -templateName General_Purpose.dbc \
       -gdbname test \
       -sid test \
       -responseFile NO_VALUE \
       -characterSet AL32UTF8 \
       -sysPassword OraPasswd1 \
       -systemPassword OraPasswd1 \
       -createAsContainerDatabase false \
       -databaseType MULTIPURPOSE \
       -automaticMemoryManagement false \
       -storageType FS \
       -datafileDestination "/u02/oradata/" \
       -ignorePreReqs
    ```

    La création de la base de données ne nécessite que quelques minutes.

    La sortie ressemble à ce qui suit :

    ```output
        Prepare for db operation
       10% complete
       Copying database files
       40% complete
       Creating and starting Oracle instance
       42% complete
       46% complete
       50% complete
       54% complete
       60% complete
       Completing Database Creation
       66% complete
       69% complete
       70% complete
       Executing Post Configuration Actions
       100% complete
       Database creation complete. For details check the logfiles at: /u01/app/oracle/cfgtoollogs/dbca/test.
       Database Information:
       Global Database Name:test
       System Identifier(SID):test
       Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/test/test.log" for further details.
    ```

4. Fixer les variables oracle

    Avant de vous connecter, vous devez définir la variable d’environnement *ORACLE_SID* :

    ```bash
        export ORACLE_SID=test
    ```

    Vous devez également ajouter la variable ORACLE_SID au fichier `.bashrc` des utilisateurs `oracle` pour les prochaines connexions à l’aide de la commande suivante :

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```

## <a name="oracle-em-express-connectivity"></a>Connectivité à Oracle EM Express

Pour un outil de gestion GUI que vous pouvez utiliser pour explorer la base de données, configurez Oracle EM Express. Pour vous connecter à Oracle EM Express, vous devez avoir préalablement configuré le port dans Oracle. 

1. Connectez-vous à votre base de données en utilisant sqlplus :

    ```bash
    sqlplus sys as sysdba
    ```

2. Une fois connecté, définissez le port 5502 pour EM Express.

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3.  Connectez-vous à EM Express à partir de votre navigateur. Assurez-vous que votre navigateur est compatible avec EM Express (l’installation de Flash est requise) : 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

    Vous pouvez vous connecter à l’aide du compte **SYS**, puis activer la case à cocher **as sysdba**. Utilisez le mot de passe **OraPasswd1** que vous avez défini lors de l’installation. 

    ![Capture d’écran de la page de connexion Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="automate-database-startup-and-shutdown"></a>Automatisation du démarrage et de l’arrêt de la base de données

La base de données Oracle par défaut ne s’enclenche pas automatiquement lorsque vous redémarrez la machine virtuelle. Pour configurer la base de données Oracle afin qu’elle démarre automatiquement, connectez-vous d’abord en tant qu’utilisateur racine. Puis, créez et mettez à jour des fichiers système.

1. Connexion en tant qu’utilisateur racine

    ```bash
    sudo su -
    ```

2.  Exécutez la commande suivante pour changer l’indicateur de démarrage automatisé en remplaçant `N` par `Y` dans le fichier `/etc/oratab` :

    ```bash
    sed -i 's/:N/:Y/' /etc/oratab
    ```

3.  Créez un fichier nommé `/etc/init.d/dbora` et collez le contenu suivant :

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/19.0.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  Modifiez les autorisations des fichiers *chmod* comme suit :

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Créez des liens symboliques pour le démarrage et l’arrêt, comme suit :

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Pour tester vos modifications, redémarrez la machine virtuelle :

    ```bash
    reboot
    ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois que vous avez terminé d’explorer votre première base de données Oracle sur Azure et que la machine virtuelle n’est plus nécessaire, vous pouvez utiliser la commande [az group delete](/cli/azure/group) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Comprendre comment protéger votre base de données dans Azure avec les [stratégies de sauvegarde Oracle](./oracle-database-backup-strategies.md)

En savoir plus sur les autres [solutions Oracle sur Azure](./oracle-overview.md). 

Effectuez le didacticiel relatif à [l’installation et à la configuration d’Oracle Automated Storage Management](configure-oracle-asm.md).
