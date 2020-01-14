---
title: Afficher et télécharger les informations d’utilisation et les frais Azure
description: Décrit comment télécharger ou afficher vos informations d’utilisation quotidienne et vos frais Azure.
keywords: facturation de l’utilisation, frais d’utilisation, téléchargement des informations d’utilisation, afficher l’utilisation, facture azure, utilisation d’azure
author: bandersmsft
ms.author: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: dbb7956bf58d6b997f782d8f88e1eeaf19d6ade8
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665097"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Afficher et télécharger vos informations d’utilisation et vos frais Azure

Vous pouvez télécharger une répartition quotidienne de votre utilisation d’Azure et de vos frais dans le portail Azure. Seuls certains rôles, par exemple l’administrateur de compte ou l’administrateur d’entreprise, sont autorisés à obtenir les informations d’utilisation Azure. Pour en savoir plus sur l’accès aux informations de facturation, consultez [Manage access to Azure billing using roles](billing-manage-access.md) (Utiliser des rôles pour gérer l’accès à la facturation Azure).

Si vous avez un Contrat Client Microsoft (MCA), vous devez être Gestionnaire de factures, ou Propriétaire, Contributeur ou Lecteur de profil de facturation pour voir votre utilisation d’Azure et les frais encourus.  Si vous avez un Contrat Partenaire Microsoft (MPA), seul le rôle Administrateur général ou Agent d’administration dans l’organisation partenaire peut voir et télécharger les informations sur l’utilisation et les frais Azure. [Vérifiez votre type de compte de facturation dans le portail Azure](#check-your-billing-account-type).

## <a name="download-usage-from-the-azure-portal-csv"></a>Télécharger l’utilisation à partir du portail Azure (.csv)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez *Gestion des coûts + facturation*.

    ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-download-azure-usage/portal-cm-billing-search.png)

1. Selon votre type d’accès, vous devrez peut-être sélectionner un compte de facturation ou un profil de facturation.
1. Dans le menu de gauche, sélectionnez **Factures** sous **Facturation**.
1. Dans la grille des factures, recherchez la ligne de la période de facturation correspondant aux informations d’utilisation que vous souhaitez télécharger.
1. Sélectionnez l’**icône de téléchargement** ou les points de suspension (`...`) à droite.
1. Le volet de téléchargement s’ouvre à droite. Sélectionnez **Télécharger** dans la section **Détails d’utilisation**.

## <a name="download-usage-for-ea-customers"></a>Télécharger l’utilisation pour les clients EA

Pour voir et télécharger les données d’utilisation en tant que client Contrat Entreprise, vous devez être administrateur d’entreprise, propriétaire de compte ou administrateur de service, et la stratégie d’affichage des coûts doit être activée.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez *Gestion des coûts + facturation*.

    ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-download-azure-usage/portal-cm-billing-search.png)

1. Sélectionnez **Utilisation + frais**.
1. Pour le mois que vous souhaitez télécharger, sélectionnez **Télécharger**.

## <a name="download-usage-for-pending-charges"></a>Télécharger les informations d’utilisation et les frais en attente

Si vous avez un Contrat Client Microsoft, vous pouvez télécharger l’utilisation en cumul mensuel à ce jour pour la période de facturation actuelle. Il s’agit des frais d’utilisation qui n’ont pas encore été facturés.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Recherchez *Gestion des coûts + facturation*.
3. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être sélectionner au préalable un compte de facturation.
4. Dans la zone **Vue d’ensemble**, recherchez les liens de téléchargement sous les frais en cumul mensuel à ce jour.
5. Sélectionnez **Utilisation et frais Azure**.

    ![Capture d’écran montrant le téléchargement à partir du panneau Vue d’ensemble](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-billing-account-type"></a>Vérifier votre type de compte de facturation
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

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
