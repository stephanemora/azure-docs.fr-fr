---
title: Gérer les groupes dont votre groupe fait partie dans Azure AD | Microsoft Docs
description: Les groupes peuvent contenir d’autres groupes dans Azure Active Directory. Voici comment gérer ces adhésions.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 10/10/2017
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: 8a71677ae3ceb5617f0a817a8eff438d5e3f2774
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860369"
---
# <a name="manage-to-which-groups-a-group-belongs-in-your-azure-active-directory-tenant"></a>Gérer les groupes auxquels un groupe appartient dans votre client Azure Active Directory
Les groupes peuvent contenir d’autres groupes dans Azure Active Directory. Voici comment gérer ces adhésions.

## <a name="how-do-i-find-the-groups-of-which-my-group-is-a-member"></a>Comment trouver les groupes dont le mien fait partie ?
1. Connectez-vous au [centre d’administration Azure AD](https://aad.portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Utilisateurs et groupes**.

   ![Ouverture de Utilisateurs et groupes](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
1. Sélectionnez **Tous les groupes**.

   ![Sélection de groupes](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
1. Sélectionnez un groupe.
2. Sélectionnez **Appartenances aux groupes**.

   ![Ouverture de Appartenances aux groupes](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
1. Pour ajouter votre groupe en tant que membre d’un autre groupe, dans le panneau **Groupe - Appartenances aux groupes**, sélectionnez la commande **Ajouter**.
2. Sélectionnez un groupe à partir du panneau **Sélectionner un groupe**, puis cliquez sur le bouton **Sélectionner** en bas du panneau. Vous pouvez ajouter votre groupe à un seul groupe à la fois. La zone **Utilisateur** filtre l’affichage en fonction de la correspondance de votre entrée avec une partie ou l’intégralité d’un nom d’utilisateur ou d’appareil. Dans cette zone aucun caractère générique n’est accepté.

   ![Ajouter une appartenance à un groupe](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. Pour supprimer votre groupe en tant que membre d’un autre groupe, dans le panneau **Groupe - Appartenances aux groupes** , sélectionnez un groupe.
9. Sélectionnez la commande **Supprimer**, puis confirmez votre choix dans l’invite de commandes.

   ![Commande Supprimer des appartenances](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. Lorsque vous avez terminé de modifier les appartenances aux groupes de votre groupe, sélectionnez **Enregistrer**.

## <a name="additional-information"></a>Informations supplémentaires
Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Consulter les groupes existants](active-directory-groups-view-azure-portal.md)
* [Création d’un nouveau groupe et ajout de membres](active-directory-groups-create-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer les membres d’un groupe](active-directory-groups-members-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs dans un groupe](../users-groups-roles/groups-dynamic-membership.md)
