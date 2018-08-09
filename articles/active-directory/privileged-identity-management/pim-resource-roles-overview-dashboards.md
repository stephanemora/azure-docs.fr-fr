---
title: 'Vue d’ensemble : Effectuer une révision d’accès dans Privileged Identity Management pour les ressources Azure | Microsoft Docs'
description: Ce document explique comment effectuer une révision d’accès dans PIM pour les ressources Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 40fe6a4b0bec2540afee635b16ee3482a182a2df
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621486"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review"></a>Utiliser un tableau de bord de ressources pour effectuer une révision d’accès

Il est possible d’utiliser un tableau de bord de ressources pour effectuer une révision d’accès dans Privileged Identity Management (PIM) pour les ressources Azure. Le tableau de bord Vue Administration se compose de trois éléments principaux :

- une représentation graphique des activations de rôle des ressources ;
- deux graphiques montrant la distribution des attributions de rôle par type d’attribution ;
- une zone de données se rapportant aux nouvelles attributions de rôle.

![Capture d’écran du tableau de bord Vue Administration montrant les graphiques](media/azure-pim-resource-rbac/rbac-overview-top.png)

![Capture d’écran du tableau de bord Vue Administration montrant les listes de données](media/azure-pim-resource-rbac/role-settings.png)

La représentation graphique des activations de rôle des ressources couvre les sept derniers jours. Ces données, limitées à la ressource sélectionnée, présentent les activations des rôles les plus courants (propriétaire, contributeur, administrateur de l’accès utilisateur) et tous rôles confondus.

À droite du graphique des activations, deux graphiques montrent la distribution des attributions de rôle par type d’attribution, pour les utilisateurs et les groupes. Vous pouvez passer des valeurs aux pourcentages (et vice versa) en sélectionnant une section du graphique.

Sous les graphiques apparaissent le nombre d’utilisateurs et de groupes ayant reçu de nouvelles attributions de rôle au cours des 30 derniers jours ainsi que la liste des rôles triée par nombre total d’attributions (dans l’ordre décroissant).


