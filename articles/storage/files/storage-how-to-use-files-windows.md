---
title: Monter un partage de fichiers SMB Azure sur Windows | Microsoft Docs
description: Apprenez à utiliser des partages de fichiers Azure avec Windows et Windows Server. Utilisez des partages de fichiers Azure avec SMB 3.x sur des installations Windows exécutées localement ou sur des machines virtuelles Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fecf2ea565343ad2f91471ba1be98df513b55478
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112115716"
---
# <a name="mount-smb-azure-file-share-on-windows"></a>Monter un partage de fichiers SMB Azure sur Windows
[Azure Files](storage-files-introduction.md) est le système de fichiers cloud facile à utiliser de Microsoft. Il est possible d’utiliser sans problème le partage de fichiers Azure dans Windows et Windows Server. Cet article décrit les considérations concernant l’utilisation d’un partage de fichiers Azure avec Windows et Windows Server.

Pour utiliser un partage de fichiers Azure via le point de terminaison public en dehors de la région Azure dans laquelle il est hébergé, par exemple localement ou dans une région Azure différente, le système d’exploitation doit prendre en charge SMB 3.x. Les versions antérieures de Windows qui prennent uniquement en charge SMB 2.1 ne peuvent pas monter de partages de fichiers Azure via le point de terminaison public.

| Version de Windows | Version SMB | Chiffrement maximal du canal SMB |
|-|-|-|-|
| Windows 10, version 21H1 | SMB 3.1.1 | AES-256-GCM |
| Canal semi-annuel Windows Server version 21H1 | SMB 3.1.1 | AES-256-GCM |
| Windows Server 2019 | SMB 3.1.1 | AES-128-GCM |
| Windows 10<br />Versions : 1607, 1809, 1909, 2004 et 20H2 | SMB 3.1.1 | AES-128-GCM |
| Canal semi-annuel Windows Server<br />Versions : 2004 et 20H2 | SMB 3.1.1 | AES-128-GCM |
| Windows Server 2016 | SMB 3.1.1 | AES-128-GCM |
| Windows 10, version 1507 | SMB 3.0 | AES-128-GCM |
| Windows 8.1 | SMB 3.0 | AES-128-CCM |
| Windows Server 2012 R2 | SMB 3.0 | AES-128-CCM |
| Windows Server 2012 | SMB 3.0 | AES-128-CCM |
| Windows 7<sup>1</sup> | SMB 2.1 | Non prise en charge |
| Windows Server 2008 R2<sup>1</sup> | SMB 2.1 | Non prise en charge |

<sup>1</sup>Le support Microsoft standard pour Windows 7 et Windows Server 2008 R2 est terminé. Il est possible d’acheter du support supplémentaire pour les mises à jour de sécurité par l’intermédiaire du [Programme des correctifs de sécurité étendus (ESU)](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates) uniquement. Nous vous recommandons vivement de procéder à la migration de ces systèmes d’exploitation.

> [!Note]  
> Nous vous conseillons de prendre la base de connaissances la plus récente pour votre version de Windows.

## <a name="applies-to"></a>S’applique à
| Type de partage de fichiers | SMB | NFS |
|-|:-:|:-:|
| Partages de fichiers Standard (GPv2), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Standard (GPv2), GRS/GZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Premium (FileStorage), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>Prérequis 
Vérifiez que le port 445 est ouvert : Le protocole SMB nécessite que le port TCP 445 soit ouvert, les connexions échoueront si ce port est bloqué. Vous pouvez vérifier si votre pare-feu bloque le port 445 avec l’applet de commande `Test-NetConnection`. Pour découvrir comment contourner un port 445 bloqué, consultez la section [Cause 1 : Le port 445 est bloqué](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) de notre guide de dépannage de Windows.

## <a name="using-an-azure-file-share-with-windows"></a>Utiliser un partage de fichiers Azure avec Windows
Pour utiliser un partage de fichiers Azure avec Windows, vous devez soit le monter (lui affecter un chemin de point de montage ou une lettre de lecteur), soit y accéder via son [chemin d’accès UNC](/windows/win32/fileio/naming-a-file). 

Cet article utilise la clé de compte de stockage pour accéder au partage de fichiers. Une clé de compte de stockage est une clé administrateur pour un compte de stockage (y compris les autorisations d’administrateur sur tous les fichiers et dossiers dans le partage de fichiers auquel vous accédez), ainsi que pour tous les partages de fichiers et autres ressources de stockage (objets blob, files d’attente, tables, etc.) contenus dans votre compte de stockage. Si cela ne suffit pas pour votre charge de travail, vous pouvez utiliser [Azure File Sync](../file-sync/file-sync-planning.md) ou une [authentification basée sur l’identité sur SMB](storage-files-active-directory-overview.md).

Un modèle commun pour délester et déplacer les applications métier (LOB) qui attendent un partage de fichiers SMB vers Azure consiste à utiliser un partage de fichiers Azure comme alternative pour l’exécution d’un serveur de fichiers Windows dédié dans une machine virtuelle Azure. Pour réussir la migration d’une application métier afin qu’elle utilise un partage de fichiers Azure, il est important de considérer que de nombreuses applications métier s’exécutent dans le contexte d’un compte de service dédié avec des autorisations système limitées, plutôt que le compte administrateur de la machine virtuelle. Par conséquent, vous devez vous assurer que vous montez/enregistrez les informations d’identification pour le partage de fichiers Azure à partir du contexte du compte de service plutôt que de votre compte d’administration.

### <a name="mount-the-azure-file-share"></a>Monter le partage de fichiers Azure

Le portail Azure vous fournit un script que vous pouvez utiliser pour monter votre partage de fichiers directement sur un hôte. Nous vous recommandons d’utiliser ce script fourni.

Pour obtenir ce script :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Accédez au compte de stockage contenant le partage de fichiers que vous souhaitez monter.
1. Sélectionner **Partages de fichiers**.
1. Sélectionnez le fichier que vous voulez monter.

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="Capture d’écran du volet Partages de fichiers, le partage de fichiers est mis en évidence.":::

1. Sélectionnez **Connecter**.

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="Capture d’écran de l’icône de connexion pour votre partage de fichiers.":::

1. Sélectionnez la lettre de lecteur sur laquelle monter le partage.
1. Copiez le script fourni.

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Capture d’écran du volet Se connecter, le bouton Copier du script est mis en évidence.":::

1. Collez le script dans un shell sur l’hôte sur lequel vous voulez monter le partage de fichiers, puis exécutez-le.

Vous avez maintenant monté votre partage de fichiers Azure.

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Montage du partage de fichiers Azure avec l’Explorateur de fichiers
> [!Note]  
> Notez que les instructions ci-après sont affichées sur Windows 10 et peuvent différer légèrement sur les versions plus anciennes. 

1. Ouvrez l’Explorateur de fichiers. Pour cela, accédez au menu Démarrer ou appuyez sur les touches Win+E.

1. Accédez à **Ce PC** sur le côté gauche de la fenêtre. Cela modifie les menus disponibles dans le ruban. Dans le menu Ordinateur, sélectionnez **Connecter un lecteur réseau**.
    
    ![Une capture d’écran du menu déroulant « Connecter un lecteur réseau »](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. Sélectionnez la lettre de lecteur et entrez le chemin d’accès UNC, le format du chemin d’accès UNC étant `\\<storageAccountName>.file.core.windows.net\<fileShareName>`. Par exemple : `\\anexampleaccountname.file.core.windows.net\example-share-name`.
    
    ![Une capture d’écran de la boîte de dialogue « Connecter un lecteur réseau »](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. Utilisez le nom du compte de stockage avec le préfixe `AZURE\` comme nom d’utilisateur et une clé de compte de stockage comme mot de passe.
    
    ![Une capture d’écran de la boîte de dialogue des identifiants réseau](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. Utilisez le partage de fichiers Azure à votre guise.
    
    ![Le partage de fichiers Azure est désormais monté](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. Lorsque vous êtes prêt à démonter le partage de fichiers Azure, il vous suffit de cliquer avec le bouton droit de la souris sur l’entrée du partage, sous **Emplacements réseau**, dans l’Explorateur de fichiers et de sélectionner **Déconnecter**.

### <a name="accessing-share-snapshots-from-windows"></a>Accès aux instantanés de partage à partir de Windows
Si vous avez utilisé un instantané de partage, soit manuellement, soit automatiquement par le biais d’un script ou d’un service tel que Sauvegarde Microsoft Azure, vous pouvez visualiser les versions précédentes d’un partage, d’un répertoire ou d’un fichier spécifique d’un partage de fichiers sur Windows. Vous pouvez prendre un instantané du partage en utilisant [Azure PowerShell](storage-how-to-use-files-powershell.md), [Azure CLI](storage-how-to-use-files-cli.md) ou le [Portail Azure](storage-how-to-use-files-portal.md).

#### <a name="list-previous-versions"></a>Répertorier les versions précédentes
Accédez à l’élément ou à l’élément parent à restaurer. Double-cliquez pour accéder au répertoire souhaité. Cliquez avec le bouton droit et sélectionnez **Propriétés** dans le menu.

![Menu contextuel pour un répertoire sélectionné](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

Sélectionnez **Versions précédentes** pour afficher la liste des instantanés de partage pour ce répertoire. Le chargement de la liste peut prendre quelques secondes, selon la vitesse du réseau et le nombre d’instantanés de partage du répertoire.

![Onglet Versions précédentes](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

Vous pouvez sélectionner **Ouvrir** pour ouvrir un instantané particulier. 

![Instantané ouvert](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Restaurer à partir d’une version précédente
Sélectionnez **Restaurer** pour copier à l’emplacement d’origine le contenu de l’ensemble du répertoire de façon récursive à l’heure de création de l’instantané de partage.

 ![Bouton Restaurer dans un message d’avertissement](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="next-steps"></a>Étapes suivantes
Consultez ces liens pour en savoir plus sur Azure Files :
- [Planification d’un déploiement Azure Files](storage-files-planning.md)
- [FORUM AUX QUESTIONS](./storage-files-faq.md)
- [Résolution des problèmes sur Windows](storage-troubleshoot-windows-file-connection-problems.md)