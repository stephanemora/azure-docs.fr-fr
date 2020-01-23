---
title: Demander une augmentation des limites de quota des processeurs virtuels de chaque série de machines virtuelles Azure | Microsoft Docs
description: Cet article explique comment demander l’augmentation de la limite de quota pour chaque processeur virtuel de machine virtuelle.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: fc59ffda245834c716067bc63e6c3745fa5d23b3
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75898845"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Quota standard : augmenter les limites par série de machines virtuelles

Azure Resource Manager prend en charge deux types de quotas de processeurs virtuels pour les machines virtuelles :
* Les *machines virtuelles avec paiement à l’utilisation* et les *instances de machine virtuelle réservées* sont soumises à un *quota de processeurs virtuels standard*.
* Les *machines virtuelles Spot* sont soumises à un *quota de processeurs virtuels Spot*. 

Le quota de processeurs virtuels Standard pour le paiement à l’utilisation et les instances de machines virtuelles réservées est appliqué à deux niveaux pour chaque abonnement de chaque région :
* Le premier niveau correspond à la *limite totale des processeurs virtuels de toutes les régions* (comprenant toutes les séries de machines virtuelles).
* Le deuxième niveau correspond à la *limite des processeurs virtuels pour chaque série de machines virtuelles* (par exemple, les processeurs virtuels de série Dv3). 

Chaque fois qu’une nouvelle machine virtuelle Spot est déployée, l’utilisation totale des processeurs virtuels nouveaux et existants appartenant à cette série de machines virtuelles ne doit pas dépasser le quota de processeurs virtuels approuvé pour cette série. En outre, le nombre total de processeurs virtuels nouveaux et existants déployés sur toutes les séries de machines virtuelles ne doit pas dépasser le quota total des processeurs virtuels de toutes les régions, qui a été approuvé pour l’abonnement. Si l’un de ces quotas est dépassé, le déploiement de la machine virtuelle n’est pas autorisé.

Vous pouvez demander une augmentation de la limite de quota de processeurs virtuels pour une série de machines virtuelles à partir du portail Azure. L’augmentation du quota des séries de machines virtuelles augmente automatiquement la limite totale des processeurs virtuels régionaux de la même valeur. 

Pour plus d’informations sur les quotas de processeurs virtuels standard, consultez [Quotas de processeurs virtuels pour les machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) et [Limites des abonnements et services Azure](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests). 

Pour savoir comment augmenter le nombre maximal de processeurs virtuels par région pour le quota Standard, consultez [Quota standard : augmenter les limites par région](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests). 

Pour plus d’informations sur l’augmentation des limites de processeurs virtuels de machines virtuelles Spot, consultez [Quota Spot : augmenter les limites de toutes les séries de machines virtuelles](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

Il existe deux façons de demander une augmentation des limites de quota standard des processeurs virtuels pour chaque série de machines virtuelles, comme nous allons le voir dans les sections suivantes.

## <a name="request-a-standard-quota-increase-from-the-help--support-pane"></a>Demander une augmentation du quota Standard à partir du volet « Aide + support »

Pour demander une augmentation du quota des processeurs virtuels Standard pour chaque série de machines virtuelles à l’aide du volet **Aide + support**, effectuez les étapes suivantes : 

> [!NOTE]
> Vous pouvez également demander l’augmentation de la limite de quota pour plusieurs régions à l’aide d’un même cas de support. Pour plus d’informations, reportez-vous à l’étape 8.

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez **Aide + support**.

   ![Lien « Aide et support »](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. Dans le volet **Aide + Support**,sélectionnez **Nouvelle demande de support**. 

    ![Nouvelle demande de support](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. Dans la liste déroulante **Type de problème**, sélectionnez **Limites du service et des abonnements (quotas)** .

   ![Liste déroulante « Type de problème »](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. Dans la liste déroulante **Abonnement**, sélectionnez l’abonnement pour lequel vous souhaitez augmenter le quota.

   ![Liste déroulante « Abonnement »](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. Dans la liste déroulante **Type de quota**, sélectionnez **Augmentations des limites de calcul/machines virtuelles (cœurs/processeurs virtuels) dans l’abonnement**. 

   ![Liste déroulante « Type de quota »](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Sous l’onglet **Détails**, sous **Détails du problème**, sélectionnez **Fournir des détails** afin de fournir des informations supplémentaires visant à faciliter le traitement de votre demande.

   ![Lien « Fournir des détails »](./media/resource-manager-core-quotas-request/provide-details.png)

1. Dans le volet **Détails du quota** situé en haut à droite, effectuez les étapes suivantes :

   ![Volet « Détails du quota »](./media/resource-manager-core-quotas-request/1-7.png)

   a. Dans la liste déroulante **Modèle de déploiement**, sélectionnez le modèle qui convient.

   b. Dans la liste déroulante **Emplacements**, sélectionnez un emplacement. Pour l’emplacement sélectionné, sous **Types**, dans la zone **Sélectionner un type**, entrez **Standard**.

   ![Volet « Détails du quota » - Types de quotas](./media/resource-manager-core-quotas-request/1-8.png)

   Sous **Types**, vous pouvez demander des types de quota Standard et Spot à partir d’un même cas de support grâce à la sélection multiple.
   
   Pour plus d’informations sur l’augmentation des limites de quota Spot, consultez [Machines virtuelles Spot Azure pour les groupes de machines virtuelles identiques](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

   c. Sous la liste déroulante **Standard**, sélectionnez la série de références SKU pour laquelle vous souhaitez augmenter les quotas.

   ![Volet « Détails du quota » - Séries de références SKU](./media/resource-manager-core-quotas-request/1-9.png)

   d. Entrez la nouvelle limite de quota que vous souhaitez pour cet abonnement. Pour supprimer une référence SKU de la liste, décochez la case en regard de la référence SKU ou sélectionnez l’icône **Supprimer** (X). 

   ![Zone de texte « Nouvelle limite de processeurs virtuels »](./media/resource-manager-core-quotas-request/1-10.png)

1. Pour demander une augmentation de quota pour plusieurs emplacements, sélectionnez un emplacement supplémentaire dans la liste déroulante, puis sélectionnez le type de machine virtuelle qui convient. Vous pouvez ensuite entrer une limite qui s’applique à l’emplacement supplémentaire.

   ![Emplacements supplémentaires dans le volet « Détails du quota »](./media/resource-manager-core-quotas-request/1-11.png)
   
1. Sélectionnez **Enregistrer et continuer** pour poursuivre la création de la demande de support.

## <a name="request-a-standard-quota-increase-from-the-subscriptions-pane"></a>Demander une augmentation du quota Standard à partir du volet « Abonnements »

Pour demander une augmentation du quota des processeurs virtuels Standard pour chaque série de machines virtuelles à l’aide du volet **Abonnements**, effectuez les étapes suivantes :

> [!NOTE]
> Vous pouvez également demander l’augmentation de la limite de quota pour plusieurs régions à l’aide d’un même cas de support. Pour plus d’informations, reportez-vous à l’étape 7.

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez **Abonnements**.

   ![Lien « Abonnements »](./media/resource-manager-core-quotas-request/subscriptions.png)

1. Sélectionnez l’abonnement pour lequel vous souhaitez augmenter le quota.

   ![Volet « Abonnements »](./media/resource-manager-core-quotas-request/select-subscription.png)

1. Dans le volet gauche de la page **\<Nom de votre abonnement>** , sélectionnez **Utilisation + quotas**.

   ![Lien « Utilisation + quotas »](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. En haut à droite, sélectionnez **Demander une augmentation**.

   ![Demander une augmentation](./media/resource-manager-core-quotas-request/request-increase.png)

1. Dans la liste déroulante **Type de quota**, sélectionnez **Augmentations des limites de calcul/machines virtuelles (cœurs/processeurs virtuels) dans l’abonnement**.

   ![Liste déroulante « Type de quota »](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
1. Dans le volet **Détails du quota** situé en haut à droite, effectuez les étapes suivantes :

   ![Volet « Détails du quota »](./media/resource-manager-core-quotas-request/1-1-6.png)

   a. Dans la liste déroulante **Modèle de déploiement**, sélectionnez le modèle qui convient.

   b. Dans la liste déroulante **Emplacements**, sélectionnez un emplacement. 
   
   c. Pour l’emplacement sélectionné, sous **Types**, sélectionnez **Sélectionner un type**, puis cochez la case **Standard**.

   ![Case à cocher « Standard »](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   Sous **Types**, vous pouvez demander des types de quota Standard et Basse priorité dans un même cas de support via la sélection multiple.
   
   Pour plus d’informations sur l’augmentation des limites de quota Spot, consultez [Machines virtuelles Spot Azure pour les groupes de machines virtuelles identiques](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

   d. Sous la liste déroulante **Standard**, sélectionnez la série de références SKU pour laquelle vous souhaitez augmenter les quotas.

   ![Volet « Détails du quota » - Séries de références SKU](./media/resource-manager-core-quotas-request/1-1-8.png)

   e. Entrez la nouvelle limite de quota que vous souhaitez pour cet abonnement. Pour supprimer une référence SKU de la liste, décochez la case en regard de la référence SKU ou sélectionnez l’icône **Supprimer** (X). 

   ![Zone de texte « Nouvelle limite de processeurs virtuels »](./media/resource-manager-core-quotas-request/1-1-9.png)
   
1. Pour demander une augmentation de quota pour plusieurs emplacements, sélectionnez un emplacement supplémentaire dans la liste déroulante, puis sélectionnez le type de machine virtuelle qui convient. 

   Cette étape précharge la série de références SKU que vous avez sélectionnée pour les précédents emplacements. Entrez les limites de quota que vous souhaitez appliquer à cette série.
   
   ![Emplacements supplémentaires dans le volet « Détails du quota »](./media/resource-manager-core-quotas-request/1-1-10.png)
 
1. Sélectionnez **Enregistrer et continuer** pour poursuivre la création de la demande de support.
