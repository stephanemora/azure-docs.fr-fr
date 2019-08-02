---
title: Demandes d’augmentation des quotas de processeurs virtuels par machine virtuelle pour Azure | Microsoft Docs
description: demandes d’augmentation des quotas de processeurs virtuels par machine virtuelle
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9caf82d2fa4683e845d23b5496f9e7b8f9682ec7
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234811"
---
# <a name="vm-series-vcpu-limit-increase"></a>Augmentation de la limite des processeurs virtuels de séries de machines virtuelles

Les quotas de processeurs virtuels de Resource Manager pour les machines virtuelles et les groupes de machines virtuelles identiques sont appliqués sur deux niveaux pour chaque abonnement, dans chaque région. 

Le premier niveau est la **limite de total de processeurs virtuels régionaux** (sur toutes les séries de machines virtuelles), et le deuxième niveau est la **limite de processeurs virtuels par série de machines virtuelles** (par exemple, les processeurs virtuels de la série D). Chaque fois qu’une nouvelle machine virtuelle est déployée, la somme des processeurs virtuels nouveaux et existants pour cette série de machines virtuelles ne doit pas dépasser le quota de processeurs virtuels approuvé pour cette série de machines virtuelles particulière. En outre, le nombre total de processeurs virtuels nouveaux et existants déployés sur toutes les séries de machines virtuelles ne doit pas dépasser le quota de processeurs virtuels régionaux total approuvé pour l’abonnement. Si l’un de ces quotas est dépassé, le déploiement des machines virtuelles n’est pas autorisé.
Vous pouvez demander une augmentation de la limite de quota de processeurs virtuels pour une série de machines virtuelles à partir du portail Azure. Une augmentation du quota de série de machines virtuelles augmente automatiquement la limite totale de processeurs virtuels régionaux du même montant. 

Apprenez-en davantage sur les quotas sur la [page Quotas de processeurs virtuels pour les machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) et la page [Limites du service et de l’abonnement Azure](https://aka.ms/quotalimits). 

Vous pouvez désormais demander une augmentation via le panneau **Aide + Support** ou **Utilisation + quotas** dans le portail. 

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Demandez une augmentation du quota total de processeurs virtuels par série de machines virtuelles au niveau de l’abonnement à l’aide du panneau **Aide + Support**

Suivez les instructions ci-dessous pour créer une demande de support via le panneau « Aide + Support » d’Azure qui est accessible à partir du portail Azure. 

1. À partir de https://portal.azure.com, sélectionnez **Aide + Support**.

   ![Aide + support](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Sélectionnez **Nouvelle demande de support**. 

     ![Nouvelle demande de support](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Dans la liste déroulante Type de problème, choisissez **Limites du service et des abonnements (quotas)** .

   ![Liste déroulante Type de problème](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Sélectionnez l’abonnement nécessitant une augmentation du quota.

   ![Sélectionner l’abonnement newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Sélectionnez **La limite d’abonnement de calcul/machine virtuelle (cœurs/processeurs virtuels) augmente** dans la liste déroulante **Type de quota**. 

   ![Sélectionner un type de quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. Dans **Détails du problème**, fournissez des informations supplémentaires pour faciliter le traitement de votre demande en cliquant sur **Fournir des informations**.

   ![Fournir des informations](./media/resource-manager-core-quotas-request/provide-details.png)

7. Dans le panneau **Détails du quota**, sélectionnez Modèle de déploiement, puis sélectionnez un emplacement.

   ![Détails des quotas : modèle de déploiement](./media/resource-manager-core-quotas-request/quota-details.png)

8. Sélectionnez les **familles de références** qui nécessitent une augmentation. 

   ![Famille de références](./media/resource-manager-core-quotas-request/sku-family.png)

9. Entrez les nouvelles limites que vous souhaitez appliquer à l’abonnement. Pour supprimer une ligne, désactivez la case de la référence dans la liste déroulante de la famille de références ou cliquez sur l’icône de fermeture « x ». Après avoir entré le quota de votre choix pour chaque famille de références, cliquez sur **Enregistrer et continuer** dans le panneau Détails du quota pour poursuivre la création de demande de support.

   ![Nouvelles limites](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Demandez une augmentation du quota total de processeurs virtuels par série de machines virtuelles au niveau de l’abonnement à l’aide du panneau **Utilisation + quotas**

Suivez les instructions ci-dessous pour créer une demande de support via le panneau « Utilisation + quotas » d’Azure qui est accessible à partir du portail Azure. 

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

   ![Panneau Problème de quota](./media/resource-manager-core-quotas-request/quota-details.png)

7. Sélectionnez les **familles de références** qui nécessitent une augmentation.

   ![Série de références sélectionnée](./media/resource-manager-core-quotas-request/sku-family.png)

8. Entrez les nouvelles limites que vous souhaitez appliquer à l’abonnement. Pour supprimer une ligne, désactivez la case de la référence dans la liste déroulante de la famille de références ou cliquez sur l’icône de fermeture « x ». Après avoir entré le quota de votre choix pour chaque famille de références, cliquez sur **Enregistrer et continuer** sur la page de l’étape Problème pour poursuivre la création de demande de support.

   ![Nouvelle demande de quota de référence](./media/resource-manager-core-quotas-request/new-limits.png)
 
