---
title: Créer des sections de facture pour organiser les coûts - Azure
description: Découvrez comment organiser les coûts avec des sections de facture.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ff8b2da353d623cd9f05c8d0b0317587d7093ce3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75389279"
---
# <a name="create-sections-on-your-invoice-to-organize-your-costs"></a>Créer des sections sur votre facture pour organiser vos coûts

Créez des sections dans votre facture pour organiser les coûts par département, par environnement de développement ou en fonction des besoins de votre organisation. Autorisez ensuite d’autres utilisateurs à créer des abonnements Azure facturés dans la section. Les frais d’utilisation et les achats liés aux abonnements sont ensuite facturés en fonction de la section. Vous pouvez afficher le total des frais de la section sur votre facture, dans le portail Microsoft Azure, ou les vérifier dans l’analyse des coûts Azure. Pour plus d’informations, consultez [Voir les transactions par section de facture](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

Cet article s'applique à un compte de facturation associé à un contrat client Microsoft. [Vérifiez que vous avez accès à un contrat client Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="create-an-invoice-section-in-the-azure-portal"></a>Créer une section de facture dans le portail Azure

Pour créer une section de facture, vous devez être **propriétaire de profil de facturation** ou **contributeur de profil de facturation**. Pour plus d’informations, consultez [Gérer les sections de facture associées au profil de facturation](billing-understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Recherchez **Gestion des coûts + facturation**.

   ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-mca-section-invoice/billing-search-cost-management-billing.png)

3. Sélectionnez **Sections de facture** dans le volet de gauche. Selon votre accès, vous devrez peut-être sélectionner un compte ou profil de facturation, puis sélectionner **Sections de facture**.

   ![Capture d’écran montrant la liste des sections de la facture](./media/billing-mca-section-invoice/mca-select-invoice-sections.png)

4. En haut de la page, sélectionnez **Ajouter**.

5. Entrez un nom pour la section de la facture, puis sélectionnez un profil de facturation. La section de cette facture du profil de facturation reflète l’utilisation de chaque abonnement et les achats que vous avez affectés à la section.

   ![Capture d’écran montrant la page de création de la section de facture](./media/billing-mca-section-invoice/mca-create-invoice-section.png)

6. Sélectionnez **Create** (Créer).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifier l'accès à un contrat client Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes

- [Créer un abonnement Azure associé dans le cadre de votre Contrat client Microsoft](billing-mca-create-subscription.md)
- [Gérer les rôles de facturation dans le portail Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Obtenir la propriété de facturation des abonnements des utilisateurs d’autres comptes de facturation](billing-mca-request-billing-ownership.md)
