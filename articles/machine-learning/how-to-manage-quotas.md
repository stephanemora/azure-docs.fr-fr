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
ms.date: 10/13/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4, contperfq2
ms.openlocfilehash: 4b072257d49011819fe19d6e2901560df43b26dc
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275553"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>Gérer et augmenter les quotas pour les ressources avec Azure Machine Learning

Azure utilise des limites et des quotas pour empêcher les dépassements de budget dus à des fraudes et respecter les contraintes de capacité Azure. Tenez compte de ces limites lors de la mise à l’échelle des charges de travail de production. Cet article porte sur les points suivants :

> [!div class="checklist"]
> + Limites par défaut des ressources Azure relatives à [Azure Machine Learning](overview-what-is-azure-ml.md).
> + Consulter vos quotas et limites.
> + Créer des quotas au niveau de l’espace de travail.
> + Demander des augmentations de quota.
> + Point de terminaison privé et quotas DNS.

En plus de la gestion des quotas, vous pouvez également apprendre à [planifier et gérer les coûts pour Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Considérations spéciales

+ Un quota est une limite de crédit, pas une garantie de capacité. Si vous avez des besoins de capacité à grande échelle, [contactez le support Azure pour augmenter votre quota](#request-quota-increases).

+ Le quota est partagé entre tous les services de vos abonnements, y compris Azure Machine Learning. Calculez l’utilisation entre tous les services lors de l’évaluation de la capacité.
    + La seule exception est la Capacité de calcul Azure Machine Learning, qui possède un quota distinct du quota de cœurs de calcul. 

+ Les limites par défaut varient selon le type de catégorie d’offre, comme Essai gratuit ou Paiement à l’utilisation, et selon la gamme de machine virtuelle (Dv2, F, G, etc.).

## <a name="default-resource-quotas"></a>Quotas de ressources par défaut

Cette section porte sur les limites de quota par défaut et maximale pour les ressources suivantes :

+ Machines virtuelles
+ Capacité de calcul Azure Machine Learning
+ Pipelines Azure Machine Learning
+ Container Instances
+ Stockage

> [!IMPORTANT]
> Les limites sont susceptibles d’être modifiées. La dernière version se trouve toujours dans le [document](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) sur les quotas au niveau du service pour l’ensemble d’Azure.

### <a name="virtual-machines"></a>Machines virtuelles
Chaque abonnement Azure est limité quant au nombre de machines virtuelles entre tous les services. Une limite totale régionale et une limite régionale par gamme de taille (Dv2, F, etc.) s’appliquent aux cœurs de machine virtuelle. Les deux limites sont appliquées séparément.

Par exemple, considérons un abonnement dont le nombre total limite de cœurs de machine virtuelle est de 30 pour la région USA Est, de 30 pour la gamme A et de 30 pour la gamme D. Cet abonnement serait autorisé à déployer 30 machines virtuelles A1, ou 30 machines virtuelles D1, ou encore une combinaison de ces deux types de machines dans la limite de 30 cœurs au total.

Les limites pour les machines virtuelles ne peuvent pas être augmentées au-delà de la valeur indiquée dans le tableau suivant.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Capacité de calcul Azure Machine Learning
La [capacité de calcul Azure Machine Learning](concept-compute-target.md#azure-machine-learning-compute-managed) a une limite de quota par défaut sur le nombre de cœurs et le nombre de ressources de calcul autorisées par région dans un abonnement. Ce quota est distinct du quota de cœurs de machines virtuelles de la section précédente.

[Demandez une augmentation du quota](#request-quota-increases) pour augmenter les limites de cette section jusqu’à la **limite maximale** indiquée dans le tableau.

Ressources disponibles :
+ Les **cœurs dédiés par région** ont une limite par défaut de 24 - 300 en fonction du type de votre offre d’abonnement.  Le nombre de cœurs dédiés par abonnement peut être augmenté pour chaque famille de machine virtuelle. Les familles de machines virtuelles spécialisées comme NCv2, NCv3 ou ND ont une valeur initiale par défaut de zéro cœur.

+ Les **cœurs de faible priorité par région** ont une limite par défaut de 100 - 3 000 en fonction du type de votre offre d’abonnement. Le nombre de cœurs basse priorité par abonnement peut être augmenté et représente une valeur unique entre les familles de machines virtuelles.

+ Les **clusters par région** ont une limite par défaut de 200. Celle-ci est partagée entre un cluster d’entraînement et une instance de calcul (qui est considérée comme un cluster à nœud unique à des fins de quota).

Le tableau suivant indique les limites supplémentaires qui ne peuvent pas être dépassées.

| **Ressource** | **Limite maximale** |
| --- | --- |
| Espaces de travail par groupe de ressources | 800 |
| Nœuds dans une seule ressource Capacité de calcul Azure Machine Learning (AmlCompute) | 100 nœuds |
| Processus MPI GPU par nœud | 1-4 |
| Workers GPU par nœud | 1-4 |
| Durée de vie du travail | 21 jours<sup>1</sup> |
| Durée de vie des travaux sur un nœud basse priorité | 7 jours<sup>2</sup> |
| Serveurs de paramètres par nœud | 1 |

<sup>1</sup> La durée de vie maximale fait référence à la durée entre le début et la fin d’une exécution. Les exécutions terminées sont conservées indéfiniment. Les données des exécutions non terminées pendant la durée de vie maximale ne sont pas accessibles.
<sup>2</sup> Les travaux sur un nœud basse priorité doivent être vidés au préalable dès qu’il existe une contrainte de capacité. Nous vous recommandons d’implémenter des points de contrôle dans votre travail.

### <a name="azure-machine-learning-pipelines"></a>Pipelines Azure Machine Learning
Les [Pipelines Azure Machine Learning](concept-ml-pipelines.md) ont les limites suivantes.

| **Ressource** | **Limite** |
| --- | --- |
| Étapes d’un pipeline | 30,000 |
| Espaces de travail par groupe de ressources | 800 |

### <a name="container-instances"></a>Container Instances

Pour plus d’informations, consultez [Limites de Container Instances](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Stockage
Les comptes de stockage Azure ont une limite de 250 comptes de stockage par région et par abonnement. Cela inclut à la fois les comptes de stockage Standard et Premium.

Pour augmenter la limite, effectuez une demande via le [support Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). L’équipe Stockage Azure examinera votre cas et pourrait approuver jusqu’à 250 comptes de stockage pour une région.


## <a name="workspace-level-quota"></a>Quota au niveau de l’espace de travail

Utilisez les quotas au niveau de l’espace de travail pour l’allocation cible de Capacité de calcul Azure Machine Learning entre plusieurs [espaces de travail](concept-workspace.md) dans le même abonnement.

Par défaut, tous les espaces de travail partagent le même quota que le quota au niveau de l’abonnement pour toutes les familles de machines virtuelles. Toutefois, vous pouvez définir un quota maximal pour les familles de machines virtuelles individuelles dans les espaces de travail d’un abonnement. Cela vous permet de partager la capacité et d’éviter les problèmes de conflit entre les ressources :

1. Accédez à n’importe quel espace de travail dans votre abonnement.
1. Dans le volet de gauche, sélectionnez **Utilisation + quotas** .
1. Sélectionnez l’onglet **Configurer les quotas** pour afficher les quotas.
1. Développez une famille de machines virtuelles.
1. Définissez une limite de quota sur un espace de travail répertorié sous cette famille de machines virtuelles.

Vous ne pouvez pas définir une valeur négative ou une valeur supérieure au quota au niveau de l’abonnement.

[![Quota de niveau d’espace de travail Azure Machine Learning](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> Vous avez besoin d’autorisations de niveau d’abonnement pour définir le quota au niveau de l’espace de travail.

## <a name="view-your-usage-and-quotas"></a>Voir votre utilisation et les quotas

Pour afficher votre quota pour différentes ressources Azure, telles que Machines virtuelles, Stockage, Réseau, utilisez le portail Azure :

1. Dans le volet gauche, sélectionnez **Tous les services** , puis **Abonnements** sous la catégorie Général.

2. Dans la liste des abonnements, sélectionnez celui dont vous recherchez le quota.

3. Sélectionnez **Utilisation + quotas** pour afficher l’utilisation et les limites de quota actuelles. Utilisez les filtres pour sélectionner le fournisseur et les emplacements. 

Le quota Capacité de calcul Azure Machine Learning sur votre abonnement est géré indépendamment des autres quotas Azure. 

1. Dans le portail Azure, accédez à votre espace de travail **Azure Machine Learning** .

2. Dans le volet de gauche, sous la **section Support + résolution des problèmes** , sélectionnez **Utilisation + quotas** pour afficher vos limites de quota et votre utilisation actuelles.

3. Sélectionnez un abonnement pour afficher les limites de quota. Pensez à ajouter un filtre sur la région qui vous intéresse.

4. Vous pouvez basculer entre une vue au niveau de l’abonnement et une vue au niveau de l’espace de travail.

## <a name="request-quota-increases"></a>Demander des augmentations de quota

Pour augmenter la limite ou le quota au-delà de la limite par défaut, [ouvrez une demande de support client en ligne](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) gratuitement.

Les limites ne peuvent pas être supérieures à la valeur de la limite maximale indiquée dans les tableaux ci-dessus. S’il n’y a pas de limite maximale, vous ne pouvez pas ajuster la limite pour la ressource.

Lorsque vous demandez une augmentation du quota, sélectionnez le service sur lequel vous souhaitez augmenter le quota. Par exemple : Azure Machine Learning, Container Instances, Stockage, etc. En ce qui concerne la Capacité de calcul Azure Machine Learning, vous pouvez sélectionner le bouton **Demander un quota** au lieu de suivre les étapes ci-dessus.

> [!NOTE]
> Les [abonnements d’essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p) ne permettent pas de bénéficier d’augmentations de la limite ou du quota. Si vous disposez d’un [abonnement d’essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p), vous pouvez le mettre à niveau vers un abonnement avec [paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/). Pour en savoir plus, référez-vous à la rubrique [Mise à niveau de la version d’évaluation gratuite d’Azure vers le paiement à l’utilisation](../billing/billing-upgrade-azure-subscription.md) et [FAQ sur l’abonnement d’essai gratuit](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Augmentations du quota pour le point de terminaison privé et le domaine DNS privé

Il existe des limites quant au nombre de points de terminaison privés et de zones DNS privées qui peuvent être créés dans un abonnement.

Bien qu’Azure Machine Learning crée des ressources dans votre abonnement (client), certains scénarios créent des ressources dans un abonnement appartenant à Microsoft.

 Dans les scénarios suivants, vous devrez peut-être demander une allocation de quota dans l’abonnement appartenant à Microsoft :

* __Espace de travail activé pour Azure Private Link avec une clé gérée par le client (CMK)__
* __Azure Container Registry de l’espace de travail derrière votre réseau virtuel__
* __Attachement d’un cluster Azure Kubernetes Service compatible avec Private Link à votre espace de travail__ .

Pour demander une allocation pour ces scénarios, procédez comme suit :

1. [Créez une demande de support Azure](/azure/azure-portal/supportability/how-to-create-azure-support-request#create-a-support-request) et sélectionnez les options suivantes dans la section __Concepts de base__  :

    | Champ | Sélection |
    | ----- | ----- |
    | Type de problème | Prérequis techniques |
    | Service | Mes services. Dans la liste déroulante, sélectionnez __Machine Learning__ . |
    | Type de problème | Configuration et sécurité de l’espace de travail |
    | Sous-type de problème | Demande d’allocation pour la zone DNS privée et le point de terminaison privé |

2. Dans la section __Détails__ , utilisez le champ __Description__ pour indiquer la région Azure que vous souhaitez utiliser et le scénario que vous prévoyez d’utiliser. Si vous devez demander des augmentations de quota pour plusieurs abonnements, indiquez également les ID des abonnements dans ce champ.

3. Sélectionnez __Créer__ pour créer la demande.

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="Capture d’écran d’un point de terminaison privé et d’une demande d’augmentation de quota de DNS privé":::

## <a name="next-steps"></a>Étapes suivantes

+ [Planifier et gérer les coûts d’Azure Machine Learning](concept-plan-manage-cost.md)
