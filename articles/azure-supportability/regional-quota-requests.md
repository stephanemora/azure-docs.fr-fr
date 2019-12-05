---
title: Demandes d’augmentation de quota régional pour Azure | Microsoft Docs
description: Demandes d’augmentation de quota régional
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 6c2d7dc64bca00ce664dc470e7c4405d69b49779
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74531517"
---
# <a name="standard-quota-regional-vcpu-limit-increase"></a>Quota standard : augmentation de la limite régionale des processeurs virtuels 

Resource Manager prend en charge deux types de quotas de processeurs virtuels pour les machines virtuelles. Les **machines virtuelles avec paiement à l’utilisation** et les **instances de machine virtuelle réservées** utilisent un quota Standard. Les **machines virtuelles basse priorité** utilisent un quota Basse priorité. 

Le quota de processeurs virtuels standard pour les instances de machine virtuelle réservées et avec paiement à l’utilisation est appliqué à deux niveaux pour chaque abonnement dans chaque région.
 
Le premier niveau est la **limite de total de processeurs virtuels régionaux** (sur toutes les séries de machines virtuelles), et le deuxième niveau est la **limite de processeurs virtuels par série de machines virtuelles** (par exemple, les processeurs virtuels de la série D). Chaque fois qu’une nouvelle machine virtuelle doit être déployée, la somme de l’utilisation des processeurs virtuels nouveaux et existants pour la série de machines virtuelles concernée ne doit pas dépasser le quota de processeurs virtuels approuvé pour cette série de machines virtuelles. En outre, le nombre total de processeurs virtuels nouveaux et existants déployés sur toutes les séries de machines virtuelles ne doit pas dépasser le quota de processeurs virtuels régionaux total approuvé pour l’abonnement. Si l’un de ces quotas est dépassé, le déploiement des machines virtuelles n’est pas autorisé. Vous pouvez demander une augmentation de la limite de quota de processeurs virtuels pour une série de machines virtuelles à partir du portail Azure. Une augmentation du quota de série de machines virtuelles augmente automatiquement la limite totale de processeurs virtuels régionaux du même montant.

Lorsqu’un nouvel abonnement est créé, la valeur par défaut du total des processeurs virtuels régionaux peut ne pas être égale à la somme des quotas de processeurs virtuels par défaut pour toutes les séries de machines virtuelles individuelles. Cela peut conduire à un abonnement avec un quota suffisant pour chaque série de machines virtuelles individuelle que vous souhaitez déployer, mais insuffisant pour le total de processeurs virtuels régionaux pour tous les déploiements. Dans ce cas, vous devez envoyer une demande d’augmentation de la limite totale de processeurs virtuels régionaux explicitement. La limite du nombre total de processeurs virtuels régionaux ne peut pas dépasser la somme des quotas approuvés sur toutes les séries de machines virtuelles pour la région.

Pour en savoir plus sur les quotas de processeurs virtuels standard, consultez les pages [Quotas de processeurs virtuels pour les machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) et [Limites des abonnements et services Azure](https://aka.ms/quotalimits).

Découvrez-en plus sur l’**augmentation des limites de processeur virtuel de machine virtuelle Basse priorité** [ici](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

Vous pouvez demander une augmentation de la **Limite du total de processeurs virtuels régionaux des machines virtuelles standard** via le panneau **Aide + support** ou le panneau **Utilisations + quota** dans le portail.

## <a name="request-standard-quota-regional-vcpu-limit-increase-at-subscription-level-using-the-help--support-blade"></a>Demander une augmentation de la limite de processeurs virtuels régionaux du quota standard au niveau de l’abonnement à l’aide du panneau Aide + Support

Suivez les instructions ci-dessous pour créer une demande de support via le panneau « Aide + Support » d’Azure disponible dans le portail Azure. 

1. À partir de https://portal.azure.com, sélectionnez **Aide + Support**.

![Aide + support](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Sélectionnez **Nouvelle demande de support**. 

![Nouvelle demande de support](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Dans la liste déroulante Type de problème, choisissez **Limites du service et des abonnements (quotas)** .

![Liste déroulante Type de problème](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Sélectionnez l’abonnement nécessitant une augmentation du quota.

![Sélectionner l’abonnement newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Sélectionnez **Autres requêtes** dans la liste déroulante **Type de quota**.

![Type de quota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. Dans le volet **Détails**, fournissez des informations supplémentaires comme l’exemple ci-dessous pour faciliter le traitement de votre demande et poursuivre la création du dossier. 
    1.  **Modèle de déploiement** – Spécifiez « Resource Manager »
    2.  **Région demandée** – Spécifiez votre région requise, par exemple, USA Est 2
    3.  **Nouvelle valeur de limite** : Spécifiez une nouvelle limite régionale. Elle ne doit pas dépasser la somme des quotas approuvés pour les familles de références SKU individuelles pour cet abonnement

![Détails du quota](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>Demandez une augmentation du quota total de processeurs virtuels régionaux de votre abonnement à l’aide du panneau **Utilisation + Quotas**

Suivez les instructions ci-dessous pour créer une demande de support via le panneau « Utilisation + quotas » d’Azure disponible dans le portail Azure. 

1. Dans https://portal.azure.com, sélectionnez **Abonnements**.

![Abonnements](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Sélectionnez l’abonnement nécessitant une augmentation du quota.

![Sélectionnez un abonnement](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Sélectionnez **Utilisation + quotas**.

![Sélectionnez Utilisation + quotas](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. Dans l’angle supérieur droit, cliquez sur **Demander une augmentation**.

![Demander une augmentation](./media/resource-manager-core-quotas-request/request-increase.png)

5. Sélectionnez **Autres requêtes** dans la liste déroulante **Type de quota**.

![Type de quota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. Dans le volet **Détails**, fournissez des informations supplémentaires comme l’exemple ci-dessous pour faciliter le traitement de votre demande et poursuivre la création du dossier. 
    1.  **Modèle de déploiement** – Spécifiez « Resource Manager »
    2.  **Région demandée** – Spécifiez votre région requise, par exemple, USA Est 2
    3.  **Nouvelle valeur de limite** : Spécifiez une nouvelle limite régionale. Elle ne doit pas dépasser la somme des quotas approuvés pour les familles de références SKU individuelles pour cet abonnement

![Détails du quota](./media/resource-manager-core-quotas-request/regional-details.png)



