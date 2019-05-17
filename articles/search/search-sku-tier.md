---
title: Choisir un niveau tarifaire ou une référence SKU pour le service Recherche Azure | Recherche Azure
description: 'Recherche Azure peut être configurée dans ces références (SKU) : Gratuit, base, Standard et des Standard sont disponible dans différentes configurations de ressources et les niveaux de capacité.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 00422209302bbcc2139be4f6b490f0bb2816c051
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65539284"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Choisir un niveau tarifaire pour Recherche Azure

Lorsque vous créez un service Azure Search, un [ressource est créée](search-create-service-portal.md) à un niveau tarifaire ou une référence (SKU) qui est fixe pour la durée de vie du service. Les niveaux incluent gratuit, Basic, Standard et optimisées pour le stockage. Standard et optimisées pour le stockage sont disponibles avec plusieurs configurations et capacités.

La plupart des clients démarrent avec le niveau gratuit afin qu’ils peuvent évaluer le service. Puis mise à niveau à un des niveaux supérieurs pour les déploiements de développement et de production. Vous pouvez effectuer tous les Démarrages rapides et didacticiels en utilisant le niveau gratuit, y compris celles pour la recherche cognitive gourmandes en ressources.

> [!NOTE]
> Microsoft fournit actuellement les niveaux de service de stockage optimisé dans une version préliminaire au tarif réduit pour les tests et l’expérimentation, dans le but de recueillir des commentaires. Le prix final sera annoncé ultérieurement lorsque ces niveaux sont généralement disponibles. Nous vous déconseillons l’utilisation de ces niveaux pour les applications de production.

Les niveaux reflètent les caractéristiques du matériel qui héberge le service (plutôt que les fonctionnalités) et se distinguent en fonction de ce qui suit :

+ Le nombre d’index que vous pouvez créer.
+ La taille et la vitesse de partitions (stockage physique).

Bien que tous les niveaux, y compris le niveau gratuit, offrent généralement la parité des fonctionnalités, les grandes charges de travail peuvent dicter besoin de niveaux supérieurs. Par exemple, [indexation d’intelligence artificielle avec Cognitive Services](cognitive-search-concept-intro.md) a des compétences de longs qui expirent sur un service gratuit, sauf si le jeu de données est faible.

> [!NOTE] 
> L’exception à la parité des fonctionnalités est [indexeurs](search-indexer-overview.md), qui ne sont pas disponible dans S3 HD.
>

Au sein d’un niveau, vous pouvez [ajuster les ressources de réplica et partition](search-capacity-planning.md) pour augmenter ou diminuer la mise à l’échelle. Vous pouvez commencer par un ou deux de chacun d’eux et soumettez une temporairement votre puissance de calcul pour une lourde charge de travail d’indexation. La possibilité d’optimiser les niveaux des ressources ajoute à la flexibilité, mais complique également un peu votre analyse. Vous devrez peut-être faire des essais pour voir si un niveau inférieur avec davantage de ressources/réplicas offre une valeur et des performances à un niveau supérieur avec moins de ressources. Pour en savoir plus sur quand et pourquoi vous devriez ajuster la capacité, consultez [Considérations sur les performances et l’optimisation](search-performance-optimization.md).

## <a name="tiers-for-azure-search"></a>Niveaux pour la recherche Azure

Le tableau suivant répertorie les niveaux disponibles. Vous trouverez plus d’informations sur les différents niveaux sur le [page de tarification](https://azure.microsoft.com/pricing/details/search/), dans le [limites de Service d’Azure Search](search-limits-quotas-capacity.md) l’article et sur le portail de la page lorsque vous êtes un service d’approvisionnement.

|Niveau | Capacité |
|-----|-------------|
|Gratuit | Partagé avec d’autres abonnés. Non évolutives. Limitée à trois index et 50 Mo de stockage. |
|De base | Ressources informatiques dédiées aux charges de production à petite échelle. Une partition de 2 Go et jusqu'à trois réplicas. |
|Standard 1 (S1) | Pour S1 et versions ultérieures, dédiées des ordinateurs avec une capacité de stockage et de traitement plus à chaque niveau. Pour S1, taille de la partition est 25 Go par partition (avec un maximum de 300 Go par service). |
|Standard 2 (S2) | Similaire à S1, mais avec des partitions de 100 Go (et un maximum de 1,2 To par service). |
|Standard 3 (S3) | Partitions de 200 Go (avec un maximum de 2,4 To par service). |
|Standard 3 haute densité (HD S3) | Haute densité est un *mode d’hébergement* pour S3. Le matériel sous-jacent est optimisé pour un grand nombre d’index plus petits et est destiné aux scénarios de l’architecture mutualisées. S3 HD a les mêmes frais par unité comme S3, mais que le matériel est optimisé pour les lectures de fichiers rapide sur un grand nombre d’index plus petits.|
|1 (L1) optimisées pour le stockage | Partitions de 1 To (avec un maximum de 12 To par service). |
|Stockage optimisé 2 (L2) | Partitions de 2 To (avec un maximum de 24 To par service). |

> [!NOTE] 
> Les niveaux de stockage optimisé offrent la plus grande capacité de stockage à un prix inférieur par to que les niveaux Standard. L’inconvénient principal est plus élevée latence de requête, vous devez valider les exigences de votre application.  Pour en savoir plus sur les considérations de performances de cette couche, consultez [considérations sur les performances et optimisation](search-performance-optimization.md).
>

## <a name="how-billing-works"></a>Comment la facturation fonctionne

Il existe trois façons d’entraîner des coûts dans Azure Search, et il existe des composants fixes et variables. Cette section décrit les trois composants de facturation : les coûts de service, les frais de sortie de données et enrichie d’intelligence artificielle de l’indexation de base.

### <a name="core-service-costs-fixed-and-variable"></a>Coûts de service Core (fixes et variables)

Pour le service lui-même, les frais minimum facturés sont la première unité de recherche (partition 1 réplica x 1). Cette valeur minimale est fixe pour la durée de vie du service, car le service ne peut pas s’exécuter sur quoi que ce soit inférieure à cette configuration.

Au-delà de la valeur minimale, vous pouvez ajouter les réplicas et partitions indépendamment. Par exemple, vous pouvez ajouter uniquement des réplicas ou des partitions uniquement. Augmentation de capacité via des réplicas et des partitions d’incrémentielle constituent le composant de coût variable.

La facturation est basée sur un [formule (réplicas x partitions x taux)](#search-units). Le montant qui que vous êtes facturé varie selon le niveau de tarification que vous sélectionnez.

Dans la capture d’écran suivante, le prix unitaire est indiqué pour gratuite, Basic et S1. (S2, S3, L1 et L2 ne sont pas visibles.) Si vous créez un service de base, votre coût mensuel sera moyenne de la valeur qui s’affiche pour *prix-1*. Pour un service Standard, votre coût mensuel sera moyenne de la valeur qui s’affiche pour *prix-2*. Coûts unitaires augmenter pour chaque niveau, car la capacité de calcul d’alimentation et de stockage est supérieure à chaque niveau consécutif. Les tarifs pour Azure Search sont disponibles sur le [page de tarification de Azure Search](https://azure.microsoft.com/pricing/details/search/).

![Prix unitaire](./media/search-sku-tier/per-unit-pricing.png "prix par unité")

Lorsque vous êtes estimation du coût d’une solution de recherche, gardez à l’esprit que la tarification et la capacité ne sont pas linéaire. (Le fait de doubler capacité plus de multiplie par deux le coût.) Pour obtenir un exemple illustrant le fonctionnement de la formule, consultez [comment allouer des réplicas et des partitions](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

#### <a name="billing-based-on-search-units"></a>Facturation selon les unités de recherche

Le concept de facturation plus important à comprendre pour les opérations de recherche Azure est la *unité de recherche* (SU). Comme Recherche Azure dépend à la fois des réplicas et des partitions pour l’indexation et les requêtes, facturer uniquement par rapport à l’un ou à l’autre n’aurait aucun sens. Au lieu de cela, la facturation est basée sur un composite de ces deux facteurs.

SU est le produit de la *réplicas* et *partitions* utilisé par un service : **(R x P = SU)** .

Chaque service commence avec une Unité Recherche (un réplica multiplié par une partition) au minimum. La valeur maximale pour n’importe quel service est de 36 unités de recherche. Ce maximum peut être contacté de plusieurs façons : réplicas de 6 partitions x 6, ou 3 partitions x 12 réplicas, par exemple. Il est courant d’utiliser moins de capacité totale (par exemple, un réplica de 3, 3-partition service facturé comme 9 su). Consultez le [combinaisons de partitions et réplicas](search-capacity-planning.md#chart) graphique pour les combinaisons valides.

Le taux de facturation est toutes les heures par SU. Chaque niveau a un taux progressivement plus élevé. Les niveaux supérieurs sont fournis avec des partitions plus volumineuse et plus rapide, et cela contribue à un taux horaire globalement plus élevé pour ce niveau. Vous pouvez afficher les tarifs de chaque niveau sur le [tarification](https://azure.microsoft.com/pricing/details/search/) page.

La plupart des clients mettent seulement une partie de la capacité totale en ligne et gardent le reste en réserve. Pour la facturation, le nombre de partitions et réplicas vous mettre en ligne, calculée par le SU formule, détermine ce que vous payez sur une base horaire.

### <a name="data-egress-charges-during-indexing"></a>Frais d’acheminement des données lors de l’indexation

À l’aide de [indexeurs Azure Search](search-indexer-overview.md) susceptibles d’affecter la facturation, selon l’emplacement de vos services. Vous pouvez éliminer les frais de sortie de données entièrement si vous créez le service de recherche Azure dans la même région que vos données. Voici quelques informations à partir de la [la bande passante de la page de tarification](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Microsoft ne facture pas pour toutes les données entrantes à n’importe quel service sur Azure, ou pour toutes les données sortantes à partir d’Azure Search.

+ Dans les solutions multiservices, il n’existe aucun frais pour les données qui traversent le câble lorsque tous les services se trouvent dans la même région.

Les frais s’appliquent pour les données sortantes si services se trouvent dans des régions différentes. Ces frais ne sont pas en fait partie de votre facture Azure Search. Ils sont mentionnés ici, car si vous utilisez data ou les indexeurs enrichie d’intelligence artificielle pour extraire les données à partir de différentes régions, vous verrez les coûts reflétées dans votre facture globale.

### <a name="ai-enriched-indexing-with-cognitive-services"></a>L’indexation avec Cognitive Services enrichi en intelligence artificielle

Pour [indexation d’intelligence artificielle avec Cognitive Services](cognitive-search-concept-intro.md), vous devriez prévoir d’attacher une ressource Azure Cognitive Services facturable, dans la même région que recherche Azure, au niveau de tarification S0 pour le traitement de paiement à l’utilisation. Il n’existe aucun coût fixe associé à l’attachement de Cognitive Services. Vous payez uniquement pour le traitement que vous avez besoin.

Extraction d’images au cours de la recherche de document est une charge de la recherche Azure. Il est facturé selon le nombre d’images extraites à partir de vos documents. L’extraction de texte est actuellement gratuite.

Autres enrichissement, comme le traitement en langage naturel, est basées sur [intégrés compétences cognitives](cognitive-search-predefined-skills.md) et facturés conformément à une ressource Cognitive Services. Ils sont facturés à la même vitesse que si vous avez effectué la tâche à l’aide de Cognitive Services directement. Pour plus d’informations, consultez [attacher une ressource Cognitive Services avec les compétences](cognitive-search-attach-cognitive-services.md).

<a name="search-units"></a>

#### <a name="billing-for-image-extraction-in-cognitive-search"></a>Facturation de l’extraction d’image dans la recherche cognitive

Si vous extrayez des images à partir de fichiers dans une pipeline d’indexation de recherche cognitive, vous êtes facturé pour cette opération dans votre facture Azure Search. Dans un [configuration de l’indexeur](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters), **imageAction** est le paramètre qui déclenche l’extraction d’images. Si **imageAction** a la valeur « none » (la valeur par défaut), vous ne serez pas facturé pour l’extraction de l’image.

La tarification est susceptible de changer. Elle est documentée sur le [tarification](https://azure.microsoft.com/pricing/details/search/) page pour la recherche Azure.

#### <a name="billing-for-built-in-skills-in-cognitive-search"></a>Facturation des compétences intégrées à la recherche cognitive

Lorsque vous configurez un pipeline d’enrichissement, toute [compétence intégrée](cognitive-search-predefined-skills.md) utilisée dans le pipeline s’appuie sur des modèles Machine Learning. Ces modèles sont fournis par Cognitive Services. Si vous utilisez ces modèles lors de l’indexation, vous serez facturé au même tarif comme vous le serait si vous avez demandé la ressource directement.

Par exemple, supposons que vous disposez d’un pipeline qui utilise la reconnaissance optique de caractères (OCR) par rapport à des fichiers JPEG analysés et le texte résultant est placé dans un index Azure Search pour les requêtes de recherche de forme libre. Votre pipeline d’indexation inclurait un indexeur avec la [compétence de reconnaissance optique de caractères](cognitive-search-skill-ocr.md), et cette compétence serait [associée à une ressource Cognitive Services](cognitive-search-attach-cognitive-services.md). Lorsque vous exécutez l’indexeur, les frais pour l’exécution de la reconnaissance optique de caractères seront affiche sur votre facture de ressources Cognitive.

## <a name="tips-for-reducing-costs"></a>Conseils de réduction des coûts

Vous ne pouvez pas arrêter le service afin de réduire votre facture. Des ressources dédiées sont toujours opérationnels, allouée pour votre usage exclusif pour la durée de vie de votre service. La seule façon de baisser votre facture est de réduire les réplicas et des partitions à un niveau qui fournit toujours des performances acceptables et [conformité SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Une pour réduire les coûts consiste à choisir un niveau à un taux horaire inférieur. Le taux horaire S1 est inférieur aux taux S2 ou S3. En supposant que vous configurez votre service à l’extrémité inférieure de votre charge de projections, si vous dépassent le service, vous pouvez créer un deuxième service supérieure à plusieurs niveaux, reconstruire vos index sur le deuxième service et puis supprimer le.

Si vous avez effectué la planification de capacité pour les serveurs locaux, vous savez qu’il est commun à « acheter inscrire » afin que vous pouvez gérer la croissance prévue. Avec un service cloud, vous pouvez adopter des économies plus agressive, car vous n’êtes pas obligé d’à un achat. Vous pouvez toujours passer à un service de niveau supérieur si l’objet actuel n’est pas suffisante.

### <a name="capacity"></a>Capacité

Dans Recherche Azure, la capacité est structurée sous forme de *réplicas* et de *partitions*.

+ Les réplicas sont des instances du service de recherche. Chaque réplica héberge une copie à charge équilibrée d’un index. Par exemple, un service avec six réplicas a six copies de chaque index chargées dans le service.

+ Partitions de stocker les index et fractionnent automatiquement les données interrogeables. Deux partitions fractionnement votre index en deux, trois partitions scindez-le en tiers et ainsi de suite. En termes de capacité, *taille de partition* est la fonctionnalité principale de différenciation entre les niveaux.

> [!NOTE]
> Tous les niveaux Standard et optimisées pour le stockage prennent en charge [combinaisons flexibles de partitions et réplicas](search-capacity-planning.md#chart) afin que vous puissiez [optimiser votre système de stockage ou de vitesse](search-performance-optimization.md) en modifiant le solde. Le niveau de base offre jusqu'à trois réplicas pour la haute disponibilité, mais a qu’une seule partition. Niveaux gratuits ne fournissent pas des ressources dédiées : calcul de ressources sont partagées par plusieurs abonnés.

### <a name="more-about-service-limits"></a>En savoir plus sur les limites de service

Services de ressources hôtes telles que des index et des indexeurs. Chaque niveau impose [limites de service](search-limits-quotas-capacity.md) sur le nombre de ressources, vous pouvez créer. Par conséquent, le nombre maximal d’index (et autres objets) est la deuxième fonctionnalité de différenciation entre les niveaux. Lorsque vous examinez chaque option dans le portail, notez les limites sur le nombre d’index. Autres ressources, telles que les indexeurs, sources de données et des compétences, sont apposées sur les limites d’index.

## <a name="consumption-patterns"></a>Modèles de consommation

La plupart des clients démarrer avec le service gratuit, qu’ils conservent indéfiniment, puis choisissez un des niveaux Standard ou stockage optimisé pour les charges de travail de développement ou de production graves.

![Recherche Azure niveaux tarifaires](./media/search-sku-tier/tiers.png "Azure Search niveaux tarifaires")

Aux extrémités supérieure et inférieure, Basic et S3 HD sont pour les modèles de consommation importante mais inhabituels. Basic concerne les charges de travail de production de petite taille. Il offre des contrats SLA, des ressources dédiées et haute disponibilité, mais il fournit un stockage modeste, plafonnait à un total de 2 Go. Ce niveau a été conçu pour les clients qui régulièrement sous-utilisent capacité disponible. Dans la partie supérieure, S3 HD est pour les charges de travail typiques des éditeurs de logiciels, partenaires, [solutions mutualisées](search-modeling-multitenant-saas-applications.md), ou toute configuration qui appelle pour un grand nombre de petits index. Il est souvent clair lors de la base ou S3 HD est le bon niveau. Si vous souhaitez confirmation, vous pouvez publier sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [contacter le support Azure](https://azure.microsoft.com/support/options/) pour obtenir des conseils.

Les niveaux standards plus couramment utilisés, S1 à S3, constituent une progression de l’augmentation des niveaux de capacité. Il existe des points d’inflexion sur la taille de la partition et les limites sur les numéros d’index, des indexeurs et des ressources cela entraînerait :

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Taille de la partition|  25 Go | 100 Go | 200 Go |  |  |  |  |
| limites d’index et d’indexeur| 50 | 200 | 200 |  |  |  |  |

S1 est un choix courant pour les clients qui ont besoin des ressources dédiées et plusieurs partitions. S1 offre des partitions de 25 Go et jusqu'à 12 partitions, en fournissant une limite par service de 300 Go si vous agrandissez partitions sur les réplicas. (Consultez [allouer des partitions et réplicas](search-capacity-planning.md#chart) pour en savoir plus les allocations à charge équilibrée.)

Les pages de portail et de tarification placer le focus sur le stockage et la taille de partition, mais, pour chaque niveau, toutes les fonctionnalités (capacité de disque, vitesse, unités centrales) de calcul généralement augmente de façon linéaire avec le prix. Un réplica de S2 est plus rapide que S1 et S3 est plus rapide que S2. Les niveaux S3 rompre le modèle de tarification de calcul linéaire avec proportionnellement plus rapide d’e/s. Si vous prévoyez d’e/s pour constituer le goulot d’étranglement, n’oubliez pas que vous pouvez obtenir beaucoup plus d’IOPS avec le niveau S3 que vous pouvez obtenir avec les niveaux inférieurs.

S3 et S3 HD sont soutenus par des infrastructure haute capacité identique, mais ils atteignent leurs limites maximales de différentes façons. S3 permet de cibler un plus petit nombre d’index très volumineux, par conséquent, sa limite maximale est liée à la ressource (2,4 To pour chaque service). S3 HD cible un grand nombre de très petits index. Avec 1 000 index, S3 HD atteint ses limites sous la forme de contraintes d’index. Si vous êtes un client S3 HD et que vous avez besoin de plus de 1 000 index, contactez le Support Microsoft pour plus d’informations sur la marche à suivre.

> [!NOTE]
> Limites de document ont été une considération en même temps, mais ils ne sont plus applicables pour les nouveaux services. Pour plus d’informations sur les conditions dans lesquelles les limites de document s’appliquent toujours, consultez [Document limites](search-limits-quotas-capacity.md#document-limits).
>

Niveaux de stockage optimisé, L1 et L2, sont idéales pour les applications avec les exigences de données volumineuses, mais un nombre relativement faible d’utilisateurs finaux, quand en réduisant la latence de requête de n’est pas la priorité absolue.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Taille de la partition|  1 To | 2 To |  |  |  |  |  |
| limites d’index et d’indexeur| 10 | 10 |  |  |  |  |  |

L2 offre deux fois la capacité de stockage globale de L1.  Choisir votre niveau selon la quantité maximale de données que vous pensez que votre index a besoin. Les partitions de niveau L1 monter en puissance par incréments de 1 To à un maximum de 12 To. Les partitions L2 augmenter en 2 To par partition jusqu'à un maximum de 24 To.

## <a name="evaluating-capacity"></a>L’évaluation de capacité

Capacité et les coûts d’exécution du service sont directement liés. Niveaux imposent des limites à deux niveaux : stockage et des ressources. Vous devez penser à la fois, car selon la limite que vous atteignez le premier est la limite effective.

Exigences commerciales imposent généralement le nombre d’index que vous avez besoin. Par exemple, vous devrez peut-être un index global pour un référentiel de documents volumineux. Ou bien, vous devrez peut-être plusieurs index en fonction de la région, une application ou niche de l’entreprise.

Pour déterminer la taille d’un index, vous devez en [créer un](search-create-index-portal.md). La structure de données dans Azure Search est principalement un [index inversé](https://en.wikipedia.org/wiki/Inverted_index) structure, qui possède des caractéristiques différentes de celles données sources. Pour un index inversé, taille et la complexité sont déterminées par la quantité de données qui vous alimentez par le contenu, mais pas nécessairement. Une source de données de grande taille avec redondance élevée peut entraîner un index plus petits qu’un jeu de données plus petit qui contient le contenu très variable. Par conséquent, il est rarement possible de déduire la taille de l’index selon la taille du jeu de données d’origine.

> [!NOTE] 
> Bien que l’estimation des besoins futurs pour les index et le stockage peut sembler un conjectures, il est intéressant de réaliser. Si la capacité d’un niveau s’avère pour être trop faible, vous devrez configurer un nouveau service à un niveau supérieur, puis [recharger vos index](search-howto-reindex.md). Il n’existe aucune mise à niveau sur place d’un service à partir d’une référence à un autre.
>

### <a name="step-1-develop-rough-estimates-by-using-the-free-tier"></a>Étape 1 : Développer des estimations approximatives à l’aide du niveau gratuit

Une approche pour l’estimation de la capacité consiste à démarrer avec le niveau gratuit. N’oubliez pas que le service gratuit offre jusqu'à trois index, 50 Mo de stockage et de 2 minutes du moment de l’indexation. Il peut être difficile à estimer une taille d’index projetée avec ces contraintes. Voici une approche que vous pouvez effectuer :

+ [Créer un service gratuit](search-create-service-portal.md).
+ Préparer un jeu de données petit, représentatif (par exemple, 5 000 documents et taille d’échantillon de 10 pour cent).
+ [Créer un index initial](search-create-index-portal.md) et notez sa taille dans le portail (par exemple, 30 Mo).

Si l’échantillon est représentatif et 10 pour cent de la source de données entier, un index de 30 Mo devient environ 300 Mo si tous les documents sont indexés. Grâce à ce nombre préliminaire, vous pouvez double de cette quantité budget pour les deux index (développement et production). Cela vous donne un total de 600 Mo des besoins de stockage. Cette exigence est satisfaite facilement le niveau de base, afin de vous commencerez il.

### <a name="step-2-develop-refined-estimates-by-using-a-billable-tier"></a>Étape 2 : Développer des estimations affinées à l’aide d’un niveau facturable

Certains clients préfèrent démarrer avec des ressources dédiées qui peuvent prendre en charge d’échantillonnage plus volumineux et les temps de traitement et ensuite développer des estimations réalistes des volumes de requêtes, la taille et la quantité d’index au cours du développement. Initialement, un service est configuré selon une estimation meilleure hypothèse. Ensuite, comme le projet de développement arrive à maturité, équipes savent généralement si le service existant est au-dessus ou au-dessous de capacité pour les charges de travail de production prévue.

1. [Passez en revue les limites du service à chaque niveau](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) pour déterminer si les niveaux inférieurs peuvent prendre en charge le nombre d’index dont vous avez besoin. Au sein des niveaux de base, S1 et S2, limites d’index sont 15, 50 et 200, respectivement. Le niveau de stockage optimisé a une limite de 10 index car il est conçu pour prendre en charge un nombre faible de très grands index.

1. [Créez un service à un niveau facturable](search-create-service-portal.md) :

    + Démarrer une faible, au base ou S1, si vous êtes au début de votre courbe d’apprentissage.
    + Démarrez élevée, S2 ou S3 de même, si vous savez que vous allez confrontées à des charges de requête et d’indexation à grande échelle.
    + Démarrer avec stockage optimisé, au L1 ou L2, si vous indexez une grande quantité de données et la charge de requête est relativement faible, comme avec une application métier interne.

1. [Générez un index initial](search-create-index-portal.md) pour déterminer comment les données sources se traduisent par un index. C’est l’unique façon d’estimer la taille de l’index.

1. [Surveillez le stockage, les limites de service, le volume de requêtes et la latence](search-monitor-usage.md) dans le portail. Le portail affiche les requêtes par secondes requêtes limitées et latence de recherche. Toutes ces valeurs peuvent vous aider à décider si vous avez sélectionné le bon niveau. Vous pouvez également configurer une surveillance complète de valeurs, telles que l’analyse des rapports générés interactifs en activant [analytique du trafic de recherche](search-traffic-analytics.md).

Numéro d’index et de taille sont tout aussi importants pour votre analyse. Il s’agit, car les limites maximales sont atteintes via la pleine utilisation du stockage (partitions) ou par les limites maximales sur les ressources (index, indexeurs et ainsi de suite), le premier prévalant. Le portail vous aide à effectuer le suivi de ces deux facteurs en affichant l’utilisation actuelle et les limites maximales côte à côte dans la page de présentation.

> [!NOTE]
> Exigences de stockage peuvent être agrandies si les documents contiennent des données superflues. Dans l’idéal, les documents contiennent uniquement les données dont vous avez besoin pour l’expérience de recherche. Données binaires n’est pas disponible pour la recherche et doivent être stockées séparément (peut-être dans un stockage de table ou un objet blob Azure). Un champ doit ensuite être ajouté à l’index pour conserver une référence URL aux données externes. La taille maximale d’un document individuel est de 16 Mo (ou moins si vous avez plusieurs documents en une seule demande de chargement en bloc). Pour plus d’informations, consultez [Limites de service d’Azure Search](search-limits-quotas-capacity.md).
>

**Considérations relatives au volume de requêtes**

Requêtes par seconde (RPS) est un critère important pendant le paramétrage des performances, mais il est généralement uniquement en considération de la couche si vous prévoyez un important volume de requêtes dès le départ.

Les niveaux Standard peuvent fournir un équilibre des réplicas et des partitions. Vous pouvez augmenter le temps de réponse de requête en ajoutant des réplicas pour l’équilibrage de charge ou ajouter des partitions pour un traitement parallèle. Vous pouvez ensuite paramétrer pour des performances une fois que le service est configuré.

Si vous prévoyez des volumes soutenus élevées pour les requêtes dès le départ, vous devez envisager les niveaux supérieurs Standard, assorties de matériel plus puissant. Vous pouvez ensuite hors connexion partitions et réplicas, ou encore basculer vers un service de niveau inférieur, si ces volumes de requête ne se produisent. Pour plus d’informations sur la façon de calculer le débit de requête, consultez [Performances et optimisation de Recherche Azure](search-performance-optimization.md).

Les niveaux de stockage optimisé sont utiles pour les charges de travail de données volumineuses, prenant en charge plus de stockage total disponible index pour lorsque les conditions de latence de requête sont moins importantes. Vous devez toujours utiliser des réplicas supplémentaires pour les partitions de supplémentaires et équilibrage de charge pour un traitement parallèle. Vous pouvez ensuite paramétrer pour des performances une fois que le service est configuré.

**Contrats de niveau de service**

Ne fournissent pas les fonctionnalités de niveau et l’aperçu gratuit [niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Pour tous les niveaux facturables, les SLA entrent en vigueur lorsque vous approvisionnez une redondance suffisante pour votre service. Vous devez disposer de deux ou plusieurs réplicas pour SLA de requête (lecture). Vous devez disposer d’au moins trois réplicas pour interroger et indexer des contrats SLA (en lecture-écriture). Le nombre de partitions n’affecte les contrats SLA.

## <a name="tips-for-tier-evaluation"></a>Conseils pour l’évaluation du niveau

+ Apprenez à créer des index efficaces et découvrez les méthodes d’actualisation ont le moins d’impact. Utilisez [analytique du trafic de recherche](search-traffic-analytics.md) pour obtenir des informations sur l’activité des requêtes.

+ Métriques générer autour de requêtes et de collecter des données relatives à des modèles d’utilisation (requêtes pendant les heures ouvrables, pendant les heures creuses d’indexation). Utilisez ces données pour informer les décisions de configuration de service. S’il n’est pas pratique à une cadence horaire ou quotidien, vous pouvez ajuster dynamiquement les partitions et les ressources pour prendre en compte les modifications planifiées dans des volumes de requêtes. Vous pouvez également satisfaire des modifications non planifiées mais maintenues si niveaux contiennent suffisamment longue pour justifier de prendre des mesures.

+ N’oubliez pas que le seul inconvénient d’underprovisioning est que vous devrez peut-être détruire un service si la configuration requise est supérieure à vos prévisions. Pour éviter toute interruption de service, vous devrez créer un service dans le même abonnement à un niveau supérieur, et l’exécuter côte à côte jusqu’à ce que toutes les applications et requêtes ciblent le nouveau point de terminaison.

## <a name="next-steps"></a>Étapes suivantes

Démarrez avec un niveau gratuit et créer un index initial à l’aide d’un sous-ensemble de vos données pour comprendre ses caractéristiques. La structure de données dans Azure Search est une structure d’index inversé. La taille et la complexité d’un index inversé est déterminée par le contenu. Souvenez-vous que le contenu hautement redondant a tendance à générer un index plus petit que le contenu très irrégulier. Caractéristiques de contenu par conséquent, plutôt que la taille du jeu de données détermine les conditions de stockage d’index.

Une fois que vous avez une estimation initiale de la taille de votre index, [approvisionner un service facturable](search-create-service-portal.md) sur l’un des niveaux abordés dans cet article : Basic, Standard ou optimisé pour le stockage. Assouplir toute contrainte artificielle sur le dimensionnement des données et [reconstruire votre index](search-howto-reindex.md) pour inclure toutes les données que vous souhaitez être disponible pour la recherche.

[Allouez des partitions et des réplicas](search-capacity-planning.md) en fonction des besoins, afin d’obtenir les performances et l’échelle requises.

Si les performances et la capacité conviennent, vous avez terminé. Dans le cas contraire, recréez un service de recherche à un autre niveau correspondant mieux à vos besoins.

> [!NOTE]
> Si vous avez des questions, publiez sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [contacter le support Azure](https://azure.microsoft.com/support/options/).
