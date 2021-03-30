---
title: Répertorier les définitions de rôles Azure AD – Azure AD
description: Apprenez à répertorier les rôles intégrés et personnalisés d’Azure.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d37b2988d32c854e4184adee998341ebadcee053
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467760"
---
# <a name="list-azure-ad-role-definitions"></a>Répertorier les définitions de rôles Azure AD

Une définition de rôle est une collection d’autorisations qui peuvent être effectuées, comme lire, écrire et supprimer. Elle est généralement simplement appelée « rôle ». Azure Active Directory compte plus de 60 rôles intégrés. Vous pouvez également créer vos propres rôles personnalisés. Si vous vous êtes déjà demandé « À quoi ces rôles servent-ils vraiment ? », vous pouvez voir une liste détaillée des autorisations pour chacun des rôles.

Cet article explique comment répertorier les rôles intégrés et personnalisés Azure AD, ainsi que leurs autorisations.

## <a name="list-all-roles"></a>Répertorier tous les rôles

1. Connectez-vous au [centre d’administration Azure AD](https://aad.portal.azure.com) et sélectionnez **Azure Active Directory**.

1. Sélectionnez **Rôles et administrateurs** pour voir la liste de tous les rôles disponibles.

    ![liste des rôles dans le portail Azure AD](./media/role-definitions-list/view-roles-in-azure-active-directory.png)

1. Sur la droite, cliquez sur le bouton de sélection, puis sur **Description** pour afficher la liste complète des autorisations pour un rôle.

    Cette page inclut des liens vers la documentation correspondante pour vous guider tout au long de la gestion des rôles.

    ![Capture d'écran représentant la page « Administrateur général - Description ».](./media/role-definitions-list/role-description.png)

## <a name="next-steps"></a>Étapes suivantes

* N’hésitez pas à nous donner votre avis sur le [forum des rôles d’administrateur Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Pour plus d’informations sur les rôles et l’attribution de rôle d’administrateur, consultez [Attribuer des rôles d’administrateur](permissions-reference.md).
* Pour les autorisations d’utilisateur par défaut, consultez une [comparaison des autorisations par défaut d’un utilisateur invité et d’un membre](../fundamentals/users-default-permissions.md).
