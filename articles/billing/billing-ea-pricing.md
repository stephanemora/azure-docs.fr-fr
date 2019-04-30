---
title: Afficher et télécharger la tarification Azure de votre organisation | Microsoft Docs
description: Découvrez comment afficher et télécharger la tarification ou estimer les coûts grâce à la tarification de votre organisation.
services: ''
documentationcenter: ''
author: adpick
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: d4c64433089f14d845cea8be9adf0ef13675cfd1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127631"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Afficher et télécharger la tarification Azure de votre organisation

Les clients Azure avec un contrat entreprise (EA) ou [contrat de client Microsoft](#check-your-access-to-a-microsoft-customer-agreement) peut afficher et télécharger leurs tarifs à partir du portail Azure. Si vous avez un contrat de client de Microsoft, consultez la vue et le prix de téléchargement de votre contrat de client de Microsoft.

## <a name="view-and-download-ea-pricing"></a>Afficher et télécharger les tarifs Contrat Entreprise

Selon les stratégies définies pour votre organisation par l’administrateur d’entreprise, seuls certains rôles d’administrateur permettent d’accéder aux informations tarifaires Contrat Entreprise de votre organisation. Pour plus d’informations, consultez [Comprendre les rôles d’administrateur Contrat Entreprise Azure dans Azure](billing-understand-ea-roles.md).

1. En tant qu’administrateur d’entreprise, connectez-vous au [portail Azure](https://portal.azure.com/).
1. Effectuez une recherche sur **Gestion des coûts + facturation**.

   ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. Sous le compte de facturation, sélectionnez **Utilisation + frais**.

   ![Capture d’écran montrant l’utilisation et les frais sous Facturation](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. Sélectionnez ![Capture d’écran montrant la recherche sur le portail Azure](./media/billing-ea-pricing/download-icon.png) **Télécharger** pour le mois correspondant.
1. Sous **Grille tarifaire**, sélectionnez **Télécharger le fichier CSV**.

   ![Capture d’écran montrant le bouton de téléchargement de fichier csv de la grille tarifaire](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="view-and-download-pricing-for-your-microsoft-customer-agreement"></a>Afficher et télécharger la tarification de votre contrat de client de Microsoft

Vous devez être le propriétaire de profil de facturation, de collaborateur, lecteur ou Gestionnaire de factures pour afficher et télécharger la tarification. Pour en savoir plus sur les rôles de facturation pour les contrats de client de Microsoft, consultez [tâches et rôles de profil de facturation](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="download-price-sheets-for-the-current-billing-period"></a>Télécharger les tables de tarification pour la période de facturation actuelle

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Effectuez une recherche sur **Gestion des coûts + facturation**.
1. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être d’abord sélectionner un compte de facturation.
1. Dans le **vue d’ensemble** panneau, recherchez les liens de téléchargement sous les frais month-to-date.
1. Sélectionnez **table de tarification Azure**.
![Capture d’écran montrant le téléchargement à partir de la vue d’ensemble](./media/billing-ea-pricing/open-pricing.png)

### <a name="download-price-sheets-for-billed-charges"></a>Télécharger les tables de tarification pour les frais de facturation

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Effectuez une recherche sur **Gestion des coûts + facturation**.
1. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être d’abord sélectionner un compte de facturation.
1. Sélectionnez **Factures**.
1. Dans la grille de la facture, recherchez la ligne de la facture correspondant à la table de tarification que vous souhaitez télécharger.
1. Cliquez sur le bouton de sélection (`...`) à la fin de la ligne.
![Capture d’écran illustrant les points de suspension sélectionné](./media/billing-ea-pricing/billingprofile-invoicegrid.png)

1. Si vous souhaitez consulter les tarifs pour les services dans la facture sélectionnée, sélectionnez **table de tarification de facture**.
1. Si vous souhaitez connaître les prix pour tous les services Azure pour la période de facturation donnée, sélectionnez **table de tarification Azure**.

![Capture d’écran montrant le menu contextuel avec les tables de tarification](./media/billing-ea-pricing/contextmenu-pricesheet.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Estimation des coûts avec la calculatrice de prix Azure

Vous pouvez également utiliser la tarification de votre organisation pour estimer les coûts avec la calculatrice de prix Azure.

1. Accédez à la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator).
1. En haut à droite, sélectionnez **Connexion**.
1. Sous **Programmes et offre** > **Programme de licence**, sélectionnez **Contrat Entreprise**.
1. Sous **Programmes et offre** > **Contrat sélectionné**, sélectionnez **Aucune sélection**.

    ![Capture d’écran montrant le bouton de téléchargement de fichier csv de la grille tarifaire](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. Choisissez l’organisation.
1. Sélectionnez **Appliquer**.
1. Recherchez et ajoutez des produits à votre devis.
1. Les prix estimés affichés sont basés sur les tarifs de l’organisation que vous avez sélectionnés.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Vérifiez votre accès à un contrat de client de Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>Étapes suivantes

Si vous êtes client EA, consultez :

- [Gérer l’accès aux informations de facturation de votre contrat entreprise pour Azure](billing-manage-access.md)
- [Afficher et télécharger votre facture Microsoft Azure](billing-download-azure-invoice.md)
- [Afficher et télécharger votre utilisation de Microsoft Azure et des frais](billing-download-azure-daily-usage.md)
- [Comprendre votre facture pour les clients contrat entreprise](billing-understand-your-bill-ea.md)

Si vous avez un contrat de client de Microsoft, consultez :

- [Comprendre les termes du contrat dans votre table de tarification pour un contrat de client de Microsoft](billing-mca-understand-pricesheet.md)
- [Afficher et télécharger votre facture Microsoft Azure](billing-download-azure-invoice.md)
- [Afficher et télécharger votre utilisation de Microsoft Azure et des frais](billing-download-azure-daily-usage.md)
- [Afficher et télécharger des documents de taxe pour votre profil de facturation](billing-mca-download-tax-document.md)
- [Comprendre les frais de facture de votre profil facturation](billing-mca-understand-your-bill.md)
