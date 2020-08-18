---
title: Gérer les ressources et les quotas
titleSuffix: Azure Machine Learning
description: Obtenez plus d’informations sur les quotas sur les ressources pour Azure Machine Learning et sur la manière de demander plus de quota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 05/08/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4
ms.openlocfilehash: a9ae3d2789758d03405fb5be82181c799d1ea692
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141123"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>Gérer et augmenter les quotas pour les ressources avec Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous allez découvrir les limites préconfigurées des ressources Azure pour votre abonnement [Azure Machine Learning](overview-what-is-azure-ml.md) et les quotas que vous pouvez gérer. Ces limites sont mises en place pour empêcher les dépassements de budget dus à des fraudes et respecter les contraintes de capacité Azure. 

Comme avec d’autres services Azure, il existe des limites concernant certaines ressources associées à Azure Machine Learning. Ces limites vont du plafond appliqué au nombre d’[espaces de travail](concept-workspace.md) aux limites sur les capacités de calcul sous-jacentes réelles utilisées pour l’entraînement des modèles ou l’inférence/le scoring. 

Lorsque vous concevez et effectuez une mise à l’échelle de vos ressources Azure Machine Learning pour des charges de travail de production, gardez ces quotas à l’esprit. Par exemple, si votre cluster n’atteint pas le nombre cible de nœuds, c’est peut-être le signe que vous avez atteint la limite de cœurs de la Capacité de calcul Azure Machine Learning de votre abonnement. Si vous souhaitez élever la limite ou le quota au-dessus de la limite par défaut, ouvrez gratuitement une demande de service clientèle en ligne. Les limites ne peuvent pas être supérieures à la valeur Limite maximale indiquée dans les tableaux suivants, en raison des contraintes de capacité Azure. Si aucune colonne Limite maximale n’est présente, la ressource spécifiée ne possède pas de limites ajustables.


En plus de la gestion des quotas, vous pouvez également apprendre à [planifier et gérer les coûts pour Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Considérations spéciales

+ Un quota est une limite de crédit, pas une garantie de capacité. Si vous avez des besoins de capacité à grande échelle, contactez le support Azure. Vous pouvez également [augmenter vos quotas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

+ Votre quota est partagé entre tous les services de votre abonnement, y compris Azure Machine Learning. La seule exception est la Capacité de calcul Azure Machine Learning, qui possède un quota distinct du quota de cœurs de calcul. Veillez à calculer le rapport d’utilisation des quotas entre tous les services lors de l’évaluation de vos besoins de capacité.

+ Les limites par défaut varient selon le type de catégorie d’offre, comme Essai gratuit ou Paiement à l’utilisation, et selon la gamme de machine virtuelle (Dv2, F, G, etc.).

## <a name="default-resource-quotas"></a>Quotas de ressources par défaut

Voici la répartition des limites de quota selon les différents types de ressources au sein de votre abonnement Azure.

> [!IMPORTANT]
> Les limites sont susceptibles d’être modifiées. La dernière version se trouve toujours dans le [document](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) sur les quotas au niveau du service pour l’ensemble d’Azure.

### <a name="virtual-machines"></a>Machines virtuelles
Pour chaque abonnement Azure, il existe une limite au nombre de machines virtuelles entre vos services ou de manière autonome. Une limite totale régionale et une limite régionale par gamme de taille (Dv2, F, etc.) s’appliquent séparément aux cœurs de machine virtuelle. Par exemple, considérons un abonnement dont le nombre total limite de cœurs de machine virtuelle est de 30 pour la région USA Est, de 30 pour la gamme A et de 30 pour la gamme D. Cet abonnement serait autorisé à déployer 30 machines virtuelles A1, ou 30 machines virtuelles D1, ou encore une combinaison de ces deux types de machines dans la limite de 30 cœurs au total (par exemple, 10 machines virtuelles A1 et 20 machines virtuelles D1).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Capacité de calcul Azure Machine Learning
La [capacité de calcul Azure Machine Learning](concept-compute-target.md#azure-machine-learning-compute-managed) fixe une limite de quota par défaut sur le nombre de cœurs et le nombre de ressources de calcul autorisées par région dans un abonnement. Ce quota est distinct du quota de cœurs de machine virtuelle ci-dessus et les limites principales ne sont pas partagées entre les deux types de ressources étant donné qu’AmlCompute est un service géré qui déploie des ressources dans un modèle « hébergé pour le compte de ».

Ressources disponibles :
+ Les cœurs dédiés par région ont une limite par défaut de 24 - 300 en fonction du type de votre offre d’abonnement avec des limites par défaut supérieures pour les types d’offres EA et CSP.  Le nombre de cœurs dédiés par abonnement peut être augmenté et est différent pour chaque famille de machine virtuelle. Certaines familles de machines virtuelles spécialisées comme NCv2, NCv3 ou ND commencent par une valeur par défaut de zéro cœur. Contactez l’assistance clientèle Azure en formulant une demande de quota pour discuter des possibilités d’augmentation.

+ Les cœurs basse priorité par région ont une limite par défaut de 100 - 3 000 en fonction du type de votre offre d’abonnement avec des limites par défaut supérieures pour les types d’offres EA et CSP. Le nombre de cœurs basse priorité par abonnement peut être augmenté et représente une valeur unique entre les familles de machines virtuelles. Contactez l'assistance clientèle Azure pour discuter des possibilités d'augmentation.

+ Les clusters par région ont une limite par défaut de 200. Celle-ci est partagée entre un cluster d’entraînement et une instance de calcul (qui est considérée comme un cluster à nœud unique à des fins de quota). Contactez le support Azure si vous voulez dépasser cette limite.

+ Il existe d’autres limites strictes qui ne peuvent pas être dépassées une fois atteintes.

| **Ressource** | **Limite maximale** |
| --- | --- |
| Nombre maximum d’espaces de travail par groupe de ressources | 800 |
| Nombre maximal de nœuds dans une seule ressource Capacité de calcul Azure Machine Learning (AmlCompute) | 100 nœuds |
| Nombre maximum de processus MPI GPU par nœud | 1-4 |
| Nombre maximum de travailleurs GPU par nœud | 1-4 |
| Durée de vie maximale des travaux | 21 jours<sup>1</sup> |
| Durée de vie maximale des travaux sur un nœud basse priorité | 7 jours<sup>2</sup> |
| Nombre maximum de serveurs de paramètre par nœud | 1 |

<sup>1</sup> La durée de vie maximale fait référence au temps du début à la fin d’une exécution. Les exécutions terminées sont conservées indéfiniment ; les données des exécutions non terminées pendant la durée de vie maximale ne sont pas accessibles.
<sup>2</sup> Les travaux sur un nœud basse priorité doivent être vidés au préalable dès qu’il existe une contrainte de capacité. Nous vous recommandons d’implémenter des points de contrôle dans votre travail.

### <a name="azure-machine-learning-pipelines"></a>Pipelines Azure Machine Learning
Pour les [pipelines Azure Machine Learning](concept-ml-pipelines.md), il existe une limite de quota sur le nombre d’étapes d’un pipeline ainsi que sur le nombre d’exécutions planifiées de pipelines publiés pour chaque région d’un abonnement.
- Un pipeline peut contenir au maximum 30 000 étapes.
- Le nombre maximal d’exécutions basées sur une planification et d’extractions d’objets blob pour les planifications déclenchées par blob sur les pipelines publiés par abonnement et par mois est de 100 000

### <a name="container-instances"></a>Container Instances

Il existe également une limite sur le nombre d’instances de conteneur que vous pouvez lancer dans un laps de temps donné (limite horaire) ou sur l’ensemble de votre abonnement.
Pour les limites, consultez [Limites de Container Instances](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Stockage
Il existe une limite sur le nombre de comptes de stockage par région ainsi que dans un abonnement donné. La limite par défaut est de 250 et comprend les comptes de stockage Standard et Premium. Si vous avez besoin de plus de 250 comptes de stockage dans une région donnée, sollicitez le [Support Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). L’équipe Stockage Azure examinera le cas de votre entreprise et pourra approuver jusqu’à 250 comptes de stockage pour une région donnée.


## <a name="workspace-level-quota"></a>Quota au niveau de l’espace de travail

Pour mieux gérer les allocations de ressources pour la cible de calcul Azure Machine Learning (Amlcompute) entre les différents [espaces de travail](concept-workspace.md), nous avons introduit une fonctionnalité qui vous permet de distribuer des quotas au niveau de l’abonnement (par famille de machines virtuelles) et de les configurer au niveau de l’espace de travail. Le comportement par défaut est que tous les espaces de travail ont le même quota que le quota de niveau abonnement pour toutes les familles de machines virtuelles. Toutefois, au fur et à mesure que le nombre d’espaces de travail augmente et que des charges de travail de différentes priorités commencent à partager les mêmes ressources, les utilisateurs souhaitent disposer d’un meilleur moyen de partage de la capacité et éviter les problèmes de conflit de ressources. Azure Machine Learning propose une solution avec son offre de calcul gérée en permettant aux utilisateurs de définir un quota maximal pour une famille de machines virtuelles spécifique sur chaque espace de travail. Cela est similaire à la distribution de votre capacité entre les espaces de travail, et les utilisateurs peuvent choisir d’allouer trop de ressources afin d’optimiser l’utilisation du disque. 

Pour définir des quotas au niveau de l’espace de travail, accédez à n’importe quel espace de travail de votre abonnement, puis cliquez sur **Usages + quotas** (Utilisations + quotas) dans le volet gauche. Sélectionnez ensuite l’onglet **Configurer les quotas** pour afficher les quotas, développez toutes les familles de machines virtuelles, puis définissez une limite de quota sur un espace de travail répertorié sous cette famille de machines virtuelles. N’oubliez pas que vous ne pouvez pas définir une valeur négative ou une valeur supérieure au quota de niveau d’abonnement. En outre, comme vous pouvez le constater, tous les espaces de travail se voient attribuer par défaut le quota d’abonnement entier pour permettre une utilisation complète du quota alloué.

[![Quota de niveau d’espace de travail Azure Machine Learning](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Il s’agit d’une fonctionnalité de l’édition Entreprise uniquement. Si vous disposez d’un espace de travail d’[édition De Base et Entreprise](overview-what-is-azure-ml.md#sku) dans votre abonnement, vous ne pouvez l’utiliser que pour définir des quotas sur vos espaces de travail Entreprise. Vos espaces de travail De base continueront d’avoir le quota de niveau d’abonnement, qui est le comportement par défaut.
>
> Vous avez besoin d’autorisations de niveau d’abonnement pour définir le quota au niveau de l’espace de travail. Cela est appliqué afin que les propriétaires de l’espace de travail individuel ne modifient pas ou n’augmentent pas leurs quotas et ne commencent à empiéter sur les ressources réservées pour un autre espace de travail. Ainsi, un administrateur d’abonnement est mieux adapté à l’allocation et à la distribution de ces quotas entre les espaces de travail.



## <a name="view-your-usage-and-quotas"></a>Voir votre utilisation et les quotas

Le quota Capacité de calcul Machine Learning sur votre abonnement est géré indépendamment des autres quotas de ressources Azure. Pour voir ce quota, vous devez explorer au niveau du détail les services Machine Learning.  

1. Dans le volet gauche, sélectionnez **Machine Learning service**, puis sélectionnez un espace de travail dans la liste qui s’affiche.

2. Dans le panneau suivant, sous la section **Support + résolution des problèmes**, sélectionnez **Utilisation + quotas** pour afficher vos limites de quota et votre utilisation actuelles.

3. Sélectionnez un abonnement pour afficher les limites de quota. Pensez à ajouter un filtre sur la région qui vous intéresse.

4. Vous pouvez maintenant basculer entre une vue de niveau d’abonnement et une vue de niveau d’espace de travail :
    + **Vue d’abonnement** : Cela vous permet d’afficher votre utilisation du quota de cœurs par famille de machines virtuelles, de l’étendre par espace de travail et de l’étendre davantage par noms de clusters réels. Cette vue est optimale pour obtenir rapidement les détails de l’utilisation de base pour une famille de machines virtuelles particulière afin de voir la répartition par espace de travail et davantage par les clusters sous-jacents pour chacun de ces espaces de travail. La convention générale dans cette vue est (utilisation/quota), où l’utilisation est le nombre actuel de cœurs mis à l’échelle, et quota est le nombre maximum logique de cœurs auquel la ressource peut évoluer. Pour chaque **espace de travail**, le quota correspond au quota de niveau de l’espace de travail (comme expliqué ci-dessus) qui indique le nombre maximum de cœurs que vous pouvez mettre à l’échelle pour une famille de machines virtuelles spécifique. De la même façon, pour un **cluster**, le quota représente en fait le nombre de cœurs correspondant au nombre maximum de nœuds auquel le cluster peut évoluer tel que défini par la propriété max_nodes.
    
    + **Vue d’espace de travail** : Cela vous permet d’afficher votre utilisation du quota de cœurs par espace de travail, de l’étendre par famille de machines virtuelles et de l’étendre davantage par noms de clusters réels. Cette vue est optimale pour obtenir rapidement les détails de l’utilisation de base pour un espace de travail particulier afin de voir la répartition par familles de machines virtuelles et davantage par les clusters sous-jacents pour chacune de ces familles.

Il est facile de voir votre quota pour les diverses autres ressources Azure, comme les machines virtuelles, le stockage ou le réseau, via le portail Azure.

1. Dans le volet gauche, sélectionnez **Tous les services**, puis **Abonnements** sous la catégorie Général.

2. Dans la liste des abonnements, sélectionnez celui dont vous recherchez le quota.

3. Sélectionnez **Utilisation + quotas** pour afficher l’utilisation et les limites de quota actuelles. Utilisez les filtres pour sélectionner le fournisseur et les emplacements. 

## <a name="request-quota-increases"></a>Demander des augmentations de quota

Si vous souhaitez élever la limite ou le quota au-dessus de la limite par défaut, [ouvrez une demande de service clientèle en ligne](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) gratuitement.

Les limites ne peuvent pas être supérieures à la valeur de limite maximale indiquée dans les tableaux. En l’absence de limite maximale, la ressource spécifiée ne possède pas de limites ajustables. [Découvrez comment augmenter votre quota en suivant des instructions pas à pas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

Quand vous demandez une augmentation de quota, vous devez sélectionner le service pour lequel vous la demandez, par exemple, le quota Machine Learning service, Container Instances ou le quota de stockage. En ce qui concerne la Capacité de calcul Azure Machine Learning, vous pouvez cliquer sur le bouton **Demander le quota** au lieu de suivre les étapes ci-dessus.

> [!NOTE]
> Les [abonnements d’essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p) ne permettent pas de bénéficier d’augmentations de la limite ou du quota. Si vous disposez d’un [abonnement d’essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p), vous pouvez le mettre à niveau vers un abonnement avec [paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/). Pour en savoir plus, référez-vous à la rubrique [Mise à niveau de la version d’évaluation gratuite d’Azure vers le paiement à l’utilisation](../billing/billing-upgrade-azure-subscription.md) et [FAQ sur l’abonnement d’essai gratuit](https://azure.microsoft.com/free/free-account-faq).

## <a name="next-steps"></a>Étapes suivantes

+ [Planifier et gérer les coûts d’Azure Machine Learning](concept-plan-manage-cost.md)
