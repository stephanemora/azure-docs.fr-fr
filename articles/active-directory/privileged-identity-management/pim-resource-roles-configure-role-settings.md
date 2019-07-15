---
title: Configurer les paramètres des rôles de ressources Azure dans PIM - Azure Active Directory | Microsoft Docs
description: Découvrez comment configurer les paramètres des rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6da92ccd50707e676a1f5d583fe22ae8b3d8b73
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476317"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>Configurer les paramètres des rôles de ressources Azure dans PIM

Lorsque vous configurez les paramètres des rôles de ressources Azure, vous définissez les paramètres par défaut appliqués aux attributions de rôles de ressources Azure dans Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Utilisez les procédures suivantes pour configurer le flux de travail d’approbation et spécifier qui peut approuver ou refuser les demandes.

## <a name="open-role-settings"></a>Ouvrir les paramètres des rôles

Suivez ces étapes pour ouvrir les paramètres pour un rôle de ressource Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide d’un nom d’utilisateur qui est membre du rôle [Administrateur de rôle privilégié](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Ressources Azure**.

1. Cliquez sur la ressource que vous souhaitez gérer, telle qu’un abonnement ou un groupe d’administration.

    ![Page de ressources Azure qui répertorie les ressources pouvant être gérées](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Cliquez sur **Paramètres de rôle**.

    ![Page Paramètres de rôle répertoriant les rôles de ressources Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Cliquez sur le rôle dont vous souhaitez configurer les paramètres.

    ![Page Détails des paramètres de rôle répertoriant plusieurs paramètres d’affectation et d’activation](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Cliquez sur **Modifier** pour ouvrir le volet Paramètres de rôle.

    ![Modifier la page des paramètres de rôle avec des options permettant de mettre à jour les paramètres d’affectation et d’activation](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    Dans le volet Paramètres de rôle de chaque rôle, vous pouvez configurer plusieurs paramètres.

## <a name="assignment-duration"></a>Durée de l’attribution

Vous pouvez choisir entre deux options de durée d’attribution pour chaque type d’attribution (éligible et actif) lorsque vous configurez les paramètres d’un rôle. Ces options deviennent la durée maximale par défaut lorsqu’un membre est attribué au rôle dans PIM.

Vous pouvez choisir l’une de ces options de durée d’attribution **éligible** :

| | |
| --- | --- |
| **Autoriser une attribution éligible permanente** | Les administrateurs de ressources peuvent attribuer une appartenance éligible permanente. |
| **Faire expirer les attributions éligibles après** | Les administrateurs de ressources peuvent exiger que toutes les attributions éligibles aient une date de début et une date de fin spécifiées. |

Vous pouvez choisir l’une de ces options de durée d’attribution **active** :

| | |
| --- | --- |
| **Autoriser une attribution active permanente** | Les administrateurs de ressources peuvent attribuer une appartenance active permanente. |
| **Faire expirer les attributions actives après** | Les administrateurs de ressources peuvent exiger que toutes les attributions actives aient une date de début et une date de fin spécifiées. |

> [!NOTE] 
> Toutes les attributions qui ont une date de fin spécifiée peuvent être renouvelées par les administrateurs de ressources. De plus, les membres peuvent lancer des demandes en libre-service afin d’[étendre ou renouveler des attributions de rôles](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Exiger une authentification multifacteur

PIM permet l’application facultative de l’authentification multifacteur (MFA) Azure dans deux scénarios distincts.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Demander l'authentification multifacteur lors de l'attribution active

Dans certains cas, vous pouvez attribuer un membre à un rôle pour une courte durée (une journée par exemple). Il n’est alors pas nécessaire que les membre attribués demandent l’activation. Dans ce scénario, PIM ne peut pas appliquer l’authentification multifacteur lorsque le membre utilise son attribution de rôle, car il est déjà actif dans le rôle depuis le moment où il est attribué.

Pour garantir que l’administrateur de ressources qui réalise l’attribution est bien celui qu’il prétend être, vous pouvez appliquer l’authentification multifacteur lors de l’attribution active en cochant la case **Demander l'authentification multifacteur lors de l'attribution active**.

### <a name="require-multi-factor-authentication-on-activation"></a>Exiger Multi-Factor Authentication lors de l’activation

Vous pouvez exiger des membres éligibles d’un rôle qu’ils exécutent l’authentification multifacteur pour pouvoir effectuer l’activation. Ce processus garantit, avec une certitude raisonnable, que l’utilisateur demandant l’activation est bien celui qu’il prétend être. L’application de cette option permet de protéger les ressources critiques au cas où le compte d’utilisateur pourrait être compromis.

Pour exiger d’un membre éligible qu’il exécute l’authentification multifacteur avant l’activation, cochez la case **Exiger l’authentification multifacteur lors de l’activation**.

Pour plus d’informations, consultez [Authentification multifacteur (MFA) et PIM](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Durée maximum d'activation

Utilisez le curseur **Durée maximum d'activation** pour définir la durée maximale, en heures, pendant laquelle un rôle reste actif avant d’expirer. Cette valeur peut être comprise entre 1 et 24 heures.

## <a name="require-justification"></a>Demander une justification

Vous pouvez exiger des membres qu’ils entrent une justification lors de l’attribution active ou quand ils effectuent l’activation. Pour demander une justification, cochez la case **Demander une justification lors de l'affectation active** ou la case **Demander une justification lors de l’activation**.

## <a name="require-approval-to-activate"></a>Demander une approbation pour activation

Si vous souhaitez exiger une approbation pour activer un rôle, procédez comme suit.

1. Cochez la case **Exiger une approbation pour activer**.

1. Cliquez sur **Sélectionner des approbateurs** pour ouvrir le volet Sélectionner un membre ou un groupe.

    ![Sélectionnez un volet de membre ou de groupe pour sélectionner les approbateurs.](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Sélectionnez au moins un membre ou un groupe, puis cliquez sur **Sélectionner**. Vous pouvez ajouter n’importe quelle combinaison de membres et de groupes. Vous devez sélectionner au moins un approbateur. Il n’existe aucun approbateur par défaut.

    Vos sélections figurent dans la liste des approbateurs sélectionnés.

1. Une fois que vous avez spécifié tous vos paramètres de rôle, cliquez sur **Mettre à jour** pour enregistrer vos modifications.

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles de ressources Azure dans PIM](pim-resource-roles-assign-roles.md)
- [Configurer des alertes de sécurité pour les rôles de ressources Azure dans PIM](pim-resource-roles-configure-alerts.md)
