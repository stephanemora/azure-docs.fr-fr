---
title: Sauvegarde hors connexion avec Azure Data Box
description: Découvrez comment vous pouvez utiliser Azure Data Box pour amorcer des données de sauvegarde initiales volumineuses hors connexion à partir de l’agent MARS dans un coffre Azure Recovery Services.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 553939df8aa7a1b097b2cd6c7f29365302101324
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026493"
---
# <a name="azure-backup-offline-backup-using-azure-data-box"></a>Sauvegarde Azure hors connexion avec Azure Data Box

Vous pouvez utiliser le service [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) pour amorcer vos sauvegardes MARS initiales volumineuses hors connexion (sans utiliser de réseau) dans un coffre Azure Recovery Services.  Cela permet de gagner du temps et d’économiser de la bande passante réseau, qui serait sinon consommée pour déplacer de grandes quantités de données de sauvegarde en ligne sur un réseau à latence élevée. Cette amélioration est actuellement en préversion. La sauvegarde hors connexion basée sur Azure Data Box offre deux avantages distincts par rapport à la [sauvegarde hors connexion basée sur le service Azure Import/Export](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

1. Aucun besoin de fournir vos propres disques et connecteurs compatibles avec Azure. Le service Azure Data Box fournit les disques associés aux [références SKU Data Box](https://azure.microsoft.com/services/databox/data/) sélectionnés.

2. Le service Sauvegarde Azure (agent MARS) peut écrire directement des données de sauvegarde sur les références SKU prises en charge d’Azure Data Box. Le besoin de provisionner un emplacement intermédiaire pour vos données de sauvegarde initiales et le recours à des utilitaires pour formater et copier ces données sur les disques sont ainsi éliminés.

## <a name="azure-data-box-with-mars-agent"></a>Azure Data Box avec l’agent MARS

Cet article explique comment vous pouvez utiliser Azure Data Box pour amorcer des données de sauvegarde initiales volumineuses hors connexion à partir de l’agent MARS dans un coffre Azure Recovery Services. Il se divise en plusieurs parties :

* Plateformes prises en charge
* SKU Data Box pris en charge
* Conditions préalables
* Configurer l’agent MARS
* Configurer Azure Data Box
* Transfert des données de sauvegarde vers Azure Data Box
* Étapes à l’issue de la sauvegarde

## <a name="supported-platforms"></a>Plateformes prises en charge

Le processus d’amorçage des données à partir de l’agent MARS avec Azure Data Box est pris en charge sur les références SKU Windows suivantes :

| **SE**                                 | **Référence SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Station de travail**                        |                                                              |
| Windows 10 64 bits                     | Entreprise, Professionnel, Famille                                       |
| Windows 8.1 64 bits                    | Entreprise, Professionnel                                             |
| Windows 8 64 bits                      | Entreprise, Professionnel                                             |
| Windows 7 64 bits                      | Édition Intégrale, Entreprise, Professionnel, Édition Familiale Premium, Édition Familiale Basique, Édition Starter |
| **Serveur**                             |                                                              |
| Windows Server 2019 64 bits            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 64 bits            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 64 bits         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 64 bits            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016 64 bits    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2 64 bits | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012 64 bits    | Standard, Workgroup                                         |
| Windows Server 2008 R2 SP1 64 bits     | Standard, Entreprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64 bits        | Standard, Entreprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Taille des données de sauvegarde et SKU Data Box pris en charge

| Taille des données de sauvegarde (après compression par MARS)* par serveur | SKU Azure Data Box pris en charge                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <= 7,2 To                                                    | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7,2 To et < = 80 To**                                      | [Azure Data Box (100 To)](https://docs.microsoft.com/azure/databox/data-box-overview) |

\* Les taux de compression habituels varient entre 10 et 20 %. <br>
** Contactez [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) si vous prévoyez plus de 80 To de données de sauvegarde initiales pour un seul serveur MARS.

>[!IMPORTANT]
>Les données de sauvegarde initiales provenant d’un serveur unique doivent se trouver sur un seul appareil Azure Data Box ou Azure Data Box Disk. Il n’est pas possible de les partager entre plusieurs appareils de SKU identiques ou différents. En revanche, un appareil Azure Data Box peut contenir des sauvegardes initiales provenant de plusieurs serveurs.

## <a name="pre-requisites"></a>Conditions préalables

### <a name="azure-subscription-and-required-permissions"></a>Abonnement Azure et autorisations nécessaires

* Le processus nécessite un abonnement Azure.
* Le processus exige que l’utilisateur désigné pour exécuter la stratégie de sauvegarde hors connexion soit « propriétaire » de l’abonnement Azure.
* Le travail Data Box et le coffre Recovery Services (dans lequel les données doivent être amorcées) ont l’obligation de se trouver dans les mêmes abonnements.
* Il est recommandé de situer le compte de stockage cible associé au travail Azure Data Box et le coffre Recovery Services dans la même région. Toutefois, cela n’est pas nécessaire.

### <a name="get-azure-powershell-370"></a>Obtenir Azure PowerShell 3.7.0

**Il s’agit du prérequis le plus important pour le processus**. Avant d’installer Azure PowerShell (version 3.7.0), effectuez les vérifications suivantes :

#### <a name="step-1-check-powershell-version"></a>Étape 1 : Vérifier la version de PowerShell

* Ouvrez Windows PowerShell et exécutez la commande suivante :

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

* Si la sortie indique une version supérieure à **3.7.0**, effectuez l’étape 2 ci-dessous. Sinon, passez à l’étape 3.

#### <a name="step-2-uninstall-the-powershell-version"></a>Étape 2 : Désinstaller la version de PowerShell

Désinstallez la version actuelle de PowerShell en réalisant les actions suivantes :

* Supprimez les modules dépendants en exécutant la commande suivante dans PowerShell :

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

* Exécutez la commande suivante pour garantir la suppression correcte de tous les modules dépendants :

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Étape 3 : Installer PowerShell version 3.7.0

Une fois que vous avez vérifié qu’aucun module AzureRM n’est présent, installez la version 3.7.0 à l’aide de l’une des méthodes suivantes :

* À partir de GitHub, [lien](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)

OR

* Exécutez la commande suivante dans la fenêtre PowerShell :

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell peut également avoir été installé à l’aide d’un fichier msi. Pour le supprimer, désinstallez-le à l’aide de l’option de désinstallation des programmes dans le Panneau de configuration.

### <a name="order-and-receive-the-data-box-device"></a>Commander et recevoir l’appareil Data Box

Le processus de sauvegarde hors connexion avec MARS et Azure Data Box nécessite que l’état des appareils Data Box nécessaires soit « Livré » pour pouvoir ensuite déclencher la sauvegarde hors connexion à l’aide de l’agent MARS. Reportez-vous à [Taille des données de sauvegarde et SKU Data Box pris en charge](#backup-data-size-and-supported-data-box-skus) pour commander le SKU qui répond le mieux à vos besoins. Suivez les étapes décrites dans [cet article](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) pour commander et recevoir vos appareils Data Box.

>[!IMPORTANT]
>Ne sélectionnez pas BlobStorage pour le type de compte. L’agent MARS exige un compte qui prend en charge les objets blob de pages, ce qui n’est pas possible quand BlobStorage est sélectionné. Nous vous recommandons vivement de sélectionner le type de compte *StorageV2* (*usage général v2*) lors de la création du compte de stockage cible pour votre travail Azure Data Box.

![Choisir un type de compte dans les détails de l’instance](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-setup-the-mars-agent"></a>Installer et configurer l’agent MARS

* Veillez à désinstaller toutes les installations précédentes de l’agent MARS.

* Téléchargez l’agent MARS le plus récent [ici](https://aka.ms/azurebackup_agent).

* Exécutez *MARSAgentInstaller.exe* et exécutez ***uniquement** les étapes nécessaires pour [installer et inscrire l’agent](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) dans le coffre Recovery Services dans lequel vous voulez stocker vos sauvegardes.

  >[!NOTE]
  > Le coffre Recovery Services doit se trouver dans le même abonnement que le travail Azure Data Box.

* Une fois que l’agent est inscrit dans le coffre Recovery Services, suivez les étapes décrites dans les sections ci-dessous.

## <a name="setup-azure-data-box-devices"></a>Configurer un ou plusieurs appareils Azure Data Box

Selon la référence SKU Azure Data Box que vous avez commandée, effectuez les étapes décrites dans les sections appropriées ci-dessous pour configurer et préparer les appareils Data Box afin de permettre à l’agent MARS d’identifier et de transférer les données de sauvegarde initiales.

### <a name="setup-azure-data-box-disk"></a>Configurer Azure Data Box Disk

Si vous avez commandé un ou plusieurs disques Azure Data Box (jusqu’à 8 To chacun), suivez les étapes mentionnées ici pour [déballer, connecter et déverrouiller votre disque Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up).

>[!NOTE]
>Il est possible que le serveur doté de l’agent MARS ne dispose d’aucun port USB. Dans ce cas, vous pouvez connecter votre disque Azure Data Box Disk à un autre serveur/client et exposer la racine de l’appareil en tant que partage réseau.

### <a name="setup-azure-data-box"></a>Configurer Azure Data Box

Si vous avez commandé un disque Azure Data Box (jusqu’à 100 To), suivez les étapes mentionnées ici [pour configurer votre disque Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up).

#### <a name="mount-your-azure-data-box-as-local-system"></a>Monter votre disque Azure Data Box en tant que système local

L’agent MARS fonctionne dans le contexte du système local et nécessite donc de fournir le même niveau de privilège au chemin de montage où le disque Azure Data Box est connecté. Suivez les étapes ci-dessous pour vérifier que vous pouvez monter votre appareil Data Box en tant que système local à l’aide du protocole NFS :

* Activez la fonctionnalité Client pour NFS sur le serveur Windows Server (sur lequel l’agent MARS est installé).<br>
  Spécifiez une autre source : *WIM:D:\Sources\Install.wim:4*

* Téléchargez PSExec à partir de <https://download.sysinternals.com/files/PSTools.zip> sur le serveur sur lequel l’agent MARS est installé.

* Ouvrez une invite de commandes avec élévation de privilèges et exécutez la commande suivante avec le répertoire contenant PSExec.exe comme répertoire actif :

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

* La fenêtre de commande qui s’ouvre à l’issue de la commande ci-dessus se trouve dans le contexte du système local. Utilisez cette fenêtre de commande pour exécuter les étapes de montage du partage d’objets blob de pages Azure en tant que lecteur réseau sur votre serveur Windows Server.

* Suivez les étapes décrites [ici](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) pour connecter votre serveur sur lequel est installé l’agent MARS à l’appareil Data Box par le biais de NFS, puis exécutez la commande suivante à l’invite de commandes du système local pour monter le partage d’objets blob de pages Azure :

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

* Une fois monté, vérifiez si vous pouvez accéder à X: à partir de votre serveur. Si oui, passez à la section suivante de cet article.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Transférer les données de sauvegarde initiales aux appareils Azure Data Box

* Ouvrez l’application **Sauvegarde Microsoft Azure** sur votre serveur.

* Cliquez sur **Planifier la sauvegarde** dans le volet **Actions**.

    ![Cliquer sur Planifier la sauvegarde](./media/offline-backup-azure-data-box/schedule-backup.png)

* Suivez les étapes de l’**Assistant Planifier la sauvegarde**.

* **Ajoutez des éléments** de sorte que la taille totale des éléments soit comprise entre les [limites de taille prises en charge par le SKU Azure Data Box](#backup-data-size-and-supported-data-box-skus) que vous avez commandé et reçu.

    ![Ajouter des éléments à sauvegarder](./media/offline-backup-azure-data-box/add-items.png)

* Sélectionnez la planification de sauvegarde et la stratégie de conservation appropriées pour les fichiers et dossiers, ainsi que l’état du système (l’état du système est applicable uniquement pour les serveurs Windows et non pour les clients Windows).

* Dans l’écran **Choisir le type de sauvegarde initiale (fichiers et dossiers)** de l’Assistant, choisissez l’option **Transférer à l’aide de disques Microsoft Azure Data Box**, puis cliquez sur **suivant**.

    ![Choisir un type de sauvegarde initiale](./media/offline-backup-azure-data-box/initial-backup-type.png)

* Connectez-vous à Azure lorsque vous y êtes invité à l’aide des informations d’identification d’utilisateur qui disposent d’un accès propriétaire à l’abonnement Azure. Une fois que vous avez effectué cette opération, un écran semblable à celui-ci doit s’afficher :

    ![Création des ressources et application des autorisations requises](./media/offline-backup-azure-data-box/creating-resources.png)

* L’agent MARS récupère ensuite les travaux Data Box inclus dans l’abonnement dont l’état est « Livré ».

    ![Récupération des travaux Data Box de l’ID d’abonnement](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

* Sélectionnez la commande Data Box appropriée pour laquelle vous avez déballé, connecté et déverrouillé votre disque Data Box. Cliquez sur **Suivant**.

    ![Sélectionner la ou les commandes Data Box](./media/offline-backup-azure-data-box/select-databox-order.png)

* Cliquez sur **Détecter l’appareil** sur l’écran **Détection de l’appareil Data Box**. Cela permet à l’agent MARS d’analyser les disques Azure Data Box attachés localement et de les détecter comme illustré ci-dessous :

    ![Détection des appareils Data Box](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Si vous avez connecté le disque Azure Data Box en tant que partage réseau (en raison de l’indisponibilité de ports USB ou parce que vous avez commandé et monté un appareil Data Box de 100 To), la détection va échoue au début, mais vous aurez la possibilité d’entrer le chemin réseau de l’appareil Data Box comme illustré ci-dessous :

    ![Entrer le chemin réseau](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Indiquez le chemin réseau du répertoire racine du disque Azure Data Box. Ce répertoire doit contenir un répertoire portant le nom *PageBlob* comme illustré ci-dessous :
    >
    >![Répertoire racine du disque Azure Data Box](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Par exemple, si le chemin du disque est `\\mydomain\myserver\disk1\` et si *Disk1* contient un répertoire appelé *PageBlob*, le chemin à fournir dans l’Assistant de l’agent MARS est `\\mydomain\myserver\disk1\`.
    >
    >Si vous [configurez un appareil Azure Data Box de 100 To](#setup-azure-data-box), indiquez le chemin réseau d’appareil suivant : `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob`

* Cliquez sur **Suivant**, puis sur **Terminer** dans l’écran suivant pour enregistrer la stratégie de sauvegarde et de conservation avec la configuration de sauvegarde hors connexion qui utilise Azure Data Box.

* L’écran suivant confirme que la stratégie est correctement enregistrée :

    ![La stratégie est correctement enregistrée](./media/offline-backup-azure-data-box/policy-saved.png)

* Cliquez sur **Fermer** dans l’écran ci-dessus.

* Cliquez sur ***Sauvegarder maintenant** dans le volet **Actions** de la console de l’agent MARS, puis cliquez sur **Sauvegarder** dans l’écran de l’Assistant, comme illustré ci-dessous :

    ![Assistant Sauvegarder maintenant](./media/offline-backup-azure-data-box/backup-now.png)

* L’agent MARS commence à sauvegarder les données que vous avez sélectionnées sur l’appareil Azure Data Box. Cette opération peut prendre quelques heures ou quelques jours selon le nombre de fichiers et la vitesse de connexion entre le serveur sur lequel est installé l’agent MARS et le disque Azure Data Box Disk.

* Une fois la sauvegarde des données terminée, un écran semblable à celui-ci apparaît sur l’agent MARS :

    ![Progression de la sauvegarde indiquée](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Étapes à l’issue de la sauvegarde

Cette section explique les étapes à suivre une fois que la sauvegarde des données a été correctement effectuée sur le disque Azure Data Box Disk.

* Suivez les étapes décrites dans cet article pour [expédier le disque Azure Data Box à Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up). Si vous avez utilisé un appareil Azure Data Box de 100 To, effectuez ces étapes pour [expédier le disque Azure Data Box à Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

* [Supervisez le travail Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) dans le portail Azure. Une fois le travail Azure Data Box terminé, l’agent MARS déplace automatiquement les données depuis le compte de stockage vers le coffre Recovery Services au moment de la sauvegarde planifiée suivante. Le travail de sauvegarde est alors marqué comme « terminé » si un point de récupération a correctement été créé.

    >[!NOTE]
    >L’agent MARS déclenche des sauvegardes aux heures planifiées pendant la création de la stratégie. Toutefois, ces travaux indiquent « En attente de la fin du travail Azure Data Box » tant que le travail n’est pas terminé.

* Une fois que l’agent MARS a réussi à créer un point de récupération correspondant à la sauvegarde initiale, vous pouvez supprimer le compte de stockage (ou son contenu spécifique) associé au travail Azure Data Box.

## <a name="troubleshooting"></a>Dépannage

L’agent Sauvegarde Microsoft Azure crée une application Azure AD pour vous dans votre locataire. Cette application nécessite un certificat à des fins d’authentification. Celui-ci est créé et chargé lors de la configuration de la stratégie d’amorçage hors connexion. Nous utilisons Azure PowerShell pour créer et charger le certificat sur l’application Azure AD.

### <a name="issue"></a>Problème

Au moment de la configuration de la sauvegarde hors connexion, vous pouvez être confronté à ce problème : en raison d’un bogue dans l’applet de commande Azure PowerShell, vous ne pouvez pas ajouter plusieurs certificats à la même application Azure AD que celle créée par l’agent Sauvegarde Microsoft Azure. Vous êtes concerné si vous avez configuré une stratégie d’amorçage hors connexion pour le même serveur ou un autre serveur.

### <a name="how-to-verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Comment vérifier si le problème est provoqué par cette cause racine spécifique

Pour vérifier que cette défaillance est due au problème ci-dessus, effectuez l’une des étapes suivantes :

#### <a name="step-1"></a>Étape 1

Vérifiez si vous voyez le message d’erreur suivant dans la console Sauvegarde Microsoft Azure au moment de la configuration de la sauvegarde hors connexion :

![Impossible de créer une stratégie de sauvegarde hors connexion pour le compte Azure actuel](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>Étape 2

* Ouvrez le dossier **temp** dans le chemin d’installation (le chemin du dossier temporaire par défaut est *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*). Recherchez le fichier **CBUICurr** et ouvrez-le.

* Faites défiler le contenu du fichier **CBUICurr** jusqu’à la dernière ligne et vérifiez si la défaillance est due à `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`.

### <a name="workaround"></a>Solution de contournement

Pour résoudre ce problème, effectuez les étapes suivantes et réessayez de configurer la stratégie.

#### <a name="first-step"></a>Première étape

Connectez-vous à PowerShell qui apparaît dans l’interface utilisateur de Sauvegarde Microsoft Azure avec un autre compte doté d’un accès administrateur à l’abonnement pour lequel le travail d’importation/exportation est créé.

#### <a name="second-step"></a>Deuxième étape

Si aucun autre serveur n’a d’amorçage hors connexion configuré et ne dépend de l’application `AzureOfflineBackup_<Azure User Id>`, supprimez cette application depuis **Portail Azure** > **Azure Active Directory** > **Inscriptions d’applications**.

>[!NOTE]
> Vérifiez si l’application `AzureOfflineBackup_<Azure User Id>` n’a pas d’autre amorçage hors connexion configuré et si aucun autre serveur ne dépend de cette application. Accédez à **Paramètres** > **Clés**. Sous la section **Clés publiques**, aucune autre clé publique ne doit être ajoutée. Examinez la capture d’écran suivante à des fins de référence :
>
>![Clés publiques](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="third-step"></a>Troisième étape

À partir du serveur sur lequel vous essayez de configurer la sauvegarde hors connexion, réalisez les actions suivantes :

1. Ouvrez l’onglet **Gérer l’application de certificat d’ordinateur** > **Personnel** et recherchez le certificat portant le nom `CB_AzureADCertforOfflineSeeding_<ResourceId>`.

2. Sélectionnez le certificat ci-dessus, cliquez avec le bouton droit sur **Toutes les tâches** et **exportez**-le sans clé privée, au format .cer.

3. Accédez à l’application de sauvegarde hors connexion Azure mentionnée au **point 2**. Dans **Paramètres** > **Clés** > **Charger une clé publique**, chargez le certificat exporté à l’étape précédente.

    ![Charger une clé publique](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Sur le serveur, ouvrez le Registre en tapant **regedit** dans la fenêtre Exécuter.

5. Accédez au Registre *Ordinateur\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.* Cliquez avec le bouton droit sur **CloudBackupProvider** et ajoutez une nouvelle valeur de chaîne portant le nom `AzureADAppCertThumbprint_<Azure User Id>`.

    >[!NOTE]
    > Pour récupérer l’ID d’utilisateur Azure, effectuez l’une des actions suivantes :
    >
    >1. À partir du PowerShell connecté à Azure, exécutez la commande `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as defined in the portal”`.
    > 2. Accédez au chemin du Registre : *Ordinateur\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup*; Name: *CurrentUserId*

6. Cliquez avec le bouton droit sur la chaîne ajoutée à l’étape ci-dessus, puis sélectionnez **Modifier**. Dans la valeur, fournissez l’empreinte du certificat que vous avez exporté au **point 2**, puis cliquez sur **OK**.

7. Pour obtenir la valeur de l’empreinte, double-cliquez sur le certificat, sélectionnez l’onglet **Détails** et faites défiler le contenu jusqu’à ce que le champ de l’empreinte apparaisse. Cliquez sur **Empreinte** et copiez la valeur.

    ![Champ Empreinte du certificat](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Questions

Pour toute question ou explication concernant les problèmes rencontrés, contactez [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com).
