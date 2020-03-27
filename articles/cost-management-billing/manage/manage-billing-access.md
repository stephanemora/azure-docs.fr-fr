---
title: Gérer l’accès à la facturation Azure
description: Découvrez comment donner accès à vos informations de facturation Azure aux membres de votre équipe.
author: vikramdesai01
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: e7a6f105c6c8292e8e95b388d60dd95c3548dbb5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79237664"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Gérer l’accès aux informations de facturation pour Azure

Vous pouvez permettre à d’autres utilisateurs d’accéder aux informations de facturation de votre compte dans le portail Microsoft Azure. Le type des rôles de facturation et les instructions permettant d’assurer l’accès aux informations de facturation varient selon le type de votre compte de facturation. Pour déterminer le type de votre compte de facturation, voir [Vérifier le type de votre compte de facturation](#check-the-type-of-your-billing-account).

Cet article s’applique aux clients disposant de comptes du programme de service en ligne Microsoft. Si vous êtes un client Azure avec un contrat Entreprise (EA) et que vous êtes l’administrateur de l’entreprise, vous pouvez accorder des autorisations aux administrateurs de services et aux propriétaires de compte dans le portail Entreprise. Pour plus d’informations, consultez [Comprendre les rôles d’administrateur Contrat Entreprise Azure dans Azure](understand-ea-roles.md). Si vous disposez d’un Contrat client Microsoft, consultez la section [Présentation des rôles d'administrateur Azure dans le cadre des Contrats client Microsoft](understand-mca-roles.md).

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Administrateurs des comptes de programme de service en ligne Microsoft

Un administrateur de compte est le seul propriétaire d’un compte de facturation du programme de service en ligne Microsoft. Le rôle est affecté à une personne qui s’inscrit dans Azure. Un administrateur de compte est autorisé à effectuer diverses tâches de facturation comme créer des abonnements, afficher des factures ou modifier la facturation d’un abonnement.

## <a name="give-others-access-to-view-billing-information"></a>Permettre à d’autres utilisateurs d’afficher des informations de facturation

Un administrateur de compte peut accorder à d’autres un accès aux informations de facturation Azure en attribuant l’un des rôles suivants à un abonnement de son compte.

- Administrateur de services
- Coadministrateur
- Propriétaire
- Contributeur
- Lecteur
- Lecteur de facturation

Ces rôles ont accès aux informations de facturation dans le [portail Microsoft Azure](https://portal.azure.com/). Les personnes qui reçoivent ces rôles peuvent également utiliser les [API Facturation](usage-rate-card-overview.md) pour obtenir par programmation des factures et des détails sur l’utilisation.

Pour attribuer des rôles, consultez [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](../../role-based-access-control/role-assignments-portal.md).

** Si vous êtes client EA, un propriétaire de compte peut affecter le rôle ci-dessus à d’autres utilisateurs de son équipe. Mais pour que ces utilisateurs puissent afficher des informations de facturation, l’administrateur d’entreprise doit permettre au propriétaire du compte d’afficher les frais dans le portail d’entreprise.


### <a name="allow-users-to-download-invoices"></a><a name="opt-in"></a> Autoriser les utilisateurs à télécharger des factures

Une fois que l’administrateur du compte a affecté les rôles appropriés aux autres utilisateurs, ces derniers doivent activer l’accès pour pouvoir télécharger des factures dans le portail Microsoft Azure. Les factures antérieures à décembre 2016 ne sont accessibles qu’à l’administrateur de compte.

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com/) en tant qu’administrateur de compte.

1. Effectuez une recherche sur **Gestion des coûts + facturation**.

    ![Capture d’écran montrant la recherche dans le portail Azure](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. Sélectionnez **Abonnements** dans le volet de gauche. Selon votre accès, vous devrez peut-être sélectionner une étendue de facturation, puis choisir **Abonnements**.

    ![Capture d’écran montrant comment sélectionner des abonnements](./media/manage-billing-access/billing-select-subscriptions.png)

1. Sélectionnez **Factures**, puis **Accéder aux factures**.

    ![Capture d’écran montrant comment déléguer l’accès aux factures](./media/manage-billing-access/aa-optin01.png)

1. Sélectionnez **Activé**, puis enregistrez.

    ![Capture d’écran montrant les options Activé-Désactivé pour déléguer l’accès aux factures](./media/manage-billing-access/aa-optinallow01.png)

L’administrateur de compte peut également être configuré afin de recevoir les factures par courrier électronique. Pour en savoir plus, consultez [Obtenir votre facture par courrier électronique](download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Donner accès en lecture seule à la facturation

Attribuez le rôle Lecteur de facturation à un utilisateur devant accéder en lecture seule aux informations de facturation d’abonnement, mais sans la possibilité de gérer ni de créer des services Azure. Ce rôle convient aux utilisateurs d’une organisation qui sont uniquement responsables de la gestion financière et des coûts pour des abonnements Azure.

La fonctionnalité Lecteur de facturation est en préversion et ne prend pas encore en charge les clouds non globaux.

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com/) en tant qu’administrateur de compte.

1. Effectuez une recherche sur **Gestion des coûts + facturation**.

    ![Capture d’écran montrant la recherche dans le portail Azure](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. Sélectionnez **Abonnements** dans le volet de gauche. Selon votre accès, vous devrez peut-être sélectionner une étendue de facturation, puis choisir **Abonnements**.

    ![Capture d’écran montrant comment sélectionner des abonnements](./media/manage-billing-access/billing-select-subscriptions.png)

1. Sélectionnez **Contrôle d’accès (IAM)** .
1. En haut de la page, sélectionnez **Ajouter**.

    ![Capture d'écran illustrant l’ajout d’une attribution de rôle](./media/manage-billing-access/billing-click-add-role-assignment.png)

1. Dans la liste déroulante **Rôle**, choisissez **Lecteur de facturation**.
1. Dans la zone de texte **Sélectionner**, tapez le nom ou l’e-mail de l’utilisateur que vous souhaitez ajouter.
1. Sélectionnez l’utilisateur.
1. Sélectionnez **Enregistrer**.
    ![Capture d’écran illustrant l’ajout d’une attribution de rôle](./media/manage-billing-access/billing-save-role-assignment.png)

1. Après quelques instants, le rôle Lecteur de facturation est attribué à l’utilisateur concernant l’abonnement.

** Si vous êtes un client EA, un propriétaire de compte ou administrateur de service peut affecter le rôle Lecteur de facturation aux membres de l’équipe. Mais pour que ce lecteur de facturation puisse afficher les informations de facturation du département ou compte, l’administrateur d’entreprise doit activer les stratégies **AO view charges** (Afficher les frais du propriétaire du compte) ou **DA view charges** (Afficher les frais de l’administrateur de service) dans le portail d’entreprise.

## <a name="check-the-type-of-your-billing-account"></a>Vérifier le type de votre compte de facturation
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Étapes suivantes

- Les utilisateurs d’autres rôles, tels que Propriétaire ou Collaborateur, peuvent non seulement accéder aux informations de facturation, mais également à des services Azure. Pour gérer ces rôles, consultez [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](../../role-based-access-control/role-assignments-portal.md).
- Pour plus d’informations sur les rôles, consultez [Rôles intégrés pour les ressources Azure](../../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).
