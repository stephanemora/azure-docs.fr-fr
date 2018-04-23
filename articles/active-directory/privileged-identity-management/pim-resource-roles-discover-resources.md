---
title: Privileged Identity Management pour les ressources Azure - Découvrir et gérer les ressources Azure | Microsoft Docs
description: Décrit comment protéger les ressources Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 78650e47ec92aa144e4ccc8c57f309240bf31ee3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="discover-and-manage-azure-resources"></a>Découvrir et gérer des ressources Azure

Si votre entreprise utilise déjà Azure AD PIM pour protéger les administrateurs de votre répertoire ou si vous êtes propriétaire d’un abonnement et que vous cherchez à sécuriser les ressources de production, vous êtes au bon endroit.

Lorsque vous activez PIM pour les ressources Azure pour la première fois, vous devez découvrir et sélectionner les ressources à protéger avec PIM. Il n’existe aucune limite quant au nombre de ressources que vous pouvez gérer avec PIM, mais nous vous recommandons de commencer avec vos ressources (de production) les plus critiques.

> [!Note]
> Seules les ressources d’abonnement peuvent faire l’objet d’une recherche et être sélectionnées pour la gestion. Choisir de gérer un abonnement dans PIM va également activer la gestion pour toutes les ressources enfants.

## <a name="discover-resources"></a>Découvrir des ressources

Accédez à Azure AD PIM et sélectionnez les ressources Azure dans la section Gérer du menu de navigation de gauche.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Si vous utilisez pour la première fois PIM pour les ressources Azure, vous devrez exécuter la découverte pour trouver des ressources à gérer.
Cliquez sur le bouton « Découvrir les ressources » au centre de l’écran pour lancer la découverte.

![](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Si un autre administrateur de ressources ou de répertoire dans votre entreprise gère déjà une ressource Azure avec PIM, ou si vous avez une attribution de rôle éligible pour une ressource, la vue de la liste contient le message : « Découvrez des ressources ou activez une attribution de rôle éligible pour continuer ». 

![](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

Lorsque vous sélectionnez le bouton, dans la barre d’action ou au milieu de l’écran Découvrir les ressources, vous verrez s’afficher la liste des abonnements disponibles pour la gestion. À ce stade, si vous voyez des abonnements en surbrillance, cela indique qu’ils sont protégés par PIM.

> [!Note]
> Pour empêcher la suppression des paramètres de PIM par un autre administrateur de ressources, il n’est pas possible d’annuler la gestion d’un abonnement.

![](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

Passez le curseur de la souris sur un abonnement que vous souhaitez protéger avec PIM et cochez la case à gauche de la ligne. Vous pouvez sélectionner plusieurs abonnements à la fois.

![](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

Pour lancer le processus d’intégration, sélectionnez le bouton « Gérer les ressources » dans la barre en haut de l’écran.

![](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

Les ressources sélectionnées sont désormais gérées par PIM. Fermez l’écran de découverte en appuyant sur « X » dans le coin supérieur droit de la page, puis cliquez sur Actualiser dans la barre en haut de l’écran Gérer les ressources Azure pour commencer à gérer les paramètres de PIM et à assigner des membres.

![](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>Étapes suivantes

[Configurer des paramètres de rôle](pim-resource-roles-configure-role-settings.md)

[Attribuer des rôles dans PIM](pim-resource-roles-assign-roles.md)
