---
title: Suivre le solde de crédit Azure pour le contrat du client Microsoft | Microsoft Docs
description: Apprenez à vérifier le solde de crédit Azure pour le contrat de client de Microsoft.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: amberb
ms.openlocfilehash: 1e8c3e6863b9cd8f2f5ced18a57918c32c865e75
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544594"
---
# <a name="track-azure-credit-balance-for-microsoft-customer-agreement"></a>Suivre le solde de crédit Azure pour le contrat de client de Microsoft

Vous pouvez vérifier le solde du crédit Azure pour le contrat de client de Microsoft dans le portail Azure. Vous utilisez des crédits pour payer les produits qui sont couverts par les crédits.

Vous êtes facturé lorsque vous utilisez les produits qui ne sont pas couverts par les crédits ou votre utilisation dépasse votre solde de crédit. Pour plus d’informations, consultez [produits qui ne sont pas couverts par les crédits Azure.](#products-that-arent-covered-by-azure-credits)

Cet article s’applique à un compte de facturation d’un contrat de client de Microsoft. [Vérifiez si vous avez accès à un contrat de client Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-credit-balance-in-the-azure-portal"></a>Consulter le solde de crédit dans le portail Azure

1. Connectez-vous au [Portail Azure]( https://portal.azure.com).

2. Effectuez une recherche sur **Gestion des coûts + facturation**.

   ![Capture d’écran montrant la recherche dans le portail de gestion des coûts + facturation](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. Atteindre le profil de facturation. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation. Dans le compte de facturation, sélectionnez **profils de facturation** et ensuite un profil de facturation.

4. Sélectionnez **crédits Azure**.

5. La page de crédits Azure affiche les informations suivantes :

   ![Capture d’écran de solde de crédit et de transactions pour un profil de facturation](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Terme               | Définition                           |
   |--------------------|--------------------------------------------------------|
   | Solde estimé  | Estimation de la quantité de crédits que vous avez après que prise en compte tous les facturé et transactions en attente |
   | Solde actuel    | Montant des crédits depuis votre dernière facture. Il n’inclut pas les transactions en attente |
   | Transactions       | Toutes les transactions de facturation qui a affecté le solde de votre crédit Azure |

   Lorsque votre solde estimé atteint 0, vous êtes facturé pour votre utilisation, y compris pour les produits qui sont couverts par les crédits.

6. Sélectionnez **liste de crédits** pour afficher la liste de crédits pour le profil de facturation. La liste de crédits fournit les informations suivantes :

   ![Capture d’écran de listes de crédits pour un profil de facturation](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Terme                 | Définition                           |
   |----------------------|--------------------------------------------------------|
   | Source               | La source de l’acquisition du crédit |
   | Date de début           | La date lorsque vous avez acquis le crédit |
   | Date d'expiration      | La date d’expiration du crédit |
   | Balance              | Le solde à votre dernière facture |
   | Montant d’origine      | Le montant du crédit d’origine |
   | Statut               | L’état actuel de crédit. État peut être actif, utilisé, expiré ou arrive à expiration |

## <a name="how-credits-are-used-in-microsoft-customer-agreement"></a>Comment les crédits sont utilisés dans le contrat de client de Microsoft

Dans un compte de facturation d’un contrat de client de Microsoft, les profils de facturation vous permet de gérer vos factures et les modes de paiement. Une facture mensuelle est générée pour chaque profil de facturation et les modes de paiement vous permet de payer la facture.

Crédits Azure sont une des méthodes de paiement. Vous obtenez un crédit à partir de Microsoft comme crédit de promotion et le crédit de niveau de service. Ces crédits sont affectés à un profil de facturation. Lorsqu’une facture est générée pour le profil de facturation, les crédits sont appliqués automatiquement pour le montant total facturé pour calculer le montant que vous devez payer. Vous payez la quantité restante avec une autre méthode de paiement comme vérification ou virement.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produits qui ne sont pas couverts par les crédits Azure

 Les produits suivants ne sont pas couvertes par vos crédits Azure. Vous êtes facturé pour l’utilisation de ces produits, quel que soit votre solde de crédit :

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop 
- Utilisateur inscrit
- Openlogic
- Utilisateur inscrit de Remote Access Rights XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise (mensuel)
- Visual Studio Enterprise (annuel)
- Visual Studio Professional (mensuel)
- Visual Studio Professional (annuel)
- Produits de la place de marché Azure
- Plans de support Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifiez l’accès à un contrat de client de Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes

- [Comprendre le compte de facturation pour un contrat de client de Microsoft](billing-mca-overview.md)
- [Comprendre les termes du contrat sur votre facture de contrat de client de Microsoft](billing-mca-understand-your-invoice.md)