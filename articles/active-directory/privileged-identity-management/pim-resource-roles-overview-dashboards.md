---
title: Tableaux de bord des ressources pour les révisions d’accès dans PIM - Azure AD | Microsoft Docs
description: Explique comment utiliser un tableau de bord de ressources pour effectuer une révision d’accès dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6affa2ecc8919dabeb6173622b525280ce96bcfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847025"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Utiliser un tableau de bord de ressources pour effectuer une révision d’accès dans Privileged Identity Management

Vous pouvez utiliser un tableau de bord de ressources pour effectuer une révision d’accès dans Privileged Identity Management (PIM). Le tableau de bord Vue Administrateur d’Azure Active Directory (Azure AD) comporte trois composants principaux :

- Une représentation graphique des activations de rôle des ressources
- Graphiques montrant la distribution des attributions de rôle par type d’attribution
- Une zone de données contenant des informations sur les nouvelles attributions de rôles

![Capture d’écran du tableau de bord Vue Administration montrant les graphiques](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Capture d’écran du tableau de bord Vue Administration montrant les listes de données](media/pim-resource-roles-overview-dashboards/role-settings.png)

La représentation graphique des activations de rôle des ressources couvre les sept derniers jours. Ces données, limitées à la ressource sélectionnée, présentent les activations des rôles les plus courants (propriétaire, contributeur, administrateur de l’accès utilisateur) et tous rôles confondus.

Sur un côté du graphique des activations, deux graphiques montrent la distribution des attributions de rôle par type d’attribution, pour les utilisateurs et les groupes. Vous pouvez passer des valeurs aux pourcentages (et vice versa) en sélectionnant une section du graphique.

Sous les graphiques apparaissent le nombre d’utilisateurs et de groupes ayant reçu de nouvelles attributions de rôle au cours des 30 derniers jours ainsi que des rôles triés par nombre total d’attributions dans l’ordre décroissant.

## <a name="next-steps"></a>Étapes suivantes

- [Commencer une révision d’accès des rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-start-access-review.md)
