---
title: 'Privileged Identity Management pour ressources Azure : attributions de membres éligibles et visibilité des ressources | Microsoft Docs'
description: Décrit comment attribuer les membres en tant que membres éligibles aux rôles de ressources.
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
ms.openlocfilehash: 273b06c91d68a764fe814374c0eca6ed1698cc2e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="eligible-assignments-and-resource-visibility"></a>Attributions de membres éligibles et visibilité des ressources

PIM pour les rôles de ressources Azure accroît la sécurité des organisations possédant des ressources Azure critiques. PIM permet aux administrateurs des ressources d’attribuer des membres aux rôles de ressources comme étant éligibles. Lisez ce qui suit pour en apprendre davantage sur les différents types d’attribution et les états des rôles de ressources Azure. 

## <a name="assignment-types"></a>Types d’attributions

PIM pour les ressources Azure fournit deux types distincts d’attribution :

- Éligible
- Actif

Les attributions de membres éligibles exigent des membres qu’ils effectuent une action pour utiliser ce rôle. Il peut s’agir de procéder à une vérification de l’authentification multifacteur, de fournir une justification et de demander une approbation auprès des approbateurs désignés.

Les attributions de membres actifs n’exigent pas des membres qu’ils effectuent une action pour utiliser ce rôle. Les membres attribués comme étant actifs possèdent à tout moment les privilèges fournis par le rôle.

## <a name="assignment-duration"></a>Durée de l’attribution

Au moment de la configuration des paramètres de PIM sur un rôle, les administrateurs des ressources peuvent choisir l’une de ces deux options pour chaque type d’attribution. Ces options deviennent la durée maximale par défaut lorsqu’un membre est attribué au rôle dans PIM.

- Autoriser une attribution éligible permanente
- Autoriser une attribution active permanente

or

- Faire expirer les attributions éligibles après
- Faire expirer les attributions actives après

Si un administrateur de la ressource choisit d’autoriser l’attribution éligible permanente et/ou d’autoriser l’attribution active permanente, tous les administrateurs qui attribuent des membres à la ressource auront la possibilité de définir des appartenances permanentes.

En choisissant de faire expirer les attributions éligibles après et/ou de faire expirer les attributions actives après, vous contrôlez le cycle de vie de l’attribution en imposant que toutes les attributions disposent d’une date de début et d’une date de fin.

>[!NOTE] 
>Toutes les attributions avec une date de fin spécifiée peuvent être renouvelées par les administrateurs de la ressource. De plus, les membres peuvent initier des demandes en libre-service pour [étendre ou renouveler les attributions](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>États d’attribution

PIM pour les ressources Azure possède deux états d’attribution distincts qui s’affichent sous l’onglet Rôles actifs dans les écrans Mes rôles, Rôles et Membres de PIM. Ces états sont :

- Attribué
- Activé

Au moment d’afficher une appartenance répertoriée dans Rôles actifs, la colonne ÉTAT vous permet de faire la distinction entre les utilisateurs qui sont « attribués » comme étant « actifs » et ceux qui ont activé une attribution éligible et sont maintenant actifs.

## <a name="next-steps"></a>Étapes suivantes

[Attribuer des rôles dans PIM](pim-resource-roles-assign-roles.md)

