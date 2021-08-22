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
ms.date: 06/03/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ffb9e53172835ddcec574802f10a4a4bbbea0e1
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112233058"
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

## <a name="approve-requests"></a>Approuver les demandes

1. Recherchez et sélectionnez la demande que vous souhaitez approuver. Une page Approuver ou rejeter s’affiche.

    ![Capture d’écran montrant la page « Approuver les demandes – Rôles Azure AD ».](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. Dans la zone **Justification**, entrez la justification professionnelle.

1. Sélectionnez **Approuver**. Vous allez recevoir une notification Azure de votre approbation.

    ![Notification d’approbation montrant que la demande a été approuvée](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

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
