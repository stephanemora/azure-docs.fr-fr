---
title: Privileged Identity Management pour les ressources Azure - Attribuer des rôles | Microsoft Docs
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
ms.openlocfilehash: 9a9046afe2ee1e578333ff9d29f6fb21e95a0f22
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---assign"></a>Privileged Identity Management - Rôles de ressource - Attribuer

## <a name="assign-roles"></a>Attribuer des rôles

Pour affecter un rôle à un utilisateur ou un groupe, sélectionnez le rôle (si vous les visualisez) 

![](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

ou cliquez sur Ajouter dans la barre d’action (si vous êtes en vue Membres).

![](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


Si vous ajoutez un utilisateur ou un groupe à partir de l’onglet Membres, vous devrez : 

1. Choisir un rôle dans le menu Ajouter avant de pouvoir sélectionner un utilisateur ou un groupe.

![](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. Choisissez un utilisateur ou un groupe à partir de l’annuaire.

3. Dans le menu déroulant, choisissez le type d’attribution approprié. 

    - **Attribution à la volée :** elle fournit aux utilisateurs ou membres du groupe un accès au rôle éligible mais pas persistant pendant une période de temps spécifiée ou bien indéfiniment (selon la configuration des paramètres de rôle). 
    - **Attribution directe :** elle ne nécessite pas l’activation de l’attribution du rôle par l’utilisateur ou les membres du groupe (connu comme un accès permanent). Microsoft recommande l’utilisation d’attributions directes pour une utilisation à court terme, par exemple pour des équipes d’appel ou des activités à durée limitée, où l’accès ne sera pas nécessaire une fois la tâche terminée.

Une case à cocher située sous la liste déroulante du type d’attribution vous permet de spécifier le caractère permanent de l’attribution (éligible de façon permanente pour activer une attribution à la volée/active de façon permanente pour l’attribution directe).

![](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

>[!NOTE]
>La case à cocher peut être non modifiable si un autre administrateur a spécifié la durée maximale d’attribution pour chaque type d’attribution dans les paramètres de rôle.

 Pour spécifier une durée d’attribution spécifique, décochez la case et modifiez les champs des heures et dates de début et/ou de fin.

![](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>Gérer les attributions de rôles

Les administrateurs peuvent gérer des attributions de rôle en sélectionnant des rôles ou des membres dans le volet de navigation gauche. La sélection des rôles permet aux administrateurs de centrer leurs tâches de gestion sur un rôle spécifique, tandis que la section Membres affiche toutes les attributions de rôles des utilisateurs et de groupe pour la ressource.

![](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
Si vous avez un rôle en attente d’activation, une bannière de notification s’affiche en haut de la page lors de l’affichage de l’appartenance.


## <a name="modify-existing-assignments"></a>Modifier des attributions existantes

Pour modifier des attributions existantes à partir de la vue des détails du groupe/utilisateur, sélectionnez Modifier les paramètres à partir de la barre d’action en haut de la page. Configurez le type d’attribution sur Attribution à la volée ou Attribution directe.

![](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
