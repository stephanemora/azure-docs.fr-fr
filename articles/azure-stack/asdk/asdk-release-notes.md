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
ms.date: 07/30/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 2f732dfdfe9bf4aff2753114c3041f8f646421c2
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39344730"
---
# <a name="azure-stack-development-kit-release-notes"></a>Notes de publication du Kit de développement Azure Stack
Ces notes de publication fournissent des informations sur les améliorations, les correctifs et les problèmes connus relatifs au Kit de développement Azure Stack. Si vous n’êtes pas sûr de la version que vous exécutez, consultez le [portail pour vérifier](.\.\azure-stack-updates.md#determine-the-current-version).

> Restez informé des nouveautés concernant l’ASDK en vous abonnant au [![flux](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11805142"></a>Build 1.1805.1.42

<!-- ### New features 
This build includes the following improvements and fixes for Azure Stack.  
-->


### <a name="fixed-issues"></a>Problèmes résolus

- **Divers correctifs** pour les performances, la stabilité, la sécurité et le système d’exploitation utilisé par Azure Stack


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Problèmes connus
 
#### <a name="portal"></a>Portail
- <!-- TBD - IS ASDK --> Vous ne pouvez pas appliquer les mises à jour de pilote à l’aide d’un package d’extension OEM avec cette version d’Azure Stack.  Il n’existe aucune solution de contournement pour ce problème.
 
- <!-- TBD - IS ASDK --> N’utilisez pas les nouveaux types d’abonnement d’administration *Abonnement de contrôle* et *Abonnement de consommation*. Ces nouveaux types d’abonnement ont été introduits avec la version 1804 mais ne sont pas encore prêts à être utilisés. Vous devez continuer à utiliser le type d’abonnement *Fournisseur par défaut*.  

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

  Ces deux alertes peuvent être ignorées en toute sécurité. Elles se ferment automatiquement dans le temps.  

#### <a name="compute"></a>Calcul
- <!-- TBD -  IS ASDK --> Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Lorsque vous créez des machines virtuelles sur le portail utilisateur Azure Stack, ce dernier affiche un nombre incorrect de disques de données que vous pouvez attacher à une machine virtuelle de série D. Toutes les machines virtuelles de série D prises en charge peuvent prendre en charge autant de disques de données que la configuration Azure.

- <!-- TBD -  IS ASDK --> Lorsqu’une image de machine virtuelle ne peut pas être créée, un élément ayant échoué que vous ne pouvez pas supprimer peut être ajouté au panneau Compute des images de machine virtuelle.

  Pour contourner ce problème, créez une image de machine virtuelle avec un disque dur virtuel factice qui peut être créé via Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Ce processus doit résoudre le problème qui empêche la suppression de l’élément ayant échoué. Ensuite, 15 minutes après avoir créé l’image factice, vous pouvez correctement la supprimer.

  Vous pouvez alors essayer de retélécharger l’image de machine virtuelle ayant précédemment échoué.

- <!-- TBD -  IS ASDK --> Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, les utilisateurs doivent laisser expirer le délai d’attente plutôt que d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

- <!-- 1662991 - IS ASDK --> Les diagnostics de machine virtuelle Linux ne sont pas pris en charge dans Azure Stack. Lorsque vous déployez une machine virtuelle Linux en activant les diagnostics de machine virtuelle, le déploiement échoue. Le déploiement échoue également si vous activez les mesures de base de la machine virtuelle Linux dans les paramètres de diagnostic. 

#### <a name="networking"></a>Mise en réseau
- <!-- 1766332 - IS, ASDK --> Sous **Mise en réseau**, si vous cliquez sur **Créer une passerelle VPN** pour configurer une connexion VPN, l’option **Basé sur des stratégies** s’affiche dans la liste des types de VPN. Ne sélectionnez pas cette option. Seule l’option **Basé sur itinéraires** est prise en charge dans Azure Stack.

- <!-- 2388980 -  IS ASDK --> Lorsqu’une machine virtuelle est créée et associée à une adresse IP publique, vous ne pouvez pas dissocier cette machine virtuelle de cette adresse IP. La dissociation semble fonctionner, mais l’adresse IP publique qui a été assignée précédemment reste associée à la machine virtuelle d’origine.

  Actuellement, vous devez utiliser uniquement les nouvelles adresses IP publiques pour les nouvelles machines virtuelles que vous créez.

  Ce comportement se produit même si vous réaffectez l’adresse IP à une nouvelle machine virtuelle (ce qui est communément appelé un *échange d’adresses IP virtuelles*). Toutes les futures tentatives de connexion au moyen de cette adresse IP aboutissent à une connexion à la machine virtuelle associée à l’origine et non à la nouvelle.

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

- <!-- TBD - IS ASDK --> App Service peut uniquement être déployé sur l’*abonnement Fournisseur par défaut* pour l’instant. Dans une prochaine mise à jour, App Service pourra être déployé dans le nouvel *abonnement de contrôle* introduit dans Azure Stack 1804. Une fois le contrôle complètement pris en charge, tous les déploiements existants seront migrés vers ce type d’abonnement.

#### <a name="usage"></a>Usage  
- <!-- TBD -  IS ASDK --> Les données d’utilisation des adresses IP publiques indiquent une même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.

<!-- #### Identity -->



## <a name="build-201805131"></a>Build 20180513.1

### <a name="new-features"></a>Nouvelles fonctionnalités 
Cette build inclut les améliorations et les correctifs suivants pour Azure Stack.  

- <!-- 1759172 - IS, ASDK --> **Nouveaux abonnements d’administration**. Avec 1804, deux nouveaux types d’abonnement sont disponibles dans le portail. Ces nouveaux types d’abonnement viennent s’ajouter à l’abonnement Fournisseur par défaut et sont visibles dans les nouvelles installations d’Azure Stack depuis la version 1804. *N’utilisez pas ces nouveaux types d’abonnement avec cette version d’Azure Stack*. Nous annoncerons quand ces types d’abonnement pourront être utilisés dans une mise à jour ultérieure. 

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
- <!-- TBD - IS ASDK --> Vous ne pouvez pas appliquer les mises à jour de pilote à l’aide d’un package d’extension OEM avec cette version d’Azure Stack.  Il n’existe aucune solution de contournement pour ce problème.
 
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

  Vous pouvez alors essayer de retélécharger l’image de machine virtuelle ayant précédemment échoué.

- <!-- TBD -  IS ASDK --> Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, les utilisateurs doivent laisser expirer le délai d’attente plutôt que d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

- <!-- 1662991 - IS ASDK --> Les diagnostics de machine virtuelle Linux ne sont pas pris en charge dans Azure Stack. Lorsque vous déployez une machine virtuelle Linux en activant les diagnostics de machine virtuelle, le déploiement échoue. Le déploiement échoue également si vous activez les mesures de base de la machine virtuelle Linux dans les paramètres de diagnostic. 

#### <a name="networking"></a>Mise en réseau
- <!-- 1766332 - IS, ASDK --> Sous **Mise en réseau**, si vous cliquez sur **Créer une passerelle VPN** pour configurer une connexion VPN, l’option **Basé sur des stratégies** s’affiche dans la liste des types de VPN. Ne sélectionnez pas cette option. Seule l’option **Basé sur itinéraires** est prise en charge dans Azure Stack.

- <!-- 2388980 -  IS ASDK --> Lorsqu’une machine virtuelle est créée et associée à une adresse IP publique, vous ne pouvez pas dissocier cette machine virtuelle de cette adresse IP. La dissociation semble fonctionner, mais l’adresse IP publique qui a été assignée précédemment reste associée à la machine virtuelle d’origine.

  Actuellement, vous devez utiliser uniquement les nouvelles adresses IP publiques pour les nouvelles machines virtuelles que vous créez.

  Ce comportement se produit même si vous réaffectez l’adresse IP à une nouvelle machine virtuelle (ce qui est communément appelé un *échange d’adresses IP virtuelles*). Toutes les futures tentatives de connexion au moyen de cette adresse IP aboutissent à une connexion à la machine virtuelle associée à l’origine et non à la nouvelle.

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



## <a name="build-201803021"></a>Build 20180302.1

### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs
Les nouveaux correctifs et fonctionnalités publiés pour les systèmes intégrés Azure Stack version 1803 s’appliquent au Kit de développement Azure Stack. Pour plus d’informations, consultez les sections relatives aux [nouvelles fonctionnalités](.\.\azure-stack-update-1803.md#new-features) et aux [problèmes résolus](.\.\azure-stack-update-1803.md#fixed-issues) dans les notes de publication des mises à jour d’Azure Stack 1803.  
> [!IMPORTANT]    
> Certains des éléments répertoriés dans ces sections concernent uniquement les systèmes intégrés Azure Stack.

### <a name="changes"></a>Changements
- La façon de changer l’état d’une offre nouvellement créée de *privé* à *public* ou à *désactivé* a été modifiée. Pour plus d’informations, consultez [Créer une offre](.\.\azure-stack-create-offer.md). 


### <a name="known-issues"></a>Problèmes connus
 
#### <a name="portal"></a>Portail
- La possibilité [d’ouvrir une nouvelle demande de support dans la liste déroulante](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) à partir du portail administrateur n’est pas disponible. À la place, utilisez le lien suivant :     
    - Pour le Kit de développement Azure Stack, utilisez https://aka.ms/azurestackforum.    

- <!-- 2050709 --> Dans le portail d’administration, il n’est pas possible de modifier les métriques de stockage pour le service Blob, le service de Table ou le service de File d’attente. Lorsque vous accédez au stockage et que vous sélectionnez la vignette du service Blob, de Table ou de File d’attente, un nouveau panneau qui affiche un graphique des métriques pour ce service s’ouvre. Si vous sélectionnez ensuite Modifier en haut de la vignette du graphique des métriques, le panneau Modifier le graphique s’ouvre, mais n’affiche pas les options pour modifier les métriques.  

- Un message d’avertissement **Activation requise** s’affiche pour vous inviter à inscrire votre Kit de développement Azure Stack. Il s’agit du comportement attendu.

- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.

- Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack. Pour résoudre ce problème, utilisez PowerShell pour vérifier les autorisations.

- Dans le tableau de bord du portail d’administration, la vignette Mise à jour ne parvient pas à afficher les informations sur les mises à jour. Pour résoudre ce problème, cliquez sur la vignette pour l’actualiser.

-   Dans le portail d’administration, vous pouvez voir une alerte critique pour le composant Microsoft.Update.Admin. Le nom, la description et la correction de l’alerte s’affichent tous comme suit :  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (ERREUR - Absence du modèle de FaultType ResourceProviderTimeout.)

    Cette alerte peut être ignorée en toute sécurité. 

- Dans le portail d’administration et le portail utilisateur, le panneau Vue d’ensemble ne parvient pas à charger lorsque vous sélectionnez le panneau Vue d’ensemble des comptes de stockage qui ont été créés avec une ancienne version de l’API (exemple : 2015-06-15). 

  Pour résoudre ce problème, utilisez PowerShell pour exécuter le script **ResourceSynchronization.ps1** pour restaurer l’accès aux détails du compte de stockage. [Le script est disponible à partir de GitHub]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts) et doit s’exécuter avec des informations d’identification d’administrateur de service sur le kit de développement hôte si vous utilisez l’ASDK.  

- Le chargement du panneau **Service Health** a échoué. Si vous ouvrez le panneau Service Health dans le portail d’administration ou utilisateur, Azure Stack affiche une erreur et ne charge pas les informations. Ce comportement est normal. Même s’il est possible de sélectionner et d’ouvrir Service Health, cette fonctionnalité n’est pas encore disponible ; elle sera implémentée dans une prochaine version d’Azure Stack.


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

- Dans le portail d’administration Azure Stack, vous pouvez voir une alerte critique portant le nom **Certificat externe sur le point d’expirer**.  Cette alerte peut être ignorée en toute sécurité et n’affecte pas les opérations du Kit de développement Azure Stack. 


#### <a name="marketplace"></a>Marketplace
- Les utilisateurs ont la possibilité de parcourir entièrement la Place de marché, et peuvent voir des éléments administratifs, tels que des plans et des offres, qui ne sont pas fonctionnels pour eux.
 
#### <a name="compute"></a>Calcul
- Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

- Lorsque vous créez des machines virtuelles sur le portail utilisateur Azure Stack, le portail affiche un nombre incorrect de disques de données que vous pouvez attacher à une machine virtuelle de la série DS. Les machines virtuelles de série DS peuvent prendre en charge autant de disques de données que la configuration Azure.

- Lorsqu’une image de machine virtuelle ne peut pas être créée, un élément ayant échoué que vous ne pouvez pas supprimer peut être ajouté au panneau Compute des images de machine virtuelle.

  Pour contourner ce problème, créez une image de machine virtuelle avec un disque dur virtuel factice qui peut être créé via Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Ce processus doit résoudre le problème qui empêche la suppression de l’élément ayant échoué. Ensuite, 15 minutes après avoir créé l’image factice, vous pouvez correctement la supprimer.

  Vous pouvez alors essayer de retélécharger l’image de machine virtuelle ayant précédemment échoué.

-  Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, les utilisateurs doivent laisser expirer le délai d’attente plutôt que d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

- <!-- 1662991 --> Les diagnostics de machine virtuelle Linux ne sont pas pris en charge dans Azure Stack. Lorsque vous déployez une machine virtuelle Linux en activant les diagnostics de machine virtuelle, le déploiement échoue. Le déploiement échoue également si vous activez les mesures de base de la machine virtuelle Linux dans les paramètres de diagnostic. 


#### <a name="networking"></a>Mise en réseau
- Sous **Mise en réseau**, si vous cliquez sur **Connexion** pour configurer une connexion VPN, **Connexion entre deux réseaux virtuels** s’affiche comme type de connexion disponible. Ne sélectionnez pas cette option. Actuellement, seule l’option **Site à site (IPsec)** est prise en charge.

- Une fois une machine virtuelle créée et associée à une adresse IP publique, vous ne pouvez pas dissocier cette machine virtuelle de cette adresse IP. La dissociation semble fonctionner, mais l’adresse IP publique qui a été assignée précédemment reste associée à la machine virtuelle d’origine.

  Actuellement, vous devez utiliser uniquement les nouvelles adresses IP publiques pour les nouvelles machines virtuelles que vous créez.

  Ce comportement se produit même si vous réaffectez l’adresse IP à une nouvelle machine virtuelle (ce qui est communément appelé un *échange d’adresses IP virtuelles*). Toutes les futures tentatives de connexion au moyen de cette adresse IP aboutissent à une connexion à la machine virtuelle associée à l’origine et non à la nouvelle.



- Azure Stack prend en charge une seule *passerelle de réseau local* par adresse IP. Cela est vrai pour tous les abonnements de locataire. Après la création de la première connexion de passerelle de réseau local, les tentatives suivantes de création d’une ressource de passerelle de réseau local avec la même adresse IP sont bloquées.

- Sur un réseau virtuel a été créé avec un paramètre de serveur DNS défini sur *Automatique*, vous ne pouvez pas choisir un serveur DNS personnalisé. Les paramètres mis à jour ne sont pas envoyés (par push) aux machines virtuelles dans ce réseau virtuel.
 
- Azure Stack ne prend pas en charge l’ajout d’interfaces réseau supplémentaires sur une instance de machine virtuelle une fois que la machine virtuelle est déployée. Si la machine virtuelle nécessite plusieurs interfaces réseau, elles doivent être définies au moment du déploiement.



#### <a name="sql-and-mysql"></a>SQL et MySQL 
- Il faut parfois attendre une heure pour que les utilisateurs puissent créer des bases de données dans une nouvelle référence SKU SQL ou MySQL.

- Les serveurs d’hébergement de base de données doivent être dédiés à une utilisation par le fournisseur de ressources et les charges de travail utilisateur. Vous ne pouvez pas utiliser une instance utilisée par un autre consommateur, notamment App Services.

- <!-- IS, ASDK --> Les caractères spéciaux, y compris les espaces et les points, ne sont pas pris en charge dans les noms **Famille** et **Niveau** lors de la création d’une référence SKU pour les fournisseurs de ressources SQL et MySQL.

#### <a name="app-service"></a>App Service
- Les utilisateurs doivent inscrire le fournisseur de ressources de stockage avant de créer leur première fonction Azure dans l’abonnement.

- Pour monter en puissance l’infrastructure (Workers, gestion, rôles frontaux), vous devez utiliser PowerShell comme décrit dans les notes de publication pour Compute.
 
#### <a name="usage"></a>Usage  
- Les données d’utilisation des adresses IP publiques indiquent une même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Téléchargement des outils Azure Stack à partir de GitHub
- Quand vous utilisez l’applet de commande PowerShell *invoke-webrequest* pour télécharger les outils Azure Stack à partir de Github, une erreur s’affiche :     
    -  *invoke-webrequest : La demande a été abandonnée : Impossible de créer un canal sécurisé SSL/TLS.*     

  Cette erreur se produit en raison d’une désapprobation récente de la prise en charge GitHub des normes de chiffrement Tlsv1 et Tlsv1.1 (valeur par défaut pour PowerShell). Pour plus d’informations, consultez [Avis de suppression des normes de chiffrement faible](https://githubengineering.com/crypto-removal-notice/).

  Pour résoudre ce problème, ajoutez `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` au début du script pour forcer la console PowerShell à utiliser TLSv1.2 lors du téléchargement à partir de dépôts GitHub.
