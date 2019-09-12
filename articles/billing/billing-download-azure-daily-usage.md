---
title: Afficher et télécharger les informations d’utilisation et les frais Azure
description: Décrit comment télécharger ou afficher vos informations d’utilisation quotidienne et vos frais Azure.
keywords: facturation de l’utilisation, frais d’utilisation, téléchargement des informations d’utilisation, afficher l’utilisation, facture azure, utilisation d’azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 7d2d7be562eaaa7dd21e63735f5697ffe5a62f8a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491441"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Afficher et télécharger vos informations d’utilisation et vos frais Azure

Si vous êtes client EA ou avez un [Contrat client Microsoft](#check-your-access-to-a-microsoft-customer-agreement), vous pouvez télécharger les informations d’utilisation et les frais Azure dans le [portail Azure](https://portal.azure.com/). Pour les autres abonnements, accédez au [Centre des comptes Azure](https://account.azure.com/Subscriptions) afin de télécharger les informations d’utilisation.

Seuls certains rôles, par exemple l’administrateur de compte ou l’administrateur d’entreprise, sont autorisés à obtenir les informations d’utilisation Azure. Pour en savoir plus sur l’accès aux informations de facturation, consultez [Manage access to Azure billing using roles](billing-manage-access.md) (Utiliser des rôles pour gérer l’accès à la facturation Azure).

Si vous avez un [Contrat client Microsoft](#check-your-access-to-a-microsoft-customer-agreement), vous devez être Gestionnaire de factures, Propriétaire, Contributeur ou Lecteur de profil de facturation pour afficher vos informations d’utilisation et vos frais Azure. Pour en savoir plus sur les rôles de facturation pour les Contrats client Microsoft, consultez [Tâches et rôles du profil de facturation](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-usage-from-the-account-center-csv"></a>Télécharger l’utilisation à partir du centre de compte (.csv)

1. Connectez-vous au [Centre des comptes Azure](https://account.windowsazure.com/subscriptions) en tant qu’administrateur de compte.

2. Sélectionnez l’abonnement pour lequel vous voulez obtenir les informations de facturation et d’utilisation.

3. Sélectionnez **HISTORIQUE DE FACTURATION**.

    ![Capture d’écran qui montre l’option d’historique de facturation](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Vous pouvez consulter vos relevés des six dernières périodes de facturation, ainsi que la période non facturée en cours.

    ![Capture d’écran qui montre les périodes de facturation, les options de téléchargement des factures et de l’utilisation quotidienne et le total des frais pour chaque période de facturation](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Sélectionnez **Afficher la déclaration actuelle** pour voir une estimation de vos frais au moment où l’estimation a été générée. Cette information est uniquement mise à jour quotidiennement et peut ne pas inclure l’ensemble de votre utilisation. Votre facture mensuelle peut différer de l’estimation.

    ![Capture d’écran qui montre l’option Afficher la déclaration actuelle](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Capture d’écran qui montre l’estimation des frais actuels](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Sélectionnez **Télécharger l’utilisation** pour télécharger les données d’utilisation quotidienne dans un fichier CSV. Si deux versions sont disponibles, téléchargez la version 2.

    ![Capture d’écran qui montre l’option Télécharger les informations d’utilisation](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Seul l’administrateur de compte peut accéder au centre des comptes Azure. Les autres administrateurs de facturation (notamment les propriétaires) peuvent obtenir des informations d’utilisation à l’aide des [API de facturation](billing-usage-rate-card-overview.md).

Pour plus d’informations sur votre utilisation quotidienne, consultez la page [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md). Pour obtenir de l’aide sur la gestion des coûts, voir [Éviter les coûts inattendus avec la gestion de la facturation et des coûts Azure](billing-getting-started.md).

## <a name="download-usage-for-ea-customers"></a>Télécharger l’utilisation pour les clients EA

Pour voir et télécharger les données d’utilisation en tant que client Contrat Entreprise, vous devez être administrateur d’entreprise, propriétaire de compte ou administrateur de service, et la stratégie d’affichage des coûts doit être activée.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Recherchez *Gestion des coûts + facturation*.

    ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Sélectionnez **Utilisation + frais**.
1. Pour le mois que vous souhaitez télécharger, sélectionnez **Télécharger**.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Télécharger les informations d’utilisation pour votre Contrat client Microsoft

Si vous avez un Contrat client Microsoft, vous pouvez télécharger les informations d’utilisation et les frais Azure associés à votre profil de facturation. Vous devez être Gestionnaire de factures, Propriétaire, Contributeur ou Lecteur de profil de facturation pour télécharger le fichier CSV des informations d’utilisation et des frais Azure.

### <a name="download-usage-for-billed-charges"></a>Télécharger les informations d’utilisation pour les frais facturés

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Recherchez *Gestion des coûts + facturation*.
3. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être sélectionner au préalable un compte de facturation.
4. Sélectionnez **Factures**.
5. Dans la grille des factures, recherchez la ligne de la facture correspondant aux informations d’utilisation que vous souhaitez télécharger.
6. Cliquez sur les points de suspension (`...`) à la fin de la ligne.

    ![Capture d’écran illustrant les points de suspension à la fin de la ligne](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. Dans le menu contextuel du téléchargement, sélectionnez **Utilisation et frais Azure**.

     ![Capture d’écran illustrant la sélection de l’option Utilisation et frais Azure](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>Télécharger les informations d’utilisation et les frais en attente

Vous pouvez également télécharger les informations d’utilisation cumulée pour le mois en cours concernant la période de facturation actuelle. Il s’agit des frais d’utilisation qui n’ont pas encore été facturés.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Recherchez *Gestion des coûts + facturation*.
3. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être sélectionner au préalable un compte de facturation.
4. Dans la zone **Vue d’ensemble**, recherchez les liens de téléchargement sous les frais en cumul mensuel à ce jour.
5. Sélectionnez **Utilisation et frais Azure**.

    ![Capture d’écran montrant le téléchargement à partir du panneau Vue d’ensemble](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Vérifier votre accès à un Contrat client Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur vos facture et frais d’utilisation, consultez :

- [Comprendre les termes figurant dans le détail de votre utilisation de Microsoft Azure](billing-understand-your-usage.md)
- [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md)
- [Afficher et télécharger votre facture Microsoft Azure](billing-download-azure-invoice.md)
- [Afficher et télécharger les tarifs Azure de votre organisation](billing-ea-pricing.md)

Si vous avez un Contrat client Microsoft, consultez :

- [Comprendre les termes figurant dans les informations d’utilisation Azure détaillées associées à votre Contrat client Microsoft](billing-mca-understand-your-usage.md)
- [Comprendre les frais indiqués sur votre facture du Contrat client Microsoft](billing-mca-understand-your-bill.md)
- [Afficher et télécharger votre facture Microsoft Azure](billing-download-azure-invoice.md)
- [Afficher et télécharger les documents fiscaux pour votre Contrat client Microsoft](billing-mca-download-tax-document.md)
- [Afficher et télécharger les tarifs Azure de votre organisation](billing-ea-pricing.md)
