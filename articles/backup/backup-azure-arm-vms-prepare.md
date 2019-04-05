---
title: Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services à l’aide de sauvegarde Azure
description: Décrit comment sauvegarder des machines virtuelles Azure dans un coffre Recovery Services à l’aide de sauvegarde Azure
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: raynew
ms.openlocfilehash: 3342b15511305ab337d9b5032080e205e36150d3
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049811"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services

Cet article explique comment sauvegarder des machines virtuelles Azure dans un coffre Recovery Services à l’aide de la [sauvegarde Azure](backup-overview.md) service. 

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Vérifier la prise en charge et configuration requise pour une sauvegarde.
> * Préparez les machines virtuelles Azure. installer l’agent de machine virtuelle Azure si nécessaire et vérifier l’accès sortant pour les machines virtuelles ;
> * Créer un coffre.
> * Découvrir les machines virtuelles et configurer une stratégie de sauvegarde.
> * Activer la sauvegarde des machines virtuelles Azure.


> [!NOTE]
> Cet article décrit comment configurer un coffre et sélectionnez les machines virtuelles à sauvegarder. Cette méthode s’avère utile pour sauvegarder plusieurs machines virtuelles. Vous pouvez également [sauvegarder une machine virtuelle Azure unique](backup-azure-vms-first-look-arm.md) directement à partir des paramètres de machine virtuelle.

## <a name="before-you-start"></a>Avant de commencer


- [Révision](backup-architecture.md#architecture-direct-backup-of-azure-vms) l’architecture de sauvegarde de machine virtuelle Azure.
- [En savoir plus sur](backup-azure-vms-introduction.md) la sauvegarde des machines virtuelles Azure et l’extension de la sauvegarde.
- [Passez en revue la matrice de prise en charge](backup-support-matrix-iaas.md) de la sauvegarde des machines virtuelles Azure.


## <a name="prepare-azure-vms"></a>Préparez les machines virtuelles Azure

Dans certains cas, vous devrez peut-être configurer l’agent de machine virtuelle Azure sur des machines virtuelles Azure, ou autoriser explicitement l’accès sortant sur une machine virtuelle.

### <a name="install-the-vm-agent"></a>Installer l’agent de machine virtuelle 

Sauvegarde Azure sauvegarde les machines virtuelles Azure en installant une extension à l’agent de machine virtuelle Azure en cours d’exécution sur l’ordinateur. Si votre machine virtuelle a été créé à partir d’une image de place de marché Azure, l’agent est installé et en cours d’exécution. Si vous créez une machine virtuelle personnalisée ou si vous migrez un ordinateur local, vous devrez peut-être installer l’agent manuellement, comme décrit dans le tableau.

**Machine virtuelle** | **Détails**
--- | ---
**Windows** | 1. [Téléchargez et installez](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) le fichier MSI de l’agent.<br/><br/> 2. Installez-le avec les autorisations d’administrateur pour l’ordinateur.<br/><br/> 3. Vérifiez l’installation. Dans *C:\WindowsAzure\Packages* sur la machine virtuelle, avec le bouton droit **WaAppAgent.exe** > **propriétés**. Sur le **détails** onglet, **version_produit** doit être défini sur 2.6.1198.718 ou une version ultérieure.<br/><br/> Si vous mettez à jour l’agent, assurez-vous qu’aucune opération de sauvegarde n’est en cours d’exécution, et [réinstaller l’agent](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Installer à l’aide d’un package RPM ou un package DEB à partir du référentiel de package de votre distribution. Il s’agit de la méthode recommandée pour l’installation et la mise à niveau l’agent Linux Azure. Tous les [fournisseurs de distribution approuvés](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) intègrent l’agent Azure Linux dans leurs images et référentiels. L’agent est disponible sur [GitHub](https://github.com/Azure/WALinuxAgent), mais nous ne recommandons pas d’effectuer une installation depuis ce site.<br/><br/> Si vous mettez à jour l’agent, assurez-vous qu’aucune opération de sauvegarde est en cours d’exécution et mettre à jour les fichiers binaires.


### <a name="establish-network-connectivity"></a>Établir la connectivité réseau

L’extension de sauvegarde en cours d’exécution sur la machine virtuelle a besoin d’un accès sortant vers des adresses IP publiques Azure.

En règle générale, vous n’avez pas besoin d’autoriser explicitement l’accès réseau sortant pour une machine virtuelle Azure pour qu’il puisse communiquer avec Azure Backup.
Si vos machines virtuelles ne peut pas se connecter, et si vous voyez l’erreur **ExtensionSnapshotFailedNoNetwork**, vous devez autoriser explicitement l’accès. L’extension de sauvegarde peut ensuite communiquer avec des adresses IP publiques Azure pour le trafic de sauvegarde.


#### <a name="explicitly-allow-outbound-access"></a>Autoriser explicitement l’accès sortant

Si une machine virtuelle ne peut pas se connecter au service de sauvegarde, vous devez explicitement autoriser un accès sortant en utilisant l’une des méthodes résumées dans le tableau.

**Option** | **Action** | **Détails** 
--- | --- | --- 
**Définir des règles de groupe de sécurité réseau** | autorisez les [plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Plutôt que de ce qui permet la gestion de chaque plage d’adresses, vous pouvez ajouter une règle de groupe de sécurité réseau qui autorise l’accès au service sauvegarde Azure en utilisant un [balise de service](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure). [Plus d’informations](../virtual-network/security-overview.md#service-tags)<br/><br/> Il n’existe aucun coût supplémentaire.<br/><br/> Les règles sont simples à gérer avec des balises de service.
**Déployer un proxy** | Déployer un serveur de proxy HTTP pour acheminer le trafic. | Cette méthode permet d’accéder à l’ensemble d’Azure et pas seulement au stockage.<br/><br/> Le contrôle granulaire des URL de stockage est autorisé.<br/><br/> Il existe un seul point d’accès internet pour les machines virtuelles.<br/><br/> Il existe des frais supplémentaires pour un serveur proxy.
**Configurer le pare-feu d’Azure** | Autoriser le trafic via le pare-feu d’Azure sur la machine virtuelle, à l’aide d’une balise de nom de domaine complet pour le service de sauvegarde Azure. |  Cette méthode est simple à utiliser si vous avez des pare-feu Azure défini dans le sous-réseau d’un réseau virtuel.<br/><br/> Vous ne peut pas créer vos propres balises de nom de domaine complet, ou modifiez les noms de domaine complets dans une balise.<br/><br/> Si vous utilisez Azure Managed Disks, vous devrez peut-être ouvrir un port supplémentaire (le port 8443) sur les pare-feu.

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Configurer une règle de groupe de sécurité réseau pour autoriser l’accès sortant vers Azure

Si un groupe de sécurité réseau gère l’accès aux machines virtuelles, autoriser l’accès sortant pour le stockage de sauvegarde vers les ports et plages d’adresses requises.

1. Dans les propriétés de la machine virtuelle > **mise en réseau**, sélectionnez **ajouter une règle de port de sortie**.
2. Dans **ajouter une règle de sécurité sortante**, sélectionnez **avancé**.
3. Dans **Source**, sélectionnez **VirtualNetwork**.
4. Dans **plages de ports sources**, entrez un astérisque (*) pour autoriser l’accès sortant à partir de n’importe quel port.
5. Dans **Destination**, sélectionnez **Balise de service**. Dans la liste, sélectionnez **Storage.region**. La région est où se trouvent le coffre et les machines virtuelles que vous souhaitez sauvegarder.
6. Dans **Plages de ports de destination**, sélectionnez le port.
    - Machine virtuelle non gérée avec un compte de stockage non chiffré : 80
    - Machine virtuelle non gérée avec un compte de stockage chiffré : 443 (paramètre par défaut)
    - Machine virtuelle gérée : 8443
7. Dans **Protocole**, sélectionnez **TCP**.
8. Dans **Priorité**, spécifiez une valeur inférieure à toute règle de refus supérieure.
   
   Si vous avez une règle qui refuse l’accès, la nouvelle permettre de règle doit être supérieure. Par exemple, si vous avez une règle **Deny_All** définie au niveau de priorité 1 000, votre nouvelle règle doit avoir une valeur inférieure à 1 000.
9. Indiquez un nom et une description pour la règle, puis sélectionnez **OK**.

Vous pouvez appliquer la règle de groupe de sécurité réseau à plusieurs machines virtuelles pour autoriser l’accès sortant. Cette vidéo vous guide tout au long du processus.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


##### <a name="route-backup-traffic-through-a-proxy"></a>Router le trafic de sauvegarde par le biais d’un proxy

Vous pouvez router le trafic de sauvegarde par le biais d’un proxy, puis octroyer au proxy l’accès aux plages Azure nécessaires. Configurer le proxy de machine virtuelle pour autoriser les éléments suivants :

- La machine virtuelle Azure route tout le trafic HTTP destiné à l’Internet public par le biais du proxy.
- Le proxy doit autoriser le trafic entrant à partir de machines virtuelles dans le réseau virtuel applicable.
- Le **verrouillage NSF** du groupe de sécurité réseau a besoin d’une règle qui autorise le trafic Internet sortant depuis la machine virtuelle proxy.

###### <a name="set-up-the-proxy"></a>Configurer le proxy

Si vous n’avez pas de proxy de compte système, configurez-en un comme suit :

1. Téléchargez [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Exécutez **PsExec.exe -i -s cmd.exe** pour exécuter l’invite de commandes sous un compte système.
3. Exécutez le navigateur dans le contexte système. Par exemple, utilisez **%PROGRAMFILES%\Internet Explorer\iexplore.exe** pour Internet Explorer.  
4. Définissez les paramètres du proxy.
   - Sur des machines Linux :
     - Ajoutez cette ligne au fichier **/etc/environnement** :
       - **http_proxy = http :\//proxy IP adresse : proxy port**
     - Ajoutez ces lignes au fichier **/etc/waagent.conf** :
         - **HttpProxy.Host=proxy IP address**
         - **HttpProxy.Port=proxy port**
   - Sur des machines Windows, dans les paramètres du navigateur, spécifiez qu’un proxy doit être utilisé. Si vous utilisez actuellement un proxy sur un compte d'utilisateur, vous pouvez utiliser ce script pour appliquer le paramètre au niveau du compte système.
       ```powershell
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

###### <a name="allow-incoming-connections-on-the-proxy"></a>Autoriser les connexions entrantes sur le proxy

Autorisez les connexions entrantes dans les paramètres du proxy.

1. Dans le pare-feu Windows, ouvrez **pare-feu Windows avec fonctions avancées de sécurité**.
2. Cliquez avec le bouton droit sur **Règles de trafic entrant** > **Nouvelle règle**.
3. Dans **Type de règle**, sélectionnez **personnalisé** > **suivant**.
4. Dans **Programme**, sélectionnez **Tous les programmes** > **Suivant**.
5. Dans **protocoles et Ports**:
   - Définissez le type sur **TCP**.
   - Définissez **Ports locaux** à **Ports spécifiques**.
   - Définissez **port distant** à **tous les Ports**.
  
6. Terminez l’Assistant et spécifiez un nom pour la règle.

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Ajouter une règle d’exception au groupe de sécurité réseau pour le proxy

Dans le **verrouillage NSF** du groupe de sécurité réseau, autorisez le trafic provenant de tout port 10.0.0.5 vers n’importe quelle adresse Internet sur le port 80 (HTTP) ou 443 (HTTPS).

Le script PowerShell suivant fournit un exemple permettant d’autoriser le trafic.
Au lieu d’autoriser le trafic sortant vers toutes les adresses internet publiques, vous pouvez spécifier une plage d’adresses IP (`-DestinationPortRange`), ou utiliser la balise de service storage.region.   

```powershell
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

### <a name="allow-firewall-access-by-using-an-fqdn-tag"></a>Autoriser l’accès de pare-feu à l’aide d’une balise de nom de domaine complet

Vous pouvez configurer des pare-feu Azure pour autoriser l’accès sortant du trafic réseau vers sauvegarde Azure.

- [Découvrez-en plus](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) sur le déploiement du Pare-feu Azure.
- [Découvrez](https://docs.microsoft.com/azure/firewall/fqdn-tags) les balises FQDN.

## <a name="modify-storage-replication-settings"></a>Modifier les paramètres de réplication de stockage

Par défaut, votre coffre utilise un [stockage géoredondant (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). Nous recommandons GRS pour votre sauvegarde principale. Vous pouvez utiliser [stockage localement redondant (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pour une option plus économique.

Modifiez le type de réplication de stockage comme suit :

1. Dans le portail, sélectionnez le nouveau coffre. Sous **paramètres**, sélectionnez **propriétés**.
2. Dans **propriétés**, sous **Configuration de la sauvegarde**, sélectionnez **mise à jour**.
3. Sélectionnez le type de réplication de stockage, puis **enregistrer**.

![Définir la configuration de stockage du nouveau coffre](./media/backup-try-azure-backup-in-10-mins/full-blade.png)


## <a name="configure-a-backup-policy"></a>Configurer une stratégie de sauvegarde

Découvrez les machines virtuelles incluses dans l’abonnement, puis configurez la sauvegarde.

1. Dans le coffre > **vue d’ensemble**, sélectionnez **+ sauvegarde**.

   ![Bouton de sauvegarde](./media/backup-azure-arm-vms-prepare/backup-button.png)

   Les volets **Sauvegarde** et **Objectif de sauvegarde** s’ouvrent.

2. Dans **Objectif de sauvegarde** > **Où s’exécute votre charge de travail ?**, sélectionnez **Azure**. Dans **Que voulez-vous sauvegarder ?**, sélectionnez **Machine virtuelle** >  **OK**.

   ![Volets Sauvegarde et Objectif de sauvegarde](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Cette étape inscrit l’extension de machine virtuelle auprès du coffre. Le volet **Objectif de sauvegarde** se ferme et le volet **Stratégie de sauvegarde** s’ouvre.

3. Dans **Stratégie de sauvegarde**, sélectionnez la stratégie à associer au coffre. Sélectionnez ensuite **OK**.
    - Les détails de la stratégie par défaut sont répertoriés dans le menu déroulant à l’écran.
    - Sélectionnez **créer un nouveau** pour créer une stratégie. [Découvrez-en plus](backup-azure-arm-vms-prepare.md#configure-a-backup-policy) sur la définition d’une stratégie.

    ![Volets Sauvegarde et Stratégie de sauvegarde](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. Dans le **sélectionner les machines virtuelles** volet, sélectionnez les machines virtuelles qui utiliseront la stratégie de sauvegarde spécifiée > **OK**.

   La machine virtuelle sélectionnée est validée. Vous pouvez uniquement sélectionner les machines virtuelles situées dans la même région que le coffre. Vous pouvez sauvegarder des machines virtuelles uniquement dans un même coffre.

   ![Volet Sélectionner les machines virtuelles](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. Dans **Sauvegarde**, sélectionnez **Activer la sauvegarde**.

   Cette étape déploie la stratégie dans le coffre et aux machines virtuelles. Il installe également l’extension de sauvegarde sur l’agent de machine virtuelle en cours d’exécution sur la machine virtuelle Azure.
   
   Cette étape ne crée pas le point de récupération initial pour la machine virtuelle.

   ![Bouton Activer la sauvegarde](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Après avoir activé la sauvegarde :

- Une sauvegarde initiale s’exécute conformément à votre planification de sauvegarde.
- Il installe l’extension de sauvegarde, que la machine virtuelle soit ou non en cours d’exécution.

Une machine virtuelle en cours d’exécution offre le plus de chance d’obtenir un point de récupération d’application cohérent. Toutefois, la machine virtuelle est sauvegardée même si elle est désactivée et que l’extension ne peut pas être installée. Il est connu comme une machine virtuelle en mode hors connexion. Dans ce cas, le point de récupération seront cohérents d’incident.
    
> [!NOTE]
> Sauvegarde Azure ne prend pas en charge l’ajustement automatique de l’horloge lors du passage à l’heure d’été pour les sauvegardes de machines virtuelles Azure. Modifiez les stratégies de sauvegarde manuellement selon les besoins.

## <a name="run-the-initial-backup"></a>Effectuer la sauvegarde initiale

La sauvegarde initiale s’exécutera conformément à la planification, sauf si vous l’exécutez immédiatement manuellement. L’exécuter manuellement comme suit :

1. Dans le menu du coffre, sélectionnez **éléments de sauvegarde**.
2. Dans **éléments de sauvegarde**, sélectionnez **Machine virtuelle Azure**.
3. Dans le **éléments de sauvegarde** , sélectionnez les points de suspension (**...** ).
4. Sélectionnez **sauvegarder maintenant**.
5. Dans **sauvegarder maintenant**, utilisez le contrôle calendrier pour sélectionner le dernier jour où le point de récupération doit être conservé > **OK**.
6. Surveiller les notifications du portail. Vous pouvez surveiller la progression du travail dans le tableau de bord du coffre > **Travaux de sauvegarde** > **En cours d’exécution**. Selon la taille de votre machine virtuelle, la création de la sauvegarde initiale peut prendre un certain temps.



## <a name="next-steps"></a>Étapes suivantes

- Résoudre les problèmes avec [agents de machine virtuelle Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) ou [sauvegarde de machine virtuelle Azure](backup-azure-vms-troubleshoot.md).
- [Restaurer](backup-azure-arm-restore-vms.md) machines virtuelles Azure.

