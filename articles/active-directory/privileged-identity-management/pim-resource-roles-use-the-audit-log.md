---
title: Privileged Identity Management pour les ressources Azure - Audit des ressources | Microsoft Docs
description: Explique comment obtenir une vue de toutes les activités de rôle pour une ressource donnée.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: b8fa7d5600c0de8a3319ea4de785281372959937
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---audit"></a>Privileged Identity Management - Rôles de ressource - Audit

L’audit d’une ressource vous donne une vue d’ensemble de l’activité des rôles pour la ressource. Vous pouvez filtrer les informations en choisissant une date prédéfinie ou une plage personnalisée.
![](media/azure-pim-resource-rbac/rbac-resource-audit.png)

L’audit d’une ressource fournit également un accès rapide aux détails de l’activité d’un utilisateur. Sous « Type d’audit », sélectionnez « Activer ». Cliquez sur « (activité) » pour afficher les actions de l’utilisateur sur les ressources Azure.
![](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>Mon audit

Mon audit vous offre une vue de l’activité du rôle personnel d’un utilisateur. Vous pouvez filtrer les informations en choisissant une date prédéfinie ou une plage personnalisée.
![](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Voir les activations et les activités des ressources Azure

Si vous avez besoin de connaître les actions d’un utilisateur spécifique effectuées sur les différentes ressources, vous pouvez consulter l’activité de la ressource Azure sur une période d’activation donnée (pour les utilisateurs éligibles). Commencez par sélectionner un utilisateur à partir de la vue des membres ou à partir de la liste des membres dans un rôle spécifique. Le résultat affiche une vue graphique des actions de l’utilisateur sur les ressources Azure classées par date ainsi que les activations de rôle récentes sur la même période de temps.

![](media/azure-pim-resource-rbac/rbac-user-details.png)

La sélection d’une activation de rôle affichera les détails de l’activation de rôle ainsi que l’activité de la ressource Azure correspondante survenue lorsque l’utilisateur était actif.

![](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

