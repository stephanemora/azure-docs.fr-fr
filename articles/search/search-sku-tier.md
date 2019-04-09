---
title: Choisir un niveau tarifaire ou une référence SKU pour le service Recherche Azure | Recherche Azure
description: 'Le service Recherche Azure peut être configuré sur les références SKU suivantes : Gratuit, De base et Standard, sachant que la référence Standard est disponible sous différentes configurations de ressources et différents niveaux de capacité.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: da8c8adacfead598a8dec6280cf3518fb7b31f49
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270945"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Choisir un niveau tarifaire pour Recherche Azure

Dans Recherche Azure, une [ressource est créée](search-create-service-portal.md) à un niveau tarifaire ou une référence SKU qui est fixe pour la durée de vie du service. Les niveaux incluent **gratuit**, **base**, **Standard**, ou **stockage optimisé**.  **Standard** et **stockage optimisé** sont disponibles dans plusieurs configurations et capacités. 

La plupart des clients démarrent avec le **gratuit** puis passer à un des niveaux payants plus élevées pour les déploiements de développement et de production et de niveau pour l’évaluation. Au niveau **Gratuit**, vous pouvez effectuer tous les démarrages rapides et suivre tous les tutoriels, notamment ceux qui concernent la recherche cognitive nécessitant de nombreuses ressources.

> [!NOTE]
> Les niveaux de service de stockage optimisé sont actuellement disponibles en version préliminaire au tarif réduit à des fins de test et d’expérimentation dans le but de recueillir des commentaires. Le prix final sera annoncé ultérieurement lorsque ces niveaux sont généralement disponibles. Nous vous déconseillons l’utilisation de ces niveaux pour les applications de production.

Les niveaux reflètent les caractéristiques du matériel qui héberge le service (plutôt que les fonctionnalités) et se distinguent en fonction de ce qui suit :

+ Nombre d’index que vous pouvez créer
+ Taille et la vitesse des partitions (stockage physique)

Bien que tous les niveaux, y compris le niveau **Gratuit**, offrent généralement la parité des fonctionnalités, de plus grandes charges de travail peuvent exiger des niveaux supérieurs. Par exemple, [l’indexation l’intelligence artificielle avec Cognitive Services](cognitive-search-concept-intro.md) a des compétences de longs qui expirent sur un service gratuit, sauf si le jeu de données se trouve être petit.

> [!NOTE] 
> Les [indexeurs](search-indexer-overview.md), qui ne sont pas disponibles sur S3HD, sont une exception à la parité des fonctionnalités.
>

Au sein d’un niveau, vous pouvez [ajuster les ressources de réplica et partition](search-capacity-planning.md) pour augmenter ou diminuer la mise à l’échelle. Vous pouvez commencer par un ou deux de chacun d’eux et soumettez une temporairement votre puissance de calcul pour une lourde charge de travail d’indexation. La possibilité d’optimiser les niveaux des ressources ajoute à la flexibilité, mais complique également un peu votre analyse. Vous devrez peut-être faire des essais pour voir si un niveau inférieur avec plus de ressources/réplicas offre de meilleures valeurs et performances qu’un niveau supérieur avec moins de ressources. Pour en savoir plus sur quand et pourquoi vous devriez ajuster la capacité, consultez [Considérations sur les performances et l’optimisation](search-performance-optimization.md).

## <a name="tiers-for-azure-search"></a>Niveaux pour la recherche Azure

Le tableau suivant répertorie les niveaux disponibles. Incluent d’autres sources d’informations de niveau le [page de tarification](https://azure.microsoft.com/pricing/details/search/), [service et les limites données](search-limits-quotas-capacity.md)et la page du portail lors de la configuration d’un service.

|Niveau | Capacité |
|-----|-------------|
|Gratuit | Partagé avec d’autres abonnés. Non évolutif, limité à 3 index et de stockage de 50 Mo. |
|De base | Ressources informatiques dédiées aux charges de production à petite échelle. Une partition de 2 Go et jusqu'à trois réplicas. |
|Standard 1 (S1) | À partir de S1 sur des ordinateurs dédiés avec une capacité de stockage et de traitement plus à chaque niveau. Taille de la partition est 25 Go par partition (limite de 300 Go par service) pour S1. |
|Standard 2 (S2) | Semblable à S1, mais avec 100 Go/partitions (max 1,2 To par service) |
|Standard 3 (S3) | 200 Go par partition (max 2,4 To par service) |
|Standard 3 haute densité (S3-HD) | Haute densité est un *mode d’hébergement* pour S3. Le matériel sous-jacent est optimisé pour un grand nombre d’index plus petits, destinées aux scénarios de l’architecture mutualisées. S3 HD a les mêmes frais par unité comme S3, mais le matériel est optimisé pour les lectures de fichiers rapide sur un grand nombre d’index plus petits.|
|1 (L1) optimisées pour le stockage | 1 to/partition (au maximum 12 To par service) |
|Stockage optimisé 2 (L2) | 2 To par partition (maximum 24 To par service) |

> [!NOTE] 
> Les niveaux de stockage optimisé offrent la plus grande capacité de stockage à un prix inférieur par to que les niveaux Standard. L’inconvénient principal est plus élevée latence de requête, vous devez valider les exigences de votre application.  Pour en savoir plus sur les performances de cette couche, consultez [considérations sur les performances et optimisation](search-performance-optimization.md).
>

## <a name="how-billing-works"></a>Comment la facturation fonctionne

Dans Azure Search, il existe trois façons d’entraîner des coûts dans Azure Search, et il existe des composants fixes et variables. Cette section examine chaque composant facturation à son tour.

### <a name="1-core-service-costs-fixed-and-variable"></a>1. Coûts de service Core (fixes et variables)

Pour le service lui-même, les frais minimum facturés sont la première unité de recherche (partition 1 réplica x 1), et ce montant constant pour la durée de vie du service, car le service ne peut pas s’exécuter sur quoi que ce soit inférieure à cette configuration. 

Dans la capture d’écran suivante, par le prix d’unité indiqué pour gratuite, Basic et S1 (S2, S3, L1 et L2 ne sont pas affichées). Si vous avez créé un **base**, **Standard**, ou **stockage optimisé** service, votre coût mensuel est moyenne de la valeur qui s’affiche pour *prix-1*et *prix-2* respectivement. Coûts unitaires go pour chaque niveau, car la capacité de calcul d’alimentation et de stockage est supérieure à chaque niveau consécutif.

![Par prix d’unité](./media/search-sku-tier/per-unit-pricing.png "par prix d’unité")

Partitions et réplicas supplémentaires sont un module complémentaire à la prise en charge. Un service de recherche nécessite un réplica et une partition par conséquent, la configuration minimale est un de chacun d’eux. Au-delà de la valeur minimale, vous ajoutez indépendamment de réplicas et partitions. Par exemple, vous pouvez ajouter uniquement des réplicas ou des partitions uniquement. 

Partitions et réplicas supplémentaires sont facturées selon un [formule](#search-units). Les coûts ne sont pas linéaires (le fait de doubler capacité plus que le coût de doubles). Pour obtenir un exemple illustrant le fonctionnement de la formule, consultez [« Comment allouer des partitions et réplicas »](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="2-data-egress-charges-during-indexing"></a>2. Frais d’acheminement des données lors de l’indexation

Utilisation de [indexeurs Azure Search](search-indexer-overview.md) peut entraîner l’impact selon où se trouvent les services de facturation. Vous pouvez éliminer les frais de sortie de données entièrement si vous créez le service de recherche Azure dans la même région que vos données.

+ Aucun frais pour toutes les données entrantes à n’importe quel service sur Azure.

+ Aucun frais pour les données sortantes à partir d’Azure Search.

+ Aucun frais pour les fichiers de données ou sortants à partir de la base de données SQL, Cosmos, stockage d’objets Blob (trafic entrant à recherche Azure), que tous les services sont dans la même région.

+ Les frais s’appliquent pour les fichiers ou les données sortantes si le stockage et recherche Azure se trouvent dans différentes régions.

Lors du routage des données entre les régions Azure, vous obtiendrez les frais de bande passante sur la facture pour ces ressources. Ces frais ne font pas partie de votre facture Azure Search, mais elles sont mentionnées ici, car si vous utilisez des indexeurs pour extraire des données ou des fichiers sur le réseau, vous verrez que frais dans votre facture globale.

Si vous n’utilisez pas d’indexeurs, il n’existe aucun frais de bande passante. 

### <a name="3-ai-enriched-indexing-using-cognitive-services"></a>3. L’indexation à l’aide de Cognitive Services enrichi en intelligence artificielle

Pour [l’indexation l’intelligence artificielle avec Cognitive Services](cognitive-search-concept-intro.md) seule, l’extraction d’images au cours de la recherche de document est facturée en fonction du nombre d’images extraites à partir de vos documents. L’extraction de texte est actuellement gratuite. Autres enrichissement, telles que le traitement du langage naturel, est basées sur [intégrés compétences cognitives](cognitive-search-predefined-skills.md) sont facturés conformément à une ressource Cognitive Services. Les enrichissements sont facturés au même tarif que si vous aviez effectué la tâche directement avec Cognitive Services.

<a name="search-units"></a>

### <a name="billing-based-on-search-units"></a>Facturation selon les unités de recherche

Pour les opérations de Recherche Azure, la notion de facturation la plus importante à saisir est l’*Unité Recherche* (SU, Search Unit). Comme Recherche Azure dépend à la fois des réplicas et des partitions pour l’indexation et les requêtes, facturer uniquement par rapport à l’un ou à l’autre n’aurait aucun sens. Au lieu de cela, la facturation est basée sur un composite de ces deux facteurs. 

L’unité de recherche est le produit des *réplicas* et des *partitions* utilisés par un service : **`(R X P = SU)`**

Chaque service commence avec une Unité Recherche (un réplica multiplié par une partition) au minimum. Quel que soit le service, le maximum est de 36 unités de recherche, ce nombre pouvant être atteint de différentes manières : 6 partitions x 6 réplicas, ou 3 partitions x 12 réplicas, par exemple. Il est courant de ne pas utiliser la capacité totale. Par exemple, un service de 3 réplicas et 3 partitions, facturé comme 9 unités de recherche. Vous pouvez consulter [ce graphique](search-capacity-planning.md#chart) pour afficher les combinaisons valides en un clin de œil.

Le taux de facturation est un **taux horaire par unité de recherche**, chaque niveau ayant un taux qui augmente progressivement. Les niveaux supérieurs sont fournis avec des partitions plus volumineuses et plus rapides, contribuant à un taux horaire globalement plus élevé pour ces niveaux. Consultez la [Tarification](https://azure.microsoft.com/pricing/details/search/)pour connaître les coûts pour chaque niveau. 

La plupart des clients mettent seulement une partie de la capacité totale en ligne et gardent le reste en réserve. En termes de facturation, c’est le nombre de partitions et de réplicas que vous mettez en ligne, calculé à l’aide de la formule d’unité de recherche (SU), qui détermine le taux horaire que vous payez réellement.

### <a name="billing-for-image-extraction-in-cognitive-search"></a>Facturation de l’extraction d’image dans la recherche cognitive

Si vous extrayez des images à partir de fichiers dans un pipeline d’indexation de recherche cognitive, vous êtes facturé pour cette opération dans votre facture Recherche Azure. Le paramètre qui déclenche l’extraction d’image est **imageAction** dans une [configuration d’indexeur](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters). Si **imageAction** est défini sur « none » (aucune, la valeur par défaut), aucuns frais ne sont imputés pour l’extraction d’image.

Les tarifs sont susceptibles de changer, mais sont toujours dans la page [Détails des tarifs](https://azure.microsoft.com/pricing/details/search/) de Recherche Azure. 

### <a name="billing-for-built-in-skills-in-cognitive-search"></a>Facturation des compétences intégrées à la recherche cognitive

Lorsque vous configurez un pipeline d’enrichissement, toute [compétence intégrée](cognitive-search-predefined-skills.md) utilisée dans le pipeline s’appuie sur des modèles Machine Learning. Ces modèles sont fournis par Cognitive Services. L’utilisation de ces modèles lors de l’indexation est facturée au même tarif que si vous aviez demandé la ressource directement.

Par exemple, supposez un pipeline constitué de la reconnaissance optique de caractères (OCR) sur des fichiers JPEG d’image numérisée, où le texte résultant est envoyé dans un index Recherche Azure pour des requêtes de recherche de forme libre. Votre pipeline d’indexation inclurait un indexeur avec la [compétence de reconnaissance optique de caractères](cognitive-search-skill-ocr.md), et cette compétence serait [associée à une ressource Cognitive Services](cognitive-search-attach-cognitive-services.md). Lorsque vous exécutez l’indexeur, des frais sont imputés sur votre facture Ressources cognitives pour l’exécution de la reconnaissance optique des caractères.

## <a name="tips-for-reducing-costs"></a>Conseils de réduction des coûts

Vous ne pouvez pas arrêter le service pour réduire la facture. Les ressources dédiées sont opérationnelles 24h/24, 7j/7, allouées pour votre usage exclusif pendant pour la durée de vie de votre service. La seule façon de réduire une facture est de réduire les réplicas et les partitions à un faible niveau fournissant toujours des performances acceptables et la [conformité SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Un levier de réduction des coûts est choix d’un niveau à taux horaire inférieur. Le taux horaire S1 est inférieur aux taux S2 ou S3. En supposant que vous provisionnez un service destiné à la limite inférieure de vos prévisions de charge, si vous dépassez le service, vous pouvez créer un second service de niveau supérieur, reconstruire vos index sur ce second service, puis supprimer le premier. 

Si vous avez planifié la capacité des serveurs sur place, vous savez qu'il est courant de « racheter » pour pouvoir faire face à la croissance prévue. Mais avec un service cloud, vous pouvez réaliser des économies beaucoup plus substantielles, parce que vous n'êtes pas lié à un achat spécifique. Vous pouvez toujours passer à un service de niveau supérieur si le service actuel est insuffisant.

### <a name="capacity-drill-down"></a>Exploration hiérarchique des capacités

Dans Recherche Azure, la capacité est structurée sous forme de *réplicas* et de *partitions*. 

+ Les réplicas sont des instances du service de recherche, chaque réplica hébergeant une copie avec équilibrage de charge d’un index. Par exemple, un service avec six réplicas a six copies de chaque index chargé dans le service. 

+ Les partitions stockent des index et fractionnent automatiquement les données de recherche : deux partitions fractionnent votre index en deux, trois partitions en trois, et ainsi de suite. En termes de capacité, la *taille de partition* est la principale caractéristique qui différencie les niveaux.

> [!NOTE]
> Tous les **Standard** et **stockage optimisé** niveaux de prise en charge [réplica de combinaisons flexibles et des partitions](search-capacity-planning.md#chart) afin que vous puissiez [d’épaisseur de la vitesse de votre système ou stockage](search-performance-optimization.md) en modifiant le solde. Le niveau **De base** offre jusqu’à trois réplicas pour la haute disponibilité, mais une seule partition. Les niveaux **Gratuit** ne fournissent pas de ressources dédiées : les ressources de calcul sont partagées par plusieurs services gratuits.

### <a name="more-about-service-limits"></a>En savoir plus sur les limites de service

Les services hébergent des ressources, telles que des index, des indexeurs, et ainsi de suite. Chaque niveau impose des [limites de service](search-limits-quotas-capacity.md) quant à la quantité de ressources que vous pouvez créer. Ainsi, le plafond du nombre d’index (et d’autres objets) est la deuxième caractéristique qui différencie les niveaux. À mesure que vous consultez chaque option dans le portail, notez les limites du nombre d’index. D’autres ressources, telles que les indexeurs, sources de données et compétences, sont soumises à des limites d’index.

## <a name="consumption-patterns"></a>Modèles de consommation

La plupart des clients démarrent avec le **gratuit** de service, lequel ils conservent indéfiniment, puis choisissez une de la **Standard** ou **stockage optimisé** niveaux pour le développement sérieux ou charges de travail de production. 

![Niveaux de Recherche Azure](./media/search-sku-tier/tiers.png "Niveaux tarifaires de Recherche Azure")

À chaque extrémité, **De base** et **S3 HD** existent pour les modèles de consommation importants mais atypiques. Le niveau **De base** convient aux charges de travail de production de petite taille : il offre un contrat SLA, des ressources dédiées, une haute disponibilité, mais un stockage modeste de 2 Go maximum. Ce niveau a été conçu pour les clients qui sous-utilisent régulièrement la capacité disponible. À l’autre extrémité, le niveau **S3 HD** convient aux charges de travail typiques des éditeurs de logiciels indépendants, des partenaires, des [solutions multi-locataires](search-modeling-multitenant-saas-applications.md) ou à toute configuration exigeant un grand nombre de petits index. Le choix entre les niveaux **De base** et **S3 HD** est souvent évident, mais si vous souhaitez une confirmation vous pouvez publier un message sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [contacter le Support Azure](https://azure.microsoft.com/support/options/) afin d’obtenir des instructions.

Quant aux niveaux Standard plus courants, **S1-S3** offrent des niveaux de capacité progressifs, avec des points d’inflexion sur la taille de partition et des valeurs maximales en ce qui concerne le nombre d’index, d’indexeurs et de ressources corollaires :

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Taille de partition|  25 Go | 100 Go | 200 Go |  |  |  |  |
| Limites d’index et d’indexeur| 50 | 200 | 200 |  |  |  |  |

**S1** est un choix courant quand les ressources dédiées et les partitions multiples deviennent une nécessité. Avec des partitions de 25 Go pour 12 partitions maximum, la limite par service avec **S1** est de 300 Go au total si vous optimisez les partitions sur les réplicas (pour obtenir des compositions plus équilibrées, consultez [Allouer des partitions et des réplicas](search-capacity-planning.md#chart)).

Les pages de portail et de tarification mettent l’accent sur le stockage et la taille de partition, mais pour chaque niveau toutes les caractéristiques de calcul (capacité du disque, vitesse, unités centrales) augmentent de manière linéaire avec le prix. Un réplica **S2** est plus rapide que **S1**, et **S3** est plus rapide que **S2**. Les niveaux **S3** ne respectent pas le modèle de tarification de calcul généralement linéaire ; ils offrent des E/S disproportionnellement plus rapides. Si vous prévoyez que les E/S constitueront le goulot d’étranglement, **S3** vous offrira davantage d’IOPS que les niveaux inférieurs.

Les niveaux **S3** et **S3 HD** sont soutenus par une infrastructure haute capacité identique, mais chacun d’eux atteint sa limite maximale de différentes façons. **S3** cible un plus petit nombre d’index très volumineux. Par conséquent, sa limite maximale est liée à la ressource (2,4 To pour chaque service). **S3 HD** cible un grand nombre de très petits index. Avec 1 000 index, **S3 HD** atteint ses limites sous la forme de contraintes d’index. Si vous êtes un client **S3 HD** qui nécessite plus de 1 000 index, contactez le Support Microsoft pour obtenir plus d’informations sur la marche à suivre.

> [!NOTE]
> Auparavant, les limites relatives aux documents constituaient un facteur important, mais elles ne sont plus applicables aux nouveaux services. Pour plus d’informations sur les conditions dans lesquelles les limites de document s’appliquent encore, consultez [Limites de service : limites du document](search-limits-quotas-capacity.md#document-limits).
>

Niveaux de stockage optimisé, **L1 L2**, sont idéales pour les applications avec les exigences de données volumineuses, mais un nombre relativement faible d’utilisateurs finaux où réduire la latence la requête n’est pas la priorité absolue.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Taille de partition|  1 To | 2 To |  |  |  |  |  |
| Limites d’index et d’indexeur| 10 | 10 |  |  |  |  |  |

*L2* offre deux fois la capacité de stockage globale pour un *L1*.  Choisir votre niveau selon la quantité maximale de données que vous pensez que votre index a besoin.  Le *L1* niveau partitionne la mise à l’échelle des incréments de 1 To à un maximum de 12 To, tandis que le *L2* augmenter en 2 To par partition jusqu'à un maximum de 24 To.

## <a name="evaluate-capacity"></a>Évaluer la capacité

La capacité et les coûts d’exécution du service vont de pair. Les niveaux imposent des limites à deux niveaux (stockage et ressources). Vous devez donc réfléchir à ces deux facteurs, car celui qui est atteint en premier constitue la limite effective. 

Ce sont généralement les exigences professionnelles qui imposent le nombre d’index dont vous aurez besoin. Par exemple, un index global pour un référentiel de documents volumineux, ou peut-être plusieurs index basés sur la région, l’application ou une niche professionnelle.

Pour déterminer la taille d’un index, vous devez en [créer un](search-create-index-portal.md). La structure des données dans Recherche Azure est principalement un [index inversé](https://en.wikipedia.org/wiki/Inverted_index), qui a des caractéristiques différentes des données sources. Pour un index inversé, la taille et la complexité sont déterminées par le contenu, et pas nécessairement par la quantité de données fournies. Une source de données de grande taille avec une redondance très conséquente peut générer un index plus petit qu’un jeu de données plus petit ayant du contenu très variable. Par conséquent, il est rarement possible de déduire la taille de l’index d’après celle du jeu de données d’origine.

> [!NOTE] 
> Bien que l’estimation des besoins futurs en matière d’index et de stockage semble très approximative, elle en vaut la peine. Si la capacité d’un niveau tarifaire est trop basse, vous devrez provisionner un nouveau service au niveau supérieur, puis [recharger vos index](search-howto-reindex.md). Il n’existe aucune mise à niveau sur place du même service d’une référence (SKU) à un autre.
>

### <a name="step-1-develop-rough-estimates-using-the-free-tier"></a>Étape 1 : Développer des estimations approximatives avec le niveau Gratuit

Une approche possible pour l’estimation de la capacité consiste à utiliser le niveau **Gratuit**. Souvenez-vous que le niveau **Gratuit** offre jusqu’à trois index, 50 Mo de stockage et deux minutes de temps d’indexation. Il peut être difficile d’effectuer une projection de taille d’index avec ces contraintes, mais l’exemple suivant illustre une approche :

+ [Créer un service gratuit](search-create-service-portal.md)
+ Préparer un petit jeu de données représentatif (disons cinq mille documents et une taille d’échantillon de dix pour cent)
+ [Générer un index initial](search-create-index-portal.md) et notez sa taille dans le portail (disons 30 Mo)

En supposant que l’échantillon soit représentatif et constitué de dix pour cent de la source de données complète, un index de 30 Mo devient environ 300 Mo si tous les documents sont indexés. Vous pourriez doubler ce chiffre préliminaire afin de budgéter pour deux index (développement et production), soit un total de 600 Mo de besoins de stockage. Ces exigences étant facilement satisfaites avec le niveau **De base**, vous devez commencer par là.

### <a name="step-2-develop-refined-estimates-using-a-billable-tier"></a>Étape 2 : Développer des estimations affinées avec le niveau facturable

Certains clients préfèrent commencer avec des ressources dédiées capables de prendre en charge un échantillonnage et des temps de traitement plus élevés, puis développer des estimations réalistes en ce qui concerne la quantité d’index, la taille et les volumes de requêtes durant le développement. Initialement, un service est provisionné d’après la meilleure hypothèse puis, à mesure que le projet de développement évolue, les équipes savent généralement si le service existant offre trop ou pas assez de capacité pour les charges de production prévues. 

1. [Passez en revue les limites de service de chaque niveau](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) afin de déterminer si les niveaux inférieurs peuvent prendre en charge la quantité d’index dont vous avez besoin. Avec les niveaux **De base**-**S1**-**S2**, les limites d’index sont respectivement de 15-50-200.  Le **stockage optimisé** couche a une limite de 10 index puisqu’il s’agit de concepteur pour prendre en charge un nombre faible de très grands index.

1. [Créez un service à un niveau facturable](search-create-service-portal.md) :

    + Commencez à un faible niveau, **De base** ou **S1**, si vous êtes au début de votre processus d’apprentissage.
    + Commencez à un niveau élevé, **S2** ou même **S3**, s’il est évident que vous aurez besoin de charges de requêtes et d’indexation à grande échelle.
    + Stockage optimisé, au **L1** ou **L2**, si vous indexez une grande quantité de données et la charge de requête est relativement faible, par exemple une application métier interne.

1. [Générez un index initial](search-create-index-portal.md) pour déterminer comment les données sources se traduisent par un index. C’est l’unique façon d’estimer la taille de l’index.

1. [Surveillez le stockage, les limites de service, le volume de requêtes et la latence](search-monitor-usage.md) dans le portail. Le portail affiche les requêtes par secondes requêtes limitées et latence de recherche ; qui peut vous aider à décider si vous avez sélectionné le bon niveau. Outre les métriques du portail, vous pouvez configurer l’analyse approfondie, telle que l’analyse à l’aide de rapports générés interactifs, en activant l’[analytique du trafic des recherches](search-traffic-analytics.md). 

Le nombre et la taille des index sont tout aussi pertinents pour votre analyse, car les limites maximales sont atteintes en cas d’utilisation totale du stockage (partitions) ou quand les valeurs limites maximales sont atteintes pour les ressources (index, indexeurs et ainsi de suite), selon ce qui se produit en premier. Le portail vous aide à effectuer le suivi de ces deux facteurs en affichant l’utilisation actuelle et les limites maximales côte à côte dans la page de présentation.

> [!NOTE]
> Les besoins de stockage peuvent être augmentés de façon excessive si les documents contiennent des données superflues. Dans l’idéal, les documents contiennent uniquement les données dont vous avez besoin pour l’expérience de recherche. Les données binaires ne sont pas interrogeables et doivent être stockées séparément (dans une table ou un objet blob de stockage Azure) avec un champ dans l’index pour conserver une référence URL aux données externes. La taille maximale d’un document individuel est de 16 Mo (ou moins si vous chargez en bloc plusieurs documents en une seule demande). Pour plus d’informations, consultez [Limites de service de Recherche Azure](search-limits-quotas-capacity.md).
>

**Considérations relatives à la requête volume**

Le nombre de requêtes par seconde est une métrique qui prend de l’importance lors du réglage des performances, mais ce n’est généralement pas un facteur lors du choix du niveau, sauf si vous vous attendez à ce que le volume de requêtes soit élevé dès le départ.

Les niveaux standard peuvent fournir un équilibre entre le nombre de réplicas et le nombre de partitions, avec la prise en charge d’un délai d’exécution de requête plus rapide grâce à des réplicas supplémentaires pour le chargement de partitions supplémentaires et d’équilibrage en vue d’un traitement parallèle. Vous pouvez régler les performances une fois le service provisionné.

Les clients qui prévoient des volumes de requête soutenu fort dès le départ doivent envisager la plus élevées **Standard** niveaux, soutenus par un matériel plus puissant. Vous pouvez ensuite mettre des partitions et des réplicas hors connexion, ou même passer à un service de niveau inférieur, si ces volumes de requêtes ne se matérialisent pas. Pour plus d’informations sur la façon de calculer le débit de requête, consultez [Performances et optimisation de Recherche Azure](search-performance-optimization.md).

Le stockage optimisé niveaux lean vers des charges de travail de données volumineuses, prenant en charge de plusieurs index stockage global disponible, où les exigences de latence de requête sont quelque peu simplifiées.  Réplicas supplémentaires doivent encore être optimisées pour le chargement d’équilibrage et d’autres partitions pour un traitement parallèle. Vous pouvez régler les performances une fois le service provisionné.

**Contrats de niveau de service**

Le niveau **Gratuit** et les fonctionnalités de préversion ne sont pas fournis avec des [Contrats de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Pour tous les niveaux facturables, les SLA entrent en vigueur lorsque vous approvisionnez une redondance suffisante pour votre service. Un SLA de requête (lecture) requiert au moins deux réplicas. Un SLA de requête et d’indexation (lecture-écriture) nécessite au moins trois réplicas. Le nombre de partitions n’est pas pris en compte dans les SLA. 

## <a name="tips-for-tier-evaluation"></a>Conseils pour l’évaluation du niveau

+ Apprenez à créer des index efficaces et à identifier les méthodologies d’actualisation ayant le moins d’impact. Nous vous recommandons l’[analytique du trafic des recherches](search-traffic-analytics.md) pour tirer parti des informations obtenues sur l’activité des requêtes.

+ Autorisez la génération de métriques autour des requêtes et recueillez des données relatives aux modèles d’utilisation (requêtes pendant les heures de bureau, indexation pendant les heures creuses), et utilisez ces données pour faciliter les décisions de provisionnement de service ultérieures. Bien que cela ne soit pas pratique à une cadence horaire ou quotidienne, vous pouvez ajuster les partitions et les ressources de manière dynamique afin de prendre en charge les changements de volumes de requêtes planifiés, ou les changements non planifiés mais soutenus si les niveaux se maintiennent suffisamment longtemps pour que des mesures s’imposent.

+ N’oubliez pas que le seul inconvénient du sous-provisionnement est que vous devrez peut-être supprimer un service si les exigences réelles sont supérieures à celles estimées. Pour éviter toute interruption de service, vous devrez créer un service dans le même abonnement à un niveau supérieur, et l’exécuter côte à côte jusqu’à ce que toutes les applications et requêtes ciblent le nouveau point de terminaison.

## <a name="next-steps"></a>Étapes suivantes

Commencez avec un niveau **Gratuit** et créez un index initial à l’aide d’un sous-ensemble de vos données afin de bien comprendre ses caractéristiques. La structure des données dans Recherche Azure est un index inversé. La taille et la complexité d’un index inversé sont déterminées par le contenu. Souvenez-vous que le contenu hautement redondant a tendance à générer un index plus petit que le contenu très irrégulier. Par conséquent, ce sont les caractéristiques du contenu plutôt que la taille du jeu de données qui déterminent les besoins de stockage d’index.

Une fois que vous avez une idée initiale de la taille de l’index, [approvisionner un service facturable](search-create-service-portal.md) à un des niveaux abordés dans cet article, soit **base**, **Standard**, ou **Stockage optimisé** niveau. Assouplir toute contrainte artificielle sur le dimensionnement des données et [reconstruire votre index](search-howto-reindex.md) pour inclure toutes les données que vous souhaitez réellement être disponible pour la recherche.

[Allouez des partitions et des réplicas](search-capacity-planning.md) en fonction des besoins, afin d’obtenir les performances et l’échelle requises.

Si les performances et la capacité conviennent, vous avez terminé. Dans le cas contraire, recréez un service de recherche à un autre niveau correspondant mieux à vos besoins.

> [!NOTE]
> Pour poser des questions et obtenir de l’aide, publiez un message sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [contactez le Support technique Azure](https://azure.microsoft.com/support/options/).
