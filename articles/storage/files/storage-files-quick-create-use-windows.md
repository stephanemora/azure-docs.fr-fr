---
title: Créer et utiliser un partage de fichiers Azure sur des machines virtuelles Windows
description: Créez et utilisez un partage de fichiers Azure dans le portail Azure. Connectez-le à une machine virtuelle Windows, connectez-vous au partage de fichiers, et chargez un fichier dans le partage de fichiers.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 07/27/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3cb97d40008a103d9de6d76018f7881764813e3c
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114727183"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-windows-virtual-machines"></a>Démarrage rapide : Créer et gérer des partages de fichiers Azure avec des machines virtuelles Windows

L’article décrit les étapes de base à suivre pour créer et utiliser un partage Azure Files. Dans ce guide de démarrage rapide, l’accent est mis sur la configuration rapide d’un partage Azure Files, afin de vous faire découvrir le fonctionnement du service. Si vous avez besoin d’instructions plus détaillées sur la création et l’utilisation de partages de fichiers Azure dans votre propre environnement, consultez [Utiliser un partage de fichiers Azure avec Windows](storage-how-to-use-files-windows.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="applies-to"></a>S’applique à
| Type de partage de fichiers | SMB | NFS |
|-|:-:|:-:|
| Partages de fichiers Standard (GPv2), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Standard (GPv2), GRS/GZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Premium (FileStorage), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Préparation de votre environnement

Ce démarrage rapide configure les éléments suivants :

- Un compte de stockage Azure et un partage de fichiers Azure
- Une machine virtuelle Windows Server 2016 Datacenter

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Avant de pouvoir utiliser un partage de fichiers Azure, vous devez créer un compte de stockage Azure. Un compte de stockage v2 universel fournit un accès à tous les services de Stockage Azure : objets blob, fichiers, files d’attente et tables. Le guide de démarrage rapide crée un compte de stockage v2 universel, mais les étapes de création d’un autre type de compte de stockage sont similaires. Un compte de stockage peut contenir un nombre illimité de partages. Un partage peut stocker un nombre illimité de fichiers, dans les limites de capacité du compte de stockage.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-an-azure-file-share"></a>Crée un partage de fichiers Azure

Ensuite, créez un partage de fichiers.

1. Une fois le déploiement du compte de stockage Azure terminé, sélectionnez **Accéder à la ressource**.
1. Sélectionnez **Partages de fichiers** dans le volet du compte de stockage.

    ![Sélectionnez Partages de fichiers.](./media/storage-files-quick-create-use-windows/click-files.png)

1. Sélectionnez **+ Partage de fichiers**.

    ![Sélectionnez + Partage de fichiers pour créer un partage de fichiers.](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Nommez le nouveau partage de fichiers *qsfileshare*, entrez « 1 » pour le **Quota**, laissez l’option **Transaction optimisée** sélectionnée, puis sélectionnez **Créer**. La valeur du quota peut être de 5 Tio au maximum (100 Tio si les partages de fichiers volumineux sont activés). Toutefois, pour ce guide de démarrage rapide, vous n’avez besoin que de 1 Gio.
1. Créez un fichier txt nommé *qsTestFile* sur votre ordinateur local.
1. Sélectionnez le nouveau partage de fichiers puis, à l’emplacement de celui-ci, cliquez sur **Charger**.

    ![Chargez un fichier.](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Accédez à l’emplacement où vous avez créé votre fichier .txt, puis sélectionnez *qsTestFile.txt* et **Charger**.

Jusqu’ici, vous avez créé un compte de stockage Azure et un partage de fichiers contenant un seul fichier dans Azure. Vous allez maintenant créer la machine virtuelle Azure avec Windows Server 2016 Datacenter pour représenter le serveur local dans ce guide de démarrage rapide.

### <a name="deploy-a-vm"></a>Déployer une machine virtuelle

1. Ensuite, développez le menu à gauche du portail Azure et choisissez **Créer une ressource** dans le coin supérieur gauche.
1. Sous **Services populaires**, sélectionnez **Machines virtuelles**.
1. Sous l’onglet **De base**, sous **Détails du projet**, sélectionnez le groupe de ressources que vous avez créé pour ce guide de démarrage rapide.

   ![Entrez des informations de base sur votre machine virtuelle dans le panneau du portail.](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. Sous **Détails de l’instance**, nommez la machine virtuelle *qsVM*.
1. Pour **Image**, sélectionnez **Centre de données Windows Server 2016 – Gen2**.
1. Conservez les paramètres par défaut pour **Région**, **Options de disponibilité** et **Taille**.
1. Sous **Compte d’administrateur**, ajoutez un **Nom d’utilisateur** puis entrez un **Mot de passe** pour la machine virtuelle.
1. Sous **Règles des ports d’entrée**, choisissez **Autoriser les ports sélectionnés**, puis sélectionnez **RDP (3389)** et **HTTP** dans la liste déroulante.
1. Sélectionnez **Vérifier + créer**.
1. Sélectionnez **Create** (Créer). La création d’une machine virtuelle prend plusieurs minutes.

1. Une fois le déploiement de votre machine virtuelle terminé, sélectionnez **Accéder à la ressource**.

À ce stade, vous avez créé une machine virtuelle et attaché un disque de données. Vous devez maintenant vous connecter à la machine virtuelle.

### <a name="connect-to-your-vm"></a>Connexion à votre machine virtuelle

1. Sélectionnez **Se connecter** dans la page de propriétés de la machine virtuelle.

   ![Se connecter à une machine virtuelle Azure à partir du portail](./media/storage-files-quick-create-use-windows/connect-vm.png)

1. Dans la page **Se connecter à la machine virtuelle**, conservez les options par défaut pour vous connecter par **adresse IP** sur le **numéro de port** *3389* et sélectionnez **Télécharger le fichier RDP**.
1. Ouvrez le fichier RDP téléchargé et, à l’invite, sélectionnez **Connecter**.
1. Dans la fenêtre **Sécurité Windows**, sélectionnez **Plus de choix**, puis **Utiliser un autre compte**. Tapez le nom d’utilisateur sous la forme *localhost\username*, où &lt;username&gt; est le nom d’utilisateur administrateur que vous avez créé pour la machine virtuelle. Entrez le mot de passe que vous avez créé pour la machine virtuelle, puis sélectionnez **OK**.

   ![Plus de choix](./media/storage-files-quick-create-use-windows/local-host2.png)

1. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Sélectionnez **Oui** ou **Continuer** pour créer la connexion.

## <a name="map-the-azure-file-share-to-a-windows-drive"></a>Mapper le partage de fichiers Azure à un lecteur Windows

1. Dans le portail Azure, accédez au partage de fichiers *qsfileshare* et sélectionnez **Se connecter**.
1. Sélectionnez une lettre de lecteur, puis copiez-collez le contenu du deuxième champ dans le **Bloc-notes**.

   :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Capture d’écran montrant le contenu du champ que vous devez copier-coller dans le Bloc-notes." lightbox="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png":::

1. Dans la machine virtuelle, ouvrez **PowerShell** et collez le contenu du **Bloc-notes**. Ensuite, appuyez sur Entrée pour exécuter la commande. Cette opération doit mapper le lecteur.

## <a name="create-a-share-snapshot"></a>Créer un instantané de partage

Maintenant que vous avez mappé le lecteur, vous pouvez créer un instantané.

1. Dans le portail, accédez à votre partage de fichiers, sélectionnez **Instantanés**, puis sélectionnez **+ Ajouter un instantané**.

   ![Sélectionnez Instantanés sous la section Opérations, puis sélectionnez Ajouter un instantané.](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. Dans la machine virtuelle, ouvrez *qstestfile.txt*, tapez « ce fichier a été modifié », puis enregistrez et fermez le fichier.
1. Créez un autre instantané.

## <a name="browse-a-share-snapshot"></a>Parcourir un instantané de partage

1. Dans votre partage de fichiers, sélectionnez **Instantanés**.
1. Dans le volet **Instantanés**, sélectionnez le premier instantané de la liste.

   ![Instantané sélectionné dans la liste des horodatages](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. Ouvrez cet instantané, puis sélectionnez *qsTestFile.txt*.

## <a name="restore-from-a-snapshot"></a>Restaurer à partir d’un instantané

1. Dans le panneau de la capture instantanée du partage de fichiers, cliquez avec le bouton droit sur *qsTestFile*, puis sélectionnez le bouton **Restaurer**.

    :::image type="content" source="media/storage-files-quick-create-use-windows/restore-share-snapshot.png" alt-text="Capture d’écran du panneau Instantanés, où qstestfile est sélectionné et où l’option Restaurer est mise en évidence.":::

1. Sélectionnez **Remplacer le fichier d’origine**.

   ![Capture d’écran de la fenêtre contextuelle Restaurer, où l’option Remplacer le fichier d’origine est sélectionnée.](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. Dans la machine virtuelle, ouvrez le fichier. La version non modifiée a été restaurée.

## <a name="delete-a-share-snapshot"></a>Supprimer un instantané de partage

1. Dans votre partage de fichiers, sélectionnez **Instantanés**.
1. Dans le volet **Instantanés**, sélectionnez le dernier instantané de la liste, puis sélectionnez **Supprimer**.

   ![Capture d’écran du panneau Instantanés, où le dernier instantané est sélectionné et où le bouton Supprimer est mis en évidence.](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

## <a name="use-a-share-snapshot-in-windows"></a>Utiliser un instantané de partage dans Windows

Comme pour les instantanés VSS locaux, vous pouvez afficher les instantanés à partir de votre partage de fichiers Azure monté à l’aide de l’onglet Versions précédentes.

1. Dans l’Explorateur de fichiers, recherchez le partage monté.

   ![Partage monté dans l’Explorateur de fichiers](./media/storage-files-quick-create-use-windows/snapshot-windows-mount.png)

1. Sélectionnez *qsTestFile.txt*, faites un clic droit, puis sélectionnez **Propriétés** dans le menu.

   ![Menu contextuel pour un répertoire sélectionné](./media/storage-files-quick-create-use-windows/snapshot-windows-previous-versions.png)

1. Sélectionnez **Versions précédentes** pour afficher la liste des instantanés de partage pour ce répertoire.

1. Sélectionnez **Ouvrir** pour ouvrir l’instantané.

   ![Onglet Versions précédentes](./media/storage-files-quick-create-use-windows/snapshot-windows-list.png)

## <a name="restore-from-a-previous-version"></a>Restaurer à partir d’une version précédente

1. Sélectionnez **Restaurer**. Cette action copie le contenu de l’ensemble du répertoire de manière récursive à l’emplacement d’origine au moment de la création de l’instantané de partage.

   ![Bouton Restaurer dans un message d’avertissement](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png)
    
    > [!NOTE]
    > Si votre fichier n’a pas changé, vous ne verrez pas la version précédente de celui-ci, car il aura la même version que l’instantané. Ceci est cohérent avec la façon dont cela fonctionne sur un serveur de fichiers Windows.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Utiliser un partage de fichiers Azure avec Windows](storage-how-to-use-files-windows.md)
