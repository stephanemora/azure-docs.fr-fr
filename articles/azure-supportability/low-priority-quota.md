---
title: Quota Spot | Microsoft Docs
description: Demandes de quota Spot
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 09c9d7940314b691e6351353e6a0076510fdcb13
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850565"
---
# <a name="spot-quota-limit-increase-for-all-vm-series"></a>Quota Spot : augmentation de la limite pour toutes les séries de machines virtuelles

Les machines virtuelles Spot fournissent un modèle d’utilisation d’Azure différent qui, en échange d’un coût réduit, permet à Azure de supprimer une machine virtuelle en fonction des besoins pour les déploiements d’instances de machines virtuelles avec paiement à l’utilisation ou réservées. Plus d’informations sur les machines virtuelles Spot [ici](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

Resource Manager prend en charge deux types de quotas de processeurs virtuels pour les machines virtuelles. **Les machines virtuelles avec paiement à l’utilisation et les instances de machine virtuelle réservées** utilisent un quota Standard. Les **machines virtuelles Spot** utilisent le quota Spot. 

Pour le type **Quota Spot**, les quotas de processeurs virtuels Resource Manager sont appliqués à toutes les séries de machines virtuelles disponibles en tant que limite régionale unique.

À chaque fois qu’une nouvelle machine virtuelle Spot doit être déployée, la somme de l’utilisation des processeurs virtuels nouveaux et existants pour toutes les instances de machine virtuelle Spot ne doit pas dépasser la limite de quota de processeurs virtuels Spot approuvée. Si le quota Spot est dépassé, le déploiement de machine virtuelle Spot n’est pas autorisé. Vous pouvez demander une augmentation de la limite de quota de processeurs virtuels Spot à partir du Portail Azure. 

Apprenez-en davantage sur les quotas Standard des processeurs virtuels dans la page Quotas de processeurs virtuels pour les machines virtuelles et la page Limites du service et de l’abonnement Azure. Apprenez-en davantage sur l’augmentation de la limite de processeurs virtuels régionale pour le quota Standard dans cette [page](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests).

Vous pouvez désormais demander une augmentation des **limites de quota Spot pour toutes les séries de machines virtuelles** via le panneau **Aide + support** ou le panneau **Utilisations + quota** dans le portail.

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-the-help--support-blade"></a>Demander une augmentation de la limite de quota Spot pour toutes les séries de machines virtuelles par abonnement à l’aide du panneau Aide + support

Suivez les instructions ci-dessous pour créer une demande de support via le panneau « Aide + Support » d’Azure disponible dans le portail Azure.

Vous pouvez également **demander un quota pour plusieurs régions** à l’aide d’un seul incident de support. Reportez-vous à l’étape 10 ci-dessous pour plus de détails. 


1. À partir de https://portal.azure.com, sélectionnez **Aide + Support**.

   ![Aide + support](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Sélectionnez **Nouvelle demande de support**. 

     ![Nouvelle demande de support](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Dans la liste déroulante Type de problème, choisissez **Limites du service et des abonnements (quotas)** .

   ![Liste déroulante Type de problème](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Sélectionnez l’abonnement nécessitant une augmentation du quota.

   ![Sélectionner l’abonnement newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Sélectionnez **Augmentations des limites de calcul/machines virtuelles (cœurs/processeurs virtuels) dans l’abonnement** dans la liste déroulante **Type de quota**. 

   ![Sélectionner un type de quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. Dans **Détails du problème**, fournissez des informations supplémentaires pour faciliter le traitement de votre demande en cliquant sur **Fournir des informations**.

   ![Fournir des informations](./media/resource-manager-core-quotas-request/provide-details.png)
   
7.  Dans le panneau **Détails du quota***, sélectionnez **Modèle de déploiement**, puis sélectionnez un **emplacement**.

![Fournir des informations](./media/resource-manager-core-quotas-request/3-7.png)

8. Pour l’emplacement sélectionné, définissez la valeur **Type** sur **Spot**. Vous pouvez demander des types de quota Standard et Spot à partir d’un seul incident de support via la prise en charge de la sélection multiple sur le champ **Type**. Découvrez-en plus sur l’**augmentation du quota Standard par série de machines virtuelles** dans cette [page](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

![Fournir des informations](./media/resource-manager-core-quotas-request/3-8.png)

9.  Entrez la nouvelle limite que vous souhaitez appliquer à l’abonnement. 
 
 ![Fournir des informations](./media/resource-manager-core-quotas-request/3-9.png)

10. Pour demander un quota pour plusieurs emplacements, vous pouvez rechercher un autre emplacement dans la liste déroulante et sélectionner le type de machine virtuelle approprié. Vous pouvez ensuite entrer les nouvelles limites que vous souhaitez.

![Fournir des informations](./media/resource-manager-core-quotas-request/3-10.png)

11. Après avoir entré le quota de votre choix, cliquez sur **Enregistrer et continuer** dans le panneau Détails du quota pour poursuivre la création de demande de support.

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-usages--quota-blade"></a>Demander une augmentation de la limite de quota Spot pour toutes les séries de machines virtuelles par abonnement à l’aide du panneau Utilisations + quota

Suivez les instructions ci-dessous pour créer une demande de support via le panneau « Utilisation + Quotas » d’Azure disponible dans le portail Azure.

Vous pouvez également **demander un quota pour plusieurs régions** à l’aide d’un seul incident de support. Reportez-vous à l’étape 9 ci-dessous pour plus de détails. 

1.  Dans https://portal.azure.com, sélectionnez **Abonnements**.

 ![Abonnements](./media/resource-manager-core-quotas-request/subscriptions.png)

2.  Sélectionnez l’abonnement nécessitant une augmentation du quota.

 ![Sélectionnez un abonnement](./media/resource-manager-core-quotas-request/select-subscription.png)

3.  Sélectionnez **Utilisation + quotas**.

 ![Sélectionnez Utilisation + quotas](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4.  Dans le coin supérieur droit, sélectionner **Demander une augmentation**.

   ![Demander une augmentation](./media/resource-manager-core-quotas-request/request-increase.png)

5.  Sélectionnez **La limite d’abonnement de calcul/machine virtuelle (cœurs/processeurs virtuels) augmente** comme type de quota.

  ![Renseignez le formulaire](./media/resource-manager-core-quotas-request/select-quota-type.png)

6.  Dans le panneau **Détails du quota**, sélectionnez Modèle de déploiement, puis sélectionnez un emplacement.

  ![Renseignez le formulaire](./media/resource-manager-core-quotas-request/3-2-6.png)
 
7.  Pour l’emplacement sélectionné, définissez la valeur **Type** sur **Spot**. Vous pouvez demander des types de quota Standard et Spot à partir d’un seul incident de support via la prise en charge de la sélection multiple sur le champ **Type**. Découvrez-en plus sur l’**augmentation du quota standard par série de machines virtuelles** dans cette [page](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

  ![Renseignez le formulaire](./media/resource-manager-core-quotas-request/3-2-7.png)
 
8.  Entrez la nouvelle limite que vous souhaitez appliquer à l’abonnement.

  ![Renseignez le formulaire](./media/resource-manager-core-quotas-request/3-2-8.png)
 
9.  Pour demander un quota pour plusieurs emplacements, vous pouvez rechercher un autre **emplacement** dans la liste déroulante et sélectionner le type de machine virtuelle approprié. Vous pouvez ensuite entrer les nouvelles limites que vous souhaitez.

  ![Renseignez le formulaire](./media/resource-manager-core-quotas-request/3-2-9.png)
 
10. Après avoir entré le quota de votre choix, cliquez sur **Enregistrer et continuer** dans le panneau Détails du quota pour poursuivre la création de demande de support.


