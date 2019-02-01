---
title: Approuver ou rejeter les demandes de rôles d’annuaire Azure AD dans PIM | Microsoft Docs
description: Découvrez comment approuver ou rejeter les demandes de rôles d’annuaire Azure AD dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/29/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 1c4b3819494ecb4c0897edeb08e2ce45d867b626
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55179070"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>Approuver ou rejeter les demandes de rôles d’annuaire Azure AD dans PIM

Avec Azure AD Privileged Identity Management (PIM), vous pouvez configurer des rôles afin d’exiger une approbation pour les activations. Vous pouvez également choisir un ou plusieurs utilisateurs ou groupes en tant qu’approbateurs délégués. Suivez les étapes décrites dans cet article pour approuver ou refuser des demandes de rôles d’annuaire Azure AD.

## <a name="view-pending-requests"></a>Afficher les demandes en attente

En tant qu’approbateur délégué, vous recevez une notification par e-mail quand une demande de rôle d’annuaire Azure AD est en attente d’approbation. Vous pouvez afficher les demandes en attente dans PIM.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Rôles d’annuaire Azure AD**.

1. Cliquez sur **Approuver les demandes**.

    ![Rôles d’annuaire Azure AD de PIM - Rôles](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    La liste des demandes en attente de votre approbation s’affiche.

## <a name="approve-requests"></a>Approuver les demandes

1. Sélectionnez les demandes que vous souhaitez approuver, puis cliquez sur **Approuver** pour ouvrir le volet Approuver des demandes sélectionnées.

    ![Liste des demandes d’approbation PIM](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Dans la zone **Approuver le motif**, tapez une raison.

    ![PIM – Approuver des demandes sélectionnées](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Cliquez sur **Approuver**.

    Le symbole d’état sera actualisé avec votre approbation.

    ![PIM – Approuver des demandes sélectionnées](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Refuser des demandes

1. Sélectionnez les demandes que vous souhaitez refuser, puis cliquez sur **Refuser** pour ouvrir le volet Refuser des demandes sélectionnées.

    ![Liste des demandes d’approbation PIM](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Dans la zone **Refuser le motif**, tapez une raison.

    ![PIM – Refuser des demandes sélectionnées](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Cliquez sur **Refuser**.

    Le symbole d’état sera actualisé avec votre refus.

## <a name="next-steps"></a>Étapes suivantes

- [Notifications par e-mail dans PIM](pim-email-notifications.md)
- [Approuver ou rejeter les demandes de rôles de ressources Azure dans PIM](pim-resource-roles-approval-workflow.md)
