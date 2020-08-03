---
title: Nouveautés de Windows Virtual Desktop - Azure
description: Nouvelles fonctionnalités et mises à jour de produit pour Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 06/15/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.openlocfilehash: 9be1053600ab89a7879a04a7c08a44ddf3bc862a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291240"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Nouveautés de Windows Virtual Desktop

Windows Virtual Desktop est mis à jour régulièrement. Vous trouverez dans cet article des informations sur :

- Les dernières mises à jour.
- Nouvelles fonctionnalités
- Les améliorations apportées aux fonctionnalités existantes.
- Résolution des bogues

Cet article est mis à jour tous les mois. Pensez à consulter cette page régulièrement pour être tenu informé des nouvelles mises à jour.

## <a name="june-2020"></a>Juin 2020

Le mois dernier, nous avons introduit l’intégration de Windows Virtual Desktop avec Azure Resource Manager en préversion. Cette mise à jour comprend de nombreuses nouvelles fonctionnalités passionnantes que nous souhaiterions vous présenter. Voici les nouveautés de cette version de Windows Virtual Desktop.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager-preview"></a>Windows Virtual Desktop est maintenant intégré à Azure Resource Manager (préversion)

Windows Virtual Desktop est maintenant intégré à Azure Resource Manager. Dans la dernière mise à jour, tous les objets Windows Virtual Desktop sont désormais des ressources Azure Resource Manager. Cette mise à jour est également intégrée aux contrôles d’accès en fonction du rôle (RBAC) Azure. Pour en savoir plus, consultez [Qu’est-ce qu’Azure Resource Manager ?](../azure-resource-manager/management/overview.md).

Les conséquences de ce changement sont les suivantes :

- Windows Virtual Desktop est désormais intégré au portail Azure. Cela signifie que vous pouvez gérer tout directement dans le portail, sans avoir besoin de PowerShell, d’applications web ou d’outils tiers. Pour bien démarrer, consultez notre tutoriel intitulé [Créer un pool d’hôtes avec le portail Azure](create-host-pools-azure-marketplace.md).

- Avant cette mise à jour, vous pouviez uniquement publier des RemoteApps et des Desktops sur des utilisateurs individuels. Avec Azure Resource Manager, vous pouvez désormais publier des ressources sur des groupes Azure Active Directory.

- La version précédente de Windows Virtual Desktop avait quatre rôles d’administrateur intégrés que vous pouviez attribuer à un locataire ou un pool d’hôtes. Ces rôles sont désormais dans le [contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md) Azure. Vous pouvez appliquer ces rôles à chaque objet Azure Resource Manager Windows Virtual Desktop, ce qui vous permet d’avoir un modèle de délégation complet et riche.

- Dans cette mise à jour, vous n’avez plus besoin d’exécuter la Place de marché Azure ou le modèle GitHub de manière répétée pour étendre un pool d’hôtes. Il vous suffit d’accéder à votre pool d’hôtes dans le portail Azure et de sélectionner **+ Ajouter** pour déployer des hôtes de session supplémentaires.

- Le déploiement de pool d’hôtes est maintenant entièrement intégré à [Azure Shared Image Gallery](../virtual-machines/windows/shared-image-galleries.md). Shared Image Gallery est un service Azure distinct qui stocke des définitions d’images de machine virtuelle, notamment la gestion de versions d’images. Vous pouvez également utiliser la réplication globale pour copier et envoyer vos images vers d’autres régions Azure en vue d’un déploiement local.

- Les fonctions de supervision qui étaient auparavant effectuées à l’aide de PowerShell ou de l’application web Service de diagnostics ont été déplacées vers Log Analytics dans le portail Azure. Vous disposez désormais aussi de deux options pour visualiser vos rapports. Vous pouvez exécuter des requêtes Kusto et utiliser des classeurs pour créer des rapports visuels.

- Vous n’avez plus besoin de finaliser le consentement Azure Active Directory (Azure AD) pour utiliser Windows Virtual Desktop. Dans cette mise à jour, le locataire Azure AD sur votre abonnement Azure authentifie vos utilisateurs et fournit des contrôles RBAC pour vos administrateurs.


### <a name="powershell-support"></a>Prise en charge de PowerShell

Nous avons ajouté de nouvelles applets de commande AzWvd au module Az Azure PowerShell avec cette mise à jour. Ce nouveau module est pris en charge dans PowerShell Core, qui s’exécute sur .NET Core.

Pour installer le module, suivez les instructions fournies dans [Configurer le module PowerShell pour Windows Virtual Desktop](powershell-module.md).

Vous pouvez également consulter la liste des commandes disponibles dans la [référence PowerShell AzWvd](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization).

Pour plus d’informations sur les nouvelles fonctionnalités, consultez [notre billet de blog](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245). 

### <a name="additional-gateways"></a>Passerelles supplémentaires

Nous avons ajouté un nouveau cluster de passerelle en Afrique du Sud pour réduire la latence de connexion.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams sur Windows Virtual Desktop (Préversion)

Nous avons apporté des améliorations à Microsoft Teams pour Windows Virtual Desktop. Plus important encore, Windows Virtual Desktop prend désormais en charge la redirection audio et vidéo pour les appels. La redirection améliore la latence en créant des chemins directs entre les utilisateurs quand ils effectuent des appels audio ou vidéo. Une distance moins élevée signifie moins de tronçons, ce qui améliore la fluidité sonore et visuelle des appels.

Pour plus d’informations, consultez [notre billet de blog](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur ce qui est prévu à l’avenir en consultant la [feuille de route Microsoft 365 Windows Virtual Desktop](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).

Consultez ces articles pour en savoir plus sur les mises à jour de nos clients pour Windows Virtual Desktop et Windows Virtual Desktop :

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)
