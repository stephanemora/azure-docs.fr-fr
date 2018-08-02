---
title: Mise à jour 1807 d’Azure Stack | Microsoft Docs
description: Découvrez les nouveautés de la mise à jour 1807 pour les systèmes intégrés Azure Stack, y compris les problèmes connus et l’emplacement de téléchargement de la mise à jour.
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
ms.date: 07/24/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: fe37a62d364d53d03a232b6fdc41e38a9ae2e30f
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39227666"
---
# <a name="azure-stack-1807-update"></a>Mise à jour 1807 d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit le contenu de la mise à jour 1807. Cette mise à jour inclut des améliorations, des corrections, des problèmes connus de cette version d’Azure Stack et l’emplacement de téléchargement de la mise à jour. Les problèmes connus sont divisés en problèmes directement liés au processus de mise à jour et en problèmes propres à la build (après l’installation).

> [!IMPORTANT]        
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

## <a name="build-reference"></a>Référence de build    
Le numéro de build de mise à jour d’Azure Stack 1807 est **1.1807.XX.X**.  


### <a name="new-features"></a>Nouvelles fonctionnalités
Cette mise à jour inclut les améliorations suivantes pour Azure Stack.

- <!-- 1658937 | ASDK, IS --> **Démarrer les sauvegardes selon un calendrier prédéfini** : En tant qu’appliance, Azure Stack peut maintenant lancer automatiquement des sauvegardes périodiques de l’infrastructure afin d’éviter toute intervention humaine. Azure Stack nettoie également automatiquement le partage externe des sauvegardes antérieures à la période de rétention définie. 

- <!-- 2496385 | ASDK, IS --> **Ajout du temps de transfert des données dans le temps total de sauvegarde.**

-   <!-- 1702130 | ASDK, IS --> **La capacité externe de sauvegarde affiche maintenant la capacité correcte du partage externe.** (la valeur était codée en dur à 10 Go)

- <!-- 2508488 |  IS   -->   Augmenter la capacité en [ajoutant des nœuds d’unité d’échelle supplémentaires](azure-stack-add-scale-node.md).



### <a name="fixed-issues"></a>Problèmes résolus

- <!-- 448955 | IS ASDK --> Il est désormais possible de consulter avec succès les journaux d’activité des systèmes déployés dans un fuseau horaire UTC+N.    

- <!-- 2319627 |  ASDK, IS --> La vérification préalable des paramètres de configuration de la sauvegarde (chemin d’accès, nom d’utilisateur, mot de passe, clé de cryptage) ne définit plus les paramètres incorrects de la configuration de la sauvegarde. (les paramètres incorrects étaient définis auparavant dans la sauvegarde qui échouait à son déclenchement)

- <!-- 2215948 |  ASDK, IS --> La liste de sauvegarde s’actualise désormais lorsque l’on supprime manuellement la sauvegarde du partage externe.

- <!-- 2332636 - IS -->  La mise à jour de cette version ne réinitialise plus le propriétaire par défaut de l’abonnement du fournisseur par défaut à l’utilisateur CloudAdmin intégré en cas de déploiement avec AD FS.

- <!-- 2360715 |  ASDK, IS -->  Lorsque vous configurez l’intégration du centre de données, vous n’accédez plus au fichier de métadonnées AD FS à partir d’un partage. Pour plus d’informations, consultez la rubrique [Configuration de l’intégration AD FS en fournissant un fichier de métadonnées de fédération](azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

- <!-- 2388980 | ASDK, IS --> Nous avons corrigé un problème qui empêchait les utilisateurs d’attribuer une adresse IP publique existante ayant déjà été attribuée à une interface réseau ou à un équilibreur de charge à une nouvelle interface réseau ou à un nouvel équilibreur de charge.  

-   <!--2292271 | ASDK, IS --> Nous avons corrigé un problème qui empêchait l’application d’une limite de quota modifiée aux abonnements existants.  Désormais, lorsque vous augmentez la limite de quota d’une ressource réseau faisant partie d’une offre et d’un plan associés à un abonnement de locataire, la nouvelle limite s’applique aux abonnements préexistants, ainsi qu’aux nouveaux abonnements.

- **Divers correctifs** pour les performances, la stabilité, la sécurité et le système d’exploitation utilisé par Azure Stack.


<!-- ### Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Avant de commencer    

### <a name="prerequisites"></a>Prérequis
- Installez la [mise à jour 1805](azure-stack-update-1805.md) d’Azure Stack avant d’appliquer la mise à jour 1807 d’Azure Stack.  Il n’existe pas de mise à jour 1806.  

- Avant de commencer l’installation de la mise à jour 1807, exécutez [Test-AzureStack](azure-stack-diagnostic-test.md) pour valider l’état de votre Azure Stack et résoudre les éventuels problèmes opérationnels détectés. Examinez aussi les alertes actives et résolvez toutes celles qui nécessitent une intervention.

### <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour   
- <!-- 2468613 - IS --> Pendant l’installation de cette mise à jour, des alertes avec le titre *Erreur - Le modèle de FaultType UserAccounts.New est manquant.* peuvent s’afficher.  Vous pouvez ignorer ces alertes de manière sécurisée. Elles se fermeront automatiquement une fois l’installation de la mise à jour terminée.   

- <!-- 2489559 - IS -->Ne tentez pas de créer des machines virtuelles pendant l’installation de cette mise à jour. Pour plus d’informations sur la gestion des mises à jour, consultez [Manage updates in Azure Stack overview](azure-stack-updates.md#plan-for-updates) (Vue d’ensemble de la gestion des mises à jour dans Azure Stack).


### <a name="post-update-steps"></a>Étapes après la mise à jour
*Il n’existe aucune étape après la mise à jour pour la mise à jour 1807.*

<!-- After the installation of this update, install any applicable Hotfixes. For more information view the following knowledge base articles, as well as our [Servicing Policy](azure-stack-servicing-policy.md).  
 - Link to KB  
 -->

## <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)
Les éléments suivants sont des problèmes connus qui apparaissent après l’installation de cette build.

### <a name="portal"></a>Portail  
- <!-- 2551834 - IS, ASDK --> Lorsque vous sélectionnez **Vue d’ensemble** pour un compte de stockage sur le portail d’administration ou utilisateur, les informations du volet *Éléments principaux* ne s’affichent pas.  Le volet Éléments principaux affiche des informations sur le compte comme son *groupe de ressources*, son *emplacement* et son *ID d’abonnement*.  D’autres options de la vue d’ensemble sont accessibles, par exemple *Services* et *Surveillance*, ainsi que des options permettant d’*Ouvrir dans Explorer* ou de *Supprimer le compte de stockage*.

  Pour afficher les informations non disponibles, utilisez la cmdlet PowerShell [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0).

- <!-- 2551834 - IS, ASDK --> Lorsque vous sélectionnez **Balises** pour un compte de stockage sur le portail d’administration ou utilisateur, les informations ne se chargent pas.  

  Pour afficher les informations non disponibles, utilisez la cmdlet PowerShell [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0). 

- <!-- TBD - IS ASDK --> Certains types d’abonnement d’administration ne sont pas disponibles.  Lorsque vous mettez à niveau Azure Stack vers cette version, les deux types d’abonnements qui ont été [introduits avec la version 1804](azure-stack-update-1804.md#new-features) ne sont pas visibles dans la console. Ceci est normal. Les types d’abonnement indisponibles sont *Abonnement de contrôle*, et *Abonnement de consommation*. Ces types d’abonnements sont visibles dans les nouveaux environnements Azure Stack depuis la version 1804, mais ils ne sont pas encore prêts à être utilisés. Vous devez continuer à utiliser le type d’abonnement *Fournisseur par défaut*.  

- <!-- 2403291 - IS ASDK --> Il se peut que vous ne puissiez pas utiliser la barre de défilement horizontale au bas du portail d’administration et du portail utilisateur. Si vous ne pouvez pas accéder à la barre de défilement horizontale, utilisez les barres de navigation pour accéder à un panneau précédent dans le portail en sélectionnant le nom du panneau que vous souhaitez afficher dans la liste des barres de navigation en haut à gauche du portail.
  ![Barre de navigation](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> Il se peut qu’il ne soit pas possible d’afficher les ressources de calcul ou de stockage sur le portail d’administration. Ce problème est dû au fait qu’une erreur s’est produite pendant l’installation de la mise à jour, et que celle-ci a été, à tort, signalée comme réussie. Si ce problème se produit, contactez les services de support technique Microsoft pour obtenir de l’aide.

- <!-- TBD - IS --> Il se peut qu’un tableau de bord vide s’affiche sur le portail. Pour récupérer le tableau de bord, sélectionnez l’icône d’engrenage dans l’angle supérieur droit du portail, puis choisissez **Restaurer les paramètres par défaut**.

- <!-- TBD - IS ASDK --> La suppression d’abonnements utilisateur génère des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.

- <!-- TBD - IS ASDK --> Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack. Pour résoudre ce problème, utilisez PowerShell pour vérifier les autorisations.


### <a name="health-and-monitoring"></a>Intégrité et surveillance
- <!-- 1264761 - IS ASDK -->  Vous risquez de recevoir des alertes pour le composant du *contrôleur d’intégrité* contenant les informations suivantes :  

   Alerte 1 :
   - NOM : Rôle d’infrastructure défectueux
   - GRAVITÉ : Avertissement
   - COMPOSANT : Contrôleur d’intégrité
   - DESCRIPTION : L’analyseur de pulsations du contrôleur d’intégrité n’est pas disponible. Cela peut affecter les rapports et les métriques d’intégrité.  

  Alerte 2 :
   - NOM : Rôle d’infrastructure défectueux
   - GRAVITÉ : Avertissement
   - COMPOSANT : Contrôleur d’intégrité
   - DESCRIPTION : L’analyseur d’erreur du contrôleur d’intégrité n’est pas disponible. Cela peut affecter les rapports et les métriques d’intégrité.

  Les deux alertes peuvent être ignorées en toute sécurité. Elles se ferment automatiquement au bout d’un moment.  

- <!-- 2368581 - IS. ASDK --> En tant qu’opérateur d’Azure Stack, si vous recevez une alerte d’insuffisance de mémoire et que les machines virtuelles client ne parviennent pas à se déployer à cause d’une *erreur de création de la structure de la machine virtuelle*, il est possible que l’horodatage d’Azure Stack soit en dehors de la mémoire disponible. Utilisez le [Capacity Planner Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) pour mieux comprendre la capacité disponible pour vos charges de travail.


### <a name="compute"></a>Calcul
- <!-- 2724873 - IS --> En utilisant les cmdlets PowerShell **Start-AzsScaleUnitNode** ou **Stop-AzsScaleunitNode** pour gérer les unités d’échelle, il est possible que la première tentative de démarrage ou d’arrêt de l’unité d’échelle échoue. Si la cmdlet échoue une première fois, exécutez-la une seconde fois. La deuxième exécution doit permettre de terminer l’opération. 

- <!-- 2494144 - IS, ASDK --> Lorsque vous sélectionnez une taille de machine virtuelle pour un déploiement de machine virtuelle, certaines tailles de machine virtuelle de série F ne sont pas visibles dans le sélecteur de taille lorsque vous créez une machine virtuelle. Les tailles de machine virtuelle suivantes n’apparaissent pas dans le sélecteur : *F8s_v2*, *F16s_v2*, *F32s_v2* et *F64s_v2*.  
  Pour résoudre ce problème, utilisez l’une des méthodes suivantes pour déployer une machine virtuelle. Dans chaque méthode, vous devez spécifier la taille de machine virtuelle que vous voulez utiliser.

  - **Modèle Azure Resource Manager :** lorsque vous utilisez un modèle, définissez la valeur *vmSize* dans le modèle pour qu’elle soit égale à la taille de machine virtuelle que vous voulez utiliser. Par exemple, l’entrée suivante permet de déployer une machine virtuelle qui utilise la taille *F32s_v2* :  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI :** Vous pouvez utiliser la commande [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) et spécifiez la taille de machine virtuelle comme paramètre, identique à `--size "Standard_F32s_v2"`.

  - **PowerShell :** avec PowerShell, vous pouvez utiliser [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) avec le paramètre qui spécifie la taille de machine virtuelle, identique à `-VMSize "Standard_F32s_v2"`.


- <!-- TBD - IS ASDK --> Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

- <!-- TBD - IS --> Lorsque vous créez un groupe à haute disponibilité dans le portail en accédant à **Nouveau** > **Compute** > **Groupe à haute disponibilité**, vous pouvez uniquement créer un groupe à haute disponibilité avec un domaine d’erreur et un domaine de mise à jour de 1. Pour contourner ce problème, lors de la création d’une nouvelle machine virtuelle, créez le groupe à haute disponibilité à l’aide de PowerShell, CLI, ou depuis le portail.

- <!-- TBD - IS ASDK --> Lorsque vous créez des machines virtuelles sur le portail utilisateur Azure Stack, ce dernier affiche un nombre incorrect de disques de données que vous pouvez attacher à une machine virtuelle de série DS. Les machines virtuelles de série DS peuvent prendre en charge autant de disques de données que la configuration Azure.

- <!-- TBD - IS ASDK --> Lorsqu’une image de machine virtuelle ne peut pas être créée, un élément ayant échoué que vous ne pouvez pas supprimer peut être ajouté au panneau Compute des images de machine virtuelle.

  Pour contourner ce problème, créez une image de machine virtuelle avec un disque dur virtuel factice qui peut être créé via Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Ce processus doit résoudre le problème qui empêche la suppression de l’élément ayant échoué. Ensuite, 15 minutes après avoir créé l’image factice, vous pouvez correctement la supprimer.

  Vous pouvez ensuite essayer à nouveau de télécharger l’image de la VM ayant précédemment échoué.

- <!-- TBD - IS ASDK --> Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, les utilisateurs doivent laisser expirer le délai d’attente plutôt que d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

- <!-- 1662991 IS ASDK --> Les diagnostics de machine virtuelle Linux ne sont pas pris en charge dans Azure Stack. Lorsque vous déployez une machine virtuelle Linux en activant les diagnostics de machine virtuelle, le déploiement échoue. Le déploiement échoue également si vous activez les mesures de base de la machine virtuelle Linux dans les paramètres de diagnostic.  


### <a name="networking"></a>Mise en réseau  

- <!-- 1766332 - IS ASDK --> Sous **Mise en réseau**, si vous cliquez sur **Créer une passerelle VPN** pour configurer une connexion VPN, l’option **Basé sur des stratégies** s’affiche dans la liste des types de VPN. Ne sélectionnez pas cette option. Seule l’option **Basé sur itinéraires** est prise en charge dans Azure Stack.

- <!-- 2304134 IS ASDK --> Vous ne pouvez pas supprimer un abonnement auquel sont associées des ressources de la zone DNS ou de la table de routage. Pour supprimer l’abonnement, vous devez d’abord supprimer les ressources de la zone DNS ou de la table de routage de l’abonnement de locataire.


- <!-- 1902460 - IS ASDK --> Azure Stack prend en charge une seule *passerelle de réseau local* par adresse IP. Cela est vrai pour tous les abonnements de locataire. Après la création de la première connexion de passerelle de réseau local, les tentatives suivantes de création d’une ressource de passerelle de réseau local avec la même adresse IP sont bloquées.

- <!-- 16309153 - IS ASDK --> Sur un réseau virtuel qui a été créé avec un paramètre de serveur DNS défini sur *Automatique*, vous ne pouvez pas choisir un serveur DNS personnalisé. Les paramètres mis à jour ne sont pas envoyés (par push) aux machines virtuelles dans ce réseau virtuel.

- <!-- TBD - IS ASDK --> Azure Stack ne prend pas en charge l’ajout d’interfaces réseau supplémentaires sur une instance de machine virtuelle une fois que la machine virtuelle est déployée. Si la machine virtuelle nécessite plusieurs interfaces réseau, elles doivent être définies au moment du déploiement.

- <!-- 2096388 IS --> Vous ne pouvez pas utiliser le portail d’administration pour mettre à jour les règles d’un groupe de sécurité réseau.

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


### <a name="sql-and-mysql"></a>SQL et MySQL

- <!-- TBD - IS --> Seul le fournisseur de ressources peut créer des éléments sur des serveurs qui hébergent SQL ou MySQL. Les éléments créés sur un serveur hôte qui ne sont pas créés par le fournisseur de ressources peuvent entraîner un état qui ne correspond pas.  

- <!-- IS, ASDK --> Les caractères spéciaux, y compris les espaces et les points, ne sont pas pris en charge dans les noms **Famille** et **Niveau** lors de la création d’une référence SKU pour les fournisseurs de ressources SQL et MySQL.


> [!NOTE]  
> <!-- TBD - IS --> Après avoir mis à jour cette version d’Azure Stack, vous pouvez continuer à utiliser les fournisseurs de ressources SQL et MySQL que vous avez déjà déployés.  Nous vous recommandons de mettre à jour SQL et MySQL lorsqu’une nouvelle version est disponible. Comme Azure Stack, appliquez de façon séquentielle les mises à jour aux fournisseurs de ressources SQL et MySQL. Par exemple, si vous utilisez la version 1804, commencez par appliquer la version 1805 avant de passer à la version 1807.      
>   
> L’installation de cette mise à jour n’affecte pas l’utilisation actuelle des fournisseurs de ressources SQL ou MySQL par vos utilisateurs.
> Quelle que soit la version des fournisseurs de ressources que vous utilisez, vos données utilisateur dans leurs bases de données restent intactes et accessibles.    



### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Les utilisateurs doivent inscrire le fournisseur de ressources de stockage avant de créer leur première fonction Azure dans l’abonnement.

- <!-- 2489178 - IS ASDK --> Pour augmenter la taille des instances sur l’infrastructure (Workers, gestion, rôles frontaux), vous devez utiliser PowerShell comme décrit dans les notes de publication pour Compute.

- <!-- TBD - IS ASDK --> App Service peut uniquement être déployé sur l’*abonnement Fournisseur par défaut* pour l’instant. Dans une prochaine mise à jour, App Service pourra être déployé dans le nouvel *abonnement de contrôle* introduit dans Azure Stack 1804. Une fois le contrôle complètement pris en charge, tous les déploiements existants seront migrés vers ce type d’abonnement.


### <a name="usage"></a>Usage  
- <!-- TBD - IS ASDK --> Les données d’utilisation des adresses IP publiques indiquent une même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Télécharger la mise à jour
Vous pouvez télécharger le package de mise à jour Azure Stack 1807 à partir de [cet emplacement](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Voir aussi
- Pour utiliser le Point de terminaison privilégié (PEP) afin de surveiller et de reprendre les mises à jour, consultez [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](azure-stack-monitor-update.md).
- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](azure-stack-updates.md).
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](azure-stack-apply-updates.md).
