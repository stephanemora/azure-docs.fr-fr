---
title: Activer mes rôles de ressources Azure dans PIM - Azure Active Directory | Microsoft Docs
description: Découvrez comment activer des rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04e8615cc5534255308c35fa1f675ef3a85aa84e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60438451"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Activer des rôles de ressources Azure dans PIM

À l’aide d’Azure Active Directory (Azure AD) Privileged Identity Management (PIM), les membres d’un rôle éligible pour les ressources Azure peuvent planifier l’activation à une date et une heure ultérieures. Ils peuvent également sélectionner une durée d’activation spécifique (à condition qu’elle ne dépasse pas la durée maximale configurée par les administrateurs).

Cet article concerne les membres qui ont besoin d’activer leur rôle de ressource Azure dans PIM.

## <a name="activate-a-role"></a>Activer un rôle

Lorsque vous avez besoin d’endosser un rôle de ressource Azure, vous pouvez demander une activation à l’aide de l’option de navigation **Mes rôles** dans PIM.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. Ouvrez **Azure AD Privileged Identity Management**. Pour plus d’informations sur l’ajout de la vignette PIM à votre tableau de bord, consultez [Commencer à utiliser PIM](pim-getting-started.md).

1. Cliquez sur **Mes rôles**.

    ![Rôles Azure AD et rôles de ressources Azure - Mes rôles](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Cliquez sur **Rôles de ressources Azure** pour afficher la liste de vos rôles de ressources Azure éligibles.

   ![Rôles de ressources Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. Dans la liste **Rôles de ressources Azure**, recherchez le rôle que vous souhaitez activer.

    ![Rôles de ressources Azure - Liste Mes rôles](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Cliquez sur **Activer** pour ouvrir le volet Activer.

1. Si votre rôle exige une authentification multifacteur (MFA), cliquez sur **Vérifier votre identité avant de continuer**. Vous ne devez vous authentifier qu’une seule fois par session.

    ![Vérification multifacteur avant activation du rôle](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Cliquez sur **Vérifier mon identité** et suivez les instructions pour effectuer une vérification de sécurité supplémentaire.

    ![Vérification de sécurité supplémentaire](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Si vous souhaitez spécifier une étendue réduite, cliquez sur **Étendue** pour ouvrir le volet Filtre de ressources.

    Il est recommandé de ne demander l’accès qu’aux ressources dont vous avez besoin. Dans le volet Filtre de ressources, vous pouvez spécifier les groupes de ressources ou les ressources auxquels vous avez besoin d’accéder.

    ![Activer - Filtre de ressources](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Si nécessaire, spécifiez une heure de début personnalisée pour l’activation. Le membre sera activé après l’heure sélectionnée.

1. Dans la zone de texte **Raison**, entrez le motif de la demande d’activation.

    ![Volet Activation terminée](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Cliquez sur **Activer**.

    Si le rôle ne nécessite pas d’approbation, il est activé et ajouté à la liste des rôles actifs. Si vous voulez utiliser le rôle, suivez les étapes décrites dans la section suivante.

    Si [l’activation du rôle nécessite une approbation](pim-resource-roles-approval-workflow.md), une notification s’affiche dans le coin supérieur droit de votre navigateur pour vous informer que la demande est en attente d’approbation.

    ![Notification de demande en attente](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Utiliser un rôle immédiatement après son activation

En cas de délai après l’activation, suivez ces étapes après avoir activé l’utilisation de vos rôles de ressources Azure immédiatement.

1. Ouvrez Azure AD Privileged Identity Management.

1. Cliquez sur **Mes rôles** pour afficher la liste de vos rôles Azure AD et rôles de ressources Azure éligibles.

1. Cliquez sur **Rôles de ressources Azure**.

1. Cliquez sur l’onglet **Rôles actifs**.

1. Une fois que le rôle est actif, déconnectez-vous du portail, puis reconnectez-vous.

    Le rôle doit maintenant être disponible.

## <a name="view-the-status-of-your-requests"></a>Afficher l’état de vos demandes

Vous pouvez afficher l’état de vos demandes d’activation en attente.

1. Ouvrez Azure AD Privileged Identity Management.

1. Cliquez sur **Mes demandes** pour afficher la liste des demandes de vos rôles Azure AD et de rôles de ressources Azure.

    ![Rôles Azure AD et rôles de ressources Azure - Mes demandes](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Faites défiler vers la droite pour afficher la colonne **État de la demande**.

## <a name="cancel-a-pending-request"></a>Annuler une demande en attente

Si vous n’avez pas besoin de l’activation d’un rôle nécessitant une approbation, vous pouvez annuler une demande en attente à tout moment.

1. Ouvrez Azure AD Privileged Identity Management.

1. Cliquez sur **Mes demandes**.

1. Pour le rôle que vous souhaitez annuler, cliquez sur le lien **Annuler**.

    Lorsque vous cliquez sur Annuler, la demande est annulée. Pour réactiver le rôle, vous devez envoyer une nouvelle demande d’activation.

   ![Annuler une demande en attente](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Résolution des problèmes

### <a name="permissions-not-granted-after-activating-a-role"></a>Autorisations non accordées après l’activation d’un rôle

Quand vous activez un rôle dans PIM, 10 minutes au moins sont nécessaires avant de pouvoir accéder au portail d’administration souhaité ou d’exécuter des fonctions au sein d’une charge de travail d’administration spécifique. Une fois l’activation terminée, déconnectez-vous du portail Azure et reconnectez-vous pour commencer à utiliser le rôle qui vient d’être activé.

Pour des étapes de dépannage supplémentaires, consultez [Résolution des problèmes des autorisations élevées](https://social.technet.microsoft.com/wiki/contents/articles/37568.troubleshooting-elevated-permissions-with-azure-ad-privileged-identity-management.aspx).

### <a name="cannot-activate-a-role-due-to-a-resource-lock"></a>Impossible d’activer un rôle en raison d’un verrou de ressource

Si vous recevez un message selon lequel une ressource Azure est verrouillée lorsque vous essayez d’activer un rôle, c’est peut-être qu’une ressource dans l’étendue d’une attribution de rôle a un verrou de ressource. Des verrous protègent les ressources contre une suppression accidentelle ou des modifications inattendues. Un verrou empêche également PIM de supprimer une attribution de rôle sur la ressource à la fin de la période d’activation. Étant donné que la technologie PIM ne peut pas fonctionner correctement lorsqu’un verrou est appliqué, elle empêche les utilisateurs d’activer des rôles sur la ressource. Il y a deux manières de résoudre ce problème :

- Supprimez le verrou, comme décrit dans [verrouiller les ressources pour empêcher les modifications inattendues](../../azure-resource-manager/resource-group-lock-resources.md).
- Si vous souhaitez conserver le verrou, rendez l’attribution de rôle permanente ou utilisez un compte de secours.

## <a name="next-steps"></a>Étapes suivantes

- [Étendre ou renouveler des rôles de ressources Azure dans PIM](pim-resource-roles-renew-extend.md)
- [Activer mes rôles Azure AD dans PIM](pim-how-to-activate-role.md)
