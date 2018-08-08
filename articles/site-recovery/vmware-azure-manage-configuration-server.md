---
title: Gérer le serveur de configuration pour la récupération d’urgence de VMware avec Azure Site Recovery | Microsoft Docs
description: Cet article explique comment utiliser Azure Site Recovery pour gérer un serveur de configuration existant dans le cadre d’une reprise après sinistre de VMware sur Azure.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: df5b2ecce2a5c9d7c263ee0acc3a49b859b93f7f
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346118"
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>Gérer le serveur de configuration pour les machines virtuelles VMware

Quand vous utilisez [Azure Site Recovery](site-recovery-overview.md) pour la reprise après sinistre de machines virtuelles VMware et de serveurs physiques sur Azure, vous devez configurer un serveur de configuration local. Le serveur de configuration coordonne la communication entre les machines locales VMware et Azure, et gère la réplication des données. Cet article récapitule les tâches courantes de gestion du serveur de configuration que vous devez effectuer après son déploiement.



## <a name="modify-vmware-settings"></a>Modifier les paramètres de VMware

Vous pouvez accéder au serveur de configuration comme suit :
    - Connectez-vous à la machine virtuelle où il est déployé et lancez le gestionnaire de configuration Azure Site Recovery en utilisant le raccourci Bureau.
    - Vous pouvez aussi accéder au serveur de configuration à distance depuis **https://*ConfigurationServerName*/:44315 /**. Connectez-vous avec des informations d'identification d'administrateur.
   
### <a name="modify-vmware-server-settings"></a>Modifier les paramètres du serveur VMware

1. Pour associer un autre serveur VMware au serveur de configuration, sélectionnez **Ajouter un serveur vCenter Server/vSphere ESXi** après la connexion.
2. Entrez les détails et sélectionnez **OK**.


### <a name="modify-credentials-for-automatic-discovery"></a>Modifier les informations d’identification pour la découverte automatique

1. Pour mettre à jour les informations d’identification à utiliser pour la connexion au serveur VMware et la découverte automatique des machines virtuelles VMware, sélectionnez **Modifier** après la connexion.
2. Entrez les nouvelles informations d’identification, puis sélectionnez **OK**.

    ![Modification de VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)


## <a name="modify-credentials-for-mobility-service-installation"></a>Modifier les informations d’identification pour l’installation du service Mobilité

Modifiez les informations d’identification à utiliser pour installer automatiquement le service Mobilité sur les machines virtuelles VMware sur lesquelles vous activez la réplication.

1. Après la connexion, sélectionnez **Gérer les informations d’identification de machine virtuelle**
2. Entrez les nouvelles informations d’identification, puis sélectionnez **OK**.

    ![Modifier les informations d’identification du service Mobilité](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Modifier les paramètres du proxy

Modifiez les paramètres du proxy que la machine serveur de configuration doit utiliser pour l’accès Internet à Azure. Si vous disposez d’une machine serveur de processus, en plus du serveur de processus par défaut exécuté sur la machine serveur de configuration, modifiez les paramètres sur les deux machines.

1. Après la connexion au serveur de configuration, sélectionnez **Gérer la connectivité**.
2. Mettez à jour les valeurs de proxy. Ensuite, sélectionnez **Enregistrer** pour mettre à jour les paramètres.

## <a name="add-a-network-adapter"></a>Ajouter une carte réseau

Le modèle OVF (Open Virtualization Format) déploie la machine virtuelle du serveur de configuration avec une seule carte réseau.

- Vous pouvez [ajouter une autre carte réseau à la machine virtuelle](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), mais vous devez le faire avant d’inscrire le serveur de configuration dans le coffre.
- Pour ajouter une carte après avoir inscrit le serveur de configuration dans le coffre, ajoutez la carte dans les propriétés de la machine virtuelle. Ensuite, vous devez réinscrire le serveur dans le coffre.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Réinscrire un serveur de configuration dans le même coffre

Vous pouvez réinscrire le serveur de configuration dans le même coffre, si nécessaire. Si vous disposez d’une machine serveur de processus supplémentaire, en plus du serveur de processus par défaut exécuté sur la machine serveur de configuration, réinscrivez les deux machines.


  1. Dans le coffre, cliquez sur **Gérer** > **Infrastructure Site Recovery** > **Serveurs de configuration**.
  2. Dans **Serveurs**, sélectionnez **Télécharger une clé d’inscription** pour télécharger le fichier d’informations d’identification du coffre.
  3. Connectez-vous à la machine du serveur de configuration.
  4. Sous **%ProgramData%\ASR\home\svsystems\bin**, ouvrez **cspsconfigtool.exe**.
  5. Sous l’onglet **Inscription du coffre**, sélectionnez **Parcourir** et recherchez le fichier d’informations d’identification du coffre que vous avez téléchargé.
  6. Si nécessaire, fournissez les détails du serveur proxy. Sélectionnez ensuite **Inscription**.
  7. Ouvrez une fenêtre Commande d’administration PowerShell, puis exécutez la commande suivante :
   ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

      >[!NOTE] 
      >Afin d’**extraire les certificats les plus récents** à partir du serveur de configuration pour évoluer le serveur de traitement, exécutez la commande *« <Installation Drive\Microsoft Azure Site Recovery\agent\cdpcli.exe> »--registermt*

  8. Enfin, redémarrez la machine en exécutant la commande suivante.
  ```
          net stop obengine
          net start obengine

## Upgrade the configuration server

You run update rollups to update the configuration server. Updates can be applied for up to N-4 versions. For example:

- If you run 9.7, 9.8, 9.9, or 9.10, you can upgrade directly to 9.11.
- If you run 9.6 or earlier and you want to upgrade to 9.11, you must first upgrade to version 9.7. before 9.11.

Links to update rollups for upgrading to all versions of the configuration server are available in the [wiki updates page](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Upgrade the server as follows:

1. In the vault, go to **Manage** > **Site Recovery Infrastructure** > **Configuration Servers**.
2. If an update is available, a link appears in the **Agent Version** > column.
    ![Update](./media/vmware-azure-manage-configuration-server/update2.png)
3. Download the update installer file to the configuration server.

    ![Update](./media/vmware-azure-manage-configuration-server/update1.png)

4. Double-click to run the installer.
5. The installer detects the current version running on the machine. Click **Yes** to start the upgrade.
6. When the upgrade completes the server configuration validates.

    ![Update](./media/vmware-azure-manage-configuration-server/update3.png)
    
7. Click **Finish** to close the installer.

## Delete or unregister a configuration server

1. [Disable protection](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) for all VMs under the configuration server.
2. [Disassociate](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) and [delete](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) all replication policies from the configuration server.
3. [Delete](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) all vCenter servers/vSphere hosts that are associated with the configuration server.
4. In the vault, open **Site Recovery Infrastructure** > **Configuration Servers**.
5. Select the configuration server that you want to remove. Then, on the **Details** page, select **Delete**.

    ![Delete configuration server](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### Delete with PowerShell

You can optionally delete the configuration server by using PowerShell.

1. [Install](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) the Azure PowerShell module.
2. Sign in to your Azure account by using this command:
    
    `Connect-AzureRmAccount`
3. Select the vault subscription.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Set the vault context.
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault> Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Retrieve the configuration server.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Delete the configuration server.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> You can use the **-Force** option in Remove-AzureRmSiteRecoveryFabric for forced deletion of the configuration server.
 
## Generate configuration server Passphrase

1. Sign in to your configuration server, and then open a command prompt window as an administrator.
2. To change the directory to the bin folder, execute the command **cd %ProgramData%\ASR\home\svsystems\bin**
3. To generate the passphrase file, execute **genpassphrase.exe -v > MobSvc.passphrase**.
4. Your passphrase will be stored in the file located at **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## Renew SSL certificates

The configuration server has an inbuilt web server, which orchestrates activities of the Mobility Service, process servers, and master target servers connected to it. The web server uses an SSL certificate to authenticate clients. The certificate expires after three years and can be renewed at any time.

### Check expiry

For configuration server deployments before May 2016, certificate expiry was set to one year. If you have a certificate that is going to expire, the following occurs:

- When the expiry date is two months or less, the service starts sending notifications in the portal, and by email (if you subscribed to Site Recovery notifications).
- A notification banner appears on the vault resource page. For more information, select the banner.
- If you see an **Upgrade Now** button, it indicates that some components in your environment haven't been upgraded to 9.4.xxxx.x or higher versions. Upgrade the components before you renew the certificate. You can't renew on older versions.

### Renew the certificate

1. In the vault, open **Site Recovery Infrastructure** > **Configuration Server**. Select the required configuration server.
2. The expiry date appears under **Configuration Server health**.
3. Select **Renew Certificates**. 


## Next steps

Review the tutorials for setting up disaster recovery of [VMware VMs](vmware-azure-tutorial.md) to Azure.
