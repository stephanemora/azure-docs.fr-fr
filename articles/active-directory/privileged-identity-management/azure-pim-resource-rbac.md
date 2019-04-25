---
title: Historique de vue activité et d’audit pour les rôles de ressources Azure dans PIM - Azure Active Directory | Microsoft Docs
description: Affichez l’activité et l’historique d’audit des rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74607f6a746558238ead65036d708b515d370035
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60441405"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-pim"></a>Afficher l’activité et l’historique d’audit des rôles de ressources Azure dans PIM

Avec Azure Active Directory (Azure AD) Privileged Identity Management (PIM), vous pouvez voir l’activité, les activations et l’historique d’audit des rôles de ressources Azure au sein de votre organisation. Cela inclut les abonnements, les groupes de ressources et même les machines virtuelles. Toutes les ressources du portail Azure qui utilisent la fonctionnalité de contrôle d’accès en fonction du rôle (RBAC) d’Azure peuvent tirer parti des possibilités de gestion de la sécurité et des cycles de vie proposées par PIM.

## <a name="view-activity-and-activations"></a>Afficher l’activité et les activations

Pour connaître les actions qu’un utilisateur en particulier a effectuées dans différentes ressources, vous pouvez voir l’activité des ressources Azure qui est associée à une période d’activation donnée.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Ressources Azure**.

1. Cliquez sur la ressource pour laquelle vous souhaitez voir l’activité et les activations.

1. Cliquez sur **Rôles** ou sur **Membres**.

1. Cliquez sur un utilisateur.

    Vous voyez une représentation graphique des actions de l’utilisateur dans les ressources Azure par date. Il montre également les dernières activations de rôle sur cette même période.

    ![Détails de l’utilisateur](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Cliquez sur une activation de rôle spécifique pour voir les détails et l’activité des ressources Azure correspondante survenue lorsque l’utilisateur était actif.

    ![Sélectionner l’activation de rôle](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>Exporter les attributions de rôle ayant des enfants

Vous pouvez avoir une exigence de conformité selon laquelle vous devez fournir une liste complète des attributions de rôle à des auditeurs. PIM permet d’interroger les attributions de rôle sur une ressource particulière, ce qui inclut les attributions de rôle de toutes les ressources enfants. Avant, il était difficile pour les administrateurs d’obtenir une liste complète des attributions de rôle d’un abonnement, car ils devaient les exporter pour chaque ressource spécifique. Avec PIM, vous pouvez interroger toutes les attributions de rôle actives et éligibles d’un abonnement, notamment les attributions de rôle des ressources et de tous les groupes de ressources.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Ressources Azure**.

1. Cliquez sur la ressource pour laquelle vous souhaitez exporter des attributions de rôle, par exemple un abonnement.

1. Cliquez sur **Membres**.

1. Cliquez sur **Exporter** pour ouvrir le volet Exporter l’appartenance.

    ![Volet Exporter l’appartenance](media/azure-pim-resource-rbac/export-membership.png)

1. Cliquez sur **Exporter tous les membres** pour exporter toutes les attributions de rôle dans un fichier CSV.

    ![Exporter le fichier CSV](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Afficher l’historique d’audit des ressources

L’audit des ressources vous donne une vue d’ensemble de l’activité des rôles d’une ressource.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Ressources Azure**.

1. Cliquez sur la ressource pour laquelle vous souhaitez consulter l’historique d’audit.

1. Cliquez sur **Audit des ressources**.

1. Filtrez l’historique en choisissant une date prédéfinie ou une plage personnalisée.

    ![Filtrer l’audit des ressources](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Pour **Type d’audit**, sélectionnez **Activer (Attribué + Activé)**.

    ![Détail de l’activité](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. Sous **Action**, cliquez sur **(activité)** pour un utilisateur, afin d’afficher le détail de l’activité de cet utilisateur dans les ressources Azure.

    ![Détail de l’activité de l’utilisateur](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Afficher mon audit

Mon audit vous permet d’afficher votre activité personnelle de rôle.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Ressources Azure**.

1. Cliquez sur la ressource pour laquelle vous souhaitez consulter l’historique d’audit.

1. Cliquez sur **Mon audit**.

1. Filtrez l’historique en choisissant une date prédéfinie ou une plage personnalisée.

    ![Activité personnelle de rôle](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles de ressources Azure dans PIM](pim-resource-roles-assign-roles.md)
- [Approuver ou rejeter les demandes de rôles de ressources Azure dans PIM](pim-resource-roles-approval-workflow.md)
- [Afficher l’historique d’audit pour les rôles d’Azure AD dans PIM](pim-how-to-use-audit-log.md)
