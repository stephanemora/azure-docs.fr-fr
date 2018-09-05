---
title: Configurer les paramètres des rôles de ressources Azure dans PIM | Microsoft Docs
description: Découvrez comment configurer les paramètres des rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 901eb5ef43ddb2840ed7a3d83fc08f2f05849461
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189733"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>Configurer les paramètres des rôles de ressources Azure dans PIM

Quand vous configurez les paramètres de rôle, vous définissez les paramètres par défaut appliqués aux affectations dans l’environnement Privileged Identity Management (PIM). Pour définir ces paramètres pour votre ressource, sélectionnez l’onglet **Paramètres de rôle** dans le volet gauche. Vous pouvez également sélectionner le bouton Paramètres de rôle de la barre d’action (dans n’importe quel rôle) pour afficher les options actuelles.

## <a name="overview"></a>Vue d’ensemble

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

## <a name="next-steps"></a>Étapes suivantes

- [Exiger une authentification multifacteur pour les rôles de ressources Azure dans PIM](pim-resource-roles-require-mfa.md)
- [Configurer des alertes de sécurité pour les rôles de ressources Azure dans PIM](pim-resource-roles-configure-alerts.md)
