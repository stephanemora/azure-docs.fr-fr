---
title: Privileged Identity Management pour les ressources Azure - Activer des rôles | Microsoft Docs
description: Décrit comment activer des rôles dans PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 3e5456e7a632639cb82d7ba2b2e073938b1798ef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---activate"></a>Privileged Identity Management - Rôles de ressource - Activer
L’activation de rôles pour les ressources Azure introduit une nouvelle expérience permettant aux membres du rôle éligible de planifier une activation à une date/heure future et de sélectionner une durée d’activation spécifique au sein d’un délai maximal (configuré par les administrateurs). En savoir plus sur [l’activation de rôles de Azure AD](../active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="activate-roles"></a>Activer des rôles
Accédez à la section Mes rôles dans la barre de navigation gauche. Cliquez sur « Activer » en regard du rôle que vous souhaitez activer.
![](media/azure-pim-resource-rbac/rbac-roles.png)

Dans le menu Activations, entrez la date et l’heure de début souhaitées pour l’activation du rôle. Si vous le souhaitez, réduisez la durée d’activation (durée pendant laquelle le rôle est actif), puis indiquez une justification si nécessaire ; cliquez sur Activer.

Si la date et l’heure de début ne sont pas modifiées, le rôle sera activé dans les secondes qui suivent. Vous verrez un message en bannière indiquant un rôle en attente d’activation dans la page Mes rôles. Cliquez sur le bouton de rafraîchissement pour effacer ce message.

![](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Si l’activation est planifiée pendant une date future, la demande en attente s’affichera dans l’onglet Demandes en attente du menu de navigation gauche. Dans le cas où l’activation du rôle ne serait plus nécessaire, l’utilisateur peut annuler la demande en cliquant sur le bouton Annuler sur le côté droit de la page.

![](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="just-enough-administration"></a>Just Enough Administration (JEA)

L’utilisation des meilleures pratiques de JEA pour vos attributions de rôles de ressource est simple avec PIM pour les ressources Azure. Les utilisateurs et les membres de groupe disposant d’attributions dans des abonnements Azure ou des groupes de ressources peuvent activer leur attribution de rôle existante en lui donnant une portée limitée. 

À partir de la page de recherche, recherchez la ressource secondaire que vous devez gérer.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Sélectionnez Mes rôles à partir du menu de navigation de gauche et choisissez le rôle approprié à activer. Notez que le type d’affectation est Héritée, étant donné que le rôle a été attribué à l’abonnement plutôt qu’au groupe de ressources, comme indiqué ci-dessous.

![](media/azure-pim-resource-rbac/my-roles-02.png)
