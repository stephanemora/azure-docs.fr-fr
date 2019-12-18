---
title: Stratégies d’accès conditionnel courantes - Azure Active Directory
description: Stratégies d’accès conditionnel couramment utilisées pour les organisations
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3d85850fb18b80490bba44b293ece7765124133
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846111"
---
# <a name="common-conditional-access-policies"></a>Stratégies d’accès conditionnel courantes

Les stratégies de protection de la base de référence sont intéressantes, mais beaucoup d’organisations ont besoin d’une plus grande flexibilité. Par exemple, de nombreuses organisations ont besoin de pouvoir exclure des comptes spécifiques, tels que leurs comptes d’administration d’accès d’urgence ou d’administration de secours avec des stratégies d’accès conditionnel nécessitant l’authentification multifacteur. Pour ces organisations, les stratégies courantes référencées dans cet article peuvent être utilisées.

![Stratégies d’accès conditionnel sur le portail Azure](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Comptes d’accès d’urgence

Pour plus d’informations sur les comptes d’accès d’urgence et la raison pour laquelle ils sont importants, consultez les articles suivants : 

* [Gérer des comptes d’accès d’urgence dans Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Créer une stratégie de gestion du contrôle d'accès résiliente avec Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Stratégies standard déployées par les organisations

* [Exiger MFA pour les administrateurs](howto-conditional-access-policy-admin-mfa.md)
* [Exiger MFA pour la gestion Azure](howto-conditional-access-policy-azure-management.md)
* [Exiger MFA pour tous les utilisateurs](howto-conditional-access-policy-all-users-mfa.md)
* [Bloquer l’authentification héritée](howto-conditional-access-policy-block-legacy.md)
* [Accès conditionnel basé sur le risque (requiert Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Exiger un emplacement approuvé pour l’inscription MFA](howto-conditional-access-policy-registration.md)
* [Bloquer l’accès par emplacement](howto-conditional-access-policy-location.md)
* [Exiger un appareil conforme](howto-conditional-access-policy-compliant-device.md)

## <a name="next-steps"></a>Étapes suivantes

- [Simuler le comportement de connexion à l’aide de l’outil What If pour l’accès conditionnel.](troubleshoot-conditional-access-what-if.md)
- [Utilisez le mode Rapport uniquement pour l’accès conditionnel afin de déterminer l’impact des nouvelles décisions de stratégie.](concept-conditional-access-report-only.md)
