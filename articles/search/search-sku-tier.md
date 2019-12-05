---
title: Choisir un niveau tarifaire ou une référence SKU
titleSuffix: Azure Cognitive Search
description: 'Le service Recherche cognitive Azure peut être approvisionné dans les références SKU suivantes : Gratuit, De base et Standard, sachant que la référence Standard est disponible en différentes configurations de ressources et différents niveaux de capacité.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d691759f1075a08ad13ec1199eb8af7fd634f5a1
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534474"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Choisir un niveau tarifaire pour Recherche cognitive Azure

Lorsque vous créez un service Recherche cognitive Azure, une [ressource est créée](search-create-service-portal.md) à un niveau tarifaire ou une référence SKU qui sont fixes pendant toute la durée de vie du service. Les niveaux disponibles sont : Gratuit, De base, Standard et À stockage optimisé. Les niveaux Standard et À stockage optimisé sont proposés dans diverses configurations et capacités.

La plupart des clients commencent par le niveau Gratuit qui leur permet d’évaluer le service. Une fois l’évaluation terminée, il est courant de créer un deuxième service à l’un des niveaux supérieurs pour les déploiements de développement et de production.

Bien que tous les niveaux, y compris le niveau Gratuit, offrent généralement la parité des fonctionnalités, des charges de travail plus importantes peuvent nécessiter des niveaux supérieurs. Par exemple, [l’enrichissement de l’IA](cognitive-search-concept-intro.md) implique des qualifications à long terme qui dépassent le délai d’attente sur un service gratuit, sauf si le jeu de données est restreint.

> [!NOTE] 
> Les [indexeurs](search-indexer-overview.md), qui ne sont pas disponibles sur S3 HD, constituent une exception à la parité des fonctionnalités.
>

<!-- For Basic tier and up, you can [adjust replica and partition resources](search-capacity-planning.md) to increase or decrease scale. You could start with one or two of each and then temporarily raise your computational power for a heavy indexing workload. The ability to tune resource levels within a tier adds flexibility, but also slightly complicates your analysis. You might have to experiment to see whether a lower tier with more resources/replicas offers better value and performance than a higher tier with fewer resources. To learn more about when and why you would adjust capacity, see [Performance and optimization considerations](search-performance-optimization.md). -->

## <a name="available-tiers"></a>Niveaux disponibles

Les niveaux reflètent les caractéristiques du matériel qui héberge le service (plutôt que les fonctionnalités) et se distinguent en fonction de ce qui suit :

+ Quantité d’index et d’indexeurs que vous pouvez créer
+ Taille et la vitesse des partitions (stockage physique)

Le niveau que vous sélectionnez détermine le taux facturable. La capture d’écran suivante du portail Azure indique les niveaux disponibles, après la déduction indiquée sur le portail et dans la [page de tarification](https://azure.microsoft.com/pricing/details/search/). Les niveaux les plus courants sont **Gratuit**, **De base** et **Standard**.

Le niveau **Gratuit** crée un service de recherche limité sur un cluster, partagé avec d’autres abonnés. Vous pouvez accomplir de petits projets, dont des démarrages rapides et des didacticiels, mais vous ne pouvez pas mettre à l’échelle le service ou exécuter des charges de travail conséquentes. Les niveau **De base** et **Standard** sont les niveaux facturables les plus utilisés, **Standard** étant le niveau par défaut.

![Niveaux tarifaires de Recherche cognitive Azure](media/search-sku-tier/tiers.png "Niveaux tarifaires de Recherche cognitive Azure")

Certains niveaux sont optimisés pour certains types de travaux. Par exemple, le niveau **Standard 3 High Density (S3 HD)** est un *mode d’hébergement* pour S3, où le matériel sous-jacent est optimisé pour un grand nombre d’index plus petits, qui est destiné aux scénarios d’architecture mutualisée. Le niveau S3 HD présente les mêmes frais à l’unité que S3, mais le matériel est optimisé pour les lectures de fichiers rapides sur un grand nombre d’index plus petits.

Les niveaux **À stockage optimisé** offrent une capacité de stockage plus importante et à moindre coût par To que les niveaux Standard. Le principal compromis impliqué par ces niveaux réside dans une latence de requête plus élevée, ce que vous devez valider pour vos exigences applicatives spécifiques.  Pour plus d’informations sur les considérations en matière de performances de ce niveau, consultez l’article [Considérations sur les performances et l’optimisation](search-performance-optimization.md).

Des informations complémentaires sur les différents niveaux sont disponibles sur la [page de tarification](https://azure.microsoft.com/pricing/details/search/), dans l’article [Service limits in Azure Search](search-limits-quotas-capacity.md) (Limites du service Recherche cognitive Azure), ainsi que sur la page du portail lorsque vous approvisionnez un service.

## <a name="billable-events"></a>Événements facturables

Une solution reposant sur Recherche cognitive Azure peut occasionner des coûts de l’une des manières suivantes :

+ Coût de base du service avec une configuration minimale (créer un service)
+ Coût incrémentiel lors de la montée en puissance (ajout de réplicas ou de partitions)
+ Frais de bande passante (transfert de données sortant) 
+ Recherche cognitive (attacher Cognitive Services pour l’enrichissement de l’intelligence artificielle, Stockage Azure pour la base de connaissances)

### <a name="service-costs"></a>Coûts de service

Contrairement à des machines virtuelles ou à d’autres ressources qui peuvent être « mises en pause » pour éviter des frais, un service Recherche cognitive Azure est toujours disponible sur du matériel dédié à votre usage exclusif. En tant que telle, la création d’un service est un événement facturable qui commence lorsque vous créez le service et se termine lorsque vous le supprimez. 

Le coût minimal est la première unité de recherche (une réplique x une partition) au tarif facturable. Ces frais minimaux sont fixes pendant toute la durée de vie du service, car il est impossible d’exécuter le service sur une configuration inférieure à cette dernière. Au-delà des frais minimaux, vous pouvez ajouter des réplicas et des partitions indépendants les uns des autres. Les augmentations incrémentielles de capacité via des réplicas et partitions augmentent votre facture selon la formule suivante : [ (réplicas x partitions x tarif) ](#search-units), où le tarif facturé dépendant du niveau de tarification sélectionné.

Lorsque vous estimez le coût d’une solution de recherche, gardez à l’esprit que la tarification et la capacité ne sont pas linéaires. (Par exemple, la multiplication par deux de la capacité coûte plus du double.) Pour découvrir un exemple du mode de fonctionnement de la formule, consultez la section [How to allocate replicas and partitions](search-capacity-planning.md#how-to-allocate-replicas-and-partitions) (Allocation de réplicas et de partitions).

### <a name="bandwidth-charges"></a>Frais liés à la bande passante

L’utilisation [d’indexeurs Recherche cognitive Azure](search-indexer-overview.md) peut avoir une incidence sur la facturation, selon l’emplacement de vos services. Vous pouvez éliminer totalement les frais de sortie de données si vous créez le service Recherche cognitive Azure dans la même région que vos données. Voici quelques informations issues de la [page de tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/) :

+ Microsoft ne facture aucune donnée entrante dans un quelconque service Azure, ni aucune donnée sortante du service Recherche cognitive Azure.
+ Dans les solutions multiservice, il n’existe aucuns frais pour les données qui transitent par le réseau lorsque tous les services se trouvent dans la même région.

Des frais s’appliquent pour les données sortantes si les services sont situés dans des régions différentes. Ces frais ne figurent pas sur votre facture Recherche cognitive Azure. Ils sont mentionnés ici, car si vous utilisez des données ou des indexeurs IA pour extraire les données de différentes régions, les coûts associés apparaîtront sur votre facture globale.

### <a name="ai-enrichment-with-cognitive-services"></a>Enrichissement de l’IA avec Cognitive Services

Pour [l’enrichissement de l’IA](cognitive-search-concept-intro.md), prévoyez d’[attacher une ressource Azure Cognitive Services facturable](cognitive-search-attach-cognitive-services.md) dans la même région que le service Recherche cognitive Azure, au niveau tarifaire S0 pour le traitement du paiement à l’utilisation. L’attachement de Cognitive Services n’entraîne aucun coût fixe. Vous payez uniquement pour le traitement dont vous avez besoin.

| Opération | Impact sur la facturation |
|-----------|----------------|
| Craquage de document, extraction de texte | Gratuit |
| Craquage de document, extraction d’image | Facturée en fonction du nombre d’images extraites de vos documents. Dans une [configuration d’indexeur](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters), **imageAction** est le paramètre qui déclenche l’extraction d’images. Si **imageAction** est défini sur « none » (valeur par défaut), l’extraction d’images ne vous sera pas facturée. Le tarif de l’extraction d’image est mentionné sur la page des [détails de tarification](https://azure.microsoft.com/pricing/details/search/) du service Recherche cognitive Azure.|
| [Compétences cognitives prédéfinies](cognitive-search-predefined-skills.md) | Facturées au même tarif que si vous aviez exécuté la tâche directement avec Cognitive Services. |
| Compétences personnalisées | Une compétence personnalisée est une fonctionnalité que vous fournissez. Le coût d’utilisation d’une compétence personnalisée dépend entièrement du fait qu’un code personnalisé appelle d’autres services mesurés. |

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Formule de facturation (R x P = SU)

La notion de facturation la plus importante à comprendre pour les opérations du service Recherche cognitive Azure est *l’Unité Recherche* (SU, Search Unit). Comme Recherche cognitive Azure dépend à la fois des réplicas et des partitions pour l’indexation et les requêtes, facturer uniquement par rapport à l’un ou à l’autre n’aurait aucun sens. Au lieu de cela, la facturation est basée sur un composite de ces deux facteurs.

L’Unité Recherche est le produit des *réplicas* et des *partitions* utilisés par un service : **(R x P = SU)** .

Chaque service commence avec une Unité Recherche (un réplica multiplié par une partition) au minimum. Le montant maximal de tout service est de 36 SU. Ce montant maximal peut être atteint de plusieurs façons : 6 partitions x 6 réplicas, ou 3 partitions x 12 réplicas, par exemple. La capacité utilisée est généralement inférieure à la capacité totale (par exemple, un service de 3 réplicas et 3 partitions facturé avec un montant de 9 SU). Pour connaître les combinaisons valides, consultez l’article [Partition and replica combinations](search-capacity-planning.md#chart) (Combinaisons de partitions et de réplicas).

Le tarif de facturation est un tarif horaire par SU. Le tarif augmente progressivement à chaque niveau. Les niveaux supérieurs proposent des partitions plus volumineuses et plus rapides, ce qui entraîne un tarif horaire globalement plus élevé pour ces niveaux. Vous pouvez consulter les tarifs de chaque niveau sur la page des [détails de tarification](https://azure.microsoft.com/pricing/details/search/).

La plupart des clients mettent seulement une partie de la capacité totale en ligne et gardent le reste en réserve. Pour la facturation, le nombre de partitions et de réplicas que vous mettez en ligne, calculé à l’aide de la formule SU, détermine le tarif horaire qui vous est facturé.

## <a name="how-to-manage-and-reduce-costs"></a>Comment gérer et réduire les coûts

Outre les suggestions suivantes, voir [Facturation et gestion des coûts](https://docs.microsoft.com/azure/billing/billing-getting-started).

- Créez toutes les ressources dans la même région ou dans le moins de régions possible afin de réduire ou d’éliminer les coûts liés à la bande passante.

- Regroupez tous les services dans un seul groupe de ressources, tel que Recherche cognitive Azure, Cognitive Services et tout autre service Azure utilisé dans votre solution. Dans le portail Azure, recherchez le groupe de ressources et utilisez les commandes **Cost Management** pour obtenir des informations sur les dépenses réelles et prévues.

- Envisagez d’utiliser Application web Azure pour votre application frontale afin que les demandes et réponses restent dans les limites du centre de données.

- Montez en puissance pour les opérations gourmandes en ressources, telles que l’indexation, puis réajustez à la baisse les charges de travail de requête régulières. Commencez avec la configuration minimale pour Recherche cognitive Azure (une unité de stockage composée d’une partition et un réplica), puis surveillez l’activité de l’utilisateur pour identifier des modèles d’utilisation qui indiqueraient un besoin de capacité supplémentaires. Si un modèle est prévisible, vous pouvez peut-être synchroniser l’échelle avec l’activité (vous devez écrire du code pour automatiser ce comportement).

Vous ne pouvez pas arrêter un service de recherche en vue de réduire votre facture. Les ressources dédiées sont toujours opérationnelles et sont allouées pour votre usage exclusif pendant pour la durée de vie de votre service. En ce qui concerne le service lui-même, le seul moyen de réduire votre facture consiste à réduire le nombre de réplicas et de partitions à un niveau offrant garantissant encore des performances acceptables et la [conformité au SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), ou à créer un service de niveau inférieur (les taux horaires du niveau S1 sont inférieurs à ceux des niveaux S2 ou S3). En supposant que vous approvisionniez votre service à la limite inférieure de vos prévisions de charge, si vous dépassez le service, vous pouvez créer un second service de niveau supérieur, régénérer vos index sur ce second service, puis supprimer le premier.

## <a name="how-to-evaluate-capacity-requirements"></a>Comment évaluer les besoins en capacité

Dans Recherche cognitive Azure, la capacité est structurée sous forme de *réplicas* et de *partitions*.

+ Les réplicas sont des instances du service de recherche. Chaque réplica héberge une copie à charge équilibrée d’un index. Par exemple, un service avec six réplicas comporte six copies de chaque index chargé dans le service.

+ Les partitions stockent les index et fractionnent automatiquement les données utilisables dans une requête. Deux partitions fractionnent votre index en deux, trois partitions le scindent en trois, et ainsi de suite. En termes de capacité, la *taille de partition* est la principale caractéristique qui différencie les niveaux.

> [!NOTE]
> Tous les niveaux Standard et À stockage optimisé prennent en charge des [combinaisons flexibles de réplicas et de partitions](search-capacity-planning.md#chart) afin que vous puissiez [optimiser votre système sur le plan du stockage ou de la vitesse](search-performance-optimization.md) en changeant l’équilibrage. Le niveau De base offre jusqu’à trois réplicas pour la haute disponibilité, mais ne comporte qu’une seule partition. Les niveaux Gratuit ne fournissent pas de ressources dédiées : les ressources de calcul sont partagées par plusieurs abonnés.

<!-- ## Consumption patterns

On the low and high ends, Basic and S3 HD are for important but atypical consumption patterns. Basic is for small production workloads. It offers SLAs, dedicated resources, and high availability, but it provides modest storage, topping out at 2 GB total. This tier was engineered for customers that consistently underutilize available capacity. At the high end, S3 HD is for workloads typical of ISVs, partners, [multitenant solutions](search-modeling-multitenant-saas-applications.md), or any configuration that calls for a large number of small indexes. It's often clear when Basic or S3 HD is the right tier. If you want confirmation, you can post to [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) or [contact Azure support](https://azure.microsoft.com/support/options/) for guidance.

The more commonly used standard tiers, S1 through S3, make up a progression of increasing levels of capacity. There are inflection points on partition size and limits on numbers of indexes, indexers, and corollary resources:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partition size|  25 GB | 100 GB | 200 GB |  |  |  |  |
| Index and indexer limits| 50 | 200 | 200 |  |  |  |  |

S1 is a common choice for customers that need dedicated resources and multiple partitions. S1 offers partitions of 25 GB and up to 12 partitions, providing a per-service limit of 300 GB if you maximize partitions over replicas. (See [Allocate partitions and replicas](search-capacity-planning.md#chart) for more balanced allocations.)

The portal and pricing pages put the focus on partition size and storage, but, for each tier, all compute capabilities (disk capacity, speed, CPUs) generally increase linearly with price. An S2 replica is faster than S1, and S3 is faster than S2. S3 tiers break from the linear compute-pricing pattern with disproportionately faster I/O. If you expect I/O to be the bottleneck, keep in mind that you can get much more IOPS with S3 than you can get with lower tiers.

S3 and S3 HD are backed by identical high-capacity infrastructure, but they reach their maximum limits in different ways. S3 targets a smaller number of very large indexes, so its maximum limit is resource-bound (2.4 TB for each service). S3 HD targets a large number of very small indexes. At 1,000 indexes, S3 HD reaches its limits in the form of index constraints. If you're an S3 HD customer and you need more than 1,000 indexes, contact Microsoft Support for information about how to proceed.

> [!NOTE]
> Document limits were a consideration at one time, but they're no longer applicable for new services. For information about conditions in which document limits still apply, see [Document limits](search-limits-quotas-capacity.md#document-limits).
>

Storage Optimized tiers, L1 and L2, are ideal for applications with large data requirements but a relatively low number of end users, when minimizing query latency isn't the top priority.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Partition size|  1 TB | 2 TB |  |  |  |  |  |
| Index and indexer limits| 10 | 10 |  |  |  |  |  |

L2 offers twice the overall storage capacity of L1.  Choose your tier based on the maximum amount of data that you think your index needs. The L1 tier partitions scale up in 1-TB increments to a maximum of 12 TB. The L2 partitions increase by 2 TBs per partition up to a maximum of 24 TB. -->

### <a name="evaluating-capacity"></a>Évaluation de la capacité

La capacité et les coûts d’exécution du service vont de pair. Les niveaux imposent des limites sur deux plans : stockage et ressources. Vous devez considérer les deux à la fois, car la limite que vous atteignez en premier représente la limite effective.

Ce sont généralement les exigences métier qui imposent le nombre d’index dont vous aurez besoin. Par exemple, vous devrez peut-être disposer d’un index global pour un référentiel de documents volumineux. Ou vous pourrez avoir besoin de plusieurs index basés sur la région, l’application ou le créneau commercial.

Pour déterminer la taille d’un index, vous devez en [créer un](search-create-index-portal.md). La structure de données du service Recherche cognitive Azure constitue principalement une structure [d’index inversé](https://en.wikipedia.org/wiki/Inverted_index), dont les caractéristiques diffèrent de celles des données sources. Dans le cas d’un index inversé, la taille et la complexité sont déterminées par le contenu, et non nécessairement par la quantité de données qui l’alimentent. Une source de données volumineuse avec un haut niveau de redondance peut générer un index plus restreint qu’un jeu de données plus modeste présentant un contenu extrêmement variable. Il est donc généralement impossible de déduire la taille de l’index d’après celle du jeu de données d’origine.

> [!NOTE] 
> Même si l’estimation des besoins futurs en matière d’index et de stockage semble très approximative, elle en vaut la peine. Si la capacité d’un niveau se révèle insuffisante, vous devrez approvisionner un nouveau service à un niveau supérieur, puis [recharger vos index](search-howto-reindex.md). Un service ne peut faire l’objet d’aucune mise à niveau sur place d’une référence SKU vers une autre.
>

### <a name="estimate-with-the-free-tier"></a>Estimer avec le niveau gratuit

Une méthode possible pour l’estimation de la capacité consiste à commencer par utiliser le niveau Gratuit. Souvenez-vous que le niveau Gratuit offre jusqu’à 3 index, 50 Mo de stockage et 2 minutes de temps d’indexation. Il peut être difficile d’estimer la taille projetée de l’index avec ces contraintes, mais voici comment procéder :

+ [Créez un service gratuit](search-create-service-portal.md).
+ Préparez un petit jeu de données représentatif.
+ [Générez un index initial sur le portail](search-create-index-portal.md) et notez sa taille. Les fonctionnalités et attributs ont une incidence sur le stockage. Par exemple, l’ajout de suggesteurs (saisie semi-automatique) augmente les besoins en stockage. À l’aide du même jeu de données, vous pouvez tenter de créer plusieurs versions d’un index, avec des attributs différents sur chaque champ, pour voir comment les besoins de stockage varient. Pour plus d’informations, voir [« Implications au niveau du stockage » dans Créer un index de base](search-what-is-an-index.md#storage-implications).

Si vous disposez d’une estimation approximative, vous pouvez doubler cette quantité pour budgéter deux index (développement et production), puis choisir votre niveau en conséquence.

### <a name="estimate-with-a-billable-tier"></a>Estimer avec un niveau facturable

Des ressources dédiées peuvent prendre en charge un échantillonnage et des temps de traitement plus conséquents pour produire des estimations plus réalistes de quantité d’index, de taille et de volume de requête durant le développement. Certains clients démarrent d’emblée avec un niveau facturable, puis réévaluent celui-ci à mesure que le projet de développement murit.

1. [Passez en revue les limites de service de chaque niveau](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) afin de déterminer si les niveaux inférieurs peuvent prendre en charge le nombre d’index dont vous avez besoin. Pour les niveaux De base, S1 et S2, les limites d’index sont respectivement de 15, 50 et 200. Le niveau À stockage optimisé présente une limite de 10 index, car il a été conçu pour prendre en charge un petit nombre d’index très volumineux.

1. [Créez un service à un niveau facturable](search-create-service-portal.md) :

    + Si vous n’êtes pas sûr de la charge projetée, commencez modestement, au niveau De base ou S1.
    + En revanche, si vous savez que vous devrez traiter des charges de requêtes et d’indexation à grande échelle, choisissez dès le départ un niveau élevé, S2 ou même S3.
    + Enfin, si vous indexez une grande quantité de données et que la charge de requêtes est relativement faible, comme dans le cas d’une application métier interne, commencez par le niveau À stockage optimisé L1 ou L2.

1. [Générez un index initial](search-create-index-portal.md) pour déterminer comment les données sources se traduisent par un index. C’est l’unique façon d’estimer la taille de l’index.

1. [Surveillez le stockage, les limites de service, le volume de requêtes et la latence](search-monitor-usage.md) dans le portail. Le portail vous indique le nombre de requêtes par seconde, les requêtes limitées et la latence de recherche. Toutes ces valeurs peuvent vous aider à décider si vous avez sélectionné le niveau adéquat. 

Le nombre d’index et la taille se révèlent tout aussi importants pour votre analyse. En effet, les limites maximales sont atteintes en cas d’utilisation totale du stockage (partitions) ou des limites maximales relatives aux ressources (index, indexeurs et ainsi de suite), selon ce qui se produit en premier. Le portail vous aide à effectuer le suivi de ces deux facteurs en affichant l’utilisation actuelle et les limites maximales côte à côte dans la page de présentation.

> [!NOTE]
> La présence de données superflues dans les documents peut entraîner une augmentation des exigences de stockage. Dans l’idéal, les documents contiennent uniquement les données dont vous avez besoin pour l’expérience de recherche. Les données binaires ne sont pas utilisables dans une requête et doivent être stockées séparément (par exemple dans un stockage Table Azure ou Blob Azure). Un champ doit ensuite être ajouté dans l’index pour conserver une référence URL aux données externes. La taille maximale d’un document individuel est de 16 Mo (ou moins si vous chargez en bloc plusieurs documents dans une seule requête). Pour plus d’informations, consultez [Limites de service dans Recherche cognitive Azure](search-limits-quotas-capacity.md).
>

**Considérations relatives au volume de requêtes**

Le nombre de requêtes par seconde est une métrique importante lors du réglage des performances, mais il ne doit généralement être pris en compte pour le choix du niveau que si vous prévoyez un volume de requêtes élevé dès le départ.

Les niveaux Standard peuvent assurer un équilibre entre le nombre de réplicas et le nombre de partitions. Vous pouvez accélérer l’exécution des requêtes en ajoutant des réplicas pour l’équilibrage de charge ou ajouter des partitions à des fins de traitement parallèle. Vous pouvez ensuite régler les performances une fois le service approvisionné.

Si vous prévoyez des volumes de requêtes élevés et soutenus dès le début, vous devez envisager d’adopter des niveaux Standard plus élevés, qui s’appuient sur du matériel plus puissant. Si ces volumes de requêtes ne surviennent pas, vous pouvez alors mettre des partitions et des réplicas hors connexion, ou même passer à un service de niveau inférieur. Pour plus d’informations sur la façon de calculer le débit de requête, consultez [Performances et optimisation de Recherche cognitive Azure](search-performance-optimization.md).

Les niveaux À stockage optimisé sont utiles pour les charges de travail de données intensives, car ils prennent en charge un stockage d’index total disponible plus important dans les cas où les exigences en matière de latence des requêtes sont moins essentielles. Vous devez toujours utiliser des réplicas supplémentaires pour l’équilibrage de charge, ainsi que des partitions supplémentaires à des fins de traitement parallèle. Vous pouvez ensuite régler les performances une fois le service approvisionné.

**Contrats de niveau de service**

Le niveau Gratuit et les fonctionnalités d’évaluation ne fournissent pas de [Contrats de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Pour tous les niveaux facturables, les SLA entrent en vigueur lorsque vous approvisionnez une redondance suffisante pour votre service. Vous devez disposer d’au moins deux réplicas pour les SLA de requête (lecture). Vous devez posséder au moins trois réplicas pour les SLA de requête et d’indexation (lecture-écriture). Le nombre de partitions n’affecte pas les SLA.

## <a name="tips-for-tier-evaluation"></a>Conseils pour l’évaluation du niveau

+ Autorisez la génération de métriques autour des requêtes et recueillez des données relatives aux modèles d’utilisation (requêtes pendant les heures de bureau, indexation pendant les heures creuses). Utilisez ces données pour faciliter les décisions d’approvisionnement de service. Bien que cela ne soit pas pratique à une cadence horaire ou quotidienne, vous pouvez ajuster les partitions et les ressources de manière dynamique afin de prendre en charge les changements de volumes de requêtes planifiés. Vous pouvez également gérer les changements non planifiés mais soutenus si les niveaux se maintiennent suffisamment longtemps pour que des mesures s’imposent.

+ N’oubliez pas que le seul inconvénient du sous-approvisionnement réside dans le fait que vous devrez peut-être supprimer un service si les exigences réelles sont supérieures à vos prévisions. Pour éviter toute interruption de service, vous devrez créer un service à un niveau supérieur, et l’exécuter côte à côte jusqu’à ce que toutes les applications et requêtes ciblent le nouveau point de terminaison.

## <a name="next-steps"></a>Étapes suivantes

Commencez avec un niveau Gratuit et créez un index initial à l’aide d’un sous-ensemble de vos données afin de bien comprendre ses caractéristiques. La structure de données du service Recherche cognitive Azure est une structure d’index inversé. Le contenu détermine la taille et la complexité d’un index inversé. Souvenez-vous que le contenu hautement redondant a tendance à générer un index plus petit que le contenu très irrégulier. Par conséquent, les exigences en matière de stockage d’index sont déterminées par les caractéristiques du contenu, et non par la taille du jeu de données.

Après avoir effectué une estimation initiale de la taille de votre index, [approvisionnez un service facturable](search-create-service-portal.md) sur l’un des niveaux présentés dans cet article : De base, Standard ou À stockage optimisé. Assouplissez les contraintes artificielles sur le dimensionnement des données et [régénérez votre index](search-howto-reindex.md) afin d’y inclure toutes les données que vous souhaitez rendre utilisables dans une requête.

[Allouez des partitions et des réplicas](search-capacity-planning.md) en fonction des besoins, afin d’obtenir les performances et l’échelle requises.

Si les performances et la capacité conviennent, vous avez terminé. Dans le cas contraire, recréez un service de recherche à un autre niveau correspondant mieux à vos besoins.

> [!NOTE]
> Si vous avez des questions, publiez un message sur le site [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [contactez le support Azure](https://azure.microsoft.com/support/options/).
