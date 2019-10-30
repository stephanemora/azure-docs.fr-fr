---
title: Stratégies de déploiement et meilleures pratiques en matière d’optimisation des performances
titleSuffix: Azure Cognitive Search
description: Découvrez les techniques et les bonnes pratiques pour optimiser les performances de la Recherche cognitive Azure et configurer une mise à l’échelle optimale.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 15557a437732ee15c3c6dada7b2d9fe1d397dc5a
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793417"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-cognitive-search"></a>Stratégies de déploiement et meilleures pratiques en matière d’optimisation des performances dans la Recherche cognitive Azure

Cet article décrit les meilleures pratiques applicables dans le cadre de scénarios avancés présentant des exigences complexes en matière d’extensibilité et de disponibilité. 

## <a name="develop-baseline-numbers"></a>Déterminer des valeurs de référence
Lorsque vous cherchez à optimiser les performances de recherche, vous devez vous concentrer sur la réduction du temps d’exécution des requêtes. Pour ce faire, vous devez savoir à quoi ressemble une charge de requête classique. Les instructions ci-après peuvent vous aider à atteindre les valeurs de requête de référence.

1. Choisissez une latence cible (ou durée maximale) nécessaire à l’exécution d’une recherche standard .
2. Créez et testez une charge de travail réelle avec votre service de recherche en utilisant un jeu de données réaliste pour mesurer les taux de latence.
3. Commencez par un petit nombre de requêtes par seconde, puis augmentez graduellement le nombre d’exécutions dans le test jusqu’à ce que la latence des requêtes soit inférieure à la latence cible définie. Il s’agit d’un test d’évaluation important qui vous aidera à planifier la mise à l’échelle à mesure que l’utilisation de votre application s’intensifie.
4. Dans la mesure du possible, réutilisez les connexions HTTP. Si vous utilisez le Kit de développement logiciel (SDK) .NET Recherche cognitive Azure, cela signifie que vous devez réutiliser une instance ou une instance [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient), et si vous utilisez l’API REST, vous devez réutiliser une instance HttpClient unique.
5. Variez la substance des requêtes afin que la recherche s’effectue sur différentes parties de votre index. Il est important de varier les requêtes, car si vous exécutez continuellement les mêmes requêtes de recherche, la mise en cache des données commencera à offrir de meilleures performances qu’avec un ensemble de requêtes plus disparates.
6. Variez la structure des requêtes afin d’obtenir différents types de requêtes. En effet, le niveau de performance varie selon les requêtes de recherche. Par exemple, une recherche de document ou une suggestion de recherche s’exécute plus rapidement qu’une requête comportant un nombre important de facettes et de filtres. La composition de test doit inclure diverses requêtes, approximativement dans les mêmes proportions que celles que vous rencontreriez en production.  

Lors de la création de ces charges de travail de test, certaines caractéristiques de la Recherche cognitive Azure doivent être prises en compte :

+ L’envoi (push) simultané d’un nombre excessif de requêtes de recherche risque de surcharger votre service. Dans ce cas, vous verrez les codes de réponse HTTP 503. Pour éviter l’obtention du code 503 lors du test, commencez avec différentes plages de requêtes de recherche pour visualiser les différences de taux de latence à mesure que vous ajoutez d’autres requêtes.

+ Le service Recherche cognitive Azure n’exécute pas de tâches d’indexation en arrière-plan. Si votre service gère les charges de travail de requête et d’indexation simultanément, prenez cela en compte en introduisant des travaux d’indexation dans vos tests de requête ou en explorant les options d’exécution des travaux d’indexation pendant les heures creuses.

> [!NOTE]
> Le [test de charge Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) est un excellent moyen d’exécuter vos tests de performances car il permet d’exécuter des requêtes HTTP comme s’il s’agissait de requêtes dans la Recherche cognitive Azure et permet ainsi la parallélisation des requêtes.
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>Montée en charge pour gérer les volumes de requêtes élevés et les requêtes limitées
Lorsque vous recevez un trop grand nombre de requêtes limitées, ou que vous dépassez vos taux de latence cibles suite à une charge de requête accrue, vous pouvez essayer de diminuer les taux de latence de l’une des deux manières suivantes :

1. **Augmenter les réplicas :**  un réplica s’apparente à une copie de vos données qui permet à la Recherche cognitive Azure d’équilibrer la charge des requêtes sur plusieurs copies.  L’intégralité de l’équilibrage de la charge et de la réplication des données entre les réplicas est gérée par la Recherche cognitive Azure et vous pouvez à tout moment modifier le nombre de réplicas alloués à votre service.  Vous pouvez allouer jusqu'à 12 réplicas dans un service de recherche Standard, et 3 dans un service de recherche de base. Les réplicas peuvent être ajustés sur le [portail Azure](search-create-service-portal.md) ou via [PowerShell](search-manage-powershell.md).
2. **Augmenter le niveau de recherche :**  la Recherche cognitive Azure est proposée dans [plusieurs niveaux](https://azure.microsoft.com/pricing/details/search/), chacun d’eux offrant des niveaux de performances différents.  Dans certains cas, vous aurez tant de requêtes que le niveau sélectionné est incapable de fournir des taux de latence suffisamment faibles, même lorsque les réplicas sont optimisés. Dans ce cas, vous pouvez passer à l’un des niveaux de recherche supérieurs, tels que le niveau Recherche cognitive Azure S3, parfaitement adapté aux scénarios impliquant un grand nombre de documents et des charges de travail de requêtes extrêmement élevées.

## <a name="scaling-for-slow-individual-queries"></a>Montée en charge pour gérer les requêtes individuelles lentes
Une requête unique dont l’exécution prend trop de temps peut également entraîner des taux de latence élevés. Dans ce cas, l’ajout de réplicas n’offrira aucune amélioration. Vous pouvez tenter de résoudre ce problème de deux manières :

1. **Augmenter les partitions** Une partition est un mécanisme permettant de répartir vos données sur des ressources supplémentaires. L’ajout d’une seconde partition fractionne les données en deux parties, une troisième partition les fractionne en trois parties, et ainsi de suite. L’un des avantages découlant de cette opération réside dans l’accélération potentielle de l’exécution des requêtes les plus lentes grâce au calcul parallèle. Nous avons constaté une parallélisation sur les requêtes qui affichent une faible sélectivité, telles que les requêtes portant sur de nombreux documents ou les facettes comptabilisant un grand nombre de documents. Dans la mesure où des calculs significatifs sont nécessaires pour évaluer la pertinence des documents ou pour comptabiliser les documents, l’ajout de partitions supplémentaires contribue à accélérer l’exécution des requêtes.  
   
   Il peut y avoir un maximum de 12 partitions dans le service de recherche standard, et 1 partition dans le service de recherche de base.  Les réplicas peuvent être ajustés sur le [portail Azure](search-create-service-portal.md) ou via [PowerShell](search-manage-powershell.md).

2. **Limiter les champs à cardinalité élevée :** un champ à cardinalité élevée est un champ utilisable comme facette ou comme filtre et contenant un grand nombre de valeurs uniques. Ce champ consomme donc une quantité substantielle de ressources lors du calcul des résultats. Par exemple, la définition d’un champ ID produit ou Description en tant que champ utilisable comme facette ou comme filtre est considérée comme une cardinalité élevée, car la plupart des valeurs sont uniques pour chaque document. Dans la mesure du possible, limitez le nombre de champs à cardinalité élevée.

3. **Augmenter le niveau de recherche :**  Choisir un niveau de Recherche cognitive Azure supérieur est une autre façon d’améliorer les performances des requêtes lentes. Chaque niveau supérieur se caractérise par un processeur plus rapide et par une plus grande quantité de mémoire. Ces deux aspects ont un impact positif sur les performances des requêtes.

## <a name="scaling-for-availability"></a>Mise à l’échelle pour la disponibilité
Les réplicas permettent non seulement de réduire la latence des requêtes mais ils peuvent également offrir une plus grande disponibilité. Avec un seul réplica, attendez-vous à des temps d’arrêt périodiques en raison du redémarrage du serveur après les mises à jour logicielles ou lorsque d’autres événements de maintenance se produisent.  Par conséquent, il est important d’évaluer si votre application requiert une haute disponibilité des recherches (requêtes) ainsi que des écritures (événements d’indexation). La Recherche cognitive Azure offre des options de contrat de niveau de service sur tous les services de recherche payants avec les attributs suivants :

* 2 réplicas pour la haute disponibilité des charges de travail en lecture seule (requêtes)
* 3 réplicas (ou plus) pour la haute disponibilité des charges de travail en lecture-écriture (requêtes et indexation)

Pour plus d’informations, consultez le [Contrat de niveau de service de la Recherche cognitive Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Étant donné que les réplicas sont des copies de vos données, l’utilisation de plusieurs réplicas permet au service Recherche cognitive Azure de gérer les redémarrages et la maintenance des machines pour un réplica spécifique, tout en permettant aux requêtes de continuer à s’exécuter sur d’autres réplicas. À l’inverse, si vous retirez des réplicas, vous constaterez une dégradation des performances de requête, en supposant que ces réplicas constituaient des ressources sous-exploitées.

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>Montée en charge pour gérer les charges de travail géodistribuées et la géoredondance
Dans le cas des charges de travail géodistribuées, les utilisateurs éloignés du centre de données qui héberge le service Recherche cognitive Azure font face des taux de latence plus élevés. L’une des manières d’atténuer ce problème consiste à approvisionner plusieurs services de recherche dans des régions plus proches de ces utilisateurs. La Recherche cognitive Azure n’offre actuellement pas de méthode automatisée de géo-réplication des index de Recherche cognitive Azure sur différentes régions, mais quelques techniques permettent d’implémenter et de gérer simplement ce processus. Elles sont présentées dans les sections suivantes.

L’objectif d’un ensemble géodistribué de services de recherche est de disposer de plusieurs index disponibles dans au moins deux régions, et de rediriger l’utilisateur vers le service Recherche cognitive Azure qui offre la plus faible latence, comme illustré dans cet exemple :

   ![Tableau croisé des services par région][1]

### <a name="keeping-data-in-sync-across-multiple-azure-cognitive-search-services"></a>Synchronisation des données entre plusieurs services Recherche cognitive Azure
Il existe deux options pour synchroniser vos services de recherche distribués : à l’aide de [l’indexeur de Recherche cognitive Azure](search-indexer-overview.md) ou de l’API Push (également appelée [API REST de Recherche cognitive Azure](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Mettre à jour le contenu dans plusieurs services à l’aide d’indexeurs

Si vous utilisez déjà un indexeur sur un service unique, vous pouvez configurer un second indexeur sur un deuxième service pour qu’il utilise le même objet de source de données, en extrayant les données au même emplacement. Chacun des services de chaque région dispose de son propre indexeur et d’un index cible (votre index de recherche n’est pas partagé, ce qui signifie que les données sont dupliquées), mais chaque indexeur référence la même source de données.

Voici une vue d’ensemble de l’aspect d’une telle architecture.

   ![Source de données unique avec indexeur distribué et combinaisons de service][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Envoyer (push) les mises à jour de contenu à plusieurs services par le biais d’API REST
Si vous utilisez l’API REST Recherche cognitive Azure pour [envoyer (push) le contenu de votre index Recherche cognitive Azure](https://docs.microsoft.com/rest/api/searchservice/update-index), vous pouvez assurer la synchronisation continue de vos différents services de recherche en envoyant les modifications à tous ces services chaque fois qu’une mise à jour est nécessaire. Dans votre code, veillez à gérer les cas dans lesquels la mise à jour d’un service de recherche échoue, mais réussit pour d’autres services de recherche.

## <a name="leverage-azure-traffic-manager"></a>Exploiter Azure Traffic Manager
[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) vous permet d’acheminer les requêtes vers plusieurs sites géo-localisés et pris en charge par plusieurs services de recherche. Traffic Manager offre l’avantage de pouvoir tester la Recherche cognitive Azure pour vous assurer qu’il est disponible et de rediriger les utilisateurs vers d’autres services de recherche en cas d’interruption du service. En outre, si vous acheminez des requêtes de recherche via des sites Web Azure, Azure Traffic Manager permet d’équilibrer les charges lorsque le site web est opérationnel mais pas la Recherche cognitive Azure. Voici un exemple d’architecture tirant parti de Traffic Manager.

   ![Tableau croisé des services par région, avec Traffic Manager central][3]

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les niveaux tarifaires et les limites de service de chacun d’eux, consultez [Limites de service dans la Recherche cognitive Azure](search-limits-quotas-capacity.md).

Consultez la page [Planification des capacités](search-capacity-planning.md) pour en savoir plus sur les combinaisons de partitions et de réplicas.

Pour plus d’informations sur les performances et obtenir des démonstrations sur la façon d’implémenter les optimisations présentées dans cet article, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
