---
title: Modifier les paramètres de cycle de vie d’un package d’accès dans la gestion des droits d’utilisation Azure AD – Azure Active Directory
description: Découvrez comment modifier les paramètres de cycle de vie d’un package d’accès dans la gestion des droits d’utilisation Azure Active Directory.
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
ms.openlocfilehash: 959d85f496a4a573a969bf736aba137d5b86154a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79230393"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Modifier les paramètres de cycle de vie d’un package d’accès dans la gestion des droits d’utilisation Azure AD

En tant que gestionnaire de package d’accès, vous pouvez modifier les paramètres de cycle de vie d’un package d’accès à tout moment en modifiant une stratégie existante. Si vous modifiez la date d'expiration d'une stratégie, la date d'expiration des demandes en attente d'approbation ou approuvées ne change pas.

Cet article explique comment modifier les paramètres de cycle de vie d’un package d’accès existant.

## <a name="open-lifecycle-settings"></a>Ouvrir les paramètres de cycle de vie

Pour modifier les paramètres de cycle de vie d’un package d’accès, vous devez ouvrir la stratégie correspondante. Suivez ces étapes pour ouvrir les paramètres de cycle de vie pour un package d’accès.

**Rôle prérequis :** Administrateur général, administrateur d’utilisateurs ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Cliquez sur **Stratégies** puis sur la stratégie qui contient les paramètres de cycle de vie que vous souhaitez modifier.

    Le volet Détails de la stratégie s'ouvre au bas de la page.

    ![Package d’accès - Volet des détails de la stratégie](./media/entitlement-management-shared/policy-details.png)

1. Cliquez sur **Modifier** pour modifier la stratégie.

    ![Package d'accès - Modifier la stratégie](./media/entitlement-management-shared/policy-edit.png)

1. Cliquez sur l’onglet **Cycle de vie** pour ouvrir les paramètres de cycle de vie.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Changer les paramètres de demande et d’approbation pour un package d’accès](entitlement-management-access-package-request-policy.md)