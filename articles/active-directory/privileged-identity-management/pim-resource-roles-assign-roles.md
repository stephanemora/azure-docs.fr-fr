---
title: Attribuer des rôles de ressource Azure dans Privileged Identity Management - Azure Active Directory | Microsoft Docs
description: Découvrez comment attribuer des rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34051a31c6ccf69356f330d7c5ecb009f760857a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79231801"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Attribuer des rôles de ressources Azure dans Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) peut gérer les rôles de ressources intégrés d’Azure, ainsi que les rôles personnalisés, notamment (liste non exhaustive) :

- Propriétaire
- Administrateur de l'accès utilisateur
- Contributeur
- Administrateur de la sécurité
- Gestionnaire de sécurité

> [!NOTE]
> Les utilisateurs ou membres d’un groupe affecté aux rôles d’abonnement Propriétaire ou Administrateur d’accès utilisateur et les administrateurs d’Azure AD Global qui activent la gestion des abonnements dans Azure AD disposent des droits d’administrateur de ressources par défaut. Ces administrateurs peuvent assigner des rôles, configurer des paramètres de rôle et revoir les accès à l’aide de Privileged Identity Management pour Azure Resources. Un utilisateur ne peut pas gérer Privileged Identity Management pour les ressources sans autorisations d’administrateur de ressources. Affichez la liste des [rôles intégrés pour les ressources Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Attribuer un rôle

Suivez ces étapes pour rendre un utilisateur éligible pour un rôle de ressource Azure.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) à l’aide d’un nom d’utilisateur qui est membre du rôle [Administrateur de rôle privilégié](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

    Pour plus d’informations sur la façon d’accorder l’accès à un autre administrateur pour la gestion de Privileged Identity Management, consultez [Accorder l’accès à d’autres administrateurs pour la gestion de Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez **Ressources Azure**.

1. Utilisez le **filtre de ressources** pour filtrer la liste des ressources managées.

    ![Liste des ressources Azure à gérer](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Sélectionnez la ressource que vous souhaitez gérer, telle qu’un abonnement ou un groupe d’administration.

1. Sous Gérer, sélectionnez **Rôles** pour afficher la liste des rôles pour les ressources Azure.

    ![Rôles de ressources Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Sélectionnez **Ajouter un membre** pour ouvrir le volet Nouvelle affectation.

1. Sélectionnez **Sélectionner un rôle** pour ouvrir le volet Sélectionner un rôle.

    ![Volet Nouvelle affectation](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Sélectionnez un rôle que vous souhaitez attribuer et cliquez sur **Sélectionner**.

    Le volet Sélectionner un membre ou un groupe s’ouvre.

1. Sélectionnez un membre ou un groupe que vous souhaitez affecter au rôle, puis cliquez sur **Sélectionner**.

    ![Volet Sélectionner un membre ou un groupe](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Le volet Paramètres d'appartenance s’ouvre.

1. Dans la liste **Type d'attribution**, sélectionnez **Éligible** ou **Actif**.

    ![Volet des paramètres des appartenances](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Azure AD Privileged Identity Management pour les ressources Azure fournit deux types distincts d’attribution :

    - Les attributions **éligibles** exigent des membres qu’ils effectuent une action pour utiliser ce rôle. Il peut s’agir de procéder à une vérification de l’authentification multifacteur (MFA), de fournir une justification professionnelle ou de demander une approbation aux approbateurs désignés.

    - Les attributions **actives** n’exigent pas des membres qu’ils effectuent une action pour utiliser ce rôle. Les membres attribués comme étant actifs détiennent à tout moment les privilèges affectés au rôle.

1. Si l’attribution doit être permanente (éligible de façon permanente ou attribuée définitivement), cochez la case **Définitivement**.

    Selon les paramètres du rôle, cette case à cocher peut ne pas être affichée ou être non modifiable.

1. Pour spécifier une durée d’attribution spécifique, décochez la case et modifiez les champs des heures et dates de début et/ou de fin.

    ![Paramètres des appartenances - date et heure](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Quand vous avez terminé, sélectionnez **Terminé**.

    ![Nouvelle affectation - Ajouter](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Pour créer la nouvelle attribution de rôle, sélectionnez **Ajouter**. Une notification de l’état s’affiche.

    ![Nouvelle affectation - Notification](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Mettre à jour ou supprimer une attribution de rôle existante

Suivez ces étapes pour mettre à jour ou supprimer une attribution de rôle existante.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez **Ressources Azure**.

1. Sélectionnez la ressource que vous souhaitez gérer, telle qu’un abonnement ou un groupe d’administration.

1. Sous Gérer, sélectionnez **Rôles** pour afficher la liste des rôles pour les ressources Azure.

    ![Rôles de ressources Azure - Sélectionner un rôle](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Sélectionnez le rôle que vous souhaitez mettre à jour ou supprimer.

1. Recherchez l’attribution de rôle sous les onglets **Rôles éligibles** et **Rôles actifs**.

    ![Mettre à jour ou supprimer une attribution de rôle](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Sélectionnez **Mettre à jour** ou **Supprimer** pour mettre à jour ou supprimer l’attribution de rôle.

    Pour obtenir des informations sur l’extension d’une attribution de rôle, consultez [Étendre ou renouveler des rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Étapes suivantes

- [Étendre ou renouveler des rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Configurer les paramètres des rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Attribuer des rôles dans Azure AD dans Privileged Identity Management](pim-how-to-add-role-to-user.md)
