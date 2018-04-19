---
title: Privileged Identity Management pour les ressources Azure - MFA | Microsoft Docs
description: Ce document décrit comment activer l’authentification multifacteur pour les ressources PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 8d1c05e7f61ed76c47613bfab7bb8afd9b66cbe7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---mfa"></a>Privileged Identity Management - Rôles de ressource - MFA

PIM pour les rôles de ressource Azure permet aux administrateurs de ressources et aux administrateurs d’identités de protéger l’infrastructure Azure critique avec l’appartenance limitée dans le temps et l’accès juste-à-temps. PIM permet également l’application facultative de l’authentification multifacteur (MFA) Azure dans deux scénarios distincts.

## <a name="require-mfa-to-activate"></a>Exiger l’activation de MFA

Les administrateurs de ressources peuvent exiger des membres éligibles d’un rôle qu’ils passent avec succès l’authentification multifacteur Azure pour pouvoir être activés. Ce processus garantit, avec une certitude raisonnable, que l’utilisateur demandant l’activation est bien celui qu’il prétend être. L’application de cette option permet de protéger les ressources critiques au cas où le compte d’utilisateur pourrait être compromis. 

Pour appliquer cette exigence, sélectionnez une ressource dans la liste des ressources managées. Dans le [tableau de bord de présentation](pim-resource-roles-overview-dashboards.md), sélectionnez un rôle dans la liste des rôles située en bas à droite de l’écran.

Vous pouvez également obtenir les paramètres de rôle dans les onglets « Rôles » ou « Paramètres de rôle » dans le menu de navigation gauche.

>[!Note]
>Si les options dans le menu de navigation gauche sont grisées et qu’une bannière indiquant « Vous avez des rôles éligibles qui peuvent être activés » apparaît en haut de la page, cela signifie vous n’êtes pas un administrateur actif et qu’une [activation](pim-resource-roles-activate-your-roles.md) est nécessaire avant de continuer.

![](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

Si vous voyez l’appartenance à un rôle, sélectionnez « Paramètres de rôle » dans la barre située en haut de l’écran pour ouvrir le « détail du paramètre de rôle ».

Cliquez sur le bouton **Modifier** en haut pour modifier les paramètres de rôle.

Dans la section sous **Activer**, cochez la case **Require Multi-Factor Authentication to activate** (Exiger une authentification multifacteur pour activer), puis cliquez sur Enregistrer.

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-mfa-on-assignment"></a>Exiger une authentification multifacteur lors de l’attribution

Dans certains cas, un administrateur de ressources peut souhaiter attribuer un rôle à un membre pour une courte durée (une journée par exemple), sans que les membres concernés ne demandent nécessairement une activation. Dans ce scénario, PIM ne peut pas appliquer l’authentification multifacteur lorsque le membre utilise son attribution de rôle, car il est déjà actif dans le rôle depuis le moment où il est attribué.

Pour s’assurer que l’administrateur de ressources fournisse l’attribution aux utilisateurs qui prétendent l’être, vous pouvez appliquer l’authentification multifacteur lors de l’attribution.

Dans l’écran de détails du même paramètre de rôle, cochez la case « Require Multi-Factor Authentication on assignment » (Exiger une authentification multifacteur lors de l’attribution).

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>Étapes suivantes

[Exiger une approbation pour activer](pim-resource-roles-approval-workflow.md)

[Utilisation du journal d’audit](pim-resource-roles-use-the-audit-log.md)



