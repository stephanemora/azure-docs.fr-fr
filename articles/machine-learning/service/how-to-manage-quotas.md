---
title: Guide pratique pour gérer et demander des quotas pour le service Azure Machine Learning
description: Ce guide de procédures décrit les différents quotas sur les ressources pour Azure Machine Learning, comment les afficher et comment demander un quota plus élevé.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 9/24/2018
ms.openlocfilehash: 0ee0a29be6a313e09cd77051dc29bc0131b57a40
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997809"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Gérer et demander des quotas pour les ressources Azure

À l’instar d’autres services Azure, certaines ressources associées au service Azure Machine Learning présentent des limites. Ces limites vont du plafond appliqué au nombre d’espaces de travail que vous pouvez créer aux limites sur le calcul sous-jacent réel utilisé pour l’entraînement ou l’inférence de vos modèles. Cet article fournit d’autres détails sur les limites préconfigurées de différentes ressources Azure pour votre abonnement et contient également des liens pratiques afin de demander des améliorations de quotas pour chaque type de ressource.

Gardez ces quotas à l’esprit quand vous concevez et que vous augmentez vos ressources Azure ML. Par exemple, si votre cluster n’atteint pas le nombre cible de nœuds que vous avez spécifié, vous avez peut-être atteint la limite de cœurs Batch AI pour votre abonnement. Si vous envisagez d’exécuter des charges de travail de production dans Batch AI, vous devrez peut-être affecter à un ou plusieurs des quotas une valeur supérieure à la valeur par défaut. Si vous souhaitez élever la limite ou le quota au-dessus de la limite par défaut, ouvrez gratuitement une demande de service clientèle en ligne. Les limites ne peuvent pas être supérieures à la valeur Limite maximale indiquée dans les tableaux suivants. Si aucune colonne Limite maximale n’est présente, la ressource spécifiée ne possède pas de limites ajustables. 

## <a name="special-considerations"></a>Considérations spéciales

+ Un quota est une limite de crédit, pas une garantie de capacité. Si vous avez des besoins de capacité à grande échelle, contactez le support Azure.

+ Votre quota est partagé entre tous les services, à l’exception de Batch AI, dans votre abonnement, Azure Machine Learning étant l’un d’eux. Veillez à calculer le rapport d’utilisation des quotas entre tous les services lors de l’évaluation de vos besoins de capacité.

+ Les limites par défaut varient selon le type de catégorie d’offre, comme Essai gratuit ou Paiement à l’utilisation, et selon la gamme (Dv2, F, G, etc.).

## <a name="default-resource-quotas"></a>Quotas de ressources par défaut

Voici la répartition des limites de quota selon les différents types de ressources au sein de votre abonnement Azure. 

> [!Important]
> Les limites sont susceptibles d’être modifiées. La dernière version se trouve toujours dans le [document](https://docs.microsoft.com/azure/azure-subscription-service-limits/) sur les quotas au niveau du service pour l’ensemble d’Azure.

### <a name="virtual-machines"></a>Machines virtuelles 
Il existe une limite au nombre de machines virtuelles que vous pouvez provisionner sur un abonnement Azure entre vos services ou de manière autonome. Cette limite se situe au niveau régional à la fois sur le nombre total de cœurs et en fonction de la famille.

Il est important de noter qu’une limite totale régionale et une limite régionale par gamme de taille (Dv2, F, etc.) s’appliquent séparément aux cœurs de machine virtuelle. Par exemple, considérons un abonnement dont le nombre total limite de cœurs de machine virtuelle est de 30 pour la région USA Est, de 30 pour la gamme A et de 30 pour la gamme D. Cet abonnement serait autorisé à déployer 30 machines virtuelles A1, ou 30 machines virtuelles D1, ou encore une combinaison de ces deux types de machines dans la limite de 30 cœurs au total (par exemple, 10 machines virtuelles A1 et 20 machines virtuelles D1).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

Pour obtenir une liste plus détaillée et à jour des limites de quota, consultez l’article sur les quotas à l’échelle d’Azure [ici](https://docs.microsoft.com/azure/azure-subscription-service-limits#subscription-limits-1).

### <a name="batch-ai-clusters"></a>Clusters Batch AI
Dans Batch AI, il existe une limite de quota par défaut sur le nombre de cœurs et le nombre de clusters autorisés par région dans un abonnement. Le quota Batch AI est distinct du quota de cœurs de machine virtuelle ci-dessus et les limites des cœurs ne sont pas partagées pour l’instant entre les deux types de ressources.

Ressources disponibles :
+ Les cœurs dédiés par région ont une limite par défaut de 10 à 24.  Vous pouvez augmenter le nombre de cœurs dédiés par abonnement Batch AI. Contactez l'assistance clientèle Azure pour discuter des possibilités d'augmentation.

+ Les cœurs à priorité basse par région ont une limite par défaut de 10 à 24.  Vous pouvez augmenter le nombre de cœurs à priorité basse par abonnement Batch AI. Contactez l'assistance clientèle Azure pour discuter des possibilités d'augmentation.

+ Les clusters par région ont une limite par défaut de 20 et une limite maximale de 200. Contactez le support Azure si vous voulez dépasser cette limite.

Pour obtenir une liste plus détaillée et à jour des limites de quota, consultez l’article sur les quotas à l’échelle d’Azure [ici](https://docs.microsoft.com/azure/azure-subscription-service-limits#batch-ai-limits).

### <a name="container-instances"></a>Container Instances

Il existe également une limite sur le nombre d’instances de conteneur que vous pouvez lancer dans un laps de temps donné (limite horaire) ou sur l’ensemble de votre abonnement.

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

Pour obtenir une liste plus détaillée et à jour des limites de quota, consultez l’article sur les quotas à l’échelle d’Azure [ici](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Stockage
Il existe une limite sur le nombre de comptes de stockage par région ainsi que dans un abonnement donné. La limite par défaut est de 200 et comprend les comptes de stockage Standard et Premium. Si vous avez besoin de plus de 200 comptes de stockage dans une région donnée, sollicitez le [Support Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) pour obtenir une assistance. L’équipe Stockage Azure examinera le cas de votre entreprise et pourra approuver jusqu’à 250 comptes de stockage pour une région donnée.


## <a name="find-your-quotas"></a>Rechercher vos quotas

L’affichage de votre quota pour différentes ressources, telles que les machines virtuelles, le stockage ou le réseau, est facile via le portail Azure.

1. Dans le volet gauche, sélectionnez **Tous les services**, puis **Abonnements** sous la catégorie Général.

1. Dans la liste des abonnements, sélectionnez celui dont vous recherchez le quota.

   **Il existe un inconvénient**, en particulier pour l’affichage du quota Batch AI. Comme mentionné ci-dessus, ce quota est distinct du quota de calcul de votre abonnement. 
   Pour Batch AI, après avoir sélectionné **Tous les services**, recherchez Batch AI et ouvrez le service.

1. Sous **Paramètres**, sélectionnez **Utilisation + quotas** pour afficher l’utilisation et les limites de quota actuelles.

1. Sélectionnez votre abonnement pour afficher les limites de quota. Pensez à filtrer sur le service et la région qui vous intéressent.


## <a name="request-quota-increases"></a>Demander des augmentations de quota

Si vous souhaitez élever la limite ou le quota au-dessus de la limite par défaut, [ouvrez une demande de service clientèle en ligne](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) gratuitement. 

Les limites ne peuvent pas être supérieures à la valeur de limite maximale indiquée dans les tableaux. En l’absence de limite maximale, la ressource spécifiée ne possède pas de limites ajustables. [Cet](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) article aborde le processus d’augmentation de quota plus en détail.

Quand vous demandez une augmentation de quota, vous devez sélectionner le service pour lequel vous la demandez, qui peut être un service tel que le quota AzureML, le quota Batch AI ou le quota de stockage. 

> [!NOTE]
> Les [abonnements d’essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p) ne permettent pas de bénéficier d’augmentations de la limite ou du quota. Si vous disposez d’un [abonnement d’essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p), vous pouvez le mettre à niveau vers un abonnement avec [paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/). Pour en savoir plus, référez-vous à la rubrique [Mise à niveau de la version d’évaluation gratuite d’Azure vers le paiement à l’utilisation](../../billing/billing-upgrade-azure-subscription.md) et [FAQ sur l’abonnement d’essai gratuit](https://azure.microsoft.com/free/free-account-faq).
