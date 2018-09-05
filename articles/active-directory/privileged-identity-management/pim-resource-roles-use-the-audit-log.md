---
title: Afficher l’historique d’audit pour les rôles de ressources Azure dans PIM | Microsoft Docs
description: Découvrez comment afficher l’historique d’audit des rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: c0536423e9640f78149b612ec66b0a07cdcf24bb
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189309"
---
# <a name="view-audit-history-for-azure-resource-roles-in-pim"></a>Afficher l’historique d’audit pour les rôles de ressources Azure dans PIM

L’audit d’une ressource vous donne une vue d’ensemble de l’activité des rôles pour la ressource. Vous pouvez filtrer les informations en choisissant une date prédéfinie ou une plage personnalisée.
![Filtrer les informations](media/azure-pim-resource-rbac/rbac-resource-audit.png)

L’audit des ressources offre également un accès rapide aux détails de l’activité d’un utilisateur. Sous **Type d’audit**, sélectionnez **Activer**. Cliquez sur **(activité)** pour afficher les actions de cet utilisateur dans les ressources Azure.
![Détail de l’activité](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![Plus de détails sur l’activité](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="my-audit"></a>Mon audit

Mon audit vous offre une vue de l’activité du rôle personnel d’un utilisateur. Vous pouvez filtrer les informations en choisissant une date prédéfinie ou une plage personnalisée.
![Activité du rôle personnel](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Voir les activations et les activités des ressources Azure

Pour connaître les actions qu’un utilisateur en particulier a effectuées sur différentes ressources, vous pouvez consulter l’activité en matière de ressources Azure associée à une période d’activation donnée. Commencez par sélectionner un utilisateur dans l’affichage **Membres** ou dans la liste des membres ayant un certain rôle. Le résultat affiche une représentation graphique des actions de l’utilisateur dans les ressources Azure par date. Il montre également les dernières activations de rôle sur cette même période.

![Détails de l’utilisateur](media/azure-pim-resource-rbac/rbac-user-details.png)

Si vous sélectionnez une activation de rôle, vous pourrez voir les détails de l’activation de rôle ainsi que l’activité correspondante, en matière de ressources Azure, survenue lorsque l’utilisateur était actif.

![Sélectionner l’activation de rôle](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="next-steps"></a>Étapes suivantes

- [Afficher l’historique d’audit pour les rôles d’annuaire Azure AD dans PIM](pim-how-to-use-audit-log.md)
