---
title: Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services
description: Décrit comment sauvegarder des machines virtuelles Azure dans un coffre Recovery Services à l’aide de Sauvegarde Azure
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: f2954ad2693d7b4f56e3f1b33e804a6936cf8a65
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450146"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services

Cet article explique comment sauvegarder des machines virtuelles Azure dans un coffre Recovery Services à l’aide du service [Sauvegarde Azure](backup-overview.md).

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
>
> * Préparez les machines virtuelles Azure.
> * Créer un coffre.
> * Découvrir les machines virtuelles et configurer une stratégie de sauvegarde.
> * Activer la sauvegarde pour les machines virtuelles Azure.
> * Effectuez la sauvegarde initiale.

> [!NOTE]
> Cet article explique comment configurer un coffre et sélectionner les machines virtuelles à sauvegarder. Cette méthode s’avère utile pour sauvegarder plusieurs machines virtuelles. Vous pouvez également [sauvegarder une seule machine virtuelle Azure](backup-azure-vms-first-look-arm.md) directement à partir de ses paramètres.

## <a name="before-you-start"></a>Avant de commencer

* [Passez en revue](backup-architecture.md#architecture-built-in-azure-vm-backup) l’architecture de sauvegarde de machine virtuelle Azure.
* [En savoir plus sur](backup-azure-vms-introduction.md) la sauvegarde des machines virtuelles Azure et l’extension de la sauvegarde.
* [Passez en revue la matrice de prise en charge](backup-support-matrix-iaas.md) avant de configurer la sauvegarde.

Par ailleurs, vous risquez de devoir faire deux choses dans certaines circonstances :

* **Installer l’agent de machine virtuelle sur la machine virtuelle** : Sauvegarde Azure sauvegarde les machines virtuelles Azure en installant une extension à l’agent de machine virtuelle Azure en cours d’exécution sur l’ordinateur. Si votre machine virtuelle a été créée à partir d’une image de la Place de marché Azure, l’agent est installé et en cours d’exécution. Si vous créez une machine virtuelle personnalisée ou que vous migrez une machine locale, vous devrez peut-être [installer l’agent manuellement](#install-the-vm-agent).
* **Autoriser explicitement l’accès sortant** : En règle générale, vous n’avez pas besoin d’autoriser explicitement l’accès réseau sortant pour une machine virtuelle Azure afin qu’elle communique avec Sauvegarde Azure. Toutefois, certaines machines virtuelles peuvent rencontrer des problèmes de connexion, indiquant l’erreur **ExtensionSnapshotFailedNoNetwork** lors d’une tentative de connexion. Si cela se produit, vous devez [autoriser explicitement l’accès sortant](#explicitly-allow-outbound-access) afin que l’extension Sauvegarde Azure puisse communiquer avec des adresses IP publiques Azure pour le trafic de sauvegarde.

## <a name="create-a-vault"></a>Création d'un coffre

 Un coffre stocke les sauvegardes et points de récupération créés au fil du temps, ainsi que les stratégies de sauvegarde associées aux machines sauvegardées. Créez un coffre comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans Rechercher, tapez **Recovery Services**. Sous **Services**, cliquez sur **Coffres Recovery Services**.

     ![Rechercher les coffres Recovery Services](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

3. Dans le menu **Coffres Recovery Services**, cliquez sur **+Ajouter**.

     ![Créer un coffre Recovery Services - Étape 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. Dans **Coffre Recovery Services**, tapez un nom convivial permettant d’identifier le coffre.
    * Le nom doit être unique pour l’abonnement Azure.
    * Il peut comprendre entre 2 et 50 caractères.
    * Il doit commencer par une lettre, et ne peut contenir que des lettres, des chiffres et des traits d’union.
5. Sélectionnez l’abonnement Azure, le groupe de ressources et la région géographique où le coffre doit être créé. Cliquez ensuite sur **Créer**.
    * La création du coffre peut prendre du temps.
    * Surveillez les notifications d’état dans l’angle supérieur droit du portail.

Une fois le coffre créé, il apparaît dans la liste Coffres Recovery Services. Si vous ne voyez pas votre coffre, sélectionnez **Actualiser**.

![Liste des archivages de sauvegarde](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> Il est désormais possible de personnaliser le nom du groupe de ressources créé par le service Sauvegarde Azure. Pour plus d’informations, consultez [Groupe de ressources Sauvegarde Azure pour les machines virtuelles](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

### <a name="modify-storage-replication"></a>Modifier la réplication du stockage

Par défaut, les coffres utilisent le [stockage géoredondant (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Si le coffre est votre principal mécanisme de sauvegarde, nous vous recommandons d’utiliser le GRS.
* Vous pouvez utiliser le [stockage localement redondant (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) en guise d’option plus économique.

Modifiez le type de réplication de stockage comme suit :

1. Dans le nouveau coffre, cliquez sur **Propriétés** dans la section **Paramètres**.
2. Dans **Propriétés**, sous **Configuration de la sauvegarde**, cliquez sur **Mise à jour**.
3. Sélectionnez le type de réplication de stockage, puis cliquez sur **Enregistrer**.

      ![Définir la configuration de stockage du nouveau coffre](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > Vous ne pouvez pas modifier le type de réplication de stockage une fois que le coffre est configuré et qu’il contient des éléments de sauvegarde. Pour ce faire, vous devez recréer le coffre.

## <a name="apply-a-backup-policy"></a>Appliquer une stratégie de sauvegarde

Configurez une stratégie de sauvegarde pour le coffre.

1. Dans le coffre, cliquez sur **+Sauvegarde** dans la section **Vue d’ensemble**.

   ![Bouton de sauvegarde](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. Dans **Objectif de sauvegarde** > **Où s’exécute votre charge de travail ?** , sélectionnez **Azure**. Dans **Que voulez-vous sauvegarder ?** , sélectionnez **Machine virtuelle** >  **OK**. L’extension de machine virtuelle est inscrite dans le coffre.

   ![Volets Sauvegarde et Objectif de sauvegarde](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. Dans **Stratégie de sauvegarde**, sélectionnez la stratégie à associer au coffre.
    * La stratégie par défaut sauvegarde la machine virtuelle une fois par jour. Les sauvegardes quotidiennes sont conservées pendant 30 jours. Les instantanés de récupération instantanée sont conservés pendant deux jours.
    * Si vous ne souhaitez pas utiliser la stratégie par défaut, sélectionnez **Créer** et créez une stratégie personnalisée comme indiqué dans la prochaine procédure.

      ![Stratégie de sauvegarde par défaut](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. Dans **Sélectionner des machines virtuelles**, sélectionnez les machines virtuelles que vous souhaitez sauvegarder à l’aide de la stratégie. Cliquez ensuite sur **OK**.

   * Les machines virtuelles sélectionnées sont validées.
   * Vous pouvez uniquement sélectionner les machines virtuelles situées dans la même région que le coffre.
   * Vous pouvez sauvegarder des machines virtuelles uniquement dans un même coffre.

     ![Volet Sélectionner les machines virtuelles](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. Dans **Sauvegarde**, cliquez sur **Activer la sauvegarde**. Cette action permet de déployer la stratégie dans le coffre et sur les machines virtuelles, puis d’installer l’extension de sauvegarde sur l’agent de machine virtuelle en cours d’exécution sur la machine virtuelle Azure.

     ![Bouton Activer la sauvegarde](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Après avoir activé la sauvegarde :

* Il installe l’extension de sauvegarde, que la machine virtuelle soit ou non en cours d’exécution.
* Une sauvegarde initiale s’exécute conformément à votre planification de sauvegarde.
* Lors de l’exécution des sauvegardes, notez que :
  * Une machine virtuelle en cours d’exécution a le plus de chance de capturer un point de récupération cohérent au niveau applicatif.
  * Toutefois, même si la machine virtuelle est désactivée, elle est sauvegardée. Une machine virtuelle de ce type est appelée machine virtuelle en mode hors connexion. Dans ce cas, le point de récupération est cohérent en cas de plantage.

### <a name="create-a-custom-policy"></a>Créer une stratégie personnalisée

Si vous avez choisi de créer une stratégie de sauvegarde, renseignez les paramètres de stratégie.

1. Dans **Nom de la stratégie**, spécifiez un nom explicite.
2. Dans **Planification de sauvegarde**, spécifiez quand les sauvegardes doivent être effectuées. Vous pouvez effectuer des sauvegardes quotidiennes ou hebdomadaires pour les machines virtuelles Azure.
3. Dans **Restauration instantanée**, spécifiez la durée pendant laquelle vous souhaitez conserver les instantanés localement en vue d’une restauration instantanée.
    * Quand vous effectuez une restauration, les disques de la machine virtuelle sauvegardée sont copiés depuis le stockage vers l’emplacement de stockage de récupération, via le réseau. Avec la restauration instantanée, vous pouvez tirer parti des instantanés stockés localement effectués pendant un travail de sauvegarde, sans attendre que les données de sauvegarde soient transférées vers le coffre.
    * Vous pouvez conserver les instantanés en vue de la restauration instantanée entre un à cinq jours. Le paramétrage par défaut correspond à deux jours.
4. Dans **Durée de rétention**, spécifiez la durée pendant laquelle vous souhaitez conserver vos points de sauvegarde quotidiens ou hebdomadaires.
5. Dans **Rétention du point de sauvegarde mensuel**, spécifiez si vous souhaitez conserver une sauvegarde mensuelle de vos sauvegardes quotidiennes ou hebdomadaires.
6. Cliquez sur **OK** pour enregistrer les modifications.

    ![Nouvelle stratégie de sauvegarde](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Sauvegarde Azure ne prend pas en charge l’ajustement automatique de l’horloge lors du passage à l’heure d’été pour les sauvegardes de machines virtuelles Azure. Au moment des changements horaires, modifiez les stratégies de sauvegarde manuellement selon les besoins.

## <a name="trigger-the-initial-backup"></a>Déclencher la sauvegarde initiale

La sauvegarde initiale s’exécutera conformément à la planification, mais vous pouvez l’exécuter immédiatement comme suit :

1. Dans le menu du coffre, cliquez sur **Éléments de sauvegarde**.
2. Sur **Éléments de sauvegarde**, cliquez sur **Machine virtuelle Azure**.
3. Dans la liste **Éléments de sauvegarde**, cliquez sur le bouton de sélection (...).
4. Cliquez sur **Sauvegarder maintenant**.
5. Dans **Sauvegarder maintenant**, utilisez le contrôle de calendrier pour sélectionner le dernier jour de rétention du point de récupération. Cliquez ensuite sur **OK**.
6. Surveiller les notifications du portail. Vous pouvez surveiller la progression du travail dans le tableau de bord du coffre > **Travaux de sauvegarde** > **En cours d’exécution**. Selon la taille de votre machine virtuelle, la création de la sauvegarde initiale peut prendre un certain temps.

## <a name="verify-backup-job-status"></a>Vérifier l’état du travail de sauvegarde

Les détails du travail de sauvegarde pour chaque sauvegarde de machine virtuelle se composent de deux phases : la phase **Capture instantanée**, suivie de la phase **Transférer les données vers le coffre**.<br/>
La phase de capture instantanée garantit la disponibilité d’un point de récupération stocké avec les disques pour les **restaurations instantanées**, et les captures instantanées sont disponibles pendant au maximum cinq jours en fonction de la conservation des captures instantanées configurée par l’utilisateur. Le transfert des données vers le coffre crée un point de récupération dans le coffre pour la conservation à long terme. Le transfert des données vers le coffre ne démarre qu’une fois la phase de prise d’instantané terminée.

  ![État du travail de sauvegarde](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Il existe deux **sous-tâches** en cours d’exécution sur le back-end, dont une pour le travail de sauvegarde front-end que vous pouvez consulter à partir du panneau des détails du **travail de sauvegarde**, comme indiqué ci-dessous :

  ![État du travail de sauvegarde](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

La phase **Transférer les données vers le coffre** peut prendre plusieurs jours selon la taille des disques, l’activité par disque et plusieurs autres facteurs.

L’état du travail peut varier selon les scénarios suivants :

**Instantané** | **Transférer les données vers le coffre** | **État du travail**
--- | --- | ---
Completed | En cours | En cours
Completed | Ignoré | Completed
Completed | Completed | Completed
Completed | Échec | Terminé avec un avertissement
Échec | Échec | Échec

Désormais, avec cette fonctionnalité, pour la même machine virtuelle, deux sauvegardes peuvent s’exécuter en parallèle, mais dans chaque phase (prise d’instantané, transfert des données vers le coffre), une seule sous-tâche peut être en cours d’exécution. Ainsi, les scénarios où un travail de sauvegarde en cours entraîne l’échec de la sauvegarde du jour suivant sont évités grâce à cette fonctionnalité de découplage. Les sauvegardes du jour suivant peuvent voir la réalisation de la phase de prise d’instantané, mais pas celle de la phase **Transférer les données vers le coffre**, si le travail de sauvegarde d’un jour précédent est dans l’état en cours.
Le point de récupération incrémentielle créé dans le coffre capture toute l’évolution depuis le dernier point de récupération créé dans le coffre. Il n’y a aucun impact sur l’utilisateur concernant les coûts.

## <a name="optional-steps-install-agentallow-outbound"></a>Étapes facultatives (installer l’agent/autoriser le trafic sortant)

### <a name="install-the-vm-agent"></a>Installer l’agent de machine virtuelle

Sauvegarde Azure sauvegarde les machines virtuelles Azure en installant une extension à l’agent de machine virtuelle Azure en cours d’exécution sur l’ordinateur. Si votre machine virtuelle a été créée à partir d’une image de la Place de marché Azure, l’agent est installé et en cours d’exécution. Si vous créez une machine virtuelle personnalisée ou que vous migrez une machine locale, vous devrez peut-être installer l’agent manuellement, comme le récapitule le tableau.

**Machine virtuelle** | **Détails**
--- | ---
**Windows** | 1. [Téléchargez et installez](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) le fichier MSI de l’agent.<br/><br/> 2. Installez-le avec les autorisations d’administrateur pour l’ordinateur.<br/><br/> 3. Vérifiez l’installation. Dans *C:\WindowsAzure\Packages* sur la machine virtuelle, cliquez avec le bouton droit sur **WaAppAgent.exe** > **Propriétés**. Sous l’onglet **Détails**, la **version du produit** doit être 2.6.1198.718 ou une version ultérieure.<br/><br/> Si vous mettez à jour l’agent, veillez à ce qu’aucune opération de sauvegarde ne soit en cours d’exécution et [réinstallez l’agent](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Effectuez l’installation à l’aide d’un package RPM ou DEB à partir du dépôt de packages de votre distribution. Il s’agit de la méthode recommandée pour installer et mettre à niveau l’agent Linux Azure. Tous les [fournisseurs de distribution approuvés](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) intègrent l’agent Azure Linux dans leurs images et référentiels. L’agent est disponible sur [GitHub](https://github.com/Azure/WALinuxAgent), mais nous ne recommandons pas d’effectuer une installation depuis ce site.<br/><br/> Si vous mettez à jour l’agent, veillez à ce qu’aucune opération de sauvegarde ne soit en cours d’exécution et mettez à jour les fichiers binaires.

### <a name="explicitly-allow-outbound-access"></a>Autoriser explicitement l’accès sortant

L’extension de sauvegarde en cours d’exécution sur la machine virtuelle doit disposer d’un accès sortant vers des adresses IP publiques Azure.

* En règle générale, vous n’avez pas besoin d’autoriser explicitement l’accès réseau sortant pour une machine virtuelle Azure afin qu’elle communique avec Sauvegarde Azure.
* Si vous rencontrez des problèmes de connexion avec les machines virtuelles ou que vous voyez l’erreur **ExtensionSnapshotFailedNoNetwork** quand vous tentez de vous connecter, vous devez autoriser explicitement l’accès afin que l’extension de sauvegarde puisse communiquer avec les adresses IP publiques Azure pour le trafic de sauvegarde. Les méthodes d’accès sont récapitulées dans le tableau ci-dessous.

**Option** | **Action** | **Détails**
--- | --- | ---
**Définir des règles de groupe de sécurité réseau** | autorisez les [plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/details.aspx?id=41653).<br/><br/> Au lieu d’autoriser et de gérer chaque plage d’adresses, vous pouvez ajouter une règle qui autorise l’accès au service Sauvegarde Azure en utilisant une [balise de service](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure). | [En savoir plus](../virtual-network/security-overview.md#service-tags) sur les balises de service.<br/><br/> Les balises de service simplifient la gestion des accès et n’entraînent pas de frais supplémentaires.
**Déployer un proxy** | Déployer un serveur de proxy HTTP pour acheminer le trafic. | Fournit un accès à l’ensemble d’Azure et pas seulement au stockage.<br/><br/> Le contrôle granulaire des URL de stockage est autorisé.<br/><br/> Un seul point d’accès Internet pour les machines virtuelles.<br/><br/> Frais supplémentaires pour le proxy.
**Configurer le pare-feu Azure** | autorisez le trafic via le Pare-feu Azure sur la machine virtuelle, en utilisant une balise FQDN pour le service Sauvegarde Azure | Simple à utiliser si le Pare-feu Azure est configuré dans un sous-réseau de réseau virtuel.<br/><br/> Vous ne pouvez pas créer vos propres balises FQDN ou modifier les FQDN dans une balise.<br/><br/> Si vos machines virtuelles Azure ont des disques managés, vous devrez peut-être ouvrir un port supplémentaire (8443) sur les pare-feu.

#### <a name="establish-network-connectivity"></a>Établir la connectivité réseau

Établissez une connexion avec le groupe de sécurité réseau, par proxy ou via le pare-feu.

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Configurer une règle de groupe de sécurité réseau pour autoriser l’accès sortant vers Azure

Si un groupe de sécurité réseau gère l’accès aux machines virtuelles, autorisez l’accès sortant pour le stockage de sauvegarde vers les plages et ports nécessaires.

1. Dans les propriétés de la machine virtuelle, sélectionnez **Réseau**, puis sélectionnez **Ajouter une règle de port de sortie**.
2. Dans **Ajouter une règle de sécurité de trafic sortant**, sélectionnez **Avancé**.
3. Dans **Source**, sélectionnez **VirtualNetwork**.
4. Dans **Plages de ports sources**, entrez un astérisque (*) pour autoriser l’accès sortant à partir de n’importe quel port.
5. Dans **Destination**, sélectionnez **Balise de service**. Dans la liste, sélectionnez **Storage.region**. La région est là où se trouvent le coffre et les machines virtuelles que vous souhaitez sauvegarder.
6. Dans **Plages de ports de destination**, sélectionnez le port.
    * Machine virtuelle utilisant des disques non managés avec compte de stockage non chiffré : 80
    * Machine virtuelle utilisant des disques non managés avec compte de stockage chiffré : 443 (paramètre par défaut)
    * Machine virtuelle utilisant des disques managés : 8443
7. Dans **Protocole**, sélectionnez **TCP**.
8. Dans **Priorité**, spécifiez une valeur inférieure à toute règle de refus supérieure.

   Si vous avez une règle qui refuse l’accès, la nouvelle règle qui l’autorise doit être plus haute. Par exemple, si vous avez une règle **Deny_All** définie au niveau de priorité 1 000, votre nouvelle règle doit avoir une valeur inférieure à 1 000.
9. Indiquez un nom et une description pour la règle, puis sélectionnez **OK**.

Vous pouvez appliquer la règle de groupe de sécurité réseau à plusieurs machines virtuelles pour autoriser l’accès sortant. Cette vidéo vous guide tout au long du processus.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]

##### <a name="route-backup-traffic-through-a-proxy"></a>Router le trafic de sauvegarde par le biais d’un proxy

Vous pouvez router le trafic de sauvegarde par le biais d’un proxy, puis octroyer au proxy l’accès aux plages Azure nécessaires. Configurez la machine virtuelle proxy de manière à autoriser ce qui suit :

* La machine virtuelle Azure route tout le trafic HTTP destiné à l’Internet public par le biais du proxy.
* Le proxy doit autoriser le trafic entrant provenant des machines virtuelles situées dans le réseau virtuel applicable.
* Le **verrouillage NSF** du groupe de sécurité réseau a besoin d’une règle qui autorise le trafic Internet sortant depuis la machine virtuelle proxy.

###### <a name="set-up-the-proxy"></a>Configurer le proxy

Si vous n’avez pas de proxy de compte système, configurez-en un comme suit :

1. Téléchargez [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Exécutez **PsExec.exe -i -s cmd.exe** pour exécuter l’invite de commandes sous un compte système.
3. Exécutez le navigateur dans le contexte système. Par exemple, utilisez **%PROGRAMFILES%\Internet Explorer\iexplore.exe** pour Internet Explorer.  
4. Définissez les paramètres du proxy.
   * Sur des machines Linux :
     * Ajoutez cette ligne au fichier **/etc/environnement** :
       * **http_proxy=http:\//adresse IP proxy:port proxy**
     * Ajoutez ces lignes au fichier **/etc/waagent.conf** :
         * **HttpProxy.Host=proxy IP address**
         * **HttpProxy.Port=proxy port**
   * Sur des machines Windows, dans les paramètres du navigateur, spécifiez qu’un proxy doit être utilisé. Si vous utilisez actuellement un proxy sur un compte d'utilisateur, vous pouvez utiliser ce script pour appliquer le paramètre au niveau du compte système.

       ```powershell
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

###### <a name="allow-incoming-connections-on-the-proxy"></a>Autoriser les connexions entrantes sur le proxy

Autorisez les connexions entrantes dans les paramètres du proxy.

1. Dans le Pare-feu Windows, ouvrez **Pare-feu Windows avec fonctions avancées de sécurité**.
2. Cliquez avec le bouton droit sur **Règles de trafic entrant** > **Nouvelle règle**.
3. Dans **Type de règle**, sélectionnez **Personnalisé** > **Suivant**.
4. Dans **Programme**, sélectionnez **Tous les programmes** > **Suivant**.
5. Dans **Protocoles et ports** :
   * Définissez le type sur **TCP**.
   * Définissez **Ports locaux** sur **Ports spécifiques**.
   * Définissez **Port distant** sur **Tous les ports**.

6. Terminez l’Assistant et spécifiez un nom pour la règle.

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Ajouter une règle d’exception au groupe de sécurité réseau pour le proxy

Dans le **verrouillage NSF** du groupe de sécurité réseau, autorisez le trafic provenant de tout port 10.0.0.5 vers n’importe quelle adresse Internet sur le port 80 (HTTP) ou 443 (HTTPS).

Le script PowerShell suivant fournit un exemple permettant d’autoriser le trafic.
Au lieu d’autoriser le trafic sortant vers toutes les adresses Internet publiques, vous pouvez spécifier une plage d’adresses IP (`-DestinationPortRange`) ou utiliser la balise de service storage.region.

```powershell
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

##### <a name="allow-firewall-access-with-an-fqdn-tag"></a>Autoriser l’accès à travers le pare-feu avec une balise FQDN

Vous pouvez configurer Pare-feu Azure afin d’autoriser l’accès sortant du trafic réseau vers Sauvegarde Azure.

* [Découvrez-en plus](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) sur le déploiement du Pare-feu Azure.
* [Découvrez](https://docs.microsoft.com/azure/firewall/fqdn-tags) les balises FQDN.

## <a name="next-steps"></a>Étapes suivantes

* Résolvez les problèmes liés aux [agents de machine virtuelle Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) ou à la [sauvegarde de machine virtuelle Azure](backup-azure-vms-troubleshoot.md).
* [Restaurez](backup-azure-arm-restore-vms.md) des machines virtuelles Azure.
