---
title: Attribuer des rôles Azure AD aux utilisateurs – Azure Active Directory
description: Découvrez comment accorder l’accès à des utilisateurs dans Azure Active Directory en attribuant des rôles Azure AD.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d62e723f42838343798603517ca430d727ff517
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110787832"
---
# <a name="assign-azure-ad-roles-to-users"></a>Attribuer des rôles Azure AD aux utilisateurs

Vous pouvez maintenant consulter et gérer tous les membres des rôles Administrateur dans le centre d’administration Azure AD. Si vous gérez fréquemment des attributions de rôles, vous préférerez probablement cette expérience. Cet article explique comment attribuer des rôles Azure AD à l’aide du centre d’administration Azure AD.

## <a name="prerequisites"></a>Prérequis

- Administrateur de rôle privilégié ou Administrateur général
- Licence Azure AD Premium P2 avec Privileged Identity Management (PIM)

## <a name="assign-a-role"></a>Attribuer un rôle

1. Connectez-vous au [Centre d’administration Azure AD](https://aad.portal.azure.com).

1. Sélectionnez **Azure Active Directory**.

1. Sélectionnez **Rôles et administrateurs** pour voir la liste de tous les rôles disponibles.

    ![Capture d’écran de la page Rôles et administrateurs](./media/manage-roles-portal/roles-and-administrators.png)

1. Sélectionnez un rôle pour afficher ses attributions.

    Pour vous aider à trouver le rôle dont vous avez besoin, Azure AD peut vous montrer des sous-ensembles de rôles basés sur des catégories de rôles. Jetez un coup d’œil au filtre **Type** qui vous montre uniquement les rôles du type sélectionné.

1. Sélectionnez **Ajouter des attributions**, puis sélectionnez les utilisateurs que vous souhaitez affecter à ce rôle.

    Si vous voyez quelque chose de différent de l’image suivante, lisez la note de [Privileged Identity Management (PIM)](#privileged-identity-management-pim) pour vérifier si vous utilisez PIM.

    ![liste des autorisations pour un rôle d’administrateur](./media/manage-roles-portal/add-assignments.png)

1. Sélectionnez **Ajouter** pour attribuer le rôle.

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Vous pouvez sélectionner **Gérer dans PIM** pour obtenir des capacités de gestion supplémentaires à l’aide d’[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md). Les administrateurs de rôle privilégié peuvent modifier les affectations « permanentes » (toujours actives dans le rôle) en affectations « éligibles » (dans le rôle uniquement en cas d’élévation). Si vous n’avez pas Privileged Identity Management, vous pouvez toujours sélectionner **Gérer dans PIM** pour vous inscrire à un essai. Privileged Identity Management nécessite une [licence Azure AD Premium P2](../privileged-identity-management/subscription-requirements.md).

![Capture d’écran représentant la page « Administrateur d’utilisateurs – Attributions » sur laquelle l’action « Gérer dans PIM » est sélectionnée](./media/manage-roles-portal/member-list-pim.png)

Si vous êtes un administrateur global ou un administrateur de rôle privilégié, vous pouvez facilement ajouter ou supprimer des membres, filtrer la liste ou sélectionner un membre pour voir ses rôles assignés actifs.

> [!Note]
> Si vous disposez d’une licence Azure AD Premium P2 et que vous utilisez déjà Privileged Identity Management, toutes les tâches de gestion des rôles sont effectuées dans Privilege Identity Management et non dans Azure AD.
>
> ![Rôles Azure AD gérés dans PIM pour les utilisateurs qui utilisent déjà PIM et qui disposent d’une licence Premium P2](./media/manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="next-steps"></a>Étapes suivantes

* N’hésitez pas à nous donner votre avis sur le [forum des rôles d’administrateur Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Pour plus d’informations sur les rôles, consultez [Rôles intégrés Azure AD](permissions-reference.md).
* Pour les autorisations d’utilisateur par défaut, consultez une [comparaison des autorisations par défaut d’un utilisateur invité et d’un membre](../fundamentals/users-default-permissions.md).
