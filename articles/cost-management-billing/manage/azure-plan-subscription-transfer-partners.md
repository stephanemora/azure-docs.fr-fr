---
title: Transférer des abonnements Azure d’un partenaire vers un autre (préversion)
description: Cet article vous explique ce que vous devez savoir avant et après le transfert de la propriété de facturation de votre abonnement Azure.
author: bandersmsft
ms.reviewer: mcville
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: banders
ms.openlocfilehash: 246128ec3d26e4bf3f2a5a8f0660aab7d9daca3d
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89554196"
---
# <a name="transfer-azure-subscriptions-from-one-partner-to-another-preview"></a>Transférer des abonnements Azure d’un partenaire vers un autre (préversion)

Cet article vous explique ce que vous devez savoir avant et après le transfert de la propriété de facturation de votre abonnement Azure. Pour lancer le transfert d’un abonnement Azure relevant d’un plan Azure d’un partenaire Microsoft vers un autre, vous devez contacter votre partenaire. Le partenaire vous indiquera comment procéder. À l’issue du transfert, la propriété de facturation de votre abonnement a changé.

## <a name="user-access"></a>Accès utilisateur

L’accès aux utilisateurs, groupes ou principaux de service existants auxquels a été affecté le contrôle d’accès en fonction du rôle Azure (RBAC) n’est pas affecté pendant la transition. [Azure RBAC](../../role-based-access-control/overview.md) vous permet de gérer qui a accès aux ressources Azure, ce que ces utilisateurs peuvent en faire ainsi que les zones auxquelles ils ont accès. Avec le transfert de l’abonnement, aucun accès RBAC à vos ressources n’est fourni votre nouveau partenaire. Votre précédent partenaire conserve son accès RBAC.

Par conséquent, il importe de supprimer l’accès Azure RBAC de l’ancien partenaire et d’ajouter un accès pour le nouveau partenaire. Pour plus d’informations sur l’attribution d’un accès à votre nouveau partenaire, consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle Azure (Azure RBAC) ?](../../role-based-access-control/overview.md). Pour plus d’informations sur la suppression de l’accès RBAC de votre partenaire précédent, consultez [Supprimer une attribution de rôle](../../role-based-access-control/role-assignments-portal.md#remove-a-role-assignment).

Par ailleurs, votre nouveau partenaire ne bénéficie pas automatiquement d’un accès [Administrateur pour le compte de (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) à vos abonnements. Votre partenaire a besoin d’un accès AOBO pour gérer les abonnements Azure en votre nom. Pour plus d’informations sur la façon d’attribuer un accès AOBO à votre nouveau partenaire, consultez [Transférer la propriété de facturation d’un compte d’abonnement Azure vers un autre compte](billing-subscription-transfer.md).

## <a name="stop-a-transfer"></a>Arrêter un transfert

Après avoir reçu la confirmation qu’une demande de transfert a bien été soumise, vous pouvez utiliser l’une des options suivantes **si vous ne voulez pas que le transfert se poursuive**.

- Si la demande n’a pas encore été acceptée par votre partenaire actuel, vous pouvez demander à votre nouveau partenaire d’annuler la demande de transfert qu’il a initiée (quand l’état est en attente).
- Demandez au partenaire actuel de ne rien faire quand il recevra la demande de transfert. Sans le consentement de votre partenaire actuel, le transfert ne peut pas continuer. La demande expirera.
- Vous pouvez _supprimer votre relation de revendeur_ avec le nouveau partenaire. Cette action vous retire la possibilité de déplacer votre abonnement. Elle annule de fait la demande.

Vous pouvez aussi rechercher de l’aide, signaler un manquement ou une activité suspecte à partir d’une des options proposées sur le site web [Microsoft Legal](https://www.microsoft.com/legal/). L’option permettant de signaler un problème (« Report a concern ») se trouve sous Compliance & Ethics.

## <a name="next-steps"></a>Étapes suivantes

- Pour accorder un accès RBAC à votre nouveau partenaire, consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle Azure (Azure RBAC) ?](../../role-based-access-control/overview.md).
- Pour accorder un accès AOBO à votre nouveau partenaire, consultez [Transférer la propriété de facturation d’un compte d’abonnement Azure vers un autre compte](billing-subscription-transfer.md).