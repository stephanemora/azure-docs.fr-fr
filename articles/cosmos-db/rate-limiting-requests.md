---
title: Optimiser votre application Azure Cosmos DB en utilisant la limitation du débit
description: Cet article fournit aux développeurs une méthodologie pour limiter le taux des demandes envoyées à Azure Cosmos DB. Implémenter ce modèle peut réduire les erreurs et améliorer les performances globales pour les charges de travail qui dépassent le débit provisionné de la base de données ou du conteneur cible.
author: plasne
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/07/2021
ms.author: pelasne
ms.openlocfilehash: f9130808da0f833246e013e56f081b92fa461ac9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562155"
---
# <a name="optimize-your-azure-cosmos-db-application-using-rate-limiting"></a>Optimiser votre application Azure Cosmos DB en utilisant la limitation du débit

Cet article fournit aux développeurs une méthodologie pour limiter le taux des demandes envoyées à Azure Cosmos DB. Implémenter ce modèle peut réduire les erreurs et améliorer les performances globales pour les charges de travail qui dépassent le débit provisionné de la base de données ou du conteneur cible.

Les demandes qui dépassent le débit approvisionné dans Azure Cosmos DB peuvent entraîner des erreurs temporaires telles que [TooManyRequests](troubleshoot-request-rate-too-large.md), [Timeout](troubleshoot-request-timeout.md) et [ServiceUnavailable](troubleshoot-service-unavailable.md). En règle générale, vous renouvelez ces requêtes lorsque la capacité est disponible et adaptée. Mais cette approche peut entraîner un grand nombre de requêtes qui suivent le chemin d’erreur dans votre code, avec généralement une réduction du débit.

Les performances optimales du système, mesurées en termes de coût et de temps, peuvent être obtenues en faisant correspondre le trafic de la charge de travail côté client au débit approvisionné côté serveur.

Examinez le cas suivant :

* Vous approvisionnez Azure Cosmos DB avec 20 000 RU/seconde.
* Votre application traite un travail d’ingestion qui contient 10 000 enregistrements, chacun d’entre eux coûtant 10 RU. La capacité totale requise pour effectuer ce travail est de 100 000 RU.
* Si vous envoyez l’intégralité du travail à Azure Cosmos DB, vous devez vous attendre à un grand nombre d’erreurs temporaires et à un important tampon de requêtes que vous devez réessayer. Cette condition est due au fait que le nombre total de RU requises pour le travail (100 000) est bien supérieur à la valeur maximale configurée (20 000). ~ 2 000 de ces enregistrements seront acceptés dans la base de données, mais ~ 8 000 seront rejetés. Vous allez envoyer ~ 8 000 enregistrements à Azure Cosmos DB lors d’une nouvelle tentative, dont ~ 2 000 seront acceptées, et ainsi de suite. Vous devez vous attendre à ce que ce modèle envoie ~ 30 000 enregistrements au lieu de 10 000.
* Si vous envoyez ces requêtes uniformément pendant 5 secondes, vous ne devez pas vous attendre à une défaillance et à un débit global plus rapide, car chaque lot contient 20 000 requêtes ou moins.

La répartition des requêtes sur une période donnée peut être effectuée en introduisant un mécanisme de limitation du débit dans votre code.

Les RU approvisionnées pour un conteneur seront partagées uniformément entre le nombre de partitions physiques. Dans l’exemple ci-dessus, si Azure Cosmos DB a approvisionné deux partitions physiques, chaque partition aura 10 000 RU.

Pour plus d’informations sur les unités de requête, consultez [Unités de requête dans Azure Cosmos DB](request-units.md).
Pour plus d’informations sur l’estimation du nombre de RU consommées par votre charge de travail, consultez [Considérations relatives aux unités de requête](request-units.md#request-unit-considerations).
Pour plus d’informations sur le partitionnement Azure Cosmos DB, consultez [Partitionnement et mise à l’échelle horizontale dans Azure Cosmos DB](partitioning-overview.md).

## <a name="methodology"></a>Méthodologie

Une approche de l’implémentation de la limitation du débit peut se présenter comme suit :

1. Créez un profil de votre application afin d’obtenir des données sur les écritures et les requêtes de lecture utilisées.
1. Définissez tous les index.
1. Remplissez la collection avec une quantité raisonnable de données (il peut s’agir d’exemples de données). Si votre application contient normalement des millions d’enregistrements, remplissez-la avec des millions d’enregistrements.
1. Écrivez vos documents représentatifs et notez le coût en RU.
1. Exécutez vos requêtes représentatives et notez le coût en RU.
1. Implémentez une fonction dans votre application pour déterminer le coût d’une requête donnée en fonction de vos résultats.
1. Implémentez un mécanisme de limitation du débit dans votre code pour vous assurer que la somme de toutes les opérations envoyées à Azure Cosmos DB en une seconde ne dépasse pas votre débit approvisionné.
1. Testez la charge de votre application et vérifiez que vous ne dépassez pas le débit approvisionné.
1. Testez régulièrement les coûts des RU et mettez à jour votre fonction de coût si nécessaire.

## <a name="indexing"></a>Indexation

Contrairement à d’autres bases de données SQL et NoSQL que vous connaissez peut-être, la stratégie d’indexation par défaut d’Azure Cosmos DB pour les conteneurs récemment créés indexe **chaque** propriété. Chaque propriété indexée augmente le coût en RU des écritures.

La politique d’indexation par défaut peut réduire la latence dans les systèmes à lecture intensive où les conditions de filtrage des requêtes sont bien réparties sur tous les champs stockés. Par exemple, cela peut être efficace sur les systèmes où Azure Cosmos DB passe la majeure partie de son temps à servir des recherches ad hoc élaborées par l’utilisateur final.

Vous souhaiterez peut-être exclure les propriétés qui ne font jamais l’objet d’une recherche d’index. La suppression de propriétés de l'index pourrait améliorer les performances globales du système (en coût et en temps) pour les systèmes à forte densité d'écriture et les modèles de recherche d'enregistrements plus contraignants.

Avant de mesurer les coûts, vous devez configurer intentionnellement une stratégie d’indexation appropriée à vos cas d'utilisation. En outre, si vous changez d’indice par la suite, vous devrez refaire tous les calculs de coûts. 

Dans la mesure du possible, tester un système en cours de développement avec une charge reflétant des requêtes typiques dans des conditions de demande normale et de pointe permettra de déterminer la stratégie d’indexation à utiliser.

Pour plus d’informations sur les index, consultez [Stratégies d’indexation dans Azure Cosmos DB](index-policy.md).

## <a name="measuring-cost"></a>Mesure du coût

Il existe quelques concepts clés pour mesurer les coûts :

* Prenez en compte tous les facteurs qui affectent l’utilisation des RU, comme décrit dans [Considérations relatives aux unités de requête](request-units.md#request-unit-considerations).
* Toutes les opérations de lecture et d’écriture dans votre base de données ou conteneur partagent le même débit approvisionné.
* La consommation de RU est déduite, quelles que soient les API Azure Cosmos DB utilisées.
* La stratégie de partition d’un regroupement peut avoir un impact significatif sur le coût d’un système. Pour plus d’informations, consultez [Partitionnement et mise à l’échelle horizontale dans Azure Cosmos DB](partitioning-overview.md#choose-partitionkey).
* Utilisez des documents représentatifs et des requêtes représentatives.
  * Il s’agit des documents et des requêtes que vous votre système opérationnel devrait utiliser.
  * La meilleure façon d’obtenir ces documents et requêtes représentatifs consiste à instrumenter l’utilisation de votre application. Il est toujours préférable de prendre une décision en s’appuyant sur des données.
* Mesurez les coûts régulièrement.
  * Les modifications d’index et la taille des index peuvent avoir un impact sur le coût. 
  * Il sera utile de créer un test reproductible (peut-être même automatisé) des documents et des requêtes représentatifs.
  * Vérifiez que vos documents et requêtes représentatifs sont toujours représentatifs.

La méthode permettant de déterminer le coût d’une requête est différente pour chaque API :

* [API Core](find-request-unit-charge.md)
* [API Cassandra](cassandra/find-request-unit-charge-cassandra.md)
* [API Gremlin](find-request-unit-charge-gremlin.md)
* [API MongoDB](mongodb/find-request-unit-charge-mongodb.md)
* [API de table](table/find-request-unit-charge.md)

## <a name="write-requests"></a>Demandes d’écriture

Le coût des opérations d’écriture tend à être facile à prédire. Vous allez insérer des enregistrements et documenter le coût signalé par Azure Cosmos.

Si vous avez des documents de différentes tailles et/ou de documents qui utiliseront des index différents, il est important de tous les mesurer.
Vous constaterez peut-être que vos documents représentatifs sont suffisamment proches pour vous permettre d’attribuer une valeur unique à toutes les écritures.
Par exemple, si vous avez trouvé des coûts de 13,14 RU, 16,01 RU et 12,63 RU, vous pouvez établir la moyenne de ces coûts à 14 RU.

## <a name="read-requests"></a>Demandes de lecture

Le coût des opérations de requête peut être beaucoup plus difficile à prédire pour les raisons suivantes :

* Si votre système prend en charge les requêtes définies par l’utilisateur, vous devez mapper les requêtes entrantes aux requêtes représentatives pour déterminer le coût. Ce processus peut prendre plusieurs formes :
  * Il est possible de faire correspondre exactement les requêtes. S’il n’existe aucune correspondance directe, vous devrez peut-être rechercher la requête représentative la plus proche.
  * Vous constaterez peut-être que vous pouvez calculer un coût en fonction des caractéristiques de la requête. Par exemple, vous pouvez constater que chaque clause de la requête a un certain coût, ou qu’une propriété indexée coûte « x », alors qu’une autre non indexée coûte « y », etc.
* Le nombre de résultats peut varier et, à moins que vous n’ayez des statistiques, vous ne pouvez pas prédire l’impact des RU à partir de la charge utile de retour.

Il est probable que vous n'aurez pas un coût unique des opérations de la requête, mais plutôt une fonction qui évalue la requête et calcule un coût.
Si vous utilisez l’API de base, vous pouvez alors évaluer le coût réel de l’opération et déterminer la précision de votre estimation (l’ajustement de cette estimation pourrait même se faire automatiquement dans le code).

## <a name="transient-fault-handling"></a>Gestion des erreurs temporaires

Votre application aura toujours besoin de traiter les erreurs temporaires même si vous implémentez un mécanisme de limitation du débit pour les raisons suivantes :

* Le coût réel d’une requête peut être différent du coût prévu.
* Des erreurs temporaires peuvent se produire pour des raisons autres que TooManyRequests.

Mais en implémentant correctement un mécanisme de limitation du débit dans votre application, vous réduirez considérablement le nombre d’erreurs temporaires.

## <a name="alternate-and-related-techniques"></a>Autres techniques et techniques connexes

Cet article décrit la coordination côté client et le regroupement des charges de travail, mais d’autres techniques permettent de gérer le débit global du système.

### <a name="autoscaling"></a>Mise à l’échelle automatique

Le débit approvisionné en mode de mise à l’échelle automatique dans Azure Cosmos DB vous permet de mettre à l’échelle le débit (RU/s) de votre base de données ou conteneur automatiquement et instantanément. Le débit est mis à l’échelle en fonction de l’utilisation, sans impact sur la disponibilité, la latence, le débit ou les performances de la charge de travail.

Le débit approvisionné en mode de mise à l’échelle automatique est adapté aux charges de travail stratégiques qui ont des modèles de trafic variables ou imprévisibles et qui nécessitent des contrats SLA sur des performances et une mise à l’échelle élevées.

Pour plus d’informations sur la mise à l’échelle automatique, consultez [Créer des conteneurs et des bases de données Azure Cosmos en débit de mise à l’échelle automatique](provision-throughput-autoscale.md).

### <a name="queue-based-load-leveling-pattern"></a>Modèle de nivellement de charge basé sur une file d’attente

Vous pouvez utiliser une file d’attente qui agit comme une mémoire tampon entre un client et Azure Cosmos DB afin d’atténuer les surcharges intermittentes qui entraînent l’échec du service ou l’expiration de la tâche.

Ce modèle est utile pour toute application qui utilise des services soumis à une surcharge. Mais ce modèle n’est pas utile si l’application attend une réponse du service avec une latence minimale.

Ce modèle convient souvent aux opérations d’ingestion.

Pour en savoir plus sur ce modèle, consultez [Modèle de nivellement de la charge basé sur une file d'attente](/azure/architecture/patterns/queue-based-load-leveling).

### <a name="cache-aside-pattern"></a>Modèle Cache-Aside

Vous pouvez charger les données à la demande dans un cache au lieu d’interroger Azure Cosmos DB à chaque fois. L’utilisation d’un cache peut améliorer les performances et aider à maintenir la cohérence entre les données contenues dans le cache et les données résidant dans la banque de données sous-jacente.

Pour plus d'informations, consultez : [Modèle Cache-Aside](/azure/architecture/patterns/cache-aside).

### <a name="materialized-view-pattern"></a>Modèle de vue matérialisée

Vous pouvez pré-remplir les vues dans d’autres collections après avoir stocké les données dans Azure Cosmos DB lorsque les données ne sont pas idéalement formatées pour les opérations de requête requises. Ce modèle peut aider à la prise en charge efficace des requêtes et de l’extraction de données, et améliorer les performances de l’application.

Pour plus d’informations, consultez [Modèle de vue matérialisée](/azure/architecture/patterns/materialized-view).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [résoudre les erreurs TooManyRequests](troubleshoot-request-rate-too-large.md) dans Azure Cosmos DB.
* Découvrez comment [résoudre les erreurs Timeout ](troubleshoot-request-timeout.md) dans Azure Cosmos DB.
* Découvrez comment [résoudre les erreurs ServiceUnavailable](troubleshoot-service-unavailable.md) dans Azure Cosmos DB.
* En savoir plus sur les [Unités de requête](request-units.md) dans Azure Cosmos DB.
* En savoir plus sur le [partitionnement et la mise à l’échelle horizontale](partitioning-overview.md) dans Azure Cosmos DB.
* En savoir plus sur les [stratégies d’indexation](index-policy.md) dans Azure Cosmos DB.
* En savoir plus sur la [mise à l’échelle automatique](provision-throughput-autoscale.md) dans Azure Cosmos DB.
