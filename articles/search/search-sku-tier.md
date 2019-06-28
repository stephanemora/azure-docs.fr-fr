---
title: Choisir un niveau tarifaire ou une référence SKU pour le service Recherche Azure | Recherche Azure
description: 'Le service Recherche Azure peut être approvisionné dans les références SKU suivantes : Gratuit, De base et Standard, sachant que la référence Standard est disponible en différentes configurations de ressources et différents niveaux de capacité.'
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65539284"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Choisir un niveau tarifaire pour Recherche Azure

Lorsque vous créez un service Recherche Azure, une [ressource est créée](search-create-service-portal.md) à un niveau tarifaire ou une référence SKU qui sont fixes pendant toute la durée de vie du service. Les niveaux disponibles sont : Gratuit, De base, Standard et À stockage optimisé. Les niveaux Standard et À stockage optimisé sont proposés dans diverses configurations et capacités.

La plupart des clients commencent par le niveau Gratuit qui leur permet d’évaluer le service. Ensuite, ils passent à l’un des niveaux supérieurs pour les déploiements de développement et de production. Au niveau Gratuit, vous pouvez effectuer tous les démarrages rapides et suivre tous les tutoriels, notamment ceux qui concernent la recherche cognitive nécessitant de nombreuses ressources.

> [!NOTE]
> Microsoft propose actuellement les niveaux de service À stockage optimisé sous la forme d’une préversion à tarif réduit à des fins de test et d’expérimentation, dans le but de recueillir des commentaires. La tarification finale sera annoncée ultérieurement, une fois ces niveaux généralement disponibles. Nous déconseillons l’utilisation de ces niveaux pour des applications de production.

Les niveaux reflètent les caractéristiques du matériel qui héberge le service (plutôt que les fonctionnalités) et se distinguent en fonction de ce qui suit :

+ nombre d’index que vous pouvez créer ;
+ taille et vitesse des partitions (stockage physique).

Bien que tous les niveaux, y compris le niveau Gratuit, offrent généralement la parité des fonctionnalités, des charges de travail plus importantes peuvent nécessiter des niveaux supérieurs. Par exemple, [l’indexation IA avec Cognitive Services](cognitive-search-concept-intro.md) implique des qualifications à long terme qui dépassent le délai d’attente sur un service gratuit, sauf si le jeu de données est restreint.

> [!NOTE] 
> Les [indexeurs](search-indexer-overview.md), qui ne sont pas disponibles sur S3 HD, constituent une exception à la parité des fonctionnalités.
>

Au sein d’un niveau, vous pouvez [ajuster les ressources de réplica et de partition](search-capacity-planning.md) pour augmenter ou réduire l’échelle. Vous pouvez commencer avec une ou deux ressources de chaque, puis augmenter temporairement votre puissance de calcul en cas de lourde charge de travail d’indexation. La possibilité d’optimiser les niveaux des ressources ajoute à la flexibilité, mais complique également un peu votre analyse. Vous devrez peut-être effectuer des essais pour déterminer si un niveau inférieur avec plus de ressources/réplicas offre de meilleures valeurs et performances qu’un niveau supérieur avec moins de ressources. Pour en savoir plus sur quand et pourquoi vous devriez ajuster la capacité, consultez [Considérations sur les performances et l’optimisation](search-performance-optimization.md).

## <a name="tiers-for-azure-search"></a>Niveaux du service Recherche Azure

Le tableau ci-après répertorie les niveaux disponibles. Des informations complémentaires sur les différents niveaux sont disponibles sur la [page de tarification](https://azure.microsoft.com/pricing/details/search/), dans l’article [Service limits in Azure Search](search-limits-quotas-capacity.md) (Limites du service Recherche Azure), ainsi que sur la page du portail lorsque vous approvisionnez un service.

|Niveau | Capacité |
|-----|-------------|
|Gratuit | Partagée avec d’autres abonnés. Non évolutive. Limitée à 3 index et à 50 Mo de stockage. |
|De base | Ressources informatiques dédiées aux charges de production à petite échelle. Une partition de 2 Go et jusqu’à 3 réplicas. |
|Standard 1 (S1) | Pour le niveau S1 et les niveaux supérieurs, machines dédiées avec une capacité de stockage et de traitement plus élevée à chaque niveau. Pour S1, la taille de partition est de 25 Go par partition (avec un maximum de 300 Go par service). |
|Standard 2 (S2) | Identique à S1, mais avec des partitions de 100 Go (et un maximum de 1,2 To par service). |
|Standard 3 (S3) | Partitions de 200 Go (avec un maximum de 2,4 To par service). |
|Standard 3 haute densité (S3 HD) | Le terme « haute densité » désigne un *mode d’hébergement* pour S3. Le matériel sous-jacent est optimisé pour un grand nombre d’index plus petits et est destiné aux scénarios d’architecture mutualisée. Le niveau S3 HD présente les mêmes frais à l’unité que S3, mais le matériel est optimisé pour les lectures de fichiers rapides sur un grand nombre d’index plus petits.|
|À stockage optimisé 1 (L1) | Partitions de 1 To (avec un maximum de 12 To par service). |
|À stockage optimisé 2 (L2) | Partitions de 2 To (avec un maximum de 24 To par service). |

> [!NOTE] 
> Les niveaux À stockage optimisé offrent une capacité de stockage plus importante et pour un prix moindre par To que les niveaux Standard. Le principal compromis impliqué par ces niveaux réside dans une latence de requête plus élevée, ce que vous devez valider pour vos exigences applicatives spécifiques.  Pour plus d’informations sur les considérations en matière de performances de ce niveau, consultez l’article [Considérations sur les performances et l’optimisation](search-performance-optimization.md).
>

## <a name="how-billing-works"></a>Comment la facturation fonctionne

Les frais du service Recherche Azure sont facturables de trois manières et comportent des composants fixes et variables. Cette section décrit les trois composants de facturation : coûts de base du service, frais de sortie de données et indexation IA.

### <a name="core-service-costs-fixed-and-variable"></a>Coûts de base du service (fixes et variables)

Pour le service proprement dit, les frais minimaux correspondent à la première unité de recherche (1 réplica x 1 partition). Ces frais minimaux sont fixes pendant toute la durée de vie du service, car il est impossible d’exécuter le service sur une configuration inférieure à cette dernière.

Au-delà des frais minimaux, vous pouvez ajouter des réplicas et des partitions de manière indépendante. Par exemple, il vous est possible de n’ajouter que des réplicas ou que des partitions. Les augmentations de capacité incrémentielles par le biais des réplicas et des partitions constituent le composant de coût variable.

La facturation repose sur une [formule (réplicas x partitions x tarif)](#search-units). Le montant qui vous est facturé dépend du niveau tarifaire que vous sélectionnez.

Dans la capture d’écran suivante, le prix unitaire est indiqué pour les niveaux Gratuit, De base et S1. (Les niveaux S2, S3, L1 et L2 ne sont pas visibles.) Si vous créez un service De base, votre coût mensuel constitue la moyenne de la valeur indiquée dans le champ *price-1*. Dans le cas d’un service Standard, votre coût mensuel constitue la moyenne de la valeur indiquée dans le champ *price-2*. Les coûts unitaires augmentent pour chaque niveau, car la puissance de calcul et la capacité de stockage sont plus élevées à chaque niveau. Les tarifs du service Recherche Azure sont indiqués sur la [page de tarification de Recherche Azure](https://azure.microsoft.com/pricing/details/search/).

![Prix unitaire](./media/search-sku-tier/per-unit-pricing.png "Prix unitaire")

Lorsque vous estimez le coût d’une solution de recherche, gardez à l’esprit que la tarification et la capacité ne sont pas linéaires. (Par exemple, la multiplication par deux de la capacité coûte plus du double.) Pour découvrir un exemple du mode de fonctionnement de la formule, consultez la section [How to allocate replicas and partitions](search-capacity-planning.md#how-to-allocate-replicas-and-partitions) (Allocation de réplicas et de partitions).

#### <a name="billing-based-on-search-units"></a>Facturation basée sur les Unités Recherche

La notion de facturation la plus importante à saisir pour les opérations du service Recherche Azure est *l’Unité Recherche* (SU, Search Unit). Comme Recherche Azure dépend à la fois des réplicas et des partitions pour l’indexation et les requêtes, facturer uniquement par rapport à l’un ou à l’autre n’aurait aucun sens. Au lieu de cela, la facturation est basée sur un composite de ces deux facteurs.

L’Unité Recherche est le produit des *réplicas* et des *partitions* utilisés par un service : **(R x P = SU)** .

Chaque service commence avec une Unité Recherche (un réplica multiplié par une partition) au minimum. Le montant maximal de tout service est de 36 SU. Ce montant maximal peut être atteint de plusieurs façons : 6 partitions x 6 réplicas, ou 3 partitions x 12 réplicas, par exemple. La capacité utilisée est généralement inférieure à la capacité totale (par exemple, un service de 3 réplicas et 3 partitions facturé avec un montant de 9 SU). Pour connaître les combinaisons valides, consultez l’article [Partition and replica combinations](search-capacity-planning.md#chart) (Combinaisons de partitions et de réplicas).

Le tarif de facturation est un tarif horaire par SU. Le tarif augmente progressivement à chaque niveau. Les niveaux supérieurs proposent des partitions plus volumineuses et plus rapides, ce qui entraîne un tarif horaire globalement plus élevé pour ces niveaux. Vous pouvez consulter les tarifs de chaque niveau sur la page des [détails de tarification](https://azure.microsoft.com/pricing/details/search/).

La plupart des clients mettent seulement une partie de la capacité totale en ligne et gardent le reste en réserve. Pour la facturation, le nombre de partitions et de réplicas que vous mettez en ligne, calculé à l’aide de la formule SU, détermine le tarif horaire qui vous est facturé.

### <a name="data-egress-charges-during-indexing"></a>Frais de sortie de données lors de l’indexation

L’utilisation [d’indexeurs Recherche Azure](search-indexer-overview.md) peut avoir une incidence sur la facturation, selon l’emplacement de vos services. Vous pouvez éliminer totalement les frais de sortie de données si vous créez le service Recherche Azure dans la même région que vos données. Voici quelques informations issues de la [page de tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/) :

+ Microsoft ne facture aucune donnée entrante dans un quelconque service Azure, ni aucune donnée sortante du service Recherche Azure.

+ Dans les solutions multiservice, il n’existe aucuns frais pour les données qui transitent par le réseau lorsque tous les services se trouvent dans la même région.

Des frais s’appliquent pour les données sortantes si les services sont situés dans des régions différentes. Ces frais ne figurent pas sur votre facture Recherche Azure. Ils sont mentionnés ici, car si vous utilisez des données ou des indexeurs IA pour extraire les données de différentes régions, les coûts associés apparaîtront sur votre facture globale.

### <a name="ai-enriched-indexing-with-cognitive-services"></a>Indexation IA avec Cognitive Services

Pour [l’indexation IA avec Cognitive Services](cognitive-search-concept-intro.md), prévoyez d’attacher une ressource Azure Cognitive Services facturable dans la même région que le service Recherche Azure, au niveau tarifaire S0 pour le traitement du paiement à l’utilisation. L’attachement de Cognitive Services n’entraîne aucun coût fixe. Vous payez uniquement pour le traitement dont vous avez besoin.

L’extraction d’images au cours du craquage de document est une opération facturable du service Recherche Azure. Elle est facturée en fonction du nombre d’images extraites de vos documents. L’extraction de texte est actuellement gratuite.

D’autres enrichissements, tels que le traitement en langage naturel, reposent sur des [compétences cognitives prédéfinies](cognitive-search-predefined-skills.md) et sont facturés par rapport à une ressource Cognitive Services. Ils sont facturés au même tarif que si vous aviez exécuté la tâche directement avec Cognitive Services. Pour plus d’informations, consultez l’article [Attacher une ressource Cognitive Services à un ensemble de compétences](cognitive-search-attach-cognitive-services.md).

<a name="search-units"></a>

#### <a name="billing-for-image-extraction-in-cognitive-search"></a>Facturation de l’extraction d’image dans la recherche cognitive

Si vous extrayez des images à partir de fichiers dans un pipeline d’indexation de recherche cognitive, vous serez facturé pour cette opération dans votre facture Recherche Azure. Dans une [configuration d’indexeur](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters), **imageAction** est le paramètre qui déclenche l’extraction d’images. Si **imageAction** est défini sur « none » (valeur par défaut), l’extraction d’images ne vous sera pas facturée.

Les tarifs sont susceptibles d’être modifiés. Ils sont documentés sur la page des [détails de tarification](https://azure.microsoft.com/pricing/details/search/) du service Recherche Azure.

#### <a name="billing-for-built-in-skills-in-cognitive-search"></a>Facturation des compétences intégrées à la recherche cognitive

Lorsque vous configurez un pipeline d’enrichissement, toute [compétence intégrée](cognitive-search-predefined-skills.md) utilisée dans le pipeline s’appuie sur des modèles Machine Learning. Ces modèles sont fournis par Cognitive Services. Si vous utilisez ces modèles lors de l’indexation, vous serez facturé au même tarif que si vous aviez demandé la ressource directement.

Par exemple, supposons que vous disposiez d’un pipeline utilisant la reconnaissance optique de caractères (OCR) sur des fichiers JPEG numérisés, et que le texte résultant soit transmis dans un index Recherche Azure pour les requêtes de recherche à structure libre. Votre pipeline d’indexation inclurait un indexeur avec la [compétence de reconnaissance optique de caractères](cognitive-search-skill-ocr.md), et cette compétence serait [associée à une ressource Cognitive Services](cognitive-search-attach-cognitive-services.md). Si vous exécutez l’indexeur, des frais d’exécution de reconnaissance optique de caractères apparaîtront sur votre facture de ressources cognitives.

## <a name="tips-for-reducing-costs"></a>Conseils de réduction des coûts

Vous ne pouvez pas arrêter le service en vue de réduire votre facture. Les ressources dédiées sont toujours opérationnelles et sont allouées pour votre usage exclusif pendant pour la durée de vie de votre service. Le seul moyen d’alléger votre facture consiste à abaisser les réplicas et les partitions à un niveau garantissant des performances acceptables ainsi que la [conformité au Contrat de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Vous pouvez réduire les coûts en choisissant un niveau présentant un tarif horaire inférieur. Le taux horaire S1 est inférieur aux taux S2 ou S3. En supposant que vous approvisionniez votre service à la limite inférieure de vos prévisions de charge, si vous dépassez le service, vous pouvez créer un second service de niveau supérieur, régénérer vos index sur ce second service, puis supprimer le premier.

Si vous avez planifié la capacité des serveurs locaux, vous savez qu’il est courant de « racheter » des ressources pour pouvoir faire face à la croissance prévue. Avec un service cloud, vous pouvez réaliser des économies beaucoup plus substantielles, car vous n’êtes pas lié à un achat spécifique. Vous pouvez toujours passer à un service de niveau supérieur si le service actuel est insuffisant.

### <a name="capacity"></a>Capacité

Dans Recherche Azure, la capacité est structurée sous forme de *réplicas* et de *partitions*.

+ Les réplicas sont des instances du service de recherche. Chaque réplica héberge une copie à charge équilibrée d’un index. Par exemple, un service avec six réplicas comporte six copies de chaque index chargé dans le service.

+ Les partitions stockent les index et fractionnent automatiquement les données utilisables dans une requête. Deux partitions fractionnent votre index en deux, trois partitions le scindent en trois, et ainsi de suite. En termes de capacité, la *taille de partition* est la principale caractéristique qui différencie les niveaux.

> [!NOTE]
> Tous les niveaux Standard et À stockage optimisé prennent en charge des [combinaisons flexibles de réplicas et de partitions](search-capacity-planning.md#chart) afin que vous puissiez [optimiser votre système sur le plan du stockage ou de la vitesse](search-performance-optimization.md) en changeant l’équilibrage. Le niveau De base offre jusqu’à trois réplicas pour la haute disponibilité, mais ne comporte qu’une seule partition. Les niveaux Gratuit ne fournissent pas de ressources dédiées : les ressources de calcul sont partagées par plusieurs abonnés.

### <a name="more-about-service-limits"></a>En savoir plus sur les limites de service

Les services hébergent des ressources comme des index et des indexeurs. Chaque niveau impose des [limites de service](search-limits-quotas-capacity.md) relatives au nombre de ressources que vous pouvez créer. Ainsi, le nombre maximal d’index (et d’autres objets) est la deuxième caractéristique qui différencie les niveaux. À mesure que vous consultez chaque option dans le portail, notez les limites concernant le nombre d’index. D’autres ressources, telles que les indexeurs, les sources de données et les ensembles de compétences, sont soumises à des limites d’index.

## <a name="consumption-patterns"></a>Modèles de consommation

La plupart des clients commencent avec le niveau Gratuit, qu’ils conservent indéfiniment, puis ils choisissent l’un des niveaux Standard ou À stockage optimisé pour les charges de travail de développement ou de production intensives.

![Niveaux tarifaires de Recherche Azure](./media/search-sku-tier/tiers.png "Niveaux tarifaires de Recherche Azure")

Aux extrémités inférieure et supérieure, les niveaux De base et S3 HD sont destinés aux modèles de consommation importants mais atypiques. Le niveau De base est conçu pour les charges de travail de production restreintes. Il offre des SLA, des ressources dédiées et une haute disponibilité, mais il ne fournit qu’un stockage modeste, plafonné à 2 Go. Ce niveau est destiné aux clients qui sous-utilisent régulièrement la capacité disponible. À l’autre extrémité, le niveau S3 HD convient aux charges de travail types des éditeurs de logiciels indépendants, des partenaires, des [solutions partagées au sein d’une architecture mutualisée](search-modeling-multitenant-saas-applications.md) ou de toute configuration exigeant un grand nombre de petits index. Les cas dans lesquels il convient de choisir le niveau De base ou S3 HD sont généralement évidents. Si vous souhaitez en obtenir confirmation, vous pouvez publier un message sur le site [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [contacter le support Azure](https://azure.microsoft.com/support/options/) pour obtenir de l’aide.

Les niveaux standard les plus couramment utilisés, S1 à S3, correspondent à une augmentation progressive des niveaux de capacité. Il existe des points d’inflexion sur la taille de partition et les limites relatives au nombre d’index, d’indexeurs et de ressources corollaires :

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Taille de partition|  25 Go | 100 Go | 200 Go |  |  |  |  |
| Limites d’index et d’indexeur| 50 | 200 | 200 |  |  |  |  |

S1 constitue un choix courant pour les clients qui nécessitent des ressources dédiées et plusieurs partitions. S1 offre des partitions de 25 Go et jusqu’à 12 partitions, en appliquant une limite par service de 300 Go si vous optimisez les partitions sur les réplicas. (Pour découvrir des répartitions plus équilibrées, consultez l’article [Allouer des partitions et des réplicas](search-capacity-planning.md#chart).)

Les pages de portail et de tarification mettent l’accent sur le stockage et la taille de partition, mais pour chaque niveau, toutes les caractéristiques de calcul (capacité du disque, vitesse, unités centrales) augmentent généralement de manière linéaire avec le prix. Un réplica S2 est plus rapide que S1, de même qu’un réplica S3 est plus rapide que S2. Les niveaux S3 s’affranchissent du modèle de tarification de calcul linéaire en offrant des E/S considérablement plus rapides. Si les E/S risquent de constituer le goulot d’étranglement, n’oubliez pas que le niveau S3 vous permet d’obtenir bien plus d’IOPS que les niveaux inférieurs.

Les niveaux S3 et S3 HD s’appuient sur une infrastructure haute capacité identique, mais ils atteignent leur limite maximale de différentes manières. S3 cible un plus petit nombre d’index très volumineux, de sorte que sa limite maximale est liée à la ressource (2,4 To pour chaque service). S3 HD cible un grand nombre de très petits index. Avec 1 000 index, S3 HD atteint ses limites sous la forme de contraintes d’index. Si vous êtes un client S3 HD et que vous avez besoin de plus de 1 000 index, contactez le Support Microsoft pour obtenir des informations sur la marche à suivre.

> [!NOTE]
> Les limites relatives aux documents constituaient autrefois un facteur important, mais elles ne s’appliquent plus aux nouveaux services. Pour plus d’informations sur les situations dans lesquelles les limites de document s’appliquent encore, consultez la section [Limites du document](search-limits-quotas-capacity.md#document-limits).
>

Les niveaux À stockage optimisé, L1 et L2, sont idéalement adaptés aux applications qui présentent d’importantes exigences en matière de données, mais un nombre relativement faible d’utilisateurs finaux, dans les cas où la réduction de la latence des requêtes ne constitue pas la priorité absolue.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Taille de partition|  1 To | 2 To |  |  |  |  |  |
| Limites d’index et d’indexeur| 10 | 10 |  |  |  |  |  |

Le niveau L2 offre deux fois la capacité de stockage totale du niveau L1.  Choisissez votre niveau en fonction de la quantité maximale de données dont vous pensez avoir besoin pour votre index. Les partitions du niveau L1 montent en puissance par incréments de 1 To jusqu’à un maximum de 12 To. Les partitions du niveau L2 augmentent par incréments de 2 To à hauteur de 24 To.

## <a name="evaluating-capacity"></a>Évaluation de la capacité

La capacité et les coûts d’exécution du service sont directement liés. Les niveaux imposent des limites sur deux plans : stockage et ressources. Vous devez considérer les deux à la fois, car la limite que vous atteignez en premier représente la limite effective.

Ce sont généralement les exigences métier qui imposent le nombre d’index dont vous aurez besoin. Par exemple, vous devrez peut-être disposer d’un index global pour un référentiel de documents volumineux. Ou vous pourrez avoir besoin de plusieurs index basés sur la région, l’application ou le créneau commercial.

Pour déterminer la taille d’un index, vous devez en [créer un](search-create-index-portal.md). La structure de données du service Recherche Azure constitue principalement une structure [d’index inversé](https://en.wikipedia.org/wiki/Inverted_index), dont les caractéristiques diffèrent de celles des données sources. Dans le cas d’un index inversé, la taille et la complexité sont déterminées par le contenu, et non nécessairement par la quantité de données qui l’alimentent. Une source de données volumineuse avec un haut niveau de redondance peut générer un index plus restreint qu’un jeu de données plus modeste présentant un contenu extrêmement variable. Il est donc généralement impossible de déduire la taille de l’index d’après celle du jeu de données d’origine.

> [!NOTE] 
> Même si l’estimation des besoins futurs en matière d’index et de stockage semble très approximative, elle en vaut la peine. Si la capacité d’un niveau se révèle insuffisante, vous devrez approvisionner un nouveau service à un niveau supérieur, puis [recharger vos index](search-howto-reindex.md). Un service ne peut faire l’objet d’aucune mise à niveau sur place d’une référence SKU vers une autre.
>

### <a name="step-1-develop-rough-estimates-by-using-the-free-tier"></a>Étape 1 : Développer des estimations approximatives à l’aide du niveau Gratuit

Une méthode possible pour l’estimation de la capacité consiste à commencer par utiliser le niveau Gratuit. Souvenez-vous que le niveau Gratuit offre jusqu’à 3 index, 50 Mo de stockage et 2 minutes de temps d’indexation. Il peut être difficile d’estimer la taille projetée de l’index avec ces contraintes. Voici une approche possible :

+ [Créez un service gratuit](search-create-service-portal.md).
+ Préparez un jeu de données représentatif de petite taille (par exemple, 5 000 documents et une taille d’échantillon de 10 pour cent).
+ [Générez un index initial](search-create-index-portal.md) et notez sa taille dans le portail (par exemple, 30 Mo).

Si l’échantillon est représentatif et constitué de 10 pour cent de la source de données complète, un index de 30 Mo atteint environ 300 Mo lorsque tous les documents sont indexés. Pour définir le budget de deux index (développement et production), vous pouvez doubler cette valeur préliminaire. Vos exigences de stockage totalisent donc 600 Mo. Étant donné que le niveau De base répond parfaitement à ce besoin, vous pouvez commencer par ce niveau.

### <a name="step-2-develop-refined-estimates-by-using-a-billable-tier"></a>Étape 2 : Développer des estimations affinées à l’aide d’un niveau facturable

Certains clients préfèrent commencer avec des ressources dédiées pouvant prendre en charge un échantillonnage et des temps de traitement plus étendus, puis élaborer des estimations réalistes concernant le nombre d’index, la taille et les volumes de requêtes durant le développement. À l’origine, un service est approvisionné d’après la meilleure hypothèse. Ensuite, à mesure que le projet de développement évolue, les équipes savent généralement si le service existant offre trop ou pas assez de capacité pour les charges de production prévues.

1. [Passez en revue les limites de service de chaque niveau](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) afin de déterminer si les niveaux inférieurs peuvent prendre en charge le nombre d’index dont vous avez besoin. Pour les niveaux De base, S1 et S2, les limites d’index sont respectivement de 15, 50 et 200. Le niveau À stockage optimisé présente une limite de 10 index, car il a été conçu pour prendre en charge un petit nombre d’index très volumineux.

1. [Créez un service à un niveau facturable](search-create-service-portal.md) :

    + Si vous débutez votre processus d’apprentissage, commencez par un faible niveau, De base ou S1.
    + En revanche, si vous savez que vous devrez traiter des charges de requêtes et d’indexation à grande échelle, choisissez dès le départ un niveau élevé, S2 ou même S3.
    + Enfin, si vous indexez une grande quantité de données et que la charge de requêtes est relativement faible, comme dans le cas d’une application métier interne, commencez par le niveau À stockage optimisé L1 ou L2.

1. [Générez un index initial](search-create-index-portal.md) pour déterminer comment les données sources se traduisent par un index. C’est l’unique façon d’estimer la taille de l’index.

1. [Surveillez le stockage, les limites de service, le volume de requêtes et la latence](search-monitor-usage.md) dans le portail. Le portail vous indique le nombre de requêtes par seconde, les requêtes limitées et la latence de recherche. Toutes ces valeurs peuvent vous aider à décider si vous avez sélectionné le niveau adéquat. Vous pouvez également configurer la surveillance approfondie des valeurs telles que l’analyse des clics en activant la fonctionnalité [d’analytique du trafic des recherches](search-traffic-analytics.md).

Le nombre d’index et la taille se révèlent tout aussi importants pour votre analyse. En effet, les limites maximales sont atteintes en cas d’utilisation totale du stockage (partitions) ou des limites maximales relatives aux ressources (index, indexeurs et ainsi de suite), selon ce qui se produit en premier. Le portail vous aide à effectuer le suivi de ces deux facteurs en affichant l’utilisation actuelle et les limites maximales côte à côte dans la page de présentation.

> [!NOTE]
> La présence de données superflues dans les documents peut entraîner une augmentation des exigences de stockage. Dans l’idéal, les documents contiennent uniquement les données dont vous avez besoin pour l’expérience de recherche. Les données binaires ne sont pas utilisables dans une requête et doivent être stockées séparément (par exemple dans un stockage Table Azure ou Blob Azure). Un champ doit ensuite être ajouté dans l’index pour conserver une référence URL aux données externes. La taille maximale d’un document individuel est de 16 Mo (ou moins si vous chargez en bloc plusieurs documents dans une seule requête). Pour plus d’informations, consultez [Limites de service d’Azure Search](search-limits-quotas-capacity.md).
>

**Considérations relatives au volume de requêtes**

Le nombre de requêtes par seconde est une métrique importante lors du réglage des performances, mais il ne doit généralement être pris en compte pour le choix du niveau que si vous prévoyez un volume de requêtes élevé dès le départ.

Les niveaux Standard peuvent assurer un équilibre entre le nombre de réplicas et le nombre de partitions. Vous pouvez accélérer l’exécution des requêtes en ajoutant des réplicas pour l’équilibrage de charge ou ajouter des partitions à des fins de traitement parallèle. Vous pouvez ensuite régler les performances une fois le service approvisionné.

Si vous prévoyez des volumes de requêtes élevés et soutenus dès le début, vous devez envisager d’adopter des niveaux Standard plus élevés, qui s’appuient sur du matériel plus puissant. Si ces volumes de requêtes ne surviennent pas, vous pouvez alors mettre des partitions et des réplicas hors connexion, ou même passer à un service de niveau inférieur. Pour plus d’informations sur la façon de calculer le débit de requête, consultez [Performances et optimisation de Recherche Azure](search-performance-optimization.md).

Les niveaux À stockage optimisé sont utiles pour les charges de travail de données intensives, car ils prennent en charge un stockage d’index total disponible plus important dans les cas où les exigences en matière de latence des requêtes sont moins essentielles. Vous devez toujours utiliser des réplicas supplémentaires pour l’équilibrage de charge, ainsi que des partitions supplémentaires à des fins de traitement parallèle. Vous pouvez ensuite régler les performances une fois le service approvisionné.

**Contrats de niveau de service**

Le niveau Gratuit et les fonctionnalités d’évaluation ne fournissent pas de [Contrats de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Pour tous les niveaux facturables, les SLA entrent en vigueur lorsque vous approvisionnez une redondance suffisante pour votre service. Vous devez disposer d’au moins deux réplicas pour les SLA de requête (lecture). Vous devez posséder au moins trois réplicas pour les SLA de requête et d’indexation (lecture-écriture). Le nombre de partitions n’affecte pas les SLA.

## <a name="tips-for-tier-evaluation"></a>Conseils pour l’évaluation du niveau

+ Apprenez à créer des index efficaces et à identifier les méthodes d’actualisation ayant le moins d’impact. Utilisez la fonctionnalité [d’analytique du trafic des recherches](search-traffic-analytics.md) pour obtenir des insights concernant l’activité des requêtes.

+ Autorisez la génération de métriques autour des requêtes et recueillez des données relatives aux modèles d’utilisation (requêtes pendant les heures de bureau, indexation pendant les heures creuses). Utilisez ces données pour faciliter les décisions d’approvisionnement de service. Bien que cela ne soit pas pratique à une cadence horaire ou quotidienne, vous pouvez ajuster les partitions et les ressources de manière dynamique afin de prendre en charge les changements de volumes de requêtes planifiés. Vous pouvez également gérer les changements non planifiés mais soutenus si les niveaux se maintiennent suffisamment longtemps pour que des mesures s’imposent.

+ N’oubliez pas que le seul inconvénient du sous-approvisionnement réside dans le fait que vous devrez peut-être supprimer un service si les exigences réelles sont supérieures à vos prévisions. Pour éviter toute interruption de service, vous devrez créer un service dans le même abonnement à un niveau supérieur, et l’exécuter côte à côte jusqu’à ce que toutes les applications et requêtes ciblent le nouveau point de terminaison.

## <a name="next-steps"></a>Étapes suivantes

Commencez avec un niveau Gratuit et créez un index initial à l’aide d’un sous-ensemble de vos données afin de bien comprendre ses caractéristiques. La structure de données du service Recherche Azure est une structure d’index inversé. Le contenu détermine la taille et la complexité d’un index inversé. Souvenez-vous que le contenu hautement redondant a tendance à générer un index plus petit que le contenu très irrégulier. Par conséquent, les exigences en matière de stockage d’index sont déterminées par les caractéristiques du contenu, et non par la taille du jeu de données.

Après avoir effectué une estimation initiale de la taille de votre index, [approvisionnez un service facturable](search-create-service-portal.md) sur l’un des niveaux présentés dans cet article : De base, Standard ou À stockage optimisé. Assouplissez les contraintes artificielles sur le dimensionnement des données et [régénérez votre index](search-howto-reindex.md) afin d’y inclure toutes les données que vous souhaitez rendre utilisables dans une requête.

[Allouez des partitions et des réplicas](search-capacity-planning.md) en fonction des besoins, afin d’obtenir les performances et l’échelle requises.

Si les performances et la capacité conviennent, vous avez terminé. Dans le cas contraire, recréez un service de recherche à un autre niveau correspondant mieux à vos besoins.

> [!NOTE]
> Si vous avez des questions, publiez un message sur le site [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [contactez le support Azure](https://azure.microsoft.com/support/options/).
