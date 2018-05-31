---
title: Mise à jour 1804 d’Azure Stack | Microsoft Docs
description: Découvrez le contenu de la mise à jour 1804 pour les systèmes intégrés Azure Stack, les problèmes connus et l’emplacement de téléchargement de la mise à jour.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: a7ba5f1947da09177e7d2d9d0e9e926d858dff7e
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302588"
---
# <a name="azure-stack-1804-update"></a>Mise à jour 1804 d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit les améliorations et correctifs contenus dans le package de mise à jour 1804, les problèmes connus pour cette publication, ainsi que l’emplacement de téléchargement de la mise à jour. Les problèmes connus sont divisés en problèmes directement liés au processus de mise à jour et en problèmes propres à la build (après l’installation).

> [!IMPORTANT]        
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

## <a name="build-reference"></a>Référence de build    
Le numéro de build de la mise à jour 1804 d’Azure Stack est **20180513.1**.   

### <a name="new-features"></a>Nouvelles fonctionnalités
Cette mise à jour inclut les améliorations suivantes pour Azure Stack.

- <!-- 15028744 - IS -->  **Visual Studio support for disconnected Azure Stack deployments using AD FS**. Within Visual Studio you now can add subscriptions and authenticate using AD FS federated User credentials. 
 
- <!-- 1779474, 1779458 - IS --> **Use Av2 and F series virtual machines**. Azure Stack can now use virtual machines based on the Av2-series and F-series virtual machine sizes. For more information see [Virtual machine sizes supported in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). 

- <!-- 1759172 - IS, ASDK --> **More granular administrative subscriptions**. With version 1804 and later, the Default Provider subscription is now complemented with two additional subscriptions. The additions facilitate separating the management of core infrastructure, additional resource providers, and workloads. The following three subscriptions are available after the update installs:
  - *Abonnement Fournisseur par défaut*. Utilisez cet abonnement pour l’infrastructure principale uniquement. Ne déployez pas de ressources ou de fournisseurs de ressources sur cet abonnement.
  - *Abonnement À l’usage*. Utilisez cet abonnement pour le déploiement de fournisseurs de ressources. Les ressources déployées sur cet abonnement ne sont pas facturées.
  - *Abonnement Consommation*. Utilisez cet abonnement pour toute autre charge de travail que vous voulez déployer. Les ressources déployées ici sont facturées aux prix d’utilisation normale.


## <a name="fixed-issues"></a>Problèmes résolus

- <!-- IS, ASDK -->  In the admin portal, you no longer have to refresh the Update tile before it displays information.
 
- <!-- 2050709  -->  You can now use the admin portal to edit storage metrics for Blob service, Table service, and Queue service.
 
- <!-- IS, ASDK --> Under **Networking**, when you click **Connection** to set up a VPN connection, **Site-to-site (IPsec)** is now the only available option.

- **Divers correctifs** pour les performances, la stabilité, la sécurité et le système d’exploitation utilisé par Azure Stack.

## <a name="additional-releases-timed-with-this-update"></a>Autres versions synchronisées avec cette mise à jour  
Les versions suivantes sont maintenant disponibles, mais ne nécessitent pas la mise à jour 1804 d’Azure Stack.
- **Mise à jour avec le Pack d’analyse System Center Operations Manager de Microsoft Azure Stack**. Une nouvelle version (1.0.3.0) du Pack d’analyse System Center Operations Manager de Microsoft Azure Stack est disponible au [téléchargement](https://www.microsoft.com/download/details.aspx?id=55184). Avec cette version, vous pouvez utiliser des principaux de service quand vous ajoutez un déploiement Azure Stack connecté. Cette version propose également une expérience de gestion des mises à jour qui vous permet d’effectuer une action de correction directement à partir d’Operations Manager. Il existe aussi des nouveaux tableaux de bord qui affichent les fournisseurs de ressources, les unités d’échelle et les nœuds d’unités d’échelle.

- **Nouvelle version 1.3.0 d’Azure Stack PowerShell pour les administrateurs**.  Azure Stack PowerShell version 1.3.0 est désormais disponible pour l’installation. Cette version fournit des commandes pour tous les fournisseurs de ressources d’administrateur pour gérer Azure Stack.  Avec cette version, une partie du contenu sera dépréciée dans le [dépôt](https://github.com/Azure/AzureStack-Tools) GitHub d’outils Azure Stack. 

   Pour obtenir les détails de l’installation, suivez les [instructions](azure-stack-powershell-install.md) ou le contenu d’[aide](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) du Module Azure Stack 1.3.0. 

- **Informations de référence sur la version initiale de l’API REST Azure Stack**. Les [informations de référence sur l’API pour tous les fournisseurs de ressources d’administrateur Azure Stack](https://docs.microsoft.com/rest/api/azure-stack/) sont maintenant publiées. 


## <a name="before-you-begin"></a>Avant de commencer    

### <a name="prerequisites"></a>Prérequis

- Installez la [mise à jour 1803](azure-stack-update-1803.md) d’Azure Stack avant d’appliquer la mise à jour 1804 d’Azure Stack.    

### <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour   
- Pendant l’installation de la mise à jour 1804, des alertes avec le titre *Erreur - Le modèle de FaultType UserAccounts.New est manquant.* peuvent s’afficher.  Vous pouvez ignorer ces alertes de manière sécurisée. Elles se fermeront automatiquement une fois la mise à jour 1804 terminée.   
 
- <!-- TBD - IS --> Do not attempt to create virtual machines during the installation of this update. For more information about managing updates, see [Manage updates in Azure Stack overview](azure-stack-updates.md#plan-for-updates).
### <a name="post-update-steps"></a>Étapes après la mise à jour
*Il n’existe aucune étape après la mise à jour pour la mise à jour 1804.*



### <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)
Les éléments suivants sont des problèmes connus de post-installation pour la build **20180513.1**.

#### <a name="portal"></a>Portail
- <!-- TBD -  IS ASDK -->The ability [to open a new support request from the dropdown](azure-stack-manage-portals.md#quick-access-to-help-and-support) from within the administrator portal isn’t available. Instead, use the following link:     
    - Pour les systèmes Azure Stack intégrés, utilisez https://aka.ms/newsupportrequest.

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![Chemin de navigation](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> It might not be possible to view compute or storage resources in the administrator portal. The cause of this issue is an error during the installation of the update that causes the update to be incorrectly reported as successful. If this issue occurs, contact Microsoft Customer Support Services for assistance.

- <!-- TBD - IS --> You might see a blank dashboard in the portal. To recover the dashboard, select the gear icon in the upper right corner of the portal, and then select **Restore default settings**.

- <!-- TBD - IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD - IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.

- <!-- TBD - IS ASDK --> In the admin portal, you might see a critical alert for the *Microsoft.Update.Admin* component. The Alert name, description, and remediation all display as:  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (ERREUR - Absence du modèle de FaultType ResourceProviderTimeout.)

  Cette alerte peut être ignorée en toute sécurité. 


#### <a name="compute"></a>Calcul
- <!-- TBD - IS --> When selecting a virtual machine size for a virtual machine deployment, some F-Series VM sizes are not visible as part of the size selector when you create a VM. The following VM sizes do not appear in the selector: *F8s_v2*, *F16s_v2*, *F32s_v2*, and *F64s_v2*.  
  Pour résoudre ce problème, utilisez l’une des méthodes suivantes pour déployer une machine virtuelle. Dans chaque méthode, vous devez spécifier la taille de machine virtuelle que vous voulez utiliser.

  - **Modèle Azure Resource Manager :** quand vous utilisez un modèle, définissez la valeur *vmSize* dans le modèle pour qu’elle soit égale à la taille de machine virtuelle souhaitée. Par exemple, le code suivant permet de déployer une machine virtuelle qui utilise la taille *F32s_v2* :  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI :** Vous pouvez utiliser la commande [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) et spécifiez la taille de machine virtuelle comme paramètre, identique à `--size "Standard_F32s_v2"`.

  - **PowerShell :** avec PowerShell, vous pouvez utiliser [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) avec le paramètre qui spécifie la taille de machine virtuelle, identique à `-VMSize "Standard_F32s_v2"`.


- <!-- TBD - IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD - IS --> When you create an availability set in the portal by going to **New** > **Compute** > **Availability set**, you can only create an availability set with a fault domain and update domain of 1. As a workaround, when creating a new virtual machine, create the availability set by using PowerShell, CLI, or from within the portal.

- <!-- TBD - IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach to a DS series VM. DS series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD - IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  Pour contourner ce problème, créez une image de machine virtuelle avec un disque dur virtuel factice qui peut être créé via Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Ce processus doit résoudre le problème qui empêche la suppression de l’élément ayant échoué. Ensuite, 15 minutes après avoir créé l’image factice, vous pouvez correctement la supprimer.

  Vous pouvez alors essayer de retélécharger l’image de machine virtuelle ayant précédemment échoué.

- <!-- TBD - IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  


#### <a name="networking"></a>Mise en réseau
- <!-- 1766332 - IS ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 - IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  Actuellement, vous devez utiliser uniquement les nouvelles adresses IP publiques pour les nouvelles machines virtuelles que vous créez.

  Ce comportement se produit même si vous réaffectez l’adresse IP à une nouvelle machine virtuelle (ce qui est communément appelé un *échange d’adresses IP virtuelles*). Toutes les futures tentatives de connexion au moyen de cette adresse IP aboutissent à une connexion à la machine virtuelle associée à l’origine et non à la nouvelle.

- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased. 

  Pour contourner ce problème, utilisez un plan d’extension pour augmenter un quota réseau quand le plan est déjà associé à un abonnement. Pour plus d’informations, découvrez comment [rendre un plan d’extension disponible](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription. 
  

- <!-- 1902460 - IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 - IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.

- <!-- TBD - IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.

- <!-- 2096388 IS --> You cannot use the admin portal to update rules for a network security group. 

    Solution de contournement pour App Service : si vous avez besoin d’établir la connexion d’un bureau à distance à des instances de contrôleur, vous modifiez les règles de sécurité dans les groupes de sécurité réseau avec PowerShell.  Voici des exemples montrant comment *autoriser* la configuration, puis la restaurer sur *refuser* :  
    
    - *Autoriser :*
 
      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn’t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *Refuser :*

        ```powershell
        
        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn’t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```

#### <a name="sql-and-mysql"></a>SQL et MySQL

- <!-- TBD - IS --> Only the resource provider is supported to create items on servers that host SQL or MySQL. Items created on a host server that are not created by the resource provider might result in a mismatched state.  

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** or **Tier** names when you create a SKU for the SQL and MySQL resource providers.


> [!NOTE]  
> <!-- TBD - IS --> After you update to Azure Stack 1804, you can continue to use the SQL and MySQL resource providers that you previously deployed.  We recommend you update SQL and MySQL when a new release becomes available. Like Azure Stack, apply updates to SQL and MySQL resource providers sequentially.  For example, if you use version 1802, first apply version 1803, and then update to 1804.      
>   
> L’installation de la mise à jour 1804 n’affecte pas l’utilisation actuelle des fournisseurs de ressources SQL ou MySQL par vos utilisateurs.
> Quelle que soit la version des fournisseurs de ressources que vous utilisez, vos données utilisateur dans leurs bases de données restent intactes et accessibles.    



#### <a name="app-service"></a>App Service
- <!-- TBD - IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- TBD - IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.


#### <a name="usage"></a>Usage  
- <!-- TBD - IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.


<!-- #### Identity -->
<!-- #### Health and monitoring --> 
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>Télécharger la mise à jour
Vous pouvez télécharger le package de mise à jour 1804 d’Azure Stack à partir de [cet emplacement](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Voir aussi
- Pour utiliser le Point de terminaison privilégié (PEP) afin de surveiller et de reprendre les mises à jour, consultez [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](azure-stack-monitor-update.md).
- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](azure-stack-updates.md).
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](azure-stack-apply-updates.md).
