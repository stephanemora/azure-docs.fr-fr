---
title: Accorder l’accès en fonction du rôle pour les utilisateurs dans Microsoft Azure Maps
titleSuffix: Azure Maps
description: Utilisez le contrôle d’accès en fonction du rôle pour octroyer aux utilisateurs l’autorisation vers Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/21/2021
ms.topic: include
ms.service: azure-maps
services: azure-maps
custom.ms: subject-rbac-steps
ms.openlocfilehash: 22b3eaaff0989718a781b29549b4fab102673273
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803273"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Accorder l’accès en fonction du rôle pour des utilisateurs à Azure Maps

Vous pouvez octroyer le *contrôle d’accès en fonction du rôle Azure (Azure RBAC)* , en attribuant soit un groupe Azure AD, soit un principal de sécurité, à une ou plusieurs définitions de rôle Azure Maps.

Pour afficher les définitions de rôle Azure disponibles pour Azure Maps, consultez [Afficher les définitions de rôles intégrés Azure Maps](../how-to-manage-authentication.md#view-built-in-azure-maps-role-definitions).

Pour obtenir des instructions détaillées sur l’affectation d’un rôle Azure Maps disponible à l’identité gérée créée ou au principal de service, consultez [Affecter des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md)

Pour gérer efficacement l’application Azure Maps et l’accès aux ressources d’un grand nombre d’utilisateurs, consultez [Groupes Azure AD](../../active-directory/fundamentals/active-directory-manage-groups.md).

>[!IMPORTANT]
>Pour que les utilisateurs soient autorisés à s’authentifier sur une application, les utilisateurs doivent d’abord être créés dans Azure AD. Pour plus d’informations, consultez [Ajouter ou supprimer des utilisateurs à l’aide d’Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md).

Pour en savoir plus sur la gestion efficace d’un répertoire volumineux pour les utilisateurs, consultez [Azure AD](../../active-directory/fundamentals/index.yml).

> [!WARNING]
> Les définitions de rôles intégrées Azure Maps fournissent un accès d’autorisation très large à de nombreuses API REST Azure Maps. Pour limiter l’accès aux API à un minimum, consultez cet [article](../../role-based-access-control/custom-roles.md) expliquant comment créer une définition de rôle personnalisée et affecter l’identité affectée par le système à la définition de rôle personnalisée. Cela permet d’activer les privilèges minimums nécessaires à l’application pour accéder à Azure Maps.
