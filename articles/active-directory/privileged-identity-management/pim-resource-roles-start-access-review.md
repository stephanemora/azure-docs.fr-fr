---
title: Démarrer une révision d’accès dans PIM pour les ressources Azure | Microsoft Docs
description: Explique comment démarrer une révision d’accès dans Privileged Identity Management pour les ressources Azure
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 61ed4e82e0b782b423668564dae6efb272967702
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---start-access-review"></a>Privileged Identity Management - Rôle de ressource - Démarrer la révision d’accès
Les attributions de rôles deviennent « obsolètes » lorsque les utilisateurs bénéficient d’un accès privilégié dont ils n’ont plus besoin. Pour réduire les risques associés à ces affectations de rôles « obsolètes », les administrateurs de rôle privilégié doivent régulièrement réviser les rôles qui ont été donnés aux utilisateurs. Ce document décrit les étapes de démarrage d’une révision d’accès dans Privileged Identity Management (PIM) pour les ressources Azure.

Dans la page principale de l’application PIM, accédez à :

* **Révisions d’accès** > **Ajouter**

![](media/azure-pim-resource-rbac/rbac-access-review-home.png)

Lorsque vous cliquez sur le bouton **Ajouter**, le panneau **Créer une vérification d’accès** s’affiche. Dans ce panneau, vous allez configurer la révision avec un nom et une limite de temps, choisir un rôle à réviser, et nommer la personne qui effectuera la révision.

![](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>Configuration de la révision
Pour créer une révision d’accès, vous devez la nommer et définir une date de début et de fin.

![Configuration d’une révision - capture d’écran](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Prévoyez une période suffisamment longue pour permettre aux utilisateurs de terminer la révision. Si vous avez terminé avant la date de fin, vous pouvez toujours arrêter la révision plus tôt.

### <a name="choose-a-role-to-review"></a>Sélection d’un rôle à réviser
Chaque révision se concentre sur un seul rôle. À moins d’avoir démarré la révision d’accès à partir d’un panneau de rôle spécifique, vous devez maintenant choisir un rôle.

1. Navigation vers **Réviser une appartenance à un rôle**
   
    ![Réviser une appartenance à un rôle - capture d’écran](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. Choisissez un rôle dans la liste.

### <a name="decide-who-will-perform-the-review"></a>Désignez la personne qui effectuera la révision
Il existe trois options pour effectuer une révision. Vous pouvez affecter la révision à quelqu’un d’autre, vous pouvez la faire vous-même ou vous pouvez demander à chaque utilisateur de réviser son propre accès.

1. Choisissez l'une des options :
   
   * **Utilisateurs sélectionnés** : utilisez cette option lorsque vous ne savez pas qui a besoin de l’accès. Avec cette option, vous pouvez affecter la révision à un propriétaire de ressource ou un responsable de groupe.
   * **Affecté (auto)** : utilisez cette option pour demander aux utilisateurs de réviser leurs propres affectations de rôles.
   
2. Navigation vers **Sélectionner des réviseurs**
   
    ![Sélection des réviseurs - capture d’écran](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>Démarrage d’une révision
Enfin, vous pouvez obliger les utilisateurs à indiquer le motif pour lequel ils approuvent leur accès. Ajoutez une description de la révision si vous le souhaitez, puis sélectionnez **Démarrer**.

Assurez-vous d’informer vos utilisateurs qu’une révision d’accès les attend, puis montrez-leur [comment exécuter une révision d’accès](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Gestion de la révision d’accès
Vous pouvez suivre la progression des révisions des réviseurs dans le tableau de bord des ressources Azure PIM, dans la section des révisions d'accès. Aucun droit d’accès ne sera modifié dans le répertoire avant que [la révision ne soit terminée](pim-resource-roles-complete-access-review.md).

Tant que la période de révision n’est pas terminée, vous pouvez rappeler aux utilisateurs d’effectuer leur révision, ou arrêter la révision au début de la section des révisions d’accès.

