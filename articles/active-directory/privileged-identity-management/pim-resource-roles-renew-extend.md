---
title: Étendre ou renouveler des attributions de rôles de ressources Azure dans PIM - Azure Active Directory | Microsoft Docs
description: Découvrez comment étendre ou renouveler des affectations de rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
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
ms.openlocfilehash: 8aef7d4ac37109233af4ffb364b6f3e4c9b291ac
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578388"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-pim"></a>Étendre ou renouveler des affectations de rôles de ressources Azure dans PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) propose de nouveaux contrôles pour gérer le cycle de vie accès et d’attribution pour les ressources Azure. Les administrateurs peuvent attribuer une appartenance à l’aide de propriétés de date et d’heure de début et de fin. À l’approche de la fin de l’attribution, PIM envoie des notifications par e-mail aux utilisateurs ou aux groupes concernés, ainsi qu’aux administrateurs de la ressource pour garantir le maintien de l’accès approprié. Les attributions peuvent être renouvelées et rester visibles à l’état expiré pendant 30 jours, même si l’accès n’est pas étendu.

## <a name="who-can-extend-and-renew"></a>Qui peut étendre et renouveler ?

Seuls les administrateurs de la ressource peuvent étendre ou renouveler des attributions de rôles. Le membre concerné peut demander l’extension de rôles arrivant à expiration et le renouvellement de rôles ayant déjà expiré.

## <a name="when-are-notifications-sent"></a>Quand des notifications sont-elles envoyées ?

PIM envoie des notifications par e-mail aux administrateurs et aux membres des rôles arrivant à expiration dans les 14 jours et un jour avant l’expiration. Un autre e-mail est envoyé lorsqu’une attribution expire officiellement. 

Les administrateurs reçoivent des notifications lorsqu’un membre d’un rôle arrivant à expiration ou ayant expiré demande une extension ou un renouvellement. Lorsqu’un administrateur traite la demande, tous les autres administrateurs sont informés de la décision de résolution (approuvée ou refusée). Celle-ci est ensuite communiquée au membre à l’origine de la demande. 

## <a name="extend-role-assignments"></a>Étendre des attributions de rôles

Les étapes suivantes décrivent la procédure de demande, de résolution et d’administration de l’extension ou du renouvellement d’une attribution de rôle. 

### <a name="member-extend"></a>Extension de membre

Les membres d’une attribution de rôle arrivant à expiration peuvent l’étendre directement sur l’onglet **Éligible** ou **Actif** de la page **Mes rôles** d’une ressource et sur la page **Mes rôles** de niveau supérieur du portail PIM. Les membres peuvent demander l’extension de rôles éligibles et actifs (attribués) qui expirent au cours des 14 prochains jours.

![Étendre les rôles](media/azure-pim-resource-rbac/aadpim_rbac_extend_ui.png)

Lorsque la date/heure de fin de l’attribution se situe 14 jours plus tard ou moins, le bouton **Étendre** devient un lien actif dans l’interface utilisateur. Dans l’exemple ci-dessous, supposons que la date actuelle est le 27 mars.

![Étendre le bouton](media/azure-pim-resource-rbac/aadpim_rbac_extend_within_14.png)

Pour demander une extension de cette attribution de rôle, sélectionnez **Étendre** afin d’ouvrir le formulaire de demande.

![Ouvrir le formulaire de demande](media/azure-pim-resource-rbac/aadpim_rbac_extend_role_assignment_request.png)

Pour consulter les informations relatives à l’attribution d’origine, développez **Détails de l’attribution**. Entrez le motif de la demande d’extension, puis sélectionnez **Étendre**.

>[!Note]
>Nous vous recommandons de préciser en détail la raison pour laquelle l’extension est nécessaire, ainsi que la durée de l’extension demandée (si vous la connaissez).

![Étendre les attributions de rôles](media/azure-pim-resource-rbac/aadpim_rbac_extend_form_complete.png)

Quelques instants après, les administrateurs de ressources reçoivent une notification par e-mail leur demandant d’examiner la demande d’extension. Si une demande d’extension a déjà été envoyée, une notification toast expliquant l’erreur s’affiche en haut du Portail Azure.

![Explication de l’erreur de notification](media/azure-pim-resource-rbac/aadpim_rbac_extend_failed_existing_request.png)

Accédez à l’onglet **Demandes en attente** dans le menu de gauche pour connaître l’état de votre demande ou l’annuler.

![Demandes en attente](media/azure-pim-resource-rbac/aadpim_rbac_extend_cancel_request.png)

### <a name="admin-approve"></a>Approbation d’administrateur

Lorsqu’un membre envoie une demande d’extension d’une attribution de rôle, les administrateurs de ressources reçoivent une notification par e-mail contenant les détails de l’attribution d’origine et le motif de la demande. La notification inclut un lien direct vers la requête pour que l’administrateur puisse l’approuver ou la refuser. 

Pour approuver ou refuser les demandes, les administrateurs peuvent suivre le lien contenu dans l’e-mail ou accéder au portail d’administration de PIM et sélectionner **Approuver les demandes** dans le menu de gauche.

![Capture d’écran de l’erreur](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Lorsqu’un administrateur sélectionne **Approuver** ou **Refuser**, les détails de la demande s’affichent, ainsi qu’un champ permettant de donner une justification pour les journaux d’audit.

![Approuver la demande d’attribution de rôle](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Lorsqu’ils approuvent une demande d’extension d’attribution de rôle, les administrateurs de ressources peuvent choisir de nouvelles dates de début et de fin et un nouveau type d’attribution. Une modification du type d’attribution peut être nécessaire s’ils souhaitent accorder un accès limité afin d’effectuer une tâche spécifique (un jour, par exemple). Dans cet exemple, l’administrateur peut modifier l’attribution de **Éligible** à **Actif**, ce qui signifie qu’il peut donner l’accès au demandeur sans l’obliger à s’activer.

### <a name="admin-extend"></a>Extension d’administrateur

Si un membre de rôle oublie ou n’est pas en mesure de demander une extension d’appartenance à un rôle, un administrateur peut étendre une attribution en son nom. Les extensions administratives d’appartenance à un rôle ne nécessitent pas d’approbation, mais des notifications sont envoyées à tous les autres administrateurs une fois le rôle étendu.

Pour étendre une appartenance à un rôle, accédez à la vue du rôle de ressource ou de membre dans PIM. Trouvez le membre qui a besoin d’une extension. Ensuite, sélectionnez **Étendre** dans la colonne d’action.

![Étendre une appartenance à un rôle](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_extend.png)

## <a name="renew-role-assignments"></a>Renouveler des attributions de rôles

Bien que semblable d’un point de vue conceptuel au processus de demande d’extension, le processus de renouvellement d’une attribution de rôle arrivée à expiration est différent. Les étapes suivantes permettent aux membres et aux administrateurs de renouveler si nécessaire l’accès à des rôles ayant expiré.

### <a name="member-renew"></a>Renouvellement de membre

Les membres qui n’ont plus accès aux ressources peuvent accéder jusqu’à 30 jours d’historique d’attributions arrivées à expiration. Pour cela, ils accèdent à **Mes rôles** dans le volet de gauche, puis sélectionnent l’onglet **Rôles arrivés à expiration** dans la section Rôles des ressources Azure.

![Onglet rôles expirés](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_myroles.png)

La liste des rôles affiche par défaut les **Rôles éligibles**. Utilisez le menu déroulant pour basculer entre les rôles attribués éligibles et actifs.

Pour demander le renouvellement d’une des attributions de rôles de la liste, sélectionnez l’action **Renouveler**. Ensuite, précisez le motif de la demande. Il est utile d’indiquer une durée ainsi que des éléments de contexte supplémentaires pour aider l’administrateur de ressources à l’approuver ou à la refuser.

![Renouveler une attribution de rôle](media/azure-pim-resource-rbac/aadpim_rbac_renew_request_form.png)

Une fois que la demande de renouvellement d’une attribution de rôle a été soumise, les administrateurs de ressources sont informés de la présence d’une demande en attente.

### <a name="admin-approves"></a>Approbations d’administrateur

Les administrateurs de ressources peuvent accéder à la demande de renouvellement à partir du lien contenu dans la notification par e-mail ou en accédant à PIM sur le Portail Azure et en sélectionnant **Approuver les demandes** dans le menu de gauche.

![Approuver les demandes](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Lorsqu’un administrateur sélectionne **Approuver** ou **Refuser**, les détails de la demande s’affichent, ainsi qu’un champ permettant de donner une justification pour les journaux d’audit.

![Approuver une attribution de rôle](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Lorsqu’ils approuvent une demande de renouvellement d’attribution de rôle, les administrateurs de ressources doivent entrer de nouvelles dates de début et de fin et un nouveau type d’attribution. 

### <a name="admin-renew"></a>Renouvellement d’administrateur

Les administrateurs de ressources peuvent renouveler les attributions de rôles ayant expiré dans l’onglet **Membres** du menu de navigation de gauche d’une ressource, ou dans l’onglet **Expiré** des rôles d’une ressource.

Pour voir la liste de toutes les attributions de rôles ayant expiré, sélectionnez **Rôles ayant expiré** sur l’écran **Membres**.

![Rôles ayant expiré](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_member_blade.png)

## <a name="next-steps"></a>Étapes suivantes

- [Approuver ou rejeter les demandes de rôles de ressources Azure dans PIM](pim-resource-roles-approval-workflow.md)
- [Configurer les paramètres des rôles de ressources Azure dans PIM](pim-resource-roles-configure-role-settings.md)
