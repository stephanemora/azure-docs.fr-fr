---
title: Flux de travail d’approbation Privileged Identity Management (PIM) pour les rôles de ressource Azure | Microsoft Docs
description: Décrit comment le flux de travail d’approbation traite les ressources Azure.
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
ms.openlocfilehash: c02d595d75b2d63558896054c185102ebb23cc9e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---approve"></a>Privileged Identity Management - Rôles de ressource - Approuver

Le flux de travail d’approbation dans PIM pour les rôles de ressource Azure, permet aux administrateurs de protéger ou de restreindre davantage l’accès aux ressources critiques par une demande d’approbation en vue d’activer les attributions de rôles. Le concept de hiérarchie des ressources est propre aux rôles de ressource Azure. Cette hiérarchie permet aux ressources enfant subordonnées au sein du conteneur parent d’hériter des affectations de rôle d’un objet de ressource parent. 

Par exemple Bob, un administrateur des ressources utilise PIM pour affecter Alice en tant que membre éligible au rôle de propriétaire dans l’abonnement Contoso. Grâce à cette affectation Alice est également propriétaire éligible de tous les conteneurs de groupes de ressources au sein de l’abonnement Contoso et de toutes les ressources (telles que les machines virtuelles) contenus dans chaque groupe de ressources, de l’abonnement. Supposons que l’abonnement Contoso comprenne trois groupes de ressources : sont dans  ; Fabrikam Test, Fabrikam Dev et Fabrikam Prod. Chacun de ces groupes de ressources contient une seule machine virtuelle.

Les paramètres PIM sont configurés pour chaque rôle de ressource et contrairement aux affectations, ne sont pas hérités et s’appliquent strictement au rôle de ressource. [En savoir plus sur les affectations éligibles et la visibilité des ressources.](pim-resource-roles-eligible-visibility.md)

À l’aide de l’exemple ci-dessus Bob utilise PIM pour exiger de la part de tous les membres du rôle de propriétaire de l’abonnement Contoso une demande d’approbation pour l’activation. Pour protéger les ressources contenues dans le groupe de ressources Fabrikam Prod, Bob requiert également une approbation pour les membres du rôle de propriétaire de cette ressource. Les rôles de propriétaire de Fabrikam Test et Fabrikam Dev ne nécessitent pas d’approbation pour l’activation.

Lorsqu’Alice demande l’activation de son rôle de propriétaire pour l’abonnement Contoso, un approbateur doit approuver ou refuser sa demande avant qu’elle ne devienne active dans le rôle. En outre, si Alice décide d’[étendre son activation](pim-resource-roles-activate-your-roles.md#just-enough-administration) au groupe de ressources Fabrikam Prod, un approbateur doit également approuver ou refuser cette demande. Toutefois, si Alice décide d’étendre son activation à Fabrikam Test ou Fabrikam Dev, l’approbation n’est pas requise.

Il se peut que le flux de travail d’approbation ne soit pas nécessaire pour tous les membres d’un rôle. Envisagez un scénario dans lequel votre société embauche plusieurs associés sous contrat pour aider au développement d’une application qui s’exécute dans un abonnement Azure. En tant qu’administrateur de la ressource, vous souhaitez que les employés bénéficient d’un accès éligible, sans approbation, et que l’associé sous contrat ait besoin de demander une approbation. Pour configurer le flux de travail d’approbation pour les associés sous contrat uniquement, vous pouvez créer un rôle personnalisé avec les mêmes autorisations que le rôle affecté aux employés et demander l’approbation d’activer ce rôle personnalisé. [En savoir plus sur les rôles personnalisés](pim-resource-roles-custom-role-policy.md).

Suivez les étapes ci-dessous pour configurer l’approbation de flux de travail et spécifier les personnes autorisées à approuver ou refuser les demandes.

## <a name="require-approval-to-activate"></a>Demander une approbation pour activation

Accédez à PIM dans le portail Azure et sélectionnez une ressource dans la liste.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

Dans le menu de navigation de gauche, sélectionnez **Paramètres de rôle**.

Recherchez et sélectionnez un rôle, puis cliquez sur **Modifier** pour modifier les paramètres.

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

Dans la section Activation, cochez la case **Require approval to activate** (Exiger une approbation pour activation).

![](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Spécifier des approbateurs

Cliquez sur **Sélectionner des approbateurs** pour ouvrir l’écran de sélection.

>[!NOTE]
>Vous devez sélectionner au moins un utilisateur ou un groupe pour mettre à jour le paramètre. Il n’existe aucun approbateur par défaut.

Les administrateurs de ressources peuvent ajouter n’importe quelle combinaison d’utilisateurs et de groupes à la liste des approbateurs. 

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Demander une approbation pour activation

Une demande d’approbation n’a aucun impact sur la procédure à suivre par un membre pour activation. [Passez en revue les étapes pour activer un rôle](pim-resource-roles-activate-your-roles.md).

Si un membre a demandé l’activation d’un rôle qui nécessite une approbation et si le rôle n’est plus nécessaire, le membre peut annuler sa demande dans PIM.

Pour ce faire, accédez à PIM et sélectionnez Mes demandes. Recherchez la demande, puis cliquez sur Annuler.

![](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>Approuver ou refuser une demande

Pour approuver ou refuser une demande, vous devez être un membre de la liste des approbateurs. Dans PIM, sélectionnez Approuver les demandes sous l’onglet du menu de navigation gauche et recherchez la demande.

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

Sélectionnez la demande, justifiez la décision et sélectionnez approuver ou refuser, lors du traitement de la demande.

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Notifications de flux de travail

- Tous les membres de la liste des approbateurs sont avertis par courrier électronique lorsqu’une demande concernant un rôle est en attente de leur examen. Les notifications par courrier électronique comportent un lien direct vers la demande où l’approbateur peut approuver ou refuser.
- Les demandes sont traitées par le premier membre de la liste qui les approuve ou les refuse. 
- Lorsqu’un approbateur répond à la demande, tous les membres de la liste des approbateurs sont informés de l’action. 
- Les administrateurs de ressources sont avertis de l’activation d’un membre dans leur rôle. 

>[!Note]
>Si un administrateur des ressources estime que le membre approuvé ne doit pas être actif, celui-ci peut supprimer l’affectation du rôle actif dans PIM. Bien que les administrateurs de ressources ne soient pas informés des demandes en attente, à moins d’être membres de la liste des approbateurs, ils peuvent voir et annuler les demandes en attente de tous les utilisateurs en les affichant dans PIM. 

## <a name="next-steps"></a>Étapes suivantes

[Appliquer des paramètres PIM à des groupes d’utilisateurs uniques](pim-resource-roles-custom-role-policy.md)
