---
title: Suivre le solde de crédit Azure d’un Contrat client Microsoft
description: Découvrez comment vérifier le solde de crédit Azure d’un Contrat client Microsoft.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 352737b3ea61a51a39e066d4211c8f4ceae74184
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490964"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Suivre le solde de crédit Azure d’un Contrat client Microsoft

Vous pouvez vérifier le solde de crédit Azure d'un Contrat client Microsoft dans le portail Azure. Vous utilisez des crédits pour payer les frais couverts par les crédits.

Vous êtes facturé lorsque vous utilisez les produits non couverts par les crédits ou lorsque votre utilisation dépasse votre solde de crédit. Pour plus d’informations, consultez [Produits non couverts par les crédits Azure.(#products-that-arent-covered-by-azure-credits).

Cet article s'applique à un compte de facturation associé à un contrat client Microsoft. [Vérifiez que vous avez accès à un contrat client Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Vérifier votre solde de crédit

1. Connectez-vous au [Portail Azure]( https://portal.azure.com).

2. Recherchez **Gestion des coûts + facturation**.

    ![Capture d’écran montrant la recherche dans le portail pour gestion des coûts + facturation](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Sélectionnez **Crédits Azure** dans la partie gauche. Selon votre accès, vous devrez peut-être sélectionner un compte ou profil de facturation, puis sélectionner **Crédits Azure**.

4. La page Crédits Azure affiche les informations suivantes :

   ![Capture d’écran d'un solde de crédit et de transactions pour un profil de facturation](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Terme               | Définition                           |
   |--------------------|--------------------------------------------------------|
   | Solde estimé  | Montant estimé de crédits dont vous disposez après prise en compte de toutes les transactions facturées et en attente |
   | Solde actuel    | Montant des crédits depuis votre dernière facture. Les transactions en attente ne sont pas incluses. |
   | Transactions       | Toutes les transactions de facturation qui ont affecté votre solde de crédit Azure |

   Lorsque votre solde estimé atteint 0, vous êtes facturé pour votre utilisation, y compris pour les produits couverts par les crédits.

6. Sélectionnez **Liste des crédits** pour afficher la liste des crédits du profil de facturation. La liste des crédits fournit les informations suivantes :

   ![Capture d’écran des listes de crédits d'un profil de facturation](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Terme | Définition |
   |---|---|
   | Solde estimé | Le montant du crédit Azure dont vous disposez après avoir soustrait les frais éligibles de crédit non facturés de votre solde actuel|
   | Solde actuel | Le montant du crédit Azure dont vous disposez avant de considérer les frais éligibles de crédit non facturés. Il est calculé en ajoutant les nouveaux crédits Azure que vous avez reçu au solde de crédit au moment de votre dernière facture|
   | Source | Source d’acquisition du crédit |
   | Date de début | Date d'acquisition du crédit |
   | Date d'expiration | Date d’expiration du crédit |
   | Balance | Solde depuis votre dernière facture |
   | Montant d’origine | Montant d'origine du crédit |
   | Statut | État actuel du crédit. Cet état peut être actif, utilisé, expiré ou en cours d'expiration |

## <a name="how-credits-are-used"></a>Utilisation des crédits

Dans un compte de facturation pour un Contrat client Microsoft, vous utilisez les profils de facturation pour gérer vos factures et modes de paiement. Une facture mensuelle est générée pour chaque profil de facturation et les modes de paiement permettent de payer la facture.

Les crédits Azure constituent l'un de ces modes de paiement. Vous obtenez un crédit de la part de Microsoft, comme un crédit promotionnel ou un crédit de niveau de service. Ces crédits sont attribués à un profil de facturation. Lorsqu’une facture est générée pour le profil de facturation, les crédits sont automatiquement appliqués au montant total facturé pour calculer le montant que vous devez payer. Vous utilisez un autre mode de paiement, chèque ou virement, pour vous acquitter du montant restant.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produits non couverts par les crédits Azure

 Les produits suivants ne sont pas couverts par vos crédits Azure. Ces produits vous sont facturés, quel que soit votre solde de crédit :

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
- Produits de la Place de marché Azure
- Plans de support Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifier l’accès à un Contrat client Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes

- [Comprendre le compte de facturation associé à un Contrat client Microsoft](billing-mca-overview.md)
- [Comprendre les termes indiqués sur votre facture du Contrat client Microsoft](billing-mca-understand-your-invoice.md)
