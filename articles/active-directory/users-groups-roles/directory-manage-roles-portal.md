---
title: Afficher et assigner des autorisations de rôles d’administrateur dans Azure Active Directory | Microsoft Docs
description: Vous pouvez maintenant consulter et gérer les membres d’un rôle d’administrateur Azure AD dans le portail. Pour les personnes qui gèrent fréquemment les attributions de rôles.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/06/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 267b9f7c6dcdc994345371a0884db3c1271f56bd
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198161"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Afficher et assigner des rôles d’administrateur dans Azure Active Directory

Vous pouvez maintenant consulter et gérer tous les membres des rôles d’administrateur dans le portail Azure Active Directory. Si vous gérez fréquemment des attributions de rôles, vous préférerez probablement cette expérience. Et si vous vous êtes déjà demandé « À quoi ces rôles servent-ils vraiment ? », vous pouvez voir une liste détaillée des autorisations pour chacun des rôles d’administrateur Azure AD.

## <a name="view-all-roles"></a>Afficher tous les rôles

Dans Azure Active Directory, sélectionnez **Rôles et administrateurs** pour afficher la liste de tous les rôles disponibles. 

Cliquez sur les points de suspension à droite de chaque ligne pour ouvrir la description détaillée du rôle.

![liste des rôles dans le portail Azure AD](./media/directory-manage-roles-portal/role-list.png)

## <a name="view-my-roles"></a>Afficher mes rôles

Il est également facile d’afficher vos propres autorisations. Sélectionnez **Votre rôle** dans la page **Rôles et administrateurs** pour afficher les rôles qui sont actuellement attribués.

## <a name="view-assignments-for-a-role"></a>Afficher les attributions pour un rôle

Cliquez sur un rôle pour afficher les utilisateurs assignés au rôle. Vous pouvez sélectionner **Gérer dans PIM** pour disposer de davantage de fonctionnalités de gestion. Les administrateurs de rôle privilégié peuvent modifier les affectations « permanentes » (toujours actives dans le rôle) en affectations « éligibles » (dans le rôle uniquement en cas d’élévation). Si vous n’avez pas PIM, vous pouvez toujours sélectionner **Gérer dans PIM** pour vous inscrire à un essai. Privileged Identity Management nécessite un [plan de licences Azure AD Premium P2](../privileged-identity-management/subscription-requirements.md).

![liste des membres d’un rôle d’administrateur](./media/directory-manage-roles-portal/member-list.png)

Si vous êtes un administrateur global ou un administrateur de rôle privilégié, vous pouvez facilement ajouter ou supprimer des membres, filtrer la liste ou sélectionner un membre pour voir ses rôles assignés actifs.

## <a name="view-a-users-role-permissions"></a>Afficher les autorisations de rôles d’un utilisateur

Lorsque vous affichez les membres d’un rôle, sélectionnez **Description** pour afficher la liste complète des autorisations accordées par l’attribution de rôle. Cette page inclut des liens vers la documentation correspondante pour vous guider tout au long de la gestion des rôles d’annuaire.

![liste des autorisations pour un rôle d’administrateur](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Étapes suivantes

* N’hésitez pas à nous donner votre avis sur le [forum des rôles d’administrateur Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Pour plus d’informations sur les rôles et l’attribution de rôle d’administrateur, consultez [Attribuer des rôles d’administrateur](directory-assign-admin-roles.md).
* Pour les autorisations d’utilisateur par défaut, consultez une [comparaison des autorisations par défaut d’un utilisateur invité et d’un membre](../fundamentals/users-default-permissions.md).
