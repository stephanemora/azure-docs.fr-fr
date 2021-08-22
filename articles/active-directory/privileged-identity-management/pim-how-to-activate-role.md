---
title: Activer mes rôles Azure AD dans PIM - Azure Active Directory | Microsoft Docs
description: Découvrez comment activer des rôles Azure AD dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3926fb277251f7020539942c76d9c97f0ce46d75
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524598"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Activer mes rôles Azure AD dans PIM

Le service Azure Active Directory (Azure AD) Privileged Identity Management (PIM) simplifie la gestion par les entreprises de l’accès privilégié aux ressources dans Azure AD et d’autres services en ligne Microsoft, tels que Microsoft 365 ou Microsoft Intune.  

Si vous êtes *éligible* à un rôle d’administrateur, vous devez *activer* ce rôle quand vous devez effectuer des actions privilégiées. Par exemple, si vous gérez occasionnellement des fonctionnalités de Microsoft 365, les administrateurs de rôle privilégié de votre organisation peuvent ne pas vous attribuer un rôle d’administrateur général permanent, étant donné que ce rôle influe également sur les autres services. Au lieu de cela, ils peuvent vous attribuer des rôles Azure AD tels qu’administrateur Exchange Online. Vous pouvez faire une demande pour activer ce rôle lorsque vous avez besoin de ses privilèges. Vous aurez ainsi le contrôle d’administrateur pendant une période prédéterminée.

Cet article est destiné aux administrateurs qui doivent activer leur rôle Azure AD dans Privileged Identity Management.

## <a name="activate-a-role"></a>Activer un rôle

Lorsque vous devez assumer un rôle Azure AD, vous pouvez demander une activation en ouvrant **Mes rôles** dans Privileged Identity Management.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Ouvrez **Azure AD Privileged Identity Management**. Pour plus d’informations sur la façon d’ajouter la mosaïque Privileged Identity Management à votre tableau de bord, consultez [Commencer à utiliser Privileged Identity Management](pim-getting-started.md).

1. Sélectionnez **Mes rôles**, puis **Rôles Azure AD** pour afficher la liste de vos rôles Azure AD éligibles.

    ![Page Mes rôles montrant les rôles que vous pouvez activer](./media/pim-how-to-activate-role/my-roles.png)

1. Dans la liste **Rôles Azure AD**, recherchez le rôle que vous souhaitez activer.

    ![Rôles Azure AD : liste Mes rôles éligibles](./media/pim-how-to-activate-role/activate-link.png)

1. Sélectionnez **Activer** pour ouvrir le volet Activer.

    ![Rôles Azure AD : la page d’activation contient la durée et l’étendue](./media/pim-how-to-activate-role/activate-page.png)

1. Sélectionnez **Vérification supplémentaire requise**** et suivez les instructions pour effectuer une vérification de sécurité supplémentaire. Vous ne pouvez vous authentifier qu’une seule fois par session.

    ![Écran de vérification de sécurité permettant la saisie d’un code secret, par exemple](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Après l’authentification multifacteur, sélectionnez **Activer avant de continuer**.

    ![Vérifier mon identité à l’aide de l’authentification multifacteur avant activation du rôle](./media/pim-how-to-activate-role/activate-role-mfa-banner.png)

1. Si vous souhaitez spécifier une étendue réduite, sélectionnez **Étendue** pour ouvrir le volet des filtres. Dans le volet des filtres, vous pouvez spécifier les ressources Azure AD auxquelles vous devez accéder. Il est recommandé de demander l’accès aux seules ressources dont vous avez besoin.

1. Si nécessaire, spécifiez une heure de début personnalisée pour l’activation. Le rôle Azure AD sera activé après l’heure sélectionnée.

1. Dans la zone de texte **Raison**, entrez le motif de la demande d’activation.

1. Sélectionnez **Activer**.

    Si [l’activation du rôle nécessite une approbation](pim-resource-roles-approval-workflow.md), une notification s’affiche dans le coin supérieur droit de votre navigateur pour vous informer que la demande est en attente d’approbation.

    ![La requête d’activation est en attente de la notification d’approbation](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests-for-new-version"></a>Afficher l’état de vos requêtes pour la nouvelle version

Vous pouvez afficher l’état de vos demandes d’activation en attente.

1. Ouvrez Azure AD Privileged Identity Management.

1. Sélectionnez **Mes demandes** pour afficher la liste des demandes de vos rôles Azure AD et de rôles de ressources Azure.

    ![Mes demandes : page Azure AD montrant vos demandes en attente](./media/pim-how-to-activate-role/my-requests-page.png)

1. Faites défiler vers la droite pour afficher la colonne **État de la demande**.

## <a name="cancel-a-pending-request-for-new-version"></a>Annuler une demande en attente pour la nouvelle version

Si vous n’avez pas besoin de l’activation d’un rôle nécessitant une approbation, vous pouvez annuler une demande en attente à tout moment.

1. Ouvrez Azure AD Privileged Identity Management.

1. Sélectionnez **Mes demandes**.

1. Pour le rôle que vous souhaitez annuler, sélectionnez le lien **Annuler**.

    Lorsque vous sélectionnez Annuler, la requête est annulée. Pour réactiver le rôle, vous devez envoyer une nouvelle demande d’activation.

   ![Ma liste de demandes dans laquelle l’action d’annulation est mise en surbrillance](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot-for-new-version"></a>Résoudre les problèmes pour la nouvelle version

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Les autorisations ne sont pas accordées après l’activation d’un rôle

Quand vous activez un rôle dans Privileged Identity Management, il est possible que l’activation ne se propage pas instantanément à tous les portails qui nécessitent le rôle privilégié. Parfois, même si la modification est propagée, son application immédiate peut être empêchée par la mise en cache web dans un portail. Si votre activation est retardée, déconnectez-vous du portail dans lequel vous essayez d’effectuer l’action, puis reconnectez-vous. Dans le portail Azure, PIM vous déconnecte et vous reconnecte automatiquement.

## <a name="next-steps"></a>Étapes suivantes

- [Voir l’historique d'audit pour les rôles Azure AD](pim-how-to-use-audit-log.md)
