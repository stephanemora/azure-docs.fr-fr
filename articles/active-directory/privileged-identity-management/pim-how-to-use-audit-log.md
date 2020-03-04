---
title: Afficher le rapport d’audit pour les rôles Azure AD dans PIM - Azure AD | Microsoft Docs
description: Découvrez comment afficher l’historique des audits des rôles Azure AD dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4a2eccc02d13bf5a2dfc8bf3ceb7887e4962489
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77498488"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Voir l’historique d’audit pour les rôles Azure AD dans PIM

Vous pouvez utiliser l’historique d’audit PIM (Privileged Identity Management) pour voir toutes les activations et toutes les affectations de rôles qui ont été effectuées au cours des 30 derniers jours pour tous les rôles privilégiés. Si vous souhaitez consulter l’historique d’audit complet de l’activité dans votre organisation Azure Active Directory (Azure AD), notamment l’activité de l’administrateur, de l’utilisateur final et de la synchronisation, vous pouvez utiliser les [rapports d’activité et de sécurité d’Azure Active Directory](../reports-monitoring/overview-reports.md).

## <a name="determine-your-version-of-pim"></a>Déterminer votre version de PIM

Depuis novembre 2019, la partie Rôles Azure AD de Privileged Identity Management est mise à jour vers une nouvelle version qui correspond à l’expérience des rôles de ressources Azure. Cela permet de créer des fonctionnalités supplémentaires et d’apporter des [modifications à l’API existante](azure-ad-roles-features.md#api-changes). Pendant que la nouvelle version est déployée, les procédures que vous suivez dans cet article dépendent de la version de Privileged Identity Management que vous possédez actuellement. Pour déterminer la version de Privileged Identity Management dont vous disposez, procédez de la manière décrite dans cette section. Une fois que vous connaissez votre version de Privileged Identity Management, vous pouvez sélectionner les procédures de cet article qui correspondent à cette version.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide d’un utilisateur avec le rôle [Administrateur de rôle privilégié](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).
1. Ouvrez **Azure AD Privileged Identity Management**. Si une bannière figure en haut de la page de présentation, suivez les instructions sous l’onglet **Nouvelle version** de cet article. Sinon, suivez les instructions sous l’onglet **Version précédente**.

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nouvelle version](#tab/new)

Suivez ces étapes pour afficher l’historique des audits pour les rôles Azure AD.

## <a name="view-resource-audit-history"></a>Afficher l’historique d’audit des ressources

L’audit des ressources vous donne un aperçu de toutes les activités associées à vos rôles Azure AD.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez des **rôles Azure AD**.

1. Sélectionnez un **audit de ressources**.

1. Filtrez l’historique en choisissant une date prédéfinie ou une plage personnalisée.

    ![Liste des audits de ressources avec filtres](media/pim-how-to-use-audit-log/resource-audit.png)

## <a name="view-my-audit"></a>Afficher mon audit

Mon audit vous permet d’afficher votre activité personnelle de rôle.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez des **rôles Azure AD**.

1. Sélectionnez la ressource pour laquelle vous souhaitez consulter l’historique d’audit.

1. Sélectionnez **Mon audit**.

1. Filtrez l’historique en choisissant une date prédéfinie ou une plage personnalisée.

    ![Liste d’audits pour l’utilisateur actuel](media/pim-how-to-use-audit-log/audit-time-span.png)

# <a name="previous-version"></a>[Version précédente](#tab/previous)

## <a name="view-audit-history"></a>Afficher l’historique d'audit

Suivez ces étapes pour afficher l’historique des audits pour les rôles Azure AD.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide d’un nom d’utilisateur qui est membre du rôle [Administrateur de rôle privilégié](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez des **rôles Azure AD**.

1. Sélectionnez l’**historique d’audit des rôles d’annuaire**.

    En fonction de l’historique d’audit, un histogramme s’affiche dans lequel figurent le nombre total d’activations, le nombre maximal d’activations par jour et la moyenne d’activations par jour.

    ![Historique d’audit des rôles d’annuaire](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Au bas de la page, un tableau s’affiche avec des informations sur chaque action de l’historique d’audit disponible. Voici la signification de ces colonnes :

    | Colonne | Description |
    | --- | --- |
    | Temps | Moment auquel une action s’est produite. |
    | Demandeur | Personne qui a demandé l’activation d’un rôle ou sa modification. Si la valeur est **Système Azure**, consultez l’historique d’audit Azure pour obtenir plus d’informations. |
    | Action | Actions effectuées par le demandeur. Il peut s’agir des actions Affecter, Annuler l’affectation, Activer, Désactiver ou AddedOutsidePIM. |
    | Membre | Utilisateur qui active un rôle ou est affecté à ce rôle. |
    | Role | Rôle affecté ou activé par l’utilisateur. |
    | Raisonnement | Texte entré dans le champ Motif pendant l’activation. |
    | Expiration | Date d’expiration d’un rôle activé. S’applique uniquement aux affectations de rôles éligibles. |

1. Pour trier l’historique d’audit, utilisez les boutons **Date**, **Action** et **Rôle**.

## <a name="filter-audit-history"></a>Filtrer l’historique d’audit

1. En haut de la page de l’historique d’audit, cliquez sur le bouton **Filtrer**.

    Le volet **Mettre à jour les paramètres du graphique** s’affiche.

1. Dans **Intervalle de temps**, sélectionnez un intervalle de temps.

1. Dans **Rôles**, activez les cases à cocher pour indiquer les rôles que vous souhaitez afficher.

    ![Volet Mettre à jour les paramètres du graphique](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Sélectionnez **Terminé** pour voir l’historique d’audit filtré.

---

## <a name="next-steps"></a>Étapes suivantes

- [Afficher l’activité et l’historique d’audit des rôles de ressources Azure dans Privileged Identity Management](azure-pim-resource-rbac.md)
