---
title: Découvrir et gérer les ressources Azure à l’aide de Privileged Identity Management | Microsoft Docs
description: Décrit comment protéger les ressources Azure à l’aide de PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: 563c7f24ec4045b46d5bdcf0dc267dbdda2d5a5e
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234469"
---
# <a name="discover-and-manage-azure-resources-by-using-privileged-identity-management"></a>Découvrir et gérer les ressources Azure à l’aide de Privileged Identity Management

Découvrez comment découvrir et gérer les ressources Azure quand vous utilisez PIM (Privileged Identity Management) dans Azure AD (Azure Active Directory). Ces informations peuvent être utiles aux organisations qui utilisent déjà PIM pour protéger les ressources de l’administrateur, ainsi qu’aux propriétaires d’abonnements qui souhaitent sécuriser les ressources de production.

Quand vous configurez PIM pour les ressources Azure pour la première fois, vous devez découvrir et sélectionner les ressources à protéger avec PIM. Il n’existe aucune limite quant à la quantité de ressources que vous pouvez gérer avec PIM. Toutefois, nous vous recommandons de commencer avec vos ressources les plus critiques (production).

> [!NOTE]
> Vous pouvez uniquement rechercher et sélectionner les ressources d’abonnement à gérer à l’aide de PIM. Quand vous gérez un abonnement dans PIM, vous pouvez également gérer les ressources enfants dans l’abonnement.

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

- [Configurer des paramètres de rôle](pim-resource-roles-configure-role-settings.md)
- [Attribuer des rôles dans PIM](pim-resource-roles-assign-roles.md)
