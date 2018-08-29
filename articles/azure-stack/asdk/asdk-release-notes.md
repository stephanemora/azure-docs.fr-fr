---
title: Notes de publication du Kit de développement Microsoft Azure Stack | Microsoft Docs
description: Améliorations, correctifs et problèmes connus pour le Kit de développement Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: b77772ce69afbc32bfe8a6826fdf8420076074d9
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "41946453"
---
# <a name="azure-stack-development-kit-release-notes"></a>Notes de publication du Kit de développement Azure Stack  
Ces notes de publication fournissent des informations sur les améliorations, les correctifs et les problèmes connus relatifs au Kit de développement Azure Stack. Si vous n’êtes pas sûr de la version que vous exécutez, consultez le [portail pour vérifier](.\.\azure-stack-updates.md#determine-the-current-version).

> Restez informé des nouveautés concernant l’ASDK en vous abonnant au [![flux](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).


## <a name="build-11807076"></a>Build 1.1807.0.76

### <a name="new-features"></a>Nouvelles fonctionnalités
Cette build inclut les améliorations et les correctifs suivants pour Azure Stack.  

- <!-- 1658937 | ASDK, IS --> **Démarrer les sauvegardes selon un calendrier prédéfini** : En tant qu’appliance, Azure Stack peut maintenant lancer automatiquement des sauvegardes périodiques de l’infrastructure afin d’éviter toute intervention humaine. Azure Stack nettoie également automatiquement le partage externe des sauvegardes antérieures à la période de rétention définie. Pour en savoir plus, voir [Activer la sauvegarde d’Azure Stack avec PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).

- <!-- 2496385 | ASDK, IS -->  **Ajout du temps de transfert des données dans le temps total de sauvegarde.** Pour en savoir plus, voir [Activer la sauvegarde d’Azure Stack avec PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).

-   <!-- 1702130 | ASDK, IS -->  **La capacité externe de sauvegarde affiche maintenant la capacité correcte du partage externe.** (la valeur était codée en dur à 10 Go) Pour en savoir plus, voir [Activer la sauvegarde d’Azure Stack avec PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).
 
- <!-- 2753130 |  IS, ASDK   -->  **Les modèles Azure Resource Manager prennent désormais en charge l’élément de condition** - Vous pouvez désormais déployer une ressource dans un modèle Azure Resource Manager à l’aide d’une condition. Vous pouvez concevoir votre modèle afin de déployer une ressource en fonction d’une condition, par exemple évaluer si une valeur de paramètre est présente. Pour plus d’informations sur l’utilisation d’un modèle en tant que condition, consultez [Déployer une ressource de manière conditionnelle](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) et [Section Variables des modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) dans la documentation Azure. 

   Vous pouvez également utiliser des modèles pour [déployer des ressources Azure sur plusieurs groupes de ressources ou abonnements](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

- <!--2753073 | IS, ASDK -->  **La prise en charge de la version de ressource d’API Microsoft.Network a été mise à jour** pour inclure la prise en charge de l’API version 2017-10-01 à partir de la version 2015-06-15 pour les ressources réseau Azure Stack.  La prise en charge des versions de ressources entre 2017-10-01 et 2015-06-15 n’est pas incluse dans cette version, mais sera incluse dans une version ultérieure.  Pour connaître les différences de fonctionnalités, consultez [Considérations relatives à la mise en réseau Azure Stack](.\.\user\azure-stack-network-differences.md).

- <!-- 2272116 | IS, ASDK   -->  **Azure Stack a ajouté la prise en charge des recherches DNS inversées pour les points de terminaison d’infrastructure Azure Stack exposés à l’extérieur**  (autrement dit pour portal, adminportal, management et adminmanagement). Cela permet de résoudre les noms des points de terminaison externes Azure Stack à partir d’une adresse IP.

- <!-- 2780899 |  IS, ASDK   --> **Azure Stack prend désormais en charge l’ajout d’interfaces réseau supplémentaires à une machine virtuelle existante.**  Cette fonctionnalité est disponible à l’aide du portail, de PowerShell et de l’interface CLI. Pour plus d’informations, consultez [Ajouter ou supprimer des interfaces réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) dans la documentation Azure. 

- <!-- 2222444 | IS, ASDK   -->   **Des améliorations en matière de précision et de résilience ont été apportées aux compteurs d’utilisation de mise en réseau**. Les compteurs d’utilisation réseau sont désormais plus précis et tiennent compte des abonnements suspendus, des périodes d’interruption et des conditions de concurrence.

- <!-- 2297790 | IS, ASDK -->  **Améliorations apportées au client syslog Azure Stack (fonctionnalité en préversion)**. Ce client permet le transfert des journaux et de l’audit liés à l’infrastructure d’Azure Stack vers un serveur Syslog ou un logiciel SIEM externe à Azure Stack. Le client syslog prend désormais en charge le protocole TCP avec texte brut ou chiffrement TLS 1.2, cette dernière option étant la configuration par défaut. Vous pouvez configurer la connexion TLS avec authentification mutuelle ou serveur uniquement.

  Pour configurer la façon dont le client syslog communique avec le serveur syslog (par exemple le protocole, le chiffrement et l’authentification), utilisez l’applet de commande Set-SyslogServer. Cette applet de commande est disponible à partir du point de terminaison privilégié (PEP). 

  Pour ajouter le certificat côté client pour l’authentification mutuelle TLS 1.2 du client syslog, utilisez l’applet de commande Set-SyslogClient dans le PEP.

  Avec cette préversion, vous pouvez voir un plus grand nombre d’audits et d’alertes. 

  Cette fonctionnalité étant toujours en préversion, ne vous reposez pas sur elle dans les environnements de production.

  Pour plus d’informations, consultez [Transfert de syslog dans Azure Stack](.\.\azure-stack-integrate-security.md).

- <!-- ####### | IS, ASDK -->  **Azure Resource Manager inclut le nom de la région.** Avec cette version, les objets récupérés à partir d’Azure Resource Manager incluront désormais l’attribut de nom de région. Si un script PowerShell existant passe directement l’objet à une autre applet de commande, le script peut générer une erreur et échouer. Ce comportement est conforme à Azure Resource Manager et oblige le client appelant à soustraire l’attribut de région. Pour plus d’informations sur Azure Resource Manager, consultez la [documentation Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

- <!-- TBD | IS, ASDK -->  **Déplacer des abonnements entre des fournisseurs délégués.** Vous pouvez maintenant déplacer des abonnements entre des abonnements de fournisseurs délégués nouveaux ou existants appartenant au même locataire d’annuaire. Les abonnements appartenant à l’abonnement Fournisseur par défaut peuvent également être déplacés vers les abonnements Fournisseur délégué dans le même locataire d’annuaire. Pour plus d’informations, consultez [Déléguer des offres dans Azure Stack](.\.\azure-stack-delegated-provider.md).
 
- <!-- 2536808 IS ASDK --> **Amélioration du temps de création de machine virtuelle** pour les machines virtuelles qui sont créées avec des images que vous téléchargez à partir de la Place de marché Azure.

### <a name="fixed-issues"></a>Problèmes résolus

- <!-- TBD | ASDK, IS --> Différentes améliorations ont été apportées au processus de mise à jour afin de le rendre plus fiable. Des correctifs ont également été apportés à l’infrastructure sous-jacente, qui améliore le vidage des nœuds, réduisant ainsi le risque d’interruption pour les charges de travail pendant la mise à jour.

-   <!--2292271 | ASDK, IS --> Nous avons corrigé un problème qui empêchait l’application d’une limite de quota modifiée aux abonnements existants.  Désormais, lorsque vous augmentez la limite de quota d’une ressource réseau faisant partie d’une offre et d’un plan associés à un abonnement d’utilisateur, la nouvelle limite s’applique aux abonnements préexistants, ainsi qu’aux nouveaux abonnements.

- <!-- 2448955 | IS ASDK --> Il est désormais possible de consulter avec succès les journaux d’activité des systèmes déployés dans un fuseau horaire UTC+N.    

- <!-- 2319627 |  ASDK, IS --> La vérification préalable des paramètres de configuration de la sauvegarde (chemin d’accès, nom d’utilisateur, mot de passe, clé de cryptage) ne définit plus les paramètres incorrects de la configuration de la sauvegarde. (les paramètres incorrects étaient définis auparavant dans la sauvegarde qui échouait à son déclenchement)

- <!-- 2215948 |  ASDK, IS --> La liste de sauvegarde s’actualise désormais lorsque l’on supprime manuellement la sauvegarde du partage externe.

- <!-- 2360715 |  ASDK, IS -->  Lorsque vous configurez l’intégration du centre de données, vous n’accédez plus au fichier de métadonnées AD FS à partir d’un partage. Pour plus d’informations, consultez la rubrique [Configuration de l’intégration AD FS en fournissant un fichier de métadonnées de fédération](.\.\azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

- <!-- 2388980 | ASDK, IS --> Nous avons corrigé un problème qui empêchait les utilisateurs d’attribuer une adresse IP publique existante ayant déjà été attribuée à une interface réseau ou à un équilibreur de charge à une nouvelle interface réseau ou à un nouvel équilibreur de charge.  

- <!-- 2551834 - IS, ASDK --> Quand vous sélectionnez *Vue d’ensemble* pour un compte de stockage dans le portail d’administration ou utilisateur, le volet *Éléments principaux* affiche désormais correctement toutes les informations attendues. 

- <!-- 2551834 - IS, ASDK --> Quand vous sélectionnez *Balises* pour un compte de stockage dans le portail d’administration ou utilisateur, les informations sont désormais affichées correctement.

- <!-- TBD - IS ASDK --> Cette version d’Azure Stack corrige le problème qui empêchait l’application des mises à jour de pilote à partir de packages d’extension OEM.

-   <!-- 2055809- IS ASDK --> Nous avons résolu un problème qui vous empêchait de supprimer des machines virtuelles du panneau de calcul quand la création de la machine virtuelle échouait.  

- <!--  2643962 IS ASDK -->  L’alerte de *faible capacité mémoire* n’apparaît plus incorrectement.

- <!--  TBD ASDK --> La machine virtuelle qui héberge le point de terminaison privilégié (PEP) a été augmentée à 4 Go. Dans le Kit ASDK, cette machine virtuelle est nommée AzS-ERCS01.

- **Divers correctifs** pour les performances, la stabilité, la sécurité et le système d’exploitation utilisé par Azure Stack


<!-- ### Changes  -->
<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Problèmes connus

#### <a name="portal"></a>Portail  
- <!--2760466 – IS  ASDK --> Quand vous installez un nouvel environnement Azure Stack qui exécute cette version, l’alerte qui indique *Activation requise* peut ne pas s’afficher. L’[activation](.\.\azure-stack-registration.md) est nécessaire avant que vous puissiez utiliser la syndication de Place de marché. 

- <!-- TBD - IS ASDK --> Les deux types d’abonnements d’administration qui ont été [introduits avec la version 1804](.\.\azure-stack-update-1804.md#new-features) ne doivent pas être utilisés. Les types d’abonnements sont **Abonnement de contrôle** et **Abonnement de consommation**. Ces types d’abonnements sont **Abonnement de contrôle** et **Abonnement de consommation**. Ces types d’abonnements sont visibles dans les nouveaux environnements Azure Stack depuis la version 1804, mais ils ne sont pas encore prêts à être utilisés. Vous devez continuer à utiliser le type d’abonnement **Fournisseur par défaut**.

- <!-- 2403291 - IS ASDK --> Il se peut que vous ne puissiez pas utiliser la barre de défilement horizontale au bas du portail d’administration et du portail utilisateur. Si vous ne pouvez pas accéder à la barre de défilement horizontale, utilisez les barres de navigation pour accéder à un panneau précédent dans le portail en sélectionnant le nom du panneau que vous souhaitez afficher dans la liste des barres de navigation en haut à gauche du portail.
  ![Barre de navigation](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> La suppression d’abonnements utilisateur génère des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.

- <!-- TBD -  IS ASDK --> Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack. Pour résoudre ce problème, utilisez PowerShell pour vérifier les autorisations.

- <!--  TBD | ASDK -->  Le fuseau horaire par défaut pour votre déploiement Azure Stack sera désormais défini au format UTC. Vous pouvez sélectionner un fuseau horaire lors de l’installation d’Azure Stack, mais il sera automatiquement rétabli par défaut au format UTC lors de l’installation.

#### <a name="health-and-monitoring"></a>Intégrité et surveillance
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

  Les deux alertes peuvent être ignorées en toute sécurité. Elles se fermeront automatiquement au bout d’un moment.  

- <!-- 2368581 - IS. ASDK --> En tant qu’opérateur d’Azure Stack, si vous recevez une alerte d’insuffisance de mémoire et que les machines virtuelles client ne parviennent pas à se déployer à cause d’une *erreur de création de la structure de la machine virtuelle*, il est possible que l’horodatage d’Azure Stack soit en dehors de la mémoire disponible. Utilisez le [Capacity Planner Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) pour mieux comprendre la capacité disponible pour vos charges de travail.

- <!-- TBD - IS. ASDK --> L’exécution de l’applet de commande Test-AzureStack sur le point de terminaison privilégié (PEP) génère un message d’avertissement pour la machine virtuelle ERCS. Vous pouvez continuer à utiliser l’ASDK. 

#### <a name="compute"></a>Calcul
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


- <!-- TBD -  IS ASDK --> Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Lorsque vous créez des machines virtuelles sur le portail utilisateur Azure Stack, ce dernier affiche un nombre incorrect de disques de données que vous pouvez attacher à une machine virtuelle de série D. Toutes les machines virtuelles de série D prises en charge peuvent prendre en charge autant de disques de données que la configuration Azure.

- <!-- TBD -  IS ASDK --> Lorsqu’une image de machine virtuelle ne peut pas être créée, un élément ayant échoué que vous ne pouvez pas supprimer peut être ajouté au panneau Compute des images de machine virtuelle.

  Pour contourner ce problème, créez une image de machine virtuelle avec un disque dur virtuel factice qui peut être créé via Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Ce processus doit résoudre le problème qui empêche la suppression de l’élément ayant échoué. Ensuite, 15 minutes après avoir créé l’image factice, vous pouvez correctement la supprimer.

  Vous pouvez ensuite essayer à nouveau de télécharger l’image de la VM ayant précédemment échoué.

- <!-- TBD -  IS ASDK --> Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, les utilisateurs doivent laisser expirer le délai d’attente plutôt que d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

- <!-- 1662991 - IS ASDK --> Les diagnostics de machine virtuelle Linux ne sont pas pris en charge dans Azure Stack. Lorsque vous déployez une machine virtuelle Linux en activant les diagnostics de machine virtuelle, le déploiement échoue. Le déploiement échoue également si vous activez les mesures de base de la machine virtuelle Linux dans les paramètres de diagnostic.

- <!-- 2724961- IS ASDK --> Quand vous inscrivez le fournisseur de ressources **Microsoft.Insight** dans les paramètres d’abonnement et que vous créez une machine virtuelle Windows avec diagnostics du système d’exploitation invité activés, le graphique de pourcentage d’UC dans la page de vue d’ensemble de machine virtuelle ne peut pas afficher les données de métriques. Pour trouver le graphique de pourcentage d’UC de la machine virtuelle, accédez au panneau **Métriques** et affichez toutes les métriques de machines virtuelles Windows invitées prises en charge.

#### <a name="networking"></a>Mise en réseau
- <!-- 1766332 - IS, ASDK --> Sous **Mise en réseau**, si vous cliquez sur **Créer une passerelle VPN** pour configurer une connexion VPN, l’option **Basé sur des stratégies** s’affiche dans la liste des types de VPN. Ne sélectionnez pas cette option. Seule l’option **Basé sur itinéraires** est prise en charge dans Azure Stack.

- <!-- 1902460 -  IS ASDK --> Azure Stack prend en charge une seule *passerelle de réseau local* par adresse IP. Cela est vrai pour tous les abonnements de locataire. Après la création de la première connexion de passerelle de réseau local, les tentatives suivantes de création d’une ressource de passerelle de réseau local avec la même adresse IP sont bloquées.

- <!-- 16309153 -  IS ASDK --> Sur un réseau virtuel qui a été créé avec un paramètre de serveur DNS défini sur *Automatique*, vous ne pouvez pas choisir un serveur DNS personnalisé. Les paramètres mis à jour ne sont pas envoyés (par push) aux machines virtuelles dans ce réseau virtuel.

- <!-- 2702741 -  IS ASDK --> La conservation des adresses IP publiques déployées à l’aide de la méthode d’allocation dynamique n’est pas garantie après l’émission d’une commande d’arrêt/désallocation.

- <!-- 2529607 - IS ASDK --> Durant la *rotation des secrets* Azure Stack, il existe une période pendant laquelle les adresses IP publiques sont inaccessibles pendant deux à cinq minutes.

-   <!-- 2664148 - IS ASDK --> Dans les cas où le locataire accède à ses machines virtuelles à l’aide d’un tunnel VPN S2S, il peut rencontrer un scénario où les tentatives de connexion échouent si le sous-réseau local a été ajouté à la passerelle de réseau local après la création de la passerelle. 


#### <a name="sql-and-mysql"></a>SQL et MySQL
- <!-- TBD - ASDK --> Les serveurs d’hébergement de base de données doivent être dédiés à une utilisation par le fournisseur de ressources et les charges de travail utilisateur. Vous ne pouvez pas utiliser une instance utilisée par un autre consommateur, notamment App Services.

- <!-- IS, ASDK --> Les caractères spéciaux, y compris les espaces et les points, ne sont pas pris en charge dans les noms **Famille** lors de la création d’une référence SKU pour les fournisseurs de ressources SQL et MySQL.

#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Les utilisateurs doivent inscrire le fournisseur de ressources de stockage avant de créer leur première fonction Azure dans l’abonnement.

- <!-- TBD - IS ASDK --> Pour augmenter la taille des instances sur l’infrastructure (Workers, gestion, rôles frontaux), vous devez utiliser PowerShell comme décrit dans les notes de publication pour Compute.  

- <!-- TBD - IS ASDK --> App Service peut uniquement être déployé sur l’*abonnement Fournisseur par défaut* pour l’instant. Dans une prochaine mise à jour, App Service pourra être déployé dans le nouvel *abonnement de contrôle* introduit dans Azure Stack 1804. Une fois le contrôle complètement pris en charge, tous les déploiements existants seront migrés vers ce type d’abonnement.

#### <a name="usage"></a>Usage  
- <!-- TBD -  IS ASDK --> Les données d’utilisation des adresses IP publiques indiquent une même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.

<!-- #### Identity -->






## <a name="build-11805147"></a>Build 1.1805.1.47

> [!TIP]  
> En fonction des commentaires des clients, il existe une mise à jour du schéma de la version en cours d’utilisation pour Microsoft Azure Stack. À compter de cette mise à jour, 1805, le nouveau schéma représente mieux la version cloud actuelle.  
>
> Le schéma de version est à présent *Version.YearYearMonthMonth.MinorVersion.BuildNumber* où le deuxième et le troisième ensemble indiquent la version et la publication. Par exemple, 1805.1 représente la *version finalisée* de 1805.  


### <a name="new-features"></a>Nouvelles fonctionnalités
Cette build inclut les améliorations et les correctifs suivants pour Azure Stack.  

- <!-- 2297790 - IS, ASDK --> **Azure Stack inclut désormais un client *Syslog* en tant** que *fonctionnalité préliminaire*. Ce client permet le transfert des journaux d’audit et de sécurité liés à l’infrastructure d’Azure Stack vers un serveur Syslog ou un logiciel SIEM externe à Azure Stack. Actuellement, le client Syslog ne prend en charge que les connexions UDP non authentifiées via le port par défaut 514. La charge utile de chaque message Syslog est formatée en CEF (Common Event Format).

  Pour configurer le client Syslog, utilisez la cmdlet **Set-SyslogServer** exposée dans le point de terminaison privilégié.

  Avec cette préversion, vous pouvez voir les trois alertes suivantes. Présentées par Azure Stack, ces alertes incluent des *descriptions* et des conseils de *correction*.
  - TITRE : Intégrité du code désactivée  
  - TITRE : Intégrité du code en mode audit
  - TITRE : Compte d’utilisateur créé

  Tant que cette fonctionnalité est en préversion, vous ne devez pas l’utiliser dans votre environnement de production.   


### <a name="fixed-issues"></a>Problèmes résolus
- Nous avons résolu le problème qui bloquait l’[ouverture d’une nouvelle requête de support dans la liste déroulante](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) depuis le portail d’administration. Cette option fonctionne désormais normalement.

- <!--  TBD ASDK --> La machine virtuelle qui héberge le point de terminaison privilégié (PEP) a été augmentée à 4 Go. Dans le Kit ASDK, cette machine virtuelle est nommée AzS-ERCS01.

- **Divers correctifs** pour les performances, la stabilité, la sécurité et le système d’exploitation utilisé par Azure Stack


<!-- ### Changes  -->


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Problèmes connus

#### <a name="portal"></a>Portail
- <!-- 2551834 - IS, ASDK --> Lorsque vous sélectionnez **Vue d’ensemble** pour un compte de stockage sur le portail d’administration ou utilisateur, les informations du volet *Éléments principaux* ne s’affichent pas.  Le volet Éléments principaux affiche des informations sur le compte comme son *groupe de ressources*, son *emplacement* et son *ID d’abonnement*.  D’autres options de la vue d’ensemble sont accessibles, par exemple *Services* et *Surveillance*, ainsi que des options permettant d’*Ouvrir dans Explorer* ou de *Supprimer le compte de stockage*.  

  Pour afficher les informations non disponibles, utilisez la cmdlet PowerShell [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0).

- <!-- 2551834 - IS, ASDK --> Lorsque vous sélectionnez **Balises** pour un compte de stockage sur le portail d’administration ou utilisateur, les informations ne se chargent pas.  

  Pour afficher les informations non disponibles, utilisez la cmdlet PowerShell [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0).

- <!-- TBD - IS ASDK --> N’utilisez pas les nouveaux types d’abonnement d’administration *Abonnement de contrôle* et *Abonnement de consommation*. Ces nouveaux types d’abonnement ont été introduits avec la version 1804 mais ne sont pas encore prêts à être utilisés. Vous devez continuer à utiliser le type d’abonnement *Fournisseur par défaut*.  
- <!-- TBD - IS ASDK --> Vous ne pouvez pas appliquer les mises à jour de pilote à l’aide d’un package d’extension OEM avec cette version d’Azure Stack.  Il n’existe aucune solution de contournement pour ce problème.
 
- <!-- TBD - IS ASDK --> La possibilité d’[ouvrir une nouvelle demande de support dans la liste déroulante](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) à partir du portail d’administration n’est pas disponible. À la place, utilisez le lien suivant :     
    - Pour le Kit de développement Azure Stack, utilisez https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> Il se peut que vous ne puissiez pas utiliser la barre de défilement horizontale au bas du portail d’administration et du portail utilisateur. Si vous ne pouvez pas accéder à la barre de défilement horizontale, utilisez les barres de navigation pour accéder à un panneau précédent dans le portail en sélectionnant le nom du panneau que vous souhaitez afficher dans la liste des barres de navigation en haut à gauche du portail.
  ![Barre de navigation](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> La suppression d’abonnements utilisateur génère des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.

- <!-- TBD -  IS ASDK --> Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack. Pour résoudre ce problème, utilisez PowerShell pour vérifier les autorisations.


#### <a name="health-and-monitoring"></a>Intégrité et surveillance
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

    Les deux alertes 1 et 2 peuvent être ignorées en toute sécurité. Elles se ferment automatiquement au bout d’un moment. 

  Vous pouvez également voir l’alerte suivante pour *Capacité*. Pour cette alerte, le pourcentage de mémoire disponible identifiée dans la description peut varier :  

  Alerte 3 :
   - NOM : Capacité de mémoire faible
   - GRAVITÉ : Critique
   - COMPOSANT : Capacité
   - DESCRIPTION : La région a consommé plus de 80 % de la mémoire disponible. La création de machines virtuelles avec de grandes quantités de mémoire peut échouer.  

  Dans cette version d’Azure Stack, cette alerte peut se déclencher de façon incorrecte. Si les machines virtuelles de locataire continuent le déploiement avec succès, vous pouvez ignorer cette alerte en toute sécurité. 
  
  L’alerte 3 n’est pas automatiquement fermée. Si vous fermez cette alerte, Azure Stack crée la même alerte dans les 15 minutes qui suivent.  

- <!-- 2368581 - IS. ASDK --> En tant qu’opérateur d’Azure Stack, si vous recevez une alerte d’insuffisance de mémoire et que les machines virtuelles client ne parviennent pas à se déployer à cause d’une *erreur de création de la structure de la machine virtuelle*, il est possible que l’horodatage d’Azure Stack soit en dehors de la mémoire disponible. Utilisez le [Capacity Planner Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) pour mieux comprendre la capacité disponible pour vos charges de travail.

- <!-- TBD - IS. ASDK --> L’exécution de l’applet de commande Test-AzureStack sur le point de terminaison privilégié (PEP) génère un message d’avertissement pour la machine virtuelle ERCS. Vous pouvez continuer à utiliser l’ASDK. 


#### <a name="compute"></a>Calcul
- <!-- TBD - IS, ASDK --> Lorsque vous sélectionnez une taille de machine virtuelle pour un déploiement de machine virtuelle, certaines tailles de machine virtuelle de série F ne sont pas visibles dans le sélecteur de taille lorsque vous créez une machine virtuelle. Les tailles de machine virtuelle suivantes n’apparaissent pas dans le sélecteur : *F8s_v2*, *F16s_v2*, *F32s_v2* et *F64s_v2*.  
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


- <!-- TBD -  IS ASDK --> Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Lorsque vous créez des machines virtuelles sur le portail utilisateur Azure Stack, ce dernier affiche un nombre incorrect de disques de données que vous pouvez attacher à une machine virtuelle de série D. Toutes les machines virtuelles de série D prises en charge peuvent prendre en charge autant de disques de données que la configuration Azure.

- <!-- TBD -  IS ASDK --> Lorsqu’une image de machine virtuelle ne peut pas être créée, un élément ayant échoué que vous ne pouvez pas supprimer peut être ajouté au panneau Compute des images de machine virtuelle.

  Pour contourner ce problème, créez une image de machine virtuelle avec un disque dur virtuel factice qui peut être créé via Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Ce processus doit résoudre le problème qui empêche la suppression de l’élément ayant échoué. Ensuite, 15 minutes après avoir créé l’image factice, vous pouvez correctement la supprimer.

  Vous pouvez ensuite essayer à nouveau de télécharger l’image de la VM ayant précédemment échoué.

- <!-- TBD -  IS ASDK --> Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, les utilisateurs doivent laisser expirer le délai d’attente plutôt que d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

- <!-- 1662991 - IS ASDK --> Les diagnostics de machine virtuelle Linux ne sont pas pris en charge dans Azure Stack. Lorsque vous déployez une machine virtuelle Linux en activant les diagnostics de machine virtuelle, le déploiement échoue. Le déploiement échoue également si vous activez les mesures de base de la machine virtuelle Linux dans les paramètres de diagnostic.

#### <a name="networking"></a>Mise en réseau
- <!-- TBD - IS ASDK --> Impossible de créer des itinéraires définis par l’utilisateur, que ce soit sur le portail d’administration ou le portail utilisateur. Pour résoudre ce problème, utilisez [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

- <!-- 1766332 - IS, ASDK --> Sous **Mise en réseau**, si vous cliquez sur **Créer une passerelle VPN** pour configurer une connexion VPN, l’option **Basé sur des stratégies** s’affiche dans la liste des types de VPN. Ne sélectionnez pas cette option. Seule l’option **Basé sur itinéraires** est prise en charge dans Azure Stack.

- <!-- 2388980 -  IS ASDK --> Lorsqu’une machine virtuelle est créée et associée à une adresse IP publique, vous ne pouvez pas dissocier cette machine virtuelle de cette adresse IP. La dissociation semble fonctionner, mais l’adresse IP publique qui a été assignée précédemment reste associée à la machine virtuelle d’origine.

  Actuellement, vous devez utiliser uniquement les nouvelles adresses IP publiques pour les nouvelles machines virtuelles que vous créez.

  Ce comportement se produit même si vous réaffectez l’adresse IP à une nouvelle machine virtuelle (ce qui est communément appelé un *échange d’adresses IP virtuelles*). Toutes les futures tentatives de connexion au moyen de cette adresse IP aboutissent à une connexion à la machine virtuelle originelle et non à la nouvelle.

- <!-- 2292271 - IS ASDK --> Si vous définissez une limite de quota pour une ressource réseau qui fait partie d’une offre et d’un plan associés à un abonnement de locataire, la nouvelle limite n’est pas appliquée à cet abonnement. Toutefois, la nouvelle limite s’applique aux nouveaux abonnements créés après l’augmentation du quota.

  Pour contourner ce problème, utilisez un plan d’extension pour augmenter un quota réseau quand le plan est déjà associé à un abonnement. Pour plus d’informations, découvrez comment [rendre un plan d’extension disponible](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Vous ne pouvez pas supprimer un abonnement auquel sont associées des ressources de la zone DNS ou de la table de routage. Pour supprimer l’abonnement, vous devez d’abord supprimer les ressources de la zone DNS ou de la table de routage de l’abonnement de locataire.


- <!-- 1902460 -  IS ASDK --> Azure Stack prend en charge une seule *passerelle de réseau local* par adresse IP. Cela est vrai pour tous les abonnements de locataire. Après la création de la première connexion de passerelle de réseau local, les tentatives suivantes de création d’une ressource de passerelle de réseau local avec la même adresse IP sont bloquées.

- <!-- 16309153 -  IS ASDK --> Sur un réseau virtuel qui a été créé avec un paramètre de serveur DNS défini sur *Automatique*, vous ne pouvez pas choisir un serveur DNS personnalisé. Les paramètres mis à jour ne sont pas envoyés (par push) aux machines virtuelles dans ce réseau virtuel.

- <!-- TBD -  IS ASDK --> Azure Stack ne prend pas en charge l’ajout d’interfaces réseau supplémentaires sur une instance de machine virtuelle une fois que la machine virtuelle est déployée. Si la machine virtuelle nécessite plusieurs interfaces réseau, elles doivent être définies au moment du déploiement.


#### <a name="sql-and-mysql"></a>SQL et MySQL
- <!-- TBD - ASDK --> Les serveurs d’hébergement de base de données doivent être dédiés à une utilisation par le fournisseur de ressources et les charges de travail utilisateur. Vous ne pouvez pas utiliser une instance utilisée par un autre consommateur, notamment App Services.

- <!-- IS, ASDK --> Les caractères spéciaux, y compris les espaces et les points, ne sont pas pris en charge dans les noms **Famille** lors de la création d’une référence SKU pour les fournisseurs de ressources SQL et MySQL.

#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Les utilisateurs doivent inscrire le fournisseur de ressources de stockage avant de créer leur première fonction Azure dans l’abonnement.

- <!-- TBD - IS ASDK --> Pour augmenter la taille des instances sur l’infrastructure (Workers, gestion, rôles frontaux), vous devez utiliser PowerShell comme décrit dans les notes de publication pour Compute.  

- <!-- TBD - IS ASDK --> App Service peut uniquement être déployé sur l’*abonnement Fournisseur par défaut* pour l’instant. <!-- In a future update, App Service will deploy into the new *Metering subscription* that was introduced in Azure Stack 1804. When Metering is supported for use, all existing deployments will be migrated to this new subscription type. -->

#### <a name="usage"></a>Usage  
- <!-- TBD -  IS ASDK --> Les données d’utilisation des adresses IP publiques indiquent une même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.

<!-- #### Identity -->


## <a name="build-201805131"></a>Build 20180513.1

### <a name="new-features"></a>Nouvelles fonctionnalités
Cette build inclut les améliorations et les correctifs suivants pour Azure Stack.  

- <!-- 1759172 - IS, ASDK --> **Nouveaux abonnements d’administration**. Avec 1804, deux nouveaux types d’abonnement sont disponibles dans le portail. Ces nouveaux types d’abonnement viennent s’ajouter à l’abonnement Fournisseur par défaut et sont visibles dans les nouvelles installations d’Azure Stack depuis la version 1804. *N’utilisez pas ces nouveaux types d’abonnement avec cette version d’Azure Stack*. <!-- We will announce the availability to use these subscription types in with a future update. -->

  Ces nouveaux types d’abonnement sont visibles mais s’inscrivent dans un changement plus large destiné à sécuriser l’abonnement Fournisseur par défaut et à faciliter le déploiement de ressources partagées telles que les serveurs d’hébergement SQL.

  Les trois types d’abonnement maintenant disponibles sont les suivants :  
  - Abonnement Fournisseur par défaut : continuez à utiliser ce type d’abonnement.
  - Abonnement À l’usage : *n’utilisez pas ce type d’abonnement.*
  - Abonnement Consommation : *n’utilisez pas ce type d’abonnement*

### <a name="fixed-issues"></a>Problèmes résolus
- <!-- IS, ASDK -->  Dans le portail d’administration, vous ne devez plus actualiser la vignette Mise à jour pour afficher des informations.

- <!-- 2050709 - IS, ASDK -->  Vous pouvez maintenant utiliser le portail d’administration pour modifier les métriques de stockage pour le service Blob, le service de Table et le service de File d’attente.

- <!-- IS, ASDK --> Sous **Mise en réseau**, lorsque vous cliquez sur **Connexion** pour configurer une connexion VPN, **Site à site (IPsec)** est maintenant la seule option disponible.

- **Divers correctifs** pour les performances, la stabilité, la sécurité et le système d’exploitation utilisé par Azure Stack

<!-- ### Changes  -->
### <a name="additional-releases-timed-with-this-update"></a>Autres versions synchronisées avec cette mise à jour  
Les versions suivantes sont maintenant disponibles, mais ne nécessitent pas la mise à jour 1804 d’Azure Stack.
- **Mise à jour avec le Pack d’analyse System Center Operations Manager de Microsoft Azure Stack**. Une nouvelle version (1.0.3.0) du Pack d’analyse System Center Operations Manager de Microsoft Azure Stack est disponible au [téléchargement](https://www.microsoft.com/download/details.aspx?id=55184). Avec cette version, vous pouvez utiliser des principaux de service quand vous ajoutez un déploiement Azure Stack connecté. Cette version propose également une expérience de gestion des mises à jour qui vous permet d’effectuer une action de correction directement à partir d’Operations Manager. Il existe aussi des nouveaux tableaux de bord qui affichent les fournisseurs de ressources, les unités d’échelle et les nœuds d’unités d’échelle.

- **Nouvelle version 1.3.0 d’Azure Stack PowerShell pour les administrateurs**.  Azure Stack PowerShell version 1.3.0 est désormais disponible pour l’installation. Cette version fournit des commandes pour tous les fournisseurs de ressources d’administrateur pour gérer Azure Stack.  Avec cette version, une partie du contenu sera dépréciée dans le [dépôt](https://github.com/Azure/AzureStack-Tools) GitHub d’outils Azure Stack.

   Pour obtenir les détails de l’installation, suivez les [instructions](.\.\azure-stack-powershell-install.md) ou le contenu d’[aide](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) du Module Azure Stack 1.3.0.

- **Informations de référence sur la version initiale de l’API REST Azure Stack**. Les [informations de référence sur l’API pour tous les fournisseurs de ressources d’administrateur Azure Stack](https://docs.microsoft.com/rest/api/azure-stack/) sont maintenant publiées.

### <a name="known-issues"></a>Problèmes connus

#### <a name="portal"></a>Portail
- <!-- TBD - IS ASDK --> La possibilité d’[ouvrir une nouvelle demande de support dans la liste déroulante](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) à partir du portail d’administration n’est pas disponible. À la place, utilisez le lien suivant :     
    - Pour le Kit de développement Azure Stack, utilisez https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> Il se peut que vous ne puissiez pas utiliser la barre de défilement horizontale au bas du portail d’administration et du portail utilisateur. Si vous ne pouvez pas accéder à la barre de défilement horizontale, utilisez les barres de navigation pour accéder à un panneau précédent dans le portail en sélectionnant le nom du panneau que vous souhaitez afficher dans la liste des barres de navigation en haut à gauche du portail.
  ![Barre de navigation](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> La suppression d’abonnements utilisateur génère des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.

- <!-- TBD -  IS ASDK --> Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack. Pour résoudre ce problème, utilisez PowerShell pour vérifier les autorisations.

-   <!-- TBD -  IS ASDK --> Dans le portail d’administration, vous pouvez voir une alerte critique pour le composant Microsoft.Update.Admin. Le nom de l’alerte, la description et la correction s’affichent tous comme suit :  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (ERREUR - Absence du modèle de FaultType ResourceProviderTimeout.)

    Cette alerte peut être ignorée en toute sécurité.

#### <a name="health-and-monitoring"></a>Intégrité et surveillance
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

  Ces deux alertes peuvent être ignorées en toute sécurité. Elles se ferment automatiquement dans le temps.  

#### <a name="marketplace"></a>Marketplace
- Les utilisateurs ont la possibilité de parcourir entièrement la Place de marché, et peuvent voir des éléments administratifs, tels que des plans et des offres, qui ne sont pas fonctionnels pour eux.

#### <a name="compute"></a>Calcul
- <!-- TBD -  IS ASDK --> Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Lorsque vous créez des machines virtuelles sur le portail utilisateur Azure Stack, ce dernier affiche un nombre incorrect de disques de données que vous pouvez attacher à une machine virtuelle de série DS. Les machines virtuelles de série DS peuvent prendre en charge autant de disques de données que la configuration Azure.

- <!-- TBD -  IS ASDK --> Lorsqu’une image de machine virtuelle ne peut pas être créée, un élément ayant échoué que vous ne pouvez pas supprimer peut être ajouté au panneau Compute des images de machine virtuelle.

  Pour contourner ce problème, créez une image de machine virtuelle avec un disque dur virtuel factice qui peut être créé via Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Ce processus doit résoudre le problème qui empêche la suppression de l’élément ayant échoué. Ensuite, 15 minutes après avoir créé l’image factice, vous pouvez correctement la supprimer.

  Vous pouvez ensuite essayer à nouveau de télécharger l’image de la VM ayant précédemment échoué.

- <!-- TBD -  IS ASDK --> Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, les utilisateurs doivent laisser expirer le délai d’attente plutôt que d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

- <!-- 1662991 - IS ASDK --> Les diagnostics de machine virtuelle Linux ne sont pas pris en charge dans Azure Stack. Lorsque vous déployez une machine virtuelle Linux en activant les diagnostics de machine virtuelle, le déploiement échoue. Le déploiement échoue également si vous activez les mesures de base de la machine virtuelle Linux dans les paramètres de diagnostic.

#### <a name="networking"></a>Mise en réseau
- <!-- 1766332 - IS, ASDK --> Sous **Mise en réseau**, si vous cliquez sur **Créer une passerelle VPN** pour configurer une connexion VPN, l’option **Basé sur des stratégies** s’affiche dans la liste des types de VPN. Ne sélectionnez pas cette option. Seule l’option **Basé sur itinéraires** est prise en charge dans Azure Stack.

- <!-- 2388980 -  IS ASDK --> Lorsqu’une machine virtuelle est créée et associée à une adresse IP publique, vous ne pouvez pas dissocier cette machine virtuelle de cette adresse IP. La dissociation semble fonctionner, mais l’adresse IP publique qui a été assignée précédemment reste associée à la machine virtuelle d’origine.

  Actuellement, vous devez utiliser uniquement les nouvelles adresses IP publiques pour les nouvelles machines virtuelles que vous créez.

  Ce comportement se produit même si vous réaffectez l’adresse IP à une nouvelle machine virtuelle (ce qui est communément appelé un *échange d’adresses IP virtuelles*). Toutes les futures tentatives de connexion au moyen de cette adresse IP aboutissent à une connexion à la machine virtuelle originelle et non à la nouvelle.

- <!-- 2292271 - IS ASDK --> Si vous définissez une limite de quota pour une ressource réseau qui fait partie d’une offre et d’un plan associés à un abonnement de locataire, la nouvelle limite n’est pas appliquée à cet abonnement. Toutefois, la nouvelle limite s’applique aux nouveaux abonnements créés après l’augmentation du quota.

  Pour contourner ce problème, utilisez un plan d’extension pour augmenter un quota réseau quand le plan est déjà associé à un abonnement. Pour plus d’informations, découvrez comment [rendre un plan d’extension disponible](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Vous ne pouvez pas supprimer un abonnement auquel sont associées des ressources de la zone DNS ou de la table de routage. Pour supprimer l’abonnement, vous devez d’abord supprimer les ressources de la zone DNS ou de la table de routage de l’abonnement de locataire.


- <!-- 1902460 -  IS ASDK --> Azure Stack prend en charge une seule *passerelle de réseau local* par adresse IP. Cela est vrai pour tous les abonnements de locataire. Après la création de la première connexion de passerelle de réseau local, les tentatives suivantes de création d’une ressource de passerelle de réseau local avec la même adresse IP sont bloquées.

- <!-- 16309153 -  IS ASDK --> Sur un réseau virtuel qui a été créé avec un paramètre de serveur DNS défini sur *Automatique*, vous ne pouvez pas choisir un serveur DNS personnalisé. Les paramètres mis à jour ne sont pas envoyés (par push) aux machines virtuelles dans ce réseau virtuel.

- <!-- TBD -  IS ASDK --> Azure Stack ne prend pas en charge l’ajout d’interfaces réseau supplémentaires sur une instance de machine virtuelle une fois que la machine virtuelle est déployée. Si la machine virtuelle nécessite plusieurs interfaces réseau, elles doivent être définies au moment du déploiement.


#### <a name="sql-and-mysql"></a>SQL et MySQL
- <!-- TBD - ASDK --> Les serveurs d’hébergement de base de données doivent être dédiés à une utilisation par le fournisseur de ressources et les charges de travail utilisateur. Vous ne pouvez pas utiliser une instance utilisée par un autre consommateur, notamment App Services.

- <!-- IS, ASDK --> Les caractères spéciaux, y compris les espaces et les points, ne sont pas pris en charge dans les noms **Famille** lors de la création d’une référence SKU pour les fournisseurs de ressources SQL et MySQL.

#### <a name="app-service"></a>App Service
- <!-- TBD -  IS ASDK --> Les utilisateurs doivent inscrire le fournisseur de ressources de stockage avant de créer leur première fonction Azure dans l’abonnement.

- <!-- TBD -  IS ASDK --> Pour augmenter la taille des instances sur l’infrastructure (Workers, gestion, rôles frontaux), vous devez utiliser PowerShell comme décrit dans les notes de publication pour Compute.

#### <a name="usage"></a>Usage  
- <!-- TBD -  IS ASDK --> Les données d’utilisation des adresses IP publiques indiquent une même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>Téléchargement des outils Azure Stack à partir de GitHub
- Quand vous utilisez l’applet de commande PowerShell *invoke-webrequest* pour télécharger les outils Azure Stack à partir de Github, une erreur s’affiche :     
    -  *invoke-webrequest : La demande a été abandonnée : Impossible de créer un canal sécurisé SSL/TLS.*     

  Cette erreur se produit en raison d’une désapprobation récente de la prise en charge GitHub des normes de chiffrement Tlsv1 et Tlsv1.1 (valeur par défaut pour PowerShell). Pour plus d’informations, consultez [Avis de suppression des normes de chiffrement faible](https://githubengineering.com/crypto-removal-notice/).

<!-- #### Identity -->



