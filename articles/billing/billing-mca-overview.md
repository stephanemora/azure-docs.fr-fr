---
title: Prise en main de votre compte de facturation dans le cadre d'un Contrat client Microsoft - Azure | Microsoft Docs
description: Comprendre le compte de facturation associé à un contrat client Microsoft
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: banders
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: ea625a61ed600dbaa22fef85987e9570a6fb7dbc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371448"
---
# <a name="get-started-with-your-billing-account-for-a-microsoft-customer-agreement"></a>Prise en main de votre compte de facturation dans le cadre d'un Contrat client Microsoft

Un compte de facturation est créé pour chaque contrat que vous signez avec Microsoft pour utiliser Azure. Votre compte de facturation vous permet de gérer la facturation et de suivre les coûts. Vous pouvez accéder à plusieurs comptes de facturation. Par exemple, vous pouvez vous être inscrit à Azure pour vos projets personnels. Vous pouvez aussi accéder à Azure via le Contrat Entreprise de votre organisation ou le Contrat client Microsoft. Pour chacun de ces scénarios, vous disposez d'un compte de facturation distinct.

Cet article s’applique à un compte de facturation associé à un Contrat client Microsoft. [Vérifiez que vous avez accès à un Contrat client Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="understand-billing-account"></a>Comprendre le modèle de facturation

Votre compte de facturation pour le Contrat client Microsoft contient un ou plusieurs profils de facturation qui vous permettent de gérer vos factures et modes de paiement. Chaque profil de facturation contient une ou plusieurs sections de facture qui vous permettent d'organiser les coûts sur la facture du profil de facturation.

Le diagramme suivant montre la relation entre un compte de facturation, les profils de facturation et les sections de facture.

![Diagramme illustrant la hiérarchie de facturation pour un Contrat client Microsoft](./media/billing-mca-overview/mca-billing-hierarchy.png)

Les rôles du compte de facturation disposent du plus haut niveau d’autorisations. Par défaut, seuls les administrateurs généraux de l'instance Azure Active Directory de votre organisation accèdent au compte de facturation. Ces rôles sont à attribuer aux utilisateurs qui doivent consulter des factures et suivre les coûts pour toute votre organisation, comme les responsables des services financiers et informatiques. Pour plus d’informations, consultez [Rôles et tâches liés au compte de facturation](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="understand-billing-profiles"></a>Comprendre les profils de facturation

Utilisez un profil de facturation pour gérer vos factures et modes de paiement. Une facture mensuelle est générée pour les abonnements Azure et autres produits achetés par le biais du profil de facturation. Les modes de paiement vous permettent de payer la facture.

Un profil de facturation est automatiquement créé pour votre compte de facturation. Vous pouvez créer des profils de facturation pour configurer des factures supplémentaires. Par exemple, vous pouvez souhaiter différentes factures pour chaque service ou projet de votre organisation.

Vous pouvez également créer des sections de facture pour organiser les coûts sur une facture du profil de facturation. Les frais correspondant aux abonnements et produits Azure achetés pour une section de facture s'affichent dans la section. La facture du profil de facturation inclut les frais de toutes les sections de facture.

Les rôles associés aux profils de facturation sont autorisés à afficher et à gérer les factures et les modes de paiement. Attribuez ces rôles aux utilisateurs qui paient les factures tels que les membres du service de comptabilité de votre organisation. Pour plus d’informations, consultez [Rôles et tâches liés au profil de facturation](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="monthly-invoice-generated-for-each-billing-profile"></a>Facture mensuelle générée pour chaque profil de facturation

Une facture mensuelle est générée à la date de facture pour chaque profil de facturation. La facture contient tous les frais du mois précédent.

Dans le portail Azure, vous pouvez afficher la facture, télécharger des documents et modifier le paramètre pour obtenir les futures factures par e-mail. Pour plus d'informations, consultez [Télécharger les factures pour un Contrat client Microsoft](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoices-paid-through-payment-methods"></a>Factures payées par le biais des modes de paiement

Chaque profil de facturation a ses propres modes de paiement qui permettent de régler les factures associées. Les modes de paiement suivants sont prises en charge :

| Type             | Définition  |
|------------------|-------------|
|Crédits Azure    |  Les crédits sont automatiquement appliqués au montant total facturé de votre facture pour calculer le montant que vous devez payer. Pour plus d’informations, consultez [Suivre le solde du crédit Azure de votre profil de facturation](billing-mca-check-azure-credits-balance.md). |
|Chèque ou virement | Vous pouvez payer le montant dû de votre facture par chèque ou virement. Les instructions de paiement sont indiquées sur la facture |

### <a name="control-azure-marketplace-and-reservation-purchases-by-applying-policies"></a>Contrôler les achats de Place de marché Azure et de réservations en appliquant des stratégies

Appliquez des stratégies pour contrôler les achats effectués à l’aide d’un profil de facturation. Vous pouvez définir des stratégies pour désactiver l’achat de Réservations Azure et produits de la Place de marché. Lorsque les stratégies sont appliquées, les abonnements créés pour les sections de facture dans le profil de facturation ne peuvent pas être utilisés pour acheter des Réservations Azure et des produits de la Place de marché.

### <a name="allow-users-to-create-azure-subscriptions-by-enabling-azure-plans"></a>Autoriser les utilisateurs à créer des abonnements Azure en activant les plans Azure

Les plans Azure sont automatiquement activés lorsque vous créez un profil de facturation. Toutes les sections de facture du profil de facturation ont accès à ces plans. Les utilisateurs ayant accès à la section de facture utilisent les plans pour créer des abonnements Azure. Ils ne peuvent pas créer d'abonnements Azure, sauf si un plan Azure est activé pour le profil de facturation. Les plans Azure suivants sont pris en charge dans les comptes de facturation du Contrat client Microsoft :

| Planification             | Définition  |
|------------------|-------------|
|Offre Microsoft Azure   | Autorisez les utilisateurs à créer des abonnements pouvant exécuter n'importe quelle charge de travail. Pour plus d’informations, consultez [Offre Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/). |
|Offre Microsoft Azure pour Dev/Test | Autorisez les abonnés Visual Studio à créer des abonnements limités aux charges de travail de développement ou de test. Ces abonnements s'accompagnent d'avantages tels que des tarifs privilégiés et un accès à des images de machine virtuelle exclusives dans le portail Azure. Pour plus d’informations, consultez [Offre Microsoft Azure pour DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/).|

## <a name="understand-invoice-sections"></a>Comprendre les sections de facture

Créez des sections de facture pour organiser les coûts sur la facture d’un profil facturation. Par exemple, une seule facture peut suffire pour votre organisation, mais vous pouvez souhaitez organiser les coûts par service, équipe ou projet. Pour ce scénario, vous disposez d'un seul profil de facturation dans lequel vous créez une section de facture pour chaque service, équipe ou projet.

Lorsqu'une section de facture est créée, vous pouvez autoriser d’autres utilisateurs à créer des abonnements Azure pour la section. Les frais d'utilisation et les achats liés aux abonnements apparaissent ensuite dans la section qui convient de la facture.

Les rôles figurant dans la section de facture sont autorisés à contrôler qui crée des abonnements Azure. Attribuez ces rôles aux utilisateurs configurant l’environnement Azure pour les équipes de votre organisation, tels que les responsables d'ingénierie et les architectes techniques. Pour plus d’informations, consultez [Rôles et tâches liés aux sections de facture](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifier l’accès à un Contrat client Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour en savoir plus sur votre compte de facturation :

- [Présentation des rôles d'administrateur Azure dans le cadre des Contrats client Microsoft](billing-understand-mca-roles.md)
- [Créer un abonnement Azure associé dans le cadre de votre Contrat client Microsoft](billing-mca-create-subscription.md)
- [Créer des sections sur votre facture pour organiser vos coûts](billing-mca-section-invoice.md)