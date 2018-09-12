---
title: Activer des rôles de ressources Azure dans PIM | Microsoft Docs
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
ms.component: pim
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 59bce2c61db5838bb21a29757d4e354311ecffd5
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666245"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Activer des rôles de ressources Azure dans PIM

À l’aide d’Azure AD Privileged Identity Management (PIM), les membres d’un rôle éligible pour les ressources Azure peuvent planifier l’activation à une date et une heure ultérieures. Ils peuvent également sélectionner une durée d’activation spécifique (à condition qu’elle ne dépasse pas la durée maximale configurée par les administrateurs).

Cet article concerne les membres qui ont besoin d’activer leur rôle de ressource Azure dans PIM.

## <a name="activate-a-role"></a>Activer un rôle

Lorsque vous avez besoin d’endosser un rôle de ressource Azure, vous pouvez demander une activation à l’aide de l’option de navigation **Mes rôles** dans PIM.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. Ouvrez **Azure AD Privileged Identity Management**. Pour plus d’informations sur l’ajout de la vignette PIM à votre tableau de bord, consultez [Commencer à utiliser PIM](pim-getting-started.md).

1. Cliquez sur **Mes rôles** pour afficher la liste de vos rôles d’annuaire Azure AD et rôles de ressource Azure éligibles.

    ![Rôles d’annuaire Azure AD et rôles de ressources Azure - Mes rôles](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

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

    Si le rôle ne nécessite pas d’approbation, il est activé et figure dans la liste des rôles actifs. Si [l’activation du rôle nécessite une approbation](pim-resource-roles-approval-workflow.md), une notification s’affiche dans le coin supérieur droit de votre navigateur pour vous informer que la demande est en attente d’approbation.

    ![Notification de demande en attente](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Afficher l’état de vos demandes

Vous pouvez afficher l’état de vos demandes d’activation en attente.

1. Ouvrez Azure AD Privileged Identity Management.

1. Cliquez sur **Mes demandes** pour afficher la liste des demandes de vos rôles d’annuaire Azure AD et de ressources Azure.

    ![Rôles d’annuaire Azure AD et rôles de ressources Azure - Mes demandes](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Faites défiler vers la droite pour afficher la colonne **État de la demande**.

## <a name="use-a-role-immediately-after-activation"></a>Utiliser un rôle immédiatement après son activation

En raison de la mise en cache, vous devez réactualiser la page pour voir les nouvelles activations dans le portail Azure. Si vous avez besoin de réduire les risques de délais après l’activation d’un rôle, vous pouvez utiliser la page **Accès à l’application** du portail. Les applications auxquelles accèdent les utilisateurs à partir de cette page vérifient les nouvelles attributions de rôles immédiatement.

1. Ouvrez Azure AD Privileged Identity Management.

1. Cliquez sur la page **Accès à l’application**.

    ![Accès à l’application dans PIM - Capture d’écran](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. Cliquez sur **Ressources Azure** pour rouvrir le portail dans la page **Toutes les ressources**.

    Lorsque vous cliquez sur ce lien, vous forcez l’actualisation, ce qui lance une recherche des nouvelles attributions de rôles de ressources Azure.

## <a name="cancel-a-pending-request"></a>Annuler une demande en attente

Si vous n’avez pas besoin de l’activation d’un rôle nécessitant une approbation, vous pouvez annuler une demande en attente à tout moment.

1. Ouvrez Azure AD Privileged Identity Management.

1. Cliquez sur **Mes demandes**.

1. Pour le rôle que vous souhaitez annuler, cliquez sur le lien **Annuler**.

    Lorsque vous cliquez sur Annuler, la demande est annulée. Pour réactiver le rôle, vous devez envoyer une nouvelle demande d’activation.

   ![Annuler une demande en attente](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="next-steps"></a>Étapes suivantes

- [Étendre ou renouveler des rôles de ressources Azure dans PIM](pim-resource-roles-renew-extend.md)
- [Activer des rôles d’annuaire Azure AD dans PIM](pim-how-to-activate-role.md)