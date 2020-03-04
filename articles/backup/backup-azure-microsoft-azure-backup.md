---
title: Utiliser un serveur de sauvegarde Azure pour sauvegarder des charges de travail
description: Dans cet article, découvrez comment préparer votre environnement à la protection et à la sauvegarde des charges de travail avec le serveur de sauvegarde Microsoft Azure (MABS).
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: dd506668f9d75523ff7494bccb2979bf0785990d
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617615"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Installer et mettre à niveau Azure Backup Server

> [!div class="op_single_selector"]
>
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> S’applique à : MABS v3. (MABS v2 n’est plus pris en charge. Si vous utilisez une version antérieure à MABS v3, effectuez une mise à niveau vers la version la plus récente.)

Cet article décrit la préparation de votre environnement à la sauvegarde des charges de travail avec Microsoft Azure Backup Server (MABS). Le serveur de sauvegarde Azure vous permet de protéger des charges de travail d’application telles que des machines virtuelles Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange et des clients Windows à partir d’une console unique.

> [!NOTE]
> Serveur de sauvegarde Azure peut désormais protéger les machines virtuelles VMware et fournit des fonctionnalités de sécurité améliorées. Installez le produit comme expliqué dans les sections ci-dessous et le dernier agent Azure Backup. Pour plus d’informations sur la sauvegarde de serveurs VMware avec le serveur de sauvegarde Azure, voir [Utiliser le serveur de sauvegarde Azure pour sauvegarder un serveur VMware](backup-azure-backup-server-vmware.md). Pour découvrir les fonctionnalités de sécurité, voir la [documentation sur les fonctionnalités de sécurité de sauvegarde Azure](backup-azure-security-feature.md).
>
>

Les serveurs MABS (Microsoft Azure Backup Server) déployés dans une machine virtuelle Azure peuvent sauvegarder des machines virtuelles dans Azure, mais ils doivent se trouver dans le même domaine pour que l’opération de sauvegarde puisse s’effectuer. Le processus de sauvegarde d’une machine virtuelle Azure reste identique à la sauvegarde d’une machine virtuelle en local, mais le déploiement de Microsoft Azure Backup Server dans Azure présente certaines limitations. Pour plus d’informations sur ces limitations, consultez [DPM en tant que machine virtuelle Azure](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites)

> [!NOTE]
> Azure comporte deux modèles de déploiement pour la création et l’utilisation de ressources : [Resource Manager et classique](../azure-resource-manager/management/deployment-models.md). Cet article fournit les informations et les procédures relatives à la restauration des machines virtuelles déployées à l’aide du modèle Resource Manager.
>
>

Azure Backup Server hérite d’une grand partie des fonctionnalités de sauvegarde de charge de travail de Data Protection Manager (DPM). Cet article comporte des liens vers la documentation sur DPM pour expliquer certaines des fonctionnalités partagées. Bien que la plupart des fonctionnalités d’Azure Backup Server soient identiques à celles de DPM, Azure Backup Server ne sauvegarde pas sur bande et ne s’intègre pas dans System Center.

## <a name="choose-an-installation-platform"></a>Choisir une plateforme d’installation

La première étape de la mise en service d’Azure Backup Server consiste à configurer un serveur Windows Server. Il peut s’agir d’un serveur local ou d’un serveur dans Azure.

### <a name="using-a-server-in-azure"></a>Utilisation d’un serveur dans Azure

Lorsque vous choisissez un serveur pour exécuter le serveur de sauvegarde Azure, nous vous recommandons de commencer par une image de la galerie de Windows Server 2016 Datacenter ou de Windows Server 2019 Datacenter. L’article [Créer votre première machine virtuelle Windows dans le portail Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)propose un didacticiel de prise en main de la machine virtuelle recommandée dans Azure, même si vous n’avez jamais utilisé Azure. Configuration minimale recommandée pour la machine virtuelle serveur : Standard_A4_v2 avec quatre cœurs et 8 Go de RAM.

La protection des charges de travail à l’aide d’Azure Backup Server peut prendre plusieurs formes. L’article [Installation de DPM en tant que machine virtuelle Azure](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/jj852163(v=sc.12)), permet d’expliquer ces différentes formes. Avant de déployer la machine, lisez cet entièrement cet article.

### <a name="using-an-on-premises-server"></a>Utilisation d’un serveur local

Si vous ne souhaitez pas exécuter le serveur de base dans Azure, vous pouvez l’exécuter sur un ordinateur virtuel Hyper-V, un ordinateur virtuel VMware ou un hôte physique. Configuration minimale recommandée pour le matériel de serveur : 2 cœurs et 8 Go de RAM. Les systèmes d’exploitation pris en charge sont répertoriés dans le tableau ci-après :

| Système d’exploitation | Plateforme | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64 bits |Standard, Datacenter, Essentials |
| Windows Server 2016 et derniers Service Packs |64 bits |Standard, Datacenter, Essentials  |

Vous pouvez dédupliquer le stockage DPM en vous servant de la fonction de déduplication Windows Server. En savoir plus sur le fonctionnement du [DPM et de la déduplication](https://docs.microsoft.com/system-center/dpm/deduplicate-dpm-storage?view=sc-dpm-2019) en cas de déploiement sur des machines virtuelles Hyper-V.

> [!NOTE]
> Le serveur de sauvegarde Azure est conçu pour s’exécuter sur un serveur dédié spécialisé. Vous ne pouvez pas installer le serveur de sauvegarde Azure sur :
>
> * Un ordinateur servant de contrôleur de domaine
> * Un ordinateur sur lequel est installé le rôle de serveur d’applications
> * Un ordinateur qui est un serveur d’administration de System Center Operations Manager
> * Un ordinateur sur lequel Exchange Server s’exécute
> * Un ordinateur qui est un nœud d’un cluster
>
> L'installation de serveur de sauvegarde Azure n'est pas prise en charge sur Windows Server Core ou Microsoft Hyper-V Server.

Joignez toujours le serveur de sauvegarde Azure à un domaine. Si vous envisagez de déplacer le serveur vers un autre domaine, installez d’abord le serveur de sauvegarde Azure, puis joignez-le au nouveau domaine. Le déplacement d’une machine Azure Backup Server vers un nouveau domaine après le déploiement *n’est pas pris en charge*.

Que vous envoyiez des données de sauvegarde vers Azure ou que vous les conserviez localement, le serveur de sauvegarde Azure doit être inscrit auprès d’un coffre Recovery Services.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Définir la réplication du stockage

L’option de réplication du stockage vous permet de choisir entre stockage géo-redondant et stockage localement redondant. Par défaut, les coffres Recovery Services utilisent le stockage géoredondant. Si cet archivage est votre archivage principal, laissez l’option de stockage définie sur un stockage géoredondant. Choisissez Stockage localement redondant si vous souhaitez une option plus économique, mais moins durable. Pour en savoir plus sur les options de stockage [géo-redondant](../storage/common/storage-redundancy-grs.md) et [localement redondant](../storage/common/storage-redundancy-lrs.md), consultez l’article [Réplication Stockage Azure](../storage/common/storage-redundancy.md).

Pour modifier le paramètre de réplication du stockage :

1. Dans le panneau **Coffres Recovery Services**, cliquez sur le nouveau coffre. Dans la section **Paramètres**, cliquez sur **Propriétés**.
2. Dans **Propriétés**, sous **Configuration de la sauvegarde**, cliquez sur **Mise à jour**.

3. Sélectionnez le type de réplication de stockage, puis cliquez sur **Enregistrer**.

     ![Définir la configuration de stockage du nouveau coffre](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Package logiciel

### <a name="downloading-the-software-package"></a>Téléchargement du package logiciel

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Si vous avez un coffre Recovery Services ouvert, passez à l’étape 3. Si vous n’avez aucun coffre Recovery Services ouvert, mais que vous vous trouvez dans le portail Azure, cliquez sur **Parcourir**dans le menu principal.

   * Dans la liste des ressources, tapez **Recovery Services**.
   * Au fur et à mesure des caractères saisis, la liste est filtrée. Lorsque vous voyez **Coffres Recovery Services**, cliquez dessus.

     ![Créer un coffre Recovery Services - Étape 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     La liste des archivages de Recovery Services s’affiche.
   * Dans la liste des archivages de Recovery Services, sélectionnez un archivage.

     Le tableau de bord de l’archivage sélectionné s'ouvre.

     ![Ouvrir le panneau de l’archivage](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Le panneau **Paramètres** s’ouvre par défaut. S’il est fermé, cliquez sur **Paramètres** pour ouvrir le panneau Paramètres.

    ![Ouvrir le panneau de l’archivage](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Cliquez sur **Sauvegarde** pour ouvrir l’Assistant Mise en route.

    ![Prise en main de la sauvegarde](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    Dans le panneau **Prise en main de la sauvegarde** qui s’affiche, **Objectifs de la sauvegarde** est automatiquement sélectionné.

    ![Backup-goals-default-opened](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. Dans le panneau **Objectif de sauvegarde** du menu **Où s’exécute votre charge de travail ?** , sélectionnez **Local**.

    ![en local et charges de travail comme objectifs](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    À partir du menu déroulant **Que voulez-vous sauvegarder ?** , sélectionnez les charges de travail que vous souhaitez protéger à l’aide du serveur de sauvegarde Azure, puis cliquez sur **OK**.

    L’Assistant **Mise en route de la sauvegarde** modifie l’option de **préparation de l’infrastructure** pour sauvegarder les charges de travail sur Azure.

   > [!NOTE]
   > Si vous souhaitez sauvegarder uniquement des fichiers et dossiers, nous vous recommandons d’utiliser l’agent Azure Backup et de suivre les instructions de l’article [Premier aperçu : sauvegarder des fichiers et des dossiers dans un déploiement de Resource Manager](backup-try-azure-backup-in-10-mins.md). Si vous envisagez de protéger davantage de fichiers et de dossiers, ou si à l’avenir vous envisagez d’étendre les besoins de protection, sélectionnez ces charges de travail.
   >
   >

    ![Modification de l’Assistant Mise en route](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. Dans le panneau **Préparer l’infrastructure** qui s’ouvre, cliquez sur les **liens de téléchargement** pour installer le serveur de sauvegarde Azure et télécharger les informations d’identification du coffre. Vous utilisez les informations d’identification du coffre lors de l’inscription d’Azure Backup Server dans le coffre Recovery Services. Les liens vous dirigent vers le Centre de téléchargement à partir duquel le package logiciel peut être téléchargé.

    ![Préparer l’infrastructure pour Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Sélectionnez tous les fichiers, puis cliquez sur **Suivant**. Téléchargez tous les fichiers en provenance de la page de téléchargement Microsoft Azure Backup et placez tous les fichiers dans le même dossier.

    ![Centre de téléchargement 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Puisque la taille de téléchargement de l’ensemble des fichiers est supérieure à 3Go, sur un lien de téléchargement de 10 Mbits/s, le téléchargement peut prendre jusqu’à 60 minutes.

### <a name="extracting-the-software-package"></a>Extraction du package logiciel

Une fois que vous avez téléchargé tous les fichiers, cliquez sur **MicrosoftAzureBackupInstaller.exe**. **L’Assistant Installation de Microsoft Azure Backup** démarre l’extraction des fichiers d’installation vers l’emplacement que vous avez spécifié. Suivez les instructions de l’Assistant et cliquez sur le bouton **Extraire** pour commencer le processus d’extraction.

> [!WARNING]
> Au moins 4 Go d’espace libre sont nécessaires pour l’extraction des fichiers d’installation.
>
>

![L’Assistant Installation de Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Une fois le processus d’extraction terminé, cochez la case pour exécuter le fichier *setup.exe* que vous venez d’extraire pour installer Microsoft Azure Backup Server, puis cliquez sur le bouton **Terminer** .

### <a name="installing-the-software-package"></a>Installation du package logiciel

1. Cliquez sur **Microsoft Azure Backup** pour lancer l’Assistant d’installation.

    ![L’Assistant Installation de Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Dans l’écran d’accueil, cliquez sur le bouton **Suivant**. Vous êtes redirigé vers la section *Vérification des conditions préalables* . Dans cet écran, cliquez sur **Vérifier** afin de déterminer si les configurations matérielle et logicielle requises pour serveur de sauvegarde Azure sont respectées. Si toutes les conditions préalables sont réunies, un message indiquant que la machine présente la configuration requise s’affiche. Cliquez sur le bouton **Suivant** .

    ![Azure Backup Server - Accueil et contrôle des conditions préalables requises](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Le package d’installation du serveur de sauvegarde Azure fait partie d’un bundle. Il est fourni avec les fichiers binaires SQL Server appropriés requis. Quand vous démarrez une nouvelle installation du serveur de sauvegarde Azure, sélectionnez l’option **Installer une nouvelle instance de SQL Server avec ce programme d’installation** et cliquez sur le bouton **Vérifier et installer**. Une fois les composants requis installés, cliquez sur **Suivant**.

    >[!NOTE]
    >Si vous souhaitez utiliser votre propre serveur SQL, les versions de SQL Server prises en charge sont SQL Server 2014 SP1 ou ultérieur, 2016 et 2017.  Toutes les versions de SQL Server doivent être Standard ou Entreprise 64 bits.
    >Azure Backup Server ne fonctionne pas avec une instance de serveur SQL distante. L’instance utilisée par le serveur de sauvegarde Azure doit être installée en local. Si vous utilisez un serveur SQL existant pour MABS, la configuration MABS prend uniquement en charge l’utilisation d’*instances nommées* du serveur SQL.

    ![Serveur de sauvegarde Azure - Vérification SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Si une défaillance se produit et qu’il est conseillé de redémarrer la machine, faites-le, puis cliquez sur **Vérifier à nouveau**. En cas de problèmes de configuration de SQL, reconfigurez SQL conformément aux instructions SQL et réessayez d’installer/de mettre à niveau MABS à l’aide de l’instance existante de SQL.

   **Configuration manuelle**

   Lorsque vous utilisez votre propre instance de SQL, veillez à ajouter builtin\Administrators au rôle d’administrateur système pour la base de données maître.

    **Configuration de SSRS avec SQL 2017**

    Quand vous utilisez votre propre instance de SQL 2017, vous devez configurer manuellement SSRS. Après la configuration de SSRS, vérifiez que la propriété *IsInitialized* de SSRS est définie sur *True*. Lorsqu’elle est définie sur True, MABS suppose que SSRS est déjà configuré et ignorera la configuration de SSRS.

    Utilisez les valeurs suivantes pour la configuration SSRS :
    * Compte de service : « Utiliser un compte intégré » doit être Service réseau
    * URL du service web : « Répertoire virtuel » doit être ReportServer_\<NomInstanceSQL>
    * Base de données : « Nom de la base de données » doit être ReportServer$\<NomInstanceSQL>
    * URL du portail web : « Répertoire virtuel » doit être Reports_\<NomInstanceSQL>

    [En savoir plus](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) sur la configuration de SSRS.

    > [!NOTE]
    > La gestion des licences pour SQL Server utilisé comme base de données pour MABS est régie par les [conditions de services en ligne Microsoft](https://www.microsoft.com/licensing/product-licensing/products) (OST). D’après OST, SQL Server fourni avec MABS peut être utilisé uniquement comme base de données pour MABS.

4. Fournissez un emplacement pour l’installation des fichiers de serveur Microsoft Azure Backup, puis cliquez sur **Suivant**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Un emplacement temporaire est requis pour la sauvegarde sur Azure. Vérifiez que l’emplacement temporaire correspond à au moins 5 % du volume qu’il est prévu de sauvegarder dans le cloud. Pour la protection de disque, des disques séparés doivent être séparés une fois l’installation terminée. Pour plus d’informations sur les pools de stockage, consultez [Configurer des pools de stockage et de stockage sur disque](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)).
5. Fournissez un mot de passe fort pour les comptes utilisateur locaux restreints et cliquez sur **Suivant**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Indiquez si vous souhaitez utiliser *Microsoft Update* pour vérifier les mises à jour et cliquez sur **Suivant**.

   > [!NOTE]
   > Nous conseillons de faire en sorte que Windows Update soit redirigé vers Microsoft Update, qui offre une sécurité et des mises à jour importantes pour Windows et autres produits, tels que le serveur Microsoft Azure Backup.
   >
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Examinez le *Résumé des paramètres* et cliquez sur **Installer**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. L’installation se déroule en plusieurs phases. Lors de la première phase, l’Agent Microsoft Azure Recovery Services est installé sur le serveur. L’Assistant vérifie également la connectivité à Internet. Si la connectivité à Internet est disponible, vous pouvez poursuivre l’installation, sinon, vous devez fournir les informations de proxy détaillées pour se connecter à Internet.

    L’étape suivante consiste à configurer l’Agent Microsoft Azure Recovery Services. Dans le cadre de la configuration, vous devrez fournir les informations d’identification de coffre pour inscrire l’ordinateur dans le coffre Recovery Services. Vous allez également fournir une phrase secrète pour chiffrer/déchiffrer les données circulant entre Azure et votre environnement local. Vous pouvez automatiquement générer une phrase secrète ou fournir votre propre phrase secrète d’au minimum 16 caractères. Continuez avec l’Assistant jusqu’à ce que l’agent soit configuré.

    ![Azure Backup Server PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Une fois l’inscription du serveur Microsoft Azure Backup terminée et réussie, l’Assistant général d’installation procède à l’installation et à la configuration de SQL Server et des composants Azure Backup Server. Une fois l’installation du composant SQL Server terminée, les composants Azure Backup Server sont installés.

    ![Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Lorsque l’étape d’installation est terminée, les icônes du produit sur le bureau sont en principe créées elles aussi. Double-cliquez sur l’icône pour lancer le produit.

### <a name="add-backup-storage"></a>Ajouter de l’espace de stockage pour la sauvegarde

La première copie de sauvegarde est conservée sur l’espace de stockage associé à l’ordinateur du serveur de sauvegarde Azure. Pour plus d’informations sur l’ajout de disques, consultez la section [Configurer des pools de stockage et un disque de stockage](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

> [!NOTE]
> Vous devez ajouter un stockage de sauvegarde même si vous prévoyez d’envoyer des données à Azure. Dans l’architecture actuelle d’Azure Backup Server, le coffre Azure Backup conserve la *deuxième* copie des données, alors que le stockage local conserve la première copie (obligatoire).
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Installer et mettre à jour l’agent de protection Data Protection Manager

MABS utilise l’agent de protection System Center Data Protection Manager. [Voici les étapes](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) à suivre pour installer l’agent de protection sur vos serveurs de protection.

Les sections suivantes décrivent comment mettre à jour des agents de protection pour les ordinateurs clients.

1. Dans la console Administrateur du serveur de sauvegarde, sélectionnez **Gestion** > **Agents**.

2. Dans le volet d’affichage, sélectionnez les ordinateurs clients dont vous souhaitez mettre à jour l’agent de protection.

   > [!NOTE]
   > La colonne **Mises à jour d’agent** indique lorsqu’une mise à jour de l’agent de protection est disponible pour chaque ordinateur protégé. Dans le volet **Actions**, l’action **Mettre à jour** n’est proposée que lorsqu’un ordinateur protégé est sélectionné et que des mises à jour sont disponibles.
   >
   >

3. Pour installer les agents de protection à jour sur les ordinateurs sélectionnés, dans le volet **Actions**, sélectionnez **Mettre à jour**.

4. Pour un ordinateur client qui n’est pas connecté au réseau, la colonne **État de l’agent** indique **Mise à jour en attente** tant que l’ordinateur n’est pas connecté au réseau.

   Une fois qu’un ordinateur client est connecté au réseau, la colonne **Mises à jour d’agent** de cet ordinateur indique l’état **Mise à jour**.

## <a name="move-mabs-to-a-new-server"></a>Déplacer MABS vers un nouveau serveur

Voici les étapes à suivre si vous devez déplacer MABS vers un nouveau serveur tout en conservant le stockage. C’est possible uniquement si toutes les données sont sur MBS.

  > [!IMPORTANT]
  >
  > * Le nom du nouveau serveur doit être le même que celui de l’instance du serveur Sauvegarde Azure d’origine. Vous ne pouvez pas changer le nom de la nouvelle instance du serveur de sauvegarde Azure si vous souhaitez utiliser le pool de stockage précédent et la base de données MABS (DPMDB) pour conserver les points de récupération.
  > * Vous devez disposer d’une sauvegarde de la base de données MABS (DPMDB). Vous devez restaurer la base de données.

1. Dans le volet d’affichage, sélectionnez les ordinateurs clients dont vous souhaitez mettre à jour l’agent de protection.
2. Arrêtez le serveur de sauvegarde Azure d’origine ou mettez-le hors connexion.
3. Réinitialisez le compte de la machine dans Active Directory.
4. Installez Server 2016 sur une nouvelle machine et attribuez à celle-ci le même nom que le serveur de sauvegarde Azure d’origine.
5. Joignez le domaine.
6. Installez Azure Backup Server V3 ou version ultérieure (déplacez les disques du pool de stockage de MABS à partir de l’ancien serveur et effectuez l’importation).
7. Restaurez la base de données DPM de l’étape 1.
8. Attachez le stockage à partir du serveur de sauvegarde d’origine au nouveau serveur.
9. Depuis SQL, restaurez la base de données DPM.
10. À partir de la ligne de commande d’administration sur le nouveau serveur, accédez au répertoire d’installation de Sauvegarde Microsoft Azure, puis au dossier bin.

    Exemple de chemin : C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"

11. Vers la sauvegarde Azure, exécutez DPMSYNC -SYNC

    Si vous avez ajouté de nouveaux disques au pool de stockage DPM au lieu de déplacer les anciens, exécutez DPMSYNC -Reallocatereplica

## <a name="network-connectivity"></a>Connectivité réseau

Pour que le produit fonctionne correctement, Azure Backup Server exige la connectivité au service Azure Backup. Pour vérifier que la machine est bien connectée à Azure, utilisez l’applet de commande ```Get-DPMCloudConnection``` dans la console Azure Backup Server PowerShell. Si le résultat de la cmdlet est TRUE, la connexion existe. Dans le cas contraire, elle n’existe pas.

Dans le même temps, l’abonnement Azure doit être dans un état correct. Pour déterminer l’état de votre abonnement et le gérer, connectez-vous au [portail d’abonnement](https://account.windowsazure.com/Subscriptions).

Une fois que vous connaissez l’état de la connectivité d’Azure et de l’abonnement Azure, vous pouvez utiliser le tableau ci-dessous pour déterminer l’impact sur la fonctionnalité de sauvegarde/restauration proposée.

| État de la connectivité | Abonnement Azure | Sauvegarde sur Azure | Sauvegarde sur disque | Restauration à partir d’Azure | Restauration à partir d’un disque |
| --- | --- | --- | --- | --- | --- |
| Connecté |Actif |Autorisé |Autorisé |Autorisé |Autorisé |
| Connecté |Expiré |Arrêté |Arrêté |Autorisé |Autorisé |
| Connecté |Approvisionnement annulé |Arrêté |Arrêté |Arrêté et points de restauration Azure supprimés |Arrêté |
| Connectivité perdue depuis > 15 jours |Actif |Arrêté |Arrêté |Autorisé |Autorisé |
| Connectivité perdue depuis > 15 jours |Expiré |Arrêté |Arrêté |Autorisé |Autorisé |
| Connectivité perdue depuis > 15 jours |Approvisionnement annulé |Arrêté |Arrêté |Arrêté et points de restauration Azure supprimés |Arrêté |

### <a name="recovering-from-loss-of-connectivity"></a>Récupération après la perte de connectivité

Si vous êtes équipé d’un pare-feu ou d’un proxy qui empêche l’accès à Azure, vous devez autoriser les adresses de domaine suivantes dans le profil de pare-feu/proxy :

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Si vous utilisez le peering Microsoft ExpressRoute, sélectionnez les services/régions suivants :

* Azure Active Directory (12076:5060)
* Région Microsoft Azure (en fonction de l’emplacement de votre coffre Recovery Services)
* Stockage Azure (en fonction de l’emplacement de votre coffre Recovery Services)

Pour plus d’informations, visitez [Configuration requise pour le routage ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

Une fois la connectivité à Azure restaurée sur l’ordinateur Azure Backup Server, les opérations qui peuvent être exécutées sont déterminées par l’état de l’abonnement Azure. Le tableau ci-dessus comporte les détails des opérations autorisées une fois l’ordinateur « connecté ».

### <a name="handling-subscription-states"></a>Gestion des états d’abonnement

Il est possible de faire passer un abonnement Azure de l’état *Expiré* ou *Approvisionnement annulé* à l’état *Actif*. Cependant, cette opération a certaines conséquences sur le comportement du produit quand l’état n’est pas *Actif* :

* Un abonnement dont *l’approvisionnement est annulé* ne fonctionne pas pendant la période pour laquelle l’approvisionnement est annulé. En redevenant *Actif*, la fonctionnalité de sauvegarde/restauration du produit est rétablie. Les données de sauvegarde présentes sur le disque local peuvent également être récupérées si le délai de rétention est suffisant. Toutefois, les données de sauvegarde dans Azure sont irrémédiablement perdues une fois que l’abonnement passe à l’état *Approvisionnement annulé* .
* Un abonnement *Expiré* ne fonctionne plus tant qu’il n’a pas été *réactivé*. Lorsque l’abonnement est à l’état *Expiré* , les sauvegardes planifiées ne sont pas exécutées.

## <a name="upgrade-mabs"></a>Mettre à niveau MABS

Utilisez les procédures suivantes pour mettre à niveau MABS.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Mettre à niveau de MABS V2 vers MABS V3

> [!NOTE]
>
> MABS V2 n’est pas une condition préalable à l’installation de MABS V3. Toutefois, vous ne pouvez mettre à niveau vers MABS V3 qu’à partir de MABS V2.

Procédez comme suit pour mettre à niveau MABS :

1. Pour mettre à niveau de MABS V2 vers MABS V3, mettez à niveau votre système d’exploitation vers Windows Server 2016 ou Windows Server 2019 si nécessaire.

2. Mettre à niveau votre serveur. La procédure est similaire à celle de l’[installation](#install-and-upgrade-azure-backup-server). Toutefois, pour les paramètres de SQL, vous aurez le choix entre deux options : mettre à niveau votre instance SQL vers SQL 2017, ou utiliser votre propre instance SQL server 2017.

   > [!NOTE]
   >
   > Ne quittez pas pendant la mise à niveau de votre instance SQL, car ceci désinstallera l’instance de création de rapports SQL et, par conséquent, une tentative de nouvelle mise à niveau de MABS échouera.

   > [!IMPORTANT]
   >
   >  Dans le cadre de la mise à niveau de SQL 2017, nous sauvegardons les clés de chiffrement SQL et désinstallons les services de création de rapports. Après la mise à niveau du serveur SQL, le service de création de rapports (14.0.6827.4788) est installé et les clés de chiffrement sont restaurées.
   >
   > Lorsque vous configurez manuellement SQL 2017, reportez-vous à la section *configuration de SSRS avec SQL 2017* sous les instructions d’installation.

3. Mettez à jour les agents de protection sur les serveurs protégés.
4. Les sauvegardes doivent continuer sans qu’il soit nécessaire de redémarrer vos serveurs de production.
5. Vous pouvez commencer à protéger vos données dès maintenant. Si vous mettez à niveau vers le stockage de sauvegarde moderne, tout en protégeant les données, vous pouvez également choisir les volumes dans lesquels vous souhaitez stocker les sauvegardes et vérifier s'il n'y a pas d'espace sous-approvisionné. [Plus d’informations](backup-mabs-add-storage.md)

## <a name="troubleshooting"></a>Dépannage

Si le serveur de sauvegarde Microsoft Azure échoue avec des erreurs pendant la phase d’installation (ou de sauvegarde ou restauration), reportez-vous à ce [document de codes d’erreur](https://support.microsoft.com/kb/3041338) pour plus d’informations.
Vous pouvez également vous reporter au [FAQ relatives à la sauvegarde Azure](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez obtenir dans ce [document](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-2019) des informations détaillées sur la préparation de votre environnement pour DPM. Ce dernier contient également des informations relatives aux configurations prises en charge sur lesquelles Azure Backup Server peut être déployé et utilisé. Vous pouvez utiliser une série d’[applet de commande PowerShell](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) pour effectuer diverses opérations.

Vous pouvez utiliser ces articles pour apprendre à mieux connaître la notion de protection de charge de travail à l’aide du serveur Microsoft Azure Backup.

* [Sauvegarde SQL Server](backup-azure-backup-sql.md)
* [Sauvegarde de serveur SharePoint](backup-azure-backup-sharepoint.md)
* [Sauvegarde sur un autre serveur](backup-azure-alternate-dpm-server.md)
