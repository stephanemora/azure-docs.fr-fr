---
title: Amorcer la sauvegarde hors connexion avec le service Import/Export
description: Découvrez comment la sauvegarde Azure vous permet d’envoyer des données en dehors du réseau à l’aide du service Azure Import/Export. Cet article décrit l’amorçage hors connexion des données de sauvegarde initiale à l’aide du service Azure Import/Export.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 162d129eaea83ef6623daaa063e8a088c021e25d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022611"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Flux de travail de la sauvegarde hors connexion dans la sauvegarde Azure

La sauvegarde Azure offre plusieurs fonctionnalités intégrées pour réduire les coûts de stockage et de réseau pendant les sauvegardes complètes initiales des données dans Azure. Les sauvegardes complètes initiales transfèrent généralement de grandes quantités de données et requièrent davantage de bande passante, en comparaison avec les sauvegardes suivantes qui transfèrent uniquement les données deltas/incrémentielles. Via le processus d’amorçage hors connexion, la sauvegarde Azure peut utiliser des disques pour charger les données de sauvegarde hors connexion dans Azure.

Le processus d’amorçage hors connexion de la sauvegarde Azure est étroitement intégré au [service Azure Import/Export](../storage/common/storage-import-export-service.md) qui vous permet de transférer des données de sauvegarde initiale vers Azure à l’aide de disques. Si vous devez transférer des téraoctets (To) de données de sauvegarde initiale sur un réseau à latence élevée et à faible bande passante, vous pouvez utiliser le flux de travail d’amorçage hors connexion pour expédier la copie de sauvegarde initiale sur un ou plusieurs disques durs à un centre de données Azure. L’image suivante présente les étapes du flux de travail.

  ![présentation du processus de flux de travail d’importation hors connexion](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Le processus de sauvegarde hors connexion inclut les étapes suivantes :

1. Au lieu d’envoyer les données de sauvegarde sur le réseau, écrivez les données de sauvegarde dans un emplacement intermédiaire.
2. À l’aide de l’utilitaire AzureOfflineBackupDiskPrep, écrivez les données figurant dans l’emplacement intermédiaire sur un ou plusieurs disques SATA.
3. Au cours de l’étape de préparation, l’utilitaire AzureOfflineBackupDiskPrep crée une tâche d’importation Azure. Envoyez les disques durs SATA au centre de données Azure le plus proche et référencez la tâche d’importation pour connecter les activités.
4. Dans le centre de données Azure, les données stockées sur les disques sont copiées vers un compte de stockage Azure.
5. La sauvegarde Azure copie les données de sauvegarde à partir du compte de stockage vers le coffre Recovery Services et des sauvegardes incrémentielles sont planifiées.

## <a name="supported-configurations"></a>Configurations prises en charge

Les charges de travail ou fonctionnalités de sauvegarde Azure suivantes prennent en charge la sauvegarde hors connexion.

> [!div class="checklist"]
>
> * Sauvegarde de fichiers et dossiers avec l’agent Microsoft Azure Recovery Services, ou agent de sauvegarde Azure.
> * Sauvegarde de l’ensemble des charges de travail et fichiers avec Microsoft System Center Data Protection Manager
> * Sauvegarde de l’ensemble des charges de travail et fichiers avec Serveur Sauvegarde Microsoft Azure
 
   > [!NOTE]
   > La sauvegarde hors connexion n’est pas prise en charge pour les sauvegardes d’état du système effectuées à l’aide de l’agent Sauvegarde Azure.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Conditions préalables requises

  > [!NOTE]
  > Les prérequis et le flux de travail suivants s’appliquent uniquement à une sauvegarde hors connexion des fichiers et dossiers à l’aide de [la dernière version de l’agent Microsoft Azure Recovery Services](https://aka.ms/azurebackup_agent). Pour effectuer des sauvegardes hors connexion pour des charges de travail à l’aide de System Center DPM ou du serveur de sauvegarde Azure, reportez-vous à [cet article](backup-azure-backup-server-import-export-.md).

Avant de démarrer le flux de travail de sauvegarde hors connexion, assurez-vous que les prérequis suivants sont respectés :

* Créez un [coffre Recovery Services](backup-azure-recovery-services-vault-overview.md). Pour créer un coffre, suivez les étapes décrites dans [cet article](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Vérifiez que la [dernière version de l’agent de sauvegarde Azure](https://aka.ms/azurebackup_agent) a été installée sur le client Windows Server ou Windows, le cas échéant, et que l’ordinateur est inscrit auprès du coffre Recovery Services.
* Azure PowerShell version 3.7.0 doit être installé sur l’ordinateur qui exécute l’agent de sauvegarde Azure. Nous vous recommandons de télécharger et [d’installer la version 3.7.0 d’Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* Sur l’ordinateur exécutant l’agent de sauvegarde Azure, assurez-vous que Microsoft Edge ou Internet Explorer 11 est installé, et que JavaScript est activé.
* Créez un compte de stockage Azure dans le même abonnement que le coffre Recovery Services.
* Assurez-vous de disposer des [autorisations nécessaires](../active-directory/develop/howto-create-service-principal-portal.md) pour créer l’application Azure Active Directory. Le flux de travail de sauvegarde hors connexion crée une application Azure Active Directory dans l’abonnement associé au compte de stockage Azure. L’objectif de l’application est de fournir au service de sauvegarde Azure un accès sécurisé et délimité au service d’importation Azure qui est requis pour le flux de travail de sauvegarde hors connexion.
* Inscrivez le fournisseur de ressources Microsoft.ImportExport dans l’abonnement contenant le compte de stockage Azure. Pour inscrire le fournisseur de ressources :
    1. Dans le menu principal, cliquez sur **Abonnements**.
    2. Si vous disposez de plusieurs abonnements, sélectionnez l’abonnement que vous utilisez pour la sauvegarde hors connexion. Si vous n’utilisez qu’un seul abonnement, celui-ci s’affiche.
    3. Dans le menu de l’abonnement, cliquez sur **Fournisseurs de ressources** pour afficher la liste des fournisseurs.
    4. Faites défiler la liste des fournisseurs jusqu’à Microsoft.ImportExport. Si l’état est NotRegistered, cliquez sur **Inscrire**.
    ![inscription du fournisseur de ressources](./media/backup-azure-backup-import-export/registerimportexport.png)
* Un emplacement intermédiaire est créé. Il peut s’agir d’un partage réseau ou de tout lecteur supplémentaire, interne ou externe, sur l’ordinateur offrant suffisamment d’espace disque pour conserver votre copie initiale. Par exemple, si vous tentez de sauvegarder un serveur de fichiers de 500 Go, assurez-vous que la zone intermédiaire dispose d’au moins 500 Go (bien qu’une quantité inférieure soit utilisée en raison de la compression).
* Lors de l’envoi de disques dans Azure, utilisez uniquement des disques SSD de 2,5 pouces ou des disques durs internes SATA II/III de 2,5 ou 3,5 pouces. La capacité maximale par disque dur est de 10 To. Consultez la [documentation sur le service Azure Import/Export](../storage/common/storage-import-export-requirements.md#supported-hardware) pour connaître la dernière série de disques pris en charge par le service.
* Les disques SATA doivent être connectés à un ordinateur (appelé *ordinateur de copie*) à partir duquel est effectuée la copie des données de sauvegarde de *l’emplacement intermédiaire* vers les disques SATA. Vérifiez que BitLocker est activé sur l’*ordinateur de copie*.

## <a name="workflow"></a>Workflow

Cette section décrit le flux de travail de sauvegarde hors connexion qui permet à vos données d’être transmises à un centre de données Azure et chargées dans le stockage Azure. Si vous avez des questions sur le service d’importation ou sur tout autre aspect du processus, consultez l’article [Vue d’ensemble du service Import](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Lancer la sauvegarde hors connexion

1. Lorsque vous planifiez une sauvegarde sur l’agent MARS, l’écran suivant s’affiche.

    ![Écran d’importation](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

2. Sélectionnez l’option **Transfert à l’aide de mes propres disques**.

    >[!NOTE]
    >Nous vous recommandons d’utiliser l’option Azure Data Box pour transférer des données de sauvegarde initiales hors connexion. Cette option réduit les efforts nécessaires pour trouver vos propres disques compatibles avec Azure en vous livrant des appareils Azure Data Box, appartenant à Microsoft, sécurisés et inviolables, sur lesquels les données de sauvegarde peuvent être écrites directement par l’agent MARS.

3. Cliquez sur **Suivant** et renseignez les entrées avec soin :

    ![Entrer les détails de votre disque](./media/backup-azure-backup-import-export/your-disk-details.png)

   Les champs sont décrits ci-dessous :

    * **Emplacement intermédiaire** : Emplacement de stockage temporaire dans lequel la copie de sauvegarde initiale est écrite. L’emplacement intermédiaire peut être sur un partage réseau ou un ordinateur local. Si l’ordinateur de copie et l’ordinateur source sont différents, nous vous recommandons de spécifier le chemin d’accès réseau complet de l’emplacement intermédiaire.
    * **Compte de stockage Azure Resource Manager** : Nom du compte de stockage de type Resource Manager (usage général v1 ou usage général v2) dans un abonnement Azure quelconque.
    * **Conteneur de stockage Azure** : Nom de l’objet blob de stockage cible dans le compte de stockage Azure où les données de sauvegarde sont importées avant d’être copiées dans le coffre Recovery Services.
    * **ID d’abonnement Azure** : ID de l’abonnement Azure dans lequel le compte de stockage Azure est créé.
    * **Nom de la tâche d’importation Azure** : Nom unique utilisé par le service Azure Import et Sauvegarde Azure pour effectuer le suivi du transfert des données envoyées dans Azure à l’aide de disques.
  
   Renseignez les champs à l’écran et cliquez sur **Suivant**. Enregistrez *l’emplacement intermédiaire* fourni et le *Nom de la tâche d’importation Azure*. Ces informations sont nécessaires pour la préparation des disques.

4. Lorsque vous y êtes invité, connectez-vous à votre abonnement Azure. Vous devez vous connecter afin que le service de sauvegarde Azure puisse créer l’application Azure Active Directory et fournir les autorisations requises pour accéder au service d’importation Azure.

    ![Sauvegarder maintenant](./media/backup-azure-backup-import-export/azure-login.png)

5. Exécutez le flux de travail, puis, dans la console de l’agent de sauvegarde Azure, cliquez sur **Sauvegarder maintenant**.

    ![Sauvegarder maintenant](./media/backup-azure-backup-import-export/backupnow.png)

6. Dans la page de confirmation de l’Assistant, cliquez sur **Sauvegarder**. La sauvegarde initiale est écrite dans la zone intermédiaire tel que cela a été configuré.

   ![Confirmez que vous êtes prêt à sauvegarder maintenant](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Une fois l’opération terminée, l’emplacement intermédiaire est prêt à être utilisé pour la préparation des disques.

   ![Sauvegarder maintenant](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Préparer des disques SATA et envoyer à Azure

L’utilitaire *AzureOfflineBackupDiskPrep* prépare les disques SATA qui sont envoyés au centre de données Azure le plus proche. Cet utilitaire est disponible dans le répertoire d’installation de l’agent Recovery Services, dont le chemin d’accès est le suivant :

    *\Microsoft Azure Recovery Services Agent\Utils\\*

1. Accédez au répertoire, puis copiez le répertoire **AzureOfflineBackupDiskPrep** vers un autre ordinateur auquel les disques SATA sont connectés. Sur l’ordinateur auquel les disques SATA sont connectés, vérifiez les points suivants :

   * L’ordinateur de copie peut accéder à l’emplacement intermédiaire spécifié pour le flux de travail d’amorçage hors connexion via le même chemin d’accès réseau que celui fourni dans le flux de travail **Lancer la sauvegarde hors connexion** .
   * BitLocker est activé sur l’ordinateur de copie.
   * Azure PowerShell 3.7.0 est installé.
   * Les navigateurs compatibles les plus récents (Microsoft Edge ou Internet Explorer 11) sont installés et JavaScript est activé.
   * L’ordinateur de copie peut accéder au portail Azure. Si nécessaire, l’ordinateur de copie peut être le même que l’ordinateur source.

     > [!IMPORTANT]
     > Si l’ordinateur source est une machine virtuelle, vous devez utiliser un serveur physique ou un ordinateur client distinct comme ordinateur de copie.

2. Ouvrez une invite de commande avec élévation de privilèges sur l’ordinateur de copie en utilisant le répertoire de l’utilitaire *AzureOfflineBackupDiskPrep* comme répertoire actuel, puis exécutez la commande suivante :

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Paramètre | Description |
    | --- | --- |
    | s:&lt;*Staging Location Path*&gt; |Entrée obligatoire utilisée pour fournir le chemin d’accès vers l’emplacement intermédiaire que vous avez saisi lors du flux de travail **Lancer la sauvegarde hors connexion**. |
    | p:&lt;*Path to PublishSettingsFile*&gt; |Entrée optionnelle utilisée pour fournir le chemin d’accès vers le fichier de **paramètres de publication Azure** que vous avez saisi lors du flux de travail **Lancer la sauvegarde hors connexion**. |

    Quand vous exécutez la commande, l’utilitaire vous demande de sélectionner la tâche d’importation Azure correspondant aux disques à préparer. S’il existe un seul travail d’importation associé à l’emplacement intermédiaire spécifié, un écran similaire à l’écran suivant s’affiche.

    ![Entrée d’outil de préparation des disques Azure](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Entrez la lettre du disque, sans les deux-points de fin, correspondant au disque monté que vous souhaitez préparer pour le transfert vers Azure.
4. Confirmez le formatage du disque lorsque vous y êtes invité.
5. Vous êtes invité à vous connecter à votre abonnement Azure. Indiquez vos informations d’identification.

    ![Entrée d’outil de préparation des disques Azure](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    L’outil commence ensuite à préparer le disque et à copier les données de sauvegarde. Vous devrez peut-être ajouter des disques supplémentaires lorsque vous y êtes invité par l’outil, au cas où le disque fourni n’aurait pas suffisamment d’espace pour les données de sauvegarde. <br/>

    Une fois que l’outil a été correctement exécuté, l’invite de commandes fournit trois informations :
   1. Le ou les disques que vous avez fournis sont préparés en vue de leur envoi dans un centre de données Azure.
   2. Vous recevez la confirmation que votre tâche d’importation a été créée. La tâche d’importation utilise le nom que vous avez fourni.
   3. L’outil affiche l’adresse d’expédition correspondant au centre de données Azure.

      ![Préparation des disques Azure terminée](./media/backup-azure-backup-import-export/console2.png)<br/>

6. Une fois la commande exécutée, vous pouvez mettre à jour les informations d’expédition.

7. Expédiez les disques à l’adresse indiquée par l’outil et conservez le numéro de suivi pour référence.

   > [!IMPORTANT]
   > Chaque tâche d’importation Azure doit avoir un numéro de suivi unique. Assurez-vous que les disques préparés par l’utilitaire dans le cadre d’une tâche d’importation Azure sont expédiés ensemble dans un package unique, et qu’il existe un numéro de suivi unique pour ce package. Ne combinez pas dans un même package des disques préparés dans le cadre de tâches d’importation Azure distinctes.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Mettre à jour les informations d’expédition dans la tâche d’importation Azure

La procédure suivante met à jour les informations d’expédition de la tâche d’importation Azure. Ces informations incluent :

* le nom du transporteur qui livre les disques à Azure
* les détails de retour d’expédition pour vos disques

1. Connectez-vous à votre abonnement Azure.
2. Dans le menu principal, cliquez sur **Tous les services**, puis, dans la boîte de dialogue Tous les services, saisissez Importer. Cliquez sur **Tâches d’importation/exportation**.
    ![Saisie des informations d’expédition](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Le menu **Tâches d’importation/exportation** s’ouvre et affiche la liste de toutes les tâches d’importation/exportation associées à l’abonnement sélectionné.

3. Si vous avez plusieurs abonnements, veillez à sélectionner l’abonnement utilisé pour importer les données de sauvegarde. Sélectionnez ensuite la tâche d’importation que vous venez de créer pour en afficher les informations détaillées.

    ![Réviser les informations d’expédition](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. Dans le menu Paramètres de la tâche d’importation, cliquez sur **Manage Shipping Info** (Gérer les informations d’expédition) et saisissez les informations de retour d’expédition.

    ![Stockage des informations d’expédition](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. Une fois que votre transporteur vous a transmis le numéro de suivi, cliquez sur la bannière dans la page de la tâche d’importation Azure et entrez les informations suivantes :

   > [!IMPORTANT]
   > Assurez-vous que les informations relatives au transporteur et au numéro de suivi sont mises à jour dans un délai de deux semaines après la création de la tâche d’importation Azure. Si ces informations ne sont pas confirmées dans un délai de deux semaines, la tâche peut être annulée, annulant de ce fait le traitement des disques.

   ![Stockage des informations d’expédition](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Stockage des informations d’expédition](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Temps de traitement des lecteurs

Le temps nécessaire au traitement d’une tâche d’importation Azure varie en fonction de différents facteurs, dont l’heure d’expédition, le type de travail, le type et la taille des données copiées, ainsi que la taille des disques fournis. Le service Azure Import/Export n’offre pas de contrat de niveau de service (SLA) mais, une fois les disques reçus, il s’efforce de copier les données de sauvegarde vers votre compte de stockage Azure dans les 7 à 10 jours.

### <a name="monitoring-azure-import-job-status"></a>Surveillance de l’état de la tâche d’importation Azure

Vous pouvez suivre l’état de votre tâche d’importation à partir du portail Azure en accédant à la page **Tâches d’importation/exportation** et en sélectionnant votre tâche. Pour en savoir plus sur l’état des tâches d’importation, consultez l’article [Storage Import Export service](../storage/common/storage-import-export-service.md) (Service d’importation/exportation du stockage).

### <a name="complete-the-workflow"></a>Terminer le flux de travail

Une fois la tâche d’importation terminée, les données de sauvegarde initiale sont disponibles dans votre compte de stockage. Lors de la sauvegarde planifiée suivante, le service de sauvegarde Azure copie le contenu des données à partir du compte de stockage vers le coffre Recovery Services, comme illustré ci-dessous :

   ![Copie de données vers le coffre Recovery Services](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Lors de la sauvegarde planifiée suivante, le service de sauvegarde Azure effectue une sauvegarde incrémentielle.

### <a name="cleaning-up-resources"></a>Suppression des ressources

Une fois la sauvegarde initiale terminée, vous pouvez supprimer sans risque les données importées dans le conteneur de stockage Azure et les données de sauvegarde enregistrées dans l’emplacement intermédiaire.

## <a name="next-steps"></a>Étapes suivantes

* Pour toute question au sujet du flux de travail Azure Import/Export, voir [Transfert de données vers le stockage d’objets blob à l’aide du service Microsoft Azure Import/Export](../storage/common/storage-import-export-service.md).
