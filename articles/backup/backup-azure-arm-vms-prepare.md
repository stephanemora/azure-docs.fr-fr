---
title: Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services à l’aide de la sauvegarde Azure
description: Décrit comment sauvegarder des machines virtuelles Azure dans un coffre Recovery Services à l’aide de la sauvegarde Azure
service: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: raynew
ms.openlocfilehash: 98934216c0860c79575874df26603b1187e35978
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149082"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services

Cet article explique comment sauvegarder des machines virtuelles Azure dans un coffre Recovery Services, à l’aide de la [sauvegarde Azure](backup-overview.md) service. 

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Préparez les machines virtuelles Azure.
> * Créer un coffre.
> * Découvrir les machines virtuelles et configurer une stratégie de sauvegarde.
> * Activer la sauvegarde des machines virtuelles Azure.
> * Effectuez la sauvegarde initiale.


> [!NOTE]
> Cet article décrit comment configurer un coffre et sélectionnez les machines virtuelles à sauvegarder. Cette méthode s’avère utile pour sauvegarder plusieurs machines virtuelles. Vous pouvez également [sauvegarder une machine virtuelle Azure unique](backup-azure-vms-first-look-arm.md) directement à partir des paramètres de machine virtuelle.

## <a name="before-you-start"></a>Avant de commencer


- [Révision](backup-architecture.md#architecture-direct-backup-of-azure-vms) l’architecture de sauvegarde de machine virtuelle Azure.
- [En savoir plus sur](backup-azure-vms-introduction.md) la sauvegarde des machines virtuelles Azure et l’extension de la sauvegarde.
- [Passez en revue la matrice de prise en charge](backup-support-matrix-iaas.md) avant de configurer la sauvegarde.

En outre, il existe plusieurs choses que vous devrez peut-être faire dans certaines circonstances :

- **Installez l’agent de machine virtuelle sur la machine virtuelle**: Sauvegarde Azure sauvegarde les machines virtuelles Azure en installant une extension à l’agent de machine virtuelle Azure en cours d’exécution sur l’ordinateur. Si votre machine virtuelle a été créé à partir d’une image de place de marché Azure, l’agent est installé et en cours d’exécution. Si vous créez une machine virtuelle personnalisée ou si vous migrez un ordinateur local, vous devrez peut-être [installer manuellement l’agent](#install-the-vm-agent).
- **Autoriser explicitement l’accès sortant**: En règle générale, vous n’avez pas besoin d’autoriser explicitement l’accès réseau sortant pour une machine virtuelle Azure afin qu’il communique avec la sauvegarde Azure. Toutefois, certaines machines virtuelles peuvent rencontrer des problèmes de connexion, à montrant le **ExtensionSnapshotFailedNoNetwork** erreur lorsque vous tentez de vous connecter. Si cela se produit, vous devez [autoriser explicitement l’accès sortant](#explicitly-allow-outbound-access), de sorte que l’extension de sauvegarde Azure peut communiquer avec des adresses IP publiques Azure pour le trafic de sauvegarde.


## <a name="create-a-vault"></a>Création d'un coffre

 Un coffre stocke les sauvegardes et points de récupération créés au fil du temps, ainsi que les stratégies de sauvegarde associées aux machines sauvegardées. Créez un coffre comme suit :    

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).    
2. Dans Rechercher, tapez **Recovery Services**. Sous **Services**, cliquez sur **coffres Recovery Services**.   

     ![Recherchez des coffres Recovery Services](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/> 

3. Dans **coffres Recovery Services** menu, cliquez sur **+ ajouter**.    

     ![Créer un coffre Recovery Services - Étape 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)   

4. Dans **coffre Recovery Services**, tapez un nom convivial pour identifier le coffre.   
    - Le nom doit être unique pour l’abonnement Azure.   
    - Il peut comprendre entre 2 et 50 caractères.    
    - Il doit commencer par une lettre, et ne peut contenir que des lettres, des chiffres et des traits d’union.   
5. Sélectionnez l’abonnement Azure, le groupe de ressources et la région géographique dans laquelle le coffre doit être créé. Cliquez ensuite sur **Créer**.    
    - La création du coffre peut prendre du temps.  
    - Surveillez les notifications d’état dans l’angle supérieur droit du portail.   


 Une fois le coffre est créé, il apparaît dans la liste des coffres Recovery Services. Si vous ne voyez pas votre coffre, sélectionnez **Actualiser**.
 
![Liste des archivages de sauvegarde](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)    

### <a name="modify-storage-replication"></a>Modifier la réplication de stockage

Par défaut, utilisez les coffres [stockage géo-redondant (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

- Si le coffre est votre principal mécanisme de sauvegarde, nous vous recommandons de qu'utiliser GRS.
- Vous pouvez utiliser [stockage localement redondant (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pour une option plus économique.

Modifiez le type de réplication de stockage comme suit :

1. Dans le nouveau coffre, cliquez sur **propriétés** dans le **paramètres** section.
2. Dans **propriétés**, sous **Configuration de la sauvegarde**, cliquez sur **mise à jour**.
3. Sélectionnez le type de réplication de stockage, puis cliquez sur **enregistrer**.

      ![Définir la configuration de stockage du nouveau coffre](./media/backup-try-azure-backup-in-10-mins/full-blade.png)
> [!NOTE]
   > Vous ne pouvez pas modifier le type de réplication de stockage une fois que le coffre est configuré et qu’il contient des éléments de sauvegarde. Pour ce faire, vous devez recréer le coffre. 

## <a name="apply-a-backup-policy"></a>Appliquer une stratégie de sauvegarde

Configurer une stratégie de sauvegarde pour le coffre.

1. Dans le coffre, cliquez sur **+ sauvegarde** dans le **vue d’ensemble** section.

   ![Bouton de sauvegarde](./media/backup-azure-arm-vms-prepare/backup-button.png)


2. Dans **objectif de sauvegarde** > **où s’exécute votre charge de travail ?** sélectionnez **Azure**. Dans **que voulez-vous sauvegarder ?** sélectionnez **machine virtuelle** >  **OK**. L’extension de machine virtuelle est inscrite dans le coffre.

   ![Volets Sauvegarde et Objectif de sauvegarde](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. Dans **Stratégie de sauvegarde**, sélectionnez la stratégie à associer au coffre. 
    - Sauvegarde de la stratégie par défaut de la machine virtuelle une fois par jour. Les sauvegardes quotidiennes sont conservées pendant 30 jours. Instantanés de la récupération instantanée sont conservés pendant deux jours.
    - Si vous ne souhaitez pas utiliser la stratégie par défaut, sélectionnez **créer un nouveau**et créer une stratégie personnalisée comme décrit dans la procédure suivante.

      ![Stratégie de sauvegarde par défaut](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. Dans **sélectionner les machines virtuelles**, sélectionnez les machines virtuelles que vous souhaitez sauvegarder à l’aide de la stratégie. Cliquez ensuite sur **OK**.

   - Les machines virtuelles sélectionnées sont validés.
   - Vous pouvez uniquement sélectionner les machines virtuelles situées dans la même région que le coffre.
   - Vous pouvez sauvegarder des machines virtuelles uniquement dans un même coffre.

     ![Volet Sélectionner les machines virtuelles](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. Dans **sauvegarde**, cliquez sur **activer la sauvegarde**. Cette action permet de déployer la stratégie dans le coffre et sur les machines virtuelles, puis d’installer l’extension de sauvegarde sur l’agent de machine virtuelle en cours d’exécution sur la machine virtuelle Azure.
     
     ![Bouton Activer la sauvegarde](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Après avoir activé la sauvegarde :

- Il installe l’extension de sauvegarde, que la machine virtuelle soit ou non en cours d’exécution.
- Une sauvegarde initiale s’exécute conformément à votre planification de sauvegarde.
- Lors de l’exécution des sauvegardes, notez que :
    - Une machine virtuelle qui est en cours d’exécution ont le plus de chance pour capturer un point de récupération cohérent d’application.
    - Toutefois, même si la machine virtuelle est désactivée. il est sauvegardé. Une machine virtuelle est appelée sur une machine virtuelle en mode hors connexion. Dans ce cas, le point de récupération seront cohérents d’incident.
    

### <a name="create-a-custom-policy"></a>Créer une stratégie personnalisée

Si vous avez sélectionné pour créer une stratégie de sauvegarde, renseignez les paramètres de stratégie.

1. Dans **nom de la stratégie**, spécifiez un nom explicite.
2. Dans **la planification de sauvegarde** spécifier quand les sauvegardes doivent être effectuées. Vous pouvez tirer des sauvegardes quotidiennes ou hebdomadaires pour les machines virtuelles Azure.
2. Dans **la restauration instantanée**, spécifiez la durée pendant laquelle vous souhaitez conserver des instantanés localement pour la restauration instantanée.
    - Lorsque vous restaurez, sauvegardé une machine virtuelle, les disques sont copiés à partir du stockage, sur le réseau vers l’emplacement de stockage de récupération. Avec la restauration instantanée, vous pouvez tirer parti prises pendant une opération de sauvegarde, sans attendre les données seront transférés vers le coffre de sauvegarde de captures instantanées stockées localement.
    - Vous pouvez conserver des instantanés pour la restauration instantanée pour entre une à cinq jours. Deux jours est le paramètre par défaut.
3. Dans **durée de rétention**, spécifiez la durée pendant laquelle vous souhaitez conserver vos points de sauvegarde quotidiennes ou hebdomadaires.
4. Dans **rétention mensuelle du point de sauvegarde**, spécifiez si vous souhaitez conserver un mensuel de sauvegarde de vos sauvegardes quotidiennes ou hebdomadaires. 
5. Cliquez sur **OK** pour enregistrer les modifications.

    ![Stratégie de sauvegarde](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Sauvegarde Azure ne prend pas en charge l’ajustement automatique de l’horloge lors du passage à l’heure d’été pour les sauvegardes de machines virtuelles Azure. Comme l’heure d’été se produire, modifier des stratégies de sauvegarde manuellement en fonction des besoins.

## <a name="trigger-the-initial-backup"></a>Déclencher la sauvegarde initiale

La sauvegarde initiale s’exécute conformément au planning, mais vous pouvez l’exécuter immédiatement en procédant comme suit :

1. Dans le menu du coffre, cliquez sur **Éléments de sauvegarde**.
2. Sur **Éléments de sauvegarde**, cliquez sur **Machine virtuelle Azure**.
3. Dans le **éléments de sauvegarde** , cliquez sur les points de suspension (...).
4. Cliquez sur **Sauvegarder maintenant**.
5. Dans **sauvegarder maintenant**, utilisez le contrôle calendrier pour sélectionner le dernier jour où le point de récupération doit être conservé. Cliquez ensuite sur **OK**.
6. Surveiller les notifications du portail. Vous pouvez surveiller la progression du travail dans le tableau de bord du coffre > **Travaux de sauvegarde** > **En cours d’exécution**. Selon la taille de votre machine virtuelle, la création de la sauvegarde initiale peut prendre un certain temps.

## <a name="optional-steps-install-agentallow-outbound"></a>Étapes facultatives (installer l’agent/autoriser le trafic sortant)
### <a name="install-the-vm-agent"></a>Installer l’agent de machine virtuelle

Sauvegarde Azure sauvegarde les machines virtuelles Azure en installant une extension à l’agent de machine virtuelle Azure en cours d’exécution sur l’ordinateur. Si votre machine virtuelle a été créé à partir d’une image de place de marché Azure, l’agent est installé et en cours d’exécution. Si vous créez une machine virtuelle personnalisée ou si vous migrez un ordinateur local, vous devrez peut-être installer l’agent manuellement, comme décrit dans le tableau.

**Machine virtuelle** | **Détails**
--- | ---
**Windows** | 1. [Téléchargez et installez](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) le fichier MSI de l’agent.<br/><br/> 2. Installez-le avec les autorisations d’administrateur pour l’ordinateur.<br/><br/> 3. Vérifiez l’installation. Dans *C:\WindowsAzure\Packages* sur la machine virtuelle, avec le bouton droit **WaAppAgent.exe** > **propriétés**. Sur le **détails** onglet, **version_produit** doit être défini sur 2.6.1198.718 ou une version ultérieure.<br/><br/> Si vous mettez à jour l’agent, assurez-vous qu’aucune opération de sauvegarde n’est en cours d’exécution, et [réinstaller l’agent](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Installer à l’aide d’un package RPM ou un package DEB à partir du référentiel de package de votre distribution. Il s’agit de la méthode recommandée pour l’installation et la mise à niveau l’agent Linux Azure. Tous les [fournisseurs de distribution approuvés](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) intègrent l’agent Azure Linux dans leurs images et référentiels. L’agent est disponible sur [GitHub](https://github.com/Azure/WALinuxAgent), mais nous ne recommandons pas d’effectuer une installation depuis ce site.<br/><br/> Si vous mettez à jour l’agent, assurez-vous qu’aucune opération de sauvegarde est en cours d’exécution et mettre à jour les fichiers binaires.

### <a name="explicitly-allow-outbound-access"></a>Autoriser explicitement l’accès sortant

L’extension de sauvegarde en cours d’exécution sur la machine virtuelle a besoin d’un accès sortant vers des adresses IP publiques Azure.

- En général vous n’avez pas besoin d’autoriser explicitement l’accès réseau sortant pour une machine virtuelle Azure afin qu’il communique avec la sauvegarde Azure.
- Si vous rencontrez des difficultés avec les machines virtuelles connexion, ou si vous voyez l’erreur **ExtensionSnapshotFailedNoNetwork** lorsque vous tentez de vous connecter, vous devez autoriser explicitement l’accès pour que l’extension de sauvegarde puisse communiquer à l’adresse IP publique Azure adresses pour le trafic de sauvegarde. Méthodes d’accès sont résumées dans le tableau suivant.


**Option** | **Action** | **Détails** 
--- | --- | --- 
**Définir des règles de groupe de sécurité réseau** | autorisez les [plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/details.aspx?id=41653).<br/><br/> Plutôt que de ce qui permet la gestion de chaque plage d’adresses, vous pouvez ajouter une règle qui autorise l’accès au service sauvegarde Azure en utilisant un [balise de service](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure). | [En savoir plus](../virtual-network/security-overview.md#service-tags) sur les balises de service.<br/><br/> Balises de services simplifient la gestion des accès et n’entraînent des frais supplémentaires.
**Déployer un proxy** | Déployer un serveur de proxy HTTP pour acheminer le trafic. | Fournit un accès à l’ensemble d’Azure et pas seulement au stockage.<br/><br/> Le contrôle granulaire des URL de stockage est autorisé.<br/><br/> Un seul point d’accès Internet pour les machines virtuelles.<br/><br/> Frais supplémentaires pour le proxy.
**Configurer le pare-feu Azure** | autorisez le trafic via le Pare-feu Azure sur la machine virtuelle, en utilisant une balise FQDN pour le service Sauvegarde Azure | Simple à utiliser si vous avez Azure pare-feu configuré dans un sous-réseau de réseau virtuel.<br/><br/> Vous ne peut pas créer vos propres balises de nom de domaine complet, ou modifiez les noms de domaine complets dans une balise.<br/><br/> Si vos machines virtuelles Azure ont des disques managés, vous devrez peut-être ouvrir un autre port (8443) sur les pare-feu.

#### <a name="establish-network-connectivity"></a>Établir la connectivité réseau

Établir une connexion avec le groupe de sécurité réseau, par proxy ou via le pare-feu

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
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

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

##### <a name="allow-firewall-access-with-an-fqdn-tag"></a>Autoriser l’accès de pare-feu avec une balise de nom de domaine complet

Vous pouvez configurer des pare-feu Azure pour autoriser l’accès sortant du trafic réseau vers sauvegarde Azure.

- [Découvrez-en plus](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) sur le déploiement du Pare-feu Azure.
- [Découvrez](https://docs.microsoft.com/azure/firewall/fqdn-tags) les balises FQDN.



## <a name="next-steps"></a>Étapes suivantes

- Résoudre les problèmes avec [agents de machine virtuelle Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) ou [sauvegarde de machine virtuelle Azure](backup-azure-vms-troubleshoot.md).
- [Restaurer](backup-azure-arm-restore-vms.md) machines virtuelles Azure.

