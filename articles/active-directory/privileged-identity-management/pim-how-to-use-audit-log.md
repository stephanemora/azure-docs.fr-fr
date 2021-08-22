---
title: Afficher le rapport de journal d’audit pour les rôles Azure AD dans Azure AD PIM | Microsoft Docs
description: Découvrez comment afficher l’historique du journal d’audit des rôles Azure AD dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 06/03/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c28b901643e150cf98b97633f0dbec0a348b3995
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112232862"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Afficher l’historique d’audit des rôles Azure AD dans Privileged Identity Management

Vous pouvez utiliser l’historique d’audit PIM (Privileged Identity Management) pour voir toutes les activations et toutes les affectations de rôles qui ont été effectuées au cours des 30 derniers jours pour tous les rôles privilégiés. Si vous souhaitez conserver les données d’audit plus longtemps que la période de conservation par défaut, utilisez Azure Monitor pour les transférer vers un compte de stockage Azure. Pour plus d’informations, consultez [Archiver des journaux d’activité Azure AD sur un compte de stockage Azure](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md). Si vous souhaitez consulter l’historique d’audit complet de l’activité dans votre organisation Azure Active Directory (Azure AD), notamment l’activité de l’administrateur, de l’utilisateur final et de la synchronisation, vous pouvez utiliser les [rapports d’activité et de sécurité d’Azure Active Directory](../reports-monitoring/overview-reports.md).

Suivez ces étapes pour afficher l’historique des audits pour les rôles Azure AD.

## <a name="view-resource-audit-history"></a>Afficher l’historique d’audit des ressources

L’audit des ressources vous donne un aperçu de toutes les activités associées à vos rôles Azure AD.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez des **rôles Azure AD**.

1. Sélectionnez un **audit de ressources**.

1. Filtrez l’historique en choisissant une date prédéfinie ou une plage personnalisée.

    ![Liste des audits de ressources avec filtres](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Afficher mon audit

Mon audit vous permet d’afficher votre activité personnelle de rôle.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez des **rôles Azure AD**.

1. Sélectionnez la ressource pour laquelle vous souhaitez consulter l’historique d’audit.

1. Sélectionnez **Mon audit**.

1. Filtrez l’historique en choisissant une date prédéfinie ou une plage personnalisée.

    ![Liste d’audits pour l’utilisateur actuel](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Étapes suivantes

- [Afficher l’activité et l’historique d’audit des rôles de ressources Azure dans Privileged Identity Management](azure-pim-resource-rbac.md)
