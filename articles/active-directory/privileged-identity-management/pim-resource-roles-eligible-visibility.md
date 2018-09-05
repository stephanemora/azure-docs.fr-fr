---
title: Attributions de rôles éligibles et visibilité des ressources dans PIM - Azure | Microsoft Docs
description: Découvrez comment attribuer des rôles éligibles pour des ressources Azure dans Azure AD Privileged Identity Management (PIM).
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
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 3551c3231a94f8a844d26a713cbf171ca7653815
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189212"
---
# <a name="eligible-assignments-and-resource-visibility-in-pim"></a>Attributions de rôles éligibles et visibilité des ressources dans PIM

PIM (Privileged Identity Management) pour les rôles de ressources Azure renforce la sécurité des organisations ayant des ressources Azure critiques. Les administrateurs de ressources peuvent utiliser PIM pour attribuer des membres en tant que membres éligibles pour des rôles de ressources. Lisez les sections qui suivent pour en apprendre davantage sur les différents types et états d’attribution des rôles de ressources Azure. 

## <a name="assignment-types"></a>Types d’attributions

PIM pour les ressources Azure fournit deux types distincts d’attribution :

- Éligible
- Actif

Les attributions de membres éligibles exigent des membres qu’ils effectuent une action pour utiliser ce rôle. Il peut s’agir de procéder à une vérification de l’authentification multifacteur, de fournir une justification professionnelle ou de demander une approbation aux approbateurs désignés.

Les attributions de membres actifs n’exigent pas des membres qu’ils effectuent une action pour utiliser ce rôle. Les membres attribués comme étant actifs détiennent à tout moment les privilèges affectés au rôle.

## <a name="assignment-duration"></a>Durée de l’attribution

Au moment de la configuration des paramètres de PIM pour un rôle, les administrateurs des ressources peuvent choisir parmi deux options possibles pour chaque type d’attribution. Ces options deviennent la durée maximale par défaut lorsqu’un membre est attribué au rôle dans PIM. 

Un administrateur peut choisir l’un de ces types d’attribution :

- Autoriser une attribution éligible permanente
- Autoriser une attribution active permanente

Un administrateur peut également choisir l’un de ces types d’attribution :

- Faire expirer les attributions éligibles après
- Faire expirer les attributions actives après

Si un administrateur de ressources choisit **Autoriser une attribution éligible permanente** ou **Autoriser une attribution active permanente**, tous les administrateurs qui attribuent des membres à la ressource peuvent définir des appartenances permanentes.

Si un administrateur de ressources choisit **Faire expirer les attributions éligibles après** ou **Faire expirer les attributions actives après**, il contrôle le cycle de vie de l’attribution en exigeant que toutes les attributions aient une date de début et une date de fin spécifiées.

> [!NOTE] 
> Toutes les attributions qui ont une date de fin spécifiée peuvent être renouvelées par les administrateurs de ressources. De plus, les membres peuvent lancer des demandes en libre-service afin d’[étendre ou renouveler des attributions](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>États d’attribution

PIM pour les ressources Azure a deux états d’attribution distincts qui s’affichent sous l’onglet **Rôles actifs** dans les vues **Mes rôles**, **Rôles** et **Membres** de PIM. Ces états sont :

- Attribué
- Activé

Quand vous visualisez une appartenance listée dans **Rôles actifs**, vous pouvez utiliser la valeur indiquée dans la colonne **ÉTAT** pour faire la distinction entre les utilisateurs qui sont **Attribués** comme étant actifs et ceux qui ont **Activé** une attribution éligible et sont maintenant actifs.

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles de ressources Azure dans PIM](pim-resource-roles-assign-roles.md)
