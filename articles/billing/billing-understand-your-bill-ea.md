---
title: Comprendre votre facture pour Azure Enterprise | Microsoft Docs
description: Découvrir comment lire et comprendre vos données d’utilisation et de facture pour les clients Azure ayant un Contrat Entreprise
services: ''
documentationcenter: ''
author: adpick
manager: dougeby
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: banders
ms.openlocfilehash: 36ce4d96e02bac1eae1791acf811da468726b4a6
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902701"
---
# <a name="understand-your-bill-for-azure-customers-with-an-enterprise-agreement"></a>Comprendre la facture des clients Azure ayant un Contrat Entreprise

Les clients Azure qui ont un Contrat Entreprise reçoivent une facture quand ils dépassent le crédit de l’organisation ou utilisent des services qui ne sont pas couverts par le crédit. 

Le crédit de votre organisation comprend votre engagement financier. L’engagement financier correspond au montant que votre organisation a payé à l’avance pour l’utilisation des services Azure. Vous pouvez ajouter des fonds d’engagement financier à votre Contrat Entreprise en contactant votre revendeur ou votre responsable de compte Microsoft.  

## <a name="when-credit-exceeded-or-doesnt-apply"></a>Quand le crédit est dépassé ou ne s’applique pas

Vous recevez une ou plusieurs factures dans les cas suivants :

- **Dépassement de service** : les frais d’utilisation de votre organisation dépassent votre solde de crédit.
- **Frais facturés séparément** : les services utilisés par votre organisation ne sont pas couverts par le crédit. Vous êtes facturé pour les services suivants, quel que soit votre solde de crédit :
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
- **Frais de la Place de marché** : les achats et l’utilisation liés à la Place de marché Azure ne sont pas couverts par le crédit de votre organisation et sont facturés séparément. L’administrateur d’entreprise peut activer et désactiver les achats sur la Place de marché pour son organisation dans Enterprise Portal. 

Quand vous avez des frais de dépassement de service et des frais facturés séparément au cours de la période de facturation, vous recevez une facture qui inclut les deux types de frais. Les frais de la Place de marché sont toujours facturés séparément.

## <a name="review-charges"></a>Passer en revue les frais

Pour consulter et vérifier les frais figurant sur votre facture, vous devez être administrateur d’entreprise. Pour plus d’informations, consultez [Comprendre les rôles d’administrateur Contrat Entreprise Azure dans Azure](billing-understand-ea-roles.md). Si vous ignorez qui est l’administrateur d’entreprise de votre organisation, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="review-service-overage-invoice"></a>Passer en revue la facture de dépassement de service

Comparez le montant total de votre utilisation sur Enterprise Portal dans **Rapports** > **Récapitulatif de l’utilisation** avec votre facture de dépassement de service. La facture de dépassement de service inclut l’utilisation qui dépasse le crédit de votre organisation et/ou les services qui ne sont pas couverts par le crédit. Les montants du **Récapitulatif de l’utilisation** n’incluent pas les taxes. 

1. Connectez-vous à [Enterprise Portal](https://ea.azure.com).
1. Sélectionnez **Rapports**.
1. Dans le coin supérieur droit de l’onglet, changez d’affichage en passant de **M** à **C**, et faites correspondre la période sur la facture.
 
   ![Capture d’écran qui montre l’option M + C dans le récapitulatif de l’utilisation.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. Le montant **Utilisation totale** doit correspondre au **Total final** de votre facture de dépassement de service. Le tableau suivant liste les termes et descriptions figurant sur la facture ainsi que sur le **Récapitulatif de l’utilisation** dans Enterprise Portal :

   |Terme relatif à la facturation|Terme relatif au récapitulatif de l’utilisation|Description|
   |---|---|---|
   |Total final|Utilisation totale|Total des frais d’utilisation hors taxes pour la période spécifique avant l’application du crédit.|
   |Utilisation de l’engagement|Utilisation de l’engagement|Crédit appliqué pendant cette période spécifique.|
   |Total de la vente|Dépassement total|Montant total des frais d’utilisation dépassant le montant de votre crédit. Ce montant n’inclut pas les taxes.|
   |Montant des taxes|Non applicable|Taxes qui s’appliquent au montant total de la vente pour la période spécifique.|
   |Montant total|Non applicable|Montant dû pour la facture une fois le crédit appliqué et la taxe ajoutée.|
1. Pour plus d’informations sur vos frais, accédez à **Télécharger l’utilisation** > **Téléchargement de rapport avancé**. Ce rapport n’inclut pas les taxes ou frais de réservation, ni les frais de la Place de marché.

      ![Capture d’écran montrant le téléchargement de rapport avancé sous l’onglet Téléchargement de l’utilisation.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-advanced.png)

### <a name="review-marketplace-invoice"></a>Passer en revue la facture de la Place de marché

Comparez le total de la Place de marché Azure sur **Rapports** > **Récapitulatif de l’utilisation** dans Enterprise Portal avec votre facture de la Place de marché. La facture de la Place de marché concerne uniquement les achats et l’utilisation liés à la Place de marché Azure. Les montants du **Récapitulatif de l’utilisation** n’incluent pas les taxes. 

1. Connectez-vous à [Enterprise Portal](https://ea.azure.com).
1. Sélectionnez **Rapports**.
1. Dans le coin supérieur droit de l’onglet, changez d’affichage en passant de **M** à **C**, et faites correspondre la période sur la facture.

     ![Capture d’écran qui montre l’option M + C dans le récapitulatif de l’utilisation.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. Le total de la **Place de marché Azure** doit correspondre au **Total de la vente** sur votre facture de la Place de marché.
1. Pour plus d’informations sur vos frais d’utilisation, accédez à **Télécharger l’utilisation**. Sous **Frais de la Place de marché**, sélectionnez **Télécharger**. Ce rapport n’inclut pas les taxes et ne montre pas les achats définitifs.

     ![Capture d’écran montrant l’option de téléchargement sous les frais de la Place de marché.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-marketplace.png)

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
