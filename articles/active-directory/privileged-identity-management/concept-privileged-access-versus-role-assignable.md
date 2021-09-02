---
title: La différence entre les groupes d’accès privilégiés et les groupes assignables à un rôle - Azure AD | Microsoft Docs
description: Découvrez comment faire la différence entre les groupes d’accès privilégiés et les groupes assignables à un rôle dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/02/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5f9d968c114c28dcff8e247435c40960040c4e9
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122563937"
---
# <a name="whats-the-difference-between-privileged-access-groups-and-role-assignable-groups"></a>La différence entre les groupes d’accès privilégiés et les groupes assignables à un rôle

Privileged Identity Management (PIM) prend en charge la possibilité d’activer l’accès privilégié sur les groupes assignables à un rôle. Toutefois, étant donné qu’un groupe assignable à un rôle est une condition préalable à la création d’un groupe d’accès privilégié, cet article explique les différences et comment en tirer parti.

## <a name="what-are-azure-ad-role-assignable-groups"></a>Que sont les groupes assignables à un rôle Azure AD ?

Azure AD vous permet d’affecter un groupe de sécurité cloud Azure AD à un rôle Azure AD. Les administrateurs généraux et administrateurs de rôles privilégiés doivent créer un groupe de sécurité et rendre activer l’attribution de rôle pour le groupe au moment de la création. Seuls les utilisateurs appartenant aux rôles Administrateur général, Administrateur de rôle privilégié ou Propriétaire du groupe peuvent modifier l’appartenance du groupe. En outre, aucun autre utilisateur ne peut réinitialiser le mot de passe des utilisateurs membres du groupe. Cette fonctionnalité permet d’empêcher les administrateurs de s’élever à un rôle de privilège plus élevé sans passer par une procédure de requête et d’approbation.

## <a name="what-are-privileged-access-groups"></a>Que sont les groupes d’accès privilégié ?

Les groupes d’accès privilégiés permettent aux utilisateurs d’élever le rôle de propriétaire ou de membre d’un groupe de sécurité Azure AD. Cette fonctionnalité vous permet de configurer des workflows juste-à-temps pour non seulement les rôles Azure AD et Azure par lots, mais également des scénarios juste-à-temps pour d’autres cas d’usage comme Azure SQL, Azure Key Vault, Intune ou d’autres rôles d’application. Pour plus d’informations, consultez [Fonctionnalités de gestion pour les groupes d’accès privilégié](groups-features.md).

>[!Note]
>Pour les groupes d’accès privilégiés utilisés pour l’élévation des rôles Azure AD, Microsoft recommande de demander un processus d’approbation pour les attributions de membres éligibles. Les affectations pouvant être activées sans approbation peuvent vous laisser vulnérable à un risque de sécurité venant d’administrateurs moins privilégiés. Par exemple, l’administrateur du support technique a l’autorisation de réinitialiser les mots de passe d’un utilisateur éligible.

## <a name="when-to-use-each-type-of-group"></a>Quand utiliser chaque type de groupe

Vous pouvez configurer l’accès juste-à-temps aux autorisations et rôles au-delà d’Azure AD et des ressources Azure. Si vous avez d’autres ressources dont l’autorisation peut être connectée à un groupe de sécurité Azure AD (pour Azure Key Vault, Intune, SQL Azure ou d’autres applications et services), vous devez activer l’accès privilégié sur le groupe et affecter des utilisateurs comme éligibles pour l’appartenance au groupe.

Si vous souhaitez affecter un groupe à un rôle Azure AD ou de ressource Azure et que vous devez effectuer une élévation via un processus PIM, il existe deux façons de procéder :

- **Assignez le groupe de façon permanente à un rôle**. Vous accordez ensuite aux utilisateurs un accès aux membres éligibles au groupe dans PIM. Les utilisateurs éligibles doivent ensuite activer leur appartenance pour accéder au groupe qui est affecté de manière permanente au rôle. Ce chemin d’accès nécessite l’activation d’un groupe assignable à un rôle dans PIM en tant que groupe d’accès privilégié pour le rôle Azure AD.
- **Affectez le groupe comme éligible pour un rôle** à travers PIM. Tout le monde dans le groupe doit activer son attribution pour accéder au rôle. Ce chemin d’accès nécessite un groupe assignable à un rôle pour le rôle Azure AD et un groupe de sécurité pour les ressources Azure.

    ![Diagramme montrant deux façons d’attribuer un rôle à l’aide de groupes d’accès privilégiés dans PIM.](./media/concept-privileged-access-versus-role-assignable/concept-privileged-access.png)

L’une ou l’autre de ces méthodes fonctionnera pour le scénario de bout en bout. Nous vous recommandons d’utiliser la première méthode dans la plupart des cas. Vous devez utiliser la deuxième méthode uniquement si vous essayez d’effectuer les opérations suivantes :

- Affecter un groupe à plusieurs rôles Azure AD ou de ressources Azure et demander aux utilisateurs de s’activer une seule fois pour accéder à plusieurs rôles.
- Maintenir différentes stratégies d’activation pour différents groupes d’utilisateurs afin d’accéder à un rôle Azure AD ou de ressource Azure. Par exemple, si vous souhaitez que certains utilisateurs soient approuvés avant de devenir administrateur général tout en permettant à d’autres utilisateurs d’être approuvés automatiquement, vous pouvez configurer deux groupes d’accès privilégié, les affecter de manière permanente (une affectation « permanente » dans Privileged Identity Management) au rôle d’administrateur général, puis utiliser une stratégie d’activation différente pour le rôle de membre pour chaque groupe.

## <a name="next-steps"></a>Étapes suivantes

- [Approuver ou rejeter les requêtes de rôles Azure AD](azure-ad-pim-approval-workflow.md)
- [Approuver ou rejeter les demandes de rôles de ressources Azure](pim-resource-roles-approval-workflow.md)
- [Approuver des demandes d’activation de membres et propriétaires de groupe d’accès privilégié (préversion)](groups-approval-workflow.md)
