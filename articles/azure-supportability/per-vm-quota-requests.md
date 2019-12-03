---
title: Demandes d’augmentation des quotas de processeurs virtuels par machine virtuelle pour Azure | Microsoft Docs
description: demandes d’augmentation des quotas de processeurs virtuels par machine virtuelle
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: ccd0c88c95ae9a752ef8ea2387bbde4f8559bc68
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74531667"
---
# <a name="standard-quota-per-vm-series-vcpu-limit-increase"></a>Quota Standard : augmentation de la limite de processeurs virtuels de séries de machines virtuelles

Resource Manager prend en charge deux types de quotas de processeurs virtuels pour les machines virtuelles. **Les machines virtuelles avec paiement à l’utilisation et les instances de machine virtuelle réservées** utilisent un quota Standard. **Les machines virtuelles Basse priorité** utilisent un quota Basse priorité. Le quota de processeurs virtuels Standard pour le paiement à l’utilisation et les instances de machine virtuelle réservées est appliqué à deux niveaux pour chaque abonnement de chaque région.

Le premier niveau est la limite **Total des processeurs virtuels régionaux** (sur toutes les séries de machines virtuelles), et le deuxième niveau est la limite **Processeurs virtuels par série de machines virtuelles** (par exemple, les processeurs virtuels de série Dv3). Chaque fois qu’une nouvelle machine virtuelle est déployée, la somme des processeurs virtuels nouveaux et existants pour cette série de machines virtuelles ne doit pas dépasser le quota de processeurs virtuels approuvé pour cette série de machines virtuelles particulière. En outre, le nombre total de processeurs virtuels nouveaux et existants déployés sur toutes les séries de machines virtuelles ne doit pas dépasser le quota de processeurs virtuels régionaux total approuvé pour l’abonnement. Si l’un de ces quotas est dépassé, le déploiement des machines virtuelles n’est pas autorisé.
Vous pouvez demander une augmentation de la limite de quota de processeurs virtuels pour une série de machines virtuelles à partir du portail Azure. Une augmentation du quota de série de machines virtuelles augmente automatiquement la limite totale de processeurs virtuels régionaux du même montant. 

Apprenez-en davantage sur les quotas Standard des processeurs virtuels sur la page [Quotas de processeurs virtuels pour les machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) et la page [Limites du service et de l’abonnement Azure](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests). 

Apprenez-en davantage sur l’augmentation de la limite de processeurs virtuels régionale pour le quota Standard [ici](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests). 

Apprenez-en davantage sur l’**augmentation des limites de processeurs virtuels de machine virtuelle Basse priorité** [ici](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

Vous pouvez demander une augmentation des **limites de quota de processeurs virtuels Standard par série de machines virtuelles** via le panneau **Aide + support** ou le panneau **Utilisations + quota** dans le portail.

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-the-help--support-blade"></a>Demandez une augmentation du quota Standard de processeurs virtuels par série de machines virtuelles au niveau de l’abonnement à l’aide du panneau Aide + support

Suivez les instructions ci-dessous pour créer une demande de support via le panneau « Aide + Support » d’Azure disponible dans le portail Azure. 

Vous pouvez également demander un quota pour plusieurs régions à l’aide d’un seul incident de support. Reportez-vous à l’étape 11 ci-dessous pour plus de détails.

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

7. Dans le panneau **Détails du quota**, sélectionnez **Modèle de déploiement**, puis sélectionnez un **emplacement**.

   ![Détails des quotas : modèle de déploiement](./media/resource-manager-core-quotas-request/1-7.png)

8. Pour l’emplacement sélectionné, définissez la valeur **Type** sur **Standard**. Vous pouvez demander des types de quota Standard et Basse priorité à partir d’un seul incident de support via la prise en charge de la sélection multiple sur le champ **Type**. Apprenez-en davantage sur l’**augmentation des limites de quota Basse priorité** sur la page **<>** .

   ![Famille de références](./media/resource-manager-core-quotas-request/1-8.png)

9. Sélectionnez les **familles de références SKU** qui nécessitent une augmentation.

   ![Famille de références](./media/resource-manager-core-quotas-request/1-9.png)

10. Entrez les nouvelles limites que vous souhaitez appliquer à l’abonnement. Pour supprimer une ligne, désactivez la case de la référence SKU dans la liste déroulante de la famille de références SKU ou cliquez sur l’icône de fermeture « x ». 

   ![Nouvelles limites](./media/resource-manager-core-quotas-request/1-10.png)

11. Pour demander un quota pour plusieurs emplacements, vous pouvez rechercher un autre **emplacement** dans la liste déroulante et sélectionner le type de machine virtuelle approprié. Cette étape précharge les familles de références SKU sélectionnées pour les **emplacement** précédents par rapport au nouvel emplacement. Vous pouvez simplement entrer les nouvelles limites que vous souhaitez.

   ![Emplacements multiples](./media/resource-manager-core-quotas-request/1-11.png)
   
12. Après avoir entré le quota de votre choix pour chaque famille de références, cliquez sur **Enregistrer et continuer** dans le panneau Détails du quota pour poursuivre la création de demande de support.

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-usages--quota-blade"></a>Demandez une augmentation du quota Standard de processeurs virtuels par série de machines virtuelles au niveau de l’abonnement à l’aide du panneau Utilisations + quotas

Suivez les instructions ci-dessous pour créer une demande de support via le panneau « Utilisation + Quotas » d’Azure disponible dans le portail Azure.

Vous pouvez également **demander un quota pour plusieurs régions** à l’aide d’un seul incident de support. Reportez-vous à l’étape 10 ci-dessous pour plus de détails.

1. Dans https://portal.azure.com, sélectionnez **Abonnements**.

   ![Abonnements](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Sélectionnez l’abonnement nécessitant une augmentation du quota.

   ![Sélectionnez un abonnement](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Sélectionnez **Utilisation + quotas**.

   ![Sélectionnez Utilisation + quotas](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. Dans l’angle supérieur droit, cliquez sur **Demander une augmentation**.

   ![Demander une augmentation](./media/resource-manager-core-quotas-request/request-increase.png)

5. Sélectionnez **La limite d’abonnement de calcul/machine virtuelle (cœurs/processeurs virtuels) augmente** comme type de quota. 

   ![Renseignez le formulaire](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. Dans le panneau **Détails du quota**, sélectionnez Modèle de déploiement, puis sélectionnez un emplacement.

   ![Panneau Problème de quota](./media/resource-manager-core-quotas-request/1-1-6.png)

7. Pour l’emplacement sélectionné, définissez la valeur **Type** sur **Standard**. Vous pouvez demander des types de quota Standard et Basse priorité à partir d’un seul incident de support via la prise en charge de la sélection multiple sur le champ **Type**. Apprenez-en davantage sur l’**augmentation des limites de processeurs virtuels Basse priorité** sur cette [page](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

   ![Série de références sélectionnée](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   
8. Sélectionnez les **familles de références SKU** qui nécessitent une augmentation.

   ![Série de références sélectionnée](./media/resource-manager-core-quotas-request/1-1-8.png)

9. Entrez les nouvelles limites que vous souhaitez appliquer à l’abonnement. Pour supprimer une ligne, désactivez la case de la référence SKU dans la liste déroulante de la famille de références SKU ou cliquez sur l’icône de fermeture « x ». 

   ![Nouvelle demande de quota de référence](./media/resource-manager-core-quotas-request/1-1-9.png)
   

10. Pour demander un quota pour plusieurs emplacements, vous pouvez rechercher un autre **emplacement** dans la liste déroulante et sélectionner le type de machine virtuelle approprié. Cette étape précharge les familles de références SKU sélectionnées pour les **emplacement** précédents par rapport au nouvel emplacement. Vous pouvez simplement entrer les nouvelles limites que vous souhaitez.
   
    ![Nouvelle demande de quota de référence](./media/resource-manager-core-quotas-request/1-1-10.png)
 
