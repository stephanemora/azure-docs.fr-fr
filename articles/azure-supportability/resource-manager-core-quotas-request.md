---
title: Demandes d’augmentation des quotas de processeurs virtuels pour Azure Resource Manager | Microsoft Docs
description: Demandes d’augmentation des quotas de processeurs virtuels pour Azure Resource Manager
author: ganganarayanan
ms.author: gangan
ms.date: 6/13/2018
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 204deaf3a67984c0dd5eca5352686719e7767885
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60649290"
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Demandes d’augmentation des quotas de processeurs virtuels pour Resource Manager

Les quotas de processeurs virtuels pour Resource Manager sont appliqués au niveau de la région et de la famille de références (SKU).
Pour en savoir plus sur la façon dont les quotas sont appliqués, consultez la page [Abonnement Azure et limites, quotas et contraintes de service](https://aka.ms/quotalimits).
Pour en savoir plus sur les familles de références, vous pouvez comparer le coût et les performances sur la page [Tarification des machines virtuelles](https://aka.ms/pricingcompute).

Pour demander une augmentation, suivez les instructions ci-dessous pour créer une demande de support via le panneau « Utilisation + quotas » d’Azure disponible dans le portail Azure. 

## <a name="request-quota-increase-at-subscription-level"></a>Demander une augmentation de quota au niveau de l’abonnement

1. Dans https://portal.azure.com, sélectionnez **Abonnements**.

   ![Abonnements](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Sélectionnez l’abonnement nécessitant une augmentation du quota.

   ![Sélectionnez un abonnement](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Sélectionnez **Utilisation + quotas**.

   ![Sélectionnez Utilisation + quotas](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. Dans l’angle supérieur droit, cliquez sur **Demander une augmentation**.

   ![Demander une augmentation](./media/resource-manager-core-quotas-request/request-increase.png)

5. Étape 1 : Sélectionnez **Cœurs** comme type de quota. 

   ![Renseignez le formulaire](./media/resource-manager-core-quotas-request/forms.png)
   
6. Étape 2 : Sélectionnez le modèle de déploiement « Resource Manager », puis sélectionnez un emplacement.

    ![Panneau Problème de quota](./media/resource-manager-core-quotas-request/Problem-step.png)

3. Sélectionnez les familles de références qui nécessitent une augmentation.

    ![Série de références sélectionnée](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. Entrez les nouvelles limites que vous souhaitez appliquer à l’abonnement.

    ![Nouvelle demande de quota de référence](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- Pour supprimer une ligne, désactivez la case de la référence dans la liste déroulante de la famille de références ou cliquez sur l’icône de fermeture « x ».
Après avoir entré le quota de votre choix pour chaque famille de références, cliquez sur « Suivant » sur la page de l’étape Problème pour poursuivre la création de demande de support.

