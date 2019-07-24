---
title: Utiliser Azure Files avec Linux | Microsoft Docs
description: Découvrez comment monter un partage de fichiers Azure via SMB sur Linux.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e9363f88db4fa44879eb8f6a6a04e23563c5ba44
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67125735"
---
# <a name="use-azure-files-with-linux"></a>Utiliser Azure Files avec Linux

[Azure Files](storage-files-introduction.md) est le système de fichiers cloud facile à utiliser de Microsoft. Les partages de fichiers Azure peuvent être montés dans des distributions Linux à l’aide du [client SMB en mode noyau](https://wiki.samba.org/index.php/LinuxCIFS). Cet article présente deux méthodes de montage d’un partage de fichiers Azure : à la demande avec la commande `mount` et au démarrage en créant une entrée dans `/etc/fstab`.

> [!NOTE]  
> Pour monter un partage de fichiers Azure en dehors de la région Azure dans laquelle il est hébergé, par exemple en local ou dans une région Azure différente, le système d’exploitation doit prendre en charge la fonctionnalité de chiffrement de SMB 3.0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Conditions préalables au montage d’un partage de fichiers Azure avec Linux et le package cifs-utils
<a id="smb-client-reqs"></a>

* **Un partage de fichiers et un compte de stockage Azure existants** : Pour effectuer ce qui est décrit dans cet article, vous devez disposer d’un compte de stockage et d’un partage de fichiers. Si vous n’en avez pas déjà créé un, consultez l’un de nos démarrages rapides à ce propos : [Créer des partages de fichiers - CLI](storage-how-to-use-files-cli.md).

* **Le nom et la clé de votre compte de stockage** : vous devez disposer du nom et de la clé du compte de stockage afin d’effectuer ce qui est décrit dans cet article. Si vous en avez créé un à l’aide du démarrage rapide CLI, vous devriez déjà les avoir. Sinon, vous pouvez consulter le démarrage rapide CLI via le lien fournit précédemment afin d’en savoir plus sur la récupération de votre clé de compte de stockage.

* **Choisissez une distribution Linux adaptée à vos besoins de montage.**  
      Azure Files peut être monté via SMB 2.1 et SMB 3.0. Pour les connexions provenant de clients en local ou dans d’autres régions Azure, vous devez utiliser SMB 3.0 ; Azure Files refuse SMB 2.1 (ou SMB 3.0 sans chiffrement). Si vous accédez au partage de fichiers Azure à partir d’une machine virtuelle au sein de la même région Azure, vous pouvez accéder à votre partage de fichiers à l’aide de SMB 2.1 si, et seulement si, le *transfert sécurisé requis* est désactivé pour le compte de stockage qui héberge le partage de fichiers Azure. Nous conseillons systématiquement de demander un transfert sécurisé et d'utiliser SMB 3.0 avec chiffrement uniquement.

    La prise en charge du chiffrement SMB 3.0 a été introduite dans Linux Kernel version 4.11 et a été rétroportée sur des versions antérieures du noyau pour les distributions Linux populaires. Au moment de publier ce document, les distributions suivantes de la galerie Azure prennent en charge l’option de montage spécifiée dans les en-têtes du tableau. 

### <a name="minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30"></a>Versions minimales recommandées avec les capacités de montage correspondantes (comparaison entre SMB 2.1 et SMB 3.0)

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

* **Déterminer les autorisations de fichier/répertoire du partage monté** : dans les exemples ci-dessous, l’autorisation `0777` permet d’accorder des autorisations de lecture, d’écriture et d’exécution à tous les utilisateurs. Vous pouvez la remplacer par d’autres [autorisations chmod](https://en.wikipedia.org/wiki/Chmod) de votre choix, mais cela risque d’entraîner une limitation éventuelle de l’accès. Si vous utilisez d’autres autorisations, vous devez envisager d’utiliser également uid et gid afin de conserver l’accès pour les utilisateurs et groupes locaux de votre choix.

> [!NOTE]
> Si vous n’accordez pas explicitement les autorisations pour les répertoires et les fichiers avec dir_mode et file_mode, leur valeur par défaut sera 0755.

* **Vérifiez que le port 445 est ouvert** : SMB communique via le port TCP 445. Assurez-vous que votre pare-feu ne bloque pas les ports TCP 445 à partir de la machine cliente.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montage du partage de fichiers Azure à la demande avec `mount`

1. **[Installez le package cifs-utils pour votre distribution Linux](#install-cifs-utils)** .

1. **Créez un dossier pour le point de montage** : Vous pouvez créer un dossier pour un point de montage n’importe où sur le système de fichiers, mais il est d’usage courant de le créer sous un nouveau dossier. Par exemple, la commande suivante crée un nouveau répertoire. Remplacez **<storage_account_name>** (nom du compte de stockage) et **<file_share_name>** (nom du partage de fichiers) par les informations adéquates pour votre environnement :

    ```bash
    mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **utilisez la commande de montagne pour monter le partage de fichiers Azure** : Veillez à remplacer **<storage_account_name>** (nom du compte de stockage), **<share_name>** (nom du partage), **<smb_version>** (version de SMB), **<storage_account_key>** (clé du compte de stockage) et **<mount_point>** (point de montage) par les informations adéquates pour votre environnement. Si votre distribution Linux prend en charge SMB 3.0 avec chiffrement (pour plus d’informations, consultez [Comprendre les exigences du client SMB](#smb-client-reqs)), remplacez **<smb_version>** par **3.0**. Pour les distributions Linux qui ne prennent pas en charge SMB 3.0 avec chiffrement, remplacez **<smb_version>** par **2.1**. Un partage de fichiers Azure peut uniquement être monté en dehors d’une région Azure (notamment en local ou dans une région Azure différente) avec SMB 3.0. Si vous le souhaitez, vous pouvez modifier les autorisations des répertoires et des fichiers de votre partage monté, mais cela se traduirait par une restriction de l’accès.

    ```bash
    sudo mount -t cifs //<storage_account_name>.file.core.windows.net/<share_name> <mount_point> -o vers=<smb_version>,username=<storage_account_name>,password=<storage_account_key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Quand vous avez terminé d’utiliser le partage de fichiers Azure, vous pouvez utiliser `sudo umount <mount_point>` pour démonter le partage.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Création d’un point de montage persistant pour le partage de fichiers Azure avec `/etc/fstab`

1. **[Installez le package cifs-utils pour votre distribution Linux](#install-cifs-utils)** .

1. **Créez un dossier pour le point de montage** : Vous pouvez créer un dossier pour un point de montage n’importe où sur le système de fichiers, mais il est d’usage courant de le créer sous un nouveau dossier. Lors de chaque création, notez le chemin absolu du dossier. Par exemple, la commande suivante crée un nouveau répertoire. Remplacez **<storage_account_name>** (nom du compte de stockage) et **<file_share_name>** (nom du partage de fichiers) par les informations adéquates pour votre environnement.

    ```bash
    sudo mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Créez un fichier d’informations d’identification pour stocker le nom d’utilisateur (nom du compte de stockage) et le mot de passe (clé du compte de stockage) pour le partage de fichiers.** Remplacez **<storage_account_name>** (nom du compte de stockage) et **<storage_account_key>** (clé du compte de stockage) par les informations adéquates pour votre environnement.

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
    fi
    if [ ! -f "/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred" ]; then
    sudo bash -c 'echo "username=<STORAGE ACCOUNT NAME>" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    sudo bash -c 'echo "password=7wRbLU5ea4mgc<DRIVE LETTER>PIpUCNcuG9gk2W4S2tv7p0cTm62wXTK<DRIVE LETTER>CgJlBJPKYc4VMnwhyQd<DRIVE LETTER>UT<DRIVE LETTER>yR5/RtEHyT/EHtg2Q==" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    fi
    ```

1. **Modifiez les autorisations du fichier d’informations d’identification afin que seule la racine puisse lire ou modifier le fichier du mot de passe.** Étant donné que la clé du compte de stockage est avant tout un mot de passe de super administrateur pour le compte de stockage, il est important de définir des autorisations sur le fichier de sorte que seule la racine puisse y accéder. Ainsi, les utilisateurs de privilège inférieurs ne peuvent pas récupérer la clé du compte de stockage.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage_account_name>.cred
    ```

1. **Utilisez la commande suivante pour ajouter la ligne suivante à `/etc/fstab`**  : Veillez à remplacer **<storage_account_name>** (nom du compte de stockage), **<share_name>** (nom du partage), **<smb_version>** (version de SMB) et **<mount_point>** (point de montage) par les informations adéquates pour votre environnement. Si votre distribution Linux prend en charge SMB 3.0 avec chiffrement (pour plus d’informations, consultez [Comprendre les exigences du client SMB](#smb-client-reqs)), remplacez **<smb_version>** par **3.0**. Pour les distributions Linux qui ne prennent pas en charge SMB 3.0 avec chiffrement, remplacez **<smb_version>** par **2.1**. Un partage de fichiers Azure peut uniquement être monté en dehors d’une région Azure (notamment en local ou dans une région Azure différente) avec SMB 3.0.

    ```bash
    sudo bash -c 'echo "//<STORAGE ACCOUNT NAME>.file.core.windows.net/<FILE SHARE NAME> /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME> cifs nofail,vers=3.0,credentials=/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'

    sudo mount /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME>
    ```

> [!Note]  
> Vous pouvez utiliser `sudo mount -a` pour monter le partage de fichiers Azure après la modification de `/etc/fstab` plutôt que de redémarrer.

## <a name="feedback"></a>Commentaires

Utilisateurs de Linux, nous attendons votre avis !

Azure Files pour le groupe d’utilisateurs Linux propose un forum qui vous permet de partager vos commentaires quand vous évaluez et adoptez le stockage de fichiers sur Linux. Envoyez un e-mail aux [utilisateurs Linux d’Azure Files](mailto:azurefileslinuxusers@microsoft.com) pour rejoindre le groupe d’utilisateurs.

## <a name="next-steps"></a>Étapes suivantes

Consultez ces liens pour en savoir plus sur Azure Files :

* [Planification d’un déploiement Azure Files](storage-files-planning.md)
* [FORUM AUX QUESTIONS](../storage-files-faq.md)
* [Dépannage](storage-troubleshoot-linux-file-connection-problems.md)
