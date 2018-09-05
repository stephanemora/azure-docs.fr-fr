---
title: Découvrir les ressources Azure à gérer dans PIM | Microsoft Docs
description: Apprenez à découvrir les ressources Azure à gérer dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: b5d48b3f854afaa79574e0ec13cff91f60396ac6
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190656"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>Découvrir les ressources Azure à gérer dans PIM

Découvrez comment découvrir et gérer les ressources Azure quand vous utilisez PIM (Privileged Identity Management) dans Azure AD (Azure Active Directory). Ces informations peuvent être utiles aux organisations qui utilisent déjà PIM pour protéger les ressources de l’administrateur, ainsi qu’aux propriétaires d’abonnements qui souhaitent sécuriser les ressources de production.

Quand vous configurez PIM pour les ressources Azure pour la première fois, vous devez découvrir et sélectionner les ressources à protéger avec PIM. Il n’existe aucune limite quant à la quantité de ressources que vous pouvez gérer avec PIM. Toutefois, nous vous recommandons de commencer avec vos ressources les plus critiques (production).

> [!NOTE]
> Seules les ressources de groupe d’administration et d’abonnement peuvent être gérées dans PIM. Quand vous gérez un groupe d’administration ou un abonnement dans PIM, vous pouvez également gérer ses ressources enfants.

## <a name="discover-resources"></a>Découvrir des ressources

Dans le portail Azure, accédez au volet **Privileged Identity Management**. Dans le menu de gauche, dans la section **Gérer**, sélectionnez **Ressources Azure**.

![Volet « Privileged Identity Management - Ressources Azure »](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Si vous utilisez pour la première fois PIM pour les ressources Azure, exécutez d’abord la découverte pour trouver des ressources à gérer. Dans le volet **Découvrir les ressources**, sélectionnez le bouton **Découvrir les ressources** pour lancer l’expérience de découverte.

![Volet « Découvrir les ressources »](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Si un autre administrateur de ressources ou d’annuaire dans votre entreprise gère déjà une ressource Azure à l’aide de PIM, ou si vous avez une attribution de rôle éligible pour une ressource, l’affichage de liste contient le message **Découvrez des ressources ou activez une attribution de rôle éligible pour continuer**. 

![Bouton « Découvrir les ressources » dans le volet « Privileged Identity Management - Ressources Azure »](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

Quand vous sélectionnez le bouton **Découvrir les ressources**, que ce soit à partir du menu principal ou au milieu du volet, une liste des abonnements que vous pouvez gérer s’affiche. Les abonnements qui sont mis en surbrillance sont déjà protégés par PIM.

> [!NOTE]
> Pour empêcher la suppression des paramètres de PIM par un autre administrateur de ressources, une fois qu’un abonnement est configuré comme géré, il n’est plus possible d’annuler la gestion.

![Volet « Ressources Azure - Découverte »](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

Dans la colonne **Ressource**, placez votre souris sur un abonnement que vous souhaitez protéger avec PIM. Ensuite, cochez la case à gauche du nom de la ressource. Vous pouvez sélectionner plusieurs abonnements à la fois.

![Liste des ressources dans le volet « Ressources Azure - Découverte »](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

Pour lancer le processus d’intégration, dans le menu du haut, sélectionnez **Gérer les ressources**.

![Bouton « Gérer les ressources » dans le volet « Ressources Azure - Découverte »](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

Les ressources sélectionnées sont désormais gérées par PIM. Pour fermer l’écran de découverte, dans le coin supérieur droit, sélectionnez **X**. Pour commencer à gérer les paramètres de PIM et à affecter des membres, dans le menu en haut du volet **Privileged Identity Management - Ressources Azure**, sélectionnez le bouton **Actualiser**.

![Bouton « Actualiser » dans le menu supérieur du volet « Privileged Identity Management - Ressources Azure »](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les paramètres des rôles de ressources Azure dans PIM](pim-resource-roles-configure-role-settings.md)
- [Attribuer des rôles de ressources Azure dans PIM](pim-resource-roles-assign-roles.md)
