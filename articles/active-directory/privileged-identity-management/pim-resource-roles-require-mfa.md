---
title: Appliquer Azure Multi-Factor Authentication dans les ressources Azure avec Privileged Identity Management | Microsoft Docs
description: Ce document décrit comment activer l’authentification multifacteur pour les ressources PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 0c50a17b651fc1fb5d49915da5da4a37d40a0d3e
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260061"
---
# <a name="enforce-azure-multi-factor-authentication-in-azure-resources-by-using-privileged-identity-management"></a>Appliquer Azure Multi-Factor Authentication dans les ressources Azure avec Privileged Identity Management

Privileged Identity Management (PIM) pour les rôles de ressources Azure permet aux administrateurs de ressources et aux administrateurs d’identités de protéger l’infrastructure Azure critique avec une appartenance limitée dans le temps et un accès juste-à-temps. PIM permet également l’application facultative d’Azure Multi-Factor Authentication dans deux scénarios distincts.

## <a name="require-multi-factor-authentication-to-activate"></a>Exiger Multi-Factor Authentication pour l’activation

Les administrateurs de ressources peuvent exiger des membres éligibles d’un rôle qu’ils lancent Azure Multi-Factor Authentication pour pouvoir s’activer. Ce processus garantit, avec une certitude raisonnable, que l’utilisateur demandant l’activation est bien celui qu’il prétend être. L’application de cette option permet de protéger les ressources critiques au cas où le compte d’utilisateur pourrait être compromis. 

Pour appliquer cette exigence, sélectionnez une ressource dans la liste des ressources managées. Dans le [tableau de bord de présentation](pim-resource-roles-overview-dashboards.md), sélectionnez un rôle dans la liste située en bas à droite de l’écran.

Vous pouvez également accéder aux paramètres de rôle dans les onglets **Rôles** et **Paramètres de rôle** du volet de gauche.

>[!Note]
>Si les options du volet de gauche sont grisées et qu’une bannière indiquant « Vous avez des rôles éligibles qui peuvent être activés » apparaît en haut de la page, cela signifie vous n’êtes pas un administrateur actif et qu’une [activation](pim-resource-roles-activate-your-roles.md) est nécessaire pour pouvoir continuer.

![Onglets « Rôles » et « Paramètres de rôle » ](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

Pour afficher l’appartenance à un rôle, sélectionnez **Paramètres de rôle** dans la barre située en haut de l’écran afin d’ouvrir **Détail des paramètres de rôle**.

Pour modifier les paramètres de rôle, sélectionnez le bouton **Modifier** en haut.

Dans la section sous **Activer**, cochez la case **Exiger Multi-Factor Authentication lors de l’activation**. Ensuite, sélectionnez **Enregistrer**.

![Exiger Multi-Factor Authentication lors de l’activation](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-multi-factor-authentication-on-assignment"></a>Exiger Multi-Factor Authentication lors de l’attribution

Dans certains cas, l’administrateur de ressources souhaite attribuer un rôle à un membre pour une courte durée (une journée par exemple). Il n’est pas nécessaire que ce membre demande l’activation. Dans ce scénario, PIM ne peut pas appliquer Multi-Factor Authentication lorsque le membre utilise son attribution de rôle, car il est déjà actif dans le rôle depuis son attribution.

Pour garantir que l’administrateur de ressources qui réalise l’attribution est celui qu’il qui prétend être, vous pouvez appliquer Multi-Factor Authentication lors de l’attribution.

Sur le même écran de détails des paramètres de rôle, cochez la case **Exiger Multi-Factor Authentication lors de l’attribution directe**.

![Exiger Multi-Factor Authentication lors de l’attribution directe](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>Étapes suivantes

[Exiger une approbation pour activer](pim-resource-roles-approval-workflow.md)

[Utilisation du journal d’audit](pim-resource-roles-use-the-audit-log.md)



