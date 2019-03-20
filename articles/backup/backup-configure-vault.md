---
title: Sauvegarder des ordinateurs Windows avec l’agent Azure Backup MARS
description: Utilisez l’agent Microsoft Recovery Services (MARS) d’Azure Backup pour sauvegarder des machines de Windows.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: raynew
ms.openlocfilehash: 7a1bd6da68b49481429709c7e4fd37dd5c07ae2c
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200784"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>Sauvegarder des ordinateurs Windows avec l’agent Azure Backup MARS

Cet article explique comment sauvegarder des ordinateurs Windows à l’aide de la [sauvegarde Azure](backup-overview.md) service et l’agent Microsoft Azure Recovery Services (MARS), également appelé l’agent Azure Backup.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Vérifier les conditions préalables et créer un coffre Recovery Services.
> * Télécharger et configurer l’agent MARS
> * Créer une stratégie de sauvegarde et de la planification.
> * Effectuez une ad-hoc sauvegarde.

## <a name="about-the-mars-agent"></a>À propos de l’agent MARS

L’agent MARS est utilisée par le service sauvegarde Azure pour sauvegarder les fichiers, dossiers et l’état du système à partir des machines locales et machines virtuelles Azure dans un coffre Recovery Services sauvegarde Azure. Vous pouvez exécuter l’agent comme suit :

- Exécutez l’agent directement sur les ordinateurs Windows en local afin qu’ils peuvent sauvegarder directement dans un coffre Recovery Services sauvegarde Azure.
- Exécutez les machines virtuelles Azure de l’agent en cours d’exécution Windows (côte à côte avec l’extension de sauvegarde de machine virtuelle Azure) pour sauvegarder des fichiers et dossiers spécifiques sur la machine virtuelle.
- Exécutez l’agent sur un serveur de sauvegarde Microsoft Azure (MABS) ou sur un serveur System Center Data Protection Manager (DPM). Dans ce scénario, les charges de travail et les machines Sauvegardez au serveur de sauvegarde AZURE/DPM et MABS/DPM sauvegarde ensuite dans un coffre dans Azure à l’aide de l’agent MARS.
Les éléments que vous pouvez sauvegarder varient en fonction de l’endroit où l’agent est installé.

> [!NOTE]
> La principale méthode pour la sauvegarde des machines virtuelles Azure est à l’aide d’une extension de sauvegarde Azure sur la machine virtuelle. La totalité de la machine virtuelle est ainsi sauvegardée. Vous souhaiterez peut-être installer et utiliser l’agent MARS en même temps que l’extension si vous souhaitez sauvegarder des fichiers et dossiers spécifiques sur la machine virtuelle. [Plus d’informations](backup-architecture.md#architecture-direct-backup-of-azure-vms)

![Étapes du processus de sauvegarde](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Avant de commencer

- [Découvrez comment](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders) Azure Backup sauvegarde les machines Windows avec l’agent MARS.
- [En savoir plus sur](backup-architecture.md#architecture-back-up-to-dpmmabs) l’architecture de sauvegarde en cours d’exécution l’agent MARS sur un serveur MABS ou DPM secondaire.
- [Révision](backup-support-matrix-mars-agent.md) ce qui est pris en charge et ce qui peut être sauvegardé avec l’agent MARS.
- Vérifier l’accès internet sur les ordinateurs que vous souhaitez sauvegarder.
- Pour sauvegarder un serveur ou un client sur Azure, vous devez disposer d’un compte Azure. Si vous n’en possédez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes.

### <a name="verify-internet-access"></a>Vérifiez l’accès à Internet

Si votre ordinateur dispose d’un accès à internet limité, assurez-vous que les paramètres de pare-feu sur l’ordinateur ou le proxy permettent à ces URL et l’adresse IP :

**URLs**

- www\.msftncsi.com
- *.Microsoft.com
- *.MicrosoftAzure.com
- *.microsoftonline.com
- * .windows.net

**Adresse IP**

- 20.190.128.0/18
- 40.126.0.0/18


## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

Un coffre Recovery Services stocke toutes les sauvegardes et les points de récupération créés au fil du temps et contient la stratégie de sauvegarde appliquée aux machines sauvegardées. Créez un coffre comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide de votre abonnement Azure.
2. Dans Rechercher, tapez **Recovery Services** et cliquez sur **coffres Recovery Services**.

    ![Créer un coffre Recovery Services - Étape 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png)

3. Sur le **coffres Recovery Services** menu, cliquez sur **+ ajouter**.

    ![Créer un coffre Recovery Services - Étape 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. Sous **Nom**, entrez un nom convivial permettant d’identifier le coffre.

   - Le nom doit être unique pour l’abonnement Azure.
   - Il peut comprendre entre 2 et 50 caractères.
   - Il doit commencer par une lettre, et ne peut contenir que des lettres, des chiffres et des traits d’union.

5. Sélectionnez l’abonnement Azure, le groupe de ressources et la région géographique dans laquelle le coffre doit être créé. Données de sauvegarde sont envoyées dans le coffre. Cliquez ensuite sur **Créer**.

    ![Créer un coffre Recovery Services - Étape 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

   - La création du coffre peut prendre du temps.
   - Surveillez les notifications d’état dans l’angle supérieur droit du portail. Si vous ne voyez pas le coffre après quelques minutes, cliquez sur **Actualiser**.

     ![Bouton Actualiser](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>Définir la redondance du stockage

Sauvegarde Azure gère automatiquement le stockage pour le coffre. Vous devez spécifier la manière dont ce stockage est répliqué.

1. Dans le panneau **Coffres Recovery Services**, cliquez sur le nouveau coffre. Sous le **paramètres** , cliquez sur **propriétés**.
2. Dans **propriétés**, sous **Configuration de la sauvegarde**, cliquez sur **mise à jour**.

3. Sélectionnez le type de réplication de stockage, puis cliquez sur **enregistrer**.

      ![Définir la configuration de stockage du nouveau coffre](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

- Nous recommandons que si vous utilisez Azure comme un point de terminaison de stockage de sauvegarde principal, continuer à utiliser la valeur par défaut **géo-redondant** paramètre.
- Sinon, choisissez l’option **Localement redondant**, qui réduit les coûts de stockage Azure.
- En savoir plus sur [géo](../storage/common/storage-redundancy-grs.md) et [local](../storage/common/storage-redundancy-lrs.md) redondance.

## <a name="download-the-mars-agent"></a>Télécharger l’agent MARS

Téléchargez l’agent MARS pour installation sur les ordinateurs que vous souhaitez sauvegarder.

- Si vous avez déjà installé l’agent sur tous les ordinateurs, vérifiez que vous utilisez la dernière version.
- La dernière version est disponible dans le portail ou à l’aide un [téléchargement direct](https://aka.ms/azurebackup_agent)

1. Dans le coffre, sous **mise en route**, cliquez sur **sauvegarde**.

    ![Ouvrir le panneau Objectif de sauvegarde](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. Dans **où s’exécute votre charge de travail ?**, sélectionnez **On-premises**. Vous devez sélectionner cette option même si vous souhaitez installer l’agent MARS sur une machine virtuelle Azure.
3. Dans **que voulez-vous sauvegarder ?**, sélectionnez **fichiers et dossiers** et/ou **état du système**. Il existe un nombre d’autres options disponibles, mais ils sont pris en charge uniquement si vous exécutez un serveur de sauvegarde secondaire. Cliquez sur **préparer l’Infrastructure**.

      ![Configuration des fichiers et dossiers](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. Sur le **préparer l’infrastructure**, sous **agent installer les Services de récupération**, téléchargez l’agent MARS.

    ![Télécharger l’agent pour Windows Server ou Windows Client](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. Dans le menu contextuel de téléchargement, cliquez sur **Enregistrer**. Par défaut, le fichier **MARSagentinstaller.exe** est enregistré dans le dossier Téléchargements.

6. À présent, **déjà téléchargement ou à l’aide de l’Agent Recovery Services dernière**, puis téléchargez les informations d’identification de coffre.

    ![Télécharger les informations d’identification du coffre](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. Cliquez sur **Enregistrer**. Le fichier est téléchargé vers votre dossier de téléchargement. Vous ne pouvez pas ouvrir le fichier d’informations d’identification de coffre.

## <a name="install-and-register-the-agent"></a>Installer et inscrire l’agent

1. Exécutez le **MARSagentinstaller.exe** fichier sur les ordinateurs que vous souhaitez sauvegarder.
2. Dans l’Assistant Installation de l’Agent MARS > **paramètres d’Installation**, spécifiez où vous souhaitez installer l’agent et un emplacement à utiliser pour le cache. Cliquez ensuite sur **Suivant**.
   - Sauvegarde Azure utilise le cache pour stocker les instantanés de données avant leur envoi vers Azure.
   - L’emplacement du cache doit avoir un espace libre égal au moins 5 % de la taille des données que vous sauvegardez.

     ![Paramètres d’installation Assistant MARS](./media/backup-configure-vault/mars1.png)

2. Dans **Configuration du Proxy**, spécifiez la façon dont l’agent en cours d’exécution sur l’ordinateur Windows se connecte à internet. Cliquez ensuite sur **Suivant**.

   - Si vous êtes l’aide d’un proxy spécifier les paramètres de proxy et les informations d’identification si nécessaire.
   - N’oubliez pas que l’agent a besoin d’accéder à [ces URL](#verify-internet-access).

     ![Accès internet de l’Assistant MARS](./media/backup-configure-vault/mars2.png)

3. Dans **Installation** vérifiez vérifier les conditions préalables, puis cliquez sur **installer**.
4. Une fois que l’agent est installé, cliquez sur **procéder à l’enregistrement**.
5. Dans le **Assistant Inscription de serveur** > **Identification de coffre**, recherchez et sélectionnez le fichier d’informations d’identification que vous avez téléchargé. Cliquez ensuite sur **Suivant**.

    ![Register - informations d’identification de coffre](./media/backup-configure-vault/register1.png)

6. Dans **paramètre de chiffrement**, spécifiez une phrase secrète qui sera utilisée pour chiffrer et déchiffrer des sauvegardes pour la machine.

    - Enregistrer la phrase secrète de chiffrement dans un emplacement sécurisé.
    - Si vous perdez ou oubliez cette phrase secrète, Microsoft ne peut pas vous aider à récupérer les données de sauvegarde. Enregistrez le fichier dans un emplacement sécurisé. Vous en avez besoin pour restaurer une sauvegarde.

7. Cliquez sur **Terminer**. L’agent est désormais installé et votre ordinateur est inscrit dans le coffre. Vous êtes prêt à configurer et à planifier votre sauvegarde.

## <a name="create-a-backup-policy"></a>Créer une stratégie de sauvegarde

La stratégie de sauvegarde spécifie à quel moment de prendre des instantanés des données pour créer des points de récupération et la durée de conservation des points de récupération.

- Vous configurez une stratégie de sauvegarde à l’aide de l’agent MARS.
- Sauvegarde Azure ne prend automatiquement pas l’heure d’été (DST) en compte. Cela peut entraîner certaines différences entre l’heure réelle et l’heure de sauvegarde planifiée.

Créez une stratégie comme suit :

1. Sur chaque ordinateur, ouvrez l’agent MARS. Vous pouvez le trouver en recherchant **Sauvegarde Microsoft Azure** sur votre ordinateur.
2. Dans **Actions**, cliquez sur **planifier la sauvegarde**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

3. Dans l’Assistant Planification de sauvegarde > **mise en route**, cliquez sur **suivant**.
4. Dans **sélectionner les éléments à sauvegarder**, cliquez sur **ajouter les éléments**.
5. Dans **sélectionner les éléments**, sélectionnez ce que vous voulez sauvegarder. Cliquez ensuite sur **OK**.
6. Dans **sélectionner les éléments à sauvegarder** , cliquez sur **suivant**.
7. Dans **spécifier la planification de sauvegarde** , spécifiez lorsque vous souhaitez effectuer des sauvegardes quotidiennes ou hebdomadaires. Cliquez ensuite sur **Suivant**.

    - Un point de récupération est créé lorsqu’une sauvegarde est effectuée.
    - Le nombre de points de récupération créés dans votre environnement dépend de votre planification de sauvegarde.

1. Vous pouvez planifier des sauvegardes quotidiennes jusqu'à trois fois par jour. Par exemple, la capture d’écran montre deux sauvegardes quotidiennes, une à minuit et à 18 h 00.

    ![Planification quotidienne](./media/backup-configure-vault/day-schedule.png)

9. Vous pouvez exécuter des sauvegardes hebdomadaires trop. Par exemple, la capture d’écran montre les sauvegardes effectuées chaque dimanche autre et le mercredi à 9 h 30 et à 1 h 00.

    ![Planification hebdomadaire](./media/backup-configure-vault/week-schedule.png)

8. Sur le **sélectionner la stratégie de rétention** , spécifiez la manière dont vous stockez des copies historiques de vos données. Cliquez ensuite sur **Suivant**.

   - Paramètres de rétention spécifient les points de récupération doivent être stockées, et la durée pendant laquelle ils sont stockés.
   - Par exemple, lorsque vous définissez un paramètre de rétention quotidienne, vous indiquez qu’à l’heure spécifiée pour la rétention quotidienne, le dernier point de récupération est conservé pour le nombre de jours spécifié. Ou bien, un autre exemple, vous pouvez spécifier une stratégie de rétention mensuelle pour indiquer que le point de récupération créé le 30 de chaque mois doit être stocké pendant 12 mois.
   - Rétention des points de récupération quotidien et hebdomadaire est généralement coïncide avec la planification de sauvegarde. Ce qui signifie que lorsque la sauvegarde est déclenchée en fonction de la planification, le point de récupération créé par la sauvegarde est stocké pour la durée indiquée dans la stratégie de rétention quotidienne ou hebdomadaire.
   - Par exemple, dans la capture d’écran suivante :
     - Les sauvegardes quotidiennes à minuit et à 18 h 00 sont conservés pendant sept jours.
     - Les sauvegardes effectuées le samedi à minuit et à 18 h 00 sont conservées pendant 4 semaines.
     - Les sauvegardes effectuées sur la dernière semaine du mois à minuit et à 18 h 00 le samedi sont conservées pendant 12 mois. -Les sauvegardes effectuées sur un samedi dans la dernière semaine du mois de mars sont conservées pendant 10 ans.

   ![Exemple de rétention](./media/backup-configure-vault/retention-example.png)

11. Dans **choisir le Type de sauvegarde initiale** spécifier la façon d’effectuer la première sauvegarde, via le réseau ou hors connexion. Cliquez ensuite sur **Suivant**.

10. Dans **Confirmation**, passez en revue les informations, puis cliquez sur **Terminer**.
11. Lorsque l’Assistant a terminé la création de la planification de la sauvegarde, cliquez sur **Fermer**.

### <a name="perform-the-initial-backup-offline"></a>Effectuer la sauvegarde initiale hors connexion

Vous pouvez exécuter automatiquement une initiale sauvegarder sur le réseau ou hors connexion. L’amorçage hors connexion pour une sauvegarde initiale est utile si vous avez de grandes quantités de données qui nécessitent beaucoup de bande passante réseau à transférer. Vous effectuez un transfert hors connexion comme suit :

1. Vous écrivez les données de sauvegarde vers un emplacement intermédiaire.
2. Vous utilisez l’outil AzureOfflineBackupDiskPrep pour copier les données à partir de l’emplacement intermédiaire à un ou plusieurs disques SATA.
3. L’outil crée une tâche d’importation Azure. [En savoir plus](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) sur Azure, importez et l’exportation.
4. Vous envoyez les disques SATA à un centre de données Azure.
5. Dans le centre de données, les données du disque sont copiées vers un compte de stockage Azure.
6. La sauvegarde Azure copie les données à partir du compte de stockage dans le coffre, et les sauvegardes incrémentielles sont planifiées.

[En savoir plus](backup-azure-backup-import-export.md) sur l’amorçage hors connexion.

### <a name="enable-network-throttling"></a>Activation de la limitation du réseau

Vous pouvez contrôler la façon dont la bande passante réseau est utilisée par l’agent MARS en activant la limitation du réseau. La limitation est utile si vous devez sauvegarder les données pendant les heures de travail mais souhaitez contrôler la bande passante est utilisée pour la sauvegarde et restaurer l’activité.

- Réseau de sauvegarde Azure utilise la limitation [qualité de Service (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) sur le système d’exploitation local.
- Réseau de limitation pour la sauvegarde est disponible sur Windows Server 2008 R2 et versions ultérieures et Windows 7 et versions ultérieures. Systèmes d’exploitation doit être en cours d’exécution les derniers service packs.

Activer la limitation réseau comme suit :

1. Dans l’agent MARS, cliquez sur **modifier les propriétés**.
2. Sur le **limitation** onglet, vérification **activer l’utilisation de la bande passante internet de limitation pour les opérations de sauvegarde**.

    ![Limitation du réseau](./media/backup-configure-vault/throttling-dialog.png)
3. Spécifiez la bande passante autorisée pendant le travail et en dehors des heures de travail. Les valeurs de bande passante qui démarrent à 512 Kbits/s, aller jusqu'à 1 023 Mbits/s. Cliquez ensuite sur **OK**.

## <a name="run-an-ad-hoc-backup"></a>Exécuter une sauvegarde ad hoc

1. Dans l’agent MARS, cliquez sur **sauvegarder maintenant**. Ceci lance la réplication initiale sur le réseau.

    ![Sauvegarder Windows Server maintenant](./media/backup-configure-vault/backup-now.png)

2. Dans **Confirmation**, passez en revue les paramètres, puis cliquez sur **sauvegarder**.
3. Cliquez sur **Fermer** pour fermer l’assistant. Si vous procédez ainsi avant la fin de la sauvegarde, l’Assistant continue de s’exécuter en arrière-plan.

Une fois la sauvegarde initiale terminée, le statut **Tâche terminée** apparaît dans la console Backup.

## <a name="next-steps"></a>Étapes suivantes

[Découvrez comment](backup-azure-restore-windows-server.md) restaurer des fichiers.
