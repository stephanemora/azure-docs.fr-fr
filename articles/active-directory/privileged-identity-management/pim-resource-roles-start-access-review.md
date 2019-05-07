---
title: Créer une révision d’accès des rôles de ressources Azure dans PIM - Azure Active Directory | Microsoft Docs
description: Découvrez comment créer une révision d’accès des rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/29/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78e4de5bbc56f95c0e903b1dac4e8481373716f3
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143506"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-pim"></a>Créer une révision d’accès des rôles de ressources Azure dans PIM

Accès aux rôles de ressource Azure privilégiée pour les employés changent au fil du temps. Pour réduire les risques associés aux attributions de rôles obsolètes, vous devez vérifier régulièrement les accès. Vous pouvez utiliser Azure Active Directory (Azure AD) Privileged Identity Management (PIM) pour créer des révisions d’accès à privileged des rôles de ressources Azure. Vous pouvez également configurer les révisions d’accès périodique qui se produisent automatiquement.

Cet article décrit comment créer un ou plusieurs révisions d’accès pour les rôles de ressource Azure privilégiée.

## <a name="prerequisites"></a>Conditions préalables

- [Administrateur de rôle privilégié](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Révisions d’accès ouvert

1. Connectez-vous à [Azure portal](https://portal.azure.com/) avec un utilisateur qui est membre du rôle administrateur de rôle privilégié.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Dans le menu de gauche, cliquez sur **ressources Azure**.

1. Cliquez sur la ressource que vous souhaitez gérer, telle qu’un abonnement ou un groupe d’administration.

1. Sous gérer, cliquez sur **révisions d’accès**.

    ![Révisions d’accès des ressources Azure-](./media/pim-resource-roles-start-access-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>Démarrer la révision d’accès

Une fois que vous avez spécifié les paramètres pour une révision d’accès, cliquez sur **Démarrer**. La révision d’accès s’affiche dans votre liste avec un indicateur de son état.

![Liste des révisions d’accès](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Par défaut, Azure AD envoie un e-mail aux réviseurs peu de temps après le démarrage de la révision. Si vous ne souhaitez pas qu’Azure AD envoie cet e-mail, veillez à informer les réviseurs qu’une révision d’accès leur a été assignée. Vous pouvez leur montrer les instructions pour savoir comment [réviser l’accès aux rôles de ressources Azure](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Gestion de la révision d’accès

Vous pouvez suivre la progression à mesure que les réviseurs effectuent des révisions sur le **vue d’ensemble** page de la révision d’accès. Aucun droit d’accès n’est modifiées dans le répertoire avant que le [révision est terminée](pim-resource-roles-complete-access-review.md).

![Progression des révisions d’accès](./media/pim-resource-roles-start-access-review/access-review-overview.png)

S’il s’agit d’une révision à usage unique, puis une fois la période de révision d’accès ou l’administrateur interrompt la révision d’accès, suivez les étapes de [effectuer une révision d’accès des rôles de ressources Azure](pim-resource-roles-complete-access-review.md) pour voir et appliquer les résultats.  

Pour gérer une série d’accès révisions, accédez à la révision d’accès, et vous trouver des occurrences à venir dans les révisions planifiée et modifier la date de fin ou ajouter/supprimer des réviseurs en conséquence.

Selon vos sélections dans **paramètres de saisie semi-automatique**, appliquer automatiquement les va être exécutée après la date de fin de la révision ou lorsque vous arrêtez manuellement la révision. L’état de la révision ne pourra **terminé** par le biais des états intermédiaires comme **application** et enfin à l’état **appliqué**. Vous devriez voir les utilisateurs non autorisés, cas échéant, en cours de suppression de rôles dans quelques minutes.

## <a name="next-steps"></a>Étapes suivantes

- [Réviser l’accès aux rôles de ressources Azure](pim-resource-roles-perform-access-review.md)
- [Effectuer une révision d’accès des rôles de ressources Azure](pim-resource-roles-complete-access-review.md)
- [Créer une révision d’accès des rôles Azure AD](pim-how-to-start-security-review.md)
