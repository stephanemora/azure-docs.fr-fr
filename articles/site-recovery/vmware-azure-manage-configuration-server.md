---
title: Gérer le serveur de configuration pour la récupération d’urgence de VMware avec Azure Site Recovery | Microsoft Docs
description: Cet article explique comment utiliser Azure Site Recovery pour gérer un serveur de configuration existant dans le cadre d’une reprise après sinistre de VMware sur Azure.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 753e123c660b1aacea1157157f0e580e15c47536
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287403"
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
      net stop obengine
      net start obengine
      ```

## <a name="upgrade-the-configuration-server"></a>Mettre à niveau le serveur de configuration

Vous exécutez des correctifs cumulatifs pour mettre à jour le serveur de configuration. Les mises à jour peuvent être appliquées jusqu’aux versions N-4. Par exemple : 

- Si vous exécutez la version 9.7, 9.8, 9.9 ou 9.10, vous pouvez mettre à niveau directement vers la version 9.11.
- Si vous exécutez la version 9.6 ou une version antérieure, et souhaitez mettre à niveau vers la version 9.11, vous devez d’abord mettre à niveau vers la version 9.7, avant de mettre à niveau vers la version 9.11.

Des liens vers des correctifs cumulatifs pour la mise à niveau de toutes les versions du serveur de configuration sont disponibles dans la [page wiki relative aux mises à jour](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Mettez à niveau le serveur comme suit :

1. Dans le coffre, accédez à **Gérer** > **Infrastructure Site Recovery** > **Serveurs de configuration**.
2. Si une mise à jour est disponible, un lien s’affiche dans la colonne **Version de l’agent** >.

    ![Mettre à jour](./media/vmware-azure-manage-configuration-server/update2.png)

1. Téléchargez le fichier du programme d’installation des mises à jour sur le serveur de configuration.

    ![Mettre à jour](./media/vmware-azure-manage-configuration-server/update1.png)

4. Double-cliquez pour exécuter le programme d’installation.
2. Le programme d’installation détecte la version actuelle en cours d’exécution sur la machine. Cliquez sur **Oui** pour démarrer la mise à niveau. 
3. À l’issue de la mise à niveau, la configuration du serveur valide.

    ![Mettre à jour](./media/vmware-azure-manage-configuration-server/update3.png)

4. Cliquez sur **Terminer** pour fermer le programme d’installation.


## <a name="delete-or-unregister-a-configuration-server"></a>Supprimer un serveur de configuration ou annuler son inscription

1. [Désactivez la protection](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) de toutes les machines virtuelles sous le serveur de configuration.
2. [Dissociez](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) et [supprimez](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) toutes les stratégies de réplication du serveur de configuration.
3. [Supprimez](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) tous les serveurs vCenter/hôtes vSphere associés au serveur de configuration.
4. Dans le coffre, ouvrez **Infrastructure Site Recovery** > **Serveurs de configuration**.
5. Sélectionnez le serveur de configuration que vous souhaitez supprimer. Ensuite, dans la page **Détails**, sélectionnez **Supprimer**.

    ![Suppression d’un serveur de configuration](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### <a name="delete-with-powershell"></a>Supprimer avec PowerShell

Vous pouvez également supprimer le serveur de configuration à l’aide de PowerShell.

1. [Installez](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) le module Azure PowerShell.
2. Connectez-vous à votre compte Azure à l’aide de cette commande :
    
    `Connect-AzureRmAccount`
3. Sélectionnez l’abonnement du coffre.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Définir le contexte d’archivage.
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Récupérez le serveur de configuration.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Supprimez le serveur de configuration.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Vous pouvez utiliser l’option **-Force** dans Remove-AzureRmSiteRecoveryFabric pour forcer la suppression du serveur de configuration.
 


## <a name="renew-ssl-certificates"></a>Renouveler les certificats SSL

Le serveur de configuration comprend un serveur web intégré, qui orchestre les activités du service Mobilité, des serveurs de processus et des serveurs cibles maîtres connectés à celui-ci. Le serveur web utilise un certificat SSL pour authentifier les clients. Le certificat expire au bout de trois ans et peut être renouvelé à tout moment.

### <a name="check-expiry"></a>Vérifier la date d’expiration

Pour les déploiements de serveurs de configuration effectués avant mai 2016, le certificat expire au bout d’un an. Si votre certificat est sur le point d’expirer, voici ce qui se produit :

- Deux mois (ou moins) avant la date d’expiration, le service commence à envoyer des notifications via le portail et par e-mail (si vous avez souscrit aux notifications Site Recovery).
- Une bannière de notification s’affiche sur la page de ressources du coffre. Pour plus d’informations, sélectionnez la bannière.
- Si le bouton **Mettre à niveau maintenant** s’affiche, cela indique que certains composants de votre environnement n’ont pas été mis à niveau vers 9.4.xxxx.x ou une version supérieure. Mettez à niveau les composants avant de renouveler le certificat. Vous ne pouvez pas renouveler de versions antérieures.

### <a name="renew-the-certificate"></a>Renouveler le certificat

1. Dans le coffre, ouvrez **Infrastructure Site Recovery** > **Serveur de configuration**. Sélectionnez le serveur de configuration dont vous avez besoin.
2. La date d’expiration est indiquée sous **Intégrité de Configuration Server**.
3. Sélectionnez **Renouveler les certificats**. 


## <a name="next-steps"></a>Étapes suivantes

Consultez les tutoriels pour en savoir plus sur la configuration de la reprise après sinistre des [machines virtuelles VMware](vmware-azure-tutorial.md) dans Azure.
