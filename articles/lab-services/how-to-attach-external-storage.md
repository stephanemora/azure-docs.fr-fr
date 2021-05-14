---
title: Utilisation du stockage de fichiers externes dans Lab Services | Microsoft Docs
description: Découvrez comment configurer un labo qui utilise le stockage de fichiers externes dans Lab Services.
author: emaher
ms.topic: article
ms.date: 03/30/2021
ms.author: enewman
ms.openlocfilehash: 70be69cad59cd00ef9feaa78ad2294c64626d07a
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108125676"
---
# <a name="using-external-file-storage-in-lab-services"></a>Utilisation du stockage de fichiers externes dans Lab Services

Cet article aborde certaines des options de stockage de fichiers externes lors de l’utilisation d’Azure Lab Services.  [Azure Files](https://azure.microsoft.com/services/storage/files/) fournit des partages de fichiers entièrement managés dans le cloud, [accessibles via SMB 2.1 et SMB 3.0.](../storage/files/storage-how-to-use-files-windows.md)  Un partage Azure Files peut être connecté publiquement ou en privé au sein d’un réseau virtuel.  En outre, il peut être configuré pour utiliser les informations d’identification AD d’étudiant pour la connexion au partage de fichiers.  L’utilisation d’Azure NetApp Files avec des volumes NFS pour les machines Linux est une autre option pour le stockage de fichiers externes avec Azure Lab Services.  

## <a name="deciding-which-solution-to-use"></a>Choix de la solution à utiliser

Chaque solution a des conditions requises et des capacités différentes.  Le tableau ci-dessous répertorie les points importants à prendre en compte pour chaque solution.  

|     Solution    |    À savoir    |
| -------------- | ------------------------ |
| [Partage Azure Files avec un point de terminaison public](#azure-files-share) | <ul><li>Tout le monde dispose d’un accès en lecture/écriture.</li><li>Aucune homologation de réseau virtuel n’est requise.</li><li>Accessible à toutes les machines virtuelles, pas seulement aux machines virtuelles de labo.</li><li>Si vous utilisez Linux, les étudiants ont accès à la clé du compte de stockage.</li></ul> |
| [Partage Azure Files avec un point de terminaison privé](#azure-files-share) | <ul><li>Tout le monde dispose d’un accès en lecture/écriture.</li><li>Homologation de réseau virtuel requise.</li><li>Accessible uniquement aux machines virtuelles sur le même réseau (ou réseau homologue) que le compte de stockage.</li><li>Si vous utilisez Linux, les étudiants ont accès à la clé du compte de stockage.</li></ul> |
| [Azure Files avec l’autorisation basée sur l’identité](#azure-files-with-identity-base-authorization) | <ul><li>Les autorisations d’accès en lecture ou en lecture/écriture peuvent être définies pour un dossier ou un fichier.</li><li>Homologation de réseau virtuel requise.</li><li>Le compte de stockage doit être connecté à Active Directory.</li><li>Les machines virtuelles de labo doivent être jointes à un domaine.</li><li>Clé de compte de stockage non utilisée par les étudiants pour se connecter au partage de fichiers.</li></ul> |
| [NetApp Files avec volumes NFS](#netapp-files-with-nfs-volumes) | <ul><li>L’accès en lecture ou en lecture/écriture peut être défini pour les volumes.</li><li>Les autorisations sont définies à l’aide de l’adresse IP de la machine virtuelle de l’étudiant.</li><li>Homologation de réseau virtuel requise.</li><li>Vous devrez peut-être vous inscrire pour utiliser le service de NetApp Files.</li><li>Linux uniquement.</li></ul>

Le coût d’utilisation du stockage externe n’est pas inclus dans le coût d’utilisation d’Azure Lab Services.  Pour plus d’informations sur la tarification, consultez [Tarifs Azure Files](https://azure.microsoft.com/pricing/details/storage/files/) et [Tarifs Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/).

## <a name="azure-files-share"></a>Partage Azure Files

Les partages Azure Files sont accessibles à l’aide d’un point de terminaison public ou privé.  Les partages Azure Files sont montés en utilisant la clé de compte de stockage comme mot de passe.  Avec cette approche, tout le monde dispose d’un accès en lecture-écriture au partage de fichiers.

Si vous utilisez un point de terminaison public pour le partage Azure Files, il est important de se souvenir de ce qui suit :

- Le réseau virtuel pour le compte de stockage n’a pas besoin d’être homologué au compte de labo.  Le partage de fichiers peut être créé à tout moment avant la publication du modèle de machine virtuelle.
- Le partage de fichiers est accessible à partir de n’importe quelle machine si un utilisateur possède la clé de compte de stockage.  
- Les étudiants Linux peuvent voir la clé de compte de stockage.  Les informations d’identification pour le montage d’un partage Azure Files sont stockées dans `{file-share-name}.cred` sur des machines virtuelles Linux et lisibles par sudo.  Dans la mesure où les étudiants reçoivent un accès sudo par défaut dans les machines virtuelles Azure Lab Service, ils peuvent lire la clé du compte de stockage.  Si le point de terminaison du compte de stockage est public, les étudiants peuvent accéder au partage de fichiers en dehors de leur machine virtuelle d’étudiant.  Envisagez de faire pivoter la clé de compte de stockage après la fin de la classe et d’utiliser des partages de fichiers privés.

Si vous utilisez un point de terminaison privé pour le partage Azure Files, il est important de se souvenir de ce qui suit :

- L’accès est limité au trafic provenant du réseau privé et n’est pas accessible via l’Internet public.  Seules les machines virtuelles du réseau virtuel privé, les machines virtuelles d’un réseau homologué au réseau virtuel privé ou les machines connectées à un VPN pour le réseau privé peuvent accéder au partage de fichiers.  
- Les étudiants Linux peuvent voir la clé de compte de stockage.  Les informations d’identification pour le montage d’un partage Azure Files sont stockées dans `{file-share-name}.cred` sur des machines virtuelles Linux et lisibles par sudo.  Dans la mesure où les étudiants reçoivent un accès sudo par défaut dans les machines virtuelles Azure Lab Service, ils peuvent lire la clé du compte de stockage.  Envisagez de faire pivoter la clé de compte de stockage une fois la classe terminée.
- Cette approche nécessite que le réseau virtuel de partage de fichiers soit homologué au compte de labo.  Le réseau virtuel pour le compte de stockage Azure doit être homologué au réseau virtuel pour le compte de labo **avant** la création du labo.

> [!NOTE]
> Les partages de fichiers supérieurs à 5 To sont disponibles uniquement pour les [[comptes de stockage localement redondant (LRS)]](../storage/files/storage-files-how-to-create-large-file-share.md#restrictions).

Procédez comme suit pour créer une machine virtuelle connectée à un partage Azure Files.

1. Créez un [Compte de stockage Azure](../storage/files/storage-how-to-create-file-share.md). Sur la page « méthode de connectivité », choisissez un point de terminaison public ou privé.
2. Le cas échéant, créez un [point de terminaison privé](../private-link/tutorial-private-endpoint-storage-portal.md) afin que les partages de fichiers soient accessibles à partir du réseau virtuel.  Créez une [zone DNS privée](../dns/private-dns-privatednszone.md) ou utilisez-en une existante. Les zones Azure DNS privées fournissent une résolution de noms au sein d’un réseau virtuel.
3. Créez un [partage de fichiers Azure](../storage/files/storage-how-to-create-file-share.md). Le partage de fichiers est accessible par le nom d’hôte public du compte de stockage.
4. Montez le partage de fichiers Azure dans le modèle de machine virtuelle :
    - [[Windows]](../storage/files/storage-how-to-use-files-windows.md)
    - [[Linux]](../storage/files/storage-how-to-use-files-linux.md).  Pour éviter les problèmes de montage sur les machines virtuelles des étudiants, consultez [Utilisation d’Azure files avec Linux](#using-azure-files-with-linux).
5. [Publiez](how-to-create-manage-template.md#publish-the-template-vm) le modèle de machine virtuelle.

> [!IMPORTANT]
> Assurez-vous que le pare-feu ne bloque pas la connexion SMB sortante via le port 445. Par défaut, SMB est autorisé pour les machines virtuelles Azure.

### <a name="using-azure-files-with-linux"></a>Utilisation d’Azure Files avec Linux

Si vous utilisez les _instructions par défaut_ pour monter un partage de fichiers Azure, le partage de fichiers semble disparaître sur les machines virtuelles des étudiants une fois le modèle publié.  Vous trouverez ci-dessous un script modifié qui résout ce problème.  

```bash
#!/bin/bash

# Assign variables values for your storage account and file share
storage_account_name=""
storage_account_key=""
fileshare_name=""

# Do not use 'mnt' for mount directory.
# Using ‘mnt’ will cause issues on student VMs.
mount_directory="prm-mnt" 

sudo mkdir /$mount_directory/$fileshare_name
if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
fi
if [ ! -f "/etc/smbcredentials/$storage_account_name.cred" ]; then
    sudo bash -c "echo ""username=$storage_account_name"" >> /etc/smbcredentials/$storage_account_name.cred"
    sudo bash -c "echo ""password=$storage_account_key"" >> /etc/smbcredentials/$storage_account_name.cred"
fi
sudo chmod 600 /etc/smbcredentials/$storage_account_name.cred

sudo bash -c "echo ""//$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name cifs nofail,vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino"" >> /etc/fstab"
sudo mount -t cifs //$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name -o vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino
```

Si le modèle de machine virtuelle déjà publié monte le partage Azure Files dans le répertoire `/mnt`, l’étudiant peut effectuer l’une des opérations suivantes.

- Déplacez l’instruction à monter `/mnt` en haut du fichier `/etc/fstab`.  
- Modifiez l’instruction à monter `/mnt/{file-share-name}` dans un répertoire différent comme `/prm-mnt/{file-share-name}`.

Les étudiants doivent exécuter `mount -a` pour remonter les répertoires.

Pour plus d’informations générales sur l’utilisation de partages de fichiers avec Linux, consultez [utiliser Azure Files avec Linux](../storage/files/storage-how-to-use-files-linux.md).

## <a name="azure-files-with-identity-base-authorization"></a>Azure Files avec l’autorisation basée sur l’identité

Les partages Azure Files sont également accessibles à l’aide de l’authentification AD si

1. La machine virtuelle de l’étudiant est jointe au domaine.
2. L’authentification AD est [activée sur le compte de stockage Azure](../storage/files/storage-files-active-directory-overview.md) qui héberge le partage de fichiers.  

Le lecteur réseau est monté sur la machine virtuelle à l’aide de l’identité de l’utilisateur, et non de la clé du compte de stockage.  L’accès au compte de stockage peut utiliser des points de terminaison publics ou privés.

Passons en revue quelques points importants de cette approche.

- Les autorisations peuvent être définies au niveau d’un répertoire ou d’un fichier.
- Les informations d’identification de l’utilisateur actuel sont utilisées pour l’authentification auprès du partage de fichiers.

Si vous utilisez un point de terminaison public pour le partage Azure Files, il est important de se souvenir de ce qui suit :

- Le réseau virtuel pour le compte de stockage n’a pas besoin d’être homologué au compte de labo.  Le partage de fichiers peut être créé à tout moment avant la publication du modèle de machine virtuelle.

Si vous utilisez un point de terminaison privé pour le partage Azure Files, il est important de se souvenir de ce qui suit :

- L’accès est limité au trafic provenant du réseau privé et n’est pas accessible via l’Internet public.  Seules les machines virtuelles du réseau virtuel privé, les machines virtuelles d’un réseau homologué au réseau virtuel privé ou les machines connectées à un VPN pour le réseau privé peuvent accéder au partage de fichiers.  
- Cette approche nécessite que le réseau virtuel de partage de fichiers soit homologué au compte de labo.  Le réseau virtuel pour le compte de stockage Azure doit être homologué au réseau virtuel pour le compte de labo **avant** la création du labo.

Suivez les étapes ci-dessous pour créer un partage Azure Files et une jonction de domaine aux machines virtuelles du labo activés par authentification AD.

1. Créez un [Compte de stockage Azure](../storage/files/storage-how-to-create-file-share.md).
2. Le cas échéant, créez un [point de terminaison privé](../private-link/tutorial-private-endpoint-storage-portal.md) afin que les partages de fichiers soient accessibles à partir du réseau virtuel.  Créez une [zone DNS privée](../dns/private-dns-privatednszone.md) ou utilisez-en une existante. Les zones Azure DNS privées fournissent une résolution de noms au sein d’un réseau virtuel.
3. Créez un [partage de fichiers Azure](../storage/files/storage-how-to-create-file-share.md).
4. Suivez les étapes pour activer l’autorisation basée sur l’identité.  Si vous utilisez AD local synchronisé avec Azure AD, suivez les étapes pour [l’authentification Active Directory Domain Services locale sur SMB pour les partages de fichiers Azure](../storage/files/storage-files-identity-auth-active-directory-enable.md).  Si vous utilisez Azure AD uniquement, suivez les étapes pour [activer l’authentification Azure Active Directory Domain Services sur Azure Files](../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md).
    >[!IMPORTANT]
    >Contactez l’équipe qui gère votre AD pour vérifier que toutes les conditions préalables répertoriées dans les instructions sont remplies.
5. Affectez des rôles d’autorisation de partage SMB dans Azure.  Pour plus d’informations sur les autorisations accordées à chaque rôle, consultez les [autorisations au niveau du partage](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).
    1. Le rôle « Contributeur avec élévation de privilèges du partage SMB de données de fichiers de stockage » doit être attribué à la personne ou au groupe qui configure des autorisations pour le contenu du partage de fichiers.
    2. Le rôle « Contributeur de partage SMB de données de fichiers de stockage » doit être attribué aux étudiants qui doivent ajouter ou modifier des fichiers sur le partage de fichiers.
    3. Le rôle « Lecteur de partage SMB des données de fichiers de stockage » doit être attribué aux étudiants qui doivent uniquement lire les fichiers à partir du partage de fichiers.
6. Configurer des autorisations au niveau du répertoire/fichier pour le partage de fichiers.  Les autorisations doivent être configurées à partir d’un ordinateur joint à un domaine qui dispose d’un accès réseau au partage de fichiers.  **Pour modifier les autorisations au niveau du répertoire/fichier, montez le partage de fichiers à l’aide avec la clé de stockage, et non vos informations d’identification AAD.**  La commande [Set-Acl](/powershell/module/microsoft.powershell.security/set-acl) PowerShell ou [icacls](/windows-server/administration/windows-commands/icacls) sont les outils recommandés pour l’utilisation lors de l’attribution d’autorisations.
7. [Homologuez le réseau virtuel](how-to-connect-peer-virtual-network.md) pour le compte de stockage sur le compte de labo.
8. [Créez le laboratoire de classe](how-to-manage-classroom-labs.md).
9. Enregistrez un script sur le modèle de machine virtuelle que les étudiants peuvent exécuter pour se connecter au lecteur réseau.  Pour obtenir un exemple de script :
    1. Ouvrez le compte de stockage dans le portail Azure.
    1. Sélectionnez **Partages de fichiers** sous **Service de fichiers** dans le menu.
    1. Recherchez le partage auquel vous souhaitez vous connecter, sélectionnez le bouton des ellipses à l’extrême droite, puis choisissez **Se connecter**.
    1. Le passage **Se connecter** s’ouvre avec des instructions pour Windows, Linux et macOS.  Si vous utilisez Windows, définissez la **Méthode d’authentification** sur **Active Directory**.
    1. Copiez le code dans l’exemple et enregistrez-le sur la machine modèle dans un fichier `.ps1` pour Windows ou un fichier `.sh` pour Linux.
10. Sur la machine modèle, téléchargez et exécutez le script pour [joindre les machines des étudiants au domaine](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Scripts/ActiveDirectoryJoin/README.md#usage).  Le script `Join-AzLabADTemplate` [publie automatiquement le modèle de machine virtuelle](how-to-create-manage-template.md#publish-the-template-vm).  
    > [!NOTE]
    > Le modèle de machine n’est pas joint au domaine. Les formateurs doivent s’attribuer une machine virtuelle d’étudiant publiée pour afficher les fichiers sur le partage.
11. Les étudiants qui utilisent Windows peuvent se connecter au partage Azure Files à l’aide de l'[Explorateur de fichiers](../storage/files/storage-how-to-use-files-windows.md) avec leurs informations d’identification, une fois le chemin d’accès au partage de fichiers spécifié.  Les étudiants peuvent également exécuter le script créé ci-dessus pour se connecter au lecteur réseau.  Pour les étudiants qui utilisent Linux, exécutez le script créé ci-dessus.

## <a name="netapp-files-with-nfs-volumes"></a>NetApp Files avec volumes NFS

[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) est un service de stockage de fichiers limité, hautes performances et de classe entreprise.

- Les stratégies d’accès peuvent être définies en fonction du volume.
- Les stratégies d’autorisation sont basées sur IP pour chaque volume.
- Si les étudiants ont besoin de leur propre volume auquel les autres étudiants n’ont pas accès, les stratégies d’autorisation doivent être attribuées après la publication du labo.
- Dans le contexte d’Azure Lab Services, seules les machines Linux sont prises en charge.
- Le réseau virtuel pour le pool de capacité Azure NetApp Files doit être homologué au réseau virtuel pour le compte de labo **avant** la création du labo.

Suivez les étapes ci-dessous pour utiliser un partage Azure NetApp Files dans Azure Lab Services.

1. Intégrez à [Azure NetApp Files](https://aka.ms/azurenetappfiles), si nécessaire.
2. Pour créer un pool de capacité NetApp Files et un ou plusieurs volumes NFS, consultez [configurer Azure NetApp Files et le volume NFS](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md).  Pour plus d’informations sur les niveaux de service, consultez [niveaux de service pour Azure NetApp Files](../azure-netapp-files/azure-netapp-files-service-levels.md).
3. [Homologuez le réseau virtuel](how-to-connect-peer-virtual-network.md) pour le pool de capacité NetApp Files sur le compte de labo.
4. [Créez le laboratoire de classe](how-to-manage-classroom-labs.md).
5. Sur le modèle de machine virtuelle, installez les composants nécessaires pour utiliser des partages de fichiers NFS.
    - Ubuntu :

        ```bash
        sudo apt update
        sudo apt install nfs-common
        ```

    - CentOS

        ```bash
        sudo yum install nfs-utils
        ```

6. Sur le modèle de machine virtuelle, enregistrez le script ci-dessous sous `mount_fileshare.sh` pour [monter le partage NetApp Files](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).  Affectez la variable `capacity_pool_ipaddress` à l’adresse IP cible de montage pour le pool de capacité.  Obtenez les instructions de montage pour le volume afin de trouver la valeur appropriée.  Le script attend le chemin d’accès/nom du volume NetApp Files.  N’oubliez pas d’exécuter `chmod u+x mount_fileshare.sh` pour vous assurer que le script peut être exécuté par les utilisateurs.

    ```bash
    #!/bin/bash
    
    if [ $# -eq 0 ];  then
        echo "Must provide volume name."
        exit 1
    fi
    
    volume_name=$1
    capacity_pool_ipaddress=0.0.0.0 # IP address of capacity pool
    
    # Do not use 'mnt' for mount directory.
    # Using ‘mnt’ may cause issues on student VMs.
    mount_directory="prm-mnt" 
    
    sudo mkdir -p /$mount_directory
    sudo mkdir /$mount_directory/$folder_name
    
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp $capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name
    sudo bash -c "echo ""$capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0"" >> /etc/fstab"
    ```

7. Si tous les étudiants partagent l’accès au même volume NetApp Files, le script `mount_fileshare.sh` peut être exécuté sur le modèle de machine avant la publication.  Si les étudiants obtiennent chacun leur propre volume, enregistrez le script pour qu’il soit exécuté ultérieurement par l’étudiant.
8. [Publiez](how-to-create-manage-template.md#publish-the-template-vm) le modèle de machine virtuelle.
9. [Configurez la stratégie](../azure-netapp-files/azure-netapp-files-configure-export-policy.md) pour le partage de fichiers.  La stratégie d’exportation peut permettre à une seule machine virtuelle ou à plusieurs machines virtuelles d’accéder à un volume.  L’accès en lecture seule ou en lecture/écriture peut être accordé.
10. Les étudiants doivent démarrer leur machine virtuelle et exécuter le script pour monter le partage de fichiers.  Ils n’ont à exécuter le script qu’une seule fois.  La commande ressemble à ceci : `./mount_fileshare.sh myvolumename`.

## <a name="next-steps"></a>Étapes suivantes

Les étapes suivantes sont communes à la configuration de n’importe quel labo.

- [Créer et gérer un modèle](how-to-create-manage-template.md)
- [Ajout d'utilisateurs](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Définir un quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Définir un calendrier](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Envoyer par mail des liens d’inscription aux étudiants](how-to-configure-student-usage.md#send-invitations-to-users)