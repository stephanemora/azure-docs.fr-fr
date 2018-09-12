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
ms.openlocfilehash: fb52bc92c86261831d0e8d8e9e863a4863fe8fb9
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666887"
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

## <a name="azure-resource-role-approval-workflow"></a>Workflow d’approbation de rôle de ressource Azure

Le workflow d’approbation dans PIM pour les rôles de ressources Azure permet aux administrateurs de protéger ou restreindre davantage l’accès aux ressources critiques. Autrement dit, les administrateurs peuvent exiger une approbation pour activer des attributions de rôles.

Le concept de hiérarchie des ressources est propre aux rôles de ressources Azure. Cette hiérarchie permet aux ressources enfants au sein du conteneur parent d’hériter des attributions de rôle d’un objet de ressource parent. 

Considérez l’exemple suivant : Bob, administrateur des ressources, utilise PIM pour affecter Alice en tant que membre éligible au rôle de propriétaire dans l’abonnement Contoso. Avec cette attribution, Alice est propriétaire éligible de tous les conteneurs de groupes de ressources au sein de l’abonnement Contoso. Elle est également propriétaire éligible de toutes les ressources (telles que les machines virtuelles) au sein de chaque groupe de ressources de l’abonnement.

Supposez que l’abonnement Contoso comprenne trois groupes de ressources : Fabrikam Test, Fabrikam Dev et Fabrikam Prod. Chacun de ces groupes de ressources contient une seule machine virtuelle.

Les paramètres de PIM sont configurés pour chaque rôle d’une ressource. Contrairement aux attributions, ces paramètres ne sont pas hérités et s’appliquent uniquement au rôle de ressource.

Bob utilise PIM pour exiger de la part de tous les membres du rôle de propriétaire de l’abonnement Contoso une demande d’approbation pour l’activation. Pour aider à protéger les ressources contenues dans le groupe de ressources Fabrikam Prod, Bob exige également une approbation pour les membres du rôle de propriétaire de cette ressource. Les rôles de propriétaire de Fabrikam Test et Fabrikam Dev ne nécessitent pas d’approbation pour l’activation.

Quand Alice demande l’activation de son rôle de propriétaire pour l’abonnement Contoso, un approbateur doit approuver ou refuser sa demande pour qu’elle devienne active dans le rôle. Si Alice décide d’[étendre son activation](pim-resource-roles-activate-your-roles.md) au groupe de ressources Fabrikam Prod, un approbateur doit également approuver ou refuser cette demande. En revanche, si Alice décide d’étendre son activation à Fabrikam Test ou Fabrikam Dev, l’approbation n’est pas requise.

Il se peut que le flux de travail d’approbation ne soit pas nécessaire pour tous les membres d’un rôle. Envisagez un scénario dans lequel votre société embauche plusieurs associés sous contrat pour aider au développement d’une application qui s’exécute dans un abonnement Azure. En tant qu’administrateur de la ressource, vous souhaitez que les employés bénéficient d’un accès éligible, sans approbation, et que l’associé sous contrat ait besoin de demander une approbation. Pour configurer le flux de travail d’approbation pour les associés sous contrat uniquement, vous pouvez créer un rôle personnalisé avec les mêmes autorisations que le rôle affecté aux employés. Vous pouvez demander l’approbation pour activer ce rôle personnalisé. [En savoir plus sur les rôles personnalisés](pim-resource-roles-custom-role-policy.md).

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles de ressources Azure dans PIM](pim-resource-roles-assign-roles.md)
