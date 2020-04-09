---
title: Modifier les paramètres de demande et d’approbation d’un package d’accès dans la gestion des droits d’utilisation Azure AD – Azure Active Directory
description: Découvrez comment modifier les paramètres de demande et d’approbation d’un package d’accès dans la gestion des droits d’utilisation Azure Active Directory.
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
ms.date: 03/30/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0eae4f53283c33f3d7372a606f8c0a3ac27d079
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655957"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Modifier les paramètres de demande et d’approbation d’un package d’accès dans la gestion des droits d’utilisation Azure AD

En tant que gestionnaire de package d’accès, vous pouvez modifier la liste des utilisateurs qui peuvent demander un package d’accès à tout moment en éditant la stratégie ou en ajoutant une nouvelle stratégie. Vous pouvez également modifier les paramètres d’approbation.

Cet article explique comment modifier les paramètres de requête et d’approbation d’un package d’accès existant.

## <a name="choose-between-one-or-multiple-polices"></a>Choisir entre une ou plusieurs stratégies

Pour choisir les personnes autorisées à demander un package d'accès, utilisez une stratégie. Lorsque vous créez un package d’accès, vous spécifiez le paramètre de requête et d’approbation qui crée une stratégie. La plupart des packages d’accès sont associés à une seule stratégie, mais un même package d’accès peut posséder plusieurs stratégies. Vous pouvez créer plusieurs stratégies pour un package d'accès si vous souhaitez permettre à différents groupes d'utilisateurs de se voir attribuer des affectations avec différents paramètres de requête et d’approbation. Par exemple, une stratégie unique ne permet pas d’affecter des utilisateurs internes et externes à un même package d'accès. Cependant, vous pouvez créer deux stratégies dans un même package d'accès : une pour les utilisateurs internes et une autre pour les utilisateurs externes. Si plusieurs stratégies s'appliquent à un utilisateur, celui-ci sera invité, au moment de sa requête, à sélectionner la stratégie à laquelle il souhaite être affecté. Le diagramme suivant montre un package d’accès avec deux stratégies.

![Plusieurs stratégies dans un package d’accès](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| Scénario | Nombre de stratégies |
| --- | --- |
| Je souhaite que tous les utilisateurs de mon annuaire aient les mêmes paramètres de demande et d’approbation pour un package d’accès | Une |
| Je souhaite que tous les utilisateurs de certaines organisations connectées puissent demander un package d’accès | Une |
| Je souhaite autoriser les utilisateurs de mon annuaire et également les utilisateurs en dehors de mon annuaire à demander un package d’accès | Multiple |
| Je souhaite spécifier différents paramètres d’approbation pour certains utilisateurs | Multiple |
| Je veux que certains utilisateurs accèdent aux affectations de packages d’accès qui expirent alors que d’autres utilisateurs peuvent étendre leur accès | Multiple |

Pour plus d’informations sur la logique de priorité utilisée lorsque plusieurs stratégies s’appliquent, consultez [Stratégies multiples](entitlement-management-troubleshoot.md#multiple-policies
).

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>Ouvrir une stratégie existante de paramètres de demande et d’approbation

Pour modifier les paramètres de demande et d’approbation d’un package d’accès, vous devez ouvrir la stratégie correspondante. Suivez ces étapes pour ouvrir les paramètres de requête et d’approbation pour un package d’accès.

**Rôle prérequis :** Administrateur général, administrateur d’utilisateurs ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Cliquez sur **Stratégies**, puis sélectionnez la stratégie à modifier.

    Le volet Détails de la stratégie s'ouvre au bas de la page.

    ![Package d’accès - Volet des détails de la stratégie](./media/entitlement-management-shared/policy-details.png)

1. Cliquez sur **Modifier** pour modifier la stratégie.

    ![Package d'accès - Modifier la stratégie](./media/entitlement-management-shared/policy-edit.png)

1. Cliquez sur l’onglet **Requêtes** pour ouvrir les paramètres de demande et d’approbation.

1. Suivez les étapes d’une des sections relatives aux requêtes suivantes.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>Ajouter une nouvelle stratégie de paramètres de demande et d’approbation

Si vous disposez d’un ensemble d’utilisateurs qui doivent avoir des paramètres de demande et d’approbation différents, vous devrez probablement créer une nouvelle stratégie. Suivez ces étapes pour commencer à ajouter une nouvelle stratégie à un package d’accès existant.

**Rôle prérequis :** Administrateur général, administrateur d’utilisateurs ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Cliquez sur **Stratégies**, puis sur **Ajouter une stratégie**.

1. Entrez un nom et une description pour la stratégie.

    ![Créer une stratégie avec un nom et une description](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Cliquez sur **Suivant** pour ouvrir l’onglet **Requêtes**.

1. Suivez les étapes d’une des sections relatives aux requêtes suivantes.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

Si vous modifiez une stratégie, cliquez sur **Mettre à jour**. Si vous ajoutez une nouvelle stratégie, cliquez sur **Créer**.

## <a name="next-steps"></a>Étapes suivantes

- [Changer les paramètres de cycle de vie pour un package d’accès](entitlement-management-access-package-lifecycle-policy.md)
- [Afficher les requêtes d’un package d’accès](entitlement-management-access-package-requests.md)