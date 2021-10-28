---
title: Déploiement de la plateforme SAP BusinessObjects BI sur Azure pour Linux | Microsoft Docs
description: Déployer et configurer la plateforme SAP BusinessObjects BI sur Azure pour Linux
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: e3cc7420a976812d462b3f5c5e60878ba67641ff
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130249150"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Guide de déploiement de la plateforme SAP BusinessObjects BI pour Linux sur Azure

Cet article décrit la stratégie de déploiement de la plateforme SAP BusinessObjects BI (BOBI) sur Azure pour Linux. Dans cet exemple, vous configurez deux machines virtuelles avec disques managés SSD (solid-state drive) Premium comme répertoire d’installation. Vous utilisez Azure Database pour MySQL pour votre base de données CMS et vous partagez Azure NetApp Files pour votre serveur de référentiel de fichiers sur les deux serveurs. Sur les deux machines virtuelles, vous installez ensemble l’application web Java Tomcat par défaut et l’application de plateforme BI. Pour équilibrer la charge des demandes utilisateur, vous utilisez Azure Application Gateway avec des fonctionnalités de déchargement TLS/SSL natives.

Ce type d’architecture convient parfaitement pour les environnements hors production ou les déploiements à petite échelle. Dans les déploiements à grande échelle ou les environnements de production, vous pouvez prévoir des hôtes distincts pour votre application web. Vous pouvez également avoir plusieurs hôtes d’application BOBI, ce qui permet au serveur de traiter davantage d’informations.

![Diagramme du déploiement de SAP BOBI sur Azure pour Linux](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

Cet exemple utilise la configuration de version de produit et de système de fichiers suivante :

- Plateforme SAP BusinessObjects 4.3
- SUSE Linux Enterprise Server 12 SP5
- Azure Database pour MySQL (version : 8.0.15)
- Connecteur de l’API C MySQL – libmysqlclient (version : 6.1.11)

| Système de fichiers        | Description                                                                                                               | Taille (Go)             | Propriétaire  | Groupe  | Stockage                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | Système de fichiers pour l’installation de l’instance SAP BOBI, de l’application web Tomcat par défaut et des pilotes de base de données (le cas échéant). | Recommandations de dimensionnement SAP | bl1adm | sapsys | Disque managé Premium – SSD |
| /usr/sap/frsinput  | Le répertoire de montage est destiné aux fichiers partagés entre tous les hôtes BOBI qui seront utilisés en tant que répertoire de référentiel de fichiers d’entrée.  | Besoin métier         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | Le répertoire de montage est destiné aux fichiers partagés entre tous les hôtes BOBI qui seront utilisés en tant que répertoire de référentiel de fichiers de sortie. | Besoin métier         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Déployer une machine virtuelle Linux via Portail Azure

Dans cette section, vous créez deux machines virtuelles avec l’image du système d’exploitation Linux pour la plateforme SAP BOBI. Les principales étapes pour créer des machines virtuelles sont les suivantes :

1. Créez un [groupe de ressources](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups).

2. Créez un [réseau virtuel](../../../virtual-network/quick-create-portal.md#create-a-virtual-network).

   - N’utilisez pas un sous-réseau unique pour l’ensemble des services Azure dans le déploiement de la plateforme SAP BI. L’architecture de plateforme SAP BI impose de créer plusieurs sous-réseaux. Dans ce déploiement, vous créez trois sous-réseaux distincts pour l’application, le magasin de référentiels de fichiers et Application Gateway.
   - Dans Azure, Application Gateway et Azure NetApp Files doivent toujours se trouver sur des sous-réseaux séparés. Pour plus d’informations, consultez [Azure Application Gateway](../../../application-gateway/configuration-overview.md) et [Consignes pour planifier un réseau Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-network-topologies.md).

3. Créer un groupe à haute disponibilité. Pour assurer la redondance à chaque niveau dans un déploiement multi-instance, placez les machines virtuelles de chaque niveau dans un groupe à haute disponibilité. Veillez à séparer les groupes à haute disponibilité pour chaque niveau en fonction de votre architecture.

4. Créez la machine virtuelle 1, appelée **(azusbosl1)** .

   - Vous pouvez utiliser une image personnalisée ou choisir une image à partir de la Place de marché Azure. Pour plus d’informations, consultez [Déploiement d’une machine virtuelle à partir de la Place de marché Azure pour SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) ou [Déploiement d’une machine virtuelle avec une image personnalisée pour SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap).

5. Créez la machine virtuelle 2, appelée **(azusbosl2)** .
6. Ajoutez un disque SSD Premium. Vous l’utiliserez comme répertoire d’installation de SAP BOBI.

## <a name="provision-azure-netapp-files"></a>Approvisionner Azure NetApp Files

Avant de poursuivre la configuration d’Azure NetApp Files, familiarisez-vous avec la [documentation d’Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md).

Azure NetApp Files est disponible dans plusieurs [régions Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Vérifiez si la région Azure que vous avez sélectionnée est compatible avec Azure NetApp Files.

Pour vérifier la disponibilité d’Azure NetApp Files par région, consultez [Disponibilité d’Azure NetApp Files par région Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all).

### <a name="deploy-azure-netapp-files-resources"></a>Déployer des ressources Azure NetApp Files

Les instructions suivantes supposent que vous avez déjà déployé votre [réseau virtuel Azure](../../../virtual-network/virtual-networks-overview.md). Les ressources Azure NetApp Files ainsi que les machines virtuelles sur lesquelles les ressources Azure NetApp Files seront montées doivent être déployées dans le même réseau virtuel Azure ou dans des réseaux virtuels Azure appairés.

1. [Créez un compte Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md) dans la région Azure de votre choix.

2. [Configurez un pool de capacités Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md). L’architecture de la plateforme SAP BI présentée dans cet article utilise un seul pool de capacités Azure NetApp Files au niveau de service Premium. Pour le serveur de référentiel de fichiers SAP BI sur Azure, nous vous recommandons d’utiliser Azure NetApp Files avec un [niveau de service](../../../azure-netapp-files/azure-netapp-files-service-levels.md) Premium ou Ultra.

3. [Déléguer un sous-réseau à Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).

5. Déployez des volumes Azure NetApp Files en suivant les instructions de la page [Créer un volume NFS pour Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).

   Vous pouvez déployer des volumes avec NFSv3 et NFSv 4.1, car ces deux protocoles sont pris en charge par la plateforme SAP BOBI. Déployez les volumes dans leurs sous-réseaux Azure NetApp Files respectifs. Les adresses IP des volumes Azure NetApp Files sont attribuées automatiquement.

Gardez à l’esprit que les ressources Azure NetApp Files et les machines virtuelles Azure doivent être dans le même réseau virtuel Azure ou dans des réseaux virtuels Azure appairés. Par exemple, *azusbobi-frsinput* et *azusbobi-frsoutput* sont les noms de volumes et *nfs://10.31.2.4/azusbobi-frsinput* et *nfs://10.31.2.4/azusbobi-frsoutput* sont les chemins de fichiers pour les volumes Azure NetApp Files.

- Volume azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- Volume azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>Considérations importantes

Quand vous créez votre instance Azure NetApp Files pour le serveur de référentiel de fichiers de la plateforme SAP BOBI, tenez compte des points suivants :

- La taille de pool de capacité minimale est de 4 tébioctets (Tio).
- La taille de volume minimale est de 100 gibioctets (Gio).
- Azure NetApp Files et toutes les machines virtuelles où les volumes Azure NetApp Files seront montés doivent être déployés dans le même réseau virtuel Azure ou bien dans des [réseaux virtuels appairés](../../../virtual-network/virtual-network-peering-overview.md) dans la même région. L’accès à Azure NetApp Files par l’appairage de réseaux virtuels dans la même région est pris en charge. L’accès à Azure NetApp Files via le Peering mondial n’est pas pris en charge actuellement.
- Le réseau virtuel sélectionné doit avoir un sous-réseau délégué à Azure NetApp Files.
- Grâce à la [stratégie d’exportation](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md) d’Azure NetApp Files, vous pouvez contrôler les clients autorisés, le type d’accès (par exemple, en lecture et écriture ou en lecture seule).
- La fonctionnalité Azure NetApp Files ne tient pas encore compte des zones. Actuellement, la fonctionnalité n’est pas déployée dans toutes les zones de disponibilité d’une région Azure. Méfiez-vous de l’impact potentiel sur les temps de latence dans certaines régions Azure.
- Les volumes Azure NetApp Files peuvent être déployés en tant que volumes NFSv3 ou NFSv4.1. Les deux protocoles sont pris en charge pour les applications de la plateforme SAP BI.

## <a name="configure-file-systems-on-linux-servers"></a>Configurer des systèmes de fichiers sur des serveurs Linux

Les étapes de cette section utilisent le préfixe suivant :

**[A]**  : L’étape s’applique à tous les hôtes.

### <a name="format-and-mount-the-sap-file-system"></a>Formater et monter le système de fichiers SAP

1. **[A]** Listez tous les disques attachés.

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to virtual machine, whose device name is sdc
    ```

2. **[A]** Formatez l’appareil de bloc pour /usr/sap.

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** Créez le répertoire de montage.

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** Récupérez l’UUID de l’appareil de bloc.

    ```bash
    sudo blkid

    # It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** Gérez l’entrée de montage du système de fichiers dans /etc/fstab.

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** Montez le système de fichiers.

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-the-azure-netapp-files-volume"></a>Monter le volume Azure NetApp Files

1. **[A]** Créez des répertoires de montage.

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** Configurez le système d’exploitation client pour la prise en charge du montage NFSv4.1 (applicable uniquement si vous utilisez NFSv4.1).

   Si vous utilisez des volumes Azure NetApp Files avec le protocole NFSv4.1, exécutez la configuration suivante sur toutes les machines virtuelles où les volumes Azure NetApp Files NFSv4.1 doivent être montés.

   À cette étape, vous devez vérifier les paramètres de domaine NFS. Assurez-vous que le domaine est configuré comme domaine Azure NetApp Files par défaut (`defaultv4iddomain.com`) et que le mappage est défini sur `nobody`.

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   > Veillez à définir le domaine NFS dans /etc/idmapd.conf sur la machine virtuelle pour qu’il corresponde à la configuration de domaine par défaut sur Azure NetApp Files (`defaultv4iddomain.com`). En cas de non-correspondance, les autorisations de fichiers sur les volumes Azure NetApp Files qui sont montés sur les machines virtuelles s’affichent comme `nobody`.

   Vérifier `nfs4_disable_idmapping` Sa valeur doit être `Y`. Pour créer la structure de répertoire où se trouve `nfs4_disable_idmapping`, exécutez la commande mount. Vous ne serez pas en mesure de créer manuellement le répertoire sous /sys/modules, car l’accès est réservé pour le noyau/les pilotes.

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]** Ajoutez des entrées de montage.

   Si vous utilisez NFSv3 :

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   Si vous utilisez NFSv4.1 :

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** Montez des volumes NFS.

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-azure-database-for-mysql"></a>Configurer Azure Database pour MySQL

Cette section fournit des informations sur le provisionnement d’Azure Database pour MySQL à l’aide du portail Azure. Elle fournit également des instructions sur la création de bases de données CMS et d’audit pour la plateforme SAP BOBI et sur la création d’un compte d’utilisateur pour accéder à la base de données.

Les instructions s’appliquent uniquement si vous utilisez Azure Database pour MySQL. Pour les autres bases de données, consultez la documentation SAP ou celle de la base de données en question pour obtenir des instructions.

### <a name="create-a-database"></a>Création d'une base de données

Connectez-vous au portail Azure et effectuez les étapes décrites dans [Démarrage rapide : Créer un serveur Azure Database pour MySQL à l’aide du portail Azure](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Voici quelques points à prendre en compte quand vous provisionnez Azure Database pour MySQL :

- Sélectionnez la même région pour Azure Database pour MySQL que celle où les serveurs d’applications de la plateforme SAP BI s’exécutent.

- Choisissez une version de base de données prise en charge en vous référant à la [matrice de disponibilité des produits (PAM) pour SAP BI](https://support.sap.com/pam) correspondant à votre version de SAP BOBI.

- Dans **calcul + stockage**, sélectionnez **Configurer le serveur**, puis sélectionnez le niveau tarifaire approprié en fonction de la taille de votre sortie.

- **Croissance automatique du stockage** est activée par défaut. N’oubliez pas que vous pouvez uniquement effectuer un scale-up du [stockage](../../../mysql/concepts-pricing-tiers.md#storage), mais pas de scale-down.

- Par défaut, la **période de conservation des sauvegardes** est de sept jours. Vous pouvez [éventuellement la configurer](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) à 35 jours maximum.

- Les sauvegardes d’Azure Database pour MySQL sont localement redondantes par défaut. Si vous souhaitez effectuer les sauvegardes du serveur dans le stockage géoredondant, sélectionnez **Géographiquement redondant** dans **Options de redondance des sauvegardes**.

>[!Important]
>Une fois le serveur créé, vous ne pouvez plus modifier les paramètres dans [Options de redondance des sauvegardes](../../../mysql/concepts-backup.md#backup-redundancy-options).

>[!Note]
>La fonctionnalité de lien privé est disponible uniquement pour les serveurs Azure Database pour MySQL dans les niveaux tarifaires Usage général ou Mémoire optimisée. Vérifiez que le serveur de base de données se trouve dans l’un de ces niveaux tarifaires.

### <a name="configure-azure-private-link"></a>Configurer Azure Private Link

Dans cette section, vous créez une liaison privée qui permet aux machines virtuelles SAP BOBI de se connecter à Azure Database pour MySQL par le biais d’un point de terminaison privé. Azure Private Link intègre les services Azure à votre réseau virtuel privé.

1. Sélectionnez la base de données créée dans la section précédente.
2. Accédez à **Sécurité** > **Connexions des points de terminaison privés**.
3. Dans **Connexions des points de terminaison privés**, sélectionnez **Point de terminaison privé**.
4. Sélectionnez **Abonnement** > **Groupe de ressources** > **Emplacement**.
5. Entrez le **nom** du point de terminaison privé.
6. Dans la section **Ressource**, spécifiez les informations suivantes :
   - Type de ressource : Microsoft.DBforMySQL/servers
   - Ressource : la base de données MySQL créée dans la section précédente
   - Sous-ressource cible : mysqlServer
7. Dans la section **Mise en réseau**, sélectionnez le **réseau virtuel** et le **sous-réseau** sur lesquels l’application SAP BOBI est déployée.
   >[!NOTE]
   >Si un groupe de sécurité réseau (NSG) est activé dans le sous-réseau, il sera désactivé pour les points de terminaison privés de ce sous-réseau uniquement. Les autres ressources du sous-réseau seront toujours soumises au NSG.
8. Pour l’option **Intégrer avec une zone DNS privée**, acceptez la **valeur par défaut (oui)** .
9.  Sélectionnez votre **zone DNS privée** dans la liste déroulante.
10. Sélectionnez **Vérifier + créer** et créez un point de terminaison privé.

Pour plus d'informations, consultez [Liaison privée pour Azure Database pour MySQL](../../../mysql/concepts-data-access-security-private-link.md).

### <a name="create-the-cms-and-audit-databases"></a>Créer les bases de données CMS et d’audit

1. Téléchargez et installez MySQL Workbench à partir du [site web MySQL](https://dev.mysql.com/downloads/workbench/). Veillez à installer MySQL Workbench sur le serveur ayant accès à Azure Database pour MySQL.

2. Connectez-vous au serveur en utilisant MySQL Workbench. Suivez les instructions fournies dans [Obtenir des informations de connexion](../../../mysql/connect-workbench.md#get-connection-information). Si le test de connexion réussit, vous obtenez le message suivant :

   ![Capture d’écran du message dans MySQL Workbench.](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. Sous l’onglet Requête SQL, exécutez la requête suivante pour créer un schéma pour les bases de données CMS et d’audit.

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```

4. Créez un compte d’utilisateur avec lequel vous connecter au schéma.

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. Vérifiez les privilèges et les rôles du compte d’utilisateur MySQL :

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-on-a-linux-server"></a>Installer le connecteur de l’API C MySQL sur un serveur Linux

Pour que le serveur d’applications SAP BOBI accède à une base de données, il a besoin de pilotes clients de base de données. Pour accéder aux bases de données CMS et d’audit, vous devez utiliser le connecteur de l’API C MySQL pour Linux. Une connexion ODBC à la base de données CMS n’est pas prise en charge. Cette section fournit des instructions sur la configuration du connecteur de l’API C MySQL sur Linux.

1. Consultez [Pilotes MySQL et outils de gestion compatibles avec Azure Database pour MySQL](../../../mysql/concepts-compatibility.md). Lisez les informations relatives au pilote **Connecteur MySQL/C (libmysqlclient)** dans cet article.

2. Pour télécharger des pilotes, consultez les [archives des produits MySQL](https://downloads.mysql.com/archives/c-c/).

3. Sélectionnez le système d’exploitation et téléchargez le package rpm de composant partagé du connecteur MySQL. Dans cet exemple, la version mysql-connector-c-shared-6.1.11 du connecteur est utilisée.

4. Installez le connecteur dans toutes les instances d’application SAP BOBI.

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. Vérifiez le chemin d’accès de libmysqlclient.so.

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. Définissez `LD_LIBRARY_PATH` pour qu’il référence le répertoire `/usr/lib64` associé au compte d’utilisateur à utiliser pour l’installation.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>Préparation du serveur

Les étapes de cette section utilisent le préfixe suivant :

**[A]**  : L’étape s’applique à tous les hôtes.

1. **[A]** En fonction de la saveur de Linux (SLES ou RHEL), vous devez définir les paramètres du noyau et installer les bibliothèques requises. Reportez-vous à la section« System requirements » (Configuration système requise) dans [Business Intelligence Platform Installation Guide for Unix](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3) (Guide d’installation de la plateforme Business Intelligence pour UNIX).

2. **[A]** Vérifiez que le fuseau horaire de votre machine est correctement défini. Dans le guide d’installation, consultez [Additional Unix and Linux requirements](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/46b143336e041014910aba7db0e91070.html) (Conditions requises supplémentaires pour UNIX et Linux).

3. **[A]** Créez un compte d’utilisateur (**bl1** adm) et un groupe (sapsys) sous lesquels les processus d’arrière-plan du logiciel peuvent s’exécuter. Servez-vous de ce compte pour effectuer l’installation et utiliser le logiciel. Le compte ne nécessite pas de privilèges racine.

4. **[A]** Configurez l’environnement du compte d’utilisateur (**bl1** adm) pour utiliser des paramètres régionaux UTF-8 pris en charge et vérifiez que votre logiciel de console prend en charge les jeux de caractères UTF-8. Pour vous assurer que votre système d’exploitation utilise les paramètres régionaux corrects, définissez les variables d’environnement `LC_ALL` et `LANG` sur les paramètres régionaux de votre choix dans votre environnement utilisateur (**bl1** adm).

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** Configurez le compte d’utilisateur (**bl1** adm).

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. Téléchargez et extrayez le média de la plateforme SAP BusinessObjects BI à partir de SAP Service Marketplace.

## <a name="installation"></a>Installation

Vérifiez les paramètres régionaux définis pour le compte d’utilisateur **bl1** adm sur le serveur :

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

Accédez au média de la plateforme SAP BOBI, puis exécutez la commande suivante avec le compte d’utilisateur **bl1** adm :

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

Suivez les instructions données dans le guide d’installation de la [plateforme SAP BOBI](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) pour UNIX propre à la version que vous utilisez. Voici quelques points à prendre en compte quand vous installez la plateforme SAP BOBI :

- Dans **Configure Product Registration** (Configurer l’enregistrement du produit), vous pouvez soit utiliser une clé de licence temporaire pour les solutions SAP BusinessObjects à partir de la note SAP [1288121](https://launchpad.support.sap.com/#/notes/1288121), soit générer une clé de licence dans SAP Service Marketplace.

- Dans **Select Install Type** (Sélectionner le type d’installation), sélectionnez **Full** (Complète) sur le premier serveur (`azusbosl1`). Pour l’autre serveur (`azusbosl2`), sélectionnez **Custom / Expand** (Personnalisée/Étendue) pour étendre la configuration BOBI actuelle.

- Dans **Select Default or Existing Database** (Sélectionner une base de données existante ou par défaut), sélectionnez **Configure an existing database** (Configurer une base de données existante). Vous serez alors invité à sélectionner des bases de données CMS et d’audit. Sélectionnez **MySQL** pour ces types de bases de données.

  Vous pouvez également sélectionner **No auditing database** (Aucune base de données d’audit) si vous ne voulez pas configurer l’audit pendant l’installation.

- Dans l’**écran Select Java Web Application Server** (Sélectionner le serveur d’applications web Java), sélectionnez les options appropriées en fonction de votre architecture SAP BOBI. Dans cet exemple, nous avons sélectionné l’option 1, qui installe un serveur Tomcat sur la même plateforme SAP BOBI.

- Entrez les informations de la base de données CMS dans **Configure CMS Repository Database - MySQL** (Configurer la base de données du référentiel CMS – MySQL). L’exemple suivant montre les informations de la base de données CMS à entrer pour une installation Linux. Azure Database pour MySQL est utilisé sur le port 3306 par défaut.
  
  ![Capture d’écran illustrant le déploiement de SAP BOBI sur Linux - Base de données CMS.](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- (Facultatif) Entrez les informations de la base de données d’audit dans **Configure Audit Repository Database - MySQL** (Configurer la base de données du référentiel d’audit – MySQL). L’exemple suivant montre les informations de la base de données d’audit à entrer pour une installation Linux.

  ![Capture d’écran illustrant le déploiement de SAP BOBI sur Linux - Base de données d’audit.](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- Suivez les instructions et saisissez les entrées requises pour terminer l’installation.

Pour un déploiement multi-instance, exécutez le programme d’installation sur un deuxième hôte (`azusbosl2`). Dans l’écran **Select Install Type** (Sélectionner le type d’installation), sélectionnez **Custom / Expand** (Personnalisée/Étendue) pour étendre la configuration BOBI actuelle.

Dans Azure Database pour MySQL, une passerelle redirige les connexions vers les instances de serveur. Une fois la connexion établie, le client de MySQL affiche la version de MySQL définie dans la passerelle, et non la version en cours d’exécution sur votre instance de serveur MySQL. Pour déterminer la version de votre instance de serveur MySQL, utilisez la commande `SELECT VERSION();` à l’invite de MySQL. Pour plus d’informations, consultez [Versions prises en charge du serveur Azure Database pour MySQL](../../../mysql/concepts-supported-versions.md).

![Capture d’écran illustrant le déploiement de SAP BOBI sur Linux - Paramètres CMC.](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>Après installation

Après une installation multi-instance de la plateforme SAP BOBI, vous devez effectuer d’autres étapes post-configuration pour la prise en charge de la haute disponibilité des applications.

### <a name="configure-the-cluster-name"></a>Définir le nom du cluster

Dans le cadre d’un déploiement multi-instance de la plateforme SAP BOBI, vous voulez exécuter plusieurs serveurs CMS ensemble dans un cluster. Un cluster se compose d’au moins deux serveurs CMS qui travaillent conjointement sur une base de données système CMS commune. Si un nœud qui s'exécute sur un CMS échoue, un nœud situé sur un autre CMS continue à traiter les requêtes de la plateforme BI. Sur une plateforme SAP BOBI, un nom de cluster reflète par défaut le nom d’hôte du premier CMS installé.

Pour définir le nom d’un cluster sur Linux, suivez les instructions indiquées dans le [Guide d’administration de la plateforme SAP Business Intelligence](https://help.sap.com/viewer/2e167338c1b24da9b2a94e68efd79c42/4.3). Après avoir défini le nom du cluster, suivez la note SAP [1660440](https://launchpad.support.sap.com/#/notes/1660440) pour définir l'entrée système par défaut dans la page de connexion au Launchpad CMC ou BI.

### <a name="configure-input-and-output-filestore-location-to-azure-netapp-files"></a>Définir l’emplacement du magasin de fichiers d’entrée et de sortie pour Azure NetApp Files

Le magasin de fichiers fait référence aux répertoires de disque où se trouvent les fichiers SAP BusinessObjects. L’emplacement par défaut du serveur de référentiel de fichiers de la plateforme SAP BOBI correspond au répertoire d’installation local. Dans un déploiement multi-instance, il est important de configurer le magasin de fichiers sur un stockage partagé comme Azure NetApp Files. De cette façon, le magasin de fichiers sera accessible depuis tous les serveurs, quel que soit leur niveau de stockage.

1. Si vous n’avez pas encore créé de volumes NFS, créez-les dans Azure NetApp Files. (Aidez-vous des instructions fournies dans la section précédente « Provisionner Azure NetApp Files ».)

2. Montez le volume NFS. (Aidez-vous des instructions fournies dans la section précédente « Monter le volume Azure NetApp Files ».)

3. Suivez la note SAP [2512660](https://launchpad.support.sap.com/#/notes/0002512660) pour changer le chemin du référentiel de fichiers (entrée et sortie).

### <a name="session-replication-in-tomcat-clustering"></a>Réplication de session dans le clustering Tomcat

Tomcat prend en charge le clustering d’au moins deux serveurs d’applications pour la réplication et le basculement de session. Les sessions de la plateforme SAP BOBI sont sérialisées, mais une session utilisateur peut basculer en toute transparence vers une autre instance de Tomcat, même en cas de défaillance d’un serveur d’applications.

Prenons l’exemple d’un utilisateur qui est connecté à un serveur web, lequel s’arrête alors que l’utilisateur est en train de parcourir l’arborescence des dossiers dans une application SAP BI. Avec un cluster correctement configuré, l’utilisateur peut continuer à parcourir l’arborescence des dossiers sans être redirigé vers la page de connexion.

Reportez-vous à la note SAP [2808640](https://launchpad.support.sap.com/#/notes/2808640) pour connaître la procédure de configuration du clustering Tomcat au moyen de la multidiffusion. Notez toutefois qu’Azure ne prend pas en charge la multidiffusion. Pour que le cluster Tomcat fonctionne dans Azure, vous devez utiliser [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) (voir la note SAP [2764907](https://launchpad.support.sap.com/#/notes/2764907)). Pour plus d’informations, consultez le billet de blog [Tomcat Clustering using Static Membership for SAP BusinessObjects BI Platform](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) (Clustering Tomcat avec l’appartenance statique pour la plateforme SAP BusinessObjects BI).

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>Équilibrage de la charge du niveau web de la plateforme SAP BI

Dans le cadre d’un déploiement multi-instance de SAP BOBI, les serveurs d’applications web Java (niveau web) sont exécutés sur deux hôtes au moins. Pour répartir uniformément la charge des utilisateurs entre les serveurs web, vous pouvez utiliser un équilibreur de charge entre les utilisateurs finaux et les serveurs web. Dans Azure, vous pouvez utiliser Azure Load Balancer ou Azure Application Gateway pour gérer le trafic vers vos serveurs d’applications web. Les détails de chaque offre sont expliqués dans la section suivante.

#### <a name="azure-load-balancer"></a>Azure Load Balancer

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) est un équilibreur de charge de couche 4 (TCP, UDP) à haute performance et à faible latence. Il répartit le trafic entre les machines virtuelles saines. Une sonde d’intégrité d’équilibreur de charge surveille un port spécifié sur chaque machine virtuelle et distribue le trafic uniquement aux machines virtuelles opérationnelles. Vous pouvez choisir d’utiliser un équilibreur de charge public ou un équilibreur de charge interne, selon que vous souhaitez ou pas que la plateforme SAP BI soit accessible à partir d’Internet. Azure Load Balancer est redondant interzone, garantissant une haute disponibilité de la fonctionnalité Zones de disponibilité.

Dans le diagramme ci-dessous, référez-vous à l’illustration de l’équilibreur de charge interne. Le serveur d’applications web s’exécute sur le port 8080, le port HTTP par défaut de Tomcat, qui sera surveillé par la sonde d’intégrité. Toutes les requêtes entrantes émanant d’utilisateurs finaux sont redirigées vers les serveurs d’applications web (`azusbosl1` ou `azusbosl2`). Load Balancer ne prend pas en charge la terminaison TLS/SSL (également appelé déchargement TLS/SSL). Si vous utilisez Load Balancer pour répartir le trafic entre les serveurs web, utilisez Standard Load Balancer.

> [!NOTE]
> Si des machines virtuelles sans adresse IP publique sont placées dans le pool de l’équilibreur Standard Load Balancer interne (aucune adresse IP publique), il n’y a pas de connectivité Internet sortante, sauf si vous effectuez une configuration supplémentaire pour autoriser le routage vers des points de terminaison publics. Pour plus d’informations, consultez [Connectivité de point de terminaison public pour les machines virtuelles avec Azure Standard Load Balancer dans les scénarios de haute disponibilité SAP](high-availability-guide-standard-load-balancer-outbound-connections.md).

![Diagramme qui montre l’équilibrage du trafic entre les serveurs web par Azure Load Balancer.](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway"></a>Azure Application Gateway

[Azure Application Gateway](../../../application-gateway/overview.md) fournit Application Delivery Controller (ADC) en tant que service. Ce service aide l’application à diriger le trafic des utilisateurs vers un ou plusieurs serveurs d’applications. Il offre différentes capacités d’équilibrage de charge de couche 7, comme le déchargement TLS/SSL, le pare-feu d’applications web (WAF) et l’affinité de session basée sur les cookies.

Dans une plateforme SAP BI, Application Gateway dirige le trafic web des applications vers les ressources spécifiées (`azusbosl1` ou `azusbos2`). Vous attribuez un écouteur à un port, créez des règles et ajoutez des ressources à un pool. Dans le diagramme suivant, Application Gateway a une adresse IP privée (10.31.3.20) qui joue le rôle de point d’entrée pour les utilisateurs. Il gère également les connexions TLS/SSL (HTTPS - TCP/443) entrantes, déchiffre le TLS/SSL et passe la requête non chiffrée (HTTP -TCP/8080) aux serveurs. Il simplifie les opérations, avec la gestion d’un seul certificat TLS/SSL sur Application Gateway.

![Diagramme qui montre l’équilibrage du trafic entre les serveurs web par Application Gateway.](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

Si vous souhaitez configurer Application Gateway pour un serveur web SAP BOBI, consultez le billet de blog [Load Balancing SAP BOBI Web Servers using Azure Application Gateway](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) (Équilibrage de la charge des serveurs web SAP BOBI à l’aide d’Azure Application Gateway).

> [!NOTE]
> Azure Application Gateway est la solution recommandée pour équilibrer la charge du trafic vers un serveur web. Elle fournit plusieurs fonctionnalités utiles, telles que le déchargement SSL, la gestion SSL centralisée pour réduire la surcharge de chiffrement et de déchiffrement sur le serveur, un algorithme de tourniquet (round-robin) pour répartir le trafic, des fonctionnalités WAF et la haute disponibilité.

## <a name="sap-bobi-platform-reliability-on-azure"></a>Fiabilité de la plateforme SAP BOBI sur Azure

La plateforme SAP BOBI comprend différents niveaux, qui sont chacun optimisés pour des tâches et des opérations spécifiques. Quand un composant d’un de ces niveaux n’est plus disponible, une application SAP BOBI devient inaccessible ou limitée dans ses fonctionnalités. Assurez-vous que la conception de chaque niveau est fiable afin de garantir le bon fonctionnement de l’application sans aucune interruption de l’activité.

Ce guide explique comment les fonctionnalités natives d’Azure, combinées à la configuration de la plateforme SAP BOBI, améliorent la disponibilité du déploiement SAP. Cette section se concentre sur les options suivantes :

- **Sauvegarde et restauration** : processus consistant à créer des copies périodiques des données et des applications à un emplacement distinct. Vous pouvez restaurer ou récupérer un état antérieur si les données ou applications d’origine sont perdues ou endommagées.

- **Haute disponibilité** : une plateforme à haute disponibilité dispose d’au moins deux copies de chaque élément dans une région Azure pour que l’application reste opérationnelle si l’un des serveurs n’est plus disponible.
- **Reprise d’activité après sinistre** : il s’agit d’un processus de restauration des fonctionnalités de l’application en cas de perte irrécupérable, par exemple si l’intégralité de la région Azure devenait indisponible en raison d’une catastrophe naturelle.

L’implémentation de cette solution varie selon la nature de la configuration du système dans Azure. Personnalisez vos solutions de sauvegarde/restauration, de haute disponibilité et de récupération d’activité après sinistre en fonction des besoins de l’entreprise.

## <a name="backup-and-restore"></a>Sauvegarde et restauration

La sauvegarde et la restauration sont un élément essentiel de toute stratégie de récupération d’activité après sinistre. Pour développer une stratégie complète pour la plateforme SAP BOBI, identifiez les composants qui entraînent un temps d’arrêt du système ou une interruption de l’application. Dans la plateforme SAP BOBI, la sauvegarde des composants suivants est essentielle pour protéger l’application :

- Répertoire d’installation SAP BOBI (disques Premium managés)
- Serveur de référentiel de fichiers (Azure NetApp Files ou Azure Premium Files)
- Base de données CMS (Azure Database pour MySQL ou base de données sur machines virtuelles Azure)

La section suivante décrit comment implémenter une stratégie de sauvegarde et de restauration pour chacun de ces composants.

### <a name="backup-and-restore-for-sap-bobi-installation-directory"></a>Sauvegarde et restauration du répertoire d’installation SAP BOBI

Dans Azure, la [Sauvegarde Azure](../../../backup/backup-overview.md) constitue la méthode la plus simple pour sauvegarder des serveurs d’applications et tous les disques attachés. Elle fournit des sauvegardes indépendantes et isolées pour éviter une destruction involontaire des données de vos machines virtuelles. Les sauvegardes sont stockées dans un coffre Recovery Services avec gestion intégrée des points de récupération. La configuration et la mise à l’échelle sont simples à effectuer, les sauvegardes sont optimisées, et vous pouvez facilement restaurer les données dont vous avez besoin.

Dans le cadre du processus de sauvegarde, un instantané est créé et les données sont transférées vers le coffre, tout cela sans impact sur les charges de travail de production. Pour plus d’informations, consultez [Cohérence des instantanés](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency). Vous pouvez également choisir de sauvegarder un sous-ensemble des disques de données de votre machine virtuelle en utilisant la fonctionnalité de sauvegarde et de restauration sélectives de disques. Pour plus d’informations, consultez [Sauvegarde de machines virtuelles Azure](../../../backup/backup-azure-vms-introduction.md) et [Forum aux questions – Sauvegarde de machines virtuelles Azure](../../../backup/backup-azure-vm-backup-faq.yml).

### <a name="backup-and-restore-for-file-repository-server"></a>Sauvegarde et restauration du serveur de référentiel de fichiers

Si votre déploiement de SAP BOBI sur Linux le permet, vous pouvez utiliser Azure NetApp Files comme magasin de fichiers pour votre plateforme SAP BOBI. Choisissez une des options de sauvegarde et de restauration suivantes en fonction du stockage que vous utilisez pour le magasin de fichiers.

- **Azure NetApp Files** : vous pouvez créer des instantanés à la demande, et planifier des instantanés automatiques en utilisant des stratégies d’instantané. Les instantanés fournissent une copie à un moment donné de votre volume. Pour plus d’informations, consultez [Gérer les instantanés avec Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md).

- Si vous avez créé un serveur NFS distinct, veillez à implémenter la stratégie de sauvegarde et de restauration pour le même serveur.

### <a name="backup-and-restore-for-cms-and-audit-databases"></a>Sauvegarde et restauration des bases de données CMS et d’audit

Sur les machines virtuelles Linux, les bases de données CMS et d’audit peuvent s’exécuter sur n’importe quelle base de données prise en charge. Pour plus d’informations, consultez la [matrice de prise en charge](businessobjects-deployment-guide.md#support-matrix). Il est important que vous adoptiez la stratégie de sauvegarde et de restauration de la base de données utilisée pour le magasin de données CMS et d'audit.

- Azure Database pour MySQL crée automatiquement des sauvegardes de serveur, qu’il conserve dans un stockage localement redondant ou géoredondant configuré par l’utilisateur. Azure Database pour MySQL effectue des sauvegardes des fichiers de données et du journal des transactions. Selon la taille de stockage maximale prise en charge, des sauvegardes complètes et différentielles (serveurs de stockage de 4 To au maximum) ou des sauvegardes d’instantanés (serveurs de stockage jusqu’à 16 To) sont réalisées. Ces sauvegardes vous permettent de restaurer un serveur à n’importe quel point dans le temps au cours de la période de conservation des sauvegardes configurée. Par défaut, la période de conservation des sauvegardes est de sept jours, mais vous pouvez [éventuellement la configurer](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) jusqu’à trois jours. Toutes les sauvegardes sont chiffrées à l’aide du chiffrement AES 256 bits. Ces fichiers de sauvegarde ne sont pas exposés aux utilisateurs et ne peuvent pas être exportés. Ces sauvegardes sont utilisables uniquement pour les opérations de restauration dans Azure Database pour MySQL. Vous pouvez utiliser [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) pour copier une base de données. Pour plus d’informations, consultez [Sauvegarde et restauration dans Azure Database pour MySQL](../../../mysql/concepts-backup.md).

- Pour une base de données installée sur une machine virtuelle Azure, vous pouvez utiliser les outils de sauvegarde standard ou [Sauvegarde Azure](../../../backup/sap-hana-db-about.md) pour les bases de données prises en charge. Vous pouvez aussi utiliser des outils de sauvegarde tiers pris en charge qui fournissent un agent permettant la sauvegarde et la récupération de tous les composants de la plateforme SAP BOBI.

## <a name="high-availability"></a>Haute disponibilité

La *haute disponibilité* fait référence à un ensemble de technologies qui peuvent réduire les interruptions informatiques en assurant la continuité d’activité des applications et des services. Cela se fait par le biais de composants redondants, tolérants aux pannes ou protégés par basculement au sein du même centre de centres. Dans notre cas, les centres de données se trouvent dans une même région Azure. Pour plus d’informations, consultez [Scénarios et architecture de haute disponibilité pour SAP](sap-high-availability-architecture-scenarios.md).

En fonction du résultat de dimensionnement de la plateforme SAP BOBI, vous devez concevoir le paysage et déterminer la distribution des composants BI entre les machines virtuelles et les sous-réseaux Azure. Le niveau de redondance dans l’architecture distribuée dépend de l’objectif de délai de récupération (RTO) et de l’objectif de point de récupération (RPO) recherchés par votre entreprise. La plateforme SAP BOBI comprend différents niveaux, et les composants de chaque niveau doivent être conçus de manière à assurer la redondance. Par exemple :

- Serveurs d’applications redondants, tels que les serveurs d’applications BI et le serveur web.
- Composants uniques, tels que la base de données CMS, le serveur de référentiel de fichiers et l’équilibreur de charge Load Balancer.

Les sections suivantes expliquent comment atteindre la haute disponibilité sur chaque composant de la plateforme SAP BOBI.

### <a name="high-availability-for-application-servers"></a>Haute disponibilité pour les serveurs d’applications

Vous pouvez atteindre la haute disponibilité pour les serveurs d’applications au moyen de la redondance. Pour ce faire, configurez plusieurs instances de serveurs web et BI sur différentes machines virtuelles Azure.

Pour réduire l’impact des temps d’arrêt dus à un ou plusieurs événements, suivez ces conseils :

- Utilisez des zones de disponibilité pour la protection contre les défaillances au niveau du centre de données.
- Configurez plusieurs machines virtuelles dans un groupe à haute disponibilité pour assurer la redondance.
- Utilisez des disques managés pour les machines virtuelles dans un groupe à haute disponibilité.
- Configurez chaque couche Application dans des groupes à haute disponibilité distincts.

Pour plus d’informations, consultez [Gérer la disponibilité des machines virtuelles Linux](../../availability.md).

>[!Important]
>Les concepts de zones de disponibilité Azure et de groupes à haute disponibilité Azure s’excluent mutuellement. Vous pouvez déployer deux machines virtuelles ou plus dans une zone de disponibilité ou dans un groupe à haute disponibilité, mais pas dans les deux à la fois.

### <a name="high-availability-for-a-cms-database"></a>Haute disponibilité pour une base de données CMS

Si vous utilisez Azure Database pour MySQL pour vos bases de données CMS et d’audit, vous disposez par défaut d’un framework de haute disponibilité localement redondant. Il vous suffit de sélectionner la région et les capacités de haute disponibilité, de redondance et de résilience inhérentes au service, sans avoir à configurer de composants supplémentaires. Si la stratégie de déploiement de la plateforme SAP BOBI s’applique à plusieurs zones de disponibilité, vous devez vous assurer d’avoir une redondance de zone pour vos bases de données CMS et d’audit. Pour plus d’informations, consultez [Haute disponibilité dans Azure Database pour MySQL](../../../mysql/concepts-high-availability.md) et [Haute disponibilité pour Azure SQL Database](../../../azure-sql/database/high-availability-sla.md).

Pour les autres déploiements de la base de données CMS, consultez les informations sur la haute disponibilité dans les [Guides de déploiement SGBD pour une charge de travail SAP](dbms_guide_general.md).

### <a name="high-availability-for-filestore"></a>Haute disponibilité pour le magasin de fichiers

Le terme « magasin de fichiers » désigne les répertoires de disque où sont stockés les contenus tels que les rapports, les univers et les connexions. Il est partagé par tous les serveurs d’applications de ce système. Vous devez donc vous assurer qu'il est hautement disponible, tout comme les autres composants de la plateforme SAP BOBI.

Pour une plateforme SAP BOBI sur Linux, vous pouvez choisir [Azure Premium Files](../../../storage/files/storage-files-introduction.md) ou [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) comme partages de fichiers ; les deux sont conçus pour être hautement disponibles et hautement durables par nature. Pour plus d’informations, consultez [Redondance](../../../storage/files/storage-files-planning.md#redundancy) pour Azure Files.

> [!Important]
> Le protocole SMB pour Azure Files est en disponibilité générale, tandis que la prise en charge du protocole NFS pour Azure Files est actuellement en préversion. Pour plus d’informations, consultez [Préversion de la prise en charge de NFS 4.1 par Azure Files](https://azure.microsoft.com/blog/nfs-41-support-for-azure-files-is-now-in-preview/).

Notez que ce service de partage de fichiers n’est pas disponible dans toutes les régions. Pour obtenir les dernières informations à ce sujet, consultez [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/). Si le service n’est pas disponible dans votre région, vous pouvez créer un serveur NFS à partir duquel vous pourrez partager le système de fichiers avec l’application SAP BOBI. Toutefois, vous devrez également tenir compte de sa haute disponibilité.

### <a name="high-availability-for-load-balancer"></a>Haute disponibilité pour Load Balancer

Pour distribuer le trafic sur un serveur web, vous pouvez utiliser Azure Load Balancer ou Azure Application Gateway. La redondance de l’un ou de l’autre peut être obtenue en fonction du SKU choisi pour le déploiement.

- Pour Azure Load Balancer, la redondance peut être obtenue en configurant Standard Load Balancer comme redondant interzone. Pour en savoir plus, consultez [Standard Load Balancer et Zones de disponibilité](../../../load-balancer/load-balancer-standard-availability-zones.md).

- Pour Application Gateway, la haute disponibilité peut être obtenue en fonction du type de niveau sélectionné lors du déploiement.
   -  Le SKU v1 prend en charge les scénarios de haute disponibilité lorsque vous avez déployé deux instances ou plus. Azure distribue ces instances entre les domaines de mise à jour et d’erreur pour garantir qu'elles n’échouent pas toutes en même temps. Vous obtenez une redondance intra-zone.
   -  Le SKU v2 garantit automatiquement que les nouvelles instances sont réparties sur les domaines d’erreur et les domaines de mise à jour. Si vous choisissez une redondance de zone, les instances les plus récentes sont également réparties sur les zones de disponibilité pour offrir une résilience zonale en cas d’échec. Pour plus de détails, consultez [Application Gateway v2 avec mise à l’échelle automatique et redondance interzone](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md).

### <a name="reference-high-availability-architecture-for-sap-bobi-platform"></a>Architecture de haute disponibilité de référence pour la plateforme SAP BOBI

Le diagramme suivant illustre la configuration de la plateforme SAP BOBI quand vous exécutez un groupe à haute disponibilité sur un serveur Linux. L’architecture présente l’utilisation de différents services, comme Azure Application Gateway, Azure NetApp Files et Azure Database pour MySQL. Ces services intègrent une redondance, ce qui simplifie la gestion de différentes solutions à haute disponibilité.

Notez que le trafic entrant (HTTPS-TCP/443) est équilibré en charge par le biais du SKU Azure Application Gateway v1, qui est hautement disponible lorsqu’il est déployé sur deux instances ou plus. Plusieurs instances du serveur web, des serveurs d’administration et des serveurs de traitement sont déployées sur des machines virtuelles distinctes pour assurer la redondance, et chaque niveau est déployé dans des groupes à haute disponibilité distincts. Azure NetApp Files disposant d’une redondance intégrée dans le centre de données, vos volumes Azure NetApp Files pour le serveur de référentiels de fichiers sont hautement disponibles. La base de données CMS est provisionnée sur Azure Database pour MySQL, qui a une haute disponibilité inhérente. Pour plus d’informations, consultez [Haute disponibilité dans Azure Database pour MySQL](../../../mysql/concepts-high-availability.md).

![Diagramme illustrant la redondance de la plateforme SAP BusinessObjects BI avec les groupes à haute disponibilité.](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

L’architecture précédente fournit des informations sur la façon dont un déploiement SAP BOBI sur Azure peut être effectué. Toutefois, elle ne couvre pas toutes les options de configuration possibles. Vous pouvez adapter votre déploiement aux besoins de votre entreprise.

Dans plusieurs régions Azure, vous pouvez utiliser des zones de disponibilité. Ainsi, vous disposez d’une source d’alimentation électrique, d’un système de refroidissement et d’un réseau indépendants. Cela vous permet de déployer une application dans deux ou trois zones de disponibilité. Si vous souhaitez bénéficier d’une haute disponibilité entre les régions de disponibilité, vous pouvez déployer la plateforme SAP BOBI dans ces zones, en veillant à ce que chaque composant de l’application soit redondant interzone.

## <a name="disaster-recovery"></a>Récupération d'urgence

Cette section explique la stratégie à adopter pour assurer la reprise d’activité après sinistre sur une plateforme SAP BOBI exécutée sur Linux. Elle complète le document [Reprise d’activité pour SAP](../../../site-recovery/site-recovery-sap.md), qui présente les principales ressources pour l’approche SAP globale de la reprise d’activité après sinistre. Pour la plateforme SAP BOBI, consultez la note SAP [2056228](https://launchpad.support.sap.com/#/notes/2056228), qui décrit les méthodes suivantes permettant d’implémenter un environnement sécurisé de reprise d’activité après sinistre.

- Utilisation totale ou sélective de la gestion du cycle de vie ou de la fédération pour promouvoir et distribuer le contenu à partir du système principal.
- Copie périodique du contenu des serveurs de référentiels CMS et de fichiers.

Ce guide se concentre sur la deuxième option. Il ne couvre pas toutes les options de configuration possibles pour la reprise d’activité ; il décrit une solution qui utilise des services Azure natifs en association avec une configuration de plateforme SAP BOBI.

>[!Important]
>La disponibilité de chaque composant de la plateforme SAP BOBI doit être prise en compte dans la région secondaire, et vous devez minutieusement tester l’ensemble de la stratégie de reprise d’activité.

### <a name="reference-disaster-recovery-architecture-for-sap-bobi-platform"></a>Architecture de référence de la reprise d’activité après sinistre pour la plateforme SAP BOBI

Cette architecture de référence exécute un déploiement multi-instance de la plateforme SAP BOBI avec des serveurs d’applications redondants. Pour la reprise d’activité après sinistre, vous devez basculer tous les composants de la plateforme SAP BOBI vers une région secondaire. Dans le diagramme ci-dessous, Azure NetApp Files est utilisé en tant que magasin de fichiers, Azure Database pour MySQL en tant que référentiel CMS et d'audit, et Azure Application Gateway comme équilibreur de charge. La stratégie de reprise d’activité diffère selon le composant. Les différences sont décrites dans les sections suivantes.

![Diagramme montrant la reprise d’activité après sinistre pour la plateforme SAP BusinessObjects BI.](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

### <a name="load-balancer"></a>Équilibrage de charge

Un équilibreur de charge est utilisé pour répartir le trafic entre les différents serveurs d’applications web de la plateforme SAP BOBI. Dans Azure, vous pouvez utiliser la solution Azure Load Balancer ou Azure Application Gateway. Afin de bénéficier de la reprise d’activité après sinistre pour les services d’équilibrage de charge, vous devez implémenter une autre instance d’Azure Load Balancer ou d’Azure Application Gateway dans la région secondaire. Pour conserver la même URL après un basculement en cas de reprise d’activité, vous devez modifier l’entrée dans le DNS pour qu’elle référence le service d’équilibrage de charge qui est exécuté dans la région secondaire.

### <a name="vms-running-web-and-bi-application-servers"></a>Machines virtuelles exécutant des serveurs d’applications web et BI

Utilisez [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) pour répliquer des machines virtuelles exécutant des serveurs d’applications web et BI dans la région secondaire. Ce service réplique les serveurs et tous leurs disques managés attachés dans la région secondaire. Ainsi, en cas de sinistre ou de panne, vous pouvez facilement basculer vers votre environnement répliqué et continuer à travailler. Pour commencer la réplication de toutes les machines virtuelles d’application SAP vers le centre de données de reprise d’activité Azure, suivez les instructions dans [Répliquer une machine virtuelle vers Azure](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md).

### <a name="file-repository-servers"></a>Serveurs de référentiel de fichiers

Le magasin de fichiers est un répertoire du disque où sont stockés physiquement des fichiers comme les rapports et les documents BI. Il est important que tous les fichiers du magasin de fichiers soient synchronisés avec la région de reprise d’activité. Selon le type de service de partage de fichiers que vous utilisez pour la plateforme SAP BOBI exécutée sur Linux, la stratégie de reprise d’activité appropriée doit être adoptée pour synchroniser le contenu.

- **Azure NetApp Files** fournit des volumes NFS et SMB ; vous pouvez donc utiliser n’importe quel outil de copie basé sur des fichiers pour répliquer les données entre des régions Azure. Pour plus d’informations sur la copie d’un volume dans une autre région, consultez [Questions fréquentes (FAQ) sur Azure NetApp Files](../../../azure-netapp-files/faq-data-migration-protection.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region).

  Vous pouvez utiliser la réplication interrégionale Azure NetApp Files, actuellement en [préversion](https://azure.microsoft.com/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/). Seuls les blocs modifiés sont envoyés sur le réseau dans un format fiable et compressé. Cela limite le volume de données nécessaire à la réplication dans les différentes régions, réduisant de fait le coût de transfert des données. Cela réduit également la durée de réplication, ce qui vous permet d’obtenir un RPO inférieur. Pour plus d'informations, consultez [Configuration requise et considérations pour la réplication inter-région](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md).

- **Les fichiers Azure Premium** prennent uniquement en charge le stockage localement redondant (LRS) et le stockage redondant interzone (ZRS). Dans le cadre de la stratégie de reprise d’activité après sinistre, vous pouvez utiliser [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) ou [Azure PowerShell](/powershell/module/az.storage/) pour copier vos fichiers vers un autre compte de stockage dans une région différente. Pour plus d’informations, consultez [Récupération d’urgence et basculement de compte de stockage](../../../storage/common/storage-disaster-recovery-guidance.md).

   > [!Important]
   > Le protocole SMB pour Azure Files est en disponibilité générale, tandis que la prise en charge du protocole NFS pour Azure Files est actuellement en préversion. Pour plus d’informations, consultez [Préversion de la prise en charge de NFS 4.1 par Azure Files](https://azure.microsoft.com/blog/nfs-41-support-for-azure-files-is-now-in-preview/).

### <a name="cms-database"></a>Base de données CMS

Les bases de données CMS et d’audit de la région de reprise d’activité après sinistre doivent être une copie des bases de données exécutées dans la région primaire. Selon le type de base de données, il est important de copier la base de données dans la région de reprise d’activité après sinistre en fonction du RTO et du RPO requis par l’entreprise.

#### <a name="azure-database-for-mysql"></a>Azure Database pour MySQL

Azure Database pour MySQL offre plusieurs options de récupération des bases de données après un sinistre. Choisissez une option adaptée aux besoins de votre entreprise.

- Activez les réplicas en lecture inter-régions pour améliorer la planification de la continuité d’activité et de la reprise d’activité. Vous pouvez effectuer la réplication à partir du serveur source vers cinq réplicas au maximum. Les réplicas en lecture sont mis à jour de manière asynchrone au moyen de la technologie de réplication des journaux de transactions de MySQL. Les réplicas sont de nouveaux serveurs que vous gérez de manière similaire à des serveurs classiques dans Azure Database pour MySQL. Pour plus d’informations, consultez [Réplicas en lecture dans Azure Database pour MySQL](../../../mysql/concepts-read-replicas.md).

- Utilisez la fonctionnalité de géorestauration pour restaurer le serveur à l’aide de sauvegardes géoredondantes. Ces sauvegardes sont accessibles même lorsque la région dans laquelle votre serveur est hébergé est hors connexion. Vous pouvez effectuer une restauration à partir de ces sauvegardes dans n’importe quelle autre région et remettre votre serveur en ligne.

  > [!NOTE]
  > La géorestauration n’est possible que si vous avez provisionné le serveur avec le stockage de sauvegardes géoredondantes. La modification des **options de redondance des sauvegardes** après la création du serveur n’est pas prise en charge. Pour plus d’informations, consultez [Redondance des sauvegardes](../../../mysql/concepts-backup.md#backup-redundancy-options).

Dans le tableau suivant, vous trouverez la recommandation de reprise d’activité de chaque niveau utilisé dans cet exemple.

| Niveaux de la plateforme SAP BOBI   | Recommandation                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure Application Gateway | Configuration parallèle d’Application Gateway dans une région secondaire.                                             |
| Serveurs d'applications web   | Réplication à l’aide d’Azure Site Recovery.                                                                  |
| Serveurs d’applications décisionnelles    | Réplication à l’aide de Site Recovery.                                                                        |
| Azure NetApp Files        | Outil de copie basé sur des fichiers pour répliquer les données dans une région secondaire, ou réplication interrégionale.      |
| Azure Database pour MySQL  | Réplicas en lecture entre régions, ou restauration de sauvegarde à partir de sauvegardes géoredondantes.                                |

## <a name="next-steps"></a>Étapes suivantes

- [Configurer la reprise d’activité pour un déploiement d’application SAP multiniveau](../../../site-recovery/site-recovery-sap.md)
- [Planification et implémentation de machines virtuelles Azure pour SAP](planning-guide.md)
- [Déploiement de machines virtuelles Azure pour SAP](deployment-guide.md)
- [Déploiement SGBD de machines virtuelles Azure pour SAP](./dbms_guide_general.md)
