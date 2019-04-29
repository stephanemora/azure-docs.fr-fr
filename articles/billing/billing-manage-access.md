---
title: Gérer l’accès à la facturation Azure | Microsoft Docs
description: Découvrez comment donner accès à vos informations de facturation Azure aux membres de votre équipe.
services: ''
documentationcenter: ''
author: vikramdesai01
manager: amberb
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 8c2843840790d1e0dbfd4a789775c6c7ceb51a54
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60918521"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Gérer l’accès aux informations de facturation pour Azure

Pour la plupart des abonnements, vous pouvez donner aux membres de votre équipe l’accès aux informations de facturation à partir de l’onglet **Abonnements** du portail Azure. Si vous êtes un client Azure avec un contrat Entreprise (client EA) et que vous êtes l’administrateur de l’entreprise, vous pouvez accorder des autorisations aux administrateurs de services et aux propriétaires de compte dans le portail Entreprise.

## <a name="give-access-to-billing"></a>Donner accès à la facturation

À l’exception des clients EA, tous les utilisateurs peuvent accorder l’accès aux informations de facturation Azure en attribuant un des rôles d’utilisateur suivants aux membres de votre équipe :

- Administrateur de comptes
- Administrateur de services
- Coadministrateur
- Propriétaire
- Contributeur
- Lecteur
- Lecteur de facturation

Pour attribuer des rôles, consultez [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](../role-based-access-control/role-assignments-portal.md).

Ces rôles ont accès aux informations de facturation dans le [portail Azure](https://portal.azure.com/). Les personnes qui reçoivent ces rôles peuvent également utiliser les [API Facturation](billing-usage-rate-card-overview.md) pour obtenir par programmation des factures et des détails sur l’utilisation. Pour plus d’informations, consultez [Rôles dans le contrôle d’accès en fonction du rôle (RBAC) Azure](../role-based-access-control/built-in-roles.md).

### <a name="opt-in"></a> Autoriser les utilisateurs à télécharger des factures

Une fois que vous affectez les rôles appropriés aux membres de votre équipe, l’administrateur de compte devez activer l’accès à télécharger des factures dans le portail Azure. Les factures antérieures à décembre 2016 ne sont accessibles qu’à l’administrateur de compte.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
1. En tant qu’administrateur de compte, sélectionnez votre abonnement dans le [panneau Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) du portail Azure.

1. Sélectionnez **Factures**, puis **Accéder aux factures**.

    ![Capture d’écran montrant comment déléguer l’accès aux factures](./media/billing-manage-access/AA-optin.png)

1. Sélectionnez **Activé**, puis enregistrez.

    ![Capture d’écran montrant les options Activé-Désactivé pour déléguer l’accès aux factures](./media/billing-manage-access/AA-optinAllow.png)

L’administrateur de compte peut également être configuré afin de recevoir les factures par courrier électronique. Pour en savoir plus, consultez [Obtenir votre facture par courrier électronique](billing-download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Donner accès en lecture seule à la facturation

Attribuez le rôle Lecteur de facturation à un utilisateur devant accéder en lecture seule aux informations de facturation d’abonnement, mais sans la possibilité de gérer ni de créer des services Azure. Ce rôle convient aux utilisateurs d’une organisation qui sont uniquement responsables de la gestion financière et des coûts pour des abonnements Azure.

Si vous êtes un client EA, un propriétaire de compte ou administrateur de service peut affecter le rôle Lecteur de facturation aux membres de l’équipe. Mais pour que ce lecteur de facturation puisse afficher les informations de facturation du département ou compte, l’administrateur d’entreprise doit activer les stratégies **AO view charges** (Afficher les frais du propriétaire du compte) ou **DA view charges** (Afficher les frais de l’administrateur de service) dans le portail d’entreprise.

La fonctionnalité Lecteur de facturation est en préversion et ne prend pas encore en charge les clouds non globaux.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
1. Sélectionnez votre abonnement dans le [panneau Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) du portail Azure.

1. Sélectionnez **Contrôle d’accès (IAM)**.
1. Sélectionnez **Attributions de rôles** afin d’afficher toutes les attributions de rôles pour cet abonnement.
1. Sélectionnez **Ajouter une attribution de rôle**.
1. Dans la liste déroulante **Rôle**, choisissez **Lecteur de facturation**.
1. Dans la zone de texte **Sélectionner**, tapez le nom ou l’e-mail de l’utilisateur que vous souhaitez ajouter.
1. Sélectionnez l’utilisateur.
1. Sélectionnez **Enregistrer**.
1. Après quelques instants, le rôle Lecteur de facturation est attribué à l’utilisateur au niveau de l’abonnement.
1. Le lecteur de facturation reçoit un e-mail contenant un lien pour se connecter.

    ![Capture d’écran montrant ce que le lecteur de facturation peut voir dans le portail Azure](./media/billing-manage-access/billing-reader-view.png)

## <a name="allow-department-administrator-or-account-owner-billing-access"></a>Autoriser l’administrateur de service ou le propriétaire du compte à accéder à la facturation

L’administrateur d’entreprise peut autoriser les administrateurs de services et les propriétaires de comptes à afficher les détails d’utilisation et les coûts liés aux services et comptes qu’ils gèrent.

1. Connectez-vous en tant qu’administrateur d’entreprise au [portail EA](https://ea.azure.com/).
1. Sélectionnez **Gérer**.
1. Sous **Inscription**, définissez l’option **DA view charges** (Afficher les frais de l’administrateur de service) sur **Activé** pour autoriser l’administrateur de service à afficher les détails de l’utilisation et les coûts.
1. Définissez l’option **AO view charges** (Afficher les frais du propriétaire du compte) sur **activé** pour autoriser le propriétaire du compte à afficher les détails de l’utilisation et les coûts.


Pour plus d’informations, consultez [Comprendre les rôles administratifs Azure Enterprise Agreement dans Azure](billing-understand-ea-roles.md).

## <a name="only-account-admins-can-access-account-center"></a>Seuls les administrateurs de compte peuvent accéder au Centre des comptes

L’administrateur de compte est le propriétaire légal de l’abonnement. Par défaut, l’utilisateur qui s’est inscrit ou a acheté l’abonnement Azure est l’administrateur de compte, sauf si la [propriété de l’abonnement a été transférée](billing-subscription-transfer.md) à quelqu’un d’autre. L’administrateur de compte peut créer des abonnements, annuler des abonnements, modifier l’adresse de facturation d’un abonnement et gérer les stratégies d’accès de l’abonnement depuis le [Centre des comptes](https://account.azure.com/Subscriptions).

## <a name="next-steps"></a>Étapes suivantes

- Les utilisateurs d’autres rôles, tels que Propriétaire ou Collaborateur, peuvent non seulement accéder aux informations de facturation, mais également à des services Azure. Pour gérer ces rôles, consultez [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](../role-based-access-control/role-assignments-portal.md).
- Pour plus d’informations sur les rôles, consultez [Rôles intégrés pour les ressources Azure](../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créer une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).
