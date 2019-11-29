---
title: Comprendre les frais indiqués sur la facture de votre Contrat Partenaire Microsoft - Azure
description: Découvrez comment lire et comprendre les frais indiqués sur votre facture.
author: jureid
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2019
ms.author: banders
ms.openlocfilehash: 9994ef6da379d0063930912a4d198b81623ddbe8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223708"
---
# <a name="understand-charges-on-your-microsoft-partner-agreement-invoice"></a>Comprendre les frais indiqués sur la facture de votre Contrat Partenaire Microsoft

 Dans le compte de facturation d’un Contrat Partenaire Microsoft, une facture est générée chaque mois pour chaque profil de facturation. La facture inclut tous les frais facturés aux clients depuis le mois précédent. Pour comprendre les frais indiqués sur votre facture, vous pouvez analyser les transactions individuelles dans le portail Azure. Vous pouvez également consulter vos factures dans le portail Azure. Pour plus d’informations, consultez l’article qui explique [comment télécharger des factures à partir du portail Azure](billing-download-azure-invoice.md).

Cet article s’applique à un compte de facturation associé à un Contrat Partenaire Microsoft. [Vérifiez si vous avez accès à un Contrat Partenaire Microsoft](#check-access-to-a-microsoft-partner-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Afficher les transactions d’une facture dans le portail Microsoft Azure

1. Connectez-vous au [Portail Azure](https://www.azure.com).

2. Recherchez *Gestion des coûts + facturation*.

   <!--  ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Sur le côté gauche de la page, sélectionnez **Toutes les transactions**. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation, un profil de facturation ou un client avant de sélectionner **Toutes les transactions**.

4. La page Toutes les transactions affiche les informations suivantes :

    <!-- ![Screenshot that shows the billed transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-all-transactions.png) -->

    |Colonne  |Définition  |
    |---------|---------|
    |Date     | Date de transaction  |
    |ID de facture     | L’identificateur de la facture où la transaction a été facturée. Si vous envoyez une demande de support, partagez l’ID avec le support Azure pour accélérer le traitement de votre demande |
    |Type de transaction     |  Il existe différents types de transactions : des frais d’achats, d’annulation et d’utilisation  |
    |Famille de produits     | La catégorie du produit, tel que le calcul pour les machines virtuelles ou la base de données pour Azure SQL Database|
    |Référence SKU de produit     | Un code unique identifiant l’instance de votre produit |
    |Montant     |  Le montant de la transaction      |
    |Profil de facturation     | La transaction s’affiche sur cette facture du profil de facturation |

5. Recherchez l’ID de facture pour filtrer les transactions de la facture.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Passez en revue les frais en attente pour estimer votre prochaine facture

Les frais sont estimés et restent en attente jusqu’à ce qu’ils soient facturés. Pour estimer votre prochaine facture, vous pouvez consulter les frais en attente sur votre profil de facturation associé à un Contrat Partenaire Microsoft dans le portail Azure. Les frais en attente sont des estimations et s’entendent hors taxes. Les frais réels figurant sur votre prochaine facture seront différents des frais en attente.

### <a name="view-pending-transactions"></a>Afficher les transactions en attente

Lorsque vous avez identifié les frais en attente, vous pouvez les comprendre en analysant les transactions individuelles qui forment ces frais. À ce stade, les frais d’utilisation en attente ne sont pas affichés sur la page Toutes les transactions. Vous pouvez les afficher sur la page Abonnements Azure. Pour plus d’informations, veuillez consulter [Afficher les frais d’utilisation en attente](#view-pending-usage-charges)

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Recherchez *Gestion des coûts + facturation*.

   <!-- ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation. Dans le compte de facturation, sélectionnez **Profils de facturation**, puis un profil de facturation.

4. Sur le côté gauche de la page, sélectionnez **Toutes les transactions**.

5. Recherchez *En attente*. Utilisez le filtre **Timespan** (Période) pour afficher les frais en attente pour le mois actuel ou le mois dernier.

   <!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>Consulter les frais en attente par client

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Recherchez *Gestion des coûts + facturation*.

3. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation. Dans le compte de facturation, sélectionnez **Profils de facturation**, puis un profil de facturation.

4. Sélectionnez **Clients** sur le côté gauche de la page.

    <!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

5. La page Clients indique affiche les frais du mois actuel et du mois dernier pour chaque client associé au profil de facturation. Les frais du mois en cours sont des frais en attente. Ils seront facturés lors de la génération de la facture à la fin du mois. Si la facture du mois dernier n’est pas encore générée, les frais du mois dernier sont également en attente.

### <a name="view-pending-usage-charges"></a>Afficher les frais d’utilisation en attente

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Recherchez *Gestion des coûts + facturation*.

3. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation. Dans le compte de facturation, sélectionnez **Profils de facturation**, puis un profil de facturation.

4. Sur le côté gauche de la page, sélectionnez **Abonnements Azure**.

5. La page Abonnements Azure affiche les frais actuels et ceux du mois dernier pour chaque abonnement dans le profil de facturation. Les frais du mois en cours sont des frais en attente. Ils seront facturés lors de la génération de la facture à la fin du mois. Si la facture du mois dernier n’est pas encore générée, les frais du mois dernier sont également en attente.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Analyser vos frais d’utilisation Azure

Utilisez le fichier CSV Utilisation et frais Azure pour analyser vos frais en fonction de votre utilisation. Vous pouvez [télécharger le fichier CSV Utilisation et frais Azure à partir du portail Azure](billing-download-azure-daily-usage.md).

Vous pouvez filtrer le fichier Utilisation et frais Azure pour rapprocher les frais d’utilisation de chaque produit listé sur la facture PDF. Pour voir les frais d’utilisation détaillés d’un produit en particulier, filtrez la colonne **product** dans le fichier CSV Utilisation et frais Azure pour inclure uniquement le nom de ce produit.

Vous pouvez également filtrer la colonne **customerName** dans le fichier CSV Utilisation et frais Azure afin de voir les frais d’utilisation quotidienne facturés à chacun de vos clients. Si vous souhaitez voir les frais d’utilisation quotidienne par abonnement Azure, filtrez la colonne **subscriptionName**.


## <a name="pay-your-bill"></a>Régler votre facture

En bas de la facture, vous trouverez des instructions pour le règlement de votre facture. Vous pouvez payer par chèque ou par virement dans un délai de 60 jours à compter de la date de votre facture.

Si vous avez déjà payé votre facture, vous pouvez vérifier le statut du paiement dans la page Factures dans le portail Microsoft Azure.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Vérifier l’accès à un Contrat Partenaire Microsoft
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur votre facture et consulter des informations détaillées sur votre utilisation, consultez :
