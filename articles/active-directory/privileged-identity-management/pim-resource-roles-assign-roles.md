---
title: Attribuer des rôles pour des ressources Azure à l’aide de Privileged Identity Management | Microsoft Docs
description: Décrit comment attribuer des rôles dans PIM.
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
ms.openlocfilehash: 501f063992d2f5c7769a5c9059b346aa2b5c2bb4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149370"
---
# <a name="assign-roles-for-azure-resources-by-using-privileged-identity-management"></a>Attribuer des rôles pour des ressources Azure à l’aide de Privileged Identity Management

## <a name="assign-roles"></a>Attribuer des rôles

Pour affecter un utilisateur ou un groupe à un rôle quand vous visualisez le volet **Rôles**, sélectionnez le rôle, puis **Ajouter un utilisateur**. 

![Volet « Rôles » avec le bouton « Ajouter un utilisateur »](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

Vous pouvez également sélectionner **Ajouter un utilisateur** à partir du volet **Membres**.

![Volet « Membres » avec le bouton « Ajouter un utilisateur »](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


Si vous ajoutez un utilisateur ou un groupe à partir du volet **Membres**, vous devez : 

1. Choisir un rôle dans le volet **Sélectionner un rôle** avant de pouvoir sélectionner un utilisateur ou un groupe.

   ![Volet « Sélectionner un rôle »](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. Choisissez un utilisateur ou un groupe à partir de l’annuaire.

3. Dans le menu déroulant, choisissez le type d’attribution approprié : 

   - **Juste-à-temps :** fournit aux utilisateurs ou membres du groupe un accès au rôle éligible mais pas persistant pendant une période spécifiée ou bien indéfiniment (en fonction de la configuration des paramètres de rôle). 
   - **Directe :** ne nécessite pas l’activation de l’attribution du rôle par l’utilisateur ou les membres du groupe (connu sous le nom d’accès permanent). Nous vous recommandons d’utiliser l’attribution directe pour une utilisation à court terme, où l’accès ne sera pas obligatoire une fois la tâche terminée, par exemple dans le cas d’activités d’astreinte ou soumises à des contraintes de temps.

4. Si l’attribution doit être permanente (éligible en permanence pour une attribution juste-à-temps, ou active en permanence pour une attribution directe), cochez la case qui se trouve sous la zone **Type d’attribution**.

   ![Volet « Paramètres d’appartenance » avec la zone « Type d’attribution » et la case à cocher associée](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

   >[!NOTE]
   >La case à cocher peut être non modifiable si un autre administrateur a spécifié la durée maximale d’attribution pour chaque type d’attribution dans les paramètres de rôle.

   Pour spécifier une durée d’attribution spécifique, décochez la case et modifiez les champs des heures et dates de début et/ou de fin.

   ![Volet « Paramètres d’appartenance » avec champs de date de début, heure de début, date de fin et heure de fin](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>Gérer les attributions de rôles

Les administrateurs peuvent gérer les attributions de rôle en sélectionnant **Rôles** ou **Membres** dans le volet gauche. Sélectionner **Rôles** permet aux administrateurs de limiter leurs tâches de gestion à un rôle spécifique. Sélectionner **Membres** permet d’afficher toutes les attributions de rôles d’utilisateurs et de groupes pour la ressource.

![Volet « Rôles »](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![Volet « Membres »](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
Si vous avez un rôle en attente d’activation, une bannière de notification s’affiche en haut de la page quand vous visualisez l’appartenance.


## <a name="modify-existing-assignments"></a>Modifier des attributions existantes

Pour modifier des attributions existantes à partir de la vue des détails du groupe/utilisateur, sélectionnez **Modifier les paramètres** à partir de la barre d’action. Sélectionnez le type d’attribution **Juste-à-temps** ou **Directe**.

![Volet « Détails de l’utilisateur » avec le bouton « Modifier les paramètres »](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
