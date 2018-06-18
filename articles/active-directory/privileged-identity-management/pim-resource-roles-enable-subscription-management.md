---
title: Privileged Identity Management pour les ressources Azure - Activer la gestion des abonnements | Microsoft Docs
description: Découvrez comment les administrateurs généraux peuvent gérer les abonnements dans le locataire.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 03/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 628ee70f7eb59673d4229441e3c4242e1ef8e0d3
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234270"
---
# <a name="enable-subscription-management-in-your-tenant"></a>Activer la gestion des abonnements dans votre locataire

En tant qu’administrateur général de votre annuaire, il est possible que vous n’ayez pas accès par défaut à toutes les ressources d’abonnement de votre locataire. Cet article décrit les étapes qui vous permettront de vous donner accès à tous les abonnements dans votre locataire. Il présente également une approche recommandée pour garantir la conformité aux contrôles de sécurité exigés par votre organisation une fois que vous bénéficiez de l’accès.

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>Qui peut activer la gestion des abonnements dans mon répertoire ?

Chaque utilisateur affecté au rôle d’administrateur général doit suivre les étapes ci-dessous pour activer la gestion des abonnements. Une fois que vous avez activé la gestion des abonnements pour vous-même, vous pouvez ajouter d’autres administrateurs généraux qui peuvent avoir besoin d’accéder aux ressources. Il n’existe aucun paramètre d’annuaire qui active l’accès pour tous les membres disposant du rôle d’administrateur général.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au portail Azure avec un compte qui est associé ou éligible au rôle d’administrateur général. Si le compte est éligible au rôle d’administrateur général, vous devez d’abord activer le rôle avant de passer à l’étape suivante.

## <a name="access-the-azure-active-directory-admin-center"></a>Accéder au Centre d’administration Azure Active Directory

Maintenant que vous êtes connecté au portail Azure en tant qu’administrateur général, vous pouvez modifier les paramètres qui fournissent l’accès aux abonnements Azure. Accédez au Centre d’administration Azure Active Directory (Azure AD) et sélectionnez **Propriétés**.

![Capture d’écran du Centre d’administration Azure AD avec Propriétés mis en surbrillance](media/azure-pim-resource-rbac/aad_properties.png)

Dans la liste des propriétés, sous **L’administrateur général peut gérer les abonnements Azure**, sélectionnez **Oui**.

![Capture d’écran de la page Propriétés, avec bouton bascule défini sur Oui](media/azure-pim-resource-rbac/aad_properties_save.png)

Votre compte est maintenant ajouté automatiquement au rôle Administrateur de l’accès utilisateur pour chaque ressource d’abonnement dans le locataire.

## <a name="browse-to-azure-ad-pim"></a>Accéder à Azure AD PIM

 À partir de là, accédez à Azure AD Privileged Identity Management (PIM). Sous **Gérer**, sélectionnez **Ressources Azure**.

![Capture d’écran de PIM avec Ressources Azure mis en surbrillance](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>Gérer et découvrir des ressources

Si votre entreprise utilise déjà Azure AD PIM pour protéger les administrateurs dans Azure AD, une liste d’abonnements s’affiche dans le panneau qui apparaît.

![Capture d’écran de PIM avec liste des abonnements affichée dans le panneau](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> Si aucune ressource n’est visible, vérifiez que :
>- Votre rôle d’administrateur général n’a pas expiré. 
>- Votre entreprise dispose d’un abonnement Azure.

![Capture d’écran de PIM avec liste de ressources vide](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>Configurer des affectations

Sélectionnez un abonnement dans la liste et affectez-vous directement (ou un groupe dont vous êtes membre) en tant que propriétaire éligible de la ressource. 
[En savoir plus sur l’affectation de rôles](pim-resource-roles-assign-roles.md).

Répétez cette opération pour chaque ressource avant de passer à l’étape suivante.

## <a name="clean-up-standing-access"></a>Supprimer l’accès permanent

Maintenant que vous disposez d’affectations éligibles pour les abonnements importants de votre entreprise, vous pouvez supprimer l’accès permanent en désactivant l’option correspondante dans les propriétés de l’annuaire.

![Capture d’écran de la page Propriétés avec bouton bascule défini sur Non](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>Étapes suivantes

[Découvrir des ressources](pim-resource-roles-discover-resources.md)

[Configurer des paramètres de rôle](pim-resource-roles-configure-role-settings.md)








