---
title: Effectuez une révision d’accès des ressources Azure et des rôles Azure AD dans PIM - Azure AD | Microsoft Docs
description: Découvrez comment réviser l’accès aux ressources Azure et rôles Azure AD dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31a2d1b9e15f795da4931ffbff88f5222aad69c2
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669584"
---
# <a name="perform-an-access-review-of-azure-resource-and-azure-ad-roles-in-pim"></a>Créez une révision d’accès des ressources Azure et des rôles Azure AD dans PIM

Le service Privileged Identity Management (PIM) simplifie la gestion par les entreprises de l’accès privilégié aux ressources dans Azure Active Directory et d’autres services en ligne Microsoft, tels que Microsoft 365 ou Microsoft Intune. Suivez les étapes décrites dans cet article pour effectuer des révisions de l’accès aux rôles.

Si vous êtes affecté à un rôle d’administrateur, l’administrateur de rôle privilégié de votre organisation peut vous demander de confirmer régulièrement que vous avez toujours besoin de ce rôle pour votre travail. Vous pouvez recevoir un e-mail contenant un lien, ou accéder directement au [portail Azure](https://portal.azure.com) et commencer.

Si vous êtes un administrateur de rôle privilégié ou administrateur global intéressé par les révisions d’accès, consultez [Comment démarrer une révision d’accès](pim-create-azure-ad-roles-and-resource-roles-review.md)pour plus de détails.

## <a name="approve-or-deny-access"></a>Approuver ou refuser l'accès

Vous pouvez approuver ou refuser l’accès selon que l’utilisateur a toujours besoin d’accéder au rôle. Choisissez **Approuver** si vous souhaitez conserver le rôle, ou **Refuser** si vous n’avez plus besoin de l’accès. L’état d’affectation des utilisateurs ne changera pas tant que la vérification n’est pas fermée et que l’administrateur n’a pas appliqué les résultats. Les scénarios courants dans lesquels certains utilisateurs refusés ne peuvent pas avoir de résultats appliqués peuvent inclure les éléments suivants :

- **Examen des membres d’un groupe local Windows AD synchronisé**: si le groupe est synchronisé à partir d’un Windows AD local, le groupe ne peut pas être géré dans Azure AD et, par conséquent, l’appartenance ne peut pas être modifiée.
- **Examen d’un rôle avec des groupes imbriqués affectés**: pour les utilisateurs qui sont membres d’un groupe imbriqué, la révision d’accès ne supprime pas leur appartenance au groupe imbriqué et, par conséquent, ils conservent l’accès au rôle en cours de révision.
- **Utilisateur introuvable ou autres erreurs**: peuvent également entraîner la non prise en charge d’un résultat d’application.

Procédez comme suit pour rechercher et terminer la révision de l’accès :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Sélectionnez **Azure Active Directory**, puis ouvrez **Privileged Identity Management**.
1. Sélectionnez **Réviser l’accès**. Si vous avez des révisions d’accès en attente, elles apparaîtront dans la page des révisions d’accès.

    :::image type="content" source="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-complete.png" alt-text="Capture d’écran de l’application Privileged Identity Management, avec le panneau révision de l’accès sélectionné pour les rôles Azure AD." lightbox="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-complete.png":::

1. Sélectionnez la révision à terminer.
1. Cliquez sur **Approuver** ou **Refuser**. Dans la **zone fournir une raison**, entrez une justification commerciale pour votre décision, si nécessaire.

    :::image type="content" source="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-completed.png" alt-text="Capture d’écran de l’application Privileged Identity Management, avec la révision de l’accès sélectionnée pour les rôles Azure AD." lightbox="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-completed.png":::

## <a name="next-steps"></a>Étapes suivantes

- [Créez une révision d’accès des ressources Azure et des rôles Azure AD dans PIM ](pim-create-azure-ad-roles-and-resource-roles-review.md)
- [Effectuez une révision d’accès des ressources Azure et des rôles Azure AD dans PIM](pim-complete-azure-ad-roles-and-resource-roles-review.md)
