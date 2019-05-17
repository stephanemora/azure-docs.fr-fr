---
title: Comprendre votre facture de contrat de client de Microsoft | Microsoft Docs
description: Découvrez comment lire et comprendre votre facture MCA
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: aee51793c66ae57f740300797b8fdc1799e685cd
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604041"
---
# <a name="understand-terms-on-your-microsoft-customer-agreement-invoice"></a>Comprendre les termes du contrat sur votre facture de contrat de client de Microsoft

Cet article s’applique à un compte de facturation d’un contrat de client de Microsoft. [Vérifiez si vous avez accès à un contrat de client Microsoft](#check-access-to-a-microsoft-customer-agreement).

Votre facture fournit un résumé de vos frais et les instructions de paiement. Elle est disponible au téléchargement au format PDF (.pdf) à partir du [portail Azure](https://portal.azure.com/) ou peut être envoyée par courrier électronique. Pour plus d’informations, consultez [vue et le téléchargement de Microsoft Azure facture](billing-download-azure-invoice.md).

<!-- ## When am I billed?

You are invoiced on a monthly basis. You can find out which day of the month you receive invoices by checking *invoice date* under billing profile properties in the [Azure portal](https://portal.azure.com/). Charges that occur between the end of the billing period and the invoice date are included in the next month's invoice, since they are in the next billing period. The billing period start and end dates for each invoice are listed in the invoice PDF above **Billing Summary**. -->

## <a name="invoice-terms-and-descriptions"></a>Facture termes et descriptions

Les sections suivantes répertorient les termes importants que vous voyez sur votre facture et les descriptions de chaque terme.

### <a name="invoice-summary"></a>Résumé de la facture

Le **résumé de la facture** se trouve en haut de la première page et affiche des informations sur votre profil de facturation et le mode de paiement.

![Section Récapitulatif de la facture](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Terme | Description  |
| --- | --- |
| Vendu à |Adresse de votre entité juridique, a été trouvée dans les propriétés du compte de facturation|
| Facturer à |Votre adresse de réception de la facture le profil de facturation de facturation, trouvé dans les propriétés de profil de facturation|
| Profil de facturation |Le nom du profil de facturation reçoit la facture |
| P.O. number |Numéro de commande facultatif, attribué par vous pour le suivi |
| Numéro de facture |Un numéro de facture unique, généré par Microsoft utilisé aux fins de suivi |
| Date de facture |Date à laquelle la facture est générée, généralement des jours de cinq à 12 après la fin du cycle de facturation. Vous pouvez vérifier votre date de facture dans les propriétés du profil de facturation.|
| Conditions de paiement |Mode de paiement de votre facture Microsoft. *30 jours NET* signifie que vous payez pendant 30 jours suivant la date de facture. |

### <a name="billing-summary"></a>Résumé de facturation

Le **résumé facturation** montre les frais par rapport au profil de facturation depuis la période de facturation précédente, tout crédit qui ont été appliqués, taxes et le montant total dû.

![Section de résumé de facturation](./media/billing-understand-your-invoice-mca/billingsummary.png)

| Terme | Description  |
| --- | --- |
| Frais|Nombre total des frais de Microsoft pour ce profil de facturation depuis la dernière période de facturation |
| Crédits |Vous avez reçu de retours de crédits |
| Crédits Azure appliqués | Crédits Azure qui sont appliqués automatiquement aux frais Azure chaque période de facturation |
| Sous-total |Le montant hors taxes dû |
| Taxe |Le type et le montant des taxes à payer, selon le pays/la région de votre profil de facturation. Si vous n’êtes pas obligé de taxe, puis vous les verrez taxes sur votre facture. |
| Économies totales estimés |Le montant total estimé que vous avez enregistré à partir de remises efficaces. Le cas échéant, taux de remise efficaces sont répertoriés sous les éléments de ligne d’achat dans les détails par Section de la facture. |

### <a name="invoice-sections"></a>Sections de facture

Pour chaque section de la facture, sous votre profil de facturation, vous verrez les frais, le montant des crédits Azure appliqué, taxes et le montant total dû.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Détails par section de la facture

Les détails indiquent le coût pour chaque section de facture ventilé par ordre de produit. Dans chaque commande du produit, le coût est divisé par le type de service. Vous pouvez trouver des frais quotidiens pour vos produits et services dans le portail Azure et de l’utilisation d’Azure et de frais CSV. Pour en savoir plus, consultez [comprendre les frais sur votre facture pour un contrat de client Microsoft](billing-mca-understand-your-bill.md).

Le montant total dû pour chaque famille de service est calculé en soustrayant *crédits Azure* de *crédits/frais* et l’ajout de *taxe*:

<!-- `Total = Charges/Credits - Azure Credit + Tax` -->

![Détails par section de la facture](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Terme |Description  |
| --- | --- |
| Prix unitaire | Le prix unitaires efficaces du service (dans la devise de tarification) qui est utilisé pour le taux de l’utilisation. Il est unique pour un produit, service famille, jauge et offre. |
| Qty | Quantité achetée ou consommées pendant la période de facturation |
| Frais et crédits | Montant net des frais après application des crédits/remboursements |
| Crédit Azure | Le montant des crédits Azure appliqué aux frais/crédits|
| Taux d’imposition | Taux de t.v.a. selon le pays/région |
| Montant des taxes | Montant de taxe appliquée pour l’achat basé sur les taux d’imposition |
| Total | Le montant total dû pour l’achat |

### <a name="how-to-pay"></a>Mode de paiement

En bas de la facture, il existe des instructions pour payer votre facture. Vous pouvez payer par vérification, câble, ou en ligne. Si vous payez en ligne, vous pouvez utiliser une carte de crédit ou de crédits Azure, le cas échéant.

### <a name="publisher-information"></a>Informations sur l’éditeur

Si vous avez des services tiers dans votre facture, le nom et l’adresse de chaque serveur de publication est indiqué au bas de votre facture.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifiez l’accès à un contrat de client de Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créer une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- [Comprendre les frais de facture de votre profil facturation](billing-mca-understand-your-bill.md)
- [Obtention de votre facture Azure et de vos données d’utilisation quotidienne](billing-download-azure-invoice-daily-usage-date.md)
- [Affichez la tarification Azure de votre organisation](billing-ea-pricing.md)
- [Afficher des documents de taxe pour votre profil de facturation](billing-mca-download-tax-document.md)
