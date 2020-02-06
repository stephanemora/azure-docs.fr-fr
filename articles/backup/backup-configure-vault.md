---
title: Sauvegarder des machines Windows avec l’agent MARS
description: Utilisez l’agent MARS (Microsoft Azure Recovery Services) pour sauvegarder des machines Windows.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 990929cc95d1c25117873ca39415d33370456b91
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025535"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>Sauvegarder des machines Windows avec l’agent MARS de la sauvegarde Azure

Cet article explique comment sauvegarder des machines Windows à l’aide du service [Sauvegarde Azure](backup-overview.md) et de l’agent MARS (Microsoft Azure Recovery Services), également appelé agent Sauvegarde Azure.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
>
> * Vérifiez les conditions préalables et créez un coffre Recovery Services.
> * Télécharger et configurer l’agent MARS
> * Créez une stratégie et une planification de la sauvegarde.
> * Effectuez une sauvegarde à la demande.

## <a name="about-the-mars-agent"></a>À propos de l’agent MARS

L’agent MARS est utilisé par Sauvegarde Azure pour sauvegarder les fichiers, les dossiers et l'état du système de machines locales et de machines virtuelles Azure dans un coffre Recovery Services de sauvegarde sur Azure. Pour exécuter l'agent, procédez comme suit :

* Exécutez l’agent directement sur des machines Windows locales pour les sauvegarder directement dans un coffre Recovery Services de sauvegarde sur Azure.
* Exécutez l’agent sur des machines virtuelles Azure exécutant Windows (côte à côte avec l’extension de sauvegarde des machines virtuelles Azure) pour sauvegarder des fichiers et des dossiers spécifiques sur la machine virtuelle.
* Exécutez l’agent sur un serveur de sauvegarde Microsoft Azure (MABS) ou sur un serveur System Center Data Protection Manager (DPM). Dans ce scénario, les machines et les charges de travail sont sauvegardées sur un serveur MABS/DPM, lequel effectue la sauvegarde dans un coffre sur Azure à l’aide de l’agent MARS.
Les éléments que vous pouvez sauvegarder varient en fonction de l’endroit où l’agent est installé.

> [!NOTE]
> La méthode principale pour sauvegarder des machines virtuelles Azure consiste à utiliser une extension Sauvegarde Azure sur la machine virtuelle. La totalité de la machine virtuelle est ainsi sauvegardée. Vous souhaiterez peut-être installer et utiliser l’agent MARS avec l’extension pour sauvegarder des fichiers et des dossiers spécifiques sur la machine virtuelle. [Plus d’informations](backup-architecture.md#architecture-built-in-azure-vm-backup)

![Étapes du processus de sauvegarde](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Avant de commencer

* [Découvrez comment ](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)Sauvegarde Azure sauvegarde des machines Windows avec l’agent MARS.
* [Découvrez](backup-architecture.md#architecture-back-up-to-dpmmabs) l’architecture de sauvegarde exécutant l’agent MARS sur un serveur MABS ou DPM secondaire.
* [Passez en revue](backup-support-matrix-mars-agent.md) ce qui est pris en charge et ce qui peut être sauvegardé avec l’agent MARS.
* Vérifiez l’accès à Internet sur les machines que vous souhaitez sauvegarder.
* Pour sauvegarder un serveur ou un client sur Azure, vous devez disposer d’un compte Azure. Si vous n’en possédez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes.

### <a name="verify-internet-access"></a>Vérifiez l’accès à Internet

Si votre machine a un accès à Internet limité, assurez-vous que les paramètres du pare-feu sur la machine ou le proxy autorisent ces URL et cette adresse IP :

#### <a name="urls"></a>URLs

* www\.msftncsi.com
* *.Microsoft.com
* *.MicrosoftAzure.com
* *.microsoftonline.com
* \* .windows.net

#### <a name="ip-addresses"></a>Adresses IP

* 20.190.128.0/18
* 40.126.0.0/18

L’accès à toutes les URL et adresses IP listées ci-dessus utilise le protocole HTTPS sur le port 443.

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

Un coffre Recovery Services stocke les sauvegardes et points de récupération que vous créez au fil du temps, et contient la stratégie de sauvegarde appliquée aux machines sauvegardées. Créez un coffre comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide de votre abonnement Azure.

2. Recherchez et sélectionnez **Coffres Recovery Services**.

    ![Créer un coffre Recovery Services - Étape 1](./media/backup-configure-vault/open-recovery-services-vaults.png)

3. Dans le menu **Coffres Recovery Services**, cliquez sur **+Ajouter**.

    ![Créer un coffre Recovery Services - Étape 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. Sous **Nom**, entrez un nom convivial permettant d’identifier le coffre.

   * Le nom doit être unique pour l’abonnement Azure.
   * Il peut comprendre entre 2 et 50 caractères.
   * Il doit commencer par une lettre, et ne peut contenir que des lettres, des chiffres et des traits d’union.

5. Sélectionnez l’abonnement Azure, le groupe de ressources et la région géographique où le coffre doit être créé. Les données de sauvegarde sont envoyées dans le coffre. Cliquez ensuite sur **Créer**.

    ![Créer un coffre Recovery Services - Étape 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

   * La création du coffre peut prendre du temps.
   * Surveillez les notifications d’état dans l’angle supérieur droit du portail. Si vous ne voyez pas le coffre après quelques minutes, cliquez sur **Actualiser**.

     ![Bouton Actualiser](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>Définir la redondance du stockage

La Sauvegarde Azure gère automatiquement le stockage du coffre. Vous devez spécifier le mode de réplication de ce stockage.

1. Dans le panneau **Coffres Recovery Services**, cliquez sur le nouveau coffre. Dans la section **Paramètres**, cliquez sur **Propriétés**.
2. Dans **Propriétés**, sous **Configuration de la sauvegarde**, cliquez sur **Mise à jour**.

3. Sélectionnez le type de réplication de stockage, puis cliquez sur **Enregistrer**.

      ![Définir la configuration de stockage du nouveau coffre](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

* Si vous utilisez Azure comme principal point de terminaison du stockage de sauvegarde, nous vous recommandons de continuer à utiliser le paramètre **Géoredondant** par défaut.
* Sinon, choisissez l’option **Localement redondant**, qui réduit les coûts de stockage Azure.
* Informez-vous sur la redondance [géo](../storage/common/storage-redundancy-grs.md) et [locale](../storage/common/storage-redundancy-lrs.md).

## <a name="download-the-mars-agent"></a>Télécharger l’agent MARS

Téléchargez l’agent MARS à des fins d'installation sur les machines que vous souhaitez sauvegarder.

* Si vous avez déjà installé l’agent sur des machines, assurez-vous que vous exécutez la dernière version.
* La dernière version est disponible dans le portail ou en [téléchargement direct](https://aka.ms/azurebackup_agent).

1. Dans le coffre, sous **Prise en main**, cliquez sur **Sauvegarde**.

    ![Ouvrir le panneau Objectif de sauvegarde](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. Dans **Où s’exécute votre charge de travail ?** , sélectionnez **Local**. Vous devez sélectionner cette option, même si vous souhaitez installer l’agent MARS sur une machine virtuelle Azure.
3. Dans **Que voulez-vous sauvegarder ?** , sélectionnez **Fichiers et dossiers** et/ou **État système**. D'autres options sont disponibles, mais elles ne sont prises en charge que si vous exécutez un serveur de sauvegarde secondaire. Cliquez sur **Préparer l’infrastructure**.

      ![Configuration des fichiers et dossiers](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. Sur **Préparer l’infrastructure**, sous **Installer l'agent Recovery Services**, téléchargez l’agent MARS.

    ![Télécharger l’agent pour Windows Server ou Windows Client](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. Dans le menu contextuel de téléchargement, cliquez sur **Enregistrer**. Par défaut, le fichier **MARSagentinstaller.exe** est enregistré dans le dossier Téléchargements.

6. À présent, cochez **J'ai déjà téléchargé ou j'utilise déjà la dernière version de l'agent Recovery Services**, puis téléchargez les informations d’identification du coffre.

    ![Télécharger les informations d’identification du coffre](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. Cliquez sur **Enregistrer**. Le fichier est téléchargé dans votre dossier de téléchargement. Vous ne pouvez pas le fichier d’informations d’identification du coffre.

## <a name="install-and-register-the-agent"></a>Installer et inscrire l’agent

1. Exécutez le fichier **MARSagentinstaller.exe** sur les machines que vous souhaitez sauvegarder.
2. Dans l’Assistant Installation de l’agent MARS > **Paramètres d’installation**, spécifiez l’emplacement où vous souhaitez installer l'agent, ainsi que l'emplacement à utiliser pour le cache. Cliquez ensuite sur **Suivant**.
   * Sauvegarde Azure utilise le cache pour stocker les instantanés de données avant de les envoyer à Azure.
   * L’emplacement du cache doit disposer d'un espace libre correspondant à au moins 5 % de la taille des données à sauvegarder.

    ![Paramètres d’installation de l'Assistant MARS](./media/backup-configure-vault/mars1.png)

3. Dans **Configuration du proxy**, spécifiez la manière dont l’agent en cours d’exécution sur la machine Windows se connecte à Internet. Cliquez ensuite sur **Suivant**.

   * Si vous utilisez un proxy personnalisé, spécifiez les paramètres de ce proxy, de même que les informations d’identification, si besoin.
   * Souvenez-vous que l’agent doit avoir accès à [ces URL](#verify-internet-access).

     ![Accès à Internet de l'Assistant MARS](./media/backup-configure-vault/mars2.png)

4. Dans **Installation**, passez en revue la configuration requise, puis cliquez sur **Installer**.
5. Une fois l’agent installé, cliquez sur **Procéder à l’enregistrement**.
6. Dans l’**Assistant Inscrire un serveur** > **Identification du coffre**, parcourez et sélectionnez le fichier d’informations d’identification que vous avez téléchargé. Cliquez ensuite sur **Suivant**.

    ![Inscrire - Informations d’identification du coffre](./media/backup-configure-vault/register1.png)

7. Dans **Paramètre de chiffrement**, spécifiez une phrase secrète qui sera utilisée pour chiffrer et déchiffrer les sauvegardes de la machine.

    * Enregistrer la phrase secrète de chiffrement dans un emplacement sécurisé.
    * En cas de perte ou d’oubli de la phrase secrète, Microsoft ne sera pas en mesure de vous aider à récupérer les données de sauvegarde. Enregistrez le fichier dans un emplacement sécurisé. Il vous sera nécessaire pour restaurer une sauvegarde.

8. Cliquez sur **Terminer**. L’agent est désormais installé et votre ordinateur est inscrit dans le coffre. Vous êtes prêt à configurer et à planifier votre sauvegarde.

## <a name="create-a-backup-policy"></a>Créer une stratégie de sauvegarde

La stratégie de sauvegarde spécifie à quel moment prendre des instantanés des données pour créer des points de récupération, ainsi que la durée de conservation de ces derniers.

* Vous configurez une stratégie de sauvegarde à l’aide de l’agent MARS.
* Sauvegarde Azure ne prend pas automatiquement en compte l’heure d’été. Cela peut entraîner une certaine incohérence entre l’heure réelle et l’heure de sauvegarde planifiée.

Créez une stratégie comme suit :
1. Après le téléchargement et l’inscription de l’agent MARS, lancez la console de l’agent. Vous pouvez le trouver en recherchant **Sauvegarde Microsoft Azure** sur votre ordinateur.  
2. Dans **Actions**, cliquez sur **Planifier la sauvegarde**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-configure-vault/schedule-first-backup.png)
3. Dans l’Assistant Planifier la sauvegarde > **Prise en main**, cliquez sur **Suivant**.
4. Dans **Sélectionner les éléments à sauvegarder**, cliquez sur **Ajouter des éléments**.

    ![Sélectionner les éléments à sauvegarder](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. Dans **Sélectionner des éléments**, sélectionnez ce que vous souhaitez sauvegarder, puis cliquez sur **OK**.

    ![Éléments sélectionnés à sauvegarder](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. Dans **Sélectionner les éléments à sauvegarder**, cliquez sur **Suivant**.
7. Sur la page **Spécifier la planification de la sauvegarde**, indiquez à quel moment vous souhaitez effectuer des sauvegardes quotidiennes ou hebdomadaires. Cliquez ensuite sur **Suivant**.

    - Un point de récupération est créé lorsqu’une sauvegarde est effectuée.
    - Le nombre de points de récupération créés dans votre environnement dépend de votre planification de sauvegarde.


8. Vous pouvez planifier des sauvegardes quotidiennes, jusqu’à trois fois par jour. Par exemple, la capture d’écran montre deux sauvegardes quotidiennes, l’une à minuit et l’autre à 18h.

    ![Planification quotidienne](./media/backup-configure-vault/day-schedule.png)


9. Vous pouvez également exécuter des sauvegardes hebdomadaires. Par exemple, la capture d’écran montre des sauvegardes effectuées un dimanche et un mercredi sur deux à 9h30 et 1h.

    ![Planification hebdomadaire](./media/backup-configure-vault/week-schedule.png)


10. Sur la page **Sélectionner la stratégie de conservation**, spécifiez la façon dont vous stockez les copies historiques de vos données. Cliquez ensuite sur **Suivant**.

    - Les paramètres de conservation spécifient les points de récupération à stocker, ainsi que la durée pendant laquelle ils doivent être stockés.
    - Par exemple, lorsque vous définissez un paramètre de conservation quotidienne, vous indiquez qu’à l’heure spécifiée pour la conservation quotidienne, le dernier point de récupération sera conservé pendant le nombre de jours spécifié. Ou, autre exemple, vous pouvez spécifier une stratégie de conservation mensuelle pour indiquer que le point de récupération créé le 30 de chaque mois doit être stocké pendant 12 mois.
    - La conservation quotidienne et hebdomadaire des points de récupération coïncide généralement avec la planification de sauvegarde. Cela signifie que lorsque la sauvegarde est déclenchée en fonction de la planification, le point de récupération créé par la sauvegarde est stocké pendant la durée indiquée dans la stratégie de conservation quotidienne ou hebdomadaire.
    - Vous trouverez un exemple dans la capture d’écran suivante :

        -   Les sauvegardes quotidiennes effectuées à minuit et 18h sont conservées pendant sept jours.
        -   Les sauvegardes effectuées le samedi à minuit et 18h sont conservées pendant quatre semaines.
        -   Les sauvegardes effectuées le samedi de la dernière semaine du mois à minuit et 18h sont conservées pendant 12 mois.
        -   Les sauvegardes effectuées le samedi au cours de la dernière semaine du mois de mars sont conservées pendant 10 ans.

        ![Exemple de conservation](./media/backup-configure-vault/retention-example.png)


11. Dans **Choisir le type de sauvegarde initiale**, décidez si vous souhaitez effectuer la sauvegarde initiale sur le réseau ou utiliser la sauvegarde en mode hors connexion (pour plus d’informations sur la sauvegarde en mode hors connexion, consultez cet [article](offline-backup-azure-data-box.md)). Pour effectuer la sauvegarde initiale sur le réseau, sélectionnez **Sur le réseau automatiquement** et cliquez sur **Suivant**.

    ![Type de sauvegarde initiale](./media/backup-azure-manage-mars/choose-initial-backup-type.png)


12. Dans **Confirmation**, passez en revue les informations, puis cliquez sur **Terminer**.

    ![Confirmer le type de sauvegarde](./media/backup-azure-manage-mars/confirm-backup-type.png)


13. Lorsque l’Assistant a terminé la création de la planification de la sauvegarde, cliquez sur **Fermer**.

    ![Confirmer la modification du processus de sauvegarde](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Vous devez créer une stratégie sur chaque ordinateur sur lequel l’agent est installé.

### <a name="perform-the-initial-backup-offline"></a>Effectuer la sauvegarde initiale hors connexion

Vous pouvez exécuter une sauvegarde initiale automatiquement sur le réseau ou hors connexion. L’amorçage hors connexion d'une sauvegarde initiale s'avère utile en présence de nombreuses données nécessitant beaucoup de bande passante réseau à des fins de transfert. Pour effectuer un transfert hors connexion, procédez comme suit :

1. Vous écrivez les données de sauvegarde dans un emplacement intermédiaire.
2. Utilisez l'outil AzureOfflineBackupDiskPrep pour copier les données figurant dans l’emplacement intermédiaire sur un ou plusieurs disques SATA.
3. L’outil crée une tâche d’importation Azure. [Apprenez-en davantage](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) sur l’importation et l’exportation.
4. Vous envoyez les disques SATA à un centre de données Azure.
5. Dans le centre de données, les données de disque sont copiées vers un compte de stockage Azure.
6. Sauvegarde Azure copie les données de sauvegarde à partir du compte de stockage vers le coffre, et des sauvegardes incrémentielles sont planifiées.

[Apprenez-en davantage](offline-backup-azure-data-box.md) sur l'amorçage hors connexion.

### <a name="enable-network-throttling"></a>Activation de la limitation du réseau

Vous pouvez contrôler la manière dont la bande passante réseau est utilisée par l’agent MARS en activant la limitation de bande passante. Cette limitation vous est utile si vous devez sauvegarder des données pendant les heures de travail, mais souhaitez contrôler la quantité de bande passante utilisée pour l’activité de sauvegarde et de restauration.

* La fonctionnalité de limitation du réseau Sauvegarde Azure utilise implique la [qualité de Service (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) sur le système d’exploitation local.
* La limitation de bande passante pour la sauvegarde est disponible sur Windows Server 2012, Windows 8 et versions ultérieures. Les systèmes d’exploitation doivent exécuter les derniers Service Packs.

Pour activer la limitation du réseau, procédez comme suit :

1. Dans l'agent MARS, cliquez sur **Modifier les propriétés**.
2. Sous l’onglet **Limitation**, cochez la case **Activer la limitation de la bande passante sur Internet pour les opérations de sauvegarde**.

    ![Limitation du réseau](./media/backup-configure-vault/throttling-dialog.png)
3. Spécifiez la bande passante autorisée pendant et en dehors des heures de travail. Les valeurs de bande passante s'échelonnent de 512 Kbits/s à 1 023 Mbits/s. Cliquez ensuite sur **OK**.

## <a name="run-an-on-demand-backup"></a>Exécuter une sauvegarde à la demande

1. Dans l’agent MARS, cliquez sur **Sauvegarder maintenant**.

    ![Sauvegarder Windows Server maintenant](./media/backup-configure-vault/backup-now.png)

2. Si la version de l’agent MARS correspond à la version 2.0.9169.0 ou plus récente, une conservation personnalisée peut être définie. Dans la section **Conserver la sauvegarde jusqu'au**, choisissez une date dans le calendrier qui s'affiche :

   ![Calendrier de conservation de la sauvegarde](./media/backup-configure-vault/mars-ondemand.png)

3. Dans **Confirmation**, passez en revue les paramètres, puis cliquez sur **Sauvegarder**.
4. Cliquez sur **Fermer** pour fermer l’assistant. Si vous le faites avant le terme de la sauvegarde, l’Assistant continue de s’exécuter en arrière-plan.
5. Une fois la sauvegarde initiale terminée, le statut **Tâche terminée** apparaît dans la console Backup.

## <a name="on-demand-backup-policy-retention-behavior"></a>Comportement de conservation de la stratégie de sauvegarde à la demande

>[!NOTE]
>Applicable uniquement aux versions de l’agent MARS antérieures à la version 2.0.9169.0
>

* Pour plus d’informations, reportez-vous à l’étape 8 de [Créer une stratégie de sauvegarde](backup-configure-vault.md#create-a-backup-policy)

| Option de planification de la sauvegarde | Combien de temps les données sauvegardées sont-elles conservées ?
| -- | --
| Planifier une sauvegarde chaque : *Jour | **Conservation par défaut** : Correspondant à la « conservation en jours pour les sauvegardes quotidiennes » <br/><br/> **Exception** : si un jeu de sauvegarde planifiée quotidienne pour la conversation à long terme (semaines, mois, années) échoue, une sauvegarde à la demande déclenchée juste après l’échec de la sauvegarde planifiée est prise en compte à des fins de conservation à long terme. Dans le cas contraire, la sauvegarde planifiée suivante est prise en compte pour la conservation à long terme.<br/><br/> **Exemple** : si la sauvegarde planifiée effectuée le jeudi à 8h échoue et que la même sauvegarde a été prise en compte pour une conservation hebdomadaire/mensuelle/annuelle, la première sauvegarde à la demande déclenchée avant la sauvegarde planifiée suivante (par exemple, le vendredi à 8h) est automatiquement marquée à des fins de conservation hebdomadaire/mensuelle/annuelle comme applicable à la sauvegarde du jeudi à 8h.
| Planifier une sauvegarde chaque : *Hebdomadaire | **Conservation par défaut** : 1 jour <br/> Les sauvegardes à la demande effectuées pour une source de données avec une stratégie de sauvegarde hebdomadaire sont supprimées le jour suivant, même s’il s’agit des plus récentes sauvegardes de la source de données. <br/><br/> **Exception** : si un jeu de sauvegarde planifiée hebdomadaire pour la conservation à long terme (semaines, mois, années) échoue, une sauvegarde à la demande déclenchée juste après l’échec de la sauvegarde planifiée est prise en compte pour la conservation à long terme. Dans le cas contraire, la sauvegarde planifiée suivante est prise en compte pour la conservation à long terme. <br/><br/> **Exemple** : si la sauvegarde planifiée effectuée le jeudi à 8h échoue et que la même sauvegarde a été prise en compte pour la conservation mensuelle/annuelle, la première sauvegarde à la demande déclenchée avant la sauvegarde planifiée suivante (par exemple, le jeudi à 8h) est automatiquement marquée pour la conservation mensuelle/annuelle comme applicable à la sauvegarde du jeudi à 8h

## <a name="next-steps"></a>Étapes suivantes

[Découvrez comment restaurer des fichiers](backup-azure-restore-windows-server.md).
