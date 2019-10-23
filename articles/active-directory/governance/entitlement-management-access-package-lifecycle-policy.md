---
title: Modifier les paramètres de cycle de vie pour un package d’accès dans la gestion des droits d’utilisation d’Azure Active Directory (préversion) - Azure Active Directory
description: Découvrez comment modifier les paramètres de cycle de vie d’un package d'accès dans la gestion des droits d’utilisation d’Azure Active Directory (préversion).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46d63a12443edf04db3570fa43fbd8f20619122e
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391972"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Modifier les paramètres de cycle de vie pour un package d’accès dans la gestion des droits d’utilisation d’Azure Active Directory (préversion)

> [!IMPORTANT]
> La gestion des droits d’utilisation Azure Active Directory (Azure AD) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

En tant que gestionnaire de package d’accès, vous pouvez modifier les paramètres de cycle de vie d’un package d’accès à tout moment en modifiant une stratégie existante. Si vous modifiez la date d'expiration d'une stratégie, la date d'expiration des demandes en attente d'approbation ou approuvées ne change pas.

Cet article explique comment modifier les paramètres de cycle de vie d’un package d’accès existant.

## <a name="open-lifecycle-settings"></a>Ouvrir les paramètres de cycle de vie

Pour modifier les paramètres de cycle de vie d’un package d’accès, vous devez ouvrir la stratégie correspondante. Suivez ces étapes pour ouvrir les paramètres de cycle de vie pour un package d’accès.

**Rôle prérequis :** Administrateur général, administrateur d’utilisateurs ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Cliquez sur **Stratégies** puis sur la stratégie qui contient les paramètres de cycle de vie que vous souhaitez modifier.

    Le volet Détails de la stratégie s’ouvre au bas de la page.

    ![Package d’accès - Volet des détails de la stratégie](./media/entitlement-management-shared/policy-details.png)

1. Cliquez sur **Modifier** pour modifier la stratégie.

    ![Package d'accès - Modifier la stratégie](./media/entitlement-management-shared/policy-edit.png)

1. Cliquez sur l’onglet **Cycle de vie** pour ouvrir les paramètres de cycle de vie.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Changer les paramètres de demande et d’approbation pour un package d’accès](entitlement-management-access-package-request-policy.md)