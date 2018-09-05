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
ms.component: pim
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7bf1e437e97fdb4d929af23bd7b2a9abb49268df
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189156"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>Approuver ou rejeter les demandes de rôles d’annuaire Azure AD dans PIM

Avec Privileged Identity Management, vous pouvez configurer des rôles afin d’exiger une approbation pour les activations. En outre, vous pouvez choisir un ou plusieurs utilisateurs, ou groupes, comme approbateurs délégués.

## <a name="view-pending-approvals-requests"></a>Afficher les approbations (demandes) en attente

En tant qu’approbateur délégué, vous recevez une notification par courrier électronique lorsqu’une demande est en attente d’approbation. Pour afficher ces requêtes dans le portail PIM, dans le tableau de bord (dans la nouvelle navigation), sélectionnez l’onglet « Demandes d’approbation en attente » dans la barre de navigation de gauche.

![](media/azure-ad-pim-approval-workflow/image023.png)

Une liste des demandes d’approbation en attente s’affiche ici :

![](media/azure-ad-pim-approval-workflow/image024.png)

## <a name="approve-or-deny-requests-for-role-elevation-single-andor-bulk"></a>Approuver ou rejeter des demandes d’élévation de rôle (unique ou en bloc)

Sélectionnez les demandes que vous souhaitez approuver ou refuser, puis cliquez sur le bouton dans la barre d’action qui correspond à votre décision :

![](media/azure-ad-pim-approval-workflow/image025.png)

## <a name="provide-justification-for-my-approvaldenial"></a>Fournir une justification pour l’approbation ou le rejet

Un nouveau panneau s’ouvre, dans lequel vous pouvez approuver ou refuser plusieurs demandes simultanément. Entrez une justification de votre choix, puis cliquez sur Approuver (ou Refuser) au bas du panneau :

![](media/azure-ad-pim-approval-workflow/image029.png)

Une fois le processus de demande terminé, le symbole d’état reflète la décision que vous avez prise (dans cet exemple, Approuver) :

![](media/azure-ad-pim-approval-workflow/image031.png)

## <a name="next-steps"></a>Étapes suivantes

- [Approuver ou rejeter les demandes de rôles de ressources Azure dans PIM](pim-resource-roles-approval-workflow.md)
- [Notifications par e-mail dans PIM](pim-email-notifications.md)
