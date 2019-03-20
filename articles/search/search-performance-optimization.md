---
title: Stratégies de déploiement et les meilleures pratiques pour optimiser les performances - recherche Azure
description: Découvrez les techniques et les bonnes pratiques pour optimiser les performances de Recherche Azure et configurer une mise à l’échelle optimale.
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 8a07657d04cee57cb69c9f5f7862fed3e7965716
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404465"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-search"></a>Stratégies de déploiement et les meilleures pratiques pour optimiser les performances sur Azure Search

Cet article décrit les meilleures pratiques pour les scénarios avancés avec des exigences plus complexes pour l’évolutivité et disponibilité. 

## <a name="develop-baseline-numbers"></a>Développer des numéros de ligne de base
Lors de l’optimisation des performances de recherche, vous devez vous concentrer sur la réduction des temps d’exécution de requête. Pour ce faire, vous devez savoir à quoi ressemble une charge de requête classique. Les instructions suivantes peuvent vous aider à arriver à des valeurs de requête de référence.

1. Choisissez une latence cible (ou durée maximale) nécessaire à l’exécution d’une recherche standard .
2. Créez et testez une charge de travail réelle avec votre service de recherche en utilisant un jeu de données réaliste pour mesurer les taux de latence.
3. Démarrer avec un petit nombre de requêtes par seconde (RPS) et l’augmenter progressivement le nombre d’exécutions dans le test jusqu'à ce que la latence des requêtes descend en dessous la latence cible définie. Il s’agit d’un test d’évaluation important qui vous aidera à planifier la mise à l’échelle à mesure que l’utilisation de votre application s’intensifie.
4. Dans la mesure du possible, réutilisez les connexions HTTP. Si vous utilisez le Kit de développement logiciel (SDK) .NET Azure Search, cela signifie que vous devez réutiliser une instance ou [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) , et si vous utilisez l’API REST, vous devez réutiliser une instance HttpClient unique.
5. Varier la substance de demandes de requête afin que la recherche se produit sur différentes parties de votre index. Variante est important, car si vous exécutez continuellement les mêmes requêtes de recherche, la mise en cache de données commenceront à rendre les performances à paraître meilleures qu’il peut avec une requête plus disparates défini.
6. Modifier la structure de demandes de requête, afin que vous obtenez différents types de requêtes. Pas de chaque requête de recherche s’effectue au même niveau. Par exemple, une suggestion de recherche ou de recherche de document est généralement plus rapide qu’une requête avec un nombre important de facettes et les filtres. Composition de test doit inclure diverses requêtes, à peu près les mêmes rapports comme prévu en production.  

Lors de la création de ces charges de travail de test, certaines caractéristiques d’Azure Search doivent être prises en compte :

+ Il est possible de surcharger votre service en envoyant trop de requêtes de recherche à la fois. Dans ce cas, vous verrez les codes de réponse HTTP 503. Pour éviter un 503 pendant le test, commencer avec différentes plages de requêtes de recherche pour voir les différences de taux de latence lorsque vous ajoutez d’autres requêtes de recherche.

+ Recherche Azure n’exécute pas les tâches d’indexation en arrière-plan. Si votre service gère les interroger et indexer les charges de travail simultanément, cela prendre en compte en l’introduisant l’indexation des travaux dans vos tests de requête, ou en explorant les options d’exécution des travaux d’indexation pendant heures creuses.

> [!NOTE]
> Le [test de charge Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) est un excellent moyen d’exécuter vos tests de performances car il permet d’exécuter des requêtes HTTP comme s’il s’agissait de requêtes dans la Recherche Azure et permet ainsi la parallélisation des requêtes.
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>Mise à l’échelle pour un important volume de requêtes et demandes limitées
Lorsque vous recevez trop de demandes limitées ou dépassez votre taux de latence cible à partir d’une charge accrue des requêtes, vous pouvez chercher à diminuer les taux de latence de deux manières :

1. **Augmenter les réplicas :**  un réplica s’apparente à une copie de vos données qui permet à la Recherche Azure d’équilibrer la charge des requêtes sur plusieurs copies.  L’intégralité de l’équilibrage de la charge et de la réplication des données entre les réplicas est gérée par Azure Search et vous pouvez à tout moment modifier le nombre de réplicas alloués à votre service.  Vous pouvez allouer jusqu'à 12 réplicas dans un service de recherche Standard, et 3 dans un service de recherche de base. Les réplicas peuvent être ajustés sur le [portail Azure](search-create-service-portal.md) ou via [PowerShell](search-manage-powershell.md).
2. **Augmenter le niveau de recherche :**  la Recherche Azure est proposée dans [plusieurs niveaux](https://azure.microsoft.com/pricing/details/search/), chacun d’eux offrant des niveaux de performances différents.  Dans certains cas, vous aurez tant de requêtes que le niveau sélectionné est incapable de fournir des taux de latence suffisamment faibles, même lorsque les réplicas sont optimisés. Dans ce cas, vous souhaiterez en tirant parti d’un des niveaux de recherche supérieurs tel que le niveau de recherche Azure S3 est particulièrement adapté aux scénarios avec un grand nombre de documents et des charges de travail de requêtes extrêmement élevées.

## <a name="scaling-for-slow-individual-queries"></a>Mise à l’échelle pour des requêtes individuelles lentes
Une autre raison pour les taux de latence élevée est une requête unique qui prend trop de temps. Dans ce cas, l’ajout de réplicas ne permet pas. Deux options possibles qui peuvent aider à inclure les éléments suivants :

1. **Augmenter les partitions** Une partition est un mécanisme permettant de répartir vos données sur des ressources supplémentaires. Ajout d’une deuxième partition divise les données en deux, une troisième partition divise en trois et ainsi de suite. Un effet positif est que les requêtes plus lents parfois effectuer plus rapidement en raison de l’informatique parallèle. Nous avons noté parallélisation sur les requêtes de sélectivité faible, telles que les requêtes qui correspondent à nombreux documents ou des facettes de fournir un nombre dans un grand nombre de documents. Étant donné que le calcul significatif est requis pour évaluer la pertinence des documents, ou pour compter le nombre de documents, l’ajout de partitions supplémentaires permet de requêtes se terminer plus rapidement.  
   
   Il peut y avoir un maximum de 12 partitions dans le service de recherche standard, et 1 partition dans le service de recherche de base.  Les réplicas peuvent être ajustés sur le [portail Azure](search-create-service-portal.md) ou via [PowerShell](search-manage-powershell.md).

2. **Limiter les champs à cardinalité élevée :** Un champ à cardinalité élevée se compose d’une à choix multiples ou d’un champ filtrable qui a un nombre important de valeurs uniques et par conséquent, consomme des ressources importantes lors du calcul des résultats. Par exemple, la définition d’un champ de Description ou un ID de produit en tant que facette ou comme filtre compte comme une cardinalité élevée, car la plupart des valeurs à partir du document pour le document est unique. Dans la mesure du possible, limitez le nombre de champs à cardinalité élevée.

3. **Augmenter le niveau de recherche :**  choisir un niveau de Recherche Azure supérieur est une autre façon d’améliorer les performances des requêtes lentes. Chaque niveau supérieur fournit plus rapidement processeurs et plus de mémoire, qui ont toutes deux un impact positif sur les performances de requête.

## <a name="scaling-for-availability"></a>Mise à l’échelle pour la disponibilité
Les réplicas permettent non seulement de réduire la latence des requêtes mais ils peuvent également offrir une plus grande disponibilité. Avec un seul réplica, attendez-vous à des temps d’arrêt périodiques en raison du redémarrage du serveur après les mises à jour logicielles ou lorsque d’autres événements de maintenance se produisent.  Par conséquent, il est important d’évaluer si votre application requiert une haute disponibilité des recherches (requêtes) ainsi que des écritures (événements d’indexation). Azure Search offre des options de contrat de niveau de service sur tous les services de recherche payants avec les attributs suivants :

* 2 réplicas pour la haute disponibilité des charges de travail en lecture seule (requêtes)
* 3 réplicas (ou plus) pour la haute disponibilité des charges de travail en lecture-écriture (requêtes et indexation)

Pour plus d’informations, consultez le [contrat de niveau de service Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Dans la mesure où les réplicas sont des copies de vos données, avoir plusieurs réplicas permet à Azure Search à la machine redémarre et maintenance par rapport à un seul réplica tout en autorisant l’exécution des requêtes continuer en cas d’autres réplicas. À l’inverse, si vous prenez des réplicas de suite, vous allez encourez dégradation des performances de requête, en supposant que ces réplicas ont été une ressources sous-utilisées.

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>Mise à l’échelle pour les charges de travail géo-distribuées et géo-redondance
Pour les charges de travail géo-distribuée, les utilisateurs qui se trouvent loin d’être le centre de données qui héberge Azure Search aura des taux de latence plus élevées. Une atténuation consiste à configurer plusieurs services de recherche dans les régions avec proches de ces utilisateurs. Azure Search n’offre actuellement pas de méthode automatisée de géo-réplication des index Azure Search sur différentes régions, mais quelques techniques permettent d’implémenter et de gérer simplement ce processus. Elles sont présentées dans les sections suivantes.

L’objectif d’un jeu géo-distribuée des services de recherche consiste à avoir deux ou plusieurs index disponibles dans deux ou plusieurs régions où un utilisateur est acheminé vers le service de recherche Azure qui fournit la latence la plus faible, comme illustré dans cet exemple :

   ![Tableau croisé des services par région][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Synchronisation des données entre plusieurs services Azure Search
Il existe deux options pour synchroniser vos services de recherche distribués : à l’aide de [l’indexeur de Recherche Azure](search-indexer-overview.md) ou de l’API Push (également appelée [API REST de Recherche Azure](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Utilisez des indexeurs pour la mise à jour de contenu sur plusieurs services

Si vous utilisez déjà l’indexeur sur un seul service, vous pouvez configurer un indexeur deuxième sur un deuxième service pour utiliser le même objet de source de données, en extrayant des données à partir de l’emplacement. Chaque service dans chaque région a son propre indexeur et un index cible (votre corpus de recherche non partagé, ce qui signifie que les données sont dupliquées), mais chaque indexeur fait référence à la même source de données.

Voici un visuel de haut niveau de quoi ressemblerait cette architecture.

   ![Source de données unique avec indexeur distribué et combinaisons de service][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Utilisez des API REST pour envoyer des mises à jour de contenu sur plusieurs services
Si vous utilisez l’API REST Azure Search à [envoyer le contenu dans votre index Azure Search](https://docs.microsoft.com/rest/api/searchservice/update-index), vous pouvez synchroniser vos différents services de recherche en envoyant les modifications à tous les services de recherche chaque fois qu’une mise à jour est nécessaire. Dans votre code, veillez à gérer les cas où une mise à jour un service de recherche échoue, mais échoue pour d’autres services de recherche.

## <a name="leverage-azure-traffic-manager"></a>Tirer parti d’Azure Traffic Manager
[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) vous permet d’acheminer les requêtes vers plusieurs sites géo-localisés et pris en charge par plusieurs services Azure Search. Traffic Manager offre l’avantage de pouvoir tester Azure Search pour vous assurer qu’il est disponible et de rediriger les utilisateurs vers d’autres services de recherche en cas d’interruption du service. En outre, si vous acheminez des requêtes de recherche via des sites Web Azure, Azure Traffic Manager permet d’équilibrer les charges lorsque le site web est opérationnel mais pas Azure Search. Voici un exemple d’architecture tirant parti de Traffic Manager.

   ![Tableau croisé des services par région, avec Traffic Manager central][3]

## <a name="monitor-performance"></a>Analyser les performances
Recherche Azure offre la possibilité de surveiller et d’analyser les performances de votre service via [analytique du trafic de recherche](search-traffic-analytics.md). Lorsque vous activez cette fonctionnalité et que vous ajoutez une instrumentation à votre application cliente, vous pouvez éventuellement enregistrer les opérations de recherche individuelles ainsi que des mesures agrégées à un compte de stockage Azure qui peut être traité pour l’analyse ou visualisé dans Power BI. Captures de métriques de cette façon fournissent des statistiques de performances comme le nombre moyen de requêtes ou des temps de réponse de requête. En outre, la journalisation des opérations vous permet d’examiner en détail des opérations de recherche spécifique.

Analytique du trafic est utile pour comprendre les taux de latence à partir de ce point de vue de recherche Azure. Comme les mesures de performances des requêtes consignées sont basées sur la durée nécessaire pour qu’une requête soit entièrement traitée dans Azure Search (depuis le moment où elle est demandée jusqu’à son envoi), vous pouvez l’utiliser pour déterminer si les problèmes de latence se produisent du côté du service Azure Search ou à l’extérieur de ce service, par exemple en raison d’une latence du réseau.  

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les niveaux tarifaires et les limites de service de chacun d’eux, consultez [Limites de service d’Azure Search](search-limits-quotas-capacity.md).

Consultez la page [Planification des capacités](search-capacity-planning.md) pour en savoir plus sur les combinaisons de partitions et de réplicas.

Pour plus d’informations sur les performances et obtenir des démonstrations sur la façon d’implémenter les optimisations présentées dans cet article, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
