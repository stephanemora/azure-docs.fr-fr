---
title: 'Vue d’ensemble : Effectuer une révision d’accès dans Privileged Identity Management pour les ressources Azure | Microsoft Docs'
description: Ce document explique comment effectuer une révision d’accès dans PIM pour les ressources Azure.
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
ms.date: 03/30/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 61be9873cac462c096599680a6e071e104f3a54c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33200618"
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


