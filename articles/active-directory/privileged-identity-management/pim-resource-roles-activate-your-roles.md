---
title: Activer mes rôles de ressources Azure dans PIM - Azure Active Directory | Microsoft Docs
description: Découvrez comment activer des rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e2bf1253f1c2f53b839a73034fcdb33f3aab463
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804336"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Activer des rôles de ressources Azure dans PIM

À l’aide d’Azure Active Directory (Azure AD) Privileged Identity Management (PIM), les membres d’un rôle éligible pour les ressources Azure peuvent planifier l’activation à une date et une heure ultérieures. Ils peuvent également sélectionner une durée d’activation spécifique (à condition qu’elle ne dépasse pas la durée maximale configurée par les administrateurs).

Cet article concerne les membres qui ont besoin d’activer leur rôle de ressource Azure dans PIM.

## <a name="activate-a-role"></a>Activer un rôle

Lorsque vous avez besoin d’endosser un rôle de ressource Azure, vous pouvez demander une activation à l’aide de l’option de navigation **Mes rôles** dans PIM.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. Ouvrez **Azure AD Privileged Identity Management**. Pour plus d’informations sur l’ajout de la vignette PIM à votre tableau de bord, consultez [Commencer à utiliser PIM](pim-getting-started.md).

1. Cliquez sur **Mes rôles**.

    ![Page Mes rôles montrant les rôles que vous pouvez activer](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Cliquez sur **Rôles de ressources Azure** pour afficher la liste de vos rôles de ressources Azure éligibles.

   ![Page Mes rôles - Rôles de ressources Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. Dans la liste **Rôles de ressources Azure**, recherchez le rôle que vous souhaitez activer.

    ![Rôles de ressources Azure - Liste Mes rôles éligibles](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Cliquez sur **Activer** pour ouvrir le volet Activer.

1. Si votre rôle exige une authentification multifacteur (MFA), cliquez sur **Vérifier votre identité avant de continuer**. Vous ne devez vous authentifier qu’une seule fois par session.

    ![Vérifier mon identité à l’aide de l’authentification multifacteur avant activation du rôle](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Cliquez sur **Vérifier mon identité** et suivez les instructions pour effectuer une vérification de sécurité supplémentaire.

    ![Écran de vérification de sécurité permettant la saisie d’un code secret, par exemple](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Si vous souhaitez spécifier une étendue réduite, cliquez sur **Étendue** pour ouvrir le volet Filtre de ressources.

    Il est recommandé de ne demander l’accès qu’aux ressources dont vous avez besoin. Dans le volet Filtre de ressources, vous pouvez spécifier les groupes de ressources ou les ressources auxquels vous avez besoin d’accéder.

    ![Activer - Volet Filtre de ressources pour spécifier l’étendue](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Si nécessaire, spécifiez une heure de début personnalisée pour l’activation. Le membre sera activé après l’heure sélectionnée.

1. Dans la zone de texte **Raison**, entrez le motif de la demande d’activation.

    ![Volet Activer complètement renseigné comportant l’étendue, l’heure de début, la durée et le motif](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Cliquez sur **Activer**.

    Si le rôle ne nécessite pas d’approbation, il est activé et ajouté à la liste des rôles actifs. Si vous voulez utiliser le rôle, suivez les étapes décrites dans la section suivante.

    Si [l’activation du rôle nécessite une approbation](pim-resource-roles-approval-workflow.md), une notification s’affiche dans le coin supérieur droit de votre navigateur pour vous informer que la demande est en attente d’approbation.

    ![La demande d’activation est en attente de la notification d’approbation](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

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

    ![Mes demandes - Page des ressources Azure montrant vos demandes en attente](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Faites défiler vers la droite pour afficher la colonne **État de la demande**.

## <a name="cancel-a-pending-request"></a>Annuler une demande en attente

Si vous n’avez pas besoin de l’activation d’un rôle nécessitant une approbation, vous pouvez annuler une demande en attente à tout moment.

1. Ouvrez Azure AD Privileged Identity Management.

1. Cliquez sur **Mes demandes**.

1. Pour le rôle que vous souhaitez annuler, cliquez sur le lien **Annuler**.

    Lorsque vous cliquez sur Annuler, la demande est annulée. Pour réactiver le rôle, vous devez envoyer une nouvelle demande d’activation.

   ![Ma liste de demandes dans laquelle l’action d’annulation est mise en surbrillance](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Résolution des problèmes

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Les autorisations ne sont pas accordées après l’activation d’un rôle

Lorsque vous activez un rôle dans PIM, il est possible que l’activation ne se propage pas instantanément à tous les portails qui nécessitent le rôle privilégié. Parfois, même si la modification est propagée, son application immédiate peut être empêchée par la mise en cache web dans un portail. Si l’activation est retardée, voici les étapes à suivre.

1. Déconnectez-vous du portail Azure, puis reconnectez-vous.

    Quand vous activez un rôle de ressource Azure AD, vous voyez les phases de l’activation. Une fois toutes les phases terminées, vous voyez un lien **Se déconnecter**. Vous pouvez utiliser ce lien pour vous déconnecter. Cette procédure résout la plupart des cas de retard d’activation.

1. Dans PIM, vérifiez que vous êtes répertorié en tant que membre du rôle.

## <a name="next-steps"></a>Étapes suivantes

- [Étendre ou renouveler des rôles de ressources Azure dans PIM](pim-resource-roles-renew-extend.md)
- [Activer mes rôles Azure AD dans PIM](pim-how-to-activate-role.md)
