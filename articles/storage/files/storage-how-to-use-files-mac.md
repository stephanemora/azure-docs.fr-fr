---
title: Montage du partage de fichiers Azure via SMB avec MacOS | Microsoft Docs
description: Découvrez comment monter un partage de fichiers Azure via SMB avec macOS à l’aide de Finder ou de Terminal. Azure Files est le système de fichiers cloud facile à utiliser de Microsoft.
author: RenaShahMSFT
ms.service: storage
ms.topic: how-to
ms.date: 09/19/2017
ms.author: renash
ms.subservice: files
ms.openlocfilehash: 2cddf8a7d3dbc7abcc25fb76aba8a0af1790fe4d
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88034445"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Montage du partage de fichiers Azure via SMB avec MacOS
[Azure Files](storage-files-introduction.md) est le système de fichiers cloud facile à utiliser de Microsoft. Les partages de fichiers Azure peuvent être montés avec le protocole standard SMB 3 par macOS El Capitan 10.11 et version supérieure. Cet article expose deux méthodes de montage d’un partage de fichiers Azure sur macOS : l’une avec l’interface utilisateur Finder et l’autre avec Terminal.

> [!Note]  
> Avant de monter un partage de fichiers Azure via SMB, il est recommandé de désactiver la signature de paquet SMB. Dans le cas contraire, cela risque d’entraîner des dysfonctionnements lors de l’accès au partage de fichiers Azure depuis MacOS. Votre connexion SMB est chiffrée. Cela n’affecte donc pas la sécurité de votre connexion. À partir du terminal, utilisez les commandes suivantes pour désactiver la signature de paquet SMB, conformément à la[rubrique d’aide Apple sur la désactivation de la signature des paquets SMB](https://support.apple.com/HT205926) :  
>    ```
>    sudo -s
>    echo "[default]" >> /etc/nsmb.conf
>    echo "signing_required=no" >> /etc/nsmb.conf
>    exit
>    ```

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Conditions préalables pour le montage d’un partage de fichiers Azure sous MacOS
* **Nom du compte de stockage** : Pour monter un partage de fichiers Azure, vous avez besoin du nom du compte de stockage.

* **Clé du compte de stockage** : Pour monter un partage de fichiers Azure, vous avez besoin de la clé de stockage primaire (ou secondaire). Actuellement, les clés SAS ne sont pas prises en charge pour le montage.

* **Vérifiez que le port 445 est ouvert** : SMB communique via le port TCP 445. Sur votre machine client (Mac), vérifiez que votre pare-feu ne bloque pas le port TCP 445.

## <a name="mount-an-azure-file-share-via-finder"></a>Montage d’un partage de fichiers Azure via Finder
1. **Ouvrez Finder** : Finder est ouvert par défaut sur macOS, mais vous pouvez vérifier qu’il s’agit bien de l’application actuellement sélectionnée en cliquant sur l’icône de visage macOS dans la barre :  
    ![L’icône MacOS (visage)](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Sélectionnez « Se connecter au serveur » dans le menu « Aller »**  : En utilisant le chemin UNC des prérequis, remplacez la double barre oblique inverse (`\\`) par `smb://` et toutes les autres barres obliques inverses (`\`) par des barres obliques normales (`/`). Votre lien doit se présenter comme suit : ![Boîte de dialogue « Se connecter au serveur »](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Utilisez le nom du compte de stockage et la clé du compte de stockage quand un nom d’utilisateur et un mot de passe vous sont demandés** : Quand vous cliquez sur « Connexion », dans la boîte de dialogue « Se connecter au serveur », vous êtes invité à entrer le nom d’utilisateur et le mot de passe (votre nom d’utilisateur macOS est automatiquement prérempli). Vous avez la possibilité de placer le nom/la clé du compte de stockage dans votre trousseau MacOS.

4. **Utilisez le partage de fichiers Azure à votre guise** : Après avoir remplacé le nom d’utilisateur et le mot de passe par le nom du partage et par la clé du compte de stockage, vous pouvez monter le partage. Vous pouvez l’utiliser comme un partage de fichiers / un dossier local normal. Vous pouvez notamment faire glisser et déposer des fichiers dans le partage :

    ![Une capture instantanée d’un partage de fichiers Azure monté](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Montage d’un partage de fichiers Azure via Terminal
1. Remplacez  `<storage-account-name>`  par le nom de votre compte de stockage. Lorsque vous y êtes invité, entrez la clé du compte de stockage comme mot de passe. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Utilisez le partage de fichiers Azure à votre guise** : Le partage de fichiers Azure est monté sur le point de montage spécifié par la commande précédente.  

    ![Une capture instantanée du partage de fichiers Azure monté](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Étapes suivantes
Consultez ces liens pour en savoir plus sur Azure Files.

* [Rubrique d’aide Apple : connexion au partage de fichiers sur Mac](https://support.apple.com/HT204445)
* [FORUM AUX QUESTIONS](../storage-files-faq.md)
* [Résolution des problèmes sur Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Résolution des problèmes sur Linux](storage-troubleshoot-linux-file-connection-problems.md)    
