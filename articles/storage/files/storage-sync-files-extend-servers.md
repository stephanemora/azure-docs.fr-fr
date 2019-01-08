---
title: 'Didacticiel : étendre les serveurs de fichiers Windows avec Azure File Sync | Microsoft Docs'
description: Découvrez la procédure complète pour étendre les serveurs de fichiers Windows avec Azure File Sync.
services: storage
author: wmgries
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 3ebf450f4e84fed572307a18f20f36013e32c7a5
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53630697"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Tutoriel : Étendre les serveurs de fichiers Windows avec Azure File Sync
Dans ce didacticiel, nous allons montrer les étapes de base permettant d’étendre la capacité de stockage d’un serveur Windows à l’aide d’Azure File Sync. Bien que nous utilisions une machine virtuelle Azure Windows Server pour ce didacticiel, ce processus s’applique sans problème à vos serveurs locaux. Si vous êtes prêt à déployer Azure File Sync dans votre propre environnement, consultez plutôt l’article [Déployer Azure File Sync](storage-sync-files-deployment-guide.md).

> [!div class="checklist"]
> * Déployer le service de synchronisation de stockage
> * Préparer Windows Server pour une utilisation avec Azure File Sync
> * Installer l’agent Azure File Sync
> * Inscrire un serveur Windows Server au service de synchronisation de stockage
> * Créer un groupe de synchronisation et un point de terminaison du cloud
> * Créer un point de terminaison de serveur

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="prepare-your-environment"></a>Préparation de votre environnement
Pour ce didacticiel, vous devez configurer quelques éléments avant de déployer Azure File Sync. En plus de la création d’un compte de stockage Azure et d’un partage de fichiers, vous allez créer une machine virtuelle Windows Server 2016 Datacenter et préparer ce serveur pour Azure File Sync.

### <a name="create-a-folder-and-txt-file"></a>Créer un dossier et un fichier .txt

Sur votre ordinateur local, créez un dossier nommé *FilesToSync* et ajoutez un fichier texte nommé *mytestdoc.txt*. Vous chargerez ce fichier sur le partage de fichiers plus loin dans ce didacticiel.

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Créer un partage de fichiers
Ensuite, créez un partage de fichiers.

1. Lorsque le déploiement du compte de stockage Azure est terminé, cliquez sur **Accéder à la ressource**.
1. Cliquez sur **Fichiers** depuis le volet du compte de stockage.

    ![Cliquer sur Fichiers](./media/storage-sync-files-extend-servers/click-files.png)

1. Cliquez sur **+ Partage de fichiers**.

    ![Cliquer sur le bouton Ajouter un partage de fichiers](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Nommez le nouveau partage de fichiers *afsfileshare* et entrez « 1 » pour le **Quota**, puis cliquez sur **Créer**. La valeur du quota peut être de 5 TiB au maximum, mais vous avez besoin uniquement de 1 Go pour ce didacticiel.

    ![Indiquer un nom et un quota pour le nouveau partage de fichiers](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Sélectionnez le nouveau partage de fichiers puis, sur l’emplacement du partage de fichier, cliquez sur **Charger**.

    ![Charger un fichier](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Accédez au dossier *FilesToSync* où vous avez créé votre fichier .txt, sélectionnez *mytestdoc.txt* et cliquez sur **Charger**.

    ![Explorer le partage de fichiers](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

À ce stade, vous avez créé un compte de stockage Azure et un partage de fichiers contenant un fichier dans Azure. Vous allez maintenant créer la machine virtuelle Azure avec Windows Server 2016 Datacenter pour représenter le serveur local dans ce didacticiel.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Déployer une machine virtuelle et attacher un disque de données

1. Ensuite, développez le menu à gauche du portail Azure et choisissez **Créer une ressource** dans le coin supérieur gauche.
1. Dans la zone de recherche au-dessus de la liste des ressources de la **Place de marché Azure**, recherchez et sélectionnez **Windows Server 2016 Datacenter**, puis choisissez **Créer**.
1. Dans l’onglet **Notions de base**, sous **Détails du projet**, sélectionnez le groupe de ressources que vous avez créé pour ce didacticiel.

   ![Saisie des informations de base sur votre machine virtuelle dans le panneau du portail](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. Sous **Détails de l’instance**, indiquez un nom de machine virtuelle, tel que *myVM*.
1. Conservez les paramètres par défaut pour la **Région**, les **Options de disponibilité**, l’**Image**, et la **Taille**.
1. Sous **Compte Administrateur**, indiquez un **nom d’utilisateur** et un **mot de passe** pour la machine virtuelle.
1. Sous **Règles des ports d’entrée**, choisissez **Autoriser les ports sélectionnés**, puis sélectionnez **RDP (3389)** et **HTTP** dans la liste déroulante.

   Avant de créer la machine virtuelle, vous devez créer un disque de données.

1. Cliquez sur **Suivant : Disques**

   ![Ajouter des disques de données](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

1. Dans l’onglet **Disques**, sous **Options du disque**, laissez les valeurs par défaut.
1. Sous **DISQUES DE DONNÉES**, cliquez sur **Créer et attacher un disque**.

1. Laissez les valeurs par défaut, mais remplacez la **Taille (Gio)** par **1 Go** pour ce didacticiel.

   ![Détails du disque de données](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

1. Cliquez sur **OK**.
1. Cliquez sur **Revoir + créer**.
1. Cliquez sur **Créer**.

   Vous pouvez cliquer sur l’icône **Notifications** pour regarder la **progression du déploiement**. La création d’une machine virtuelle prend plusieurs minutes.

1. Une fois le déploiement de votre machine virtuelle terminé, cliquez sur **Accéder à la ressource**.

   ![Accéder à la ressource](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

   À ce stade, vous avez créé une machine virtuelle et attaché un disque de données. Vous devez maintenant vous connecter à la machine virtuelle.

### <a name="connect-to-your-vm"></a>Connexion à votre machine virtuelle

1. Dans le portail Azure, cliquez sur **Se connecter** sur la page de propriétés de la machine virtuelle.

   ![Se connecter à une machine virtuelle Azure à partir du portail](./media/storage-sync-files-extend-servers/connect-vm.png)

1. Sur la page **Se connecter à la machine virtuelle**, conservez les options par défaut pour vous connecter par **adresse IP** sur le port 3389 et cliquez sur **Télécharger le fichier RDP**.

   ![Téléchargement du fichier RDP](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Ouvrez le fichier RDP téléchargé et, à l’invite, cliquez sur **Se connecter**.
1. Dans la fenêtre **Sécurité Windows**, sélectionnez **Plus de choix**, puis **Utiliser un autre compte**. Tapez le nom d’utilisateur sous la forme *localhost\username*, entrez le mot de passe créé pour la machine virtuelle, puis cliquez sur **OK**.

   ![Plus de choix](./media/storage-sync-files-extend-servers/local-host2.png)

1. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Cliquez sur **Oui** ou **Continuer** pour créer la connexion.

### <a name="prepare-the-windows-server"></a>Préparer le serveur Windows Server
Pour le serveur **Windows Server 2016 Datacenter**, désactivez la **Configuration de sécurité renforcée d’Internet Explorer**. Cette étape est nécessaire uniquement pour l’inscription initiale du serveur. Vous pouvez réactiver cette configuration une fois que le serveur a été inscrit.

Dans la machine virtuelle **Windows Server 2016 Datacenter**, le **Gestionnaire de serveur** s’ouvre automatiquement.  Si le **Gestionnaire de serveur** ne s’ouvre par défaut, recherchez-le dans l’Explorateur.

1. Dans le **Gestionnaire de serveur**, cliquez sur **Serveur Local**.

   ![« Serveur local » sur le côté gauche de l’interface utilisateur du Gestionnaire de serveur](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. Dans le sous-volet **Propriétés**, sélectionnez le lien vers **Configuration de sécurité renforcée d’Internet Explorer**.  

    ![Volet « Configuration de sécurité renforcée d’Internet Explorer » dans l’interface utilisateur du Gestionnaire de serveur](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. Dans la boîte de dialogue **Configuration de sécurité renforcée d’Internet Explorer**, sélectionnez **Désactivé** pour **Administrateurs** et **Utilisateurs**.

    ![Fenêtre contextuelle Configuration de sécurité renforcée d’Internet Explorer avec l’option « Désactivé » sélectionnée](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

   Vous pouvez maintenant ajouter le disque de données à la machine virtuelle.

### <a name="add-the-data-disk"></a>Ajouter le disque de données

1. Toujours dans la machine virtuelle **Windows Server 2016 Datacenter**, cliquez sur **Fichiers et services de stockage** > **Volumes** > **Disques**.

    ![Disque de données](media/storage-sync-files-extend-servers/your-disk.png)

1. Faites un clic droit sur le disque de 1 Go nommé **disque virtuel Msft** et cliquez sur **Nouveau volume**.
1. Terminez l’Assistant en conservant les valeurs par défaut, notez la lettre de lecteur assignée, puis cliquez sur **Créer**.
1. Cliquez sur **Fermer**.

   À ce stade, vous avez mis le disque en ligne et créé un volume. Vous pouvez vérifier que l’ajout du disque de données a réussi en ouvrant un Explorateur sur la machine virtuelle et en vérifiant que le nouveau lecteur est bien présent.

1. Dans l’Explorateur de la machine virtuelle, développez **Ce PC** et double-cliquez sur le nouveau lecteur. Dans cet exemple, il s’agit du lecteur F: .
1. Faites un clic droit et sélectionnez **Nouveau** > **Dossier**. Nommez le dossier *FilesToSync*.
1. Double-cliquez sur le dossier **FilesToSync**.
1. Faites un clic droit et sélectionnez **Nouveau** > **Document texte**. Nommez le fichier texte *MyTestFile*.

    ![Ajouter un nouveau fichier texte](media/storage-sync-files-extend-servers/new-file.png)

1. Fermez l’**Explorateur** et le **Gestionnaire de serveur**.

### <a name="download-the-azure-powershell-module"></a>Télécharger le module Azure PowerShell
Ensuite, sur la machine virtuelle **Windows Server 2016 Datacenter**, installez le **module Azure PowerShell** sur le serveur.

1. Dans la machine virtuelle, ouvrez une fenêtre Windows PowerShell avec des privilèges élevés.
1. Exécutez la commande suivante :

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

   > [!NOTE]
   > Si votre version de NuGet est antérieure à 2.8.5.201, vous êtes invité à télécharger et installer la dernière version de NuGet.

   Par défaut, la galerie PowerShell n’est pas configurée comme un référentiel de confiance pour PowerShellGet. La première fois que vous utilisez PSGallery, le message suivant s’affiche :

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. Répondez `Yes` ou `Yes to All` pour procéder à l’installation.

Le module `Az` est un module cumulatif pour les cmdlets Azure PowerShell. Son installation permet de télécharger tous les modules Azure Resource Manager disponibles, et rend leurs cmdlets disponibles.

À ce stade, vous avez terminé de configurer votre environnement pour le didacticiel et vous êtes prêt à commencer à déployer le **Service de synchronisation de stockage**.

## <a name="deploy-the-service"></a>Déployer le service 
Le déploiement d’Azure File Sync commence par le placement d’une ressource **Service de synchronisation de stockage** dans un groupe de ressources de votre abonnement sélectionné. Le service de synchronisation de stockage hérite des autorisations d’accès de l’abonnement et du groupe de ressources dans lequel vous l’avez déployé.

1. Dans le portail Azure, cliquez sur **Créer une ressource**, puis recherchez **Azure File Sync**.
1. Dans les résultats de recherche, sélectionnez **Azure File Sync**.
1. Sélectionnez **Créer** pour ouvrir l’onglet **Déployer la synchronisation du stockage**.

   ![Déployer la synchronisation du stockage](media/storage-sync-files-extend-servers/afs-info.png)

   Dans le volet qui s’ouvre, entrez les informations suivantes :

   | Valeur | Description |
   | ----- | ----- |
   | **Nom** | Nom unique (par abonnement) du service de synchronisation de stockage.<br><br>Dans ce didacticiel, nous utilisons *afssyncservice02*. |
   | **Abonnement** | L’abonnement que vous utilisez pour ce didacticiel. |
   | **Groupe de ressources** | Le groupe de ressources que vous utilisez pour ce didacticiel.<br><br>Dans ce didacticiel, nous utilisons *afsresgroup101918*. |
   | **Lieu** | USA Est |

1. Quand vous avez terminé, sélectionnez **Créer** pour déployer le **service de synchronisation de stockage**.
1. Cliquez sur l’onglet **Notifications** > **Accéder à la ressource**.

## <a name="install-the-agent"></a>Installer l’agent
L’agent Azure File Sync est un package téléchargeable qui permet à Windows Server de rester synchronisé avec un partage de fichiers Azure.

1. Revenez à la machine virtuelle **Windows Server 2016 Datacenter** et ouvrez **Internet Explorer**.
1. Accédez au [Centre de téléchargement Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Faites défiler jusqu’à la section **Agent Azure File Sync** et cliquez sur **Télécharger**.

   ![Téléchargement de l’agent de synchronisation](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Cochez la case **StorageSyncAgent_V3_WS2016. EXE** et cliquez sur **Suivant**.

   ![Sélectionner l’agent](media/storage-sync-files-extend-servers/select-agent.png)

1. **Autoriser une fois** > **Exécuter** > **Ouvrir** le fichier.
1. Si vous ne l’avez pas déjà fait, fermez la fenêtre PowerShell.
1. Acceptez les valeurs par défaut dans l’**Assistant Configuration de l’agent de synchronisation de stockage**.
1. Cliquez sur **Installer**.
1. Cliquez sur **Terminer**.

Vous avez déployé le Service Azure Sync et installé l’agent sur la machine virtuelle **Windows Server 2016 Datacenter**. Maintenant, vous devez inscrire la machine virtuelle avec le **Service de synchronisation de stockage**.

## <a name="register-windows-server"></a>Inscrire le serveur Windows Server
L’inscription de votre serveur Windows Server à un service de synchronisation de stockage établit une relation d’approbation entre votre serveur (ou cluster) et le service de synchronisation de stockage. Un serveur ne peut être inscrit qu’auprès d’un seul service de synchronisation du stockage et peut se synchroniser avec d’autres serveurs et avec des partages de fichiers Azure associés avec le même service de synchronisation de stockage.

L’interface utilisateur d’inscription du serveur s’ouvre normalement automatiquement après l’installation de l’**agent Azure File Sync**. Si ce n’est pas le cas, vous pouvez l’ouvrir manuellement à partir de son emplacement de fichier : C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.

1. Quand l’interface utilisateur d’inscription du serveur s’ouvre dans la machine virtuelle, cliquez sur **OK**.
1. Cliquez sur **Connexion** pour commencer.
1. Connectez-vous avec les informations d’identification de votre compte Azure puis cliquez sur **Connexion**.
1. Fournissez les informations suivantes :

   ![Capture d’écran de l’interface utilisateur d’inscription de serveur](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Valeur | Description |
   | **Abonnement Azure** | L’abonnement qui contient le Service de synchronisation de stockage pour ce didacticiel. |
   | **Groupe de ressources** | L’abonnement qui contient le Service de synchronisation de stockage pour ce didacticiel. Nous avons utilisé *afsresgroup101918* tout au long de ce didacticiel. |
   | **Service de synchronisation de stockage** | Le nom du Service de synchronisation de stockage que vous avez utilisé pour ce didacticiel. Nous avons utilisé *afssyncservice02* tout au long de ce didacticiel. |

1. Cliquez sur **Inscrire** pour terminer le processus d’inscription du serveur.
1. Dans le cadre du processus d’inscription, vous êtes invité à effectuer une nouvelle connexion. Connectez-vous, puis cliquez sur **Suivant**.
1. Cliquez sur **OK**.

## <a name="create-a-sync-group"></a>Créer un groupe de synchronisation
Un groupe de synchronisation définit la topologie de synchronisation d’un ensemble de fichiers. Un groupe de synchronisation doit contenir un seul point de terminaison cloud, qui représente un partage de fichiers Azure, et un ou plusieurs points de terminaison de serveur. Un point de terminaison de serveur représente un chemin d’accès vers un serveur inscrit.

1. Pour créer un groupe de synchronisation, dans le [portail Azure](https://portal.azure.com/), sélectionnez **+ Groupe de synchronisation** à partir du service de synchronisation de stockage que vous avez créé pour ce didacticiel. Nous avons utilisé *afssyncservice02* dans ce didacticiel.

   ![Créer un groupe de synchronisation dans le portail Azure](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Dans le volet qui s’ouvre, entrez les informations suivantes pour créer un groupe de synchronisation avec un point de terminaison cloud :

   | Valeur | Description |
   | ----- | ----- |
   | **Nom du groupe de synchronisation** | Ce nom doit être unique au sein du service de synchronisation de stockage, mais vous pouvez choisir tout nom ayant un sens pour vous. Dans ce didacticiel, nous utilisons *afssyncgroup*.|
   | **Abonnement** | L’abonnement pour lequel vous avez déployé le Service de synchronisation de stockage pour ce didacticiel. |
   | **Compte de stockage** |Cliquez sur **Sélectionner le compte de stockage**. Dans le volet qui s’affiche, sélectionnez le compte de stockage qui dispose du partage de fichiers Azure que vous avez créé pour ce didacticiel. Nous avons utilisé *afsstoracct101918*. |
   | **Partage de fichiers Azure** | Le nom du partage de fichiers Azure que vous avez créé pour ce didacticiel. Nous avons utilisé *afsfileshare*. |

1. Cliquez sur **Créer**.

Si vous sélectionnez votre groupe de synchronisation, vous pouvez voir que vous disposez maintenant d’un **point de terminaison cloud**.

## <a name="add-a-server-endpoint"></a>Ajouter un point de terminaison de serveur
Un point de terminaison de serveur représente un emplacement spécifique sur un serveur inscrit, comme un dossier sur un volume de serveur.

1. Pour ajouter un point de terminaison de serveur, accédez au nouveau groupe de synchronisation, puis sélectionnez **Ajouter un point de terminaison de serveur**.

   ![Ajouter un nouveau point de terminaison de serveur dans le volet Groupe de synchronisation](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. Dans le volet **Ajouter un point de terminaison de serveur**, entrez les informations suivantes pour créer un point de terminaison de serveur :

   | | |
   | ----- | ----- |
   | Valeur | Description |
   | **Serveur inscrit** | Le nom du serveur que vous avez créé pour ce didacticiel. Nous avons utilisé *afsvm101918* dans ce didacticiel. |
   | **Chemin d’accès** | Le chemin d’accès Windows Server vers le lecteur que vous avez créé pour ce didacticiel. Dans notre exemple, il s’agit de *f:\filestosync*. |
   | **Hiérarchisation cloud** | Laissez le champ désactivé pour ce didacticiel. |
   | **Espace libre du volume** | Laissez le champ vide pour ce didacticiel. |

1. Cliquez sur **Créer**.

Vos fichiers sont maintenant synchronisés entre le partage de fichiers Azure et Windows Server.

![Le Stockage Azure s’est synchronisé avec succès](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris les étapes de base permettant d’étendre la capacité de stockage d’un serveur Windows à l’aide d’Azure File Sync. Suivez ce lien pour plus de détails concernant la planification d’un déploiement Azure File Sync.

> [!div class="nextstepaction"]
> [Planification d’un déploiement de synchronisation de fichiers Azure](./storage-sync-files-planning.md)
