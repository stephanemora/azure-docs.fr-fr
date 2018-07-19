---
title: Affichage des membres d’un rôle d’administrateur dans Azure Active Directory | Microsoft Docs
description: Vous pouvez maintenant consulter et gérer les membres d’un rôle d’administrateur Azure AD dans le portail. Pour les personnes qui gèrent fréquemment les attributions de rôles.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/02/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 39f7d69482845af48b1dce8c2e51f4acf77bf4fb
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448058"
---
# <a name="view-all-members-of-an-administrator-role-in-azure-active-directory"></a>Affichage de tous les membres d’un rôle d’administrateur dans Azure Active Directory

Vous pouvez maintenant consulter et gérer tous les membres des rôles d’administrateur dans le portail Azure Active Directory. Si vous gérez fréquemment des attributions de rôles, vous préférerez probablement cette expérience. Et si vous vous êtes déjà demandé « À quoi ces rôles servent-ils vraiment ? », vous pouvez voir une liste détaillée des autorisations pour chacun des rôles d’administrateur Azure AD.

Il est également facile de voir vos propres autorisations. Cliquez sur **Votre rôle** pour accéder rapidement à votre page d’utilisateur et obtenir la liste de tous vos rôles affectés actifs. Cliquez sur les points de suspension à droite de chaque ligne pour ouvrir la description détaillée du rôle.

![liste des rôles dans le portail Azure AD](./media/directory-manage-roles-portal/role-list.png)

Sélectionnez la ligne entière pour afficher la liste des membres affectés. Vous pouvez sélectionner **Gérer dans PIM** pour disposer de davantage de fonctionnalités de gestion. Les administrateurs de rôle privilégié peuvent modifier les affectations « permanentes » (toujours actives dans le rôle) en affectations « éligibles » (dans le rôle uniquement en cas d’élévation). Si vous n’avez pas PIM, vous pouvez toujours sélectionner **Gérer dans PIM** pour vous inscrire à un essai. Privileged Identity Management nécessite un [plan de licences Azure AD Premium P2](./privileged-identity-management/subscription-requirements.md).

![liste des membres d’un rôle d’administrateur](./media/directory-manage-roles-portal/member-list.png)

Si vous êtes un administrateur global ou un administrateur de rôle privilégié, vous pouvez facilement ajouter ou supprimer des membres, filtrer la liste ou sélectionner un membre pour accéder à la page d’utilisateur afin de voir ses rôles affectés actifs. 

## <a name="role-details-in-the-portal"></a>Détails du rôle dans le portail

Lorsque vous affichez les membres d’un rôle, sélectionnez **Description** pour afficher la liste complète des autorisations accordées par l’attribution de rôle. Cette page inclut des liens vers la documentation correspondante pour vous guider tout au long de la gestion des rôles d’annuaire.

![liste des autorisations pour un rôle d’administrateur](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Étapes suivantes

* N’hésitez pas à nous donner votre avis sur le [forum des rôles d’administrateur Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Pour plus d’informations sur les rôles et l’attribution de rôle d’administrateur, consultez [Attribuer des rôles d’administrateur](users-groups-roles/directory-assign-admin-roles.md).
* Pour les autorisations par défaut, consultez une [comparaison des autorisations par défaut d’un utilisateur invité et d’un membre](./fundamentals/users-default-permissions.md).
