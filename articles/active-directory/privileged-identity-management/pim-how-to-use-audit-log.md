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
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c4a157d8d5bcd281ca9fee488e58c455034e898
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022059"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Voir l’historique d’audit pour les rôles Azure AD dans PIM

Vous pouvez utiliser l’historique d’audit PIM (Privileged Identity Management) pour voir toutes les activations et toutes les affectations de rôles qui ont été effectuées au cours des 30 derniers jours pour tous les rôles privilégiés. Si vous souhaitez consulter l’historique d’audit complet de l’activité dans votre organisation Azure Active Directory (Azure AD), notamment l’activité de l’administrateur, de l’utilisateur final et de la synchronisation, vous pouvez utiliser les [rapports d’activité et de sécurité d’Azure Active Directory](../reports-monitoring/overview-reports.md).

## <a name="view-audit-history"></a>Afficher l’historique d'audit

Suivez ces étapes pour afficher l’historique des audits pour les rôles Azure AD.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide d’un nom d’utilisateur qui est membre du rôle [Administrateur de rôle privilégié](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Rôles Azure AD**.

1. Cliquez sur **Historique d’audit des rôles d’annuaire**.

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

1. Pour voir l’historique d’audit filtré, cliquez sur **Terminé**.

## <a name="next-steps"></a>Étapes suivantes

- [Afficher l’activité et l’historique d’audit des rôles de ressources Azure dans Privileged Identity Management](azure-pim-resource-rbac.md)
