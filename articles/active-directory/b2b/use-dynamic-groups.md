---
title: Groupes dynamiques et collaboration B2B - Azure Active Directory | Microsoft Docs
description: Cet article montre comme utiliser des groupes dynamiques Azure AD avec Azure Active Directory B2B Collaboration.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17f701eb9ad3c083712ddf9e774e6ddbefbe91fd
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65227950"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Groupes dynamiques et Azure Active Directory B2B Collaboration

## <a name="what-are-dynamic-groups"></a>Qu'est-ce-que les groupes dynamiques ?
La configuration dynamique de l’appartenance au groupe de sécurité pour Azure Active Directory (Azure AD) est disponible dans le [portail Azure](https://portal.azure.com). Les administrateurs peuvent définir des règles pour remplir les groupes qui sont créés dans Azure AD en fonction des attributs utilisateur (par exemple, userType, département ou pays/région). Les membres peuvent être automatiquement ajoutés ou supprimés d’un groupe de sécurité en fonction de leurs attributs. Ces groupes permettent d’accorder l’accès à des applications ou à des ressources cloud (sites SharePoint, documents) et d’attribuer des licences à des utilisateurs. En savoir plus sur les groupes dynamiques dans [Groupes dédiés dans Azure Active Directory](../active-directory-accessmanagement-dedicated-groups.md).

La [licence Azure AD Premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory/) appropriée est nécessaire pour créer et utiliser des groupes dynamiques. Pour en savoir plus, consultez l’article [Créer des règles basées sur les attributs pour l’appartenance à un groupe dynamique dans Azure Active Directory](../users-groups-roles/groups-dynamic-membership.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Que sont les groupes dynamiques intégrés ?
Le groupe dynamique **Tous les utilisateurs** permet à des administrateurs de locataires de créer un groupe contenant tous les utilisateurs dans le locataire en un seul clic. Par défaut, le groupe **Tous les utilisateurs** inclut tous les utilisateurs du répertoire, dont les invités et les utilisateurs externes.
Dans le nouveau portail d’administration Azure Active Directory, vous pouvez choisir d’activer le groupe **Tous les utilisateurs** dans l’affichage des paramètres de groupe.

![Activer le groupe Tous les utilisateurs défini sur Oui](media/use-dynamic-groups/enable-all-users-group.png)

## <a name="hardening-the-all-users-dynamic-group"></a>Renforcement de la sécurité du groupe dynamique Tous les utilisateurs
Par défaut, le groupe **Tous les utilisateurs** contient également vos utilisateurs B2B Collaboration (invités). Vous pouvez sécuriser davantage votre groupe **Tous les utilisateurs** en utilisant une règle pour supprimer les utilisateurs invités. L’illustration suivante montre le groupe **Tous les utilisateurs** modifié de manière à exclure les invités.

![Règle pour laquelle le type utilisateur n’est pas Invité](media/use-dynamic-groups/exclude-guest-users.png)

Vous trouverez peut-être aussi utile de créer un groupe dynamique contenant uniquement les utilisateurs invités, afin de pouvoir leur appliquer des stratégies (telles que des stratégies d’accès conditionnel Azure AD).
Ce groupe pourrait ressembler à ceci :

![Règle pour laquelle le type utilisateur est Invité](media/use-dynamic-groups/only-guest-users.png)

## <a name="next-steps"></a>Étapes suivantes

- [Propriétés de l’utilisateur B2B Collaboration](user-properties.md)
- [Ajout d’un utilisateur B2B Collaboration à un rôle](add-guest-to-role.md)
- [Accès conditionnel pour les utilisateurs de B2B Collaboration](conditional-access.md)

