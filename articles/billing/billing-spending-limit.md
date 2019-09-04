---
title: Limite de dépense d’Azure | Microsoft Docs
description: Cet article décrit le fonctionnement de la limite de dépense d’Azure et comment la désactiver.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: banders
ms.openlocfilehash: 1324b60de05805cd409975358ff78e5b2b27eef1
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114448"
---
# <a name="azure-spending-limit"></a>Limite de dépense d’Azure

La limite de dépense dans Azure vous empêche de dépasser la quantité de vos crédits. Pour tout nouveau client optant pour un essai d’Azure ou une offre incluant des crédits s’étalant sur plusieurs mois, la limite de dépense est activée par défaut. La limite de dépense est de 0 $ et n’est pas modifiable. Par exemple, vous ne pouvez pas fixer la limite de dépense à 100 $. Cependant, vous pouvez supprimer la limite de dépense. Par conséquent, soit vous n’avez pas de limite, soit vous avez une limite nulle qui vous empêche de couvrir la plupart des dépenses. La limite de dépense n’est pas disponible pour les abonnements faisant l’objet d’un paiement à l’utilisation et les plans d’engagement. Pour plus d’informations, consultez la [liste complète des offres Azure et la disponibilité de la limite de dépense](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Atteindre une limite de dépense

Si votre utilisation entraîne des frais dépassant les quantités mensuelles incluses dans votre abonnement Azure, les services que vous avez déployés sont désactivés jusqu’à la fin de cette période de facturation.

Par exemple, quand vous dépensez tous les crédits inclus dans votre abonnement, les ressources Azure que vous déployez sont supprimées de la production et vos machines virtuelles Azure sont arrêtées et désallouées. Les données de vos comptes de stockage sont accessibles en lecture seule.

Au début de la période de facturation suivante, si votre offre d’abonnement inclut des crédits sur plusieurs mois, votre abonnement est automatiquement réactivé. Vous pouvez redéployer vos ressources Azure et disposer d’un accès complet à vos comptes de stockage et bases de données.

Azure envoie des notifications par e-mail quand vous atteignez la limite de dépense de votre abonnement. Connectez-vous au [Centre des comptes](https://account.windowsazure.com/Subscriptions) pour voir les notifications concernant les abonnements qui ont atteint leur limite de dépense.

Si vous utilisez un essai gratuit et que vous atteignez la limite de dépense, vous pouvez passer à un abonnement avec [paiement à l’utilisation](billing-upgrade-azure-subscription.md) pour supprimer la limite de dépense et activer automatiquement l’abonnement.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Supprimer la limite de dépense dans le Centre des comptes

Vous pouvez supprimer la limite de dépense à tout moment, pour autant qu’un mode de paiement valide soit associé à votre abonnement Azure. Pour les offres bénéficiant d’un crédit s’étalant sur plusieurs mois, vous pouvez également activer la limite de dépense au début de votre période de facturation suivante.

Pour supprimer votre limite de dépense, procédez comme suit :

1. Connectez-vous au [Centre des comptes](https://account.windowsazure.com/Subscriptions).
1. Sélectionnez un abonnement. Si l’abonnement est désactivé car la limite de dépense est atteinte, cliquez sur la notification : **L’abonnement a atteint la limite de dépense et a été désactivé pour empêcher les frais.** Autrement, cliquez sur **Supprimer la limite de dépense** dans la zone **ÉTAT DE L’ABONNEMENT**.
1. Sélectionnez une option adaptée à votre situation.

![Sélection d’une option de suppression de la limite de dépense](./media/billing-spending-limit/remove-spending-limit.PNG)

| Option | Résultat |
| --- | --- |
| Supprimer la limite de dépense pour une durée indéterminée | Supprime la limite de dépense sans l'activer automatiquement au début de la période de facturation suivante. |
| Supprimer la limite de dépense pour la période de facturation en cours | Supprime la limite de dépense et l’active automatiquement au début de la période de facturation suivante. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Quel est l’intérêt de supprimer la limite de dépense ?

La limite de dépense peut vous empêcher de déployer ou d’utiliser certains services Microsoft et tiers. Voici les situations dans lesquelles vous devez lever la limite de dépense liée à votre abonnement.

-  Vous prévoyez de déployer des images internes telles qu’Oracle et des services tels qu’Azure DevOps Services. Cette situation entraîne presque immédiatement un dépassement de votre limite de dépense, qui a pour effet de désactiver votre abonnement.
- Vous avez des services que vous ne souhaitez pas interrompre.
- Vous avez des services et ressources utilisant des paramètres tels que des adresses IP virtuelles que vous ne voulez pas perdre. Ces paramètres sont perdus en cas de désallocation des services et ressources.

## <a name="turn-on-the-spending-limit-after-removing"></a>Activer la limite de dépense après l’avoir supprimée

Cette fonctionnalité est disponible uniquement lorsque la limite de dépense a été supprimée indéfiniment. Modifiez cette option pour l’activer automatiquement au début de la prochaine période de facturation.

1. Connectez-vous au [Centre des comptes](https://account.windowsazure.com/Subscriptions).
1. Cliquez sur la bannière jaune pour modifier l’option de limite de dépense.
1. Choisissez **Activer la limite de dépense de la prochaine période de facturation \<date de début de la période de facturation\>**

## <a name="custom-spending-limit"></a>Limite de dépense personnalisée

Les limites de dépense personnalisées ne sont pas disponibles.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Une limite de dépense n’empêche pas la facturation de tous les frais

[Certains services externes publiés sur la Place de marché Azure](billing-understand-your-azure-marketplace-charges.md) ne sont pas utilisables avec des crédits d’abonnement et risquent d’occasionner des frais distincts même si une limite de dépense est définie. C’est le cas, par exemple, des licences Visual Studio, d’Azure Active Directory Premium, des plans de support et de la plupart des services vendus sous marque de tiers. Lors de l’approvisionnement d’un nouveau service externe, un avertissement indique que les services sont facturés séparément :

![Avertissement d’achat Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes
- Effectuez une mise à niveau vers un plan avec [paiement à l’utilisation](billing-upgrade-azure-subscription.md).
