---
title: Effectuer des tâches de contrat entreprise dans le contrat du client Microsoft - Azure | Microsoft Docs
description: Découvrez comment effectuer des tâches dans votre nouveau compte de facturation accord entreprise.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/24/2018
ms.author: banders
ms.openlocfilehash: 9b76fa935263904957c87cd062c84d0607771369
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371329"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Effectuer des tâches de contrat entreprise dans votre compte de facturation d’un contrat de client de Microsoft

Si votre organisation a signé un contrat de client Microsoft pour renouveler votre inscription contrat entreprise, un nouveau compte de facturation est créé pour l’accord. La facturation dans votre nouveau compte est organisée différemment de votre contrat entreprise. Cet article décrit comment vous pouvez utiliser le nouveau compte de facturation pour effectuer les tâches que vous avez effectuées dans votre contrat entreprise.

## <a name="how-billing-is-organized-in-the-new-account"></a>Comment la facturation est organisée dans le nouveau compte

Le diagramme suivant décrit comment la facturation est organisée dans votre nouveau compte de facturation.

![Image de la hiérarchie du transition post ea mca](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

| Contrat Entreprise   | Contrat client Microsoft    |
|------------------------|--------------------------------------------------------|
| Inscription            | Vous utilisez un profil de facturation pour gérer la facturation pour votre organisation, similaire à l’inscription de votre contrat entreprise. Les administrateurs d’entreprise deviennent propriétaires du profil de facturation. Pour en savoir plus sur les profils de facturation, consultez [comprendre les profils de facturation](billing-mca-overview.md#understand-billing-profiles).
| department            | Vous utilisez une section de facture pour organiser vos coûts, similaires aux départements dans votre inscription contrat entreprise. Service devient sections de facture et aux administrateurs de service deviennent propriétaires des sections de facture respectifs. Pour en savoir plus sur les sections de facture, consultez [comprendre les sections de facture](billing-mca-overview.md#understand-invoice-sections). |
| Compte               | Les comptes qui ont été créés dans votre contrat entreprise ne sont pas pris en charge dans le nouveau compte de facturation. Abonnements du compte appartiennent à la section facture respectifs pour leur département. Les propriétaires de compte peuvent créer et gérer des abonnements pour leurs sections de facture. |

## <a name="changes-for-enterprise-administrators-in-the-new-billing-account"></a>Modifications pour les administrateurs d’entreprise dans le nouveau compte de facturation

Les modifications suivantes s’appliquent aux administrateurs d’entreprise sur un contrat entreprise qui a été renouvelé pour un contrat de client de Microsoft.

- Un profil de facturation est créé pour votre inscription. Le profil de facturation vous permet de gérer la facturation pour votre organisation, telles que l’inscription de votre contrat entreprise. Pour en savoir plus sur les profils de facturation, [comprendre les profils de facturation](billing-mca-overview.md#understand-billing-profiles).
- Une section de la facture est créée pour chaque service dans votre inscription contrat entreprise. Les sections de facture vous permet de gérer vos services. Vous pouvez créer de nouvelles sections de facture pour configurer des services supplémentaires. Pour en savoir plus sur les sections de facture, consultez [comprendre les sections de facture](billing-mca-overview.md#understand-invoice-sections).
- Vous utiliserez le rôle de créateur d’abonnement Azure sur les sections de facture pour permettre à d’autres pour créer un abonnement Azure, tels que les comptes qui ont été créés dans l’accord entreprise.
- Vous utiliserez le [Azure portal](https://portal.azure.com) pour gérer la facturation pour votre organisation, au lieu du portail Azure EA.

Vous obtenez les rôles suivants sur le nouveau compte de facturation :

**Propriétaire de profil de facturation** -vous sont affectés au rôle de propriétaire de profil facturation sur le profil de facturation qui a été créé lors de la signature de l’accord. Le rôle vous permet de gérer la facturation pour votre organisation. Vous pouvez afficher les frais et les factures, organiser les coûts sur la facture, gérer les modes de paiement et contrôler l’accès à la facturation de votre organisation.

**Propriétaire de la section facture** -vous sont affectés au rôle de propriétaire de section de facture sur toutes les sections de facture sont créées pour les départements de votre inscription contrat entreprise. Le rôle vous permet de contrôler qui peut créer des abonnements Azure et acheter d’autres produits.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>Afficher le solde de frais et les crédits pour votre organisation

Votre profil de facturation vous permet d’effectuer le suivi de la facturation et le solde de crédits Azure pour votre organisation similaire à l’inscription de votre contrat entreprise.

Pour savoir comment afficher le solde de crédit pour un profil de facturation, consultez [suivre le solde du crédit Azure de votre profil de facturation](billing-mca-check-azure-credits-balance.md).

Pour savoir comment afficher les frais pour un profil de facturation, consultez [comprendre les frais de facture de votre contrat client Microsoft](billing-mca-understand-your-bill.md).

### <a name="view-charges-for-a-department"></a>Afficher les frais pour un département

Une section de la facture est créée pour chaque service que vous aviez dans votre contrat entreprise. Vous pouvez afficher les frais d’une section de facture dans le portail Azure. Pour plus d’informations, consultez [afficher les transactions en sections de facture](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Afficher les frais d’un compte

Les comptes qui ont été créés dans votre inscription contrat entreprise ne sont pas pris en charge dans le nouveau compte de facturation. Abonnements du compte appartiennent à la section facture respectifs pour leur département. Les propriétaires de compte peuvent créer et gérer des abonnements pour leurs sections de facture.

Pour afficher le coût cumulé pour les abonnements appartenant à un compte, vous devez définir un centre de coût pour chaque abonnement. Vous pouvez ensuite utiliser le fichier csv utilisation et votre facturation Azure pour filtrer les abonnements par le centre de coût.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Télécharger l’utilisation et frais csv, table de tarification et des documents fiscaux

Une facture mensuelle est générée pour chaque profil de facturation dans votre compte de facturation. Pour chaque facture, vous pouvez télécharger les fichiers de volume partagé de cluster l’utilisation et frais Azure, table de tarification et document de taxe (le cas échéant). Vous pouvez également télécharger des fichiers csv Azure utilisation et des frais pour les frais du mois en cours.

Pour savoir comment télécharger des fichiers de volume partagé de cluster utilisation et votre facturation Azure, consultez [télécharger l’utilisation de votre contrat de client Microsoft](billing-download-azure-daily-usage.md#download-usage-for-your-microsoft-customer-agreement).

Pour savoir comment télécharger la table de tarification, consultez [télécharger la tarification de votre contrat de client Microsoft](billing-ea-pricing.md#view-and-download-pricing-for-your-microsoft-customer-agreement).

Pour savoir comment télécharger des documents de taxes, consultez [afficher les documents de taxe de votre contrat de client Microsoft](billing-mca-download-tax-document.md#view-and-download-tax-documents).

### <a name="add-an-additional-enterprise-administrator"></a>Ajouter un administrateur d’entreprise supplémentaires

Donner aux utilisateurs d’accéder au profil de facturation pour leur permettre de vue et de gérer la facturation pour votre organisation. Vous pouvez utiliser la **contrôle d’accès (IAM)** page dans le portail Azure pour accorder l’accès.  Pour en savoir plus sur les rôles de profil de facturation, consultez [tâches et rôles de profil de facturation](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

Pour savoir comment fournir, accéder à votre profil de facturation, consultez [gérer les rôles de facturation dans le portail Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Créer un nouveau service

Créez une section de facture pour organiser vos coûts en fonction de vos besoins, comme les départements de votre inscription contrat entreprise. Vous pouvez créer une nouvelle section de facture dans le portail Azure. Pour plus d’informations, consultez [créer des sections sur votre facture pour organiser vos coûts](billing-mca-section-invoice.md).

### <a name="create-a-new-account"></a>Créer un nouveau compte

Affecter des utilisateurs le rôle de créateur d’abonnement Azure sur des sections de facture pour leur donner l’autorisation pour créer un abonnement Azure, tels que les comptes qui sont créés dans l’accord entreprise. Pour plus d’informations, consultez [autoriser d’autres utilisateurs à créer des abonnements Azure](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

## <a name="changes-for-department-administrators-in-the-new-billing-account"></a>Modifications pour les administrateurs de service dans le nouveau compte de facturation

Les modifications suivantes s’appliquent aux administrateurs de service sur un contrat entreprise qui a été renouvelé pour un contrat de client de Microsoft.

- Une section de la facture est créée pour chaque service dans votre inscription contrat entreprise. Les sections de facture vous permet de gérer votre ou les services. Pour en savoir plus sur les sections de facture, consultez [comprendre les sections de facture](billing-mca-overview.md#understand-invoice-sections).
- Vous utiliserez le rôle de créateur d’abonnement Azure sur la section de facture pour permettre à d’autres pour créer un abonnement Azure, tels que les comptes qui sont créés dans l’accord entreprise.
- Le portail Azure vous permet de gérer la facturation pour votre organisation, au lieu du portail Azure EA.

Vous obtenez les rôles suivants sur le nouveau compte de facturation :

**Propriétaire de la section facture** -vous sont affectés au rôle de propriétaire de section de facture sur la section de facture qui est créé pour l’ou les services que vous aviez dans le contrat entreprise. Le rôle vous permet de vue et les frais de suivre et de contrôler qui peut créer des abonnements Azure et acheter d’autres produits de la section de la facture.

### <a name="view-charges-for-your-department"></a>Afficher les frais pour votre département

Vous pouvez afficher les frais pour la section de facture qui est créé pour votre département, dans le portail Azure [gestion des coûts + facturation page](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).

### <a name="add-an-additional-department-administrator"></a>Ajouter un administrateur de service supplémentaires

Une section de la facture est créée pour chaque service que vous aviez dans votre contrat entreprise. Vous pouvez utiliser la **(IAM) de l’accès** page dans le portail Azure pour que d’autres personnes d’accès pour afficher et gérer la section de la facture. Pour en savoir plus sur les rôles de section de facture, consultez [section rôles et tâches de facture](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Pour savoir comment fournir, accéder à votre section de facture, consultez [gérer les rôles de facturation dans le portail Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Créer un nouveau compte de votre service

Affecter des utilisateurs du rôle de créateur d’abonnement Azure sur section facture qui est créé pour votre service. Pour plus d’informations, consultez [autoriser d’autres utilisateurs à créer des abonnements Azure](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

### <a name="view-charges-for-accounts-in-your-departments"></a>Afficher les frais pour les comptes dans vos services

Les comptes qui ont été créés dans votre inscription contrat entreprise ne sont pas pris en charge dans le nouveau compte de facturation. Abonnements du compte appartiennent à la section facture respectifs pour leur département. Les propriétaires de compte peuvent créer et gérer des abonnements pour leurs sections de facture.

Pour afficher le coût cumulé pour les abonnements appartenant à un compte de votre service, vous devez définir un centre de coût pour chaque abonnement. Vous pouvez ensuite utiliser le fichier d’utilisation et votre facturation Azure pour filtrer les abonnements par le centre de coût.

## <a name="changes-for-account-owners-in-the-new-billing-account"></a>Modifications pour les propriétaires de compte dans le nouveau compte de facturation

Les propriétaires de comptes sur le contrat entreprise obtient l’autorisation de créer des abonnements Azure sur le nouveau compte de facturation. Vos abonnements Azure existants appartiennent à la section de facture qui est créée pour votre service. Si votre compte n’appartient pas à un service, vos abonnements appartiennent à une section de facture nommée section de facture par défaut.

Pour créer des abonnements Azure supplémentaires, vous obtenez le rôle suivant sur le nouveau compte de facturation.

**Créateur de l’abonnement Azure** -vous êtes affecté le rôle de créateur d’abonnement azure dans la section de facture qui est créé pour votre service dans le contrat entreprise. Si votre compte n’appartient pas à un service, vous obtenez le rôle de créateur d’abonnement Azure sur une section nommée section de facture par défaut. Le rôle vous permet de créer des abonnements Azure pour la section de la facture.

### <a name="create-an-azure-subscription"></a>Crée un abonnement Azure

Vous pouvez créer des abonnements Azure pour votre section de facture dans le portail Azure. Pour plus d’informations, consultez [créer un nouvel abonnement Azure pour le contrat de client de Microsoft](billing-mca-create-subscription.md)

### <a name="view-charges-for-your-account"></a>Afficher les frais pour votre compte

Pour afficher les frais relatifs aux abonnements appartenant à un compte, accédez à la [page abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) dans le portail Azure. La page Abonnements affiche les frais pour tous les votre abonnement.

### <a name="view-charges-for-a-subscription"></a>Afficher les frais pour un abonnement

Vous pouvez afficher les frais pour un abonnement soit sur le [page abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou d’analyse des coûts Azure. Pour plus d’informations sur l’analyse des coûts Azure, consultez [Explorer et analyser les coûts avec analyse des coûts](../cost-management/quick-acm-cost-analysis.md).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes

- [Comprendre le compte de facturation d’un contrat de client de Microsoft](billing-mca-overview.md)
- [Comprendre votre facture](billing-understand-your-bill.md)
- [Comprendre votre facture](billing-understand-your-invoice.md)
- [Obtenir la propriété des abonnements Azure à partir d’autres utilisateurs de facturation](billing-mca-request-billing-ownership.md)
