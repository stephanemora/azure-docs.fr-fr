---
title: Utiliser un tableau de bord de ressources pour effectuer une révision d’accès dans PIM - Azure Active Directory | Microsoft Docs
description: Explique comment utiliser un tableau de bord de ressources pour effectuer une révision d’accès dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e759ba47c16617aa1783ce6fb0e324aa62ee96d
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804119"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>Utiliser un tableau de bord de ressources pour effectuer une révision d’accès dans PIM

Vous pouvez utiliser un tableau de bord de ressources pour effectuer une révision d’accès dans Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Le tableau de bord Vue Administration se compose de trois éléments principaux :

- une représentation graphique des activations de rôle des ressources ;
- deux graphiques montrant la distribution des attributions de rôle par type d’attribution ;
- une zone de données se rapportant aux nouvelles attributions de rôle.

![Capture d’écran du tableau de bord Vue Administration montrant les graphiques](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Capture d’écran du tableau de bord Vue Administration montrant les listes de données](media/pim-resource-roles-overview-dashboards/role-settings.png)

La représentation graphique des activations de rôle des ressources couvre les sept derniers jours. Ces données, limitées à la ressource sélectionnée, présentent les activations des rôles les plus courants (propriétaire, contributeur, administrateur de l’accès utilisateur) et tous rôles confondus.

À droite du graphique des activations, deux graphiques montrent la distribution des attributions de rôle par type d’attribution, pour les utilisateurs et les groupes. Vous pouvez passer des valeurs aux pourcentages (et vice versa) en sélectionnant une section du graphique.

Sous les graphiques apparaissent le nombre d’utilisateurs et de groupes ayant reçu de nouvelles attributions de rôle au cours des 30 derniers jours ainsi que la liste des rôles triée par nombre total d’attributions (dans l’ordre décroissant).

## <a name="next-steps"></a>Étapes suivantes

- [Démarrer une révision d’accès des rôles de ressources Azure dans PIM](pim-resource-roles-start-access-review.md) 
