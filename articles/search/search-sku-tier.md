---
title: Sélectionnez un niveau tarifaire
titleSuffix: Azure Cognitive Search
description: 'En savoir plus sur les niveaux tarifaires (ou SKU) pour Recherche cognitive Azure. Un service de recherche peut être approvisionné aux niveaux suivants : Gratuit, De base et Standard. Standard est disponible sous différentes configurations de ressources et différents niveaux de capacité.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: 320f02f6ece106b4d0e14293f95533aa5b4e0743
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98693449"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Choisir un niveau tarifaire pour Recherche cognitive Azure

[Créer un service de recherche](search-create-service-portal.md) signifie entre autres que vous choisissez un niveau tarifaire (ou une référence SKU) qui est fixé pour toute la durée de vie du service. La tarification, ou le coût mensuel estimé pour l’exécution du service, s’affichent dans la page du portail **Sélectionnez le niveau tarifaire** quand vous créez le service. Si vous effectuez l’approvisionnement par le biais de PowerShell ou de Azure CLI à la place, le niveau est spécifié via le paramètre **`-Sku`** et nous vous conseillons de consulter la [tarification du service](https://azure.microsoft.com/pricing/details/search/) pour en savoir plus sur les coûts estimés.

Le niveau que vous sélectionnez détermine ce qui suit :

+ Nombre maximal d’index et d’autres objets autorisés sur le service
+ Taille et la vitesse des partitions (stockage physique)
+ Taux facturable, comme un coût mensuel fixe, mais également un coût incrémentiel si vous ajoutez plus de capacité

Dans quelques instances, le niveau que vous choisissez détermine la disponibilité des [fonctionnalités Premium](#premium-features).

> [!NOTE]
> Vous recherchez des informations sur « Références SKU » ? Commencez par consulter la [Tarification Azure](https://azure.microsoft.com/pricing/), puis faites défiler vers le bas pour afficher les liens vers les pages de tarification par service.

## <a name="tier-descriptions"></a>Descriptions des niveaux

Les niveaux disponibles sont : **Gratuit**, **De base**, **Standard** et **À stockage optimisé**. Les niveaux Standard et À stockage optimisé sont proposés dans diverses configurations et capacités. La capture d’écran suivante du portail Azure indique les niveaux disponibles, après la déduction indiquée sur le portail et dans la [page de tarification](https://azure.microsoft.com/pricing/details/search/). 

:::image type="content" source="media/search-sku-tier/tiers.png" alt-text="Graphique du niveau tarifaire" border="true":::

Le niveau **Gratuit** crée un service de recherche limité pour les projets plus petits, notamment les tutoriels et les exemples de code. En interne, les ressources système sont partagées entre plusieurs abonnés. Vous ne pouvez pas mettre à l’échelle un service gratuit ni exécuter des charges de travail importantes.

Les niveaux **De base** et **Standard** sont les niveaux facturables les plus couramment utilisés, **Standard** étant le niveau par défaut, car il vous offre plus de flexibilité dans la mise à l’échelle des charges de travail. Grâce à des ressources dédiées sous votre contrôle, vous pouvez déployer des projets plus volumineux, optimiser les performances et augmenter la capacité.

Certains niveaux sont conçus pour certains types de travaux. Par exemple, le niveau **Standard 3 High Density (S3 HD)** est un *mode d’hébergement* pour S3, où le matériel sous-jacent est optimisé pour un grand nombre d’index plus petits, qui est destiné aux scénarios d’architecture mutualisée. Le niveau S3 HD présente les mêmes frais à l’unité que S3, mais le matériel est optimisé pour les lectures de fichiers rapides sur un grand nombre d’index plus petits.

Les niveaux **À stockage optimisé** offrent une capacité de stockage plus importante et à moindre coût par To que les niveaux Standard. Le principal compromis impliqué par ces niveaux réside dans une latence de requête plus élevée, ce que vous devez valider pour vos exigences applicatives spécifiques. Pour plus d’informations sur les considérations en matière de performances de ce niveau, consultez l’article [Considérations sur les performances et l’optimisation](search-performance-optimization.md).

Des informations complémentaires sur les différents niveaux sont disponibles sur la [page de tarification](https://azure.microsoft.com/pricing/details/search/), dans l’article [Service limits in Azure Search](search-limits-quotas-capacity.md) (Limites du service Recherche cognitive Azure), ainsi que sur la page du portail lorsque vous approvisionnez un service.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>Disponibilité des fonctionnalités par niveau

La plupart des fonctionnalités sont disponibles dans tous les niveaux, y compris le niveau gratuit. Dans certains cas, le niveau que vous choisissez aura un impact sur votre capacité à implémenter une fonctionnalité. Le tableau suivant décrit les contraintes de fonctionnalités liées au niveau de service.

| Fonctionnalité | Limites |
|---------|-------------|
| [indexeurs](search-indexer-overview.md) | Les indexeurs ne sont pas disponibles sur S3 HD.  |
| [Enrichissement par IA](search-security-manage-encryption-keys.md) | Fonctionne au niveau Gratuit, mais n’est pas recommandé. |
| [Identités managées ou approuvées pour l’accès sortant (indexeur)](search-howto-managed-identities-data-sources.md) | Non disponibles au niveau Gratuit.|
| [Clés de chiffrement gérées par le client](search-security-manage-encryption-keys.md) | Non disponibles au niveau Gratuit. |
| [Accès au pare-feu IP](service-configure-firewall.md) | Non disponibles au niveau Gratuit. |
| [Point de terminaison privé (intégration à Azure Private Link)](service-create-private-endpoint.md) | Pour les connexions entrantes à un service de recherche ; non disponible au niveau Gratuit. Pour les connexions sortantes, par des indexeurs, à d'autres ressources Azure ; non disponible aux niveaux Gratuit et S3 HD. Pour les indexeurs qui utilisent des ensembles de compétences ; non disponible aux niveaux Gratuit, De base, S1 et S3 HD.|

Les fonctionnalités gourmandes en ressources risquent de ne pas fonctionner correctement si vous ne leur attribuez pas une capacité suffisante. Par exemple, [l’enrichissement par IA](cognitive-search-concept-intro.md) implique des qualifications à long terme qui dépassent le délai d’attente sur un service Gratuit, sauf si le jeu de données est restreint.

## <a name="upper-limits"></a>Limites supérieures

Les niveaux déterminent le stockage maximal du service proprement dit, ainsi que le nombre maximal d’index, d’indexeurs, de sources de données, d’ensembles de compétences et de cartes de synonymes que vous pouvez créer. Pour connaître toutes les limites, consultez [Limites de service de Recherche cognitive Azure](search-limits-quotas-capacity.md). 

## <a name="partition-size-and-speed"></a>Taille et vitesse de la partition

La tarification du niveau comprend des détails sur le stockage par partition qui varie de 2 Go pour le niveau De base à 2 To pour les niveaux de stockage optimisés (L2). D’autres caractéristiques matérielles, telles que la vitesse des opérations, la latence et les taux de transfert, ne sont pas publiées, mais les niveaux conçus pour des architectures de solution spécifiques sont basés sur du matériel qui possède les caractéristiques nécessaires pour prendre en charge ces scénarios. Pour plus d’informations sur les partitions, consultez [Estimer et gérer la capacité](search-capacity-planning.md) et [Mettre à l’échelle pour les performances](search-performance-optimization.md).

## <a name="billing-rates"></a>Tarifs de facturation

Les niveaux ont des tarifs de facturation différents, avec des tarifs plus élevés pour les niveaux qui s’exécutent sur du matériel plus onéreux ou qui offrent des fonctionnalités plus coûteuses. Le tarif de facturation par niveau est disponible dans les [pages de tarification Azure](https://azure.microsoft.com/pricing/details/search/) pour Recherche cognitive Azure.

Une fois que vous avez créé un service, le tarif de facturation devient à la fois un *coût fixe* pour faire fonctionner le service 24 h sur 24 et un *coût incrémentiel* si vous choisissez d’ajouter plus de capacité.

Les services de recherche sont alloués aux ressources de calcul sous la forme de *partitions* (pour le stockage) et de *réplicas* (instances du moteur d’interrogation). Au départ, un service est créé avec un de chaque, et le tarif de facturation comprend les deux ressources. Toutefois, si vous mettez à l’échelle la capacité, les coûts augmentent ou diminuent par incréments du tarif facturable.

L'exemple suivant illustre cette situation. Supposons un tarif de facturation hypothétique de 100 USD par mois. Si vous conservez le service de recherche à sa capacité initiale d’une partition et d’un réplica, vous pouvez alors vous attendre à payer la somme de 100 USD à la fin du mois. Toutefois, si vous ajoutez deux réplicas supplémentaires pour obtenir une haute disponibilité, la facture mensuelle s’élève à 300 USD (100 USD pour la première paire réplica-partition, puis 200 USD pour les deux réplicas).

Ce modèle de tarification est basé sur le concept d’application du tarif de facturation au nombre d’*unités de recherche* (SU) utilisées par un service de recherche. Tous les services sont initialement approvisionnés avec une SU, mais vous pouvez augmenter le nombre de SU en ajoutant des partitions ou des réplicas pour gérer des charges de travail plus volumineuses. Pour plus d’informations, consultez [Comment estimer les coûts d’un service de recherche](search-sku-manage-costs.md).

## <a name="next-steps"></a>Étapes suivantes

La meilleure façon de choisir un niveau tarifaire est de commencer par le niveau le moins cher, puis de laisser l’expérience et les tests éclairer votre décision de conserver le service ou d’en créer un nouveau à un niveau supérieur. Pour les étapes suivantes, nous vous recommandons de créer un service de recherche à un niveau qui puisse prendre en charge le niveau de test que vous vous proposez de faire, puis de consulter l’aide suivante pour obtenir des recommandations sur l’estimation du coût et de la capacité.

+ [Créer un service de recherche](search-create-service-portal.md)
+ [Estimer les coûts](search-sku-manage-costs.md)
+ [Estimer la capacité](search-sku-manage-costs.md)