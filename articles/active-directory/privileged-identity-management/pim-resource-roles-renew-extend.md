---
title: Privileged Identity Management pour les ressources Azure - Extension et renouvellement de rôles | Microsoft Docs
description: Ce document décrit comment étendre et renouveler des rôles de ressources Azure pour les ressources PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 2547b3793688eb51a4114f30bfcf61a9402f2cd2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---extend-or-renew"></a>Privileged Identity Management - Rôles de ressource - Étendre ou Renouveler

PIM pour les ressources Azure propose de nouveaux contrôles pour gérer le cycle de vie d’accès et d’attribution des ressources Azure. Les administrateurs peuvent attribuer une appartenance à l’aide de propriétés de date et d’heure de début et de fin. Lorsque la fin de l’attribution approche, PIM envoie des notifications par courrier électronique aux membres concernés (il peut s’agir d’un utilisateur ou d’un groupe) et aux administrateurs de la ressource pour s’assurer que l’accès approprié est maintenu. Si l’accès n’est pas étendu en raison d’une inaction, les attributions peuvent être renouvelées et restent visibles à l’état expiré pendant 30 jours.

## <a name="who-can-extend-and-renew"></a>Qui peut étendre et renouveler ?

Seuls les administrateurs de la ressource peuvent étendre ou renouveler des attributions de rôles. Le membre concerné peut demander l’extension de rôles arrivant à expiration et demander le renouvellement de rôles ayant déjà expiré.

## <a name="when-are-notifications-sent"></a>Quand des notifications sont-elles envoyées ?

PIM envoie des notifications par courrier électronique aux administrateurs et membres concernés d’expiration de rôle 14 jours et un jour avant l’expiration. Un autre courrier électronique est envoyé lorsqu’une attribution a officiellement expiré. 

Les administrateurs reçoivent des notifications lorsqu’un membre d’un rôle arrivant à expiration ou ayant expiré demande une extension ou un renouvellement. Lorsqu’un administrateur spécifique traite la requête, tous les autres administrateurs sont informés de la décision de résolution (approuvée ou refusée), et le membre à l’origine de la demande est informé de la décision. 

## <a name="extend-role-assignments"></a>Étendre des attributions de rôles

Les étapes ci-dessous décrivent les étapes et l’interface utilisateur concernées dans la requête, la résolution ou l’administration d’une extension ou d’un renouvellement d’une attribution de rôle. 

### <a name="member-extend"></a>Extension de membre

Les membres d’une attribution de rôle peuvent demander une extension des attributions de rôles arrivant à expiration directement depuis l’onglet « Éligible » ou « Actif » de la page « Mes rôles » d’une ressource et depuis le niveau supérieur Mes rôles du portail PIM. Les membres peuvent demander l’extension de rôles éligibles et actifs (attribués) qui expirent au cours des 14 prochains jours.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_ui.png)

Lorsque la date/heure de fin de l’attribution est de 14 jours, le bouton « Étendre » devient un lien actif dans l’interface utilisateur. Dans l’exemple ci-dessous, supposons que la date actuelle est le 27 mars.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_within_14.png)

Pour demander une extension de cette attribution de rôle, cliquez sur « Étendre » pour ouvrir le formulaire de requête.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_role_assignment_request.png)

Développez les « détails de l’attribution » pour consulter les informations relatives à l’attribution d’origine. Entrez un motif pour la requête d’extension, puis cliquez sur « Étendre ».

>[!Note]
>Nous vous recommandons d’inclure les détails de la raison pour laquelle l’extension est nécessaire, et de durée de l’extension (si connue).

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_form_complete.png)

Quelques instants après, les administrateurs de ressources reçoivent une notification par courrier électronique leur demandant de passer en revue la requête d’extension. Si une requête d’extension a déjà été envoyée, une notification toast s’affiche en haut du portail Azure expliquant l’erreur.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_failed_existing_request.png)

Consultez l’onglet « Requêtes en attente » dans le menu de navigation gauche pour connaître l’état ou annuler votre requête.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_cancel_request.png)

### <a name="admin-approve"></a>Approbation d’administrateur

Lorsqu’un membre envoie une requête d’extension d’une attribution de rôle, les administrateurs de ressources reçoivent une notification par courrier électronique contenant les détails de l’attribution d’origine et le motif fourni par le demandeur. La notification inclut un lien direct vers la requête pour que l’administrateur puisse l’approuver ou la refuser. 

Outre le lien contenu dans le courrier électronique, les administrateurs peuvent approuver ou refuser les requêtes en accédant au portail d’administration de PIM et en sélectionnant « Approuver des requêtes » dans le menu de navigation gauche.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Lorsqu’un administrateur approuve ou refuse une requête, les détails de la requête sont affichés, ainsi qu’un champ permettant de fournir une justification pour les journaux d’audit.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Lors de l’approbation d’une requête d’extension d’attribution de rôle, les administrateurs de ressources peuvent choisir de nouvelles dates/heures de début et de fin et un nouveau type d’attribution. La modification du type d’attribution peut être nécessaire si l’administrateur souhaite accorder un accès limité afin d’effectuer une tâche spécifique (un jour par exemple). Dans cet exemple, l’administrateur peut modifier l’attribution d’éligible à active en accordant au demandeur un accès sans devoir nécessairement activer.

### <a name="admin-extend"></a>Extension d’administrateur

Si un membre de rôle oublie ou n’est pas en mesure de demander une extension d’appartenance à un rôle, un administrateur peut étendre une attribution au nom du membre. Les extensions administratives d’appartenance à un rôle ne nécessitent pas d’approbation, mais des notifications sont envoyées à tous les autres administrateurs une fois l’extension du rôle terminée.

Pour étendre une appartenance à un rôle, accédez à la vue du rôle de ressource ou de membre dans PIM. Recherchez le membre demandant une extension et cliquez sur « Étendre » dans la colonne d’action.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_extend.png)

## <a name="renew-role-assignments"></a>Renouveler des attributions de rôles

Bien que semblable d’un point de vue conceptuel, le processus de renouvellement d’une attribution de rôle expirée est différent d’une requête d’extension pour les membres et les administrateurs. Les étapes ci-dessous permettent aux membres et aux administrateurs de renouveler l’accès à des rôles ayant expiré si nécessaire.

### <a name="member-renew"></a>Renouvellement de membre

Les membres ne pouvant plus accéder à des ressources peuvent accéder jusqu’à 30 jours d’historique d’attributions expirées en accédant à Mes rôles dans le volet de navigation gauche de PIM et en sélectionnant l’onglet « Rôles expirés » dans la section des rôles de ressource Azure.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_myroles.png)

La liste des rôles affiche par défaut les attributions éligibles. Utilisez la liste déroulante pour basculer entre les rôles attribués éligibles et actifs.

Pour demander le renouvellement d’une des attributions de rôle dans la liste, sélectionnez l’action « Renouveler » et indiquez le motif de la requête. Il est utile d’indiquer une durée en plus d’un contexte supplémentaire qui aidera l’administrateur de la ressource à l’approuver ou la refuser.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_request_form.png)

Lors de la soumission de la requête, des administrateurs de ressources sont informés d’une requête de renouvellement d’attribution de rôle en attente.

### <a name="admin-approves"></a>Approbations d’administrateur

Les administrateurs de ressources peuvent accéder à la requête de renouvellement à partir du lien contenu dans la notification par courrier électronique du portail Azure et en sélectionnant « Approuver des requêtes » dans le menu de navigation gauche.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Lorsqu’un administrateur approuve ou refuse une requête, les détails de la requête sont affichés, ainsi qu’un champ permettant de fournir une justification pour les journaux d’audit.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Lors de l’approbation d’une requête de renouvellement d’attribution de rôle, les administrateurs de ressources doivent entrer de nouvelles dates/heures de début et de fin et un nouveau type d’attribution. 

### <a name="admin-renew"></a>Renouvellement d’administrateur

Les administrateurs de ressources peuvent renouveler des attributions de rôles ayant expiré depuis l’onglet Membres dans le menu de navigation gauche d’une ressource, ou depuis l’onglet Rôles expirés d’un rôle de ressource.

Dans l’écran Membres, sélectionnez Rôles expirés pour afficher une liste de toutes les attributions de rôles ayant expiré.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_member_blade.png)

## <a name="next-steps"></a>Étapes suivantes

[Exiger une approbation pour activer](pim-resource-roles-approval-workflow.md)

[Activer un rôle](pim-resource-roles-use-the-audit-log.md)


