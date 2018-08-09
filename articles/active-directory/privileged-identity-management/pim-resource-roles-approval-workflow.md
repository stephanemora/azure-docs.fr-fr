---
title: Flux de travail d’approbation pour les rôles de ressources Azure dans Privileged Identity Management (PIM) | Microsoft Docs
description: Décrit le processus de flux de travail d’approbation pour les ressources Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: dcb306bda8ef7d93314390bd9a90327ffdd14a0e
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619635"
---
# <a name="approval-workflow-for-azure-resource-roles-in-privileged-identity-management"></a>Flux de travail d’approbation pour les rôles de ressources Azure dans Privileged Identity Management (PIM)

Le flux de travail d’approbation dans PIM (Privileged Identity Management) pour les rôles de ressources Azure permet aux administrateurs de protéger ou restreindre davantage l’accès aux ressources critiques. Autrement dit, les administrateurs peuvent exiger une approbation pour activer des attributions de rôles. 

Le concept de hiérarchie des ressources est propre aux rôles de ressources Azure. Cette hiérarchie permet aux ressources enfants au sein du conteneur parent d’hériter des attributions de rôle d’un objet de ressource parent. 

Considérez l’exemple suivant : Bob, administrateur des ressources, utilise PIM pour affecter Alice en tant que membre éligible au rôle de propriétaire dans l’abonnement Contoso. Avec cette attribution, Alice est propriétaire éligible de tous les conteneurs de groupes de ressources au sein de l’abonnement Contoso. Elle est également propriétaire éligible de toutes les ressources (telles que les machines virtuelles) au sein de chaque groupe de ressources de l’abonnement. 

Supposez que l’abonnement Contoso comprenne trois groupes de ressources : Fabrikam Test, Fabrikam Dev et Fabrikam Prod. Chacun de ces groupes de ressources contient une seule machine virtuelle.

Les paramètres de PIM sont configurés pour chaque rôle d’une ressource. Contrairement aux attributions, ces paramètres ne sont pas hérités et s’appliquent uniquement au rôle de ressource. [En savoir plus sur les attributions éligibles et la visibilité des ressources](pim-resource-roles-eligible-visibility.md).

Bob utilise PIM pour exiger de la part de tous les membres du rôle de propriétaire de l’abonnement Contoso une demande d’approbation pour l’activation. Pour aider à protéger les ressources contenues dans le groupe de ressources Fabrikam Prod, Bob exige également une approbation pour les membres du rôle de propriétaire de cette ressource. Les rôles de propriétaire de Fabrikam Test et Fabrikam Dev ne nécessitent pas d’approbation pour l’activation.

Quand Alice demande l’activation de son rôle de propriétaire pour l’abonnement Contoso, un approbateur doit approuver ou refuser sa demande pour qu’elle devienne active dans le rôle. Si Alice décide d’[étendre son activation](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices) au groupe de ressources Fabrikam Prod, un approbateur doit également approuver ou refuser cette demande. En revanche, si Alice décide d’étendre son activation à Fabrikam Test ou Fabrikam Dev, l’approbation n’est pas requise.

Il se peut que le flux de travail d’approbation ne soit pas nécessaire pour tous les membres d’un rôle. Envisagez un scénario dans lequel votre société embauche plusieurs associés sous contrat pour aider au développement d’une application qui s’exécute dans un abonnement Azure. En tant qu’administrateur de la ressource, vous souhaitez que les employés bénéficient d’un accès éligible, sans approbation, et que l’associé sous contrat ait besoin de demander une approbation. Pour configurer le flux de travail d’approbation pour les associés sous contrat uniquement, vous pouvez créer un rôle personnalisé avec les mêmes autorisations que le rôle affecté aux employés. Vous pouvez demander l’approbation pour activer ce rôle personnalisé. [En savoir plus sur les rôles personnalisés](pim-resource-roles-custom-role-policy.md).

Pour configurer le flux de travail d’approbation et spécifier les personnes autorisées à approuver ou à refuser les demandes, appliquez les procédures suivantes.

## <a name="require-approval-to-activate"></a>Demander une approbation pour activation

1. Accédez à PIM dans le portail Azure et sélectionnez une ressource dans la liste.

   ![Volet « Ressources Azure » avec une ressource sélectionnée](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

2. Dans le volet gauche, sélectionnez **Paramètres de rôle**.

3. Recherchez et sélectionnez un rôle, puis sélectionnez **Modifier** pour modifier les paramètres.

   ![Bouton « Modifier » pour le rôle d’opérateur](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

4. Dans la section **Activation**, cochez la case **Demander une approbation pour activation**.

   ![Section « Activation » des paramètres de rôle](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Spécifier des approbateurs

Cliquez sur **Sélectionner des approbateurs** pour ouvrir le volet **Sélectionnez un utilisateur ou un groupe**.

>[!NOTE]
>Vous devez sélectionner au moins un utilisateur ou un groupe pour mettre à jour le paramètre. Il n’existe aucun approbateur par défaut.

Les administrateurs de ressources peuvent ajouter n’importe quelle combinaison d’utilisateurs et de groupes à la liste des approbateurs. 

![Volet « Sélectionnez un utilisateur ou un groupe » avec un utilisateur sélectionné](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Demander une approbation pour activation

Une demande d’approbation n’a aucun impact sur la procédure à suivre par un membre pour l’activation. [Passez en revue les étapes pour activer un rôle](pim-resource-roles-activate-your-roles.md).

Si un membre a demandé l’activation d’un rôle qui nécessite une approbation et si le rôle n’est plus nécessaire, le membre peut annuler sa demande dans PIM.

Pour ce faire, accédez à PIM et sélectionnez **Mes demandes**. Recherchez la demande et sélectionnez **Annuler**.

![Volet « Mes demandes »](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>Approuver ou refuser une demande

Pour approuver ou refuser une demande, vous devez être un membre de la liste des approbateurs. 

1. Dans PIM, sélectionnez **Approuver les demandes** sous l’onglet du menu de gauche et recherchez la demande.

   ![Volet « Approuver des requêtes »](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. Sélectionnez la demande, justifiez la décision et sélectionnez **Approuver** ou **Refuser**. La demande est alors résolue.

   ![Demande sélectionnée avec des informations détaillées](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Notifications de flux de travail

Voici quelques éléments concernant les notifications de flux de travail :

- Tous les membres de la liste des approbateurs sont avertis par courrier électronique lorsqu’une demande concernant un rôle est en attente de leur examen. Les notifications par e-mail comportent un lien direct vers la demande, grâce auquel l’approbateur peut approuver ou refuser cette demande.
- Les demandes sont traitées par le premier membre de la liste qui les approuve ou les refuse. 
- Lorsqu’un approbateur répond à la demande, tous les membres de la liste des approbateurs sont informés de l’action. 
- Les administrateurs de ressources sont avertis de l’activation d’un membre dans leur rôle. 

>[!Note]
>Un administrateur de ressources qui estime qu’un membre approuvé ne doit pas être actif peut supprimer l’attribution de rôle active dans PIM. Bien que les administrateurs de ressources ne soient informés des demandes en attente que s’ils sont membres de la liste des approbateurs, ils peuvent voir et annuler les demandes en attente de tous les utilisateurs en les affichant dans PIM. 

## <a name="next-steps"></a>Étapes suivantes

[Appliquer des paramètres PIM à des groupes d’utilisateurs uniques](pim-resource-roles-custom-role-policy.md)
