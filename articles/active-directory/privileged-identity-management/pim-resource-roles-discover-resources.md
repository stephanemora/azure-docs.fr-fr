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
ms.date: 08/30/2018
ms.author: rolyon
ms.openlocfilehash: d9a6ab49d619e487eee6fb13abe128cfc167b560
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306687"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>Découvrir les ressources Azure à gérer dans PIM

Azure AD Privileged Identity Management (PIM) vous permet d’améliorer la protection de vos ressources Azure. Cela est utile pour les organisations qui utilisent déjà PIM pour protéger des rôles d’annuaire Azure AD, et pour les propriétaires d’abonnement et de groupe d’administration qui cherchent à sécuriser des ressources de production.

Quand vous configurez pour la première fois PIM pour des ressources Azure, vous devez découvrir et sélectionner les ressources à protéger avec PIM. Il n’existe aucune limite quant à la quantité de ressources que vous pouvez gérer avec PIM. Toutefois, nous vous recommandons de commencer avec vos ressources les plus critiques (production).

## <a name="discover-resources"></a>Découvrir des ressources

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Ressources Azure**.

    Si c’est la première fois que vous utilisez PIM pour des ressources Azure, vous voyez s’afficher un volet Découvrir les ressources.

    ![Découvrir les ressources - première fois](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Si un autre administrateur de ressources ou d’annuaire au sein de votre organisation gère déjà des ressources Azure dans PIM, vous voyez s’afficher la liste des ressources actuellement gérées.

    ![Volet Découvrir les ressources](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Cliquez sur **Découvrir les ressources** pour lancer l’expérience de découverte.

    ![Volet Découverte](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. Dans le volet Découverte, utilisez les options **Filtre d’état de ressource** et **Sélectionner le type de ressource** pour filtrer les groupes d’administration ou abonnements pour lesquels vous disposez d’une autorisation d’écriture. Le plus simple est probablement de commencer avec **Tous**.

    Seules les ressources de groupe d’administration et d’abonnement peuvent être gérées dans PIM. Quand vous gérez un groupe d’administration ou un abonnement dans PIM, vous pouvez également gérer ses ressources enfants.

1. Ajoutez une coche en regard des ressources non managées que vous souhaitez gérer.

    > [!NOTE]
    > Une fois qu’un groupe d’administration ou abonnement est défini sur managé, il ne peut pas ne pas être managé. Cela empêche tout autre administrateur de ressources de supprimer des paramètres de PIM.

    ![Découverte - Gérer les ressources](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Cliquez sur **Gérer les ressources** pour commencer à gérer les ressources sélectionnées.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les paramètres des rôles de ressources Azure dans PIM](pim-resource-roles-configure-role-settings.md)
- [Attribuer des rôles de ressources Azure dans PIM](pim-resource-roles-assign-roles.md)
