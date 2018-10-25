---
title: Utiliser DPM pour sauvegarder des charges de travail dans Azure
description: Introduction à la sauvegarde de données DPM dans un coffre Azure Recovery Services.
services: backup
author: adigan
manager: nkolli
keywords: System Center Data Protection Manager, Data Protection Manager, sauvegarde DPM
ms.service: backup
ms.topic: conceptual
ms.date: 8/22/2018
ms.author: adigan
ms.openlocfilehash: 2da5b04f56a5746fb77de6bc954bb5971eb4664b
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885168"
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Préparation de la sauvegarde des charges de travail dans Azure avec DPM
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

Cet article explique comment sauvegarder des données System Center Data Protection Manager (DPM) dans Azure, notamment :

* Comment sauvegarder des données de serveur DPM dans Azure
* les conditions requises pour une expérience de sauvegarde fluide ;
* les erreurs classiques rencontrées et comment les gérer ;
* Scénarios pris en charge

> [!NOTE]
> Azure dispose de deux modèles de déploiement pour créer et utiliser des ressources : [Resource Manager et Classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article fournit les informations et les procédures relatives à la restauration des machines virtuelles déployées à l’aide du modèle Resource Manager.
>
>

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) sauvegarde les données des fichiers et des applications. Vous trouverez plus d’informations sur les charges de travail prises en charge [ici](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix). Les données sauvegardées dans DPM peuvent être stockées sur bande, sur disque, ou sauvegardées dans Azure avec Microsoft Azure Backup. DPM interagit avec Azure Backup comme suit :

* **DPM déployé comme un serveur physique ou une machine virtuelle locale** : une instance DPM déployée comme un serveur physique ou comme une machine virtuelle Hyper-V locale sauvegarde les données dans un coffre Recovery Services, en plus d’une sauvegarde sur disque et sur bande.
* **DPM déployé comme une machine virtuelle Azure** : à compter de System Center 2012 R2 avec Update 3, vous pouvez déployer DPM comme une machine virtuelle Azure. Si DPM est déployé comme une machine virtuelle Azure, vous pouvez sauvegarder les données sur des disques Azure attachés à la machine virtuelle ou décharger le stockage de données en effectuant une sauvegarde dans un coffre Recovery Services.

## <a name="why-back-up-dpm-to-azure"></a>Pourquoi sauvegarder DPM sur Azure ?
La sauvegarde de serveurs DPM dans Azure présente les avantages commerciaux suivants :

* Pour le déploiement local de DPM, utilisez Azure au lieu d’un déploiement à long terme sur bande.
* Pour le déploiement de DPM sur une machine virtuelle dans Azure, déchargez le stockage du disque Azure. Le stockage des données plus anciennes dans votre coffre Recovery Services vous permet de monter en puissance votre activité en stockant les nouvelles données sur un disque.

## <a name="prerequisites"></a>Prérequis
Préparer Azure Backup pour sauvegarder des données DPM comme suit :

1. **Créer un coffre Recovery Services** : créez un coffre dans le portail Azure.
2. **Télécharger les informations d’identification du coffre** : téléchargez les informations d’identification que vous utilisez pour inscrire le serveur DPM auprès du coffre Recovery Services.
3. **Installer l’agent Azure Backup** : installez l’agent sur chaque serveur DPM.
4. **Inscrire le serveur** : inscrivez le serveur DPM auprès du coffre Recovery Services.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="key-definitions"></a>Définitions clés
Voici quelques définitions clés concernant la sauvegarde vers Azure pour DPM :

1. **Informations d’identification de coffre** : les informations d’identification de coffre sont nécessaires afin d’authentifier l’ordinateur pour envoyer des données de sauvegarde dans un coffre identifié dans le service Sauvegarde Azure. Elles peuvent être téléchargées à partir du coffre et sont valides pendant 48 heures.
2. **Phrase secrète** : la phrase secrète est utilisée pour chiffrer les sauvegardes vers le cloud. Enregistrez le fichier dans un emplacement sécurisé, car il vous sera demandé pendant les opérations de récupération.
3. **Code PIN de sécurité** : si vous avez activé les [Paramètres de sécurité](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) du coffre, le code PIN de sécurité est nécessaire pour effectuer les opérations de sauvegarde critiques. Cette authentification multifacteur offre une couche de sécurité supplémentaire. 
4. **Dossier de récupération** : il s’agit de l’emplacement où les sauvegardes à partir du cloud sont temporairement stockées lors des restaurations de cloud. Sa taille doit être à peu près égale à celle des éléments de sauvegarde que vous souhaitez récupérer en parallèle.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="edit-storage-replication"></a>Modifier la réplication du stockage

La réplication du stockage vous permet de choisir entre le stockage géoredondant et le stockage localement redondant. Par défaut, votre archivage utilise un stockage géo-redondant. Si vous utilisez Azure comme sauvegarde principale, conservez l’option de stockage géoredondant. Si vous souhaitez une option plus économique, mais moins durable, suivez la procédure ci-dessous pour configurer le stockage localement redondant. Pour en savoir plus sur les options de stockage [géo-redondant](../storage/common/storage-redundancy-grs.md) et [localement redondant](../storage/common/storage-redundancy-lrs.md), consultez l’article [Réplication Stockage Azure](../storage/common/storage-redundancy.md).

Pour modifier le paramètre de réplication du stockage :

> [!NOTE] 
> Configurez la réplication du stockage avant de déclencher la sauvegarde initiale. Si vous décidez de modifier la configuration de la réplication du stockage après avoir sauvegardé l’élément protégé, vous devez arrêter la protection du coffre avant de procéder.
>  

1. Sélectionnez votre coffre et ouvrez son tableau de bord.

2. Dans la section **Gérer**, cliquez sur **Infrastructure de sauvegarde**.

3. Dans le menu **Configuration de la sauvegarde**, choisissez l’option de réplication de stockage pour votre coffre.

    ![Liste des archivages de sauvegarde](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

    Après avoir sélectionné l’option de stockage pour votre archivage, vous pouvez associer la machine virtuelle à l’archivage. Pour commencer l’association, vous devez découvrir et enregistrer les machines virtuelles Azure.

## <a name="download-vault-credentials"></a>Télécharger les informations d'identification de coffre
Le fichier d’informations d’identification de coffre est un certificat qui est généré par le portail pour chaque archivage de sauvegarde. Le portail télécharge ensuite la clé publique pour le Service de contrôle d’accès (ACS). Pendant l’inscription de la machine, la clé privée du certificat devient accessible à l’utilisateur, qui authentifie la machine. En fonction de l’authentification, le service Sauvegarde Azure envoie des données vers le coffre identifié.

Les informations d’identification de coffre sont utilisées uniquement pendant le flux de travail d’inscription. Il est de la responsabilité de l’utilisateur de s’assurer que le fichier d’informations d’identification du coffre n’est pas compromis. En cas de perte du contrôle des informations d’identification, les informations d’identification du coffre peuvent servir à inscrire d’autres machines auprès du coffre. Toutefois, les données de sauvegarde sont chiffrées à l’aide d’une phrase secrète qui appartient au client. De fait, les données de sauvegarde existantes ne peuvent pas être compromises. Pour atténuer ce problème, les informations d’identification de coffre expirent au bout de 48 heures. Téléchargez les nouvelles informations d’identification de coffre aussi souvent que nécessaire. Cependant, seul le dernier fichier de d’informations d’identification de coffre peut être utilisé pendant l’inscription.

Le fichier d’informations d’identification de coffre est téléchargé via un canal sécurisé depuis le portail Azure. Le service Sauvegarde Azure ne connaît pas la clé privée du certificat. De plus, cette clé n’est disponible ni dans le portail, ni dans le service. Procédez comme suit pour télécharger les informations d’identification de coffre sur un ordinateur local.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

2. Ouvrez le coffre Recovery Services que vous voulez inscrire auprès du serveur DPM.

3. Le menu Paramètres s’ouvre par défaut. S’il est fermé, cliquez sur **Paramètres** dans le tableau de bord du coffre pour ouvrir le menu. Dans le panneau **Paramètres**, cliquez sur **Propriétés**.

    ![Ouvrir le menu du coffre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. Dans la page Propriétés, sous **Informations d’identification de la sauvegarde**, cliquez sur **Télécharger**. Le portail génère le fichier d’informations d’identification de coffre que vous pouvez télécharger.

    ![Download](./media/backup-azure-dpm-introduction/vault-credentials.png)

Le portail génère des informations d’identification de coffre en combinant le nom du coffre et la date actuelle. Cliquez sur **Enregistrer** pour télécharger les informations d’identification de coffre dans le dossier de téléchargements du compte local, ou sélectionnez Enregistrer sous dans le menu Enregistrer pour spécifier un emplacement pour les informations d’identification du coffre. La création du fichier peut prendre jusqu’à une minute.

### <a name="note"></a>Remarque
* Vérifiez que le fichier des informations d’identification de coffre est enregistré dans un emplacement accessible à partir de votre ordinateur. Si elles sont stockées dans un partage de fichiers/SMB, vérifiez les autorisations d’accès.
* Le fichier d’informations d’identification de coffre est utilisé uniquement pendant le flux de travail d’inscription.
* Le fichier d’informations d’identification de coffre expire au bout de 48 heures et peut être téléchargé à partir du portail.

## <a name="install-backup-agent"></a>Installer l’agent de sauvegarde
Après avoir créé l’archivage de sauvegarde Azure, un agent doit être installé sur chacune de vos machines Windows (Windows Server, client Windows, System Center Data Protection Manager ou Azure Backup Server) pour permettre la sauvegarde des données et des applications dans Azure.

1. Ouvrez le coffre Recovery Services auprès duquel vous voulez inscrire l’ordinateur DPM.
2. Le menu Paramètres s’ouvre par défaut. S’il est fermé, cliquez sur **Paramètres** pour ouvrir le menu Paramètres. Dans le menu Paramètres, cliquez sur **Propriétés**.

    ![Ouvrir le menu du coffre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Dans la page Paramètres, cliquez sur **Télécharger** sous **Agent Azure Backup**.

    ![Download](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

   Une fois l’agent téléchargé, exécutez MARSAgentInstaller.exe pour lancer l’installation de l’agent Azure Backup. Choisissez le dossier d’installation et le dossier de travail requis pour l’agent. L’emplacement du cache spécifié doit avoir un espace libre équivalent à au moins 5 % du volume des données de sauvegarde.

4. Si vous utilisez un serveur proxy pour vous connecter à Internet, dans l’écran **Configuration du proxy** , entrez les détails du serveur proxy. Si vous utilisez un proxy authentifié, entrez les informations de nom d’utilisateur et mot de passe dans cet écran.

5. L’agent Azure Backup installe .NET Framework 4.5 et Windows PowerShell (s’il n’est pas déjà disponible) pour terminer l’installation.

6. Une fois l’agent installé, **fermez** la fenêtre.

   ![fermez](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

7. Pour **inscrire le serveur DPM** dans le coffre, sous l’onglet **Gestion**, cliquez sur **En ligne**. Sélectionnez ensuite **Inscription**. L’Assistant Inscription d’un programme d’installation s'ouvre.

8. Si vous utilisez un serveur proxy pour vous connecter à Internet, dans l’écran **Configuration du proxy** , entrez les détails du serveur proxy. Si vous utilisez un proxy authentifié, entrez les informations de nom d’utilisateur et mot de passe dans cet écran.

    ![Configuration du proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. Dans l’écran d’informations d’identification de l’archivage, recherchez et sélectionnez le fichier d’informations d’identification d’archivage téléchargé précédemment.

    ![Informations d’identification du coffre](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

    Le fichier d’informations d’identification d’archivage est valide uniquement pendant 48 heures (à partir de son téléchargement depuis le portail). Si vous rencontrez une erreur dans cet écran (par exemple, « Le fichier d’informations d’identification de coffre fourni a expiré »), connectez-vous au portail Azure et téléchargez de nouveau le fichier d’informations d’identification du coffre.

    Assurez-vous que le fichier d’informations d’identification d’archivage se trouve dans un emplacement accessible par l’application d’installation. Si vous rencontrez des erreurs liées à l’accès, copiez le fichier d’informations d’identification d’archivage dans un emplacement temporaire sur cet ordinateur et recommencez l’opération.

    Si vous rencontrez une erreur d’informations d’identification de coffre non valides (par exemple, « Les informations d’identification de coffre fournies ne sont pas valides »), cela signifie que le fichier est endommagé ou qu’il ne possède pas les dernières informations d’identification associées au service de récupération. Recommencez l’opération après avoir téléchargé un nouveau fichier d’informations d’identification de coffre à partir du portail. Cette erreur se produit généralement si l’utilisateur clique sur l’option **Télécharger les informations d’identification de coffre** trop rapidement dans le portail Azure. Dans ce cas, seul le deuxième fichier d’informations d’identification de coffre est valide.

10. Pour contrôler l’utilisation de la bande passante réseau pendant les heures de travail et les heures chômées, dans l'écran **Paramètre de limitation** , vous pouvez définir les limites d’utilisation de la bande passante ainsi que les heures de travail et les heures chômées.

    ![Paramètre de limitation](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

11. Dans l'écran **Paramètre de dossier de récupération** , recherchez le dossier où les fichiers téléchargés à partir d’Azure seront temporairement stockés.

    ![Paramètre de dossier de récupération](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

12. Sur l’écran **Paramètre de chiffrement** , vous pouvez générer ou fournir une phrase secrète (16 caractères minimum). Pensez à enregistrer le mot de passe dans un emplacement sécurisé.

    ![Chiffrement](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Si la phrase secrète est perdue ou oubliée, Microsoft ne peut pas vous aider à récupérer les données de sauvegarde. L’utilisateur final détient la phrase secrète de chiffrement et Microsoft n’a aucune visibilité sur cette dernière. Enregistrez le fichier dans un emplacement sécurisé, car il vous sera demandé pour les opérations de récupération.
    >
    >

13. Quand vous cliquez sur le bouton **Inscrire** , l’ordinateur est correctement inscrit dans l’archivage et vous êtes désormais prêt à démarrer la sauvegarde dans Microsoft Azure.

14. Lorsque vous utilisez Data Protection Manager, vous pouvez modifier les paramètres spécifiés pendant le flux de travail d’inscription en cliquant sur l’option **Configurer** et en sélectionnant **En ligne** sous l’onglet **Gestion**.

## <a name="requirements-and-limitations"></a>Spécifications (et limitations)
* DPM peut s'exécuter en tant que serveur physique ou machine virtuelle Hyper-V installée sur System Center 2012 SP1 ou System Center 2012 R2. DPM peut également s’exécuter en tant que machine virtuelle Azure sur System Center 2012 R2 avec au moins le correctif cumulatif 3 pour DPM 2012 R2, ou en tant que machine virtuelle Windows dans VMware sur System Center 2012 R2 avec au moins le correctif cumulatif 5.
* Si vous exécutez DPM avec System Center 2012 SP1, vous devez installer le correctif cumulatif 2 pour System Center Data Protection Manager SP1. Cette opération est nécessaire avant d'installer l'agent Azure Backup.
* Windows PowerShell et .Net Framework 4.5 doivent être installés sur le serveur DPM.
* DPM permet de sauvegarder la plupart des charges de travail dans Azure Backup. Pour une liste complète des éléments pris en charge par Azure Backup, consultez la liste ci-dessous.
* Des données stockées dans Azure Backup ne peuvent pas être récupérées avec l'option « Copie sur bande ».
* Vous devez posséder un compte Azure avec la fonctionnalité Azure Backup activée. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. En savoir plus sur la [tarification d'Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* L'utilisation d'Azure Backup nécessite l'installation de l'agent Azure Backup sur les serveurs que vous souhaitez sauvegarder. Chaque serveur doit disposer d'au moins 5 % de la taille des données en cours de sauvegarde en tant que stockage local libre. Par exemple, la sauvegarde de 100 Go de données nécessite un minimum de 5 Go d'espace libre dans l'emplacement temporaire.
* Les données seront stockées dans le coffre Azure. Il n'existe aucune limite à la quantité de données que vous pouvez sauvegarder dans un coffre Azure Backup, mais la taille d'une source de données (par exemple, une machine virtuelle ou une base de données) ne doit pas dépasser 54 400 Go.

Les types de fichiers suivants sont pris en charge pour une sauvegarde vers Azure :

* Chiffré (sauvegardes complètes uniquement)
* Compressé (sauvegardes incrémentielles prises en charge)
* Partiellement alloué (sauvegardes incrémentielles prises en charge)
* Compressé et partiellement alloué (traité comme partiellement alloué)

Et les types suivants ne sont pas pris en charge :

* Les serveurs sur des systèmes de fichiers respectant la casse ne sont pas pris en charge.
* Liens physiques (ignorés)
* Points d'analyse (ignorés)
* Chiffré et compressé (ignoré)
* Chiffré et partiellement alloué (ignoré)
* Flux compressé
* Flux partiellement alloué

> [!NOTE]
> Dans System Center 2012 DPM SP1 et versions ultérieures, vous pouvez sauvegarder des charges de travail protégées dans Azure.
>
>
