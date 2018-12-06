---
title: Annulation de votre abonnement Azure | Microsoft Docs
description: Décrit comment annuler votre abonnement Azure, par exemple l’abonnement d’essai gratuit
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: cwatson
ms.openlocfilehash: de5fd6e52ab93132920b1c98188cbea92b9900b5
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584347"
---
# <a name="cancel-your-subscription-for-azure"></a>Annuler votre abonnement Azure

Vous pouvez annuler votre abonnement Azure en tant qu’[administrateur de compte](billing-subscription-transfer.md#whoisaa). Après l’annulation de l’abonnement, vous n’avez plus accès aux ressources ni aux services Azure.

Avant d’annuler votre abonnement :

* Sauvegardez vos données. Par exemple, si vous stockez des données dans le stockage Azure ou SQL, téléchargez une copie. Si vous avez une machine virtuelle, enregistrez une image localement.
* Arrêtez les services. Accédez à la [page de ressources du portail de gestion](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) et **arrêtez** les machines virtuelles, les applications ou autres services en cours d’exécution.
* Envisagez de migrer vos données. Consultez la page [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../azure-resource-manager/resource-group-move-resources.md).

Si vous annulez un plan de support Azure, vous êtes toujours facturé du reliquat de l’abonnement. Pour plus d’informations, consultez les [Plans de support Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-using-the-azure-portal"></a>Annuler l’abonnement à l’aide du portail Azure

1. Sélectionnez votre abonnement dans la [page Abonnements du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Sélectionnez l’abonnement à annuler, puis cliquez sur **Annuler l’abonnement**.

    ![Capture d’écran qui montre le bouton Annuler](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
1. Suivez les invites et terminez l’annulation.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Que se passe-t-il après l’annulation de mon abonnement ?

Une fois que vous l’annulez, la facturation s’arrête immédiatement. Toutefois, l’annulation peut prendre jusqu’à 10 minutes avant d’être visible sur le portail.

Une fois ce délai passé, vos services sont désactivés. Cela signifie que vos machines virtuelles et les adresses IP temporaires sont libérées et que le stockage est en lecture seule.

Si vous annulez en milieu de période de facturation, nous envoyons la facture finale à la date de facture par défaut à la fin de la période. 

Nous attendons 90 jours avant de supprimer définitivement vos données au cas où vous devriez y accéder ou changeriez d’avis. Nous ne vous facturons pas la conservation des données. Pour en savoir plus, consultez [Microsoft Trust Center - How we manage your data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) (Centre de gestion de la confidentialité de Microsoft - Comment nous gérons vos données).

## <a name="reactivate-subscription"></a>Réactivation de l’abonnement

Si vous annulez par inadvertance votre abonnement de paiement à l’utilisation, vous pouvez [le réactiver dans le Centre des comptes](billing-subscription-become-disable.md).

Si votre abonnement n’est pas un abonnement de paiement à l’utilisation, contactez le support dans les 90 jours suivant l’annulation pour réactiver votre abonnement.

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) (Support request).
