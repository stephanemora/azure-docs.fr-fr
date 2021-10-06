---
title: Approuver ou rejeter des demandes de rôles Azure AD dans PIM – Azure AD | Microsoft Docs
description: Découvrez comment approuver ou rejeter des demandes de rôles Azure AD dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/10/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4153e46df308a6682d3d21e509570a610ab61da7
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128707918"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Approuver ou rejeter des demandes de rôles Azure AD dans Privileged Identity Management

Avec Azure Active Directory (Azure AD) Privileged Identity Management (PIM), vous pouvez configurer des rôles afin d’exiger une approbation pour les activations. Vous pouvez également choisir un ou plusieurs utilisateurs ou groupes en tant qu’approbateurs délégués. Les approbateurs délégués ont 24 heures pour approuver les demandes. Si une demande n’est pas approuvée sous 24 heures, l’utilisateur éligible doit soumettre une nouvelle demande. Le délai d'approbation de 24 heures n’est pas configurable.

## <a name="view-pending-requests"></a>Afficher les demandes en attente

En tant qu’approbateur délégué, vous recevez une notification par e-mail quand une demande de rôle Azure AD est en attente d’approbation. Ces demandes en attente sont affichées dans Privileged Identity Management.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez **Approuver les demandes**.

    ![Approuver les demandes : page montrant une demande de révision des rôles Azure AD](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    Dans la section **Demandes d’activations de rôles**, vous voyez la liste des demandes en attente d’approbation.

## <a name="view-pending-requests-using-graph-api"></a>Voir les demandes en attente à l’aide de l’API Graph

### <a name="http-request"></a>Demande HTTP

````HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests/filterByCurrentUser(on='approver')?$filter=status eq 'PendingApproval' 
````

### <a name="http-response"></a>Réponse HTTP

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#Collection(unifiedRoleAssignmentScheduleRequest)", 
    "value": [ 
        { 
            "@odata.type": "#microsoft.graph.unifiedRoleAssignmentScheduleRequest", 
            "id": "9f2b5ddb-a50e-44a1-a6f4-f616322262ea", 
            "status": "PendingApproval", 
            "createdDateTime": "2021-07-15T19:57:17.76Z", 
            "completedDateTime": "2021-07-15T19:57:17.537Z", 
            "approvalId": "9f2b5ddb-a50e-44a1-a6f4-f616322262ea", 
            "customData": null, 
            "action": "SelfActivate", 
            "principalId": "d96ea738-3b95-4ae7-9e19-78a083066d5b", 
            "roleDefinitionId": "88d8e3e3-8f55-4a1e-953a-9b9898b8876b", 
            "directoryScopeId": "/", 
            "appScopeId": null, 
            "isValidationOnly": false, 
            "targetScheduleId": "9f2b5ddb-a50e-44a1-a6f4-f616322262ea", 
            "justification": "test", 
            "createdBy": { 
                "application": null, 
                "device": null, 
                "user": { 
                    "displayName": null, 
                    "id": "d96ea738-3b95-4ae7-9e19-78a083066d5b" 
                } 
            }, 
            "scheduleInfo": { 
                "startDateTime": null, 
                "recurrence": null, 
                "expiration": { 
                    "type": "afterDuration", 
                    "endDateTime": null, 
                    "duration": "PT5H30M" 
                } 
            }, 
            "ticketInfo": { 
                "ticketNumber": null, 
                "ticketSystem": null 
            } 
        } 
    ] 
} 
````

## <a name="approve-requests"></a>Approuver les demandes

1. Recherchez et sélectionnez la demande que vous souhaitez approuver. Une page Approuver ou rejeter s’affiche.

    ![Capture d’écran montrant la page « Approuver les demandes – Rôles Azure AD ».](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. Dans la zone **Justification**, entrez la justification professionnelle.

1. Sélectionnez **Approuver**. Vous allez recevoir une notification Azure de votre approbation.

    ![Notification d’approbation montrant que la demande a été approuvée](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

## <a name="approve-pending-requests-using-graph-api"></a>Approuver les demandes en attente à l’aide de l’API Graph

### <a name="get-ids-for-the-steps-that-require-approval"></a>Obtenir les ID des étapes qui nécessitent une approbation

Pour une demande d’activation spécifique, cette commande obtient toutes les étapes d’approbation nécessaires. Les approbations à plusieurs étapes ne sont actuellement pas prises en charge.

#### <a name="http-request"></a>Demande HTTP

````HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentApprovals/<request-ID-GUID> 
````

#### <a name="http-response"></a>Réponse HTTP

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentApprovals/$entity", 
    "id": "<request-ID-GUID>",
    "steps@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentApprovals('<request-ID-GUID>')/steps", 
    "steps": [ 
        { 
            "id": "<approval-step-ID-GUID>", 
            "displayName": null, 
            "reviewedDateTime": null, 
            "reviewResult": "NotReviewed", 
            "status": "InProgress", 
            "assignedToMe": true, 
            "justification": "", 
            "reviewedBy": null 
        } 
    ] 
} 
````

### <a name="approve-the-activation-request-step"></a>Approuver l’étape de demande d’activation

#### <a name="http-request"></a>Demande HTTP

````HTTP
PATCH 
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentApprovals/<request-ID-GUID>/steps/<approval-step-ID-GUID> 
{ 
    "reviewResult": "Approve", 
    "justification": "abcdefg" 
} 
 ````

#### <a name="http-response"></a>Réponse HTTP

Les appels PATCH réussis génèrent une réponse vide.

## <a name="deny-requests"></a>Refuser des demandes

1. Recherchez et sélectionnez la demande que vous souhaitez rejeter. Une page Approuver ou rejeter s’affiche.

    ![Approuver les demandes : volet Approuver ou refuser avec des détails et la zone Justification](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Dans la zone **Justification**, entrez la justification professionnelle.

1. Sélectionner **Rejeter**. Une notification s’affiche avec votre refus.

## <a name="workflow-notifications"></a>Notifications de flux de travail

Voici quelques informations sur les notifications de flux de travail :

- Les approbateurs sont avertis par courrier électronique lorsqu’une demande concernant un rôle est en attente de leur examen. Les notifications par e-mail comportent un lien direct vers la demande, grâce auquel l’approbateur peut approuver ou refuser cette demande.
- Les demandes sont traitées par le premier approbateur qui les approuve ou les rejette.
- Lorsqu’un approbateur répond à la demande, tous les approbateurs sont informés de l’action.
- Les Administrateurs généraux et les Administrateurs de rôle privilégié sont avertis de l’activation d’un utilisateur approuvé dans leur rôle.

>[!NOTE]
>Un Administrateur général ou un Administrateur de rôle privilégié qui estime qu’un utilisateur approuvé ne doit pas être actif peut supprimer l’attribution du rôle actif dans Privileged Identity Management. Bien que les administrateurs ne soient informés des demandes en attente que s’ils sont approbateurs, ils peuvent voir et annuler toute demande en attente des utilisateurs en affichant les demandes en attente dans Privileged Identity Management.

## <a name="next-steps"></a>Étapes suivantes

- [Notifications par e-mail dans Privileged Identity Management](pim-email-notifications.md)
- [Approuver ou rejeter des demandes de rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-approval-workflow.md)
