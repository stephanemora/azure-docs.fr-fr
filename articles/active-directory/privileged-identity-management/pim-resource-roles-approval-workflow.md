---
title: Approuver ou rejeter les demandes de rôles de ressources Azure dans PIM | Microsoft Docs
description: Découvrez comment approuver ou rejeter les demandes de rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
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
ms.date: 02/08/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a458f62ec38503e401b3fb810dc7edc8059a7715
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245573"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Approuver ou rejeter les demandes de rôles de ressources Azure dans PIM

Avec Azure AD Privileged Identity Management (PIM), vous pouvez configurer des rôles afin d’exiger une approbation pour les activations. Vous pouvez également choisir un ou plusieurs utilisateurs ou groupes en tant qu’approbateurs délégués. Les approbateurs délégués ont 24 heures pour approuver les demandes. Si une demande n’est pas approuvée sous 24 heures, l’utilisateur éligible doit soumettre une nouvelle demande. Le délai d'approbation de 24 heures n’est pas configurable.

Suivez les étapes décrites dans cet article pour approuver ou refuser des demandes de rôles de ressources Azure.

## <a name="view-pending-requests"></a>Afficher les demandes en attente

En tant qu’approbateur délégué, vous recevez une notification par e-mail quand une demande de rôle de ressource Azure est en attente d’approbation. Vous pouvez afficher les demandes en attente dans PIM.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Approuver les demandes**.

    ![Ressources Azure - Approuver les demandes](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    Dans la section **Demandes d’activations de rôles**, vous voyez la liste des demandes en attente d’approbation.

## <a name="approve-requests"></a>Approuver les demandes

1. Recherchez et cliquez sur la demande que vous souhaitez approuver. Un volet d’approbation s’affiche.

    ![Volet Approuver les demandes](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Dans la zone **Justification**, tapez une raison.

1. Cliquez sur **Approuver**.

    Une notification s’affiche avec votre approbation.

    ![Approuver une notification](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Refuser des demandes

1. Recherchez et cliquez sur la demande que vous souhaitez refuser. Un volet d’approbation s’affiche.

    ![Volet Approuver les demandes](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Dans la zone **Justification**, tapez une raison.

1. Cliquez sur **Refuser**.

    Une notification s’affiche avec votre refus.

## <a name="workflow-notifications"></a>Notifications de flux de travail

Voici quelques informations sur les notifications de flux de travail :

- Tous les membres de la liste des approbateurs sont avertis par courrier électronique lorsqu’une demande concernant un rôle est en attente de leur examen. Les notifications par e-mail comportent un lien direct vers la demande, grâce auquel l’approbateur peut approuver ou refuser cette demande.
- Les demandes sont traitées par le premier membre de la liste qui les approuve ou les refuse.
- Lorsqu’un approbateur répond à la demande, tous les membres de la liste des approbateurs sont informés de l’action.
- Les administrateurs de ressources sont avertis de l’activation d’un membre dans leur rôle.

>[!Note]
>Un administrateur de ressources qui estime qu’un membre approuvé ne doit pas être actif peut supprimer l’attribution de rôle active dans PIM. Bien que les administrateurs de ressources ne soient informés des demandes en attente que s’ils sont membres de la liste des approbateurs, ils peuvent voir et annuler les demandes en attente de tous les utilisateurs en les affichant dans PIM. 

## <a name="next-steps"></a>Étapes suivantes

- [Étendre ou renouveler des rôles de ressources Azure dans PIM](pim-resource-roles-renew-extend.md)
- [Notifications par e-mail dans PIM](pim-email-notifications.md)
- [Approuver ou rejeter les demandes de rôles d’annuaire Azure AD dans PIM](azure-ad-pim-approval-workflow.md)
