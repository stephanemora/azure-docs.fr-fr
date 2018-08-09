---
title: Gérer les partages de fichiers Azure à l’aide de l’Explorateur Stockage Azure
description: Découvrez comment utiliser l’Explorateur Stockage Azure pour gérer Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: get-started-article
ms.date: 02/27/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: e8989cadd331021a3e35fc559421bbdd08f0b4fb
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521192"
---
# <a name="manage-azure-file-shares-with-azure-storage-explorer"></a>Gérer les partages de fichiers Azure avec l’Explorateur Stockage Azure 
[Azure Files](storage-files-introduction.md) est le système de fichiers cloud facile à utiliser de Microsoft. Cet article vous explique les bases de l’utilisation des partages de fichiers Azure à l’aide de l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/). L’Explorateur Stockage est un outil client populaire qui est disponible pour Windows, macOS et Linux. Vous pouvez utiliser l’Explorateur Stockage pour gérer les partages de fichiers Azure et d’autres ressources de stockage.

Pour ce guide de démarrage rapide, l’Explorateur Stockage doit être installé. Pour le télécharger et l’installer, accédez à [Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/).

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer un groupe de ressources et un compte de stockage
> * Crée un partage de fichiers Azure 
> * Créer un répertoire
> * Charger un fichier
> * Téléchargement d’un fichier
> * Créer et utiliser un instantané de partage

Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-storage-account"></a>Créez un compte de stockage.
Vous ne pouvez pas utiliser l’Explorateur Stockage pour créer de nouvelles ressources. Dans le cadre de cette démonstration, créez le compte de stockage dans le [portail Azure](https://portal.azure.com/). 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connect-storage-explorer-to-azure-resources"></a>Connecter l’Explorateur Stockage à des ressources Azure
Lors du premier démarrage de l’Explorateur Stockage, la fenêtre **Microsoft Azure Storage Explorer - Connect** (Explorateur Stockage Microsoft Azure - Connexion) s’affiche. L’Explorateur Stockage offre de nombreuses façons de se connecter à des comptes de stockage : 

- **Se connecter à l’aide de votre compte Azure** : vous pouvez vous connecter en utilisant les informations d’identification utilisateur de votre organisation ou votre compte Microsoft. 
- **Se connecter à un compte de stockage spécifique à l’aide d’une chaîne de connexion ou d’un jeton SAS** : une chaîne de connexion est une chaîne spéciale qui contient un nom de compte de stockage et un jeton de clé/SAS de compte de stockage. Avec le jeton, l’Explorateur Stockage accède directement au compte de stockage (plutôt que d’afficher simplement tous les comptes de stockage dans un compte Azure). Pour plus d’informations sur les chaînes de connexion, consultez [Configuration des chaînes de connexion du Stockage Azure](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- **Se connecter à un compte de stockage spécifique à l’aide d’un nom et d’une clé de compte de stockage** : utilisez le nom et la clé de votre compte de stockage pour vous connecter au stockage Azure.

Dans le cadre de ce guide de démarrage rapide, connectez-vous à l’aide de votre compte Azure. Sélectionnez **Ajouter un compte Azure**, puis **Connexion**. Suivez les instructions de l’invite pour vous connecter à votre compte Azure.

![Capture d’écran de la fenêtre de connexion à l’Explorateur Stockage Microsoft Azure](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Créer un partage de fichiers
Pour créer votre premier partage de fichiers Azure dans le compte de stockage *storageacct<random number>* :

1. Développez le compte de stockage que vous avez créé.
2. Cliquez avec le bouton droit sur **Partages de fichiers**, puis sélectionnez **Créer un partage de fichiers**.  
    ![Capture d’écran du dossier du fichier de partage et du menu contextuel](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Pour le partage de fichiers, entrez *myshare*, puis appuyez sur Entrée.

> [!IMPORTANT]  
> Le nom des partages ne peut contenir que des minuscules, des nombres et des traits d’union uniques (mais ne peut commencer par un trait d’union). Pour plus d’informations sur la façon de nommer des partages de fichiers et des fichiers, consultez [Affectation de noms et références aux partages, répertoires, fichiers et métadonnées](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Lorsque le partage de fichiers est créé, un onglet pour votre partage de fichiers s’ouvre dans le volet droit. 

## <a name="work-with-the-contents-of-an-azure-file-share"></a>Utiliser le contenu d’un partage de fichiers Azure
Maintenant que vous avez créé un partage de fichiers Azure, vous pouvez monter le partage de fichiers avec SMB sur [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) ou [macOS](storage-how-to-use-files-mac.md). Vous pouvez également utiliser votre partage de fichiers Azure à l’aide d’Azure CLI. L’avantage d’utiliser Azure CLI au lieu de monter le partage de fichiers à l’aide de SMB est tel que toutes les requêtes effectuées avec Azure CLI le sont à l’aide de l’API REST de fichier. Vous pouvez utiliser l’API REST de fichier pour créer, modifier et supprimer des fichiers et des répertoires sur des clients n’ayant pas accès à SMB.

### <a name="create-a-directory"></a>Créer un répertoire
L’ajout d’un répertoire fournit une structure hiérarchique pour gérer votre partage de fichiers. Vous pouvez créer plusieurs niveaux dans votre répertoire. Vous devez toutefois vous assurer que tous les répertoires parents existent avant de créer des sous-répertoires. Par exemple, pour le chemin myDirectory/mySubDirectory, vous devez d’abord créer le répertoire *myDirectory*. Vous pouvez ensuite créer *mySubDirectory*. 

1. Sous l’onglet du partage de fichiers, dans le menu supérieur, sélectionnez le bouton **+ Nouveau dossier**. Le volet **Créer un nouvel annuaire** s’ouvre.
    ![Capture d’écran du bouton Nouveau dossier en contexte](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. Pour le nom du répertoire, entrez *myDirectory*, puis sélectionnez **OK**. 

Le répertoire *myDirectory* est répertorié sur l’onglet du partage de fichiers *myshare*.

### <a name="upload-a-file"></a>Charger un fichier 
Vous pouvez charger un fichier depuis votre ordinateur local vers le nouveau répertoire dans votre partage de fichiers. Vous pouvez charger un dossier entier ou un seul fichier.

1. Dans le menu supérieur, sélectionnez **Charger**. Vous avez alors l’option de charger un dossier ou un fichier.
2. Sélectionnez **Charger un fichier**, puis sélectionnez un fichier à charger dans votre ordinateur local.
3. Dans **Upload to a directory** (Charger vers un répertoire), entrez *myDirectory*, puis sélectionnez sur **Charger**. 

Lorsque vous avez terminé, le fichier apparaît dans la liste du volet *myDirectory*.

### <a name="download-a-file"></a>Téléchargement d’un fichier
Pour télécharger une copie d’un fichier à partir de votre partage de fichiers, cliquez avec le bouton droit sur le fichier, puis sélectionnez **Télécharger**. Choisissez où placer le fichier sur votre ordinateur local, puis sélectionnez **Enregistrer**.

La progression du téléchargement s’affiche dans le volet **Activités** en bas de la fenêtre.

## <a name="create-and-modify-share-snapshots"></a>Créer et modifier des instantanés de partage
Un instantané conserve une copie d’un point dans le temps d’un partage de fichiers Azure. Les instantanés de partage de fichiers sont similaires à d’autres technologies que vous connaissez peut-être déjà :
- Le [service VSS](https://docs.microsoft.com/en-us/windows/desktop/VSS/volume-shadow-copy-service-portal) pour les systèmes de fichiers Windows comme NTFS et ReFS
- Les instantanés du [Gestionnaire de Volume logique (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) pour les systèmes Linux
- Les instantanés du [système de fichiers Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) pour macOS

Pour créer un instantané de partage :

1. Sélectionnez l’onglet du partage de fichiers *myshare*.
2. Dans le menu supérieur, sélectionnez **Créer l’instantané**. (Vous devrez peut-être d’abord sélectionner **Plus** pour voir cette option, selon les dimensions de l’Explorateur Stockage.)  
    ![Capture d’écran du bouton Créer l’instantané en contexte](media/storage-how-to-use-files-storage-explorer/create-share-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Répertorier et parcourir les instantanés de partage
Lorsque l’instantané est créé, pour répertorier les instantanés pour le partage, sélectionnez **View Snapshots for File Share** (Afficher les instantanés pour le partage de fichiers). (Vous devrez peut-être d’abord sélectionner **Plus** pour voir cette option, selon les dimensions de l’Explorateur Stockage.) Pour parcourir un instantané de partage, double-cliquez sur l’instantané.

![Capture d’écran de fenêtre Browse snapshots (Parcourir les instantanés)](media/storage-how-to-use-files-storage-explorer/list-browse-snapshots-1.png)

### <a name="restore-from-a-share-snapshot"></a>Restaurer à partir d’un instantané de partage
Pour illustrer comment restaurer un fichier à partir d’un instantané de partage, vous devez d’abord supprimer un fichier du partage de fichiers Azure. Accédez au dossier *myDirectory*, cliquez avec le bouton droit sur le fichier chargé, puis sélectionnez **Supprimer**. Pour restaurer ce fichier depuis l’instantané de partage :

1. Sélectionnez **View Snapshots for File Share** (Afficher les instantanés pour le partage de fichiers). (Vous devrez peut-être d’abord sélectionner **Plus** pour voir cette option, selon les dimensions de l’Explorateur Stockage.)
2. Dans la liste des instantanés de partage, double-cliquez sur l’instantané de partage.
3. Parcourez l’instantané jusqu’à ce que vous trouviez le fichier que vous avez supprimé. Sélectionnez le partage de fichiers, puis **Restaurer l’instantané**. (Vous devrez peut-être d’abord sélectionner **Plus** pour voir cette option, selon les dimensions de l’Explorateur Stockage.) Une fenêtre s’ouvre et affiche un avertissement indiquant que la restauration du fichier remplacera le contenu du partage de fichiers, et que cette opération est irréversible. Sélectionnez **OK**.
4. Le fichier doit maintenant avoir retrouvé sa place dans le partage de fichiers Azure.

### <a name="delete-a-share-snapshot"></a>Supprimer un instantané de partage
Pour supprimer un instantané de partage, accédez à la [liste des instantanés de partage](#list-and-browse-share-snapshots). Cliquez avec le bouton droit sur l’instantané de partage à supprimer, puis sélectionnez **Supprimer**.

## <a name="clean-up-resources"></a>Supprimer les ressources
Vous ne pouvez pas utiliser l’Explorateur Stockage pour supprimer des ressources. Pour nettoyer les éléments de ce guide de démarrage rapide, vous pouvez utiliser le [portail Azure](https://portal.azure.com/). 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Étapes suivantes
- [Gérer les partages de fichiers avec le portail Azure](storage-how-to-use-files-portal.md)
- [Gérer les partages de fichiers avec Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Gérer les partages de fichiers avec Azure CLI](storage-how-to-use-files-cli.md)
- [Planifier un déploiement Azure Files](storage-files-planning.md)