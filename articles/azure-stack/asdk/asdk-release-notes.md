---
title: Notes de publication du Kit de développement Microsoft Azure Stack | Microsoft Docs
description: Améliorations, correctifs et problèmes connus pour le Kit de développement Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: sethm
ms.reviewer: misainat
ms.openlocfilehash: 27dbd4215deef6574622ffcd2c62a64503459258
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2018
ms.locfileid: "51515758"
---
# <a name="asdk-release-notes"></a>Notes de publication relatives à l’ASDK  
Cet article fournit des informations sur des améliorations, des correctifs et des problèmes connus en lien avec le Kit de développement Azure Stack (ASDK). Si vous n’êtes pas sûr de la version que vous exécutez, consultez le [portail pour vérifier](.\.\azure-stack-updates.md#determine-the-current-version).

> Restez informé des nouveautés concernant l’ASDK en vous abonnant au [![flux](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11809090"></a>Build 1.1809.0.90

### <a name="new-features"></a>Nouvelles fonctionnalités
Cette build inclut les améliorations et les correctifs suivants pour Azure Stack.  

<!--  2712869   | IS  ASDK -->  
- **Client Syslog Azure Stack (disponibilité générale)**  Ce client permet de transférer des journaux d’audit, d’alertes et de sécurité relatifs à l’infrastructure Azure Stack vers un serveur Syslog ou un logiciel SIEM (Security Information and Event Management, gestion des informations et événements de sécurité) externe à Azure Stack. Le client Syslog prend désormais en charge la spécification du port sur lequel écoute le serveur Syslog.

Avec cette publication, le client Syslog devient généralement disponible et peut être utilisé en environnement de production.

Pour plus d’informations, consultez [Transfert de syslog dans Azure Stack](../azure-stack-integrate-security.md).

### <a name="fixed-issues"></a>Problèmes résolus

<!-- TBD - IS ASDK -->
- Dans le portail, le graphique indiquant la mémoire libre et la capacité utilisée est désormais plus précis. Vous pouvez maintenant prévoir de manière plus fiable le nombre de machines virtuelles que vous êtes en mesure de créer.

<!-- TBD - IS ASDK --> 
- Le problème a été résolu, dans lequel le portail affichait un nombre incorrect de disques de données que vous pouvez attacher à une machine virtuelle de la série DS lorsque vous créez des machines virtuelles sur le portail utilisateur Azure Stack. Les machines virtuelles de série DS peuvent prendre en charge autant de disques de données que la configuration Azure.

- Les problèmes de disque managé suivants ont été corrigés dans la mise à jour 1809 ainsi que dans la mise à jour 1808 [Correctif Azure Stack 1.1808.7.113](https://support.microsoft.com/help/4471992/) : 

   <!--  2966665 – IS, ASDK --> 
   - Le problème a été résolu, dans lequel l’attachement de disques de données SSD à des machines virtuelles avec des disques managés de taille Premium (DS, DSv2, Fs, Fs_V2) échouait avec une erreur : *Échec de la mise à jour des disques pour la machine virtuelle ’vmname’ Erreur : l’opération demandée ne peut pas être effectuée, car le type de compte de stockage ’Premium_LRS’ n’est pas pris en charge pour la taille de machine virtuelle ’Standard_DS/Ds_V2/FS/Fs_v2)’*. 
   
   - La création d’une machine virtuelle avec disque managé à l’aide de **createOption** : **Attacher** échoue avec l’erreur suivante : *Échec de l’opération de longue durée avec l’état 'Failed'. Informations supplémentaires : 'Une erreur d'exécution interne s'est produite.'*
   ErrorCode: InternalExecutionError ErrorMessage : une erreur d’exécution interne s’est produite.
   
   Ce problème est à présent résolu.

- <!-- 2702741 -  IS, ASDK --> Problème résolu de la conservation des adresses IP publiques déployées à l’aide de la méthode d’allocation dynamique qui n’était pas garantie après l’émission d’une commande d’arrêt/libération. Elles sont désormais conservées.

- <!-- 3078022 - IS, ASDK --> Si une machine virtuelle était arrêtée/libérée avant la mise à jour 1808, il n’était pas possible de la réallouer après cette mise à jour.  Ce problème a été résolu dans la mise à jour 1809. Grâce à ce correctif introduit dans la mise à jour 1809, les instances qui se trouvaient dans cet état empêchant leur démarrage peuvent désormais être démarrées. Le correctif empêche également que ce problème se reproduise.

<!-- 3090289 – IS, ASDK --> 
- Après avoir appliqué la mise à jour 1808 qui corrige une erreur, vous rencontrerez peut-être les problèmes suivants lors du déploiement de machines virtuelles avec le service Managed Disks :

   1. Si l’abonnement a été créé avant la mise à jour 1808, le déploiement de machines virtuelles avec Managed Disks peut échouer avec un message d’erreur interne. Pour résoudre cette erreur, effectuez les étapes suivantes pour chaque abonnement :
      1. Dans le portail locataire, accédez à **Abonnements** et recherchez l’abonnement. Cliquez sur **Fournisseurs de ressources**, sur **Microsoft.Compute**, puis sur **Réinscrire**.
      2. Sous le même abonnement, accédez à **Contrôle d’accès (IAM)** et vérifiez que l’élément **Azure Stack – Managed Disks** est répertorié.
   2. Si vous avez configuré un environnement multilocataire, le déploiement de machines virtuelles dans un abonnement associé à un annuaire invité peut échouer avec un message d’erreur interne. Pour résoudre cette erreur, effectuez les étapes suivantes :
      1. Appliquez le [correctif 1808 d’Azure Stack](https://support.microsoft.com/help/4471992).
      2. Effectuez les étapes décrites dans [cet article](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) pour reconfigurer chacun de vos annuaires invités.

- **Divers correctifs** pour les performances, la stabilité, la sécurité et le système d’exploitation utilisé par Azure Stack


### <a name="changes"></a>Changements

### <a name="known-issues"></a>Problèmes connus

#### <a name="portal"></a>Portail  

<!-- 2515955   | IS ,ASDK--> 
- L’option *Tous les services* remplace l’option *Autres services* dans les portails d’administration et utilisateur Azure Stack. Vous pouvez maintenant utiliser *Tous les services* comme alternative pour naviguer dans les portails Azure Stack de la même façon que dans les portails Azure.

<!--  TBD – IS, ASDK --> 
- Les tailles de machine virtuelle *De base A* ne sont plus disponibles pour la [création de groupes de machines virtuelles identiques](../azure-stack-compute-add-scalesets.md) (VMSS) dans le portail. Pour créer un groupe de machines virtuelles identiques avec cette taille, utilisez PowerShell ou un modèle.

#### <a name="health-and-monitoring"></a>Intégrité et surveillance

<!-- 1264761 - IS ASDK -->  
- Vous risquez de recevoir des alertes pour le composant *Contrôleur d’intégrité* contenant les informations suivantes :  

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

<!-- 2368581 - IS. ASDK --> 
- En tant qu’opérateur d’Azure Stack, si vous recevez une alerte de mémoire insuffisante et que les machines virtuelles de locataire ne parviennent pas à se déployer à cause d’une *erreur de création de machine virtuelle de structure*, il est possible que l’horodatage d’Azure Stack soit à court de mémoire. Utilisez le [Capacity Planner Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) pour mieux comprendre la capacité disponible pour vos charges de travail.


#### <a name="compute"></a>Calcul 

<!-- 3164607 – IS, ASDK -->
- Le rattachement d’un disque détaché à la même machine virtuelle (VM) avec les mêmes nom et numéro d’unité logique échoue et affiche une erreur de type **Impossible d’attacher le disque de données « datadisk » à la machine virtuelle « vm1 »**. Cette erreur est due au fait que le disque est en cours de détachement ou que la dernière opération de détachement a échoué. Veuillez attendre que le disque soit complètement détaché, puis réessayez ou supprimez/détachez explicitement le disque une nouvelle fois. La solution de contournement consiste à le rattacher en utilisant un nom ou un numéro d’unité logique différent. 

<!-- 3235634 – IS, ASDK -->
- Pour déployer des machines virtuelles avec des tailles contenant un suffixe **v2** ; par exemple, **Standard_A2_v2**, spécifiez le suffixe sous la forme **Standard_A2_v2** (v minuscule). N’utilisez pas **Standard_A2_V2** (V majuscule). Cette méthode fonctionne dans Azure global et constitue une incohérence sur Azure Stack.

<!-- 3099544 – IS, ASDK --> 
- Quand vous créez une machine virtuelle à l’aide du portail Azure Stack et sélectionnez la taille de machine virtuelle, la colonne EUR/mois s’affiche avec le message **Indisponible**. Cette colonne ne devrait pas s’afficher, car l’affichage de la colonne des prix des machines virtuelles n’est pas pris en charge dans Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Quand vous utilisez [l’applet de commande **Add-AzsPlatformImage**](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), vous devez spécifier le paramètre **-OsUri** comme URI du compte de stockage où le disque est chargé. Si vous utilisez le chemin local du disque, la cmdlet échoue avec l’erreur suivante : *Échec de l’opération de longue durée avec l’état 'Failed'*. 

<!--  2966665 – IS, ASDK --> 
- L’attachement de disques de données SSD à des machines virtuelles avec des disques managés de taille Premium (DS, DSv2, Fs, Fs_V2) échoue avec une erreur : *Échec de la mise à jour des disques pour la machine virtuelle ’vmname’ Erreur : l’opération demandée ne peut pas être effectuée, car le type de compte de stockage ’Premium_LRS’ n’est pas pris en charge pour la taille de machine virtuelle ’Standard_DS/Ds_V2/FS/Fs_v2)’*

   Pour contourner ce problème, utilisez des disques de données *Standard_LRS* à la place de *disques Premium_LRS*. L’utilisation de disques de données *Standard_LRS* n’a pas d’impact sur l’IOP ou le coût de facturation.  

<!--  2795678 – IS, ASDK --> 
- Quand vous utilisez le portail pour créer des machines virtuelles de taille Premium (DS, Ds_v2, FS, FSv2), celles-ci sont créées dans un compte de stockage standard. La création dans un compte de stockage standard n’a pas d’impact sur les fonctionnalités, l’IOP ou la facturation. 

   Vous pouvez ignorer sans problème cet avertissement : *Vous avez choisi d’utiliser un disque standard sur une taille qui prend en charge les disques Premium. Cela n’est pas recommandé, car cela peut impacter les performances du système d’exploitation. Utilisez le stockage Premium (SSD) à la place.*

<!-- 2967447 - IS, ASDK --> 
- Quand vous créez un groupe de machines virtuelles identiques, l’option CentOS 7.2 est proposée pour le déploiement. Étant donné que cette image n’est pas disponible sur Azure Stack, sélectionnez un autre système d’exploitation pour votre déploiement, ou choisissez un modèle Azure Resource Manager spécifiant une autre image CentOS qui a été téléchargée par l’opérateur avant le déploiement à partir de la Place de marché.

<!-- TBD -  IS ASDK --> 
- Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

<!-- TBD -  IS ASDK --> 
- Lorsqu’une image de machine virtuelle ne peut pas être créée, un élément ayant échoué que vous ne pouvez pas supprimer peut être ajouté au panneau Compute des images de machine virtuelle.

  Pour contourner ce problème, créez une image de machine virtuelle avec un disque dur virtuel factice qui peut être créé via Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Ce processus doit résoudre le problème qui empêche la suppression de l’élément ayant échoué. Ensuite, 15 minutes après avoir créé l’image factice, vous pouvez correctement la supprimer.

  Vous pouvez ensuite essayer à nouveau de télécharger l’image de la VM ayant précédemment échoué.

<!-- TBD -  IS ASDK --> 
- Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, les utilisateurs doivent laisser expirer le délai d’attente plutôt que d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

<!-- 1662991 - IS ASDK --> 
- Les diagnostics de machine virtuelle Linux ne sont pas pris en charge dans Azure Stack. Lorsque vous déployez une machine virtuelle Linux en activant les diagnostics de machine virtuelle, le déploiement échoue. Le déploiement échoue également si vous activez les mesures de base de la machine virtuelle Linux dans les paramètres de diagnostic.

<!-- 2724961- IS ASDK --> 
- Quand vous inscrivez le fournisseur de ressources **Microsoft.Insight** dans les paramètres d’abonnement et créez une machine virtuelle Windows avec les diagnostics du système d’exploitation invité activés, le graphique de pourcentage d’UC dans la page de vue d’ensemble de machine virtuelle ne peut pas afficher les données de métriques.
 
  Pour trouver le graphique de pourcentage d’UC de la machine virtuelle, accédez au panneau **Métriques** et affichez toutes les métriques de machines virtuelles Windows invitées prises en charge.

 

#### <a name="networking"></a>Mise en réseau
<!-- 1766332 - IS, ASDK --> 
- Sous **Mise en réseau**, si vous cliquez sur **Créer une passerelle VPN** pour configurer une connexion VPN, l’option **Basé sur des stratégies** s’affiche dans la liste des types de VPN. Ne sélectionnez pas cette option. Seule l’option **Basé sur itinéraires** est prise en charge dans Azure Stack.

<!-- 1902460 -  IS ASDK --> 
- Azure Stack prend en charge une seule *passerelle de réseau local* par adresse IP. Cela est vrai pour tous les abonnements de locataire. Après la création de la première connexion de passerelle de réseau local, les tentatives suivantes de création d’une ressource de passerelle de réseau local avec la même adresse IP sont bloquées.

<!-- 16309153 -  IS ASDK --> 
- Sur un réseau virtuel a été créé avec un paramètre de serveur DNS défini sur *Automatique*, vous ne pouvez pas choisir un serveur DNS personnalisé. Les paramètres mis à jour ne sont pas envoyés (par push) aux machines virtuelles dans ce réseau virtuel.

<!-- 2702741 -  IS ASDK --> 
- La conservation des adresses IP publiques déployées à l’aide de la méthode d’allocation dynamique n’est pas garantie après l’émission d’une commande Arrêter/Libérer.

<!-- 2529607 - IS ASDK --> 
- Durant la *rotation des secrets* Azure Stack, une période de deux à cinq minutes s’écoule pendant laquelle les adresses IP publiques sont inaccessibles.

<!-- 2664148 - IS ASDK --> 
- Dans les cas où le locataire accède à ses machines virtuelles à l’aide d’un tunnel VPN S2S, il peut rencontrer un scénario où les tentatives de connexion échouent si le sous-réseau local a été ajouté à la passerelle de réseau local après la création de la passerelle. 


<!--  #### SQL and MySQL  -->


#### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- Les utilisateurs doivent inscrire le fournisseur de ressources de stockage avant de créer leur première fonction Azure dans l’abonnement.

<!-- TBD - IS ASDK --> 
- Pour monter en puissance l’infrastructure (Workers, gestion, rôles frontaux), vous devez utiliser PowerShell comme décrit dans les notes de publication pour Compute.  


#### <a name="usage"></a>Usage  
<!-- TBD -  IS ASDK --> 
- Les données d’utilisation des adresses IP publiques indiquent une même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.

<!-- #### Identity -->

## <a name="build-11808097"></a>Build 1.1808.0.97

### <a name="new-features"></a>Nouvelles fonctionnalités
Cette build inclut les améliorations et les correctifs suivants pour Azure Stack.  

<!-- 1658937 | ASDK, IS --> 
- **Démarrer les sauvegardes selon un calendrier prédéfini** : En tant qu’appliance, Azure Stack peut maintenant lancer automatiquement des sauvegardes périodiques de l’infrastructure afin d’éviter toute intervention humaine. Azure Stack nettoie également automatiquement le partage externe des sauvegardes antérieures à la période de rétention définie. Pour en savoir plus, voir [Activer la sauvegarde d’Azure Stack avec PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).

<!-- 2496385 | ASDK, IS -->  
- **Ajout du temps de transfert des données au temps total de sauvegarde.** Pour en savoir plus, voir [Activer la sauvegarde d’Azure Stack avec PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).

<!-- 1702130 | ASDK, IS --> 
- **La capacité externe de sauvegarde affiche maintenant la capacité correcte du partage externe.** (la valeur était codée en dur à 10 Go) Pour en savoir plus, voir [Activer la sauvegarde d’Azure Stack avec PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).
 
<!-- 2753130 |  IS, ASDK   -->  
- **Les modèles Azure Resource Manager prennent désormais en charge l’élément de condition** : Vous pouvez désormais déployer une ressource dans un modèle Azure Resource Manager à l’aide d’une condition. Vous pouvez concevoir votre modèle afin de déployer une ressource en fonction d’une condition, par exemple évaluer si une valeur de paramètre est présente. Pour plus d’informations sur l’utilisation d’un modèle en tant que condition, consultez [Déployer une ressource de manière conditionnelle](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) et [Section Variables des modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) dans la documentation Azure. 

   Vous pouvez également utiliser des modèles pour [déployer des ressources Azure sur plusieurs groupes de ressources ou abonnements](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

<!--2753073 | IS, ASDK -->  
- **La prise en charge de la version de ressource d’API Microsoft.Network a été mise à jour** pour inclure la prise en charge de la version d’API 2017-10-01 à partir de la version 2015-06-15 pour les ressources réseau Azure Stack. La prise en charge des versions de ressources entre 2017-10-01 et 2015-06-15 n’est pas incluse dans cette version. Pour connaître les différences de fonctionnalités, consultez [Considérations relatives à la mise en réseau Azure Stack](.\.\user\azure-stack-network-differences.md).

<!-- 2272116 | IS, ASDK   -->  
- **Azure Stack a ajouté la prise en charge des recherches DNS inversées pour les points de terminaison d’infrastructure Azure Stack externes** (autrement dit pour portal, adminportal, management et adminmanagement). Cela permet de résoudre les noms des points de terminaison externes Azure Stack à partir d’une adresse IP.

<!-- 2780899 |  IS, ASDK   --> 
- **Azure Stack prend désormais en charge l’ajout d’interfaces réseau supplémentaires à une machine virtuelle existante.**  Cette fonctionnalité est disponible à l’aide du portail, de PowerShell et de l’interface CLI. Pour plus d’informations, consultez [Ajouter ou supprimer des interfaces réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) dans la documentation Azure. 

<!-- 2222444 | IS, ASDK   -->  
- **Des améliorations en matière de précision et de résilience ont été apportées aux compteurs d’utilisation de mise en réseau**. Les compteurs d’utilisation réseau sont désormais plus précis et tiennent compte des abonnements suspendus, des périodes d’interruption et des conditions de concurrence.

<!-- 2297790 | IS, ASDK -->  
- **Améliorations apportées au client syslog Azure Stack (fonctionnalité en préversion)**. Ce client permet le transfert des journaux et de l’audit liés à l’infrastructure d’Azure Stack vers un serveur Syslog ou un logiciel SIEM externe à Azure Stack. Le client syslog prend désormais en charge le protocole TCP avec texte brut ou chiffrement TLS 1.2, cette dernière option étant la configuration par défaut. Vous pouvez configurer la connexion TLS avec authentification mutuelle ou serveur uniquement.

  Pour configurer la façon dont le client syslog communique avec le serveur syslog (par exemple le protocole, le chiffrement et l’authentification), utilisez l’applet de commande Set-SyslogServer. Cette applet de commande est disponible à partir du point de terminaison privilégié (PEP). 

- <!-- ASDK --> **Les éléments de la galerie pour les groupes de machines virtuelles identiques sont maintenant intégrés**.  Les éléments de galerie Groupe de machines virtuelles identiques sont désormais accessible dans les portails administrateur et utilisateur sans avoir à les télécharger. 

- <!-- IS, ASDK --> **Mise à l’échelle de groupes de machines virtuelles identiques**.  Vous pouvez utiliser le portail pour [mettre à l’échelle un groupe de machines virtuelles identiques](/azure/azure-stack/azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set).   

- <!-- 2489570 | IS ASDK--> **Prise en charge des configurations de stratégie IPSec/IKE personnalisées** pour les [passerelles VPN dans Azure Stack](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways).

- <!-- | IS ASDK--> **Élément Kubernetes de la Place de marché**. Vous pouvez désormais déployer des clusters Kubernetes avec [l’élément Kubernetes de la Place de marché](/azure/azure-stack/azure-stack-solution-template-kubernetes-cluster-add). Pour déployer un cluster Kubernetes sur Azure Stack, les utilisateurs n’ont qu’à sélectionner l’élément Kubernetes et renseigner quelques paramètres. L’objectif des modèles est de permettre aux utilisateurs de créer des déploiements Kubernetes de développement et de test en quelques étapes seulement.

<!-- ####### | IS, ASDK -->  
- **Azure Resource Manager inclut le nom de la région.** Avec cette version, les objets récupérés à partir d’Azure Resource Manager incluront désormais l’attribut de nom de région. Si un script PowerShell existant passe directement l’objet à une autre applet de commande, le script peut générer une erreur et échouer. Ce comportement est conforme à Azure Resource Manager et oblige le client appelant à soustraire l’attribut de région. Pour plus d’informations sur Azure Resource Manager, consultez la [documentation Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

<!-- TBD | IS, ASDK -->  
- **Déplacer des abonnements entre des fournisseurs délégués.** Vous pouvez maintenant déplacer des abonnements entre des abonnements de fournisseurs délégués nouveaux ou existants appartenant au même locataire d’annuaire. Les abonnements appartenant à l’abonnement Fournisseur par défaut peuvent également être déplacés vers les abonnements Fournisseur délégué dans le même locataire d’annuaire. Pour plus d’informations, consultez [Déléguer des offres dans Azure Stack](.\.\azure-stack-delegated-provider.md).
 
<!-- 2536808 IS ASDK --> 
- **Amélioration du temps de création de machine virtuelle** pour les machines virtuelles qui sont créées avec des images que vous téléchargez à partir de la Place de marché Azure.

### <a name="fixed-issues"></a>Problèmes résolus
- <!-- IS ASDK--> Nous avons résolu le problème de la création d’un groupe à haute disponibilité dans le portail qui créait un groupe avec un domaine d’erreur et un domaine de mise à jour 1.

<!-- TBD | ASDK, IS --> 
- Différentes améliorations ont été apportées au processus de mise à jour afin de le rendre plus fiable. Des correctifs ont également été apportés à l’infrastructure sous-jacente, qui améliore le vidage des nœuds, réduisant ainsi le risque d’interruption pour les charges de travail pendant la mise à jour.

<!--2292271 | ASDK, IS --> 
- Nous avons corrigé un problème qui empêchait l’application d’une limite de quota modifiée aux abonnements existants.  Désormais, lorsque vous augmentez la limite de quota d’une ressource réseau faisant partie d’une offre et d’un plan associés à un abonnement d’utilisateur, la nouvelle limite s’applique aux abonnements préexistants, ainsi qu’aux nouveaux abonnements.

<!-- 2448955 | IS ASDK --> 
- Il est désormais possible d’interroger correctement les journaux d’activité des systèmes déployés dans un fuseau horaire UTC+N.    

<!-- 2319627 |  ASDK, IS --> 
- La vérification préalable des paramètres de configuration de la sauvegarde (chemin, nom d’utilisateur, mot de passe, clé de chiffrement) ne définit plus de paramètres incorrects pour la configuration de la sauvegarde. (Les paramètres incorrects étaient définis auparavant dans la sauvegarde, et celle-ci échouait lors de son déclenchement.)

<!-- 2215948 |  ASDK, IS --> 
- La liste de sauvegarde s’actualise désormais quand vous supprimez manuellement la sauvegarde du partage externe.

<!-- 2360715 |  ASDK, IS -->  
- Lorsque vous configurez l’intégration du centre de données, vous n’accédez plus au fichier de métadonnées AD FS à partir d’un partage. Pour plus d’informations, consultez la rubrique [Configuration de l’intégration AD FS en fournissant un fichier de métadonnées de fédération](.\.\azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

<!-- 2388980 | ASDK, IS --> 
- Nous avons corrigé un problème qui empêchait les utilisateurs d’attribuer une adresse IP publique existante ayant déjà été attribuée à une interface réseau ou à un équilibreur de charge à une nouvelle interface réseau ou à un nouvel équilibreur de charge.  

<!-- 2551834 - IS, ASDK --> 
- Quand vous sélectionnez *Vue d’ensemble* pour un compte de stockage dans le portail d’administration ou utilisateur, le volet *Éléments principaux* affiche désormais correctement toutes les informations attendues. 

<!-- 2551834 - IS, ASDK --> 
- Quand vous sélectionnez *Balises* pour un compte de stockage dans le portail d’administration ou utilisateur, les informations sont désormais affichées correctement.

<!-- TBD - IS ASDK --> 
- Cette version d’Azure Stack corrige le problème qui empêchait l’application des mises à jour de pilote à partir de packages d’extension OEM.

<!-- 2055809- IS ASDK --> 
- Nous avons résolu un problème qui vous empêchait de supprimer des machines virtuelles du panneau de calcul quand la création de la machine virtuelle échouait.  

<!--  2643962 IS ASDK -->  
- L’alerte de *faible capacité mémoire* n’apparaît plus incorrectement.

<!--  TBD ASDK --> 
- La capacité de la machine virtuelle qui héberge le point de terminaison privilégié (PEP) a été portée à 4 Go. Dans le Kit ASDK, cette machine virtuelle est nommée AzS-ERCS01.

- <!--  TBD – IS, ASDK --> Les tailles de machine virtuelle *De base A* ne sont plus disponibles pour la [création de groupes de machines virtuelles identiques](.\.\azure-stack-compute-add-scalesets.md) dans le portail. Pour créer un groupe de machines virtuelles identiques avec cette taille, utilisez PowerShell ou un modèle. 

### <a name="known-issues"></a>Problèmes connus

#### <a name="portal"></a>Portail  
- <!-- 2967387 – IS, ASDK --> Le compte que vous utilisez pour vous connecter au portail administrateur ou utilisateur Azure Stack s’affiche comme **Utilisateur non identifié**. Cela se produit quand le *prénom* ou le *nom* du compte n’a pas été spécifié. Pour contourner ce problème, modifiez le compte d’utilisateur afin d’ajouter le nom ou le prénom. Vous devez ensuite vous déconnecter et vous reconnecter au portail. 

-  <!--  2873083 - IS ASDK --> Quand vous utilisez le portail pour créer un groupe de machines virtuelles identiques, la liste déroulante *Taille d’instance* ne se charge pas correctement dans Internet Explorer. Pour contourner ce problème, utilisez un autre navigateur quand vous créez un groupe de machines virtuelles identiques à l’aide du portail.  

#### <a name="portal"></a>Portail  
<!-- 2931230 – IS  ASDK --> 
- Les plans ajoutés à un abonnement utilisateur comme plan d’extension ne peuvent pas être supprimés, même quand vous supprimez le plan de l’abonnement utilisateur. Le plan est conservé jusqu’à ce que les abonnements qui référencent le plan d’extension soient aussi supprimés. 

<!--2760466 – IS  ASDK --> 
- Quand vous installez un nouvel environnement Azure Stack qui exécute cette version, l’alerte *Activation requise* peut ne pas s’afficher. L’[activation](.\.\azure-stack-registration.md) est nécessaire avant que vous puissiez utiliser la syndication de Place de marché. 

<!-- TBD - IS ASDK --> 
- Les deux types d’abonnements d’administration qui ont été introduits avec la version 1804 ne doivent pas être utilisés. Les types d’abonnements sont **Abonnement de contrôle** et **Abonnement de consommation**. Ces types d’abonnements sont **Abonnement de contrôle** et **Abonnement de consommation**. Ces types d’abonnements sont visibles dans les nouveaux environnements Azure Stack depuis la version 1804, mais ils ne sont pas encore prêts à être utilisés. Vous devez continuer à utiliser le type d’abonnement **Fournisseur par défaut**.

<!-- 2403291 - IS ASDK --> 
- Il se peut que vous ne puissiez pas utiliser la barre de défilement horizontale au bas du portail d’administration et du portail utilisateur. Si vous ne pouvez pas accéder à la barre de défilement horizontale, utilisez les barres de navigation pour accéder à un panneau précédent dans le portail en sélectionnant le nom du panneau que vous souhaitez afficher dans la liste des barres de navigation en haut à gauche du portail.
  ![Barre de navigation](media/asdk-release-notes/breadcrumb.png)

<!-- TBD -  IS ASDK --> 
- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.

<!-- TBD -  IS ASDK --> 
- Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack. Pour résoudre ce problème, utilisez PowerShell pour vérifier les autorisations.

<!--  TBD | ASDK -->  
- Le fuseau horaire par défaut pour votre déploiement Azure Stack sera désormais défini au format UTC. Vous pouvez sélectionner un fuseau horaire lors de l’installation d’Azure Stack, mais il sera automatiquement rétabli par défaut au format UTC lors de l’installation.

#### <a name="health-and-monitoring"></a>Intégrité et surveillance
<!-- 1264761 - IS ASDK -->  
- Vous risquez de recevoir des alertes pour le composant *Contrôleur d’intégrité* contenant les informations suivantes :  

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

<!-- 2368581 - IS. ASDK --> 
- En tant qu’opérateur d’Azure Stack, si vous recevez une alerte de mémoire insuffisante et que les machines virtuelles de locataire ne parviennent pas à se déployer à cause d’une *erreur de création de machine virtuelle de structure*, il est possible que l’horodatage d’Azure Stack soit à court de mémoire. Utilisez le [Capacity Planner Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) pour mieux comprendre la capacité disponible pour vos charges de travail.

<!-- TBD - IS. ASDK --> 
- Lors de l’exécution de la cmdlet **Test-AzureStack** sur le point de terminaison privilégié (PEP), le test de **performance d’instance de rôle d’infrastructure Azure Stack** génère un message d’AVERTISSEMENT pour la machine virtuelle ERCS. Vous pouvez ignorer le message d’AVERTISSEMENT et continuer à utiliser le Kit de développement Azure Stack en toute sécurité.

#### <a name="compute"></a>Calcul
<!-- 2494144 - IS, ASDK --> 
- Quand vous sélectionnez une taille de machine virtuelle pour un déploiement de machine virtuelle, certaines tailles de machine virtuelle de série F ne sont pas visibles dans le sélecteur de taille lorsque vous créez une machine virtuelle. Les tailles de machine virtuelle suivantes n’apparaissent pas dans le sélecteur : *F8s_v2*, *F16s_v2*, *F32s_v2* et *F64s_v2*.  
  Pour résoudre ce problème, utilisez l’une des méthodes suivantes pour déployer une machine virtuelle. Dans chaque méthode, vous devez spécifier la taille de machine virtuelle que vous voulez utiliser.

- <!-- 3099544 – IS, ASDK --> Quand vous créez une machine virtuelle à l’aide du portail Azure Stack et sélectionnez la taille de machine virtuelle, la colonne EUR/mois s’affiche avec le message **Indisponible**. Cette colonne ne devrait pas s’afficher, car l’affichage de la colonne des prix des machines virtuelles n’est pas pris en charge dans Azure Stack.

- <!-- 2869209 – IS, ASDK --> Quand vous utilisez la [cmdlet **Add-AzsPlatformImage**](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), vous devez spécifier le paramètre **-OsUri** comme URI du compte de stockage où le disque est chargé. Si vous utilisez le chemin local du disque, la cmdlet échoue avec l’erreur suivante : *Échec de l’opération de longue durée avec l’état 'Failed'*. 

- <!--  2966665 – IS, ASDK --> L’attachement de disques de données SSD à des machines virtuelles avec des disques managés de taille Premium (DS, DSv2, Fs, Fs_V2) échoue avec une erreur : *Échec de la mise à jour des disques pour la machine virtuelle 'vmname' Erreur : l’opération demandée ne peut pas être effectuée, car le type de compte de stockage 'Premium_LRS' n’est pas pris en charge pour la taille de machine virtuelle 'Standard_DS/Ds_V2/FS/Fs_v2)'*

   Pour contourner ce problème, utilisez des disques de données *Standard_LRS* à la place de *disques Premium_LRS*. L’utilisation de disques de données *Standard_LRS* n’a pas d’impact sur l’IOP ou le coût de facturation.  

- <!--  2795678 – IS, ASDK --> Quand vous utilisez le portail pour créer des machines virtuelles dans une taille de machine virtuelle Premium (DS, Ds_v2, FS, FSv2), la machine virtuelle est créée dans un compte de stockage standard. La création dans un compte de stockage standard n’a pas d’impact sur les fonctionnalités, l’IOP ou la facturation. 

   Vous pouvez ignorer sans problème cet avertissement : *Vous avez choisi d’utiliser un disque standard sur une taille qui prend en charge les disques Premium. Cela n’est pas recommandé, car cela peut impacter les performances du système d’exploitation. Utilisez le stockage Premium (SSD) à la place.*

- <!-- 2967447 - IS, ASDK --> Quand vous créez un groupe de machines virtuelles identiques, l’option CentOS 7.2 est proposée pour le déploiement. Étant donné que cette image n’est pas disponible sur Azure Stack, sélectionnez un autre système d’exploitation pour votre déploiement ou choisissez un modèle ARM spécifiant une autre image CentOS qui a été téléchargée par l’opérateur avant le déploiement à partir de la Place de marché.

<!-- TBD -  IS ASDK --> 
- Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

<!-- TBD -  IS ASDK --> 
- Quand vous créez des machines virtuelles sur le portail utilisateur Azure Stack, ce dernier affiche un nombre incorrect de disques de données que vous pouvez attacher à une machine virtuelle de série D. Toutes les machines virtuelles de série D prises en charge peuvent prendre en charge autant de disques de données que la configuration Azure.

<!-- TBD -  IS ASDK --> 
- Lorsqu’une image de machine virtuelle ne peut pas être créée, un élément ayant échoué que vous ne pouvez pas supprimer peut être ajouté au panneau Compute des images de machine virtuelle.

  Pour contourner ce problème, créez une image de machine virtuelle avec un disque dur virtuel factice qui peut être créé via Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Ce processus doit résoudre le problème qui empêche la suppression de l’élément ayant échoué. Ensuite, 15 minutes après avoir créé l’image factice, vous pouvez correctement la supprimer.

  Vous pouvez ensuite essayer à nouveau de télécharger l’image de la VM ayant précédemment échoué.

<!-- TBD -  IS ASDK --> 
- Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, les utilisateurs doivent laisser expirer le délai d’attente plutôt que d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

<!-- 1662991 - IS ASDK --> 
- Les diagnostics de machine virtuelle Linux ne sont pas pris en charge dans Azure Stack. Lorsque vous déployez une machine virtuelle Linux en activant les diagnostics de machine virtuelle, le déploiement échoue. Le déploiement échoue également si vous activez les mesures de base de la machine virtuelle Linux dans les paramètres de diagnostic.

<!-- 2724961- IS ASDK --> 
- Quand vous inscrivez le fournisseur de ressources **Microsoft.Insight** dans les paramètres d’abonnement, puis créez une machine virtuelle Windows en ayant activé les diagnostics du système d’exploitation invité, la page de vue d’ensemble de la machine virtuelle n’affiche pas les données de métriques. 

 

#### <a name="networking"></a>Mise en réseau
<!-- 1766332 - IS, ASDK --> 
- Sous **Mise en réseau**, si vous cliquez sur **Créer une passerelle VPN** pour configurer une connexion VPN, l’option **Basé sur des stratégies** s’affiche dans la liste des types de VPN. Ne sélectionnez pas cette option. Seule l’option **Basé sur itinéraires** est prise en charge dans Azure Stack.

<!-- 1902460 -  IS ASDK --> 
- Azure Stack prend en charge une seule *passerelle de réseau local* par adresse IP. Cela est vrai pour tous les abonnements de locataire. Après la création de la première connexion de passerelle de réseau local, les tentatives suivantes de création d’une ressource de passerelle de réseau local avec la même adresse IP sont bloquées.

<!-- 16309153 -  IS ASDK --> 
- Sur un réseau virtuel a été créé avec un paramètre de serveur DNS défini sur *Automatique*, vous ne pouvez pas choisir un serveur DNS personnalisé. Les paramètres mis à jour ne sont pas envoyés (par push) aux machines virtuelles dans ce réseau virtuel.

<!-- 2702741 -  IS ASDK --> 
- La conservation des adresses IP publiques déployées à l’aide de la méthode d’allocation dynamique n’est pas garantie après l’émission d’une commande Arrêter/Libérer.

<!-- 2529607 - IS ASDK --> 
- Durant la *rotation des secrets* Azure Stack, une période de deux à cinq minutes s’écoule pendant laquelle les adresses IP publiques sont inaccessibles.

<!-- 2664148 - IS ASDK --> 
- Dans les cas où le locataire accède à ses machines virtuelles à l’aide d’un tunnel VPN S2S, il peut rencontrer un scénario où les tentatives de connexion échouent si le sous-réseau local a été ajouté à la passerelle de réseau local après la création de la passerelle. 


#### <a name="sql-and-mysql"></a>SQL et MySQL
<!-- TBD - ASDK --> 
- Les serveurs d’hébergement de base de données doivent être dédiés à une utilisation par le fournisseur de ressources et les charges de travail utilisateur. Vous ne pouvez pas utiliser une instance utilisée par un autre consommateur, notamment App Services.

<!-- IS, ASDK --> 
- Les caractères spéciaux, notamment les espaces et les points, ne sont pas pris en charge dans les noms de **Famille** lors de la création d’une référence SKU pour les fournisseurs de ressources SQL et MySQL.

#### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- Les utilisateurs doivent inscrire le fournisseur de ressources de stockage avant de créer leur première fonction Azure dans l’abonnement.

<!-- TBD - IS ASDK --> 
- Pour monter en puissance l’infrastructure (Workers, gestion, rôles frontaux), vous devez utiliser PowerShell comme décrit dans les notes de publication pour Compute.  

<!-- TBD - IS ASDK --> 
- Le déploiement d’App Service n’est possible à l’heure actuelle que sur l’*abonnement du fournisseur par défaut*.

#### <a name="usage"></a>Usage  
<!-- TBD -  IS ASDK --> 
- Les données d’utilisation des adresses IP publiques indiquent une même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.

<!-- #### Identity -->


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

<!-- 2297790 - IS, ASDK --> 
- **Azure Stack inclut désormais un client *Syslog* en** tant que *fonctionnalité préliminaire*. Ce client permet le transfert des journaux d’audit et de sécurité liés à l’infrastructure d’Azure Stack vers un serveur Syslog ou un logiciel SIEM externes à Azure Stack. Actuellement, le client Syslog ne prend en charge que les connexions UDP non authentifiées via le port par défaut 514. La charge utile de chaque message Syslog est formatée en CEF (Common Event Format).

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

- <!-- 2319627 |  ASDK, IS --> La vérification préalable des paramètres de configuration de la sauvegarde (chemin d’accès, nom d’utilisateur, mot de passe, clé de cryptage) ne définit plus les paramètres incorrects de la configuration de la sauvegarde. (Les paramètres incorrects étaient définis auparavant dans la sauvegarde, et celle-ci échouait lors de son déclenchement.)

- <!-- 2215948 |  ASDK, IS --> La liste de sauvegarde s’actualise désormais lorsque l’on supprime manuellement la sauvegarde du partage externe.

- <!-- 2360715 |  ASDK, IS -->  Lorsque vous configurez l’intégration du centre de données, vous n’accédez plus au fichier de métadonnées AD FS à partir d’un partage. Pour plus d’informations, consultez la rubrique [Configuration de l’intégration AD FS en fournissant un fichier de métadonnées de fédération](.\.\azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

- <!-- 2388980 | ASDK, IS --> Nous avons corrigé un problème qui empêchait les utilisateurs d’attribuer une adresse IP publique existante ayant déjà été attribuée à une interface réseau ou à un équilibreur de charge à une nouvelle interface réseau ou à un nouvel équilibreur de charge.  

- <!-- 2551834 - IS, ASDK --> Quand vous sélectionnez *Vue d’ensemble* pour un compte de stockage dans le portail d’administration ou utilisateur, le volet *Éléments principaux* affiche désormais correctement toutes les informations attendues. 

- <!-- 2551834 - IS, ASDK --> Quand vous sélectionnez *Balises* pour un compte de stockage dans le portail d’administration ou utilisateur, les informations sont désormais affichées correctement.

- <!-- TBD - IS ASDK --> Cette version d’Azure Stack corrige le problème qui empêchait l’application des mises à jour de pilote à partir de packages d’extension OEM.

-   <!-- 2055809- IS ASDK --> Nous avons résolu un problème qui vous empêchait de supprimer des machines virtuelles du panneau de calcul quand la création de la machine virtuelle échouait.  

- <!--  2643962 IS ASDK -->  L’alerte de *faible capacité mémoire* n’apparaît plus incorrectement.

<!--  TBD ASDK --> 
- La capacité de la machine virtuelle qui héberge le point de terminaison privilégié (PEP) a été portée à 4 Go. Dans le Kit ASDK, cette machine virtuelle est nommée AzS-ERCS01.

- **Divers correctifs** pour les performances, la stabilité, la sécurité et le système d’exploitation utilisé par Azure Stack


<!-- ### Changes  -->
<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Problèmes connus

#### <a name="portal"></a>Portail
<!-- 2931230 – IS  ASDK --> 
- Les plans ajoutés à un abonnement utilisateur comme plan d’extension ne peuvent pas être supprimés, même quand vous supprimez le plan de l’abonnement utilisateur. Le plan est conservé jusqu’à ce que les abonnements qui référencent le plan d’extension soient aussi supprimés. 

<!-- 2551834 - IS, ASDK --> 
- Lorsque vous sélectionnez **Vue d’ensemble** pour un compte de stockage sur le portail d’administration ou utilisateur, les informations du volet *Éléments principaux* ne s’affichent pas.  Le volet Éléments principaux affiche des informations sur le compte comme son *groupe de ressources*, son *emplacement* et son *ID d’abonnement*.  D’autres options de la vue d’ensemble sont accessibles, par exemple *Services* et *Surveillance*, ainsi que des options permettant d’*Ouvrir dans Explorer* ou de *Supprimer le compte de stockage*.  

  Pour afficher les informations non disponibles, utilisez la cmdlet PowerShell [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0).

<!-- 2551834 - IS, ASDK --> 
- Lorsque vous sélectionnez **Balises** pour un compte de stockage sur le portail d’administration ou utilisateur, les informations ne se chargent pas.  

  Pour afficher les informations non disponibles, utilisez la cmdlet PowerShell [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0).

<!-- TBD - IS ASDK --> 
- N’utilisez pas les nouveaux types d’abonnement d’administration *Abonnement de contrôle* et *Abonnement de consommation*. Ces nouveaux types d’abonnement ont été introduits avec la version 1804 mais ne sont pas encore prêts à être utilisés. Vous devez continuer à utiliser le type d’abonnement *Fournisseur par défaut*.
  
<!-- TBD - IS ASDK --> 
- Vous ne pouvez pas appliquer les mises à jour de pilote à l’aide d’un package d’extension OEM avec cette version d’Azure Stack.  Il n’existe aucune solution de contournement pour ce problème.
 
<!-- TBD - IS ASDK --> 
- La possibilité [d’ouvrir une nouvelle demande de support dans la liste déroulante](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) à partir du portail administrateur n’est pas disponible. À la place, utilisez le lien suivant :     
    - Pour le Kit de développement Azure Stack, utilisez https://aka.ms/azurestackforum.    

<!-- 2403291 - IS ASDK --> 
- Il se peut que vous ne puissiez pas utiliser la barre de défilement horizontale au bas du portail d’administration et du portail utilisateur. Si vous ne pouvez pas accéder à la barre de défilement horizontale, utilisez les barres de navigation pour accéder à un panneau précédent dans le portail en sélectionnant le nom du panneau que vous souhaitez afficher dans la liste des barres de navigation en haut à gauche du portail.
  ![Barre de navigation](media/asdk-release-notes/breadcrumb.png)

<!-- TBD -  IS ASDK --> 
- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.

<!-- TBD -  IS ASDK --> 
- Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack. Pour résoudre ce problème, utilisez PowerShell pour vérifier les autorisations.

- <!--  TBD | ASDK -->  Le fuseau horaire par défaut pour votre déploiement Azure Stack sera désormais défini au format UTC. Vous pouvez sélectionner un fuseau horaire lors de l’installation d’Azure Stack, mais il sera automatiquement rétabli par défaut au format UTC lors de l’installation.

#### <a name="health-and-monitoring"></a>Intégrité et surveillance
<!-- 1264761 - IS ASDK -->  
- Vous risquez de recevoir des alertes pour le composant *Contrôleur d’intégrité* contenant les informations suivantes :  

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

<!-- 2368581 - IS. ASDK --> 
- En tant qu’opérateur d’Azure Stack, si vous recevez une alerte de mémoire insuffisante et que les machines virtuelles de locataire ne parviennent pas à se déployer à cause d’une *erreur de création de machine virtuelle de structure*, il est possible que l’horodatage d’Azure Stack soit à court de mémoire. Utilisez le [Capacity Planner Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) pour mieux comprendre la capacité disponible pour vos charges de travail.

<!-- TBD - IS. ASDK --> 
- L’exécution de la cmdlet Test-AzureStack sur le point de terminaison privilégié (PEP) génère un message d’avertissement pour la machine virtuelle ERCS. Vous pouvez continuer à utiliser l’ASDK. 


#### <a name="compute"></a>Calcul
<!-- TBD - IS, ASDK --> 
- Quand vous sélectionnez une taille de machine virtuelle pour un déploiement de machine virtuelle, certaines tailles de machine virtuelle de série F ne sont pas visibles dans le sélecteur de taille lorsque vous créez une machine virtuelle. Les tailles de machine virtuelle suivantes n’apparaissent pas dans le sélecteur : *F8s_v2*, *F16s_v2*, *F32s_v2* et *F64s_v2*.  
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


<!-- TBD -  IS ASDK --> 
- Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

<!-- TBD -  IS ASDK --> 
- Quand vous créez des machines virtuelles sur le portail utilisateur Azure Stack, ce dernier affiche un nombre incorrect de disques de données que vous pouvez attacher à une machine virtuelle de série D. Toutes les machines virtuelles de série D prises en charge peuvent prendre en charge autant de disques de données que la configuration Azure.

<!-- TBD -  IS ASDK --> 
- Lorsqu’une image de machine virtuelle ne peut pas être créée, un élément ayant échoué que vous ne pouvez pas supprimer peut être ajouté au panneau Compute des images de machine virtuelle.

  Pour contourner ce problème, créez une image de machine virtuelle avec un disque dur virtuel factice qui peut être créé via Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Ce processus doit résoudre le problème qui empêche la suppression de l’élément ayant échoué. Ensuite, 15 minutes après avoir créé l’image factice, vous pouvez correctement la supprimer.

  Vous pouvez ensuite essayer à nouveau de télécharger l’image de la VM ayant précédemment échoué.

<!-- TBD -  IS ASDK --> 
- Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, les utilisateurs doivent laisser expirer le délai d’attente plutôt que d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

<!-- 1662991 - IS ASDK --> 
- Les diagnostics de machine virtuelle Linux ne sont pas pris en charge dans Azure Stack. Lorsque vous déployez une machine virtuelle Linux en activant les diagnostics de machine virtuelle, le déploiement échoue. Le déploiement échoue également si vous activez les mesures de base de la machine virtuelle Linux dans les paramètres de diagnostic.

#### <a name="networking"></a>Mise en réseau
<!-- TBD - IS ASDK --> 
- Vous ne pouvez pas créer d’itinéraires définis par l’utilisateur, que ce soit sur le portail d’administration ou le portail utilisateur. Pour résoudre ce problème, utilisez [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

<!-- 1766332 - IS, ASDK --> 
- Sous **Mise en réseau**, si vous cliquez sur **Créer une passerelle VPN** pour configurer une connexion VPN, l’option **Basé sur des stratégies** s’affiche dans la liste des types de VPN. Ne sélectionnez pas cette option. Seule l’option **Basé sur itinéraires** est prise en charge dans Azure Stack.

<!-- 2388980 -  IS ASDK --> 
- Une fois une machine virtuelle créée et associée à une adresse IP publique, vous ne pouvez pas dissocier cette machine virtuelle de cette adresse IP. La dissociation semble fonctionner, mais l’adresse IP publique qui a été assignée précédemment reste associée à la machine virtuelle d’origine.

  Actuellement, vous devez utiliser uniquement les nouvelles adresses IP publiques pour les nouvelles machines virtuelles que vous créez.

  Ce comportement se produit même si vous réaffectez l’adresse IP à une nouvelle machine virtuelle (ce qui est communément appelé un *échange d’adresses IP virtuelles*). Toutes les futures tentatives de connexion au moyen de cette adresse IP aboutissent à une connexion à la machine virtuelle originelle et non à la nouvelle.

<!-- 2292271 - IS ASDK --> 
- Si vous définissez une limite de quota pour une ressource réseau qui fait partie d’une offre et d’un plan associés à un abonnement de locataire, la nouvelle limite n’est pas appliquée à cet abonnement. Toutefois, la nouvelle limite s’applique aux nouveaux abonnements créés après l’augmentation du quota.

  Pour contourner ce problème, utilisez un plan d’extension pour augmenter un quota réseau quand le plan est déjà associé à un abonnement. Pour plus d’informations, découvrez comment [rendre un plan d’extension disponible](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

<!-- 2304134 IS ASDK --> 
- Vous ne pouvez pas supprimer un abonnement auquel sont associées des ressources de la zone DNS ou de la table de routage. Pour supprimer l’abonnement, vous devez d’abord supprimer les ressources de la zone DNS ou de la table de routage de l’abonnement de locataire.

<!-- 1902460 -  IS ASDK --> 
- Azure Stack prend en charge une seule *passerelle de réseau local* par adresse IP. Cela est vrai pour tous les abonnements de locataire. Après la création de la première connexion de passerelle de réseau local, les tentatives suivantes de création d’une ressource de passerelle de réseau local avec la même adresse IP sont bloquées.

<!-- 16309153 -  IS ASDK --> 
- Sur un réseau virtuel a été créé avec un paramètre de serveur DNS défini sur *Automatique*, vous ne pouvez pas choisir un serveur DNS personnalisé. Les paramètres mis à jour ne sont pas envoyés (par push) aux machines virtuelles dans ce réseau virtuel.

<!-- TBD -  IS ASDK --> 
- Azure Stack ne prend pas en charge l’ajout d’interfaces réseau supplémentaires sur une instance de machine virtuelle une fois que la machine virtuelle est déployée. Si la machine virtuelle nécessite plusieurs interfaces réseau, elles doivent être définies au moment du déploiement.


#### <a name="sql-and-mysql"></a>SQL et MySQL
<!-- TBD - ASDK --> 
- Les serveurs d’hébergement de base de données doivent être dédiés à une utilisation par le fournisseur de ressources et les charges de travail utilisateur. Vous ne pouvez pas utiliser une instance utilisée par un autre consommateur, notamment App Services.

<!-- IS, ASDK --> 
- Les caractères spéciaux, notamment les espaces et les points, ne sont pas pris en charge dans les noms de **Famille** lors de la création d’une référence SKU pour les fournisseurs de ressources SQL et MySQL.

#### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- Les utilisateurs doivent inscrire le fournisseur de ressources de stockage avant de créer leur première fonction Azure dans l’abonnement.

<!-- TBD - IS ASDK --> 
- Pour monter en puissance l’infrastructure (Workers, gestion, rôles frontaux), vous devez utiliser PowerShell comme décrit dans les notes de publication pour Compute.  

<!-- TBD - IS ASDK --> 
- Le déploiement d’App Service n’est possible à l’heure actuelle que sur l’*abonnement du fournisseur par défaut*. <!-- In a future update, App Service will deploy into the new *Metering subscription* that was introduced in Azure Stack 1804. When Metering is supported for use, all existing deployments will be migrated to this new subscription type. -->

#### <a name="usage"></a>Usage  
<!-- TBD -  IS ASDK --> 
- Les données d’utilisation des adresses IP publiques indiquent une même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.

<!-- #### Identity -->
