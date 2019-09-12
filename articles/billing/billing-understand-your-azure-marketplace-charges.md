---
title: Présentation de vos frais de service externe Azure | Microsoft Docs
description: En savoir plus sur la facturation des frais des services externes, anciennement appelés Marketplace, dans Azure.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 278e873d01eb3dd7d614d771e5b50b8fe624800a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490350"
---
# <a name="understand-your-azure-external-services-charges"></a>Comprendre vos frais de service externes Azure
Les services externes sont publiés par des éditeurs de logiciels tiers sur la Place de marché Microsoft Azure. Par exemple, SendGrid est un service externe que vous pouvez acheter dans Azure, mais qui n’est pas publié par Microsoft. Certains produits Microsoft sont aussi vendus sur la Place de marché Azure.

## <a name="how-external-services-are-billed"></a>Facturation des services externes

- Si vous avez un [contrat client Microsoft](#check-access), vos services tiers sont facturés avec le reste de vos services Azure.
- Dans le cas contraire, vos services externes sont facturés indépendamment de vos services Azure.
- Chaque service externe possède un modèle de facturation différent. Certains services sont facturés selon un mode de paiement à l’utilisation, tandis que d’autres utilisent un modèle de paiement mensuel fixe.
- Vous ne pouvez pas utiliser de crédits mensuels gratuits pour les services externes. Si vous utilisez un abonnement Azure incluant des [crédits gratuits](https://azure.microsoft.com/pricing/spending-limits/), ceux-ci ne peuvent pas s’appliquer aux montants des factures de services externes. Lorsque vous configurez un nouveau service externe ou une ressource, un avertissement s’affiche :

    ![Avertissement d’achat Marketplace](./media/billing-understand-your-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/billing-understand-your-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="view-and-download-invoices"></a>Afficher et télécharger des factures

Si vous avez un [contrat client Microsoft](#check-access), les frais de vos fournisseurs tiers figurent sur la même facture que vos frais Azure. Découvrez comment [afficher et télécharger votre facture Azure](billing-download-azure-invoice.md) à partir du portail Microsoft Azure, afin de consulter les frais de vos fournisseurs tiers.

Si vous n’avez pas de contrat client Microsoft, les frais de vos fournisseurs tiers sont facturés de manière séparée. Vous pouvez afficher et télécharger vos factures associées à la Place de marché Azure à partir du portail Microsoft Azure, en procédant comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Recherchez **Gestion des coûts + facturation**.
1. Dans le menu de gauche, sélectionnez **Factures**.
1. Cliquez sur l’onglet **Azure Marketplace and Reservations** (Place de marché Azure et réservations).  ![Image représentant l’onglet Azure Marketplace and Reservations (Place de marché Azure et réservations)](./media/billing-understand-your-azure-marketplace-charges/invoice-tabs.png)
1. Dans la liste déroulante relative aux abonnements, sélectionnez celui qui contient les services externes dont vous voulez afficher les factures.

## <a name="external-spending-for-ea-customers"></a>Dépenses externes pour les clients EA

Les clients de contrat Entreprise (EA) peuvent voir les dépenses du service externe et télécharger des rapports dans le portail EA. Consultez la page sur [Azure Marketplace pour les clients EA](https://ea.azure.com/helpdocs/azureMarketplace) pour commencer.

## <a name="manage-payment-for-external-services"></a>Gérer les paiements pour les services externes

Lorsque vous achetez un service externe, vous choisissez un abonnement Azure pour la ressource. Le mode de paiement de l’abonnement Azure sélectionné devient le mode de paiement du service externe. Pour modifier le mode de paiement d’un service externe, vous devez [changer le mode de paiement de l’abonnement Azure](billing-how-to-change-credit-card.md) lié à ce service externe. Vous pouvez identifier l’abonnement auquel votre commande de service externe est liée en procédant comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Cliquez sur l’option **Toutes les ressources** figurant dans le menu de navigation gauche.
     ![Capture d’écran de l’élément de menu Toutes les ressources](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Recherchez votre service externe.
1. Dans la colonne **Abonnement**, recherchez le nom de l’abonnement.
    ![Capture d’écran du nom d’abonnement pour la ressource](./media/billing-understand-your-azure-marketplace-charges/sub-selected.png)
1. Cliquez sur le nom de l’abonnement et [mettez à jour le mode de paiement actif](billing-how-to-change-credit-card.md).

<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)

    ![Select marketplace in the account center](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage

    ![Select the external service you want to manage](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.

    ![Order summary](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.

    ![Select edit info](./media/billing-understand-your-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>Annuler une commande de service externe
Si vous souhaitez annuler votre commande de service externe, supprimez la ressource dans le [portail Azure](https://portal.azure.com).

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Cliquez sur l’option **Toutes les ressources** figurant dans le menu de navigation gauche.
    ![Capture d’écran de l’élément de menu Toutes les ressources](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Recherchez votre service externe.
1. Cochez la case en regard de la ressource à supprimer.
1. Sélectionnez **Supprimer** dans la barre de commandes.
    ![Capture d’écran du bouton Supprimer](./media/billing-understand-your-azure-marketplace-charges/delete-button.png)
1. Cliquez sur *Oui* dans le panneau de confirmation.
    ![Supprimer la ressource](./media/billing-understand-your-azure-marketplace-charges/delete-resource.PNG)
1. Cliquez sur **Supprimer**.

## <a name="check-access"></a>Vérifier l’accès
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes
- [Démarrer l’analyse des coûts](../cost-management/quick-acm-cost-analysis.md)
