---
title: Gérer et demander des quotas de ressources
titleSuffix: Azure Machine Learning service
description: Ce guide de procédures décrit les différents quotas sur les ressources pour Azure Machine Learning, comment les afficher et comment demander un quota plus élevé.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: aa425b6dfeb076448d14fc35cbea964516d603b0
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765854"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Gérer et demander des quotas pour les ressources Azure

À l’instar d’autres services Azure, certaines ressources associées au service Azure Machine Learning présentent des limites. Ces limites vont du plafond appliqué au nombre d’espaces de travail que vous pouvez créer aux limites sur le calcul sous-jacent réel utilisé pour l’entraînement ou l’inférence de vos modèles. Cet article fournit d’autres détails sur les limites préconfigurées de différentes ressources Azure pour votre abonnement et contient également des liens pratiques afin de demander des améliorations de quotas pour chaque type de ressource. Ces limites sont mises en place pour empêcher les dépassements de budget dus à des fraudes et respecter les contraintes de capacité Azure.

Gardez ces quotas à l'esprit lorsque vous concevez et montez en puissance vos ressources Azure Machine Learning service pour des charges de travail de production. Par exemple, si votre cluster n’atteint pas le nombre cible de nœuds que vous avez spécifié, puis vous pourrez avoir atteint une limite de cœurs Azure Machine Learning Compute pour votre abonnement. Si vous souhaitez élever la limite ou le quota au-dessus de la limite par défaut, ouvrez gratuitement une demande de service clientèle en ligne. Les limites ne peuvent pas être supérieures à la valeur Limite maximale indiquée dans les tableaux suivants, en raison des contraintes de capacité Azure. Si aucune colonne Limite maximale n’est présente, la ressource spécifiée ne possède pas de limites ajustables.

## <a name="special-considerations"></a>Considérations spéciales

+ Un quota est une limite de crédit, pas une garantie de capacité. Si vous avez des besoins de capacité à grande échelle, contactez le support Azure.

+ Votre quota est partagé entre tous les services de votre abonnement, y compris Azure Machine Learning service. La seule exception est la Capacité de calcul Azure Machine Learning, qui possède un quota distinct du quota de cœurs de calcul. Veillez à calculer le rapport d’utilisation des quotas entre tous les services lors de l’évaluation de vos besoins de capacité.

+ Les limites par défaut varient selon le type de catégorie d’offre, comme Essai gratuit ou Paiement à l’utilisation, et selon la gamme (Dv2, F, G, etc.).

## <a name="default-resource-quotas"></a>Quotas de ressources par défaut

Voici la répartition des limites de quota selon les différents types de ressources au sein de votre abonnement Azure.

> [!Important]
> Les limites sont susceptibles d’être modifiées. La dernière version se trouve toujours dans le [document](https://docs.microsoft.com/azure/azure-subscription-service-limits/) sur les quotas au niveau du service pour l’ensemble d’Azure.

### <a name="virtual-machines"></a>Machines virtuelles
Il existe une limite au nombre de machines virtuelles que vous pouvez provisionner sur un abonnement Azure entre vos services ou de manière autonome. Cette limite se situe au niveau régional à la fois sur le nombre total de cœurs et en fonction de la famille.

Il est important de noter qu’une limite totale régionale et une limite régionale par gamme de taille (Dv2, F, etc.) s’appliquent séparément aux cœurs de machine virtuelle. Par exemple, considérons un abonnement dont le nombre total limite de cœurs de machine virtuelle est de 30 pour la région USA Est, de 30 pour la gamme A et de 30 pour la gamme D. Cet abonnement serait autorisé à déployer 30 machines virtuelles A1, ou 30 machines virtuelles D1, ou encore une combinaison de ces deux types de machines dans la limite de 30 cœurs au total (par exemple, 10 machines virtuelles A1 et 20 machines virtuelles D1).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

Pour obtenir une liste plus détaillée et à jour des limites de quota, consultez l’article sur les quotas à l’échelle d’Azure [ici](https://docs.microsoft.com/azure/azure-subscription-service-limits).

### <a name="azure-machine-learning-compute"></a>Capacité de calcul Azure Machine Learning
La Capacité de calcul Azure Machine Learning fixe une limite de quota par défaut sur le nombre de cœurs et le nombre de ressources de calcul autorisées par région dans un abonnement. Ce quota est distinct du quota de cœurs de machine virtuelle ci-dessus ; par ailleurs, les limites des cœurs ne sont pour l’instant pas partagées entre les deux types de ressources.

Ressources disponibles :
+ Les cœurs dédiés par région ont une limite par défaut de 10 à 24.  Vous pouvez augmenter le nombre de cœurs dédiés par abonnement. Contactez l'assistance clientèle Azure pour discuter des possibilités d'augmentation.

+ Les cœurs à priorité basse par région ont une limite par défaut de 10 à 24.  Vous pouvez augmenter le nombre de cœurs basse priorité par abonnement. Contactez l'assistance clientèle Azure pour discuter des possibilités d'augmentation.

+ Les clusters ont une limite par défaut de 100 et une limite maximale de 200 par région. Contactez le support Azure si vous voulez dépasser cette limite.

+ Il existe **d’autres limites strictes** qui ne peuvent être dépassées une fois atteintes.

| **Ressource** | **Limite maximale** |
| --- | --- |
| Nombre maximum d’espaces de travail par groupe de ressources | 800 |
| Nombre maximal de nœuds dans une seule ressource Capacité de calcul Azure Machine Learning (AmlCompute) | 100 nœuds |
| Nombre maximum de processus MPI GPU par nœud | 1-4 |
| Nombre maximum de travailleurs GPU par nœud | 1-4 |
| Durée de vie maximale des travaux | 7 jours<sup>1</sup> |
| Nombre maximum de serveurs de paramètre par nœud | 1 |

<sup>1</sup> La durée de vie maximale fait référence au temps du début à la fin d’une exécution. Les exécutions terminées sont conservées indéfiniment ; les données des exécutions non terminées pendant la durée de vie maximale ne sont pas accessibles.

### <a name="container-instances"></a>Container Instances

Il existe également une limite sur le nombre d’instances de conteneur que vous pouvez lancer dans un laps de temps donné (limite horaire) ou sur l’ensemble de votre abonnement.

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

Pour obtenir une liste plus détaillée et à jour des limites de quota, consultez l’article sur les quotas à l’échelle d’Azure [ici](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Stockage
Il existe une limite sur le nombre de comptes de stockage par région ainsi que dans un abonnement donné. La limite par défaut est de 200 et comprend les comptes de stockage Standard et Premium. Si vous avez besoin de plus de 200 comptes de stockage dans une région donnée, formulez une demande auprès du  [Support Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). L’équipe Stockage Azure examinera le cas de votre entreprise et pourra approuver jusqu’à 250 comptes de stockage pour une région donnée.


## <a name="find-your-quotas"></a>Rechercher vos quotas

L’affichage de votre quota pour différentes ressources, telles que les machines virtuelles, le stockage ou le réseau, est facile via le portail Azure.

1. Dans le volet gauche, sélectionnez  **Tous les services**, puis **Abonnements** sous la catégorie Général.

1. Dans la liste des abonnements, sélectionnez celui dont vous recherchez le quota.

   **Il existe une réserve**, en particulier pour ce qui est de voir le quota Capacité de calcul Azure Machine Learning. Comme mentionné ci-dessus, ce quota est distinct du quota de calcul de votre abonnement.

1. Dans le volet gauche, sélectionnez  **Machine Learning service**, puis sélectionnez un espace de travail dans la liste qui s’affiche.

1. Dans le panneau suivant, sous la section **Support + résolution des problèmes**, sélectionnez **Utilisation + quotas** pour afficher vos limites de quota et votre utilisation actuelles.

1. Sélectionnez un abonnement pour afficher les limites de quota. Pensez à ajouter un filtre sur la région qui vous intéresse.


## <a name="request-quota-increases"></a>Demander des augmentations de quota

Si vous souhaitez élever la limite ou le quota au-dessus de la limite par défaut,  [ouvrez une demande de service clientèle en ligne](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) gratuitement.

Les limites ne peuvent pas être supérieures à la valeur de limite maximale indiquée dans les tableaux. En l’absence de limite maximale, la ressource ne possède pas de limites ajustables. [Cet](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) article aborde le processus d’augmentation de quota plus en détail.

Quand vous demandez une augmentation de quota, vous devez sélectionner le service pour lequel vous la demandez, par exemple, le quota Machine Learning service, Container Instances ou le quota de stockage. En ce qui concerne la Capacité de calcul Azure Machine Learning, vous pouvez également cliquer sur le bouton **Demander le quota** au lieu de suivre les étapes ci-dessus.

> [!NOTE]
> Les [abonnements d’essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p) ne permettent pas de bénéficier d’augmentations de la limite ou du quota. Si vous disposez d’un [abonnement d’essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p), vous pouvez le mettre à niveau vers un abonnement avec [paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/). Pour en savoir plus, référez-vous à la rubrique [Mise à niveau de la version d’évaluation gratuite d’Azure vers le paiement à l’utilisation](../../billing/billing-upgrade-azure-subscription.md) et [FAQ sur l’abonnement d’essai gratuit](https://azure.microsoft.com/free/free-account-faq).
