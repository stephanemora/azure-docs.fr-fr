---
title: Modifier l’offre d’abonnement Azure
description: Découvrez comment changer votre abonnement Azure et basculer vers une autre offre à l’aide du Centre des comptes Azure.
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: banders
ms.openlocfilehash: 8e083391a04188345143f8fdf9a5a945abcd9eb1
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "75986361"
---
# <a name="change-your-azure-subscription-to-a-different-offer"></a>Changer votre abonnement Azure pour une autre offre

En tant que client disposant d’un [abonnement individuel avec tarifs de paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/), vous pouvez basculer votre abonnement Azure vers une autre offre du [Centre des comptes](https://account.windowsazure.com/Subscriptions). Par exemple, vous pouvez utiliser cette fonctionnalité pour tirer parti des [crédits mensuels pour les abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

**Vous voulez juste mettre à niveau votre abonnement d’essai gratuit ?** Consultez [Mettre à niveau votre abonnement](upgrade-azure-subscription.md).

## <a name="whats-supported"></a>Opérations prises en charge :

Vous pouvez basculer votre abonnement individuel avec tarifs de paiement à l’utilisation vers les offres suivantes :

- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Plateformes MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/)

> [!NOTE]
> Pour les autres changements d’offre, [contactez le support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="switch-subscription-offer"></a>Changer d’offre d’abonnement

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. Connectez-vous au [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions).
1. Sélectionnez votre abonnement individuel avec tarifs de paiement à l’utilisation.
1. Cliquez sur **Changer d’offre**. L’option est uniquement disponible si vous avez un abonnement individuel avec tarifs de paiement à l’utilisation et si vous avez terminé votre première période de facturation.

   ![Notez le bouton de changement d’offre sur le côté droit de la page](./media/switch-azure-offer/switchbutton.png)
1. **Sélectionnez l’offre souhaitée** dans la liste des offres vers lesquelles il est possible de basculer votre abonnement. La liste varie en fonction des adhésions auxquelles est associé votre compte. Si rien n’est disponible, examinez la [liste des offres disponibles vers lesquelles il est possible de basculer](#whats-supported) et vérifiez que vous disposez des adhésions appropriées.

   ![Sélectionnez une offre à laquelle vous souhaitez passer](./media/switch-azure-offer/selectoffer.png)
1. Selon l’offre que vous envisagez d’adopter, une note peut apparaître sur l’impact du changement. Parcourez cette liste soigneusement et suivez les instructions avant de continuer.

   ![Passez en revue les notes](./media/switch-azure-offer/thingstonote.png)
1. Vous pouvez renommer votre abonnement. Par défaut, il est défini sur le nom de la nouvelle offre. Cliquez sur **Changer d’offre** pour terminer le processus.

   ![Cliquez sur le bouton vert](./media/switch-azure-offer/confirmpage.png)
1. Opération réussie. Votre abonnement est désormais associé à la nouvelle offre.

## <a name="frequently-asked-questions"></a>Forum aux questions
Les sections suivantes répondent aux questions fréquemment posées.

### <a name="what-is-an-azure-offer"></a>Qu'est-ce qu'une offre Azure ?

Une offre Azure correspond au *type* d’abonnement Azure que vous avez. Par exemple, [un abonnement avec tarifs de paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure en licence Open](https://azure.microsoft.com/offers/ms-azr-0111p/) et [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) sont tous des offres Azure. Chaque offre a différents [termes](https://azure.microsoft.com/support/legal/offer-details/) et certaines présentent des avantages particuliers. Vous trouverez l’offre de votre abonnement dans la page d’abonnement Centre des comptes. Cliquez sur le nom de l’offre pour obtenir plus de détails.

   ![Cliquez sur le lien de l’offre dans le Centre des comptes pour obtenir plus de détails](./media/switch-azure-offer/offerlink01.png)

### <a name="why-dont-i-see-the-button"></a>Pourquoi le bouton n’apparaît-il pas ?

Il est possible que le bouton **Changer pour une autre offre** ne s’affiche pas si :

* Vous ne disposez pas d’un [abonnement avec tarifs de paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/). Actuellement, seuls les abonnements avec tarifs de paiement à l’utilisation peuvent basculer vers une autre offre.
  * Si vous avez un [essai gratuit](https://azure.microsoft.com/free/), découvrez comment [mettre à niveau vers une offre de paiement à l’utilisation](upgrade-azure-subscription.md).
  * Pour basculer vers une offre d’un abonnement différent, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Vous êtes toujours dans votre première période de facturation. Vous devez attendre l’expiration de votre première période de facturation avant de pouvoir changer d’offre.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Pourquoi le message « Il n’y a pas d’offres disponibles dans votre région ou dans votre pays à l’heure actuelle. » s’affiche-t-il ?

* Vous n’êtes peut-être pas éligible à un changement d’offre. Consultez la [liste des offres disponibles vers lesquelles vous pouvez basculer](#whats-supported) et vérifiez que vous avez activé les avantages adéquats avec Visual Studio ou Bizspark.
* Certaines offres peuvent ne pas être disponibles dans tous les pays et toutes les régions.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Quel impact le basculement des offres Azure a-t-il sur mon service et ma facturation ?

Voilà ce qui se passe lorsque vous changez d’offres Azure dans le Centre des comptes.

#### <a name="no-service-downtime"></a>Aucune interruption de service.

Aucun temps d’arrêt de service n’est à déplorer pour les utilisateurs associés à l’abonnement. Toutefois, l’offre pour laquelle vous changez peut avoir des restrictions. Par exemple, certaines offres interdisent l’utilisation pour la production. Dans ce cas, il vous faudrait déplacer les ressources de production vers un autre abonnement.

#### <a name="quota-increases-are-reset"></a>Les augmentations de quota sont réinitialisées.

Lorsque vous changez d’offres, les [augmentations de limite ou de quota dépassant la limite par défaut](../../azure-portal/supportability/resource-manager-core-quotas-request.md) sont réinitialisées. Il n’existe aucune interruption de service, même si vous avez davantage de ressources au-delà de la limite par défaut. Par exemple, vous utilisez 200 cœurs sur votre abonnement, puis lorsque vous changez d’offre, votre quota de cœurs est réinitialisé à la valeur par défaut de 20 cœurs. Les machines virtuelles qui utilisent les 200 cœurs ne sont pas affectées et continuent de fonctionner. Toutefois, si vous n’effectuez pas une autre demande d’augmentation de quota, vous ne pouvez pas approvisionner davantage de cœurs.

#### <a name="billing"></a>Facturation

Le jour où vous changez d’offre, une facture est générée pour l’ensemble des frais dus. Par la suite, votre abonnement est facturé en fonction des conditions de tarification de la nouvelle offre. La date de facturation de votre abonnement devient la date à laquelle vous avez changé d’offre. Les données d’utilisation et de facturation antérieures au changement d’offre n’étant pas conservées, nous vous recommandons d’en télécharger une copie avant le changement.

### <a name="can-i-migrate-from-a-subscription-with-pay-as-you-go-rates-to-cloud-solution-provider-csp-or-enterprise-agreement-ea"></a>Puis-je effectuer la migration d’un abonnement avec paiement à l’utilisation vers un fournisseur de solutions cloud (CSP) ou un Contrat Entreprise (EA) ?

* Pour migrer vers un CSP consultez la section [Migration d’un abonnement Paiement à l’utilisation Azure vers CSP](https://docs.microsoft.com/azure/cloud-solution-provider/migration/migration-from-payg-to-csp).
* Pour migrer vers contrat Entreprise, demandez à votre administrateur d’inscription d’ajouter votre compte au contrat Entreprise. Suivez les instructions figurant dans l’e-mail d’invitation pour que vos abonnements soient déplacés vers l’inscription au contrat Entreprise. Pour plus d’informations, consultez [Associer un compte existant](https://ea.azure.com/helpdocs/associateExistingAccount) dans le portail EA.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>Puis-je migrer des données et des services vers un nouvel abonnement ?

* Vous pouvez migrer les ressources directement vers un nouvel abonnement. Consultez la rubrique [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Pour transférer la propriété d’un abonnement Azure et tout son contenu à une autre personne, consultez [Transfert de propriété d’un abonnement Azure](billing-subscription-transfer.md)

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes
- [Démarrer l’analyse des coûts](../costs/quick-acm-cost-analysis.md)
