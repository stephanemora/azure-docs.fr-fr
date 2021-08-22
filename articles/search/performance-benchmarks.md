---
title: Test d’évaluation des performances
titleSuffix: Azure Cognitive Search
description: En savoir plus sur les performances de Recherche cognitive Azure par le biais de différents tests de performances
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 03276e72224c8ddefa80358c4214893fd3363c96
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122533115"
---
# <a name="azure-cognitive-search-performance-benchmarks"></a>Tests d’évaluation des performances de Recherche cognitive Azure

Les performances de Recherche cognitive Azure dépendent de [divers facteurs](search-performance-tips.md), notamment la taille de votre service de recherche et les types de requêtes que vous envoyez. Pour vous aider à estimer la taille du service de recherche nécessaire pour votre charge de travail, nous avons exécuté plusieurs tests pour documenter les performances des différents services et configurations de recherche. Ces tests ne garantissent en aucun cas un certain niveau de performances de votre service, mais peuvent vous aider à vous faire une idée des performances que vous pouvez attendre.

Pour couvrir un maximum de cas d’utilisation différents, nous avons exécuté des tests d’évaluation pour deux scénarios principaux :

* **Recherche e-commerce** : ce test d’évaluation émule un véritable scénario e-commerce et est basé sur la société de commerce électronique nordique [CDON](https://cdon.com).
* **Recherche de documents** : ce scénario se compose d’une recherche par mot clé sur des documents de texte intégral provenant de [Semantic Scholar](https://www.aclweb.org/anthology/2020.acl-main.447/). Elle émule une solution de recherche de documents classique.

Bien que ces scénarios reflètent des cas d’utilisation différents, chaque scénario est unique. Nous recommandons donc toujours de tester les performances de votre charge de travail individuelle. Nous avons publié une [solution de test des performances à l’aide de JMeter](https://github.com/Azure-Samples/azure-search-performance-testing) pour vous permettre d’exécuter des tests similaires sur votre propre service.

## <a name="testing-methodology"></a>Méthodologie de test

Pour évaluer les performances de Recherche cognitive Azure, nous avons exécuté des tests pour deux scénarios différents dans différentes combinaisons de niveaux et de réplicas/partitions.

Pour créer ces tests d’évaluation, la méthodologie suivante a été utilisée :

1. Le test commence à `X` requêtes par seconde (RPS) pendant 180 secondes. Nous avons généralement utilisé 5 ou 10 RPS.
2. Les RPS sont ensuite augmentées de `X` et exécutées pendant une durée supplémentaire de 180 secondes
3. Toutes les 180 secondes, le test augmente les RPS de `X` jusqu’à ce que la latence moyenne passe au-dessus de 1000 ms ou que moins de 99 % des requêtes réussissent.

Le graphique ci-dessous donne un exemple visuel de la charge en requêtes du test :

![Exemple de test](./media/performance-benchmarks/example-test.png)

Chaque scénario a utilisé au moins 10 000 requêtes uniques pour éviter que les tests soient trop faussés par la mise en cache.

> [!IMPORTANT]
> Ces tests incluent uniquement des charges de travail de requête. Si vous envisagez de disposer d’un volumne élevé d’opérations d’indexation, veillez à les factoriser dans vos tests d’estimation et de performances. Vous trouverez des exemples de code pour simuler l’indexation dans ce [tutoriel](tutorial-optimize-indexing-push-api.md).

### <a name="definitions"></a>Définitions

- **Nombre maximal de RPS** : le nombre maximal de RPS ci-dessous est basé sur le nombre de RPS le plus élevé atteint dans un test où 99 % des requêtes se sont terminées avec succès sans limitation et la latence moyenne est restée sous 1000 ms.

- **Pourcentage du nombre maximal de RPS** : un pourcentage du nombre maximal de RPS atteint pour un test particulier. Par exemple, si un test donné a atteint un maximum de 100 RPS, 20 % du nombre maximal de RPS serait de 20 RPS.

- **Latence** : la latence du serveur pour une requête. Ces chiffres ne comprennent pas le [délai d’aller-retour (RTT)](https://en.wikipedia.org/wiki/Round-trip_delay). Les valeurs ci-dessous sont exprimées en millisecondes (ms).

### <a name="disclaimer"></a>Clause d'exclusion de responsabilité

Le code que nous avons utilisé pour exécuter ces tests d’évaluation est disponible [ici](https://github.com/Azure-Samples/azure-search-performance-testing/tree/main/other_tools). Il est à noter que nous avons observé des niveaux de RPS légèrement inférieurs avec la [solution de test de performances JMeter](https://github.com/Azure-Samples/azure-search-performance-testing) que dans les tests ci-dessous. Les différences peuvent être attribuées aux différences dans le style des tests. Cela implique l’importance de vous assurer que vos tests de performances sont similaires à votre charge de travail de production.

Ces tests ne garantissent en aucun cas un certain niveau de performances de votre service, mais peuvent vous aider à vous faire une idée des performances que vous pouvez attendre sur la base de votre scénario.

N’hésitez pas à nous contacter à l’adresse azuresearch_contact@microsoft.com si vous avez des questions ou des préoccupations.

## <a name="benchmark-1-e-commerce-search"></a>Test 1 : Recherche e-commerce

:::row:::
   :::column span="1":::
      ![CDON Logo](./media/performance-benchmarks/cdon-logo-160px2.png)
   :::column-end:::
   :::column span="3":::
      Ce test d’évaluation a été créé en partenariat avec la société d’e-commerce [CDON](https://cdon.com), le plus grand marché en ligne de la région nordique, avec des opérations en Suède, en Finlande, en Norvège et au Danemark. Par le biais de ses 1 500 commerçants, CDON offre un assortiment large qui inclut plus de 8 millions de produits. En 2020, CDON a eu plus de 120 millions de visiteurs et 2 millions de clients actifs. Vous pouvez en apprendre plus sur l’utilisation de Recherche cognitive Azure par CDON dans [cet article](https://pulse.microsoft.com/transform/na/fa1-how-cdon-has-been-using-technology-to-become-the-leading-marketplace-in-the-nordics/).
   :::column-end:::
:::row-end:::

Pour exécuter ces tests, nous avons utilisé un instantané de l’index de recherche de production de CDON et de milliers de requêtes uniques à partir de leur [site web](https://cdon.com).

### <a name="scenario-details"></a>Détails sur le scénario

- **Nombre de documents** : 6 000 000 
- **Taille de l’index** : 20 Go
- **Schéma d’index** : un index étendu avec 250 champs au total, 25 champs pouvant faire l’objet d’une recherche et 200 champs étant à choix multiples/filtrables
- **Types de requêtes** : requêtes de recherche en texte intégral incluant des choix multiples, des filtres, des commandes et des profils de scoring

### <a name="s1-performance"></a>Performances S1

#### <a name="queries-per-second"></a>Requêtes par seconde

Le tableau ci-dessous montre la charge de requêtes la plus élevée qu’un service peut traiter pendant une période prolongée en requêtes par seconde (RPS).

![Nombre de RPS maintenable le plus élevé pour l’e-commerce s1](./media/performance-benchmarks/s1-ecom-qps.png)

#### <a name="query-latency"></a>Latence des requêtes

La latence des requêtes varie en fonction de la charge du service, et les services soumis à une forte sollicitation auront une latence moyenne de requête plus élevée. Le tableau ci-dessous montre les 25e, 50e, 75e, 90e, 95e et 99e centiles de la latence des requêtes pour trois niveaux d’utilisation différents.

| Pourcentage du nombre maximal de RPS  | Latence moyenne | 25% | 75 % | 90% | 95 % | 99 %|
|---|---|---|---| --- | --- | --- | 
| 20%  | 104 ms  | 35 ms  | 115 ms   | 177 ms | 257 ms | 738 ms |
| 50%  | 140 ms  | 47 ms  | 144 ms   | 241 ms | 400 ms | 1175 ms |
| 80 %  | 239 ms  | 77 ms  | 248 ms   | 466 ms | 763 ms | 1752 ms | 


### <a name="s2-performance"></a>Performances S2

#### <a name="queries-per-second"></a>Requêtes par seconde

Le tableau ci-dessous montre la charge de requêtes la plus élevée qu’un service peut traiter pendant une période prolongée en requêtes par seconde (RPS).

![Nombre de RPS maintenable le plus élevé pour l’e-commerce s2](./media/performance-benchmarks/s2-ecom-qps.png)

#### <a name="query-latency"></a>Latence des requêtes

La latence des requêtes varie en fonction de la charge du service, et les services soumis à une forte sollicitation auront une latence moyenne de requête plus élevée. Le tableau ci-dessous montre les 25e, 50e, 75e, 90e, 95e et 99e centiles de la latence des requêtes pour trois niveaux d’utilisation différents.

| Pourcentage du nombre maximal de RPS  | Latence moyenne | 25% | 75 % | 90% | 95 % | 99 %|
|---|---|---|---| --- | --- | --- | 
| 20%  | 56 ms | 21 ms | 68 ms  | 106 ms  | 132 ms | 210 ms | 
| 50%  | 71 ms  | 26 ms  | 83 ms   | 132 ms | 177 ms | 329 ms |
| 80 %  | 140 ms  | 47 ms  | 153 ms   | 293 ms | 452 ms | 924 ms | 

### <a name="s3-performance"></a>Performances S3

#### <a name="queries-per-second"></a>Requêtes par seconde

Le tableau ci-dessous montre la charge de requêtes la plus élevée qu’un service peut traiter pendant une période prolongée en requêtes par seconde (RPS).

![Nombre de RPS maintenable le plus élevé pour l’e-commerce s3](./media/performance-benchmarks/s3-ecom-qps.png)

Dans ce cas, nous constatons que l’ajout d’une deuxième partition augmente considérablement le nombre maximal de RPS, mais l’ajout d’une troisième partition permet de réduire le nombre de retours marginaux. L’amélioration plus modeste est probablement due au fait que toutes les données sont déjà extraites dans la mémoire active de S3 avec seulement deux partitions.

#### <a name="query-latency"></a>Latence des requêtes

La latence des requêtes varie en fonction de la charge du service, et les services soumis à une forte sollicitation auront une latence moyenne de requête plus élevée. Le tableau ci-dessous montre les 25e, 50e, 75e, 90e, 95e et 99e centiles de la latence des requêtes pour trois niveaux d’utilisation différents.

| Pourcentage du nombre maximal de RPS  | Latence moyenne | 25% | 75 % | 90% | 95 % | 99 %|
|---|---|---|---| --- | --- | --- |
| 20%  | 50 ms  | 20 ms  | 64 ms   | 83 ms | 98 ms | 160 ms |
| 50%  | 62 ms  | 24 ms  | 80 ms   | 107 ms | 130 ms | 253 ms |
| 80 %  | 115 ms  | 38 ms  | 121 ms   | 218 ms | 352 ms | 828 ms |

## <a name="benchmark-2-document-search"></a>Test 2 : Recherche de documents

### <a name="scenario-details"></a>Détails sur le scénario

- **Nombre de documents** : 7,5 millions
- **Taille de l’index** : 22 Go
- **Schéma d’index** : 23 champs, 8 pouvant faire l’objet d’une recherche, 10 filtrables/à choix multiples
- **Types de requêtes** : recherches par mot clé avec choix multiple et mise en surbrillance des correspondances

### <a name="s1-performance"></a>Performances S1

#### <a name="queries-per-second"></a>Requêtes par seconde

Le tableau ci-dessous montre la charge de requêtes la plus élevée qu’un service peut traiter pendant une période prolongée en requêtes par seconde (RPS).

![Nombre de RPS le plus élevé maintenable pour la recherche de documents s1](./media/performance-benchmarks/s1-docsearch-qps.png)

#### <a name="query-latency"></a>Latence des requêtes

La latence des requêtes varie en fonction de la charge du service, et les services soumis à une forte sollicitation auront une latence moyenne de requête plus élevée. Le tableau ci-dessous montre les 25e, 50e, 75e, 90e, 95e et 99e centiles de la latence des requêtes pour trois niveaux d’utilisation différents.

| Pourcentage du nombre maximal de RPS  | Latence moyenne | 25% | 75 % | 90% | 95 % | 99 %|
|---|---|---|---| --- | --- | --- |
| 20%  | 67 ms  | 44 ms  | 77 ms   | 103 ms | 126 ms | 216 ms |
| 50%  | 93 ms  | 59 ms  | 110 ms   | 150 ms | 184 ms | 304 ms |
| 80 %  | 150 ms  | 96 ms  | 184 ms   | 248 ms | 297 ms | 424 ms |

### <a name="s2-performance"></a>Performances S2

#### <a name="queries-per-second"></a>Requêtes par seconde

Le tableau ci-dessous montre la charge de requêtes la plus élevée qu’un service peut traiter pendant une période prolongée en requêtes par seconde (RPS).

![Nombre de RPS le plus élevé maintenable pour la recherche de documents s2](./media/performance-benchmarks/s2-docsearch-qps.png)

#### <a name="query-latency"></a>Latence des requêtes

La latence des requêtes varie en fonction de la charge du service, et les services soumis à une forte sollicitation auront une latence moyenne de requête plus élevée. Le tableau ci-dessous montre les 25e, 50e, 75e, 90e, 95e et 99e centiles de la latence des requêtes pour trois niveaux d’utilisation différents.

| Pourcentage du nombre maximal de RPS  | Latence moyenne | 25% | 75 % | 90% | 95 % | 99 %|
|---|---|---|---| --- | --- | --- |
| 20%  | 45 ms  | 31 ms  | 55 ms   | 73 ms | 84 ms | 109 ms |
| 50%  | 63 ms  | 39 ms  | 81 ms   | 106 ms | 123 ms | 163 ms |
| 80 %  | 115 ms  | 73 ms  | 145 ms   | 191 ms | 224 ms | 291 ms |

### <a name="s3-performance"></a>Performances S3

#### <a name="queries-per-second"></a>Requêtes par seconde

Le tableau ci-dessous montre la charge de requêtes la plus élevée qu’un service peut traiter pendant une période prolongée en requêtes par seconde (RPS).

![Nombre de RPS le plus élevé maintenable pour la recherche de documents s3](./media/performance-benchmarks/s3-docsearch-qps.png)

#### <a name="query-latency"></a>Latence des requêtes

La latence des requêtes varie en fonction de la charge du service, et les services soumis à une forte sollicitation auront une latence moyenne de requête plus élevée. Le tableau ci-dessous montre les 25e, 50e, 75e, 90e, 95e et 99e centiles de la latence des requêtes pour trois niveaux d’utilisation différents.

| Pourcentage du nombre maximal de RPS  | Latence moyenne | 25% | 75 % | 90% | 95 % | 99 %|
|---|---|---|---| --- | --- | --- |
| 20%  | 43 ms  | 29 ms  | 53 ms   | 74 ms | 86 ms | 111 ms |
| 50%  | 65 ms  | 37 ms  | 85 ms   | 111 ms | 128 ms | 164 ms |
| 80 %  | 126 ms  | 83 ms  | 162 ms   | 205 ms | 233 ms | 281 ms |

## <a name="takeaways"></a>Éléments importants à retenir

Grâce à ces tests, vous pouvez vous faire une idée des performances de Recherche cognitive Azure. Vous pouvez également voir la différence entre les services à différents niveaux.

Voici quelques-uns des points à retenir de ces tests :

* S2 peut généralement gérer au moins quatre fois le volume de requêtes par rapport à S1
* S2 aura généralement une latence inférieure à celle de S1 à des volumes de requêtes comparables
* À mesure que vous ajoutez des réplicas, le nombre de RPS qu’un service peut gérer est généralement mis à l’échelle de manière linéaire (par exemple, si un réplica peut gérer 10 RPS, alors cinq réplicas peuvent généralement gérer 50 RPS)
* Plus la charge sur le service est élevée, plus la latence moyenne sera élevée

Vous pouvez également voir que les performances peuvent varier considérablement entre les scénarios. Si vous n’obtenez pas les performances attendues, consultez les [conseils pour obtenir de meilleures performances](search-performance-tips.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez vu les tests de performances, vous pouvez en apprendre plus sur l’analyse des performances et les facteurs clés de Recherche cognitive qui influencent les performances.

> [!div class="nextstepaction"]
> [Analyser les performances](search-performance-analysis.md)
> [Conseils pour de meilleures performances](search-performance-tips.md)
