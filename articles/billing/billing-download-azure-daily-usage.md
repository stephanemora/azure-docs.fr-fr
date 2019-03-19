---
title: Utilisation de vue et téléchargez de Azure et des frais | Microsoft Docs
description: Décrit comment télécharger ou afficher votre utilisation quotidienne d’Azure et vos frais.
keywords: facturation de l’utilisation, les frais d’utilisation, l’utilisation de téléchargement, afficher l’utilisation, l’utilisation de facture azure, azure
services: billing
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 20404cea2aca984ef35472fa94d37c04eb8080e4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57872638"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Afficher et télécharger votre utilisation d’Azure et les frais

Si vous êtes client EA ou que vous avez un [contrat de client Microsoft](#check-your-access-to-a-microsoft-customer-agreement), vous pouvez télécharger l’utilisation d’Azure et des frais dans le [Azure portal](https://portal.azure.com/). Pour les autres abonnements, accédez à la [centre des comptes Azure](https://account.azure.com/Subscriptions) télécharger l’utilisation.

Certains rôles uniquement ont l’autorisation pour obtenir des informations d’utilisation d’Azure, tels que le compte administrateur ou administrateur d’entreprise. Pour en savoir plus sur l’accès aux informations de facturation, consultez [Manage access to Azure billing using roles](billing-manage-access.md) (Utiliser des rôles pour gérer l’accès à la facturation Azure).

Si vous avez un [contrat de client Microsoft](#Check-your-access-to-a-Microsoft-Customer-Agreement), vous devez être un profil de facturation propriétaire, collaborateur ou lecteur, ou manager pour afficher votre utilisation d’Azure et les frais de facture. Pour en savoir plus sur les rôles de facturation pour les contrats de client de Microsoft, consultez [tâches et rôles de profil de facturation](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

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

Pour afficher et télécharger des données d’utilisation en tant que contrat entreprise client, vous devez être un administrateur d’entreprise, propriétaire du compte, ou administrateur de service avec la vue de frais stratégie est activée.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Recherchez *coût Management + facturation*.

    ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Sélectionnez **Utilisation + frais**.
1. Pour le mois que vous souhaitez télécharger, sélectionnez **Télécharger**.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Télécharger l’utilisation de votre contrat de client de Microsoft

Si vous avez un contrat de client de Microsoft, vous pouvez télécharger votre utilisation d’Azure et les frais pour votre profil de facturation. Vous devez être un profil de facturation propriétaire, collaborateur, lecteur, ou manager pour le téléchargement de l’utilisation d’Azure et CSV des frais de facture.

### <a name="download-usage-for-billed-charges"></a>Télécharger l’utilisation pour les frais de facturation

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Effectuez une recherche sur **Gestion des coûts + facturation**.
3. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être d’abord sélectionner un compte de facturation.
4. Sélectionnez **Factures**.
5. Dans la grille de la facture, recherchez la ligne de la facture correspondant à l’utilisation que vous souhaitez télécharger.
6. Cliquez sur le bouton de sélection (`...`) à la fin de la ligne.

    ![Capture d’écran illustrant les points de suspension à la fin de la ligne](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. Dans le menu contextuel de téléchargement, sélectionnez **l’utilisation d’Azure et les frais**.

     ![Capture d’écran illustrant l’utilisation d’Azure et les frais sélectionnés](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>Télécharger l’utilisation de frais en attente

Vous pouvez également télécharger l’utilisation month-to-date pour la période de facturation actuelle. Ces frais d’utilisation qui n’ont pas encore été facturés.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Effectuez une recherche sur **Gestion des coûts + facturation**.
3. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être d’abord sélectionner un compte de facturation.
4. Dans le **vue d’ensemble** panneau, recherchez les liens de téléchargement sous les frais month-to-date.
5. Sélectionnez **l’utilisation d’Azure et les frais**.

    ![Capture d’écran montrant le téléchargement à partir de la vue d’ensemble](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Vérifiez votre accès à un contrat de client de Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créer une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur vos frais de facture et l’utilisation, consultez :

- [Comprendre les termes figurant dans le détail de votre utilisation de Microsoft Azure](billing-understand-your-usage.md)
- [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md)
- [Afficher et télécharger votre facture Microsoft Azure](billing-download-azure-invoice.md)
- [Afficher et télécharger la tarification Azure de votre organisation](billing-ea-pricing.md)

Si vous avez un contrat de client de Microsoft, consultez :

- [Comprendre les termes du contrat sur votre contrat de client de Microsoft Azure utilisation détaillée](billing-mca-understand-your-usage.md)
- [Comprendre les frais sur votre facture de contrat de client de Microsoft](billing-mca-understand-your-bill.md)
- [Afficher et télécharger votre facture Microsoft Azure](billing-download-azure-invoice.md)
- [Afficher et télécharger des documents de taxe de votre contrat de client de Microsoft](billing-mca-download-tax-document.md)
- [Afficher et télécharger la tarification Azure de votre organisation](billing-ea-pricing.md)
