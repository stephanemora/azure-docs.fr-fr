---
title: Solution Azure VMware par CloudSimple - Migration avec Azure Data Box
description: Migration de données en bloc vers une solution VMware Azure par CloudSimple avec Azure Data Box
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5f19b98fbbbad2f43227bfa2f73eab47bf7b1699
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817388"
---
# <a name="migrating-data-to-azure-vmware-solution-by-cloudsimple-using-azure-data-box"></a>Migration de données vers une solution VMware Azure par CloudSimple avec Azure Data Box

La solution cloud Microsoft Azure Data Box permet d’envoyer plusieurs téraoctets de données vers Azure de façon rapide, économique et fiable. Le transfert de données sécurisé est accéléré par l’envoi à votre adresse d’un appareil de stockage Data Box propriétaire. Chaque appareil de stockage dispose d’une capacité de stockage maximum utile de 80 To, et est acheminé vers votre centre de données via un transporteur régional. L’appareil dispose d’un caisson robuste afin de protéger et de sécuriser les données au cours du transport.

Avec Azure Data Box, vous pouvez migrer en bloc vos données VMware vers votre cloud privé.  Les données de votre environnement vSphere local sont copiées vers la Data Box en utilisant le protocole NFS.  La migration de données en bloc consiste à copier une copie à un point dans le temps de machines virtuelles, de la configuration et des données associées vers la Data Box et à envoyer dans Azure.

Cet article porte sur les points suivants :

* Configuration d’Azure Data Box.
* Copie de données depuis un environnement VMware local vers la Data Box en utilisant NFS.
* Préparation du retour d’Azure Data Box.
* Préparation de données blob pour la copie vers la solution VMware Azure par CloudSimple.
* Copie des données depuis Azure vers votre cloud privé.

## <a name="scenarios"></a>Scénarios

Azure Data Box doit être utilisé dans les scénarios suivants pour la migration de données en bloc.

* Migrez une grande quantité de données depuis un site local vers la solution Azure VMware par CloudSimple à utiliser comme base de référence et synchronisez les différences sur le réseau.
* Migrez une grande quantité de machines virtuelles hors tension (machines virtuelles froides).
* Migrez les données de machines virtuelles pour configurer des environnements de développement et de test.
* Migrez un grand nombre de modèles de machine virtuelle, de fichiers ISO, de disques de machine virtuelle.

## <a name="before-you-begin"></a>Avant de commencer

* Vérifiez les prérequis et [commandez la Data Box](../databox/data-box-deploy-ordered.md) à partir de votre portail Azure.  Lors du processus de commande, vous devez sélectionner un compte de stockage qui autorise le stockage d’objets blob.  Une fois que vous avez reçu la Data Box, connectez-le à votre réseau local et [configurez la Data Box](../databox/data-box-deploy-set-up.md) avec une adresse IP accessible depuis votre réseau de gestion vSphere.

* Créez un réseau virtuel et un compte de stockage dans la même région Azure que celle où votre solution Azure VMware par CloudSimple est provisionnée.

* Créez une [connexion de réseau virtuel Azure](cloudsimple-azure-network-connection.md) depuis votre cloud privé vers le réseau virtuel où le compte de stockage est créé en suivant les étapes mentionnées dans l’article [Connecter un réseau virtuel Azure à CloudSimple à l’aide d’ExpressRoute](virtual-network-connection.md)

## <a name="set-up-azure-data-box-for-nfs"></a>Configurer Azure Data Box pour NFS

Connectez-vous à l’interface utilisateur web locale de votre Azure Data Box en suivant les étapes mentionnées dans la section **Se connecter à votre appareil** dans l’article [Tutoriel : Branchement des câbles et connexion à une Azure Data Box](../databox/data-box-deploy-set-up.md).  Configurez la Data Box pour autoriser l’accès aux clients NFS.

1. Dans l’interface utilisateur web locale, accédez à la page **Connect and copy** (Connexion et copie). Sous **Paramètres NFS**, cliquez sur **Accès au client NFS**. 

    ![Configurer l’accès au client NFS 1](media/nfs-client-access.png)

2. Entrez l’adresse IP des hôtes VMware ESXi, puis cliquez sur **Ajouter**. Vous pouvez configurer l’accès pour tous les hôtes de votre cluster vSphere en répétant cette étape. Cliquez sur **OK**.

    ![Configurer l’accès au client NFS 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Toujours créer un dossier pour les fichiers que vous envisagez de copier sous le partage, puis copier les fichiers dans ce dossier**. Le dossier créé sous les partages d’objets blob de pages et d’objets blob de blocs représente un conteneur dans lequel les données sont chargées en tant qu’objets blob. Vous ne pouvez pas copier de fichiers directement dans le dossier *root* du compte de stockage.

Sous les partages d’objet blob de blocs et d’objet blob de pages, les entités de premier niveau sont des conteneurs et les entités de second niveau sont des objets blob. Sous les partages Azure Files, les entités de premier niveau sont des partages et les entités de second niveau sont des fichiers.

Le tableau suivant montre le chemin UNC aux partages sur votre Data Box et l’URL de chemin Stockage Azure où les données sont chargées. La dernière URL de chemin de Stockage Azure peut être dérivée à partir du chemin de partage UNC.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Objets blob de blocs Azure | <li>Chemin UNC aux partages : `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL de Stockage Azure : `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Objets blob de pages Azure  | <li>Chemin UNC aux partages : `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL de Stockage Azure : `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>Chemin UNC aux partages : `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL de Stockage Azure : `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Utilisez des objets blob de blocs Azure pour copier les données VMware.

## <a name="mount-nfs-share-as-a-datastore-on-on-premises-vcenter-cluster-and-copy-data"></a>Monter un partage NFS en tant que magasin de données sur un cluster vCenter local et copier des données

Le partage NFS de votre Azure Data Box doit être monté en tant que magasin de données sur votre cluster vCenter local ou votre hôte VMware ESXi pour copier les données.  Une fois qu’il est monté, les données peuvent être copiées dans le magasin de données NFS.

1. Connectez-vous à votre serveur vCenter local.

2. Cliquez avec le bouton droit sur le **Centre de données**, sélectionnez **Stockage**, cliquez sur **Nouveau magasin de données**, puis cliquez sur **Suivant**.

   ![Ajouter un nouveau magasin de données](media/databox-migration-add-datastore.png)

3. À l’étape 1 de l’Assistant Ajout d’un magasin de données, sélectionnez le type **NFS**.

   ![Ajouter un nouveau magasin de données - type](media/databox-migration-add-datastore-type.png)

4. À l’étape 2, sélectionnez **NFS 3** comme version de NFS, puis cliquez sur **Suivant**.

   ![Ajouter un nouveau magasin de données - Version de NFS](media/databox-migration-add-datastore-nfs-version.png)

5. À l’étape 3, spécifiez le nom du magasin de données, le chemin et le serveur.  Vous pouvez utiliser l’adresse IP de votre Data Box pour le serveur.  Le chemin du dossier doit être au format `/<StorageAccountName_BlockBlob>/<ContainerName>/`.

   ![Ajouter un nouveau magasin de données - Configuration de NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. À l’étape 4, sélectionnez les hôtes ESXi où vous voulez monter le magasin de données, puis cliquez sur **Suivant**.  Dans un cluster, sélectionnez tous les hôtes pour garantir la migration des machines virtuelles.

   ![Ajouter un nouveau magasin de données - Sélectionner les hôtes](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. À l’étape 5, passez en revue le récapitulatif, puis cliquez sur **Terminer**.

## <a name="copy-data-to-data-box-nfs-datastore"></a>Copier des données vers le magasin de données NFS Data Box

Les machines virtuelles peuvent être migrées ou clonées vers le nouveau magasin de données.  Les machines virtuelles inutilisées qui doivent être migrées peuvent l’être vers le magasin de données NFS Data Box en utilisant **Storage vMotion**.  Les machines virtuelles actives peuvent être **clonées** vers le magasin données NFS Data Box.

* Identifiez la liste des machines virtuelles qui peuvent être **déplacées**.
* Identifiez la liste des machines virtuelles qui doivent être **clonées**.

### <a name="move-virtual-machine-to-data-box-datastore"></a>Déplacer une machine virtuelle vers le magasin de données Data Box

1. Cliquez avec le bouton droit sur la machine virtuelle que vous voulez déplacer vers le magasin de données Data Box, puis sélectionnez **Migrer**.

    ![Migrer une machine virtuelle](media/databox-migration-vm-migrate.png)

2. Sélectionnez **Changer seulement le stockage** comme type de migration, puis cliquez sur **Suivant**.

    ![Migrer une machine virtuelle - Stockage seulement](media/databox-migration-vm-migrate-change-storage.png)

3. Sélectionnez Magasin de données Data Box comme destination, puis cliquez sur **Suivant**.

    ![Migrer une machine virtuelle - Sélectionner un magasin de données](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Passez en revue les informations et cliquez sur **Terminer**.

5. Répétez les étapes 1 à 4 pour d’autres machines virtuelles.

> [!TIP]
> Vous pouvez sélectionner plusieurs machines virtuelles dans le même état d’alimentation (sous tension ou hors tension) et les migrer en bloc.

Le stockage de la machine virtuelle sera migré vers le magasin de données NFS depuis Azure Data Box.  Une fois que toutes les machines virtuelles sont migrées, vous pouvez arrêter les machines virtuelles sous tension en vue de la migration des données vers la solution Azure VMware.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-data-box-datastore"></a>Cloner une machine virtuelle ou un modèle de machine virtuelle vers un magasin de données Data Box

1. Cliquez avec le bouton droit sur une machine virtuelle ou sur un modèle de machine virtuelle que vous voulez cloner.  Sélectionnez **Cloner**, **Cloner vers une machine virtuelle**

    ![Clone de machine virtuelle](media/databox-migration-vm-clone.png)

2. Sélectionnez un nom pour la machine virtuelle clonée ou le modèle de machine virtuelle.

3. Sélectionnez le dossier où vous voulez placer l’objet cloné, puis cliquez sur **Suivant**.

4. Sélectionnez le cluster ou le pool de ressources où vous voulez placer l’objet cloné, puis cliquez sur **Suivant**.

5. Sélectionnez Magasin de données NFS Azure Data Box comme emplacement de stockage, puis cliquez sur **Suivant**.

    ![Clone de machine virtuelle - Sélectionner un magasin de données](media/databox-migration-vm-clone-select-datastore.png)

6. Sélectionnez les options de personnalisation si vous voulez personnaliser les options de l’objet cloné, puis cliquez sur **Suivant**.

7. Passez en revue les configurations, puis cliquez sur **Terminer**.

8. Répétez les étapes 1 à 7 pour d’autres machines virtuelles ou modèles de machine virtuelle.

Les machines virtuelles sont clonées et stockées sur le magasin de données NFS d’Azure Data Box.  Une fois que les machines virtuelles sont clonées, vérifiez que les machines virtuelles clonées sont mises hors tension en vue de la migration des données vers la solution Azure VMware.

### <a name="copy-iso-files-to-data-box-datastore"></a>Copier des fichiers ISO dans le magasin de données Data Box

1. À partir de l’interface utilisateur web de votre vCenter local, accédez à **Stockage**.  Sélectionnez Magasin de données NFS Data Box, puis cliquez sur **Fichiers**.  Créez un **Nouveau dossier** pour stocker les fichiers ISO.

    ![Copier un fichier ISO - Créer un dossier](media/databox-migration-create-folder.png)

2. Donnez un nom au dossier où les fichiers ISO doivent être stockés.

3. Double-cliquez sur le dossier nouvellement créé pour accéder au contenu.

4. Cliquez sur **Charger des fichiers**, puis sélectionnez les fichiers ISO que vous voulez charger.
    
    ![Copier un fichier ISO - Charger des fichiers](media/databox-migration-upload-iso.png)

> [!TIP]
> Si vous avez déjà des fichiers ISO sur votre magasin de données local, vous pouvez sélectionner les fichiers et **Copier vers** le magasin de données NFS Data Box.


## <a name="prepare-azure-data-box-for-return"></a>Préparer Azure Data Box pour le retour

Une fois que toutes les données des machines virtuelles, les données des modèles de machine virtuelle et les fichiers ISO sont copiés dans le magasin de données NFS Data Box, le magasin de données peut être déconnecté de votre vCenter.  Toutes les machines virtuelles et tous les modèles de machine virtuelle doivent être supprimés de l’inventaire avant que le magasin de données puisse être déconnecté.

### <a name="remove-objects-from-inventory"></a>Supprimer des objets de l’inventaire

1. À partir de l’interface utilisateur web de votre vCenter local, accédez à **Stockage**.  Sélectionnez Magasin de données NFS Data Box, puis cliquez sur **Machines virtuelles**.

2. Vérifiez que toutes les machines virtuelles sont hors tension.

    ![Supprimer les machines virtuelles de l’inventaire - Hors tension](media/databox-migration-select-databox-vm.png)

3. Sélectionnez toutes les machines virtuelles, cliquez avec le bouton droit et sélectionnez **Supprimer de l’inventaire**.

    ![Supprimer les machines virtuelles de l’inventaire](media/databox-migration-remove-vm-from-inventory.png)

4. Sélectionnez **Modèles de machine virtuelle dans les dossiers** dans les boutons du haut et répétez l’étape 3. 

### <a name="remove-azure-data-box-nfs-datastore-from-vcenter"></a>Supprimer le magasin de données NFS Azure Data Box de vCenter

Le magasin de données NFS Data Box doit être déconnecté des hôtes VMware ESXi avant de préparer le retour.

1. À partir de l’interface utilisateur web de votre vCenter local, accédez à **Stockage**.

2. Cliquez avec le bouton droit sur le magasin de données NFS Data Box et sélectionnez **Démonter le magasin**.

    ![Démonter le magasin de données Data Box](media/databox-migration-unmount-datastore.png)

3. Sélectionnez tous les hôtes ESXi où le magasin de stockage est monté, puis cliquez sur **OK**.

    ![Démonter le magasin de données Data Box - Sélectionner les hôtes](media/databox-migration-unmount-datastore-select-hosts.png)

4. Passez en revue et acceptez les avertissements, puis cliquez sur **OK**.

### <a name="prepare-data-box-for-return-and-return-the-data-box"></a>Préparer la Data Box pour le retour et la retourner

Suivez les étapes décrites dans l’article [Retourner l’appareil Azure Data Box et vérifier le chargement des données dans Azure](../databox/data-box-deploy-picked-up.md) pour retourner la Data Box.  Vérifiez l’état de la copie des données vers votre compte de stockage Azure et, une fois que l’état indique que c’est terminé, vous pouvez vérifier les données dans votre compte de stockage Azure.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution-by-cloudsimple"></a>Copier des données depuis Stockage Azure vers la solution Azure VMware par CloudSimple

Les données copiées vers votre Azure Data Box seront disponibles sur votre compte de stockage Azure une fois que l’état de la commande de votre Data Box apparaît comme terminé.  Les données peuvent maintenant être copiées vers votre solution Azure VMware par CloudSimple.  Les données du compte de stockage doivent être copiées en utilisant le protocole NFS vers le magasin de données vSAN de votre cloud privé.  Copiez d’abord les données du magasin d’objets blob vers un disque managé sur une machine virtuelle Linux dans Azure avec **AzCopy**.  Rendez le disque managé disponible via le protocole NFS et montez le partage NFS en tant que magasin de données sur votre cloud privé, puis copiez les données.  Cette méthode permet de copier plus rapidement les données vers votre cloud privé. 

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-export-as-nfs-share"></a>Copier des données vers votre cloud privé en utilisant une machine virtuelle Linux et des disques managés, et les exporter en tant que partage NFS

1. Créez une [machine virtuelle Linux](../virtual-machines/linux/quick-create-portal.md) dans Azure, dans la même région que celle où votre compte de stockage est créé et qui dispose d’une connexion de réseau virtuel Azure à votre cloud privé.

2. Créez un disque managé d’une taille supérieure à la quantité de données blob et [attachez-le à votre machine virtuelle Linux](../virtual-machines/linux/attach-disk-portal.md).  Si la quantité de données blob est supérieure au plus grand disque managé disponible, les données doivent être copiées en plusieurs étapes ou en utilisant plusieurs disques managés.

3. Connectez-vous à la machine virtuelle Linux et montez le disque managé.

4. Installez [AzCopy sur votre machine virtuelle Linux](../storage/common/storage-use-azcopy-v10.md).

5. Téléchargez les données depuis votre stockage d’objets blob Azure sur le disque managé avec AzCopy.  Syntaxe de la commande : `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`.  Remplacez `<storage-account-name>` par le nom de votre compte de stockage Azure et `<container-name>` par le conteneur qui contient les données copiées avec Azure Data Box.

6. Installez le serveur NFS sur votre machine virtuelle Linux.

    1. Sur une distribution Ubuntu/Debian : `sudo apt install nfs-kernel-server`.
    2. Sur une distribution Enterprise Linux : `sudo yum install nfs-utils`.

7. Changez l’autorisation du dossier sur votre disque managé où les données du magasin d’objets blob Azure ont été copiées.  Changez les autorisations pour tous les dossiers que vous voulez exporter en tant que partage NFS.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Affectez des autorisations pour les adresses IP clientes leur permettant d’accéder au partage NFS en modifiant le fichier `/etc/exports`.

    ```bash
    sudo vi /etc/exports
    ```
    
    Entrez les lignes suivantes dans le fichier pour chaque adresse IP de l’hôte ESXi de votre cloud privé.  Si vous créez des partages pour plusieurs dossiers, ajoutez tous les dossiers.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Exportez les partages NFS en utilisant la commande `sudo exportfs -a`.

10. Redémarrez le serveur du noyau NFS en utilisant la commande `sudo systemctl restart nfs-kernel-server`.


### <a name="mount-linux-virtual-machine-nfs-share-as-a-datastore-on-private-cloud-vcenter-cluster-and-copy-data"></a>Monter un partage NFS de machine virtuelle Linux en tant que magasin de données sur un cloud privé vCenter et copier des données

Le partage NFS de votre machine virtuelle Linux doit être monté en tant que magasin de données sur le cluster vCenter de votre cloud privé pour la copie des données.  Une fois qu’il est monté, les données peuvent être copiées depuis le magasin de données NFS vers le magasin de données vSAN du cloud privé.

1. Connectez-vous au serveur vCenter de votre cloud privé.

2. Cliquez avec le bouton droit sur le **Centre de données**, sélectionnez **Stockage**, cliquez sur **Nouveau magasin de données**, puis cliquez sur **Suivant**.

   ![Ajouter un nouveau magasin de données](media/databox-migration-add-datastore.png)

3. À l’étape 1 de l’Assistant Ajout d’un magasin de données, sélectionnez le type **NFS**.

   ![Ajouter un nouveau magasin de données - type](media/databox-migration-add-datastore-type.png)

4. À l’étape 2, sélectionnez **NFS 3** comme version de NFS, puis cliquez sur **Suivant**.

   ![Ajouter un nouveau magasin de données - Version de NFS](media/databox-migration-add-datastore-nfs-version.png)

5. À l’étape 3, spécifiez le nom du magasin de données, le chemin et le serveur.  Vous pouvez utiliser l’adresse IP de votre machine virtuelle Linux pour le serveur.  Le chemin du dossier doit être au format `/<folder>/<subfolder>/`.

   ![Ajouter un nouveau magasin de données - Configuration de NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. À l’étape 4, sélectionnez les hôtes ESXi où vous voulez monter le magasin de données, puis cliquez sur **Suivant**.  Dans un cluster, sélectionnez tous les hôtes pour garantir la migration des machines virtuelles.

   ![Ajouter un nouveau magasin de données - Sélectionner les hôtes](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. À l’étape 5, passez en revue le récapitulatif, puis cliquez sur **Terminer**.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-nfs-datastore-to-the-inventory"></a>Ajouter des machines virtuelles et des modèles de machine virtuelle du magasin de données NFS à l’inventaire

1. À partir de l’interface utilisateur web du vCenter de votre cloud privé, accédez à **Stockage**.  Sélectionnez Magasin de données NFS de machine virtuelle Linux, puis cliquez sur **Fichiers**.

    ![Sélectionner des fichiers dans un magasin de données NFS](media/databox-migration-datastore-select-files.png)

2. Sélectionnez un dossier qui contient une machine virtuelle ou un modèle de machine virtuelle.  Dans le volet d’informations, sélectionnez le fichier `.vmx` pour une machine virtuelle ou le fichier `.vmtx` pour un modèle de machine virtuelle.

3. Cliquez sur **Inscrire la machine virtuelle** pour inscrire la machine virtuelle sur le vCenter de votre cloud privé.

    ![Inscrire une machine virtuelle](media/databox-migration-datastore-register-vm.png)

4. Sélectionnez le centre de ressources, le dossier et le cluster/pool de ressources où vous voulez inscrire la machine virtuelle.

4. Répétez les étapes 3 et 4 pour toutes les machines virtuelles et tous les modèles de machine virtuelle.

5. Accédez au dossier qui contient les fichiers ISO.  Sélectionnez les fichiers ISO et **Copiez vers** un dossier sur votre magasin de données vSAN.

Les machines virtuelles et les modèles de machine virtuelle sont désormais disponibles sur le vCenter de votre cloud privé.  Ces machines virtuelles doivent être déplacées du magasin de données NFS vers le magasin de données vSAN avant leur mise sous tension.  Vous pouvez effectuer une opération de stockage vMotion des machines virtuelles et sélectionner le magasin de données vSAN comme cible pour la machine virtuelle.

Les modèles de machine virtuelle doivent être clonés à partir du magasin de données NFS de votre machine virtuelle Linux vers votre magasin de données vSAN.

### <a name="clean-up-of-your-linux-virtual-machine"></a>Nettoyage de votre machine virtuelle Linux

Une fois que toutes les données sont copiées dans votre cloud privé, vous pouvez supprimer le magasin de données NFS de votre cloud privé.

1. Vérifiez que toutes les machines virtuelles et tous les modèles sont déplacés et clonés dans votre magasin de données vSAN.

2. Supprimez tous les modèles de machine virtuelle du magasin de données NFS de l’inventaire.

3. Démontez le magasin de données de la machine virtuelle Linux du vCenter de votre cloud privé.

4. Supprimez la machine virtuelle et le disque managé d’Azure.

5. Si vous ne souhaitez pas conserver les données transférées avec Azure Data Box sur votre compte de stockage, supprimez le compte de stockage Azure.  
    


## <a name="next-steps"></a>Étapes suivantes

* Découvrir plus d’informations sur [Azure Data Box](../databox/data-box-overview.md)
* Découvrir plus d’informations sur les différentes options de [migration des charges de travail vers un cloud privé](migrate-workloads.md)
