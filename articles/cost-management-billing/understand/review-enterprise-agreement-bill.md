---
title: Consulter votre facture Contrat Entreprise Azure
description: Découvrez comment lire et comprendre vos données d’utilisation et de facture pour un Contrat Entreprise Azure.
author: banders
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2020
ms.author: banders
ms.openlocfilehash: 75b6dd1b2d76d12087270e155551402d1bcd4965
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274022"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Comprendre votre facture Contrat Entreprise Azure

Les clients Azure qui ont un Contrat Entreprise reçoivent une facture quand ils dépassent le crédit de l’organisation ou utilisent des services qui ne sont pas couverts par le crédit.

Le crédit de votre organisation comprend votre engagement financier. L’engagement financier correspond au montant que votre organisation a payé à l’avance pour l’utilisation des services Azure. Vous pouvez ajouter des fonds d’engagement financier à votre Contrat Entreprise en contactant votre revendeur ou votre responsable de compte Microsoft.

Ce tutoriel s’applique uniquement aux clients Azure disposant d’un Contrat Entreprise Azure.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Passer en revue les frais facturés.
> * Passer en revue les frais de dépassement de service.
> * Passer en revue la facture de la Place de marché.

## <a name="prerequisites"></a>Conditions préalables requises

Pour consulter et vérifier les frais figurant sur votre facture, vous devez être administrateur d’entreprise. Pour plus d’informations, consultez [Comprendre les rôles d’administrateur Contrat Entreprise Azure dans Azure](../manage/understand-ea-roles.md). Si vous ignorez qui est l’administrateur d’entreprise de votre organisation, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="review-invoiced-charges-for-most-customers"></a>Passer en revue les frais facturés pour la plupart des clients

Cette section ne s’applique pas aux clients Azure en Australie, au Japon ou à Singapour.

Vous recevez une facture Azure si l’un des événements suivants se produit au cours de la période de facturation :

- **Dépassement de service** : les frais d’utilisation de votre organisation dépassent votre solde de crédit.
- **Frais facturés séparément** : les services utilisés par votre organisation ne sont pas couverts par le crédit. Les services suivants vous sont facturés, malgré votre solde de crédit :
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
- **Frais de la Place de marché** : les achats et l’utilisation liés à la Place de marché Azure ne sont pas couverts par le crédit de votre organisation. Les frais de la Place de marché vous sont donc facturés, malgré votre solde de crédit. Sur Enterprise Portal, un administrateur d’entreprise peut activer et désactiver les achats liés à la Place de marché.

Votre facture montre toute votre utilisation d’Azure, suivie de tous les frais liés à la Place de marché. Si vous avez un solde de crédit, il est appliqué à l’utilisation d’Azure.

Comparez votre montant total combiné indiqué sur Enterprise Portal dans **Rapports** > **Récapitulatif de l’utilisation** avec votre facture Azure. Les montants dans le **Récapitulatif de l’utilisation** n’incluent pas les taxes.

Connectez-vous au [portail Azure EA](https://ea.azure.com). Ensuite, sélectionnez **Rapports**. En haut à droite de l’onglet, changez de vue en passant de **M** à **C**, et faites correspondre la période sur la facture.  

![Capture d’écran qui montre l’option M + C dans le récapitulatif de l’utilisation.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Le montant combiné de l’**Utilisation totale** et de la **Place de marché Azure** doit correspondre au **Total final** sur votre facture. Pour obtenir plus de détails sur vos frais, accédez à **Télécharger l’utilisation**.  

![Capture d’écran montrant l’onglet Télécharger l’utilisation](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>Passer en revue les frais facturés pour d’autres clients

Cette section s’applique uniquement aux clients Azure en Australie, au Japon ou à Singapour.

Vous recevez une ou plusieurs factures Azure dans les cas suivants :

- **Dépassement de service** : les frais d’utilisation de votre organisation dépassent votre solde de crédit.
- **Frais facturés séparément** : les services utilisés par votre organisation ne sont pas couverts par le crédit. Les services suivants vous sont facturés :
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
- **Frais de la Place de marché** : Les achats et l’utilisation liés à la Place de marché Azure ne sont pas couverts par le crédit de votre organisation et sont facturés séparément. Sur Enterprise Portal, un administrateur d’entreprise peut activer et désactiver les achats liés à la Place de marché.

Quand vous avez des frais de dépassements de service et des frais facturés séparément au cours de la période de facturation, vous recevez une facture. Elle inclut les deux types de frais. Les frais de la Place de marché sont toujours facturés séparément.

## <a name="review-service-overage-charges-for-other-customers"></a>Passer en revue les frais de dépassement de service pour d’autres clients

Cette section s’applique uniquement si vous vous trouvez en Australie, au Japon ou à Singapour.

Comparez le montant total de votre utilisation sur Enterprise Portal dans **Rapports** > **Récapitulatif de l’utilisation** avec votre facture de dépassement de service. La facture de dépassement de service inclut l’utilisation qui dépasse le crédit de votre organisation et/ou les services qui ne sont pas couverts par le crédit. Les montants du **Récapitulatif de l’utilisation** n’incluent pas les taxes.

Connectez-vous au [portail Azure EA](https://ea.azure.com), puis sélectionnez **Rapports**. En haut à droite de l’onglet, changez de vue en passant de **M** à **C**, et faites correspondre la période sur la facture.  

![Capture d’écran qui montre l’option M + C dans le récapitulatif de l’utilisation.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Le montant **Utilisation totale** doit correspondre au **Total final** de votre facture de dépassement de service. Pour plus d’informations sur vos frais, accédez à **Télécharger l’utilisation** > **Téléchargement de rapport avancé**. Le rapport n’inclut pas les taxes ou frais de réservation, ni les frais de la Place de marché.  

![Capture d’écran montrant le téléchargement de rapport avancé sous l’onglet Téléchargement de l’utilisation.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

Le tableau suivant liste les termes et descriptions figurant sur la facture ainsi que sur le **Récapitulatif de l’utilisation** dans Enterprise Portal :

|Terme relatif à la facturation|Terme relatif au récapitulatif de l’utilisation|Description|
|---|---|---|
|Total final|Utilisation totale|Total des frais d’utilisation hors taxes pour la période spécifique avant l’application du crédit.|
|Utilisation de l’engagement|Utilisation de l’engagement|Crédit appliqué pendant cette période spécifique.|
|Total de la vente|Dépassement total|Montant total des frais d’utilisation dépassant le montant de votre crédit. Ce montant n’inclut pas les taxes.|
|Montant des taxes|Non applicable|Taxes qui s’appliquent au montant total de la vente pour la période spécifique.|
|Montant total|Non applicable|Montant dû pour la facture une fois le crédit appliqué et la taxe ajoutée.|

### <a name="review-marketplace-invoice"></a>Passer en revue la facture de la Place de marché.

Cette section s’applique uniquement si vous vous trouvez en Australie, au Japon ou à Singapour.

Comparez le total de la Place de marché Azure sur **Rapports** > **Récapitulatif de l’utilisation** dans Enterprise Portal avec votre facture de la Place de marché. La facture de la Place de marché concerne uniquement les achats et l’utilisation liés à la Place de marché Azure. Les montants du **Récapitulatif de l’utilisation** n’incluent pas les taxes.

Connectez-vous au [portail Entreprise](https://ea.azure.com), puis sélectionnez **Rapports**. En haut à droite de l’onglet, changez de vue en passant de **M** à **C**, et faites correspondre la période sur la facture.  

![Capture d’écran qui montre l’option M + C dans le récapitulatif de l’utilisation.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

Le total de la **Place de marché Azure** doit correspondre au **Total de la vente** sur votre facture de la Place de marché. Pour plus d’informations sur vos frais d’utilisation, accédez à **Télécharger l’utilisation**. Sous **Frais de la Place de marché**, sélectionnez **Télécharger**. Le prix de la Place de marché comprend une taxe déterminée par l’éditeur. Les clients ne recevront pas de facture distincte de la part de l’éditeur pour collecter les taxes sur la transaction.

![Capture d’écran montrant l’option de téléchargement sous les frais de la Place de marché.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Passer en revue les frais facturés.
> * Passer en revue les frais de dépassement de service.
> * Passer en revue la facture de la Place de marché.

Passez à l’article suivant pour en savoir plus sur l’utilisation du portail Azure EA.

> [!div class="nextstepaction"]
> [Bien démarrer avec le portail Azure EA](../manage/ea-portal-get-started.md)
