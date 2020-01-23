---
title: Demander une augmentation des limites de quota pour les processeurs virtuels régionaux Azure | Microsoft Docs
description: Demandes d’augmentation de quota régional
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e262651a6e040c40dbe240ad3437eff1914aa3e5
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75898689"
---
# <a name="standard-quota-increase-limits-by-region"></a>Quota standard : augmenter les limites par région 

Azure Resource Manager prend en charge deux types de quotas de processeurs virtuels pour les machines virtuelles :
* Les *machines virtuelles avec paiement à l’utilisation* et les *instances de machine virtuelle réservées* sont soumises à un *quota de processeurs virtuels standard*.
* Les *machines virtuelles Spot* sont soumises à un *quota de processeurs virtuels Spot*. 

Le quota de processeurs virtuels Standard pour le paiement à l’utilisation et les instances de machines virtuelles réservées est appliqué à deux niveaux pour chaque abonnement de chaque région :
* Le premier niveau correspond à la *limite totale des processeurs virtuels de toutes les régions* (comprenant toutes les séries de machines virtuelles).
* Le deuxième niveau correspond à la *limite des processeurs virtuels pour chaque série de machines virtuelles* (par exemple, les processeurs virtuels de série D).
 
Chaque fois qu’une nouvelle machine virtuelle Spot est déployée, l’utilisation totale des processeurs virtuels nouveaux et existants appartenant à cette série de machines virtuelles ne doit pas dépasser le quota de processeurs virtuels approuvé pour cette série. En outre, le nombre total de processeurs virtuels nouveaux et existants déployés sur toutes les séries de machines virtuelles ne doit pas dépasser le quota total des processeurs virtuels de toutes les régions, qui a été approuvé pour l’abonnement. Si l’un de ces quotas est dépassé, le déploiement de la machine virtuelle n’est pas autorisé. 

Vous pouvez demander une augmentation de la limite de quota de processeurs virtuels pour une série de machines virtuelles à partir du portail Azure. L’augmentation du quota des séries de machines virtuelles augmente automatiquement la limite totale des processeurs virtuels régionaux de la même valeur.

Lorsque vous créez un nouvel abonnement, le nombre total par défaut de processeurs virtuels régionaux peut ne pas être égal au quota total par défaut de processeurs virtuels pour toutes les séries de machines virtuelles. Avec un tel écart, l’abonnement peut avoir un quota suffisant pour chacune des séries de machines virtuelles à déployer. Toutefois, ce quota peut aussi ne pas être suffisant pour le nombre total de processeurs virtuels régionaux de tous les déploiements. Dans ce cas, vous devez demander explicitement l’augmentation du nombre maximal de processeurs virtuels régionaux. La limite du nombre total de processeurs virtuels régionaux ne peut pas dépasser le quota total approuvé pour toutes les séries de machines virtuelles d’une région.

Pour plus d’informations sur les quotas de processeurs virtuels standard, consultez [Quotas de processeurs virtuels pour les machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) et [Limites des abonnements et services Azure](https://aka.ms/quotalimits).

Pour plus d’informations sur l’augmentation des limites de processeurs virtuels de machines virtuelles Spot, consultez [Quota Spot : augmenter les limites de toutes les séries de machines virtuelles](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

Vous pouvez demander une augmentation de la limite de quota standard des processeurs virtuels par région de deux façons, comme nous allons le voir dans les sections suivantes.

## <a name="request-a-quota-increase-by-region-from-the-help--support-pane"></a>Demander une augmentation du quota par région à partir du volet « Aide + support »

Pour demander une augmentation du quota de processeurs virtuels par région dans le volet **Aide + support**, effectuez les étapes suivantes : 

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez **Aide + support**.

   ![Lien « Aide et support »](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. Dans le volet **Aide + Support**,sélectionnez **Nouvelle demande de support**. 

    ![Nouvelle demande de support](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. Dans la liste déroulante **Type de problème**, sélectionnez **Limites du service et des abonnements (quotas)** .

   ![Liste déroulante « Type de problème »](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. Dans la liste déroulante **Abonnement**, sélectionnez l’abonnement pour lequel vous souhaitez augmenter le quota.

   ![Liste déroulante « Abonnement »](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. Dans la liste déroulante **Type de quota**, sélectionnez **Autres requêtes**.

   ![Liste déroulante « Type de quota »](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Dans le volet **Détails du problème**, dans la zone **Description**, fournissez les informations supplémentaires suivantes : 

    a. Pour **Modèle de déploiement**, spécifiez **Resource Manager**.  
    b. Pour **Région**, spécifiez votre région (par exemple, **USA Est 2**).  
    c. Pour **Nouvelle limite**, spécifiez une nouvelle limite de processeurs virtuels pour la région. Cette valeur ne doit pas dépasser la somme des quotas approuvés pour chacune des séries de références SKU de cet abonnement.

    ![Volet « Détails du problème »](./media/resource-manager-core-quotas-request/regional-details.png)

1. Sélectionnez **Enregistrer et continuer** pour poursuivre la création de la demande de support.

## <a name="request-a-quota-increase-by-region-from-the-subscriptions-pane"></a>Demander une augmentation de la limite de quota à partir du volet « Abonnements »

Pour demander une augmentation du quota de processeurs virtuels par région dans le volet **Abonnements**, effectuez les étapes suivantes : 

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez **Abonnements**.

   ![Lien « Abonnements »](./media/resource-manager-core-quotas-request/subscriptions.png)

1. Sélectionnez l’abonnement pour lequel vous souhaitez augmenter le quota.

   ![Volet « Abonnements »](./media/resource-manager-core-quotas-request/select-subscription.png)

1. Dans le volet gauche de la page **\<Nom de votre abonnement>** , sélectionnez **Utilisation + quotas**.

   ![Lien « Utilisation + quotas »](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. En haut à droite, sélectionnez **Demander une augmentation**.

   ![Demander une augmentation](./media/resource-manager-core-quotas-request/request-increase.png)

1. Dans la liste déroulante **Type de quota**, sélectionnez **Autres requêtes**.

   ![Liste déroulante « Type de quota »](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Dans le volet **Détails du problème**, dans la zone **Description**, fournissez les informations supplémentaires suivantes : 

    a. Pour **Modèle de déploiement**, spécifiez **Resource Manager**.  
    b. Pour **Région**, spécifiez votre région (par exemple, **USA Est 2**).  
    c. Pour **Nouvelle limite**, spécifiez une nouvelle limite de processeurs virtuels pour la région. Cette valeur ne doit pas dépasser la somme des quotas approuvés pour chacune des séries de références SKU de cet abonnement.

    ![Volet « Détails du problème »](./media/resource-manager-core-quotas-request/regional-details.png)

1. Sélectionnez **Enregistrer et continuer** pour poursuivre la création de la demande de support.

