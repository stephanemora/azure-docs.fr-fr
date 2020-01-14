---
title: Frais de facture de Contrat client Microsoft - Azure
description: Découvrez comment lire et comprendre les frais indiqués sur votre facture.
author: jureid
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: b5dfffecbd2908e987b76f29b14937f0e50ce64f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75388990"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>Comprendre les frais indiqués sur votre facture du Contrat client Microsoft

Vous pouvez comprendre les frais sur votre facture en analysant les transactions individuelles. Dans le compte de facturation d’un contrat client Microsoft, une facture est générée chaque mois pour chaque profil de facturation. La facture inclut tous les frais du mois précédent. Vous pouvez afficher vos factures dans le portail Microsoft Azure. Pour plus d'informations, veuillez consulter [Télécharger les factures pour un contrat client Microsoft](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

Cet article s'applique à un compte de facturation associé à un contrat client Microsoft. [Vérifiez que vous avez accès à un contrat client Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Afficher les transactions d’une facture dans le portail Microsoft Azure

1. Connectez-vous au [portail Azure](https://www.azure.com).

2. Recherchez **Gestion des coûts + facturation**.

    ![Capture d’écran montrant une recherche relative à la gestion des coûts et à la facturation sur le portail Microsoft Azure](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Sur le côté gauche de la page, sélectionnez **Toutes les transactions**. Selon votre accès, vous devrez peut-être sélectionner un compte, un profil ou une section de facturation, puis **Toutes les transactions**.

4. La page Toutes les transactions affiche les informations suivantes :

    ![Capture d’écran montrant la liste des transactions facturées](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |Colonne  |Définition  |
    |---------|---------|
    |Date     | Date de transaction  |
    |ID de facture     | L’identificateur de la facture où la transaction a été facturée. Si vous envoyez une demande de support, partagez l’ID avec le support Azure pour accélérer le traitement de votre demande |
    |Type de transaction     |  Il existe différents types de transactions : des frais d’achats, d’annulation et d’utilisation  |
    |Famille de produits     | La catégorie du produit, tel que le calcul pour les machines virtuelles ou la base de données pour Azure SQL Database|
    |Référence SKU de produit     | Un code unique identifiant l’instance de votre produit |
    |Montant     |  Le montant de la transaction      |
    |Section de facture     | La transaction s’affiche sur cette section de la facture du profil de facturation |
    |Profil de facturation     | La transaction s’affiche sur cette facture du profil de facturation |

5. Recherchez l’ID de facture pour filtrer les transactions de la facture.

### <a name="view-transactions-by-invoice-sections"></a>Afficher les transactions en sections de facture

Les sections de facture vous permettent d’organiser les coûts pour la facture d’un profil de facturation. Pour plus d'informations, consultez [Présentation des sections de facture](billing-mca-overview.md#invoice-sections). Lorsqu’une facture est générée, les frais pour toutes les sections du profil de facturation sont répercutés sur la facture.

L’image suivante montre les frais de la section Facture du service de comptabilité sur un exemple de facture.

![Image d’un exemple présentant les informations détaillées d’une facture organisées en sections](./media/billing-understand-your-bill-mca/invoicesection-details.png)

Lorsque vous avez identifié les frais dans la section d’une facture, vous pouvez afficher les transactions dans le portail Microsoft Azure pour comprendre ces frais.

1. Dans le portail Microsoft Azure, accédez à la page Toutes les transactions, pour afficher les transactions d’une facture. Pour plus d’informations, veuillez consulter [Afficher les transactions d’une facture dans le portail Microsoft Azure](#view-transactions-for-an-invoice-in-the-azure-portal).

2. Filtrez par nom de section de la facture pour consulter les transactions.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Passez en revue les frais en attente pour estimer votre prochaine facture

Dans le compte de facturation d’un contrat client de Microsoft, tant que les frais ne sont pas facturés, ils sont considérés comme une estimation en attente de validation. Vous pouvez consulter les frais en attente dans le portail Microsoft Azure pour estimer le montant de votre prochaine facture. Les frais en attente sont des estimations et s’entendent hors taxes. Les frais réels figurant sur votre prochaine facture seront différents des frais en attente.

### <a name="view-summary-of-pending-charges"></a>Afficher un résumé des frais en attente

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Recherchez **Gestion des coûts + facturation**.

   ![Capture d’écran montrant une recherche relative à la gestion des coûts et à la facturation sur le portail Microsoft Azure](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation. Dans le compte de facturation, sélectionnez **Profils de facturation**, puis un profil de facturation.

4. En haut de l’écran, sélectionnez l’onglet **Résumé**.

5. La section Frais affiche le mois en cours et les frais du mois dernier.

   ![Capture d’écran montrant une recherche relative à la gestion des coûts et à la facturation sur le portail Microsoft Azure](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

Les frais du mois en cours sont des frais en attente. Ils seront facturés lors de la génération de la facture à la fin du mois. Si la facture du mois dernier n’est pas encore générée, les frais du mois dernier sont également en attente et seront reportés sur votre prochaine facture.

### <a name="view-pending-transactions"></a>Afficher les transactions en attente

Lorsque vous avez identifié les frais en attente, vous pouvez les comprendre en analysant les transactions individuelles qui forment ces frais. À ce stade, les frais d’utilisation en attente ne sont pas affichés sur la page Toutes les transactions. Vous pouvez les afficher sur la page Abonnements Azure. Pour plus d’informations, veuillez consulter [Afficher les frais d’utilisation en attente](#view-pending-usage-charges)

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Recherchez **Gestion des coûts + facturation**.

   ![Capture d’écran montrant une recherche relative à la gestion des coûts et à la facturation sur le portail Microsoft Azure](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation. Dans le compte de facturation, sélectionnez **Profils de facturation**, puis un profil de facturation.

4. Sur le côté gauche de la page, sélectionnez **Toutes les transactions**.

5. Recherchez *En attente*. Utilisez le filtre **Timespan** (Période) pour afficher les frais en attente pour le mois actuel ou le mois dernier.

   ![Capture d’écran montrant la liste des transactions en attente](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Afficher les frais d’utilisation en attente

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Recherchez *Gestion des coûts + facturation*.

   ![Capture d’écran montrant une recherche relative à la gestion des coûts et à la facturation sur le portail Microsoft Azure](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation. Dans le compte de facturation, sélectionnez **Profils de facturation**, puis un profil de facturation.

4. Sur le côté gauche de la page, sélectionnez **Tous les abonnements**.

5. La page Abonnements Azure affiche les frais actuels et ceux du mois dernier pour chaque abonnement dans le profil de facturation. Les frais du mois en cours sont des frais en attente. Ils seront facturés lors de la génération de la facture à la fin du mois. Si la facture du mois dernier n’est pas encore générée, les frais du mois dernier sont également en attente.

    ![Capture d’écran montrant la liste des abonnements Azure pour le profil de facturation](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analyser vos frais d’utilisation Azure

Utilisez le fichier CSV Utilisation et frais Azure pour analyser vos frais en fonction de votre utilisation. Vous pouvez télécharger le fichier pour une facture ou des frais en attente. Pour plus d’informations, veuillez consulter [Obtenir votre facture Azure et vos données d’utilisation quotidienne](billing-download-azure-invoice-daily-usage-date.md).

### <a name="view-detailed-usage-by-invoice-section"></a>Afficher l’utilisation détaillée par section de la facture

Vous pouvez filtrer le fichier Utilisation et frais Azure pour rapprocher les frais d’utilisation des sections de votre facture.

La procédure suivante vous guide lors du rapprochement des frais de calcul pour la section Facture du service de comptabilité :

![Image d’un exemple présentant les informations détaillées d’une facture organisées en sections](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | Facture PDF | Fichier CSV Utilisation et frais Azure |
 | --- | --- |
 |Service de comptabilité |invoiceSectionName |
 |Frais d’utilisation : Offre Microsoft Azure |productOrderName |
 |Calcul |serviceFamily |

1. Filtre la colonne **invoiceSectionName** dans le fichier CSV sur **Service de comptabilité**.
2. Filtre la colonne **productOrderName** dans le fichier CSV sur **Microsoft Azure Plan**.
3. Filtre la colonne **serviceFamily** dans le fichier CSV sur **Microsoft.Compute**.

![Capture d’écran qui illustre le fichier d’utilisation et de frais filtré par section de facture](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>Afficher l’utilisation détaillée par abonnement

Vous pouvez filtrer le fichier CSV Utilisation et frais Azure pour rapprocher les frais d’utilisation de vos abonnements. Pour afficher tous les abonnements dans un profil de facturation, consultez [Afficher les frais d’utilisation en attente](#view-pending-usage-charges).

Lorsque vous avez identifié des frais d’un abonnement, utilisez le fichier CSV Utilisation et frais Azure pour analyser les frais.

L’image suivante montre la liste des abonnements dans le portail Microsoft Azure.

![Capture d’écran montrant la liste des abonnements Azure pour le profil de facturation](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

Filtre la colonne **subscriptionName** dans le fichier CSV Utilisation et frais Azure pour **WA_Subscription** afin d’afficher les frais d’utilisation détaillés pour WA_Subscription.

![Capture d’écran qui illustre le fichier Utilisation et frais, filtré par abonnement](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Régler votre facture

En bas de la facture, vous trouverez des instructions pour le règlement de votre facture. [Découvrez comment payer](billing-mca-understand-your-invoice.md#how-to-pay).

Si vous avez déjà payé votre facture, vous pouvez vérifier le statut du paiement dans la page Factures dans le portail Microsoft Azure.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifier l'accès à un contrat client Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur votre facture et consulter des informations détaillées sur votre utilisation, consultez :

- [Obtention de votre facture Azure et de vos données d’utilisation quotidienne](billing-download-azure-invoice-daily-usage-date.md)
- [Comprendre les termes indiqués sur votre facture du contrat client Microsoft](billing-mca-understand-your-invoice.md)
- [Comprendre les termes indiqués sur votre fichier CSV d’utilisation du contrat client Microsoft](billing-mca-understand-your-usage.md)
