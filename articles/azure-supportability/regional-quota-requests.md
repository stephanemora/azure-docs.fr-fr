---
title: Demandes d’augmentation de quota régional pour Azure | Microsoft Docs
description: Demandes d’augmentation de quota régional
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e73f22b0e617ad8f20b98c3bb0fb1647bf5fe61d
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249075"
---
# <a name="total-regional-vcpu-limit-increase"></a>Augmentation de la limite totale régionale des processeurs virtuels 

Les quotas de processeurs virtuels de Resource Manager pour les machines virtuelles et les groupes de machines virtuelles identiques sont appliqués en deux niveaux pour chaque abonnement, dans chaque région. 

Le premier niveau est la **limite de total de processeurs virtuels régionaux** (sur toutes les séries de machines virtuelles), et le deuxième niveau est la **limite de processeurs virtuels par série de machines virtuelles** (par exemple, les processeurs virtuels de la série D). Chaque fois qu’une nouvelle machine virtuelle est déployée, la somme des processeurs virtuels nouveaux et existants pour cette série de machines virtuelles ne doit pas dépasser le quota de processeurs virtuels approuvé pour cette série de machines virtuelles particulière. En outre, le nombre total de processeurs virtuels nouveaux et existants déployés sur toutes les séries de machines virtuelles ne doit pas dépasser le quota de processeurs virtuels régionaux total approuvé pour l’abonnement. Si l’un de ces quotas est dépassé, le déploiement des machines virtuelles n’est pas autorisé.
Vous pouvez demander une augmentation de la limite de quota de processeurs virtuels pour une série de machines virtuelles à partir du portail Azure. Une augmentation du quota de série de machines virtuelles augmente automatiquement la limite totale de processeurs virtuels régionaux du même montant. 

Lorsqu’un nouvel abonnement est créé, la valeur par défaut du total des processeurs virtuels régionaux peut ne pas être égale à la somme des quotas de processeurs virtuels par défaut pour toutes les séries de machines virtuelles individuelles. Cela peut conduire à un abonnement avec un quota suffisant pour chaque série de machines virtuelles individuelle que vous souhaitez déployer, mais insuffisant pour le total de processeurs virtuels régionaux pour tous les déploiements. Dans ce cas, vous devez envoyer une demande d’augmentation de la limite totale de processeurs virtuels régionaux explicitement. La limite du nombre total de processeurs virtuels régionaux ne peut pas dépasser la somme des quotas approuvés sur toutes les séries de machines virtuelles pour la région.

Apprenez-en davantage sur les quotas sur la [page Quotas de processeurs virtuels pour les machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) et la page [Limites du service et de l’abonnement Azure](https://aka.ms/quotalimits). 

Vous pouvez désormais demander une augmentation via le panneau **Aide + Support** ou **Utilisation + Quotas** dans le portail. 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Demandez une augmentation du quota total de processeurs virtuels régionaux de votre abonnement à l’aide du panneau **Aide + Support**

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

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. Dans le volet **Détails**, fournissez des informations supplémentaires comme l’exemple ci-dessous pour faciliter le traitement de votre demande et poursuivre la création du dossier. 
    1.  **Modèle de déploiement** – Spécifiez « Resource Manager »
    2.  **Région demandée** – Spécifiez votre région requise, par exemple, USA Est 2
    3.  **Nouvelle valeur de limite** : Spécifiez une nouvelle limite régionale. Elle ne doit pas dépasser la somme des quotas approuvés pour les familles de références SKU individuelles pour cet abonnement

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

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

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. Dans le volet **Détails**, fournissez des informations supplémentaires comme l’exemple ci-dessous pour faciliter le traitement de votre demande et poursuivre la création du dossier. 
    1.  **Modèle de déploiement** – Spécifiez « Resource Manager »
    2.  **Région demandée** – Spécifiez votre région requise, par exemple, USA Est 2
    3.  **Nouvelle valeur de limite** : Spécifiez une nouvelle limite régionale. Elle ne doit pas dépasser la somme des quotas approuvés pour les familles de références SKU individuelles pour cet abonnement

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)



