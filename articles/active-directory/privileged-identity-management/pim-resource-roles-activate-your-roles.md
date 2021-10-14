---
title: Activer les rôles de ressources Azure dans PIM - Azure AD | Microsoft Docs
description: Découvrez comment activer des rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 09/28/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee4bb5c8be060f78f557391e406a8008294f34be
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272216"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Activer mes rôles de ressources Azure dans Privileged Identity Management

Utilisez Privileged Identity Management (PIM) pour autoriser les membres d’un rôle éligible pour les ressources Azure à planifier l’activation à une date et une heure ultérieures. Ils peuvent également sélectionner une durée d’activation spécifique (à condition qu’elle ne dépasse pas la durée maximale configurée par les administrateurs).

Cet article est destiné aux membres qui doivent activer leur rôle de ressources Azure dans Privileged Identity Management.

## <a name="activate-a-role"></a>Activer un rôle

Lorsque vous avez besoin d’endosser un rôle de ressources Azure, vous pouvez demander une activation à l’aide de l’option de la navigation **Mes rôles** dans Privileged Identity Management.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Ouvrez **Azure AD Privileged Identity Management**. Pour plus d’informations sur la façon d’ajouter la mosaïque Privileged Identity Management à votre tableau de bord, consultez [Commencer à utiliser Privileged Identity Management](pim-getting-started.md).

1. Sélectionnez **Mes rôles**.

    ![Page Mes rôles montrant les rôles que vous pouvez activer](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Sélectionnez **Rôles de ressources Azure** pour afficher la liste de vos rôles de ressources Azure éligibles.

    ![Page Mes rôles - Rôles de ressources Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png)

1. Dans la liste **Rôles de ressources Azure**, recherchez le rôle que vous souhaitez activer.

    ![Rôles de ressources Azure - Liste Mes rôles éligibles](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Sélectionnez **Activer** pour ouvrir la page Activer.

    ![Volet Activer ouvert comportant l’étendue, l’heure de début, la durée et le motif](./media/pim-resource-roles-activate-your-roles/azure-role-eligible-activate.png)

1. Si votre rôle exige une authentification multifacteur, sélectionnez **Vérifier votre identité avant de continuer**. Vous ne devez vous authentifier qu’une seule fois par session.

    ![Vérifier mon identité à l’aide de l’authentification multifacteur avant activation du rôle](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Sélectionnez **Vérifier mon identité** et suivez les instructions pour effectuer une vérification de sécurité supplémentaire.

    ![Écran de vérification de sécurité permettant la saisie d’un code secret, par exemple](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Si vous souhaitez spécifier une étendue réduite, sélectionnez **Étendue** pour ouvrir le volet Filtre de ressources.

    Il est recommandé de ne demander l’accès qu’aux ressources dont vous avez besoin. Dans le volet Filtre de ressources, vous pouvez spécifier les groupes de ressources ou les ressources auxquels vous avez besoin d’accéder.

    ![Activer - Volet Filtre de ressources pour spécifier l’étendue](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Si nécessaire, spécifiez une heure de début personnalisée pour l’activation. Le membre sera activé après l’heure sélectionnée.

1. Dans la zone de texte **Raison**, entrez le motif de la demande d’activation.

    ![Volet Activer complètement renseigné comportant l’étendue, l’heure de début, la durée et le motif](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Sélectionnez **Activer**.

    Si [l’activation du rôle nécessite une approbation](pim-resource-roles-approval-workflow.md), une notification s’affiche dans le coin supérieur droit de votre navigateur pour vous informer que la demande est en attente d’approbation.

    ![La requête d’activation est en attente de la notification d’approbation](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="activate-a-role-with-arm-api"></a>Activer un rôle avec l’API ARM

Privileged Identity Management prend en charge les commandes de l’API Azure Resource Manager (ARM) pour gérer les rôles de ressources Azure, comme indiqué dans les informations de référence sur l’[API ARM PIM](/rest/api/authorization/roleeligibilityschedulerequests). Pour obtenir les autorisations requises pour utiliser l’API PIM, consultez [Comprendre les API de la Gestion de l’identité managée](pim-apis.md).

Voici un exemple de requête HTTP pour activer une attribution éligible pour un rôle Azure.

### <a name="request"></a>Requête

````HTTP
PUT https://management.azure.com/providers/Microsoft.Subscription/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/roleEligibilityScheduleRequests/64caffb6-55c0-4deb-a585-68e948ea1ad6?api-version=2020-10-01-preview
````

### <a name="request-body"></a>Corps de la demande

````JSON
{
  "properties": {
    "principalId": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
    "roleDefinitionId": "/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
    "requestType": "SelfActivate",
    "linkedRoleEligibilityScheduleId": "b1477448-2cc6-4ceb-93b4-54a202a89413",
    "scheduleInfo": {
      "startDateTime": "2020-09-09T21:35:27.91Z",
      "expiration": {
        "type": "AfterDuration",
        "endDateTime": null,
        "duration": "PT8H"
      }
    },
    "condition": "@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:ContainerName] StringEqualsIgnoreCase 'foo_storage_container'",
    "conditionVersion": "1.0"
  }
}
````

### <a name="response"></a>response

Code d’état : 201

````HTTP
{
  "properties": {
    "targetRoleAssignmentScheduleId": "c9e264ff-3133-4776-a81a-ebc7c33c8ec6",
    "targetRoleAssignmentScheduleInstanceId": null,
    "scope": "/providers/Microsoft.Subscription/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f",
    "roleDefinitionId": "/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
    "principalId": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
    "principalType": "User",
    "requestType": "SelfActivate",
    "status": "Provisioned",
    "approvalId": null,
    "scheduleInfo": {
      "startDateTime": "2020-09-09T21:35:27.91Z",
      "expiration": {
        "type": "AfterDuration",
        "endDateTime": null,
        "duration": "PT8H"
      }
    },
    "ticketInfo": {
      "ticketNumber": null,
      "ticketSystem": null
    },
    "justification": null,
    "requestorId": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
    "createdOn": "2020-09-09T21:35:27.91Z",
    "condition": "@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:ContainerName] StringEqualsIgnoreCase 'foo_storage_container'",
    "conditionVersion": "1.0",
    "expandedProperties": {
      "scope": {
        "id": "/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f",
        "displayName": "Pay-As-You-Go",
        "type": "subscription"
      },
      "roleDefinition": {
        "id": "/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
        "displayName": "Contributor",
        "type": "BuiltInRole"
      },
      "principal": {
        "id": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
        "displayName": "User Account",
        "email": "user@my-tenant.com",
        "type": "User"
      }
    }
  },
  "name": "fea7a502-9a96-4806-a26f-eee560e52045",
  "id": "/providers/Microsoft.Subscription/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/RoleAssignmentScheduleRequests/fea7a502-9a96-4806-a26f-eee560e52045",
  "type": "Microsoft.Authorization/RoleAssignmentScheduleRequests"
}
````

## <a name="view-the-status-of-your-requests"></a>Afficher l’état de vos demandes

Vous pouvez afficher l’état de vos demandes d’activation en attente.

1. Ouvrez Azure AD Privileged Identity Management.

1. Sélectionnez **Mes demandes** pour afficher la liste des demandes de vos rôles Azure AD et de rôles de ressources Azure.

    ![Mes demandes - Page des ressources Azure montrant vos demandes en attente](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Faites défiler vers la droite pour afficher la colonne **État de la demande**.

## <a name="cancel-a-pending-request"></a>Annuler une demande en attente

Si vous n’avez pas besoin de l’activation d’un rôle nécessitant une approbation, vous pouvez annuler une demande en attente à tout moment.

1. Ouvrez Azure AD Privileged Identity Management.

1. Sélectionnez **Mes demandes**.

1. Pour le rôle que vous souhaitez annuler, sélectionnez le lien **Annuler**.

    Lorsque vous sélectionnez Annuler, la requête est annulée. Pour réactiver le rôle, vous devez envoyer une nouvelle demande d’activation.

   ![Ma liste de demandes dans laquelle l’action d’annulation est mise en surbrillance](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Dépanner

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Les autorisations ne sont pas accordées après l’activation d’un rôle

Quand vous activez un rôle dans Privileged Identity Management, il est possible que l’activation ne se propage pas instantanément à tous les portails qui nécessitent le rôle privilégié. Parfois, même si la modification est propagée, son application immédiate peut être empêchée par la mise en cache web dans un portail. Si l’activation est retardée, voici les étapes à suivre.

1. Déconnectez-vous du portail Microsoft Azure, puis reconnectez-vous.
1. Dans Privileged Identity Management, vérifiez que vous êtes répertorié en tant que membre du rôle.

## <a name="next-steps"></a>Étapes suivantes

- [Étendre ou renouveler des rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Activer mes rôles Azure AD dans Privileged Identity Management](pim-how-to-activate-role.md)
