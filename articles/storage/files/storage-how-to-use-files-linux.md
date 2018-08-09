---
title: Utiliser Azure Files avec Linux | Microsoft Docs
description: Découvrez comment monter un partage de fichiers Azure via SMB sur Linux.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: renash
ms.component: files
ms.openlocfilehash: 89343f3e4ec91dd32b24cdea6632cecd855cc6f8
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523221"
---
# <a name="use-azure-files-with-linux"></a>Utiliser Azure Files avec Linux
[Azure Files](storage-files-introduction.md) est le système de fichiers cloud facile à utiliser de Microsoft. Les partages de fichiers Azure peuvent être montés dans des distributions Linux à l’aide du [client SMB en mode noyau](https://wiki.samba.org/index.php/LinuxCIFS). Cet article présente deux méthodes de montage d’un partage de fichiers Azure : à la demande avec la commande `mount` et au démarrage en créant une entrée dans `/etc/fstab`.

> [!NOTE]  
> Pour monter un partage de fichiers Azure en dehors de la région Azure dans laquelle il est hébergé, par exemple en local ou dans une région Azure différente, le système d’exploitation doit prendre en charge la fonctionnalité de chiffrement de SMB 3.0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Conditions préalables au montage d’un partage de fichiers Azure avec Linux et le package cifs-utils
<a id="smb-client-reqs"></a>
* **Choisissez une distribution Linux adaptée à vos besoins de montage.**  
      Azure Files peut être monté via SMB 2.1 et SMB 3.0. Pour les connexions provenant de clients en local ou dans d’autres régions Azure, Azure Files refuse SMB 2.1 (ou SMB 3.0 sans chiffrement). Si le *transfert sécurisé exigé* est activé pour un compte de stockage, Azure Files autorise uniquement les connexions utilisant SMB 3.0 avec chiffrement.
    
    La prise en charge du chiffrement SMB 3.0 a été introduite dans Linux Kernel version 4.11 et a été rétroportée sur des versions antérieures du noyau pour les distributions Linux populaires. Au moment de publier ce document, les distributions suivantes de la galerie Azure prennent en charge l’option de montage spécifiée dans les en-têtes du tableau. 

* **Versions minimales recommandées avec capacités de montage correspondantes (comparaison entre SMB version 2.1 et SMB version 3.0)**    
    
    |   | SMB 2.1 <br>(Montages sur des machines virtuelles au sein de la même région Azure) | SMB 3.0 <br>(Montages à partir du site local et entre les régions) |
    | --- | :---: | :---: |
    | Serveur Ubuntu | 14.04+ | 16.04+ |
    | RHEL | 7+ | 7.5+ |
    | CentOS | 7+ |  7.5+ |
    | Debian | 8+ |   |
    | openSUSE | 13.2+ | 42.3+ |
    | SUSE Linux Enterprise Server | 12 | 12 SP3+ |
    
    Si votre distribution Linux n’est pas répertoriée ici, vous pouvez vérifier la version de Linux Kernel avec la commande suivante :    

   ```bash
   uname -r
   ```    

* <a id="install-cifs-utils"></a>**Le package cifs-utils est installé.**  
    Le package cifs-utils peut être installé à l’aide du gestionnaire de package sur la distribution Linux de votre choix. 

    Sur les distributions **Ubuntu** et **basées sur Debian**, utilisez le gestionnaire de packages `apt-get` :

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    Sur **RHEL** et **CentOS**, utilisez le gestionnaire de packages `yum` :

    ```bash
    sudo yum install cifs-utils
    ```

    Sur **openSUSE**, utilisez le gestionnaire de packages `zypper` :

    ```bash
    sudo zypper install cifs-utils
    ```

    Sur les autres distributions, utilisez le gestionnaire de package approprié ou effectuez une [compilation à partir de la source](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).
    
* **Déterminer les autorisations de fichier/répertoire du partage monté** : dans les exemples ci-dessous, l’autorisation `0777` permet d’accorder des autorisations de lecture, d’écriture et d’exécution à tous les utilisateurs. Vous pouvez les remplacer par d’autres [autorisations chmod](https://en.wikipedia.org/wiki/Chmod) comme vous le souhaitez. 

* **Nom de compte de stockage** : pour monter un partage de fichiers Azure, vous avez besoin du nom du compte de stockage.

* **Clé du compte de stockage** : pour monter un partage de fichiers Azure, vous avez besoin de la clé de stockage primaire (ou secondaire). Actuellement, les clés SAS ne sont pas prises en charge pour le montage.

* **Vérifiez que le port 445 est ouvert** : SMB communique via le port TCP 445. Assurez-vous que votre pare-feu ne bloque pas les ports TCP 445 de la machine cliente.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montage du partage de fichiers Azure à la demande avec `mount`
1. **[Installez le package cifs-utils pour votre distribution Linux](#install-cifs-utils)**.

2. **Créez un dossier pour le point de montage** : un dossier pour un point de montage peut être créé n’importe où sur le système de fichiers, mais il est d’usage courant de le créer sous le dossier `/mnt`. Par exemple : 

    ```bash
    mkdir /mnt/MyAzureFileShare
    ```

3. **Utilisez la commande de montage pour monter le partage de fichiers Azure** : n’oubliez pas de remplacer `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>` et `<mount-point>` par les informations appropriées à votre environnement. Si votre distribution Linux prend en charge SMB 3.0 avec chiffrement (pour plus d’informations, consultez [Comprendre les besoins du client SMB](#smb-client-reqs)), utilisez `3.0` pour `<smb-version>`. Pour les distributions Linux qui ne prennent pas en charge SMB 3.0 avec chiffrement, utilisez `2.1` pour `<smb-version>`. Notez qu’un partage de fichiers Azure peut uniquement être monté en dehors d’une région Azure (notamment en local ou dans une région Azure différente) avec SMB 3.0. 

    ```bash
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Quand vous avez terminé d’utiliser le partage de fichiers Azure, vous pouvez utiliser `sudo umount <mount-point>` pour démonter le partage.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Création d’un point de montage persistant pour le partage de fichiers Azure avec `/etc/fstab`
1. **[Installez le package cifs-utils pour votre distribution Linux](#install-cifs-utils)**.

2. **Créez un dossier pour le point de montage** : un dossier pour un point de montage peut être créé n’importe où sur le système de fichiers, mais il est d’usage courant de le créer sous le dossier `/mnt`. Lors de chaque création, notez le chemin absolu du dossier. Par exemple, la commande suivante crée un dossier sous `/mnt` (le chemin est un chemin absolu).

    ```bash
    sudo mkdir /mnt/MyAzureFileShare
    ```

3. **Créez un fichier d’informations d’identification pour stocker le nom d’utilisateur (nom du compte de stockage) et le mot de passe (clé du compte de stockage) pour le partage de fichiers.** N’oubliez pas de remplacer `<storage-account-name>` et `<storage-account-key>` par les informations correspondant à votre environnement. 

    ```bash
    if [ -d "/etc/smbcredentials" ]; then
        sudo mkdir /etc/smbcredentials
    fi

    if [ ! -f "/etc/smbcredentials/<storage-account-name>.cred" ]; then
        sudo bash -c 'echo "username=<storage-account-name>" >> /etc/smbcredentials/<storage-account-name>.cred'
        sudo bash -c 'echo "password=<storage-account-key>" >> /etc/smbcredentials/<storage-account-name>.cred'
    fi
    ```

4. **Modifiez les autorisations du fichier d’informations d’identification afin que seule la racine puisse lire ou modifier le fichier du mot de passe.** Étant donné que la clé du compte de stockage est avant tout un mot de passe de super administrateur pour le compte de stockage, il est important de définir des autorisations sur le fichier de sorte que seule la racine puisse y accéder. Ainsi, les utilisateurs de privilège inférieurs ne peuvent pas récupérer la clé du compte de stockage.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage-account-name>.cred
    ```

5. **Utilisez la commande ci-après pour ajouter la ligne suivante à `/etc/fstab`** : n’oubliez pas de remplacer `<storage-account-name>`, `<share-name>`, `<smb-version>` et `<mount-point>` par les informations correspondant à votre environnement. Si votre distribution Linux prend en charge SMB 3.0 avec chiffrement (pour plus d’informations, consultez [Comprendre les besoins du client SMB](#smb-client-reqs)), utilisez `3.0` pour `<smb-version>`. Pour les distributions Linux qui ne prennent pas en charge SMB 3.0 avec chiffrement, utilisez `2.1` pour `<smb-version>`. Notez qu’un partage de fichiers Azure peut uniquement être monté en dehors d’une région Azure (notamment en local ou dans une région Azure différente) avec SMB 3.0. 

    ```bash
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> <mount-point> cifs nofail,vers=<smb-version>,credentials=/etc/smbcredentials/<storage-account-name>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Vous pouvez utiliser `sudo mount -a` pour monter le partage de fichiers Azure après la modification de `/etc/fstab` plutôt que de redémarrer.

## <a name="feedback"></a>Commentaires
Utilisateurs de Linux, nous attendons votre avis !

Azure Files pour le groupe d’utilisateurs Linux propose un forum qui vous permet de partager vos commentaires quand vous évaluez et adoptez le stockage de fichiers sur Linux. Envoyez un e-mail aux [utilisateurs Linux d’Azure Files](mailto:azurefileslinuxusers@microsoft.com) pour rejoindre le groupe d’utilisateurs.

## <a name="next-steps"></a>Étapes suivantes
Consultez ces liens pour en savoir plus sur Azure Files.
* [Présentation d’Azure Files](storage-files-introduction.md)
* [Planification d’un déploiement Azure Files](storage-files-planning.md)
* [FORUM AUX QUESTIONS](../storage-files-faq.md)
* [Résolution des problèmes](storage-troubleshoot-linux-file-connection-problems.md)
