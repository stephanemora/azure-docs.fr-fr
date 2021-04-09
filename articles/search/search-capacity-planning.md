---
title: Estimer la capacité des charges de travail d’interrogation et d’index
titleSuffix: Azure Cognitive Search
description: Ajustez les ressources informatiques des partitions et des réplicas dans Recherche cognitive Azure, où chaque ressource est facturée en unités de recherche facturables.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: d848c1ed1ab9d4cb24dec9423d93ec62ab45633b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99537219"
---
# <a name="estimate-and-manage-capacity-of-an-azure-cognitive-search-service"></a>Estimer et gérer la capacité d’un service Recherche cognitive Azure

Avant de [provisionner un service de recherche](search-create-service-portal.md) et de choisir un niveau tarifaire particulier, prenez quelques minutes pour bien comprendre le fonctionnement de la capacité et comment vous pouvez ajuster la capacité des réplicas et des partitions pour s’adapter à la fluctuation des charges de travail.

La capacité est une fonction du [niveau de service](search-sku-tier.md), qui permet d’établir un stockage maximal par service, par partition, ainsi que les limites maximales du nombre d’objets que vous pouvez créer. Le niveau De base est conçu pour les applications qui ont des exigences de stockage modestes (une partition uniquement), mais avec la possibilité de s’exécuter dans une configuration à haute disponibilité (trois réplicas). D’autres niveaux sont conçus pour des charges de travail ou des modèles spécifiques, tels que l’architecture mutualisée. En interne, les services créés sur ces niveaux bénéficient d’un matériel qui facilite ces scénarios.

L’architecture de scalabilité dans Recherche cognitive Azure est basée sur des combinaisons flexibles de réplicas et de partitions afin que vous puissiez faire varier la capacité selon que vous avez besoin d’une plus grande puissance d’interrogation ou d’indexation. Lorsqu’un service est créé, vous pouvez augmenter ou diminuer le nombre de réplicas ou de partitions de manière indépendante. Les coûts augmenteront avec chaque ressource physique ajoutée, mais une fois les charges de travail importantes terminées, vous pouvez réduire la taille de votre infrastructure pour faire baisser votre facture. Selon le niveau et la taille de l’ajustement, le processus d’ajout ou de réduction de capacité peut prendre de 15 minutes à plusieurs heures.

Si vous modifiez l’allocation des réplicas et des partitions, nous vous recommandons d’utiliser le portail Azure. Le portail applique des limites aux combinaisons autorisées inférieures aux limites maximales d’un niveau. Toutefois, si vous souhaitez suivre une approche de provisionnement basée sur un script ou du code, [Azure PowerShell](search-manage-powershell.md) et l’[API REST Gestion](/rest/api/searchmanagement/services) constituent des solutions alternatives.

## <a name="concepts-search-units-replicas-partitions-shards"></a>Concepts : unités de recherche, réplicas, partitions, shards

La capacité est exprimée en *unités de recherche* qui peuvent être allouées sous forme de combinaisons de *partitions* et de *réplicas*, en utilisant un mécanisme de *sharding* sous-jacent pour prendre en charge les configurations flexibles :

| Concept  | Définition|
|----------|-----------|
|*Unité de recherche* | Incrément unique de la capacité disponible totale (36 unités). Il s’agit également de l’unité de facturation pour un service Recherche cognitive Azure. Le service a besoin au minimum d’une unité pour s’exécuter.|
|*Réplica* | Instances du service de recherche, principalement utilisées pour équilibrer la charge des opérations de requête. Chaque réplica héberge une copie d’un index. Si vous allouez trois réplicas, vous avez trois copies d’un index disponibles pour traiter les demandes de requête.|
|*Partition* | Stockage physique et E/S pour les opérations de lecture/écriture (par exemple, pendant la reconstruction ou l’actualisation d’un index). Chaque partition contient une section de l’index total. Si vous allouez trois partitions, l’index est divisé en trois. |
|*Shard* | Bloc d’un index. Recherche cognitive Azure divise chaque index en shards pour accélérer le processus d’ajout de partitions (en déplaçant les shards vers de nouvelles unités de recherche).|

Le diagramme suivant montre la relation entre réplicas, partitions, shards et unités de recherche. À travers un exemple, il montre comment un même index est réparti entre quatre unités de recherche dans un service constitué de deux réplicas et deux partitions. Chacune des quatre unités de recherche stocke uniquement la moitié des shards de l’index. Les unités de recherche de la colonne de gauche stockent la première moitié des shards, qui comprend la première partition, tandis que celles situées dans la colonne de droite stockent la deuxième moitié des shards, qui comprend la deuxième partition. Compte tenu de la présence de deux réplicas, il existe deux copies de chaque shard d’index. Les unités de recherche de la ligne du haut stockent une copie, comprenant le premier réplica, tandis que celles de la ligne du bas stockent une autre copie, comprenant le deuxième réplica.

:::image type="content" source="media/search-capacity-planning/shards.png" alt-text="Les index de recherche font l’objet d’un sharding entre les partitions.":::

Le diagramme ci-dessus n’est qu’un exemple parmi d’autres. De nombreuses combinaisons de partitions et de réplicas sont possibles, jusqu’à 36 unités de recherche au maximum.

Dans Recherche cognitive, la gestion de shards est un détail d’implémentation et ne peut pas être configurée, mais le fait de savoir qu’un index fait l’objet d’un sharding aide à comprendre les anomalies occasionnelles dans les comportements de classement et d’autocomplétion :

+ Anomalies de classement : Les scores de recherche sont d’abord calculés au niveau des shards avant d’être agrégés dans un même jeu de résultats. Selon les caractéristiques du contenu des shards, les correspondances d’un shard peuvent être mieux classées que les correspondances d’un autre shard. Si vous remarquez des classements contre-intuitifs dans les résultats de recherche, cela est probablement dû aux effets du partitionnement, tout particulièrement si les index sont de petite taille. Vous pouvez éviter ces anomalies de classement en choisissant de [calculer les scores globalement dans l’index tout entier](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions), mais cela nuira aux performances.

+ Anomalies d’autocomplétion : Les requêtes autocomplétées, où les correspondances portent sur les premiers caractères d’un terme partiellement entré, acceptent un paramètre approximatif qui pardonne les petits écarts orthographiques. Pour l’autocomplétion, la correspondance approximative est limitée aux termes contenus dans le shard actif. Par exemple, si un shard contient « Microsoft » et que le terme partiel « micor » est entré, le moteur de recherche établit une correspondance avec « Microsoft » dans ce shard, mais pas dans ceux qui contiennent les autres parties de l’index.

## <a name="how-to-evaluate-capacity-requirements"></a>Comment évaluer les besoins en capacité

La capacité et les coûts d’exécution du service vont de pair. Les niveaux imposent des limites dans deux catégories : le stockage et le contenu (nombre d’index sur un service, par exemple). Il est important de prendre en compte les deux, car la limite atteinte en premier représente la limite effective.

Les quantités d’index et d’autres objets sont généralement dictées par les exigences commerciales et techniques. Par exemple, vous pouvez avoir plusieurs versions du même index pour le développement actif, les tests et la production.

Les besoins de stockage dépendent de la taille des index que vous prévoyez de générer. Il n’existe pas d’euristique solide ou de généralités qui facilitent les estimations. La seule façon de déterminer la taille d’un index est [d’en créer un](search-what-is-an-index.md). Sa taille repose sur les données importées, l’analyse de texte et la configuration de l’index, notamment si vous activez des suggesteurs, un filtrage et un tri.

Pour la recherche en texte intégral, la structure de données principale constitue une structure d’[index inversé](https://en.wikipedia.org/wiki/Inverted_index), dont les caractéristiques diffèrent de celles des données sources. Dans le cas d’un index inversé, la taille et la complexité sont déterminées par le contenu, et non nécessairement par la quantité de données qui l’alimentent. Une source de données volumineuse avec un haut niveau de redondance peut générer un index plus restreint qu’un jeu de données plus modeste présentant un contenu extrêmement variable. Il est donc généralement impossible de déduire la taille de l’index d’après celle du jeu de données d’origine.

> [!NOTE] 
> Même si l’estimation des besoins futurs en matière d’index et de stockage semble très approximative, elle en vaut la peine. Si la capacité d’un niveau se révèle insuffisante, vous devrez approvisionner un nouveau service à un niveau supérieur, puis [recharger vos index](search-howto-reindex.md). Un service ne peut faire l'objet d'aucune mise à niveau sur place d'un niveau vers un autre.
>

### <a name="estimate-with-the-free-tier"></a>Estimer avec le niveau gratuit

Une méthode possible pour l’estimation de la capacité consiste à commencer par utiliser le niveau Gratuit. Souvenez-vous que le niveau Gratuit offre jusqu’à 3 index, 50 Mo de stockage et 2 minutes de temps d’indexation. Il peut être difficile d’estimer la taille projetée de l’index avec ces contraintes, mais voici comment procéder :

+ [Créez un service gratuit](search-create-service-portal.md).
+ Préparez un petit jeu de données représentatif.
+ Créez un index et chargez vos données. Si le jeu de données peut être hébergé dans une source de données Azure prise en charge par les indexeurs, vous pouvez utiliser l’[Assistant Importer des données dans le portail](search-get-started-portal.md) pour créer et charger l’index. Sinon, vous devez utiliser REST et [Postman](search-get-started-rest.md) ou [Visual Studio Code](search-get-started-vs-code.md) pour créer l’index et envoyer (push) les données. Le modèle Push exige que les données soient sous la forme de documents JSON, où les champs du document correspondent aux champs de l’index.
+ Collectez des informations sur l’index, telles que la taille. Les fonctionnalités et attributs ont une incidence sur le stockage. Par exemple, l’ajout de suggesteurs (requêtes en cours de frappe) augmente les besoins en stockage. 

  À l’aide du même jeu de données, vous pouvez tenter de créer plusieurs versions d’un index, avec des attributs différents sur chaque champ, pour voir comment les besoins de stockage varient. Pour plus d’informations, voir [« Implications au niveau du stockage » dans Créer un index de base](search-what-is-an-index.md#index-size).

Si vous disposez d’une estimation approximative, vous pouvez doubler cette quantité pour budgéter deux index (développement et production), puis choisir votre niveau en conséquence.

### <a name="estimate-with-a-billable-tier"></a>Estimer avec un niveau facturable

Des ressources dédiées peuvent prendre en charge un échantillonnage et des temps de traitement plus conséquents pour produire des estimations plus réalistes de quantité d’index, de taille et de volume de requête durant le développement. Certains clients démarrent d’emblée avec un niveau facturable, puis réévaluent celui-ci à mesure que le projet de développement murit.

1. [Passez en revue les limites de service de chaque niveau](./search-limits-quotas-capacity.md#index-limits) afin de déterminer si les niveaux inférieurs peuvent prendre en charge le nombre d’index dont vous avez besoin. Pour les niveaux De base, S1 et S2, les limites d’index sont respectivement de 15, 50 et 200. Le niveau À stockage optimisé présente une limite de 10 index, car il a été conçu pour prendre en charge un petit nombre d’index très volumineux.

1. [Créez un service à un niveau facturable](search-create-service-portal.md) :

    + Si vous n’êtes pas sûr de la charge projetée, commencez modestement, au niveau De base ou S1.
    + Commencez à un niveau élevé, à S2 ou même S3, si les tests comprennent des charges d’interrogation et d’indexation à grande échelle.
    + Enfin, si vous indexez une grande quantité de données et que la charge de requêtes est relativement faible, comme dans le cas d’une application métier interne, commencez par le niveau À stockage optimisé L1 ou L2.

1. [Générez un index initial](search-what-is-an-index.md) pour déterminer comment les données sources se traduisent par un index. C’est l’unique façon d’estimer la taille de l’index.

1. [Surveillez le stockage, les limites de service, le volume de requêtes et la latence](search-monitor-usage.md) dans le portail. Le portail vous indique le nombre de requêtes par seconde, les requêtes limitées et la latence de recherche. Toutes ces valeurs peuvent vous aider à décider si vous avez sélectionné le niveau adéquat.

1. Ajoutez des réplicas si vous avez besoin d’une haute disponibilité ou si vous constatez un ralentissement des performances d’interrogation.

   Il n’existe aucune instruction sur le nombre de réplicas nécessaires pour prendre en charge les charges d’interrogation. Les performances des requêtes dépendent de la complexité des requêtes et des charges de travail concurrentes. Bien que l’ajout de réplicas entraîne clairement une amélioration de l’évolutivité et des performances, le résultat final n’est pas strictement linéaire : si vous ajoutez trois réplicas, le débit n’est pas forcément multiplié par trois. Pour obtenir de l’aide sur l’estimation du nombre de requêtes par seconde (RPS) pour votre solution, consultez [Mettre à l’échelle pour les performances](search-performance-optimization.md) et [Superviser les requêtes](search-monitor-queries.md).

> [!NOTE]
> Les exigences en matière de stockage peuvent être gonflées si vous incluez des données qui ne seront jamais recherchées. Dans l’idéal, les documents contiennent uniquement les données dont vous avez besoin pour l’expérience de recherche. Les données binaires ne sont pas utilisables dans une requête et doivent être stockées séparément (par exemple dans un stockage Table Azure ou Blob Azure). Un champ doit ensuite être ajouté dans l’index pour conserver une référence URL aux données externes. La taille maximale d’un document de recherche individuel est de 16 Mo (ou moins si vous chargez en bloc plusieurs documents dans une seule requête). Pour plus d’informations, consultez [Limites de service dans Recherche cognitive Azure](search-limits-quotas-capacity.md).
>

**Considérations relatives au volume de requêtes**

Le nombre de requêtes par seconde est une métrique importante lors du réglage des performances, mais il ne doit généralement être pris en compte pour le choix du niveau que si vous prévoyez un volume de requêtes élevé dès le départ.

Les niveaux Standard peuvent assurer un équilibre entre le nombre de réplicas et le nombre de partitions. Vous pouvez accélérer l’exécution des requêtes en ajoutant des réplicas pour l’équilibrage de charge ou ajouter des partitions à des fins de traitement parallèle. Vous pouvez ensuite régler les performances une fois le service approvisionné.

Si vous prévoyez des volumes de requêtes élevés et soutenus dès le début, vous devez envisager d’adopter des niveaux Standard plus élevés, qui s’appuient sur du matériel plus puissant. Si ces volumes de requêtes ne surviennent pas, vous pouvez alors mettre des partitions et des réplicas hors connexion, ou même passer à un service de niveau inférieur. Pour plus d’informations sur la façon de calculer le débit de requête, consultez [Performances et optimisation de Recherche cognitive Azure](search-performance-optimization.md).

Les niveaux À stockage optimisé sont utiles pour les charges de travail de données intensives, car ils prennent en charge un stockage d’index total disponible plus important dans les cas où les exigences en matière de latence des requêtes sont moins essentielles. Vous devez toujours utiliser des réplicas supplémentaires pour l’équilibrage de charge, ainsi que des partitions supplémentaires à des fins de traitement parallèle. Vous pouvez ensuite régler les performances une fois le service approvisionné.

**Contrats de niveau de service**

Le niveau Gratuit et les fonctionnalités d’évaluation ne fournissent pas de [Contrats de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Pour tous les niveaux facturables, les SLA entrent en vigueur lorsque vous approvisionnez une redondance suffisante pour votre service. Vous devez disposer d’au moins deux réplicas pour les SLA de requête (lecture). Vous devez posséder au moins trois réplicas pour les SLA de requête et d’indexation (lecture-écriture). Le nombre de partitions n’affecte pas les SLA.

## <a name="tips-for-capacity-planning"></a>Conseils pour la planification de la capacité

+ Autorisez la génération de métriques autour des requêtes et recueillez des données relatives aux modèles d’utilisation (requêtes pendant les heures de bureau, indexation pendant les heures creuses). Utilisez ces données pour faciliter les décisions d’approvisionnement de service. Bien que cela ne soit pas pratique à une cadence horaire ou quotidienne, vous pouvez ajuster les partitions et les ressources de manière dynamique afin de prendre en charge les changements de volumes de requêtes planifiés. Vous pouvez également gérer les changements non planifiés mais soutenus si les niveaux se maintiennent suffisamment longtemps pour que des mesures s’imposent.

+ N’oubliez pas que le seul inconvénient du sous-approvisionnement réside dans le fait que vous devrez peut-être supprimer un service si les exigences réelles sont supérieures à vos prévisions. Pour éviter toute interruption de service, vous devrez créer un service à un niveau supérieur et l’exécuter côte à côte jusqu’à ce que toutes les applications et requêtes ciblent le nouveau point de terminaison.

## <a name="when-to-add-partitions-and-replicas"></a>Quand ajouter des partitions et des réplicas

Au départ, un service se voit allouer un niveau minimal de ressources consistant en une partition et un réplica.

Un seul service doit avoir suffisamment de ressources pour gérer toutes les charges de travail (indexation et requêtes). Aucune charge de travail ne s’exécute en arrière-plan. Vous pouvez planifier l’indexation à des moments où les demandes de requête sont naturellement moins fréquentes, mais à part cela, le service n’établit pas de priorité entre les tâches. De plus, un certain degré de redondance lisse les performances des requêtes lorsque les services ou les nœuds sont mis à jour en interne.

En règle générale, les applications de recherche tendent à avoir besoin de plus de réplicas que de partitions, en particulier lorsque les opérations de service favorisent les charges de travail de requête. La section [Haute disponibilité](#HA) explique pourquoi.

Le [niveau que vous choisissez](search-sku-tier.md) détermine la taille et la vitesse des partitions, et chaque niveau est optimisé par rapport à un ensemble de caractéristiques adaptées à différents scénarios. Si vous choisissez un niveau tarifaire supérieur, vous aurez sans doute besoin de moins de partitions que si vous optez pour le niveau S1. L’une des questions auxquelles vous devrez répondre au moyen de tests autonomes est de savoir si une partition plus grande et plus coûteuse offre de meilleures performances que deux partitions moins chères sur un service provisionné à un niveau inférieur.

Les applications de recherche nécessitant une actualisation des données en temps réel ou presque ont proportionnellement besoin de plus de partitions que de réplicas. L’ajout de partitions répartit les opérations de lecture/écriture sur un plus grand nombre de ressources de calcul. Il vous offre également davantage d’espace disque pour stocker des documents et des index supplémentaires.

Plus les index sont grands, plus ils sont longs à interroger. Par conséquent, peut-être constaterez-vous que chaque augmentation incrémentielle des partitions nécessite une augmentation plus faible mais proportionnelle des réplicas. La complexité et le volume de vos requêtes auront une incidence sur la vitesse d’exécution des requêtes.

> [!NOTE]
> L’ajout de réplicas ou de partitions augmente le coût d’exécution du service et peut introduire de légères variations dans la façon dont les résultats sont classés. N’oubliez pas d’utiliser la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) pour bien comprendre l’impact de l’ajout de nœuds supplémentaires sur votre facturation. Le [graphique ci-dessous](#chart) peut vous aider à déterminer le nombre d’unités de recherche requises pour une configuration spécifique. Pour plus d’informations sur l’impact des réplicas supplémentaires sur le traitement des requêtes, consultez [Classement des résultats](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Comment allouer des réplicas et partitions

1. Connectez-vous au [portail Azure](https://portal.azure.com/), puis sélectionnez le service de recherche.

1. Sous **Paramètres**, ouvrez la page **Mise à l’échelle** pour modifier les réplicas et partitions. 

   La capture d’écran suivante montre un service De base approvisionné avec un réplica et une partition. La formule en bas indique combien d’unités de recherche sont utilisées (1). Si le prix unitaire était de 100 (prix fictif), le coût mensuel de l’exécution de ce service serait de 100 en moyenne.

   :::image type="content" source="media/search-capacity-planning/1-initial-values.png" alt-text="Page de mise à l’échelle avec les valeurs actuelles" border="true":::

1. Utilisez le curseur pour augmenter ou diminuer le nombre de partitions. La formule en bas indique combien d’unités de recherche utilisées. Sélectionnez **Enregistrer**.

   Cet exemple ajoute un deuxième réplica et une autre partition. Notez le nombre d’unités de recherche. Il est désormais de quatre, car la formule de facturation multiplie le nombre de réplicas par le nombre de partitions (2 x 2). Le doublement de la capacité fait plus que doubler le coût de l’exécution du service. Si le coût d’une unité de recherche était de 100, la nouvelle facture mensuelle serait désormais de 400.

   Pour le coût unitaire de chaque niveau, visitez la [page Tarification](https://azure.microsoft.com/pricing/details/search/).

   :::image type="content" source="media/search-capacity-planning/2-add-2-each.png" alt-text="Ajouter des réplicas et des partitions" border="true":::

1. Après l’enregistrement, vous pouvez vérifier les notifications pour confirmer que l’action a réussi.

   :::image type="content" source="media/search-capacity-planning/3-save-confirm.png" alt-text="Enregistrer les modifications" border="true":::

   La prise d’effet des changements de capacité peut nécessiter quelques heures. Une fois que le processus a démarré, vous ne pouvez pas l’annuler, et il n’y a pas de supervision en temps réel des ajustements de réplicas et de partitions. Toutefois, le message suivant reste visible pendant que les changements prennent effet.

   :::image type="content" source="media/search-capacity-planning/4-updating.png" alt-text="Message de statut dans le portail" border="true":::

> [!NOTE]
> Une fois qu’un service a été provisionné, il ne peut pas être mis à niveau vers un niveau supérieur. Vous devez créer un service de recherche au nouveau niveau et recharger vos index. Pour obtenir des instructions sur l’approvisionnement du service, voir [Créer un service Recherche cognitive Azure dans le portail](search-create-service-portal.md) .
>
> De plus, les partitions et les réplicas sont gérés de manière exclusive et en interne par le service. Il n’existe pas de concept d’affinité du processeur ni d’affectation d’une charge de travail à un nœud spécifique.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>combinaisons de partitions et de réplicas

Un service basique peut avoir exactement une partition et jusqu’à trois réplicas, pour une limite maximale de trois unités de recherche. Les seules ressources ajustables sont les réplicas. Vous devez disposer d’au moins 2 réplicas pour la haute disponibilité sur des requêtes.

Tous les services de recherche Standard et À stockage optimisé peuvent supposer les combinaisons suivantes de réplicas et de partitions, sous réserve de la limite de 36 unités de stockage autorisée pour ces niveaux. 

|   | **1 partition** | **2 partitions** | **3 partitions** | **4 partitions** | **6 partitions** | **12 partitions** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 réplica** |1 unité de recherche |2 unités de recherche |3 unités de recherche |4 unités de recherche |6 unités de recherche |12 unités de recherche |
| **2 réplicas** |2 unités de recherche |4 unités de recherche |6 unités de recherche |8 unités de recherche |12 unités de recherche |24 unités de recherche |
| **3 réplicas** |3 unités de recherche |6 unités de recherche |9 unités de recherche |12 unités de recherche |18 unités de recherche |36 unités de recherche |
| **4 réplicas** |4 unités de recherche |8 unités de recherche |12 unités de recherche |16 unités de recherche |24 unités de recherche |N/A |
| **5 réplicas** |5 unités de recherche |10 unités de recherche |15 unités de recherche |20 unités de recherche |30 unités de recherche |N/A |
| **6 réplicas** |6 unités de recherche |12 unités de recherche |18 unités de recherche |24 unités de recherche |36 unités de recherche |N/A |
| **12 réplicas** |12 unités de recherche |24 unités de recherche |36 unités de recherche |N/A |N/A |N/A |

Les unités de recherche, leur tarification et leur capacité sont détaillées sur le site web Azure. Pour plus d'informations, consultez la rubrique [Tarification](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Le nombre de réplicas et de partitions est divisible par 12 de manière égale (plus précisément, 1, 2, 3, 4, 6, 12). Recherche cognitive Azure divise au préalable chaque index en 12 partitions pour que celles-ci puissent être réparties équitablement sur plusieurs partitions. Par exemple, si votre service comporte trois partitions et que vous créez un index, chaque partition contiendra quatre partitions de l'index. Le partitionnement d’un index réalisé par la Recherche cognitive Azure est un détail d'implémentation susceptible d’être modifié dans des futures versions. Le nombre de partitions (12 à l’heure actuelle) peut être, à l’avenir, totalement différent.
>

<a id="HA"></a>

## <a name="high-availability"></a>Haute disponibilité

Nous vous recommandons généralement de démarrer avec une partition et un ou deux réplicas, puis de monter en puissance à mesure que les volumes de requête se créent. Les charges de travail de requêtes s’exécutent principalement sur des réplicas. Si vous nécessitez une haute disponibilité ou un débit plus important, vous aurez probablement besoin de réplicas supplémentaires.

Recommandations générales pour la haute disponibilité :

+ Deux réplicas pour la haute disponibilité des charges de travail en lecture seule (requêtes)

+ Trois réplicas minimum pour la haute disponibilité des charges de travail en lecture/écriture (les requêtes et l’indexation en tant que documents individuels sont ajoutées, mises à jour ou supprimées)

Les contrats de niveau de service (SLA) pour la Recherche cognitive Azure sont ciblés au moment des opérations de requête et des mises à jour d’index qui se composent d’ajout, de mise à jour ou de suppression de documents.

Le niveau De base est plafonné à une partition et trois réplicas. Si vous souhaitez pouvoir répondre immédiatement aux fluctuations de la demande sur le plan de l’indexation et du débit des requêtes, songez à passer à l’un des niveaux Standard.  Si vous trouvez que vos besoins de stockage augmentent beaucoup plus rapidement que votre débit de requête, envisagez l’un des niveaux de stockage optimisé.

## <a name="about-queries-per-second-qps"></a>À propos des requêtes par seconde

En raison du grand nombre de facteurs intervenant dans les performances des requêtes, Microsoft ne publie pas les nombres attendus de requêtes par seconde. Les estimations de requêtes par seconde doivent être développées indépendamment par chaque client à l’aide des constructions de niveau de service, de configuration, d’index et de requête valides pour votre application. La taille et la complexité des index et des requêtes ainsi que la quantité de trafic sont les principaux facteurs qui déterminent le nombre de requêtes par seconde. Si ces facteurs sont inconnus, il est impossible d’établir des estimations significatives.

Les estimations sont plus prévisibles si elles sont calculées sur des services qui s’exécutent sur des ressources dédiées (niveaux De base et Standard). Vous pouvez mieux estimer les requêtes par seconde, car vous contrôlez davantage de paramètres. Pour obtenir de l’aide sur la manière d’aborder les estimations, consultez [Performances et optimisation de Recherche cognitive Azure](search-performance-optimization.md).

Pour les niveaux à stockage optimisé (L1 et L2), attendez-vous à un plus faible débit des requêtes et à une latence plus élevée que les niveaux Standard.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Comment estimer et gérer les coûts](search-sku-manage-costs.md)