---
title: Activer des rôles pour des ressources Azure à l’aide de Privileged Identity Management | Microsoft Docs
description: Décrit comment activer des rôles dans PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: d2f61f1ebdd473a24115c7774801f5b7694f224f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443203"
---
# <a name="activate-roles-for-azure-resources-by-using-privileged-identity-management"></a>Activer des rôles pour des ressources Azure à l’aide de Privileged Identity Management
PIM (Privileged Identity Management) introduit une nouvelle expérience d’activation des rôles pour les ressources Azure. Les membres de rôles éligibles peuvent planifier l’activation à une date et une heure ultérieures. Ils peuvent également sélectionner une durée d’activation spécifique (à condition qu’elle ne dépasse pas la durée maximale configurée par les administrateurs). Pour plus d’informations, consultez [Guide pratique pour activer ou désactiver des rôles dans Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="activate-roles"></a>Activer des rôles
Accédez à la section **Mes rôles** dans le volet gauche. Sélectionnez **Activer** pour le rôle que vous souhaitez activer.

![Onglet « Rôles éligibles » dans le volet « Mes rôles ».](media/azure-pim-resource-rbac/rbac-roles.png)

Dans le menu **Activations**, entrez la date et l’heure de début souhaitées pour l’activation du rôle. Si vous le souhaitez, réduisez la durée d’activation (durée pendant laquelle le rôle est actif) et indiquez une justification si nécessaire. Ensuite, sélectionnez **Activer**.

Si la date et l’heure de début ne sont pas modifiées, le rôle est activé dans les secondes qui suivent. Dans le volet **Mes rôles**, un message de bannière indique qu’un rôle est en file d’attente pour l’activation. Sélectionnez le bouton d’actualisation pour effacer ce message.

![Volet « Mes rôles » avec un message de bannière et une notification concernant une approbation en attente](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Si l’activation est planifiée à une date ultérieure, la demande en attente apparaît sous l’onglet **Demandes en attente** du volet gauche. Si l’activation de rôle n’est plus nécessaire, vous pouvez annuler la demande en sélectionnant le bouton **Annuler**.

![Liste de demandes en attente avec boutons « Annuler »](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="apply-just-enough-administration-practices"></a>Appliquer des pratiques JEA (Just Enough Administration)

L’application de bonnes pratiques JEA pour vos attributions de rôles de ressources est simple avec PIM pour les ressources Azure. Les utilisateurs et les membres de groupes disposant d’attributions dans des abonnements Azure ou des groupes de ressources peuvent activer leur attribution de rôle existante en lui donnant une étendue limitée. 

À partir de la page de recherche, recherchez la ressource secondaire que vous devez gérer.

![Sélection d’une ressource](media/azure-pim-resource-rbac/azure-resources-02.png)

Sélectionnez **Mes rôles** dans le volet gauche et choisissez le rôle à activer. Le type d’attribution est **Héritée**, car le rôle a été attribué à l’abonnement plutôt qu’au groupe de ressources.

![Liste des attributions de rôles éligibles, avec le type d’attribution mis en surbrillance](media/azure-pim-resource-rbac/my-roles-02.png)
