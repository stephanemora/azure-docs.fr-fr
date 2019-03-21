---
title: Comprendre les frais de facture de votre contrat de client de Microsoft - Azure | Microsoft Docs
description: Découvrez comment lire et comprendre les frais sur votre facture
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: f93152ae3db926fb989c219d1e515abaf0281bf4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837879"
---
# <a name="understand-the-charges-on-your-microsoft-customer-agreements-invoice"></a>Comprendre les frais de facture de votre contrat client de Microsoft

Vous pouvez comprendre les frais sur votre facture en analysant les transactions individuelles.

Dans le compte de facturation d’un contrat de client de Microsoft, une facture est générée chaque mois pour chaque profil de facturation. La facture inclut tous les frais du mois précédent. Vous pouvez afficher vos factures dans le portail Azure. Pour plus d’informations, consultez [télécharger les factures d’un contrat de client Microsoft](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

Cet article s’applique à un compte de facturation d’un contrat de client de Microsoft. [Vérifiez si vous avez accès à un contrat de client Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Afficher les transactions d’une facture dans le portail Azure

1. Connectez-vous au [Portail Azure](https://www.azure.com).

2. Effectuez une recherche sur **coût Management + facturation**.

    ![Capture d’écran montrant la recherche du portail Azure pour la gestion des coûts + facturation](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Sélectionnez **toutes les transactions** sur le côté gauche de l’écran. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation ou d’un profil de facturation, puis sélectionnez **toutes les transactions**.

4. La page toutes les transactions affiche les informations suivantes :

    ![Capture d’écran montrant la liste des opérations de facturation](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |Colonne  |Définition  |
    |---------|---------|
    |Date     | La date de transaction  |
    |ID de la facture     | L’identificateur de la facture sur lequel la transaction a été facturée. Si vous envoyez une demande de support, partager l’ID avec le support Azure pour accélérer votre demande de support |
    |Type de transaction     |  Le type de transaction tels que des frais d’achat, Annuler et l’utilisation  |
    |Famille de produits     | La catégorie de produit, tel que le calcul pour les machines virtuelles ou de la base de données pour la base de données SQL Azure|
    |Référence (SKU) de produit     | Un code unique identifiant l’instance de votre produit |
    |Montant     |  Le montant de transaction      |
    |Section de facture     | La transaction s’affiche sur cette section de la facturation du profil |
    |Profil de facturation     | La transaction s’affiche sur la facture de ce profil facturation |

5. Recherchez les ID de facture pour filtrer les transactions pour la facture.

### <a name="view-transactions-by-invoice-sections"></a>Afficher les transactions en sections de facture

Les sections de facture vous permettent d’organiser les coûts sur la facture d’un profil facturation. Pour plus d’informations, consultez [comprendre facture section](billing-mca-overview.md#understand-invoice-sections). Quand une facture est générée pour reflètent les frais pour toutes les sections dans le profil de facturation sur la facture.

L’illustration suivante montre les frais de la section de facture Dept comptabilité sur un exemple de facture.

![Image d’un exemple présentant les détails par les informations de section de facture](./media/billing-understand-your-bill-mca/invoicesection-details.png)

Une fois que vous avez identifié les frais d’une section de facture, vous pouvez afficher les transactions dans le portail Azure pour comprendre les frais.

1. Accédez à la page de toutes les transactions dans le portail Azure pour afficher les transactions d’une facture. Pour plus d’informations, consultez [afficher les transactions d’une facture dans le portail Azure](#view-transactions-for-an-invoice-in-the-azure-portal).

2. Filtrer par nom de section de facture pour consulter les transactions de la section de la facture.

## <a name="understand-pending-charges-to-estimate-your-next-invoice"></a>Comprendre les frais en attente pour estimer votre prochaine facture

Dans le compte de facturation d’un contrat de client de Microsoft, jusqu'à ce que les frais sont facturés, ils sont l’estimation et considéré comme en attente. Vous pouvez afficher les frais dans le portail Azure pour estimer votre prochaine facture en attente. Les frais en attente sont estimation et s’entendent hors taxes et l’autre les frais réels sur votre prochaine facture des frais en attente.

### <a name="view-summary-of-pending-charges"></a>Afficher le résumé des frais en attente

1. Connectez-vous au [Portail Azure](https://www.azure.com).

2. Effectuez une recherche sur **coût Management + facturation**.

   ![Capture d’écran montrant la recherche du portail Azure pour la gestion des coûts + facturation](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation. Dans le compte de facturation, sélectionnez **profils de facturation** puis sélectionnez un profil de facturation.

4. Sélectionnez **Résumé** onglet à partir du haut de l’écran.

5. La section frais d’afficher le mois en cours et les frais du mois dernier.

   ![Capture d’écran montrant la recherche du portail Azure pour la gestion des coûts + facturation](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

Les frais month-to-date sont les frais en attente pour le mois en cours et sont facturés lors de la facture est générée pour le mois. Si la facture pour le mois dernier n’est pas encore été générée, les frais du mois dernier sont également en attente et reflète sur votre prochaine facture.

### <a name="view-pending-transactions"></a>Afficher les transactions en attente

Une fois que vous avez identifié frais en attente, vous pouvez comprendre les frais en analysant les transactions individuelles qui ont contribué à la tarification. À ce stade, en attente de l’utilisation des frais ne figurent pas dans la page tout de la transaction. Vous pouvez afficher les frais d’utilisation en attente dans la page Abonnements Azure. Pour plus d’informations, consultez [vue en attente de frais d’utilisation](#view-pending-usage-charges)

1. Connectez-vous au [Portail Azure](https://www.azure.com).

2. Effectuez une recherche sur **coût Management + facturation**.

   ![Capture d’écran montrant la recherche du portail Azure pour la gestion des coûts + facturation](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation. Dans le compte de facturation, sélectionnez **profils de facturation** puis sélectionnez un profil de facturation.

4. Sélectionnez **toutes les transactions** sur le côté gauche de l’écran.

5. Recherchez **en attente**. Utilisez le **Timespan** filtre pour afficher les frais actuel ou le mois dernier en attente.

   ![Capture d’écran montrant la liste de transactions en attente](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Afficher les frais d’utilisation en attente

1. Connectez-vous au [Portail Azure](https://www.azure.com).

2. Effectuez une recherche sur **coût Management + facturation**.

   ![Capture d’écran montrant la recherche du portail Azure pour la gestion des coûts + facturation](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation. Dans le compte de facturation, sélectionnez **profils de facturation** puis sélectionnez un profil de facturation.

4. Sélectionnez **tous les abonnements** sur le côté gauche de l’écran.

5. La page Abonnements Azure affiche actuel et les frais du mois dernier pour chaque abonnement dans le profil de facturation. Les frais month-to-date sont les frais en attente pour le mois en cours et sont facturés lors de la facture est générée pour le mois. Si la facture pour le mois dernier n’est pas encore été générée, les frais du mois dernier sont également en attente.

    ![Capture d’écran montrant la liste des abonnements Azure pour le profil de facturation](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analyser vos frais d’utilisation Azure

Utilisez le fichier csv utilisation et votre facturation Azure pour analyser vos frais basée sur l’utilisation. Vous pouvez télécharger le fichier pour une facture ou frais en attente. Pour plus d’informations, consultez [obtenir vos données d’utilisation quotidienne et la facturation Azure](billing-download-azure-invoice-daily-usage-date.md).

### <a name="view-detailed-usage-by-invoice-section"></a>Afficher l’utilisation détaillée par section de la facture

Vous pouvez filtrer le fichier d’utilisation et votre facturation Azure afin de rapprocher les frais d’utilisation pour les sections de votre facture.

Les étapes suivantes vous indiquent la réconciliation des frais de calcul pour la section de facture Dept comptabilité :

![Image d’un exemple présentant les détails par les informations de section de facture](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | Facture PDF | Utilisation d’Azure et les frais de volume partagé de cluster |
 | --- | --- |
 |Comptabilité Dept |invoiceSectionName |
 |Frais d’utilisation - Plan de Microsoft Azure |productOrderName |
 |Calcul |serviceFamily |

1. Filtre la **invoiceSectionName** colonne dans le fichier CSV pour **Dept comptabilité**.
2. Filtre la **productOrderName** colonne dans le fichier CSV pour **Microsoft Azure Plan**.
3. Filtre la **serviceFamily** colonne dans le fichier CSV pour **Microsoft.Compute**.

![Capture d’écran qui illustre l’utilisation et le fichier filtré par section de la facture des frais](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)

<!--Todo Add screenshot of usage file -->

### <a name="view-detailed-usage-by-subscription"></a>Afficher l’utilisation détaillée par abonnement

Vous pouvez filtrer le fichier csv utilisation et votre facturation Azure afin de rapprocher les frais d’utilisation pour vos abonnements. Pour afficher tous les abonnements dans un profil de facturation, consultez [vue en attente de frais d’utilisation](#view-pending-usage-charges).

Une fois que vous avez identifié des frais pour un abonnement, utilisez le fichier csv utilisation et votre facturation Azure pour analyser les frais.

La capture d’écran suivante affiche la liste des abonnements dans le portail Azure.

![Capture d’écran montrant la liste des abonnements Azure pour le profil de facturation](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

Filtre la **subscriptionName** colonne dans le fichier CSV utilisation et votre facturation Azure pour **WA_Subscription** pour afficher les frais d’utilisation détaillées pour WA_Subscription.

![Capture d’écran qui illustre l’utilisation et de facture fichier filtré par abonnement](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Payer votre facture

Instructions pour payer votre facture sont affichées en bas de la facture. [Découvrez comment payer](billing-mca-understand-your-invoice.md#how-to-pay).

Si vous avez déjà payé votre facture, vous pouvez vérifier le statut du paiement dans la page de factures dans le portail Azure.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifiez l’accès à un contrat de client de Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créer une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur votre facture et votre utilisation détaillée, consultez :

- [Obtention de votre facture Azure et de vos données d’utilisation quotidienne](billing-download-azure-invoice-daily-usage-date.md)
- [Comprendre les termes du contrat sur votre facture de contrat de client de Microsoft](billing-mca-understand-your-invoice.md)
- [Comprendre les termes du contrat sur votre fichier CSV d’utilisation de contrat de client de Microsoft](billing-mca-understand-your-usage.md)
