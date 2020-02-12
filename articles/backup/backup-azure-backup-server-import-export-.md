---
title: Sauvegarde hors connexion pour DPM et le serveur de sauvegarde Azure
description: Découvrez comment la Sauvegarde Azure vous permet d’envoyer des données en dehors du réseau à l’aide du service Azure Import/Export. Cet article explique le flux de travail de sauvegarde hors connexion pour DPM et serveur de sauvegarde Azure (MABS).
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 6be75062ab0ce06784d8cd7c833e0070476acf60
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022577"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Flux de travail de la sauvegarde hors connexion pour DPM et le serveur de sauvegarde Azure

La sauvegarde Azure offre plusieurs fonctionnalités intégrées pour réduire les coûts de stockage et de réseau pendant les sauvegardes complètes initiales des données dans Azure. Les sauvegardes complètes initiales transfèrent généralement de grandes quantités de données et requièrent davantage de bande passante, en comparaison avec les sauvegardes suivantes qui transfèrent uniquement les données deltas/incrémentielles. La sauvegarde Azure compresse les sauvegardes initiales. Via le processus d’amorçage hors connexion, la sauvegarde Azure peut utiliser des disques pour charger les données de sauvegarde initiale compressées hors connexion dans Azure.

Le processus d’amorçage hors connexion de la sauvegarde Azure est étroitement intégré au [service Azure Import/Export](../storage/common/storage-import-export-service.md) qui vous permet de transférer des données vers Azure à l’aide de disques. Si vous devez transférer des téraoctets (To) de données de sauvegarde initiale sur un réseau à latence élevée et à faible bande passante, vous pouvez utiliser le flux de travail d’amorçage hors connexion pour expédier la copie de sauvegarde initiale sur un ou plusieurs disques durs à un centre de données Azure. Cet article donne une vue d’ensemble des étapes requises pour effectuer ce flux de travail pour System Center DPM et le serveur de sauvegarde Azure.

> [!NOTE]
> Le processus de sauvegarde en mode hors connexion pour l’agent Microsoft Azure Recovery Services (MARS) est distinct de celui de System Center DPM et du serveur de sauvegarde Azure. Pour plus d’informations sur l’utilisation de la sauvegarde en mode hors connexion avec l’agent MARS, consultez [cet article](backup-azure-backup-import-export.md). La sauvegarde hors connexion n’est pas prise en charge pour les sauvegardes d’état du système effectuées à l’aide de l’agent Sauvegarde Azure.
>

## <a name="overview"></a>Vue d’ensemble

Grâce à la fonction d’amorçage hors connexion de la sauvegarde Azure et à Azure Import/Export, vous pouvez charger facilement les données dans Azure hors connexion à l’aide de disques. Le processus de sauvegarde en mode hors connexion inclut les étapes suivantes :

> [!div class="checklist"]
>
> * Les données de sauvegarde, au lieu d’être envoyées sur le réseau, sont écrites dans un *emplacement intermédiaire*
> * Les données figurant dans l’*emplacement intermédiaire* sont ensuite écrites sur un ou plusieurs disques SATA à l’aide de l’utilitaire *AzureOfflineBackupDiskPrep*
> * L’utilitaire crée automatiquement une tâche d’importation Azure
> * Les disques SATA sont ensuite envoyées au centre de données Azure le plus proche
> * Une fois les données de sauvegarde chargées dans Azure, la sauvegarde Azure copie les données de sauvegarde dans le coffre de sauvegarde, et des sauvegardes incrémentielles sont planifiées.

## <a name="supported-configurations"></a>Configurations prises en charge

La sauvegarde en mode hors connexion est prise en charge pour tous les modèles de déploiement de Sauvegarde Azure qui sauvegardent hors site des données locales sur le Microsoft Cloud. Elle inclut ce qui suit

> [!div class="checklist"]
>
> * Sauvegarde de fichiers et dossiers avec l’agent Microsoft Azure Recovery Services ou l’agent Sauvegarde Azure.
> * Sauvegarde de l’ensemble des charges de travail et fichiers avec Microsoft System Center Data Protection Manager
> * Sauvegarde de l’ensemble des charges de travail et fichiers avec Serveur Sauvegarde Microsoft Azure

## <a name="prerequisites"></a>Conditions préalables requises

Assurez-vous que les prérequis suivants sont disponibles avant de lancer le flux de travail de sauvegarde en mode hors connexion

* Un [coffre Recovery Services](backup-azure-recovery-services-vault-overview.md) a été créé. Pour en créer un, procédez de la manière décrite dans [cet article](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* L’agent Sauvegarde Azure, le serveur de sauvegarde Azure ou Microsoft System Center Data Protection Manager ont été installés sur un client Windows Server ou Windows, selon le besoin, et l’ordinateur est inscrit auprès du coffre Recovery Services. Assurez-vous que seule la [version la plus récente de Sauvegarde Azure](https://go.microsoft.com/fwlink/?linkid=229525) est utilisée.
* [Téléchargez le fichier des paramètres de publication Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) sur l’ordinateur à partir duquel vous prévoyez de sauvegarder vos données. L’abonnement à partir duquel vous téléchargez le fichier des paramètres de publication peut être différent de celui contenant le coffre Recovery Services. Si votre abonnement figure sur un cloud Azure souverain, utilisez les liens appropriés suivants pour télécharger le fichier de paramètres de publication Azure.

    | Région cloud souveraine | Lien du fichier des paramètres de publication Azure |
    | --- | --- |
    | États-Unis | [Lien](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Chine | [Lien](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Un compte de stockage Azure avec un modèle de déploiement *Resource Manager* a été créé dans l’abonnement à partir duquel vous avez téléchargé le fichier de paramètres de publication, comme indiqué ci-dessous :

  ![Création d’un compte de stockage avec développement Resource Manager](./media/backup-azure-backup-import-export/storage-account-resource-manager.png)

* Un emplacement intermédiaire est créé. Il peut s’agir d’un partage réseau ou de tout lecteur supplémentaire, interne ou externe, sur l’ordinateur offrant suffisamment d’espace disque pour conserver votre copie initiale. Par exemple, si vous tentez de sauvegarder un serveur de fichiers de 500 Go, assurez-vous que la zone intermédiaire dispose d’au moins 500 Go (bien qu’une quantité inférieure soit utilisée en raison de la compression).
* En ce qui concerne les disques qui seront envoyés à Azure, assurez-vous que seuls des disques durs internes SSD de 2,5 pouces ou SATA II/III de 2,5 ou 3,5 pouces sont utilisés. La capacité maximale par disque dur est de 10 To. Consultez la [documentation sur le service Azure Import/Export](../storage/common/storage-import-export-requirements.md#supported-hardware) pour connaître la dernière série de disques pris en charge par le service.
* Les disques SATA doivent être connectés à un ordinateur (appelé *ordinateur de copie*) à partir duquel est effectuée la copie des données de sauvegarde de l’*emplacement intermédiaire* vers les disques SATA. Vérifiez que BitLocker est activé sur l'*ordinateur de copie*

## <a name="prepare-the-server-for-the-offline-backup-process"></a>Préparer le serveur au processus de sauvegarde hors connexion

>[!NOTE]
> Si vous ne trouvez pas les utilitaires listés comme *AzureOfflineBackupCertGen.exe* dans votre installation de l’agent MARS, écrivez à AskAzureBackupTeam@microsoft.com pour pouvoir y accéder.

* Ouvrez une invite de commandes avec élévation de privilèges sur le serveur, puis exécutez la commande suivante :

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    L’outil crée une application AD de sauvegarde hors connexion Azure, si aucune n’existe déjà.

    Si une application existe déjà, cet exécutable vous invite à charger manuellement le certificat sur l’application dans le locataire. Suivez les étapes ci-dessous dans [cette section](#manually-upload-offline-backup-certificate) pour charger le certificat manuellement sur l’application.

* L’outil AzureOfflineBackup.exe génère un fichier OfflineApplicationParams.xml.  Copiez ce fichier sur le serveur avec MABS ou DPM.
* Installez l’[agent MARS le plus récent](https://aka.ms/azurebackup_agent) sur le serveur DPM/Sauvegarde Azure (MABS).
* Inscrivez le serveur sur Azure.
* Exécutez la commande suivante :

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname configured with Azure Data Box>
    ```

* La commande ci-dessus crée le fichier `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml`.

## <a name="manually-upload-offline-backup-certificate"></a>Charger manuellement le certificat de sauvegarde hors connexion

Suivez les étapes ci-dessous pour charger manuellement le certificat de sauvegarde hors connexion sur une application Azure Active Directory précédemment créée, destinée à la sauvegarde hors connexion.

1. Connectez-vous au portail Azure.
2. Accédez à **Azure Active Directory** > **Inscriptions des applications**.
3. Accédez à l’onglet **Applications détenues** et recherchez une application au format de nom d’affichage `AzureOfflineBackup _<Azure User Id` comme illustré ci-dessous :

    ![Localiser l’application sous l’onglet Applications détenues](./media/backup-azure-backup-import-export/owned-applications.png)

4. Cliquez sur l’application. Sous l’onglet **Gérer** dans le volet gauche, accédez à **Certificats et secrets**.
5. Vérifiez s’il existe déjà des certificats ou des clés publiques. S’il n’en existe pas, vous pouvez supprimer l’application en toute sécurité en cliquant sur le bouton **Supprimer** dans la page **Vue d’ensemble** de l’application. Ensuite, vous pouvez réessayer les étapes permettant de [préparer le serveur à la sauvegarde hors connexion](#prepare-the-server-for-the-offline-backup-process) et ignorer les étapes ci-dessous. Sinon, exécutez les étapes suivantes à partir du serveur DPM/serveur de sauvegarde Azure (MABS) sur lequel vous voulez configurer la sauvegarde hors connexion.
6. Ouvrez l’onglet **Gérer l’application de certificat d’ordinateur** > **Personnel** et recherchez le certificat portant le nom `CB_AzureADCertforOfflineSeeding_<ResourceId>`.
7. Sélectionnez le certificat ci-dessus, cliquez avec le bouton droit sur **Toutes les tâches** et **exportez**-le, sans clé privée, au format .cer.
8. Accédez à l’application de sauvegarde hors connexion Azure dans le portail Azure.
9. Cliquez sur **Gérer** > **Certificats et secrets** > **Charger un certificat**, puis chargez le certificat exporté à l’étape précédente.

    ![Téléchargement du certificat](./media/backup-azure-backup-import-export/upload-certificate.png)
10. Sur le serveur, ouvrez le Registre en tapant **regedit** dans la fenêtre Exécuter.
11. Accédez à l’entrée de Registre *Ordinateur\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*.
12. Cliquez avec le bouton droit sur **CloudBackupProvider** et ajoutez une nouvelle valeur de chaîne portant le nom `AzureADAppCertThumbprint_<Azure User Id>`.

    >[!NOTE]
    > Remarque : Pour trouver l’ID d’utilisateur Azure, effectuez l’une des étapes suivantes :
    >
    >1. À partir du PowerShell connecté à Azure, exécutez la commande `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as appears in the portal”`.
    >2. Accédez au chemin du Registre : `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;`

13. Cliquez avec le bouton droit sur la chaîne ajoutée à l’étape précédente, puis sélectionnez **Modifier**. Dans la valeur, fournissez l’empreinte du certificat que vous avez exporté à l’étape 7, puis cliquez sur **OK**.
14. Pour obtenir la valeur de l’empreinte, double-cliquez sur le certificat, sélectionnez l’onglet **Détails** et faites défiler le contenu jusqu’à ce que le champ de l’empreinte apparaisse. Cliquez sur **Empreinte** et copiez la valeur.

    ![Copier la valeur du champ de l’empreinte](./media/backup-azure-backup-import-export/thumbprint-field.png)

15. Passez à la section [Workflow](#workflow) pour reprendre le processus de sauvegarde hors connexion.

## <a name="workflow"></a>Workflow

Les informations de cette section vous permettent d’effectuer le flux de travail de sauvegarde hors connexion, afin que vos données puissent être remises à un centre de données Azure et chargées dans le stockage Azure. Si vous avez des questions sur le service Import ou sur un aspect du processus, consultez la documentation sur la [vue d’ensemble du service Import](../storage/common/storage-import-export-service.md) susmentionnée.

### <a name="initiate-offline-backup"></a>Lancer la sauvegarde hors connexion

1. Lorsque vous planifiez une sauvegarde, l’écran suivant s’affiche (dans Windows Server, le client Windows ou System Center Data Protection Manager).

    ![Écran d’importation](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Voici l’écran correspondant dans System Center Data Protection Manager : <br/>
    ![Écran d’importation de Microsoft System Center Data Protection Manager et du serveur de sauvegarde Azure](./media/backup-azure-backup-import-export/dpmoffline.png)

    Les champs sont décrits ci-dessous :

   * **Emplacement intermédiaire** : Emplacement de stockage temporaire dans lequel la copie de sauvegarde initiale est écrite. L’emplacement intermédiaire peut être sur un partage réseau ou un ordinateur local. Si l’ordinateur de copie et l’ordinateur source sont différents, nous vous recommandons de spécifier le chemin d’accès réseau complet de l’emplacement intermédiaire.
   * **Nom de la tâche d’importation Azure** : Nom unique utilisé par le service Azure Import et Sauvegarde Azure pour effectuer le suivi du transfert des données envoyées dans Azure à l’aide de disques.
   * **Paramètres de publication Azure** : Fournissez le chemin d’accès local au fichier de paramètres de publication.
   * **ID d’abonnement Azure** : ID de l'abonnement Azure à partir duquel vous avez téléchargé le fichier des paramètres de publication Azure.
   * **Compte de stockage Azure** : nom du compte de stockage de l'abonnement Azure associé au fichier des paramètres de publication Azure.
   * **Conteneur de stockage Azure** : nom de l'objet blob de stockage cible dans le compte de stockage Azure où les données de sauvegarde sont importées.

     Enregistrez l’*emplacement intermédiaire* et le *Nom de la tâche d’importation Azure* que vous avez fournis. Ces informations sont nécessaires pour la préparation des disques.  

2. Accomplissez le flux de travail, puis, pour lancer la copie de sauvegarde hors connexion, cliquez sur **Sauvegarder maintenant** dans la console de gestion de l’agent Sauvegarde Azure. La sauvegarde initiale est écrite dans la zone intermédiaire à cette étape.

    ![Sauvegarder maintenant](./media/backup-azure-backup-import-export/backupnow.png)

    Pour effectuer le flux de travail correspondant dans Microsoft System Center Data Protection Manager ou le serveur de sauvegarde Azure, cliquez avec le bouton droit sur le **groupe de protection**, puis sélectionnez l’option **Créer un point de récupération**. Sélectionnez ensuite l’option **Protection en ligne** .

    ![Microsoft System Center Data Protection Manager et le serveur de sauvegarde Azure sauvegardent à présent](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Une fois l’opération terminée, l’emplacement intermédiaire est prêt à être utilisé pour la préparation des disques.

    ![Progression de la sauvegarde](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Préparer des disques SATA et envoyer à Azure

L’utilitaire *AzureOfflineBackupDiskPrep* sert à préparer les disques SATA qui sont envoyés au centre de données Azure le plus proche. Cet utilitaire est disponible dans le répertoire d’installation de l’agent Recovery Services dont le chemin d’accès est le suivant :

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Accédez au répertoire, puis copiez le répertoire **AzureOfflineBackupDiskPrep** vers un ordinateur de copie auquel les disques SATA à préparer sont connectés. Vérifiez les points suivants concernant l’ordinateur de copie :

   * L’ordinateur de copie peut accéder à l’emplacement intermédiaire spécifié pour le flux de travail d’amorçage hors connexion via le même chemin d’accès réseau que celui fourni dans le flux de travail **Lancer la sauvegarde hors connexion** .
   * BitLocker est activé sur l’ordinateur de copie.
   * L’ordinateur de copie peut accéder au portail Azure.

     Si nécessaire, l’ordinateur de copie peut être le même que l’ordinateur source.

     > [!IMPORTANT]
     > Si l’ordinateur source est une machine virtuelle, il est obligatoire d’utiliser un autre serveur physique ou un ordinateur client en tant qu’ordinateur de copie.

2. Ouvrez une invite de commande avec élévation de privilèges sur l’ordinateur de copie en utilisant le répertoire de l’utilitaire *AzureOfflineBackupDiskPrep* comme répertoire actuel, puis exécutez la commande suivante :

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Paramètre | Description |
    | --- | --- |
    | s:&lt;*Staging Location Path*&gt; |Entrée obligatoire utilisée pour fournir le chemin d’accès vers l’emplacement intermédiaire que vous avez saisi lors du flux de travail **Lancer la sauvegarde hors connexion** . |
    | p:&lt;*Path to PublishSettingsFile*&gt; |Entrée optionnelle utilisée pour fournir le chemin d’accès vers le fichier de **paramètres de publication Azure** que vous avez saisi lors du flux de travail **Lancer la sauvegarde hors connexion**. |

    > [!NOTE]
    > La valeur &lt;Chemin d’accès à AzurePublishSettingFile&gt; est obligatoire quand l’ordinateur de copie et l’ordinateur source sont différents.
    >
    >

    Quand vous exécutez la commande, l’utilitaire vous demande de sélectionner la tâche d’importation Azure correspondant aux disques à préparer. S’il existe un seul travail d’importation associé à l’emplacement intermédiaire spécifié, un écran similaire à l’écran suivant s’affiche.

    ![Entrée d’outil de préparation des disques Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

3. Entrez la lettre du disque, sans les deux-points de fin, correspondant au disque monté que vous souhaitez préparer pour le transfert vers Azure. Confirmez le formatage du disque lorsque vous y êtes invité.

    L’outil commence ensuite à préparer le disque et à copier les données de sauvegarde. Vous devrez peut-être ajouter des disques supplémentaires lorsque vous y êtes invité par l’outil, au cas où le disque fourni n’aurait pas suffisamment d’espace pour les données de sauvegarde. <br/>

    À la fin de l’exécution correcte de l’outil, un ou plusieurs disques que vous avez fournis sont préparés pour l’expédition à Azure. En outre, une tâche d’importation du nom que vous avez fourni lors du flux de travail de **lancement de la sauvegarde hors connexion** est créée dans Azure. Enfin, l’outil affiche l’adresse d’expédition du centre de données Azure auquel les disques doivent être envoyés.

    ![Préparation des disques Azure terminée](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. À la fin de l’exécution de la commande, vous voyez également s’afficher l’option permettant de mettre à jour les informations d’expédition comme illustré ci-dessous :

    ![Option de mise à jour des informations d’expédition](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. Vous pouvez entrer les détails immédiatement. L’outil vous guide tout au long du processus impliquant une série d’entrées. Toutefois, si vous n’avez pas d’informations telles que le numéro de suivi ou d’autres détails concernant l’expédition, vous pouvez mettre fin à la session. La procédure de mise à jour des détails de l’expédition est décrite plus loin dans cet article.

6. Expédiez les disques à l’adresse indiquée par l’outil et conservez le numéro de suivi pour référence.

   > [!IMPORTANT]
   > Chaque tâche d’importation Azure doit avoir un numéro de suivi unique. Assurez-vous que les lecteurs préparés par l’utilitaire sous une tâche d’importation Azure sont expédiés ensemble dans un package unique, et qu’il existe un numéro de suivi unique pour ce package. Ne combinez pas dans un même package des lecteurs préparés dans le cadre de tâches d’importation Azure **différentes**.

7. Lorsque vous disposez des informations de numéro de suivi, accédez à l’ordinateur source qui attend l’achèvement de la tâche d’importation, puis exécutez la commande suivante dans une invite de commandes avec élévation de privilèges en utilisant le répertoire de l’utilitaire *AzureOfflineBackupDiskPrep* en tant que répertoire actif :

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Vous pouvez éventuellement exécuter la commande suivante à partir d’un autre ordinateur tel que l’*ordinateur de copie*, en utilisant le répertoire de l’utilitaire *AzureOfflineBackupDiskPrep* en tant que répertoire actif :

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Paramètre | Description |
    | --- | --- |
    | u: | Entrée obligatoire utilisée pour mettre à jour les détails de l’expédition d’une tâche d’importation Azure |
    | s:&lt;*Staging Location Path*&gt; | Entrée obligatoire si la commande n’est pas exécutée sur l’ordinateur source. Utilisée pour fournir le chemin d’accès à l’emplacement intermédiaire que vous avez entré durant le flux de travail de **déclenchement de sauvegarde en mode hors connexion**. |
    | p:&lt;*Path to PublishSettingsFile*&gt; | Entrée obligatoire si la commande n’est pas exécutée sur l’ordinateur source. Utilisée pour fournir le chemin d’accès au fichier de **paramètres de publication Azure** que vous avez entré durant le flux de travail de **déclenchement de sauvegarde en mode hors connexion**. |

    L’utilitaire détecte automatiquement la tâche d’importation que l’ordinateur source attend ou les tâches d’importation associées à l’emplacement intermédiaire lorsque la commande est exécutée sur un autre ordinateur. Il propose ensuite l’option de mise à jour des informations d’expédition au travers d’une série d’entrées comme indiqué ci-dessous :

    ![Entrée des informations d’expédition](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

8. Une fois toutes les informations entrées, révisez attentivement leurs détails et validez-les en tapant *Oui*.

    ![Réviser les informations d’expédition](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

9. Une fois la mise à jour des informations d’expédition effectuée, l’utilitaire fournit un emplacement local où stocker les informations d’expédition que vous avez entrées, comme indiqué ci-dessous

    ![Stockage des informations d’expédition](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > Assurez-vous que les lecteurs parviennent au centre de données Azure dans les deux semaines suivant la fourniture des informations d’expédition à l’aide de l’utilitaire *AzureOfflineBackupDiskPrep*. À défaut, les lecteurs pourraient ne pas être traités.  

Une fois les étapes ci-dessus accomplies, le centre de données Azure est prêt à recevoir les lecteurs et à les traiter afin de transférer les données de sauvegarde des lecteurs vers le compte de stockage Azure de type classique que vous avez créé.

### <a name="time-to-process-the-drives"></a>Temps de traitement des lecteurs

Le temps nécessaire au traitement d’une tâche d’importation Azure varie en fonction de différents facteurs, dont l’heure d’expédition, le type de travail, le type et la taille des données copiées, ainsi que la taille des disques fournis. Le service Azure Import/Export n’offre pas de contrat de niveau de service (SLA) mais, une fois les disques reçus, il s’efforce de copier les données de sauvegarde vers votre compte de stockage Azure dans les 7 à 10 jours. La section suivante décrit en détail comment surveiller l’état de la tâche d’importation Azure.

### <a name="monitoring-azure-import-job-status"></a>Surveillance de l’état de la tâche d’importation Azure

Quand vos lecteurs sont en transit ou se trouvent dans le centre de données Azure en vue de leurs copie vers le compte de stockage, l’agent Sauvegarde Azure, Microsoft System Center Data Protection Manager ou la console du serveur de sauvegarde Azure sur l’ordinateur source affichent l’état de la tâche suivant pour vos sauvegardes planifiées.

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Pour vérifier l’état de la tâche d’importation, procédez comme suit.

1. Ouvrez une invite de commandes avec élévation de privilèges sur l’ordinateur source, puis exécutez la commande suivante :

     `AzureOfflineBackupDiskPrep.exe u:`

2. La sortie indique l’état actuel de la tâche d’importation, comme ci-dessous :

    ![Vérification de l’état de la tâche d’importation](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Pour plus d’informations sur les différents états de la tâche d’importation Azure, voir [cet article](../storage/common/storage-import-export-view-drive-status.md)

### <a name="complete-the-workflow"></a>Terminer le flux de travail

Une fois le travail d’importation terminé, les données de sauvegarde initiale sont disponibles dans votre compte de stockage. Lors de la sauvegarde planifiée suivante, la sauvegarde Azure copie le contenu des données à partir du compte de stockage vers le coffre Recovery Services, comme ci-dessous :

   ![Copie de données vers le coffre Recovery Services](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Lors de la sauvegarde planifiée suivante, Sauvegarde Azure effectue une sauvegarde incrémentielle par-dessus la copie de sauvegarde initiale.

## <a name="next-steps"></a>Étapes suivantes

* Pour toute question au sujet du flux de travail Azure Import/Export, voir [Transfert de données vers le stockage d’objets blob à l’aide du service Microsoft Azure Import/Export](../storage/common/storage-import-export-service.md).
