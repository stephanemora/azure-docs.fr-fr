---
title: Comprendre le niveau de compatibilité pour les travaux Azure Stream Analytics
description: Apprenez à définir un niveau de compatibilité pour un travail Azure Stream Analytics et découvrez les principales modifications apportées au dernier niveau de compatibilité
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 6fb93152263d253de983b17d25f02f4c68a172fd
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361389"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Niveau de compatibilité pour les travaux Azure Stream Analytics
 
Le niveau de compatibilité fait référence aux comportements spécifiques à chaque version d’un service Azure Stream Analytics. Azure Stream Analytics est un service géré qui est régulièrement enrichi de nouvelles fonctionnalités et bénéficie d’améliorations de performances. En règle générale, les mises à jour sont automatiquement accessibles aux utilisateurs finaux. Toutefois, certaines nouvelles fonctionnalités peuvent introduire des modifications majeures, comme un changement de comportement sur un travail existant, une modification des processus qui consomment des données sur ces travaux, etc. Un niveau de compatibilité permet de représenter un changement majeur introduit dans Stream Analytics. Les changements majeurs sont toujours introduits avec un nouveau niveau de compatibilité. 

Niveau de compatibilité permet de s’assurer d’exécutent des travaux existants sans échec. Lorsque vous créez un nouveau travail d’Analytique de Stream, il est recommandé de le créer en utilisant le dernier niveau de compatibilité. 
 
## <a name="set-a-compatibility-level"></a>Définir un niveau de compatibilité 

Le niveau de compatibilité contrôle le comportement d’exécution d’un travail Stream Analytics. Vous pouvez définir le niveau de compatibilité d’un travail Stream Analytics à l’aide du portail ou en appelant [l’API REST create job](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Azure Stream Analytics prend en charge deux niveaux de compatibilité : « 1.0 » et « 1.1 ». Par défaut, le niveau de compatibilité est défini sur « 1.0 », qui date de la version en disponibilité générale d’Azure Stream Analytics. Pour mettre à jour la valeur par défaut, accédez à votre travail Stream Analytics existant > sélectionnez l’option **Niveau de compatibilité** dans la section **Configurer** et modifiez la valeur. 

Veillez à interrompre le travail avant de mettre à jour le niveau de compatibilité. Si votre travail est en cours d’exécution, vous ne peut pas mettre à jour le niveau de compatibilité. 

![Niveau de compatibilité Stream Analytics dans le portail Azure](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

 
Lorsque vous mettez à jour le niveau de compatibilité, le compilateur T-SQL valide le travail avec la syntaxe correspondant au niveau de compatibilité sélectionné. 

## <a name="major-changes-in-the-latest-compatibility-level-12"></a>Modifications majeures apportées au dernier niveau de compatibilité (1.2)

Les modifications majeures suivantes sont introduites dans le niveau de compatibilité 1.2 :

### <a name="geospatial-functions"></a>Fonctions géospatiales 

**versions précédentes :** Azure Stream Analytique utilisé calculs géographiques.

**version actuelle :** Analytique de Stream Azure vous permet calculer des coordonnées géographiques projetée géométrique. Il n’existe aucune modification dans la signature des fonctions géospatiales. Toutefois, leur sémantique est légèrement différente, ce qui permet un calcul plus précis qu’avant.

Azure Stream Analytique prend en charge l’indexation de données de référence GÉOSPATIALE. Les données de référence contenant les éléments géographiques peuvent être indexées pour un calcul plus rapide de la jointure.

Les fonctions géospatiales mis à jour mettre l’expressivité complète du format géospatiales de texte WKT (WKT). Vous pouvez spécifier les autres composants geospatial qui n’ont pas été précédemment prises en charge avec GeoJson.

Pour plus d’informations, consultez [met à jour aux fonctionnalités géospatiales dans Azure Stream Analytique – Cloud et IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Exécution de requêtes parallèles pour des sources d’entrée avec plusieurs partitions 

**versions précédentes :** Les requêtes Stream Analytique Azure nécessitent à l’utilisation de la clause PARTITION BY pour paralléliser le traitement des requêtes sur plusieurs partitions de la source d’entrée.

**version actuelle :** Si la logique de requête peut être parallélisée sur plusieurs partitions de la source d’entrée, Azure Stream Analytique crée des instances de requête séparée et exécute des calculs en parallèle.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Intégration d’API en bloc native avec sortie CosmosDB

**versions précédentes :** Le comportement upsert était *insérer ou fusionner*.

**version actuelle :** Intégration d’API en bloc native avec sortie CosmosDB optimise le débit et gère efficacement la limitation des demandes.

Le comportement d’upsert est *insérer ou remplacer*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset lors de l’écriture à la sortie SQL

**versions précédentes :** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) types ont été ajustées au format UTC.

**version actuelle :** DateTimeOffset est ajustée n’est plus.

### <a name="strict-validation-of-prefix-of-functions"></a>Validation stricte du préfixe de fonctions

**versions précédentes :** Il n’a aucune validation stricte des préfixes de fonction.

**version actuelle :** Azure Stream Analytique a une validation stricte des préfixes de fonction. Ajout d’un préfixe à une fonction intégrée provoque une erreur. Par exemple,`myprefix.ABS(…)` n’est pas pris en charge.

Ajout d’un préfixe pour les agrégats intégrés entraîne également dans l’erreur. Par exemple, `myprefix.SUM(…)` n’est pas pris en charge.

Avec le préfixe « système » pour des résultats de fonctions définies par l’utilisateur dans l’erreur.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Interdire Array et Object en tant que propriétés de clé dans l’adaptateur de sortie Cosmos DB

**versions précédentes :** Types Array et Object étaient pris en charge en tant que propriété de clé.

**version actuelle :** Types Array et Object sont plus prises en charge en tant que propriété de clé.


## <a name="next-steps"></a>Étapes suivantes
* [Résoudre les problèmes d’entrées d’Azure Stream Analytique](stream-analytics-troubleshoot-input.md)
* [Stream Analytique Resource health](stream-analytics-resource-health.md)
