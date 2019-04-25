---
title: Démarrer une révision d’accès pour les rôles de ressources Azure dans PIM - Azure Active Directory | Microsoft Docs
description: Découvrez comment démarrer une révision d’accès des rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
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
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46903967b375d882dc3c7a62cd0b7f8b6059f8b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60287036"
---
# <a name="start-an-access-review-for-azure-resource-roles-in-pim"></a>Démarrer une révision d’accès des rôles de ressources Azure dans PIM
Les attributions de rôles deviennent « obsolètes » lorsque les utilisateurs bénéficient d’un accès privilégié dont ils n’ont plus besoin. Pour réduire les risques associés à ces affectations de rôles obsolètes, les administrateurs de rôle privilégié doivent régulièrement réviser les rôles. Ce document décrit les étapes pour démarrer une révision d’accès dans Azure Active Directory (Azure AD) Privileged Identity Management (PIM).

Dans la page principale de l’application PIM, accédez à :

* **Révisions d’accès** > **Ajouter**

![Ajouter des révisions d’accès](media/azure-pim-resource-rbac/rbac-access-review-home.png)

Quand vous sélectionnez le bouton **Ajouter**, le panneau **Créer une révision d’accès** s’affiche. Dans ce panneau, configurez la révision avec un nom et une limite de temps, choisissez un rôle à réviser, puis nommez la personne qui effectue la révision.

![Créer une révision d’accès](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>Configuration de la révision
Pour créer une révision d’accès, commencez par la nommer, puis définissez une date de début et de fin.

![Configuration d’une révision - capture d’écran](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Prévoyez une période suffisamment longue pour permettre aux utilisateurs de terminer la révision. Si vous avez terminé avant la date de fin, ils peuvent toujours arrêter la révision plus tôt.

### <a name="choose-a-role-to-review"></a>Sélection d’un rôle à réviser
Chaque révision se concentre sur un seul rôle. À moins d’avoir démarré la révision d’accès à partir d’un panneau de rôle spécifique, vous devez maintenant choisir un rôle.

1. Accédez à **Réviser l’appartenance au rôle**.
   
    ![Réviser une appartenance à un rôle - capture d’écran](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. Choisissez un rôle dans la liste.

### <a name="decide-who-will-perform-the-review"></a>Désignez la personne qui effectuera la révision
Il existe trois options pour effectuer une révision. Vous pouvez affecter la révision à quelqu’un d’autre, vous pouvez la faire vous-même ou chaque utilisateur peut réviser son propre accès.

1. Choisissez l'une des options :
   
   * **Utilisateurs sélectionnés** : utilisez cette option lorsque vous ne savez pas qui a besoin de l’accès. Avec cette option, vous pouvez affecter la révision à un propriétaire de ressource ou un responsable de groupe.
   * **Affecté (auto)**  : utilisez cette option pour demander aux utilisateurs de réviser leurs propres attributions de rôles.
   
2. Accédez à **Sélectionner des réviseurs**.
   
    ![Sélection des réviseurs - capture d’écran](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>Démarrage d’une révision
Enfin, vous pouvez obliger les utilisateurs à indiquer le motif pour lequel ils approuvent l’accès. Ajoutez une description de la révision, si vous le souhaitez. Sélectionnez ensuite **Démarrer**.

Prenez soin d’informer vos utilisateurs qu’une révision d’accès les attend, puis montrez-leur [comment exécuter une révision d’accès](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Gestion de la révision d’accès
Dans le tableau de bord des ressources Azure PIM, vous pouvez suivre la progression des révisions effectuées par les réviseurs. Aucun droit d’accès n’est modifié dans le répertoire jusqu’à ce que [la révision soit terminée](pim-resource-roles-complete-access-review.md).

Tant que la période de révision n’est pas terminée, vous pouvez rappeler aux utilisateurs d’effectuer leur révision, ou arrêter la révision au début de la section des révisions d’accès.

## <a name="next-steps"></a>Étapes suivantes

- [Terminer une révision d’accès des rôles de ressources Azure dans PIM](pim-resource-roles-complete-access-review.md)
- [Effectuer une révision d’accès des rôles de ressources Azure dans PIM](pim-resource-roles-perform-access-review.md)
- [Démarrer une révision d’accès pour les rôles Azure AD dans PIM](pim-how-to-start-security-review.md)
