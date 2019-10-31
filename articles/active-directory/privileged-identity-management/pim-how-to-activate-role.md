---
title: Activer mes rôles Azure AD dans PIM - Azure Active Directory | Microsoft Docs
description: Découvrez comment activer des rôles Azure AD dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6007762c897337170dec69c3486302aa62723480
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756283"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Activer mes rôles Azure AD dans PIM

Le service Azure Active Directory (Azure AD) Privileged Identity Management (PIM) simplifie la gestion par les entreprises de l’accès privilégié aux ressources dans Azure AD et d’autres services en ligne Microsoft, tels qu’Office 365 ou Microsoft Intune.  

Si vous êtes éligible à un rôle d’administrateur, vous pouvez activer ce rôle quand vous devez effectuer des actions privilégiées. Par exemple, si vous gérez occasionnellement des fonctionnalités d’Office 365, les administrateurs de rôle privilégié de votre organisation peuvent ne pas vous attribuer le rôle d’administrateur général permanent, étant donné que ce rôle affecte également les autres services. Au lieu de cela, ils peuvent vous attribuer des rôles Azure AD tels qu’administrateur Exchange Online. Vous pouvez faire une demande pour activer ce rôle lorsque vous avez besoin de ses privilèges. Vous aurez ainsi le contrôle d’administrateur pendant une période prédéterminée.

Cet article est destiné aux administrateurs qui doivent activer leur rôle Azure AD dans Privileged Identity Management.

## <a name="activate-a-role"></a>Activer un rôle

Lorsque vous avez besoin d’endosser un rôle Azure AD, vous pouvez demander une activation à l’aide de l’option de la navigation **Mes rôles** dans Privileged Identity Management.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. Ouvrez **Azure AD Privileged Identity Management**. Pour plus d’informations sur la façon d’ajouter la mosaïque Privileged Identity Management à votre tableau de bord, consultez [Commencer à utiliser Privileged Identity Management](pim-getting-started.md).

1. Cliquez sur **Rôles Azure AD**.

1. Cliquez sur **Mes rôles** pour afficher la liste de vos rôles Azure AD éligibles.

    ![Rôles Azure AD - Liste Mes rôles montrant la liste des rôles éligibles ou actifs](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Recherchez un rôle à activer.

    ![Rôles Azure AD - Liste Mes rôles éligibles montrant le lien Activer](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Cliquez sur **Activer** pour ouvrir le volet où se trouvent les informations d’activation du rôle.

1. Si votre rôle exige une authentification multifacteur (MFA), cliquez sur **Vérifier votre identité avant de continuer**. Vous ne devez vous authentifier qu’une seule fois par session.

    ![Volet de vérification de votre identité avec l’authentification multifacteur avant l’activation du rôle](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Cliquez sur **Vérifier mon identité** et suivez les instructions pour effectuer une vérification de sécurité supplémentaire.

    ![Page de vérification de sécurité supplémentaire vous demandant comment vous contacter](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Cliquez sur **Activer** pour ouvrir le volet Activation.

    ![Volet d’activation spécifiant l’heure de début, la durée, le ticket et la raison](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Si nécessaire, spécifiez une heure de début personnalisée pour l’activation.

1. Spécifiez la durée de l’activation.

1. Dans la zone de texte **Raison de l’activation**, entrez le motif de la demande d’activation. Certains rôles vous obligent à fournir un numéro de ticket d’incident.

    ![Volet de fin de l’activation spécifiant l’heure de début, la durée, la raison et le ticket personnalisés](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Cliquez sur **Activer**.

    Si le rôle ne nécessite pas d’approbation, un volet **État d’activation** s’affiche. Il présente l’état de l’activation.

    ![Page d’état de l’activation montrant les trois étapes d’activation](./media/pim-how-to-activate-role/activation-status.png)

    Une fois toutes les étapes terminées, cliquez sur le lien **Se déconnecter** pour vous déconnecter du portail Azure. Vous pouvez maintenant utiliser le rôle lors de votre prochaine connexion.

    Si [l’activation du rôle nécessite une approbation](./azure-ad-pim-approval-workflow.md), une notification s’affiche dans le coin supérieur droit de votre navigateur pour vous informer que la demande est en attente d’approbation.

    ![La requête d’activation est en attente de la notification d’approbation](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Afficher l’état de vos demandes

Vous pouvez afficher l’état de vos demandes d’activation en attente.

1. Ouvrez Azure AD Privileged Identity Management.

1. Cliquez sur **Rôles Azure AD**.

1. Cliquez sur **Mes demandes** pour afficher la liste de vos demandes.

    ![Rôles Azure AD - Liste Mes requêtes](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Désactiver un rôle

Une fois qu’un rôle a été activé, il se désactive automatiquement quand sa limite de temps (durée éligible) est atteinte.

Si vous terminez vos tâches d’administration plus tôt que prévu, vous pouvez également désactiver un rôle manuellement dans Azure AD Privileged Identity Management.

1. Ouvrez Azure AD Privileged Identity Management.

1. Cliquez sur **Rôles Azure AD**.

1. Cliquez sur **Mes rôles**.

1. Cliquez sur **Rôles actifs** pour afficher la liste des rôles actifs.

1. Accédez au rôle dont vous n’avez plus besoin, puis cliquez sur **Désactiver**.

## <a name="cancel-a-pending-request"></a>Annuler une demande en attente

Si vous n’avez pas besoin de l’activation d’un rôle nécessitant une approbation, vous pouvez annuler une demande en attente à tout moment.

1. Ouvrez Azure AD Privileged Identity Management.

1. Cliquez sur **Rôles Azure AD**.

1. Cliquez sur **Mes demandes**.

1. Pour le rôle que vous souhaitez annuler, cliquez sur le bouton **Annuler**.

    Lorsque vous cliquez sur Annuler, la requête est annulée. Pour réactiver le rôle, vous devez envoyer une nouvelle demande d’activation.

   ![Liste Mes demandes, avec le bouton Annuler en surbrillance](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Résolution des problèmes

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Les autorisations ne sont pas accordées après l’activation d’un rôle

Quand vous activez un rôle dans Privileged Identity Management, il est possible que l’activation ne se propage pas instantanément à tous les portails qui nécessitent le rôle privilégié. Parfois, même si la modification est propagée, son application immédiate peut être empêchée par la mise en cache web dans un portail. Si l’activation est retardée, voici les étapes à suivre.

1. Déconnectez-vous du portail Microsoft Azure, puis reconnectez-vous.

    Lorsque vous activez un rôle Azure AD, vous voyez apparaître les phases de l’activation. Une fois toutes les phases terminées, un lien **Se déconnecter** s’affiche. Vous pouvez utiliser ce lien pour vous déconnecter. Cette procédure résout la plupart des cas de retard d’activation.

1. Dans Privileged Identity Management, vérifiez que vous êtes répertorié en tant que membre du rôle.

## <a name="next-steps"></a>Étapes suivantes

- [Activer mes rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-activate-your-roles.md)
