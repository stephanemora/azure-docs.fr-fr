---
title: Préparer la sauvegarde de machines virtuelles Azure avec Sauvegarde Azure
description: Décrit comment préparer des machines virtuelles Azure à la sauvegarde avec le service Sauvegarde Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: raynew
ms.openlocfilehash: ee7a9c407a26f9334a854c98793db8fc01244e2a
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994672"
---
# <a name="prepare-to-back-up-azure-vms"></a>Préparer la sauvegarde de machines virtuelles Azure

Cet article explique comment préparer la sauvegarde d’une machine virtuelle Azure à l’aide d’un coffre Recovery Services du service [Sauvegarde Azure](backup-introduction-to-azure-backup.md). La préparation de la sauvegarde inclut les étapes suivantes :


> [!div class="checklist"]
> * Avant de commencer, passer en revue les scénarios pris en charge et les limitations.
> * Vérifier les prérequis, notamment les exigences imposées aux machines virtuelles Azure et la connectivité réseau.
> * Créer un coffre.
> * Sélectionner le mode de réplication du stockage.
> * Découvrir les machines virtuelles, configurer les paramètres et la stratégie de sauvegarde.
> * Activer la sauvegarde pour les machines virtuelles sélectionnées.


> [!NOTE]
   > Cet article décrit comment sauvegarder des machines virtuelles Azure en configurant un coffre et en sélectionnant les machines virtuelles à sauvegarder. Cette méthode s’avère utile pour sauvegarder plusieurs machines virtuelles. Vous pouvez également sauvegarder une machine virtuelle Azure directement à partir de ses paramètres. [En savoir plus](backup-azure-vms-first-look-arm.md)

## <a name="before-you-start"></a>Avant de commencer

1. [Obtenez une vue d’ensemble](backup-azure-vms-introduction.md) du service Sauvegarde Azure pour les machines virtuelles Azure.
2. Passez en revue les détails de prise en charge et les limitations ci-dessous.

   **Prise en charge/Limitation** | **Détails**
   --- | ---
   **Système d’exploitation Windows** | Windows Server 2008 R2 64 bits ou version ultérieure.<br/><br/> Client Windows 7 64 bits ou version ultérieure.
   **Système d’exploitation Linux** | Vous pouvez sauvegarder des distributions Linux 64 bits [prises en charge par Azure](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), à l’exception de CoreOS Linux.<br/><br/> Passez en revue les systèmes d’exploitation Linux qui [prennent en charge la restauration de fichiers](backup-azure-restore-files-from-vm.md#for-linux-os).<br/><br/> Les autres distributions Linux peuvent fonctionner, tant que l’agent de machine virtuelle est disponible sur la machine virtuelle et que Python est pris en charge. Toutefois, ces distributions ne sont pas prises en charge.
   **Région** | Vous pouvez sauvegarder des machines virtuelles Azure dans toutes les [régions prises en charge](https://azure.microsoft.com/regions/#services). Si une région n’est pas prise en charge, vous ne pourrez pas la sélectionner quand vous créerez le coffre.<br/><br/> Vous ne pouvez pas sauvegarder et restaurer d’une région Azure à l’autre. Vous pouvez le faire uniquement au sein d’une même région.
   **Limite des disques de données** | Vous ne pouvez pas sauvegarder des machines virtuelles dotées de plus de 16 disques de données.
   **Stockage partagé** | Nous déconseillons la sauvegarde de machines virtuelles à l’aide d’un volume partagé de cluster ou d’un serveur de fichiers avec montée en puissance parallèle. En effet, il existe un risque d’échec pour les enregistreurs de volumes partagés de cluster.
   **Chiffrement Linux** | La sauvegarde de machines virtuelles Linux chiffrées selon le standard LUKS (Linux Unified Key Setup) n’est pas prise en charge.
   **Cohérence de machine virtuelle** | Sauvegarde Azure ne prend pas en charge la cohérence multimachine virtuelle.
   **Mise en réseau** | Les données sauvegardées n’incluent pas les lecteurs réseau montés attachés à une machine virtuelle.<br/><br/>
   **Instantanés** | La prise d’instantanés sur un disque avec Accélérateur d’écriture n’est pas prise en charge. En effet, elle empêche le service Sauvegarde Azure de créer un instantané cohérent dans les applications de tous les disques de machine virtuelle.
   **PowerShell** | Plusieurs actions sont uniquement disponibles avec PowerShell :<br/><br/> - Restauration de machines virtuelles gérées par des équilibreurs de charge interne/externe ou avec plusieurs adresses IP réservées ou cartes. [En savoir plus](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)<br/><br/> - Restauration d’une machine virtuelle de contrôleur de domaine dans une configuration à plusieurs contrôleurs de domaine. [Plus d’informations](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)
   **Heure système** | Sauvegarde Azure ne prend pas en charge l’ajustement automatique de l’horloge lors du passage à l’heure d’été pour les sauvegardes de machines virtuelles Azure. Modifiez les stratégies de sauvegarde manuellement selon les besoins.
   **Comptes de stockage** | Si vous utilisez un compte de stockage réseau restreint, vérifiez que vous activez **Autoriser les services Microsoft de confiance à accéder à ce compte de stockage** afin que le service Sauvegarde Azure puisse accéder au compte. La récupération au niveau élément n’est pas prise en charge pour les comptes de stockage réseau restreints.<br/><br/> Dans un compte de stockage, vérifiez que les paramètres des **pare-feu et réseaux virtuels** autorisent l’accès à partir de **tous les réseaux**.


## <a name="prerequisites"></a>Prérequis

- Vous devez créer le coffre dans la même région que les machines virtuelles Azure à sauvegarder.
- Identifiez les régions des machines virtuelles Azure avant de commencer.
    - Si vous avez des machines virtuelles dans plusieurs régions, créez un coffre dans chaque région.
    - Vous n’avez pas besoin de spécifier des comptes de stockage dans lesquels héberger les données de sauvegarde. Le coffre et le service Sauvegarde Azure gèrent cela automatiquement.
- Vérifiez que l’agent de machine virtuelle est installé sur les machines virtuelles Azure que vous souhaitez sauvegarder.



### <a name="install-the-vm-agent"></a>Installer l’agent de machine virtuelle

Pour activer la sauvegarde, Sauvegarde Azure installe une extension de sauvegarde (VM Snapshot ou VM Snapshot Linux) sur l’agent de machine virtuelle qui s’exécute sur la machine virtuelle Azure.
    -  L’agent de machine virtuelle Azure est installé par défaut sur les machines virtuelles Windows qui sont déployées à partir d’une image Place de marché Azure. Lorsque vous déployez une image Place de marché Azure à partir du portail, de PowerShell, de l’interface de ligne de commande ou d’un modèle Azure Resource Manager, vous installez également l’agent de machine virtuelle Azure.
    - Si vous avez migré une machine virtuelle localement, l’agent n’est pas installé. Vous devez l’installer avant d’activer la sauvegarde pour la machine virtuelle.

Au besoin, installez l’agent comme suit.

**Machine virtuelle** | **Détails**
--- | ---
**Machines virtuelles Windows** | [Téléchargez et installez](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) l’agent avec des autorisations d’administrateur sur la machine.<br/><br/> Pour vérifier l’installation, dans *C:\WindowsAzure\Packages* sur la machine virtuelle, cliquez avec le bouton droit sur WaAppAgent.exe > **Propriétés**, > Onglet **Détails**. La **version du produit** doit être 2.6.1198.718 ou une version ultérieure.
**Machines virtuelles Linux** | L’installation à l’aide d’un package RPM ou DEB à partir de votre référentiel de packages de distribution est la méthode privilégiée pour installer et mettre à niveau l’agent Microsoft Linux Azure. Tous les [fournisseurs de distribution approuvés](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) intègrent l’agent Azure Linux dans leurs images et référentiels. L’agent est disponible sur [GitHub](https://github.com/Azure/WALinuxAgent), mais nous ne recommandons pas d’effectuer une installation depuis ce site.
Si vous rencontrez des problèmes de sauvegarde de la machine virtuelle Azure, utilisez le tableau ci-dessous pour vérifier que l’agent de machine virtuelle Azure est correctement installé sur celle-ci. Le tableau fournit des informations supplémentaires sur l’agent de machine virtuelle pour les machines virtuelles Windows et Linux.

### <a name="establish-network-connectivity"></a>Établir la connectivité réseau

L’extension de sauvegarde en cours d’exécution sur la machine virtuelle doit avoir un accès sortant vers des adresses IP publiques Azure. Pour autoriser cet accès, vous pouvez effectuer les étapes suivantes :


- **Règles de groupe de sécurité réseau** : autorisez les [plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/details.aspx?id=41653). Vous pouvez ajouter une règle qui autorise l’accès au service Sauvegarde Azure en utilisant une [balise de service](../virtual-network/security-overview.md#service-tags), au lieu d’autoriser individuellement chaque plage d’adresses et de les gérer au fil du temps.
- **Proxy** : Déployer un serveur de proxy HTTP pour acheminer le trafic.
- **Pare-feu Azure** : autorisez le trafic qui passe par le Pare-feu Azure sur la machine virtuelle, à l’aide d’une balise de nom de domaine complet pour le service Sauvegarde Azure.

Envisagez un compromis pour déterminer votre option.

**Option** | **Avantages** | **Inconvénients**
--- | --- | ---
**Groupe de sécurité réseau** | Aucun coût supplémentaire Simple à gérer avec des balises de service. | Fournit un accès à l’ensemble d’Azure et pas seulement au stockage. |
**Proxy HTTP** | Le contrôle granulaire des URL de stockage est autorisé.<br/><br/> Un seul point d’accès Internet pour les machines virtuelles.<br/><br/> Frais supplémentaires pour le proxy.
**Balises FQDN** | Simple à utiliser si le Pare-feu Azure est configuré dans un sous-réseau de réseau virtuel. | Impossible de créer vos propres balises FQDN ou de modifier les FQDN dans une balise.



Si vous utilisez Azure Managed Disks, vous devrez peut-être ouvrir un port supplémentaire (le port 8443) sur les pare-feu.



### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Configurer une règle de groupe de sécurité réseau pour autoriser l’accès sortant vers Azure

Si l’accès de votre machine virtuelle Azure est géré par un groupe de sécurité réseau, autorisez l’accès sortant pour le stockage de sauvegarde vers les plages et ports nécessaires.



1. Dans la machine virtuelle > **Mise en réseau**, cliquez sur **Ajouter une règle de port de sortie**.
- Si vous avez une règle qui refuse l’accès, la nouvelle règle qui l’autorise doit être plus haute. Par exemple, si vous avez une règle **Deny_All** définie au niveau de priorité 1 000, votre nouvelle règle doit avoir une valeur inférieure à 1 000.
2. Dans **Ajouter une règle de sécurité de trafic sortant**, cliquez sur **Avancé**.
3. Dans Source, sélectionnez **VirtualNetwork**.
4. Dans **Plages de ports sources**, tapez un astérisque (*) pour autoriser l’accès sortant à partir de n’importe quel port.
5. Dans **Destination**, sélectionnez **Balise de service**. Dans la liste, sélectionnez Stockage.<region>. La région correspond à la région dans laquelle se trouvent le coffre et les machines virtuelles que vous souhaitez sauvegarder.
6. Dans **Plages de ports de destination**, sélectionnez le port.

    - Machine virtuelle avec des disques non managés et un compte de stockage non chiffré : 80
    - Machine virtuelle avec des disques non managés et un compte de stockage chiffré : 443 (paramètre par défaut)
    - Machine virtuelle gérée : 8443
1. Dans **Protocole**, sélectionnez **TCP**.
2. Dans **Priorité**, attribuez une valeur inférieure à toute règle de refus située plus haut.
3. Indiquez un nom et une description pour la règle, puis cliquez sur **OK**.

Vous pouvez appliquer la règle de groupe de sécurité réseau à plusieurs machines virtuelles pour autoriser l’accès sortant vers Azure pour Sauvegarde Azure.

Cette vidéo vous guide tout au long du processus.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]



### <a name="route-backup-traffic-through-a-proxy"></a>Router le trafic de sauvegarde par le biais d’un proxy

Vous pouvez router le trafic de sauvegarde par le biais d’un proxy, puis octroyer au proxy l’accès aux plages Azure nécessaires.

Vous devez configurer votre machine virtuelle proxy de sorte à autoriser ce qui suit :

- La machine virtuelle Azure route tout le trafic HTTP destiné à l’Internet public par le biais du proxy.
- Le proxy doit autoriser le trafic entrant provenant des machines virtuelles situées dans le réseau virtuel applicable.
- Le **verrouillage NSF** du groupe de sécurité réseau a besoin d’une règle qui autorise le trafic Internet sortant depuis la machine virtuelle proxy.

Voici comment vous devez configurer le proxy. Nous utilisons des exemples de valeurs. Vous devez les remplacer par les vôtres.

#### <a name="set-up-a-system-account-proxy"></a>Configurer un proxy de compte système
Si vous n’avez pas de proxy de compte système, configurez-en un comme suit :

1. Téléchargez [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Exécutez **PsExec.exe -i -s cmd.exe** pour exécuter l’invite de commandes sous un compte système.
3. Exécutez le navigateur dans le contexte système. Par exemple :  **ProgramFiles%\Internet Explorer\iexplore.exe** pour Internet Explorer.  
4. Définissez les paramètres du proxy.
    - Sur des machines Linux :
        - Ajoutez cette ligne au fichier **/etc/environnement** :
            - **http_proxy=http://proxy IP address:proxy port**
        - Ajoutez ces lignes au fichier **/etc/waagent.conf** :
            - **HttpProxy.Host=proxy IP address**
            - **HttpProxy.Port=proxy port**
    - Sur des machines Windows, dans les paramètres du navigateur, spécifiez qu’un proxy doit être utilisé. Si vous utilisez actuellement un proxy sur un compte d’utilisateur, vous pouvez utiliser ce script pour appliquer le paramètre au niveau du compte système.
        ```
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

        ```

#### <a name="allow-incoming-connections-on-the-proxy"></a>Autoriser les connexions entrantes sur le proxy

1. Autorisez les connexions entrantes dans les paramètres du proxy.
2. Par exemple, ouvrez le **Pare-feu Windows avec fonctions avancées de sécurité**.
    - Cliquez avec le bouton droit sur **Règles de trafic entrant** > **Nouvelle règle**.
    - Dans **Type de règle**, sélectionnez **Personnalisé** > **Suivant**.
    - Dans **Programme**, sélectionnez **Tous les programmes** > **Suivant**.
    - Dans **Protocoles et ports**, définissez le type sur **TCP**, **Ports locaux** sur **Ports spécifiques** et **Port distant** sur **Tous les ports**.
    - Terminez l’Assistant et spécifiez un nom pour la règle.

#### <a name="add-an-exception-rule-to-the-nsg"></a>Ajouter une règle d’exception au groupe de sécurité réseau

Dans le **verrouillage NSF** du groupe de sécurité réseau, autorisez le trafic provenant de tout port 10.0.0.5 vers n’importe quelle adresse Internet sur le port 80 (HTTP) ou 443 (HTTPS).

- Le script PowerShell suivant fournit un exemple permettant d’autoriser le trafic.
- Au lieu d’autoriser le trafic sortant vers toutes les adresses Internet publiques, vous pouvez spécifier une plage d’adresses IP (-DestinationPortRange) ou utiliser la balise de service storage.region.   

    ```
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```
### <a name="allow-firewall-access-with-fqdn-tag"></a>Autoriser l’accès à travers le pare-feu avec une balise FQDN

Vous pouvez configurer le Pare-feu Azure afin d’autoriser l’accès sortant du trafic réseau vers Sauvegarde Azure.

- [Découvrez-en plus](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) sur le déploiement du Pare-feu Azure.
- [Découvrez](https://docs.microsoft.com/azure/firewall/fqdn-tags) les balises FQDN.


## <a name="create-a-vault"></a>Création d'un coffre

Un coffre Recovery Services pour la sauvegarde permet de stocker les sauvegardes et points de récupération créés au fil du temps, ainsi que de stocker les stratégies de sauvegarde associées aux machines sauvegardées. Créez un coffre comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Dans le menu **Hub**, sélectionnez **Parcourir**, puis tapez **Recovery Services**. Au fur et à mesure des caractères saisis, la liste des ressources est filtrée. Sélectionnez **Coffres Recovery Services**.

    ![Saisie dans la zone et sélection de « Coffres Recovery Services » dans les résultats](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    La liste des archivages de Recovery Services s’affiche.
3. Dans le menu **Coffres Recovery Services**, sélectionnez **Ajouter**.

    ![Créer un coffre Recovery Services - Étape 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    Le volet **Coffres Recovery Services** s’ouvre. Il vous invite à renseigner les champs **Nom**, **Abonnement**, **Groupe de ressources** et **Emplacement**.

    ![Volet « Coffres Recovery Services »](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. Dans **Name**, entrez un nom convivial pour identifier le coffre.
    - Le nom doit être unique pour l’abonnement Azure.
    - Il peut comprendre entre 2 et 50 caractères.
    - Il doit commencer par une lettre, et ne peut contenir que des lettres, des chiffres et des traits d’union.
5. Sélectionnez **Abonnement** pour afficher la liste des abonnements disponibles. Si vous ne savez pas quel abonnement utiliser, utilisez l’abonnement par défaut (ou suggéré). Vous ne disposez de plusieurs choix que si votre compte professionnel ou scolaire est associé à plusieurs abonnements Azure.
6. Sélectionnez **Groupe de ressources** pour afficher la liste des groupes de ressources disponibles, ou **Nouveau** pour en créer un. [Découvrez-en plus](../azure-resource-manager/resource-group-overview.md) sur les groupes de ressources.
7. Sélectionnez **Emplacement** pour sélectionner la région géographique du coffre. Le coffre *doit* se trouver dans la même région que les machines virtuelles que vous souhaitez sauvegarder.
8. Sélectionnez **Créer**.
    - La création du coffre peut prendre du temps.
    - Surveillez les notifications d’état dans l’angle supérieur droit du portail.
    ![Liste des coffres de sauvegarde](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Une fois votre coffre créé, il apparaît dans la liste des coffres Recovery Services. Si vous ne voyez pas votre coffre, sélectionnez **Actualiser**.

## <a name="set-up-storage-replication"></a>Configurer la réplication du stockage

Par défaut, votre coffre utilise un [stockage géoredondant (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). Nous vous recommandons d’utiliser un GRS pour votre sauvegarde principale, mais vous pouvez utiliser un [stockage localement redondant](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pour réaliser des économies. 

Modifiez la réplication du stockage comme suit :

1. Dans le coffre > **Infrastructure de sauvegarde**, cliquez sur **Configuration de la sauvegarde**.

   ![Liste des archivages de sauvegarde](./media/backup-azure-arm-vms-prepare/full-blade.png)

2. Dans **Configuration de la sauvegarde**, modifiez la méthode de redondance du stockage selon vos besoins, puis sélectionnez **Enregistrer**.


## <a name="configure-backup"></a>Configurer une sauvegarde

Découvrez les machines virtuelles incluses dans l’abonnement, puis configurez la sauvegarde.

1. Dans le coffre > **Vue d’ensemble**, cliquez sur **+Sauvegarde**.

   ![Bouton de sauvegarde](./media/backup-azure-arm-vms-prepare/backup-button.png)

   Les volets **Sauvegarde** et **Objectif de sauvegarde** s’ouvrent.

2. Dans **Objectif de sauvegarde**> **Où s’exécute votre charge de travail ?**, sélectionnez **Azure**. Dans **Que voulez-vous sauvegarder ?**, sélectionnez **Machine virtuelle** >  **OK**. L’extension de machine virtuelle est inscrite dans le coffre.

   ![Volets Sauvegarde et Objectif de sauvegarde](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Cette étape inscrit l’extension de machine virtuelle auprès du coffre. Le volet **Objectif de sauvegarde** se ferme et le volet **Stratégie de sauvegarde** s’ouvre.

3. Dans **Stratégie de sauvegarde**, sélectionnez la stratégie à associer au coffre. Cliquez ensuite sur **OK**.
    - Les détails de la stratégie par défaut sont répertoriés dans le menu déroulant à l’écran.
    - Cliquez sur **Créer** pour créer une stratégie. [Découvrez-en plus](backup-azure-vms-first-look-arm.md#defining-a-backup-policy) sur la définition d’une stratégie.

    ![Volets Sauvegarde et Stratégie de sauvegarde](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. Dans le volet **Sélectionner les machines virtuelles**, sélectionnez les machines virtuelles qui utiliseront la stratégie de sauvegarde spécifiée > **OK**.

    - La machine virtuelle sélectionnée est validée.
    - Vous pouvez uniquement sélectionner les machines virtuelles situées dans la même région que le coffre. Vous pouvez sauvegarder des machines virtuelles uniquement dans un même coffre.

   ![Volet Sélectionner les machines virtuelles](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. Dans **Sauvegarde**, sélectionnez **Activer la sauvegarde**.

   - Cette action permet de déployer la stratégie dans le coffre et sur les machines virtuelles, puis d’installer l’extension de sauvegarde sur l’agent de machine virtuelle en cours d’exécution sur la machine virtuelle Azure.
   - Cette étape ne crée pas le point de récupération initial pour la machine virtuelle.

   ![Bouton Activer la sauvegarde](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Après avoir activé la sauvegarde :

- La stratégie de sauvegarde s’exécute conformément à votre planification de sauvegarde.
- Il installe l’extension de sauvegarde, que la machine virtuelle soit ou non en cours d’exécution.
    - Une machine virtuelle en cours d’exécution offre le plus de chance d’obtenir un point de récupération d’application cohérent.
    -  Toutefois, la machine virtuelle est sauvegardée même si elle est désactivée et que l’extension ne peut pas être installée. autrement dit si la *machine virtuelle est hors connexion*. Dans ce cas, le point de récupération sera *cohérent en cas d’incident*.
- Pour générer immédiatement une sauvegarde à la demande pour la machine virtuelle, dans **Éléments de sauvegarde**, cliquez sur les points de suspension (...) en regard de la machine virtuelle > **Sauvegarder maintenant**.


## <a name="next-steps"></a>Étapes suivantes

- Résolvez les problèmes rencontrés par les [agents de machine virtuelle Azure](/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) ou la [sauvegarde de machine virtuelle Azure](backup-azure-vms-troubleshoot.md).
- [Sauvegarder des machines virtuelles Azure](backup-azure-vms-first-look-arm.md)
