---
title: Demandes d’augmentation des quotas de processeurs virtuels pour Azure Resource Manager | Microsoft Docs
description: Demandes d’augmentation des quotas de processeurs virtuels pour Azure Resource Manager
author: sowmyavenkat86
ms.author: svenkat
ms.date: 05/09/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9d19d1a993d574777aa630b9c58f2472b94716cd
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479434"
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Demandes d’augmentation des quotas de processeurs virtuels pour Resource Manager

Quotas de processeurs virtuels Resource Manager pour les machines virtuelles et des machines virtuelles identiques sont appliqués à deux niveaux pour chaque abonnement, dans chaque région. 

Le premier niveau est la limite totale régionale de processeurs virtuels (parmi toutes les séries de machines virtuelles), et le deuxième niveau est la limite de processeurs virtuels de séries de machines virtuelles (par exemple, les processeurs virtuels de la série D). Chaque fois qu’une nouvelle machine virtuelle est déployée, la somme de l’utilisation des processeurs virtuels nouveaux et existants pour cette série de machine virtuelle ne doit pas dépasser le quota de processeurs virtuels approuvé pour ce particulier autre série de machine virtuelle, le nombre total de processeurs virtuels déployé sur toutes les séries de machine virtuelle ne doit pas dépasser le quota de processeurs virtuels régionaux Total  approuvé pour l’abonnement. Si l’un de ces quotas est dépassé, le déploiement des machines virtuelles n’est pas autorisé.
Vous pouvez demander une augmentation de la limite de quota de processeurs virtuels pour la série de machine virtuelle à partir du portail Azure. Une augmentation du quota de séries de machines virtuelles augmente automatiquement la limite totale régionale de processeurs virtuels du même montant. 

Quand un nouvel abonnement est créé, la valeur par défaut régional Total processeurs virtuels ne peut pas être égal à la somme des quotas de processeurs virtuels par défaut pour tous les individuels machine virtuelle série celui-ci peut entraîner un abonnement avec un quota suffisant pour chaque série de machine virtuelle individuelle que vous souhaitez déployer , mais l’abonnement n’a pas un quota suffisant pour les processeurs virtuels de Total régional pour tous les déploiements. Dans ce cas, vous devez soumettre une demande d’augmentation de l’augmentation du quota Total pour la limite de région explicitement. Limite du nombre total de processeurs virtuels régionaux ne peut pas dépasser la somme de quota approuvée entre toutes les séries de machines virtuelles pour la région.

En savoir plus sur les quotas sur le [page des quotas de processeurs virtuels Virtual machine](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) et [abonnement Azure et limites de service](https://aka.ms/quotalimits) page. 

Vous pouvez désormais demander une augmentation via **aide + Support** panneau ou le **utilisation + Quota** panneau dans le portail. 

## <a name="request-quota-increase-using-the-help--support-blade"></a>Augmentation de quota de demande à l’aide du **aide + Support** panneau

Suivez les instructions ci-dessous pour créer une demande de prise en charge par le biais du « Aide + Support » du panneau Azure disponible dans le portail Azure. 

1. À partir de https://portal.azure.com, sélectionnez **aide + Support**.

![Aide + Support](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Sélectionnez **Nouvelle demande de support**. 

![Nouvelle demande de support](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Dans la liste déroulante type de problème, choisissez **limites de Service et d’abonnement (quotas)** .

![Liste déroulante type de problème](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Sélectionnez l’abonnement nécessitant une augmentation du quota.

![Sélectionner un abonnement newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Sélectionnez **de calcul augmente la limite d’abonnement - VM (cœurs-processeurs virtuels)** dans **type de quota** liste déroulante. 

![Sélectionnez le type de quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. Dans **détails du problème**, fournir des informations supplémentaires pour faciliter le processus de votre demande en cliquant sur **fournissent des détails**.

![Fournir des détails](./media/resource-manager-core-quotas-request/provide-details.png)

7. Dans le **détails du Quota** panneau, sélectionnez le modèle de déploiement et sélectionnez un emplacement.

![Détails des quotas DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. Sélectionnez le **familles de références SKU** qui nécessitent une augmentation. 

![Famille de références](./media/resource-manager-core-quotas-request/sku-family.png)

9. Entrez les nouvelles limites que vous souhaitez appliquer à l’abonnement. Pour supprimer une ligne, désactivez la case de la référence dans la liste déroulante de la famille de références ou cliquez sur l’icône de fermeture « x ». Après avoir entré le quota souhaité pour chaque famille de référence (SKU), cliquez sur **enregistrer et continuer** dans le volet de détails de Quota pour poursuivre la création de demande de prise en charge.

![Nouvelles limites](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-quota-increase-at-subscription-level-using-usages--quota"></a>Demander une augmentation de quota au niveau de l’abonnement à l’aide des utilisations + Quota

Suivez les instructions ci-dessous à l’aide pour créer une demande de prise en charge par le biais d’Azure « utilisation + quota » panneau disponible dans le portail Azure. 

1. Dans https://portal.azure.com, sélectionnez **Abonnements**.

![Abonnements](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Sélectionnez l’abonnement nécessitant une augmentation du quota.

![Sélectionnez un abonnement](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Sélectionnez **Utilisation + quotas**.

![Sélectionnez Utilisation + quotas](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. Dans l’angle supérieur droit, cliquez sur **Demander une augmentation**.

![Demander une augmentation](./media/resource-manager-core-quotas-request/request-increase.png)

5. Sélectionnez **augmente la limite d’abonnement (cœurs-processeurs virtuels) de calcul-machine virtuelle** comme type de guillemet. 

![Renseignez le formulaire](./media/resource-manager-core-quotas-request/forms.png)
   
6. Dans le **détails du Quota** panneau, sélectionnez le modèle de déploiement et sélectionnez un emplacement.

![Panneau Problème de quota](./media/resource-manager-core-quotas-request/problemstep.png)

7. Sélectionnez le **familles de références SKU** qui nécessitent une augmentation.

![Série de références sélectionnée](./media/resource-manager-core-quotas-request/sku-family.png)

8. Entrez les nouvelles limites que vous souhaitez appliquer à l’abonnement. Pour supprimer une ligne, désactivez la case de la référence dans la liste déroulante de la famille de références ou cliquez sur l’icône de fermeture « x ». Après avoir entré le quota souhaité pour chaque famille de référence (SKU), cliquez sur **enregistrer et continuer** sur la page de l’étape problème pour poursuivre la création de demande de prise en charge.

![Nouvelle demande de quota de référence](./media/resource-manager-core-quotas-request/new-limits.png)


