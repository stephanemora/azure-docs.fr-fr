---
title: Bien démarrer avec votre compte de facturation pour un contrat de client de Microsoft - Azure | Microsoft Docs
description: Comprendre le compte de facturation d’un contrat de client de Microsoft
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
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337411"
---
# <a name="get-started-with-your-billing-account-for-a-microsoft-customer-agreement"></a>Bien démarrer avec votre compte de facturation pour un contrat de client de Microsoft

Un compte de facturation est créé pour chaque contrat que vous vous connectez avec Microsoft à utiliser Azure. Votre compte de facturation vous permet de gérer la facturation et de suivre les coûts. Vous pouvez accéder à plusieurs comptes de facturation. Par exemple, peut avoir souscrit pour Azure pour vos projets personnels. Peut également avoir accès à Azure via un contrat entreprise ou le contrat de client de Microsoft de votre organisation. Pour chacun de ces scénarios, vous devez un compte de facturation distinct.

Cet article s’applique à un compte de facturation d’un contrat de client de Microsoft. [Vérifiez si vous avez accès à un contrat de client Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="understand-billing-account"></a>Comprendre le compte de facturation

Votre compte de facturation pour le contrat du client Microsoft contient un ou plusieurs profils de facturation qui vous permettent de gérer vos factures et les modes de paiement. Chaque profil de facturation contient une ou plusieurs sections de facture qui vous permettent d’organiser les coûts sur la facture de facturation du profil.

Le diagramme suivant montre la relation entre un compte de facturation, les profils de facturation et les sections de facture.

![Diagramme qui montre la hiérarchie de facturation pour un contrat de client de Microsoft](./media/billing-mca-overview/mca-billing-hierarchy.png)

Rôles sur le compte de facturation ont le plus haut niveau d’autorisations. Par défaut, seuls les administrateurs généraux sur Azure Active Directory votre organisation accéder au compte de facturation. Ces rôles doivent être assignés aux utilisateurs qui doivent afficher des factures et effectuer le suivi des coûts pour toute votre organisation, comme le service financier ou les responsables informatiques. Pour plus d’informations, consultez [les tâches et les rôles de compte de facturation](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="understand-billing-profiles"></a>Comprendre les profils de facturation

Utilisez un profil de facturation pour gérer vos méthodes de facturation et de paiement. Une facture mensuelle est générée pour les abonnements Azure et d’autres produits achetés à l’aide du profil de facturation. Les méthodes de paiements vous permet de payer la facture.

Un profil de facturation est automatiquement créé pour votre compte de facturation. Vous pouvez créer des profils de facturation pour configurer des factures supplémentaires. Par exemple, vous souhaite différentes factures pour chaque service ou d’un projet dans votre organisation.

Vous pouvez également créer des sections de facture pour organiser les coûts sur la facture d’un profil facturation. Des frais pour les produits achetés pour une section de facture et les abonnements Azure s’affichent dans la section. Facture de facturation du profil inclut les frais pour toutes les sections de la facture.

Les rôles sur les profils de facturation dispose des autorisations pour afficher et gérer des factures et des modes de paiement. Attribuer ces rôles aux utilisateurs qui paient factures comme membres de l’équipe de gestion des comptes dans votre organisation. Pour plus d’informations, consultez [tâches et rôles de profil de facturation](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="monthly-invoice-generated-for-each-billing-profile"></a>Facture mensuelle généré pour chaque profil de facturation

Une facture mensuelle est générée sur la date de facturation pour chaque profil de facturation. La facture contient tous les frais pour le mois précédent.

Vous pouvez afficher la facture, téléchargez des documents et modifier le paramètre pour obtenir les factures futures par courrier électronique, dans le portail Azure. Pour plus d’informations, consultez [télécharger les factures d’un contrat de client Microsoft](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoices-paid-through-payment-methods"></a>Payé par le biais des méthodes de paiement des factures

Chaque profil de facturation a ses propres méthodes de paiement sont utilisés pour payer ses factures. Les méthodes de paiement suivantes sont prises en charge :

| Type             | Définition  |
|------------------|-------------|
|Crédits Azure    |  Crédits sont automatiquement appliqués au montant total facturé sur votre facture pour calculer le montant que vous devez payer. Pour plus d’informations, consultez [suivre le solde du crédit Azure de votre profil de facturation](billing-mca-check-azure-credits-balance.md). |
|Virement par chèque ou bancaire | Vous pouvez payer le montant dû pour votre facture soit via la vérification ou un câble de transfert. Les instructions de paiement sont accordées sur la facture |

### <a name="control-azure-marketplace-and-reservation-purchases-by-applying-policies"></a>Achats de réservation et de contrôle Azure Marketplace en appliquant des stratégies

Appliquer des stratégies pour contrôler les achats effectués à l’aide d’un profil de facturation. Vous pouvez définir des stratégies pour désactiver l’achat de réservations d’Azure et produits de la place de marché. Lorsque les stratégies sont appliquées, les abonnements créés pour les sections de facture dans le profil de facturation ne peut pas servir à acheter des réservations d’Azure et produits de la place de marché.

### <a name="allow-users-to-create-azure-subscriptions-by-enabling-azure-plans"></a>Autoriser les utilisateurs à créer des abonnements Azure en activant les plans Azure

Les plans Azure sont automatiquement activées lorsque vous créez un profil de facturation. Toutes les sections de facture dans le profil de facturation ont accès à ces plans. Les utilisateurs ayant accès à la section de facture utilisent les plans pour créer des abonnements Azure. Ils ne peuvent pas créer des abonnements Azure, sauf si un plan Azure est activé pour le profil de facturation. Les plans Azure suivants sont pris en charge dans les comptes de facturation pour un contrat de client de Microsoft :

| Planification             | Définition  |
|------------------|-------------|
|Plan Microsoft Azure   | Autoriser les utilisateurs à créer des abonnements qui peuvent exécuter des charges de travail. Pour plus d’informations, consultez [Plan de Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|Plan Microsoft Azure pour Dev/Test | Autoriser les abonnés Visual Studio créer des abonnements qui sont limitées pour le développement ou le test des charges de travail. Ces abonnements bénéficiez des avantages tels que les taux inférieur et l’accès aux images des machines virtuelles exclusif dans le portail Azure. Pour plus d’informations, consultez [Microsoft Azure planifier DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)|

## <a name="understand-invoice-sections"></a>Comprendre les sections de facture

Créer des sections de facture pour organiser les coûts sur la facture d’un profil facturation. Par exemple, vous devrez peut-être une seule facture pour votre organisation, mais vous souhaitez organiser les coûts par département, l’équipe ou projet. Pour ce scénario, vous avez un seul profil de facturation où vous créez une section de facture pour chaque service, une équipe ou un projet.

Création d’une section de facture, vous pouvez autoriser d’autres utilisateurs à créer des abonnements Azure pour la section. Les frais d’utilisation et les achats pour les abonnements sont alors répercutées sur la section appropriée de la facture.

Rôles dans la section facture dispose des autorisations pour contrôler les utilisateurs qui crée des abonnements Azure. Attribuer ces rôles aux utilisateurs qui a configuré l’environnement Azure pour les équipes dans notre organisation, comme les responsables d’ingénierie et les architectes techniques. Pour plus d’informations, consultez [section rôles et tâches de facture](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifiez l’accès à un contrat de client de Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour en savoir plus sur votre compte de facturation :

- [Comprendre les rôles d’administrateur de contrat de client de Microsoft dans Azure](billing-understand-mca-roles.md)
- [Créer un nouvel abonnement Azure pour le contrat de client de Microsoft](billing-mca-create-subscription.md)
- [Créer des sections sur votre facture pour organiser vos coûts](billing-mca-section-invoice.md)