---
title: Quota Spot du nombre d’ordinateurs virtuels | Microsoft Docs
description: Augmenter les limites de quota en effectuant des demandes de quota Spot
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9567df95e4e66d9c6b82b29cb9bba9adaf39349d
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75898861"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Quota Spot : augmenter les limites de toutes les séries de machines virtuelles

Les machines virtuelles Spot fournissent un modèle d’utilisation Azure différent. Elles vous permettent de réduire les coûts si vous autorisez Azure à supprimer des machines virtuelles dans le cadre d’un paiement à l’utilisation ou du déploiement d’une instance de machine virtuelle réservée. Pour plus d’informations sur les machines virtuelles Spot, consultez [Machines virtuelles Spot Azure pour les groupes de machines virtuelles identiques](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

Azure Resource Manager prend en charge deux types de quotas de processeurs virtuels pour les machines virtuelles :
* Les *machines virtuelles avec paiement à l’utilisation* et les *instances de machine virtuelle réservées* sont soumises à un *quota de processeurs virtuels standard*.
* Les *machines virtuelles Spot* sont soumises à un *quota de processeurs virtuels Spot*. 

Pour le type *Quota de processeurs virtuels Spot*, les quotas de processeur virtuel Resource Manager sont appliqués à toutes les séries de machines virtuelles disponibles en tant que limite régionale unique.

Chaque fois qu’une nouvelle machine virtuelle Spot est déployée, l’utilisation totale des processeurs virtuels nouveaux et existants de toutes les instances de machine virtuelle Spot ne doit pas dépasser la limite du quota de processeurs virtuels Spot approuvée. Si le quota Spot est dépassé, le déploiement de machine virtuelle Spot n’est pas autorisé. 

Cet article explique comment faire une demande d’augmentation concernant le quota de processeurs virtuels Spot dans le portail Azure. 

Pour plus d’informations sur les quotas de processeurs virtuels standard, consultez [Quotas de processeurs virtuels pour les machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) et [Limites des abonnements et services Azure](https://aka.ms/quotalimits). 

Pour savoir comment augmenter le nombre maximal de processeurs virtuels par région, consultez [Quota standard : augmentation de la limite régionale des processeurs virtuels](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests).

## <a name="request-a-quota-limit-increase-from-the-help--support-pane"></a>Demander une augmentation de la limite de quota à partir du volet « Aide + support » 

Pour demander une augmentation de la limite de quota Spot pour toutes les séries de machines virtuelles à l’aide du volet **Aide + support**, effectuez les étapes suivantes :

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

1. Sélectionnez l’onglet **Détails**, puis, sous **Détails du problème**, sélectionnez **Fournir des détails** afin de fournir des informations supplémentaires visant à faciliter le traitement de votre demande.

   ![Lien « Fournir des détails »](./media/resource-manager-core-quotas-request/provide-details.png)
   
1. Dans le volet **Détails du quota** situé en haut à droite, effectuez les étapes suivantes :

   ![Volet « Détails du quota »](./media/resource-manager-core-quotas-request/3-7.png)

   a. Dans la liste déroulante **Modèle de déploiement**, sélectionnez le modèle qui convient.

   b. Dans la liste déroulante **Emplacements**, sélectionnez un emplacement. Pour l’emplacement sélectionné, sous **Types**, dans la zone **Sélectionner un type**, entrez **Spot**. 
   
   ![Onglet Détails - Nouvelle demande de support](./media/resource-manager-core-quotas-request/3-8.png)

    Sous **Types**, vous pouvez demander des types de quota Standard et Spot à partir d’un même cas de support grâce à la sélection multiple. 
    
    Pour plus d’informations, consultez [Quota standard : augmentation de la limite de processeurs virtuels de séries de machines virtuelles](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

   c. Entrez la nouvelle limite de quota que vous souhaitez pour cet abonnement. 
 
   ![Zone de texte « Nouvelle limite de processeurs virtuels »](./media/resource-manager-core-quotas-request/3-9.png)

1. Pour demander une augmentation de quota pour plusieurs emplacements, sélectionnez un emplacement supplémentaire dans la liste déroulante, puis sélectionnez le type de machine virtuelle qui convient. Vous pouvez ensuite entrer une limite qui s’applique à l’emplacement supplémentaire.

   ![Emplacements supplémentaires dans le volet « Détails du quota »](./media/resource-manager-core-quotas-request/3-10.png)

1. Sélectionnez **Enregistrer et continuer** pour poursuivre la création de la demande de support.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Demander une augmentation de la limite de quota à partir du volet « Abonnements »

Pour demander une augmentation de la limite de quota Spot pour toutes les séries de machines virtuelles à l’aide du volet **Abonnements**, effectuez les étapes suivantes :

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

   ![Volet « Détails du quota »](./media/resource-manager-core-quotas-request/3-2-6.png)
 
   a. Dans la liste déroulante **Modèle de déploiement**, sélectionnez le modèle qui convient.

   b. Dans la liste déroulante **Emplacements**, sélectionnez un emplacement. 
   
   c. Pour l’emplacement sélectionné, sous **Types**, dans la zone **Sélectionner un type**, entrez **Spot**.

   ![Volet « Détails du quota »](./media/resource-manager-core-quotas-request/3-2-7.png)

   Pour plus d’informations, consultez [Quota standard : augmentation de la limite de processeurs virtuels de séries de machines virtuelles](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

   d. Entrez la nouvelle limite de quota que vous souhaitez pour cet abonnement.

   ![Zone de texte « Nouvelle limite de processeurs virtuels »](./media/resource-manager-core-quotas-request/3-2-8.png)
 
1. Pour demander une augmentation de quota pour plusieurs emplacements, sélectionnez un emplacement supplémentaire dans la liste déroulante, puis sélectionnez le type de machine virtuelle qui convient. Vous pouvez ensuite entrer une limite qui s’applique à l’emplacement supplémentaire.

   ![Emplacements supplémentaires dans le volet « Détails du quota »](./media/resource-manager-core-quotas-request/3-2-9.png)
 
1. Sélectionnez **Enregistrer et continuer** pour poursuivre la création de la demande de support.


