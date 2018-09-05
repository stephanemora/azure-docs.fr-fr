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
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e5cda31af5ac95e7ebe2b858b1d7355ea3f2a6bb
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189802"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Approuver ou rejeter les demandes de rôles de ressources Azure dans PIM

Pour approuver ou refuser une demande, vous devez être un membre de la liste des approbateurs. 

1. Dans PIM, sélectionnez **Approuver les demandes** sous l’onglet du menu de gauche et recherchez la demande.

   ![Volet « Approuver des requêtes »](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. Sélectionnez la demande, justifiez la décision et sélectionnez **Approuver** ou **Refuser**. La demande est alors résolue.

   ![Demande sélectionnée avec des informations détaillées](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Notifications de flux de travail

Voici quelques éléments concernant les notifications de flux de travail :

- Tous les membres de la liste des approbateurs sont avertis par courrier électronique lorsqu’une demande concernant un rôle est en attente de leur examen. Les notifications par e-mail comportent un lien direct vers la demande, grâce auquel l’approbateur peut approuver ou refuser cette demande.
- Les demandes sont traitées par le premier membre de la liste qui les approuve ou les refuse. 
- Lorsqu’un approbateur répond à la demande, tous les membres de la liste des approbateurs sont informés de l’action. 
- Les administrateurs de ressources sont avertis de l’activation d’un membre dans leur rôle. 

>[!Note]
>Un administrateur de ressources qui estime qu’un membre approuvé ne doit pas être actif peut supprimer l’attribution de rôle active dans PIM. Bien que les administrateurs de ressources ne soient informés des demandes en attente que s’ils sont membres de la liste des approbateurs, ils peuvent voir et annuler les demandes en attente de tous les utilisateurs en les affichant dans PIM. 

## <a name="next-steps"></a>Étapes suivantes

- [Approuver ou rejeter les demandes de rôles d’annuaire Azure AD dans PIM](azure-ad-pim-approval-workflow.md)
- [Notifications par e-mail dans PIM](pim-email-notifications.md)
