---
title: Comprendre le niveau de compatibilité pour les travaux Azure Stream Analytics
description: Apprenez à définir un niveau de compatibilité pour un travail Azure Stream Analytics et découvrez les principales modifications apportées au dernier niveau de compatibilité
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 5/2/2019
ms.openlocfilehash: c1b2875e6899d2301a4c4b564882214dc7bc4981
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205456"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Niveau de compatibilité pour les travaux Azure Stream Analytics

Cet article décrit l’option de niveau de compatibilité dans Azure Stream Analytique. Analytique de Stream est un service géré, avec les mises à jour régulières de fonctionnalité et de performances. La plupart des mises à jour de runtimes du service est automatiquement accessibles aux utilisateurs finaux. 

Toutefois, certaines nouvelles fonctionnalités dans le service peuvent introduire un changement majeur, telle qu’une modification dans le comportement d’une tâche existante, ou une modification des façon dont les données est consommée dans l’exécution des travaux. Vous pouvez conserver vos travaux Stream Analytique existant en cours d’exécution sans modification majeure en laissant le paramètre abaissé de niveau de compatibilité. Lorsque vous êtes prêt pour les comportements d’exécution plus récente, vous pouvez participer en déclenchant le niveau de compatibilité. 

## <a name="choose-a-compatibility-level"></a>Choisissez un niveau de compatibilité

Le niveau de compatibilité contrôle le comportement d’exécution d’un travail Stream Analytics. 

Azure Stream Analytique prend actuellement en charge trois niveaux de compatibilité :

* Comportement précédent 1.0-
* 1.1 - comportement par défaut
* 1.2 (version préliminaire) - comportement plus récent avec les dernières améliorations dans l’évaluation

Le niveau de compatibilité d’origine 1.0 a été introduit au cours de la disponibilité générale d’Azure Stream Analytique plusieurs années auparavant.

Lorsque vous créez un nouveau travail d’Analytique de Stream, il est recommandé de le créer en utilisant le dernier niveau de compatibilité. Démarrez votre conception de travail vous fier aux dernière comportements, pour éviter de complexité et modification ajoutée par la suite.

## <a name="set-the-compatibility-level"></a>Définition du niveau de compatibilité

Vous pouvez définir le niveau de compatibilité pour un travail Stream Analytique dans le portail Azure ou à l’aide de la [créer appel d’API REST de travail](/rest/api/streamanalytics/stream-analytics-job).

Pour mettre à jour le niveau de compatibilité de la tâche dans le portail Azure :

1. Utilisez le [Azure portal](https://portal.azure.com) pour localiser à votre travail Stream Analytique.
2. **Arrêter** la tâche avant la mise à jour le niveau de compatibilité. Si votre travail est en cours d’exécution, vous ne peut pas mettre à jour le niveau de compatibilité.
3. Sous le **configurer** titre, sélectionnez **niveau de compatibilité**.
4. Choisissez la valeur de niveau de compatibilité que vous souhaitez.
5. Sélectionnez **enregistrer** en bas de la page.

![Niveau de compatibilité Stream Analytics dans le portail Azure](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Lorsque vous mettez à jour le niveau de compatibilité, le compilateur T-SQL valide le travail avec la syntaxe correspondant au niveau de compatibilité sélectionné.

## <a name="compatibility-level-12-preview"></a>Niveau de compatibilité 1.2 (version préliminaire)

Les modifications majeures suivantes sont introduites dans le niveau de compatibilité 1.2 :

### <a name="geospatial-functions"></a>Fonctions géospatiales

**Niveaux précédents :** Azure Stream Analytique utilisé calculs géographiques.

**niveau 1.2 :** Analytique de Stream Azure vous permet calculer des coordonnées géographiques projetée géométrique. Il n’existe aucune modification dans la signature des fonctions géospatiales. Toutefois, leur sémantique est légèrement différente, ce qui permet un calcul plus précis qu’avant.

Azure Stream Analytique prend en charge l’indexation de données de référence GÉOSPATIALE. Les données de référence contenant les éléments géographiques peuvent être indexées pour un calcul plus rapide de la jointure.

Les fonctions géospatiales mis à jour mettre l’expressivité complète du format géospatiales de texte WKT (WKT). Vous pouvez spécifier les autres composants geospatial qui n’ont pas été précédemment prises en charge avec GeoJson.

Pour plus d’informations, consultez [met à jour aux fonctionnalités géospatiales dans Azure Stream Analytique – Cloud et IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Exécution de requêtes parallèles pour des sources d’entrée avec plusieurs partitions

**Niveaux précédents :** Les requêtes Stream Analytique Azure nécessitent à l’utilisation de la clause PARTITION BY pour paralléliser le traitement des requêtes sur plusieurs partitions de la source d’entrée.

**niveau 1.2 :** Si la logique de requête peut être parallélisée sur plusieurs partitions de la source d’entrée, Azure Stream Analytique crée des instances de requête séparée et exécute des calculs en parallèle.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Intégration d’API en bloc native avec sortie CosmosDB

**Niveaux précédents :** Le comportement upsert était *insérer ou fusionner*.

**niveau 1.2 :** Intégration d’API en bloc native avec sortie CosmosDB optimise le débit et gère efficacement la limitation des demandes. Pour plus d’informations, consultez [la sortie Azure Stream Analytique à Azure Cosmos DB page](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12).

Le comportement d’upsert est *insérer ou remplacer*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset lors de l’écriture à la sortie SQL

**Niveaux précédents :** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) types ont été ajustées au format UTC.

**niveau 1.2 :** DateTimeOffset est ajustée n’est plus.

### <a name="strict-validation-of-prefix-of-functions"></a>Validation stricte du préfixe de fonctions

**Niveaux précédents :** Il n’a aucune validation stricte des préfixes de fonction.

**niveau 1.2 :** Azure Stream Analytique a une validation stricte des préfixes de fonction. Ajout d’un préfixe à une fonction intégrée provoque une erreur. Par exemple,`myprefix.ABS(…)` n’est pas pris en charge.

Ajout d’un préfixe pour les agrégats intégrés entraîne également dans l’erreur. Par exemple, `myprefix.SUM(…)` n’est pas pris en charge.

Avec le préfixe « système » pour des résultats de fonctions définies par l’utilisateur dans l’erreur.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Interdire Array et Object en tant que propriétés de clé dans l’adaptateur de sortie Cosmos DB

**Niveaux précédents :** Types Array et Object étaient pris en charge en tant que propriété de clé.

**niveau 1.2 :** Types Array et Object sont plus prises en charge en tant que propriété de clé.

## <a name="compatibility-level-11"></a>Niveau de compatibilité 1.1

Voici les principales modifications introduites dans le niveau de compatibilité 1.1 :

### <a name="service-bus-xml-format"></a>Format XML de Bus de service

**1.0 niveau :** Azure Stream Analytics utilisait DataContractSerializer, c’est pourquoi le contenu des messages incluait des balises XML. Par exemple : 

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**niveau 1.1 :** le contenu du message renferme directement le flux sans aucune balise supplémentaire. Par exemple : `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Conservation de la casse pour les noms de champ

**1.0 niveau :** les noms de champs passaient en minuscules au moment du traitement par le moteur Azure Stream Analytics.

**niveau 1.1 :** respect de la casse est rendue persistante pour les noms de champ lorsqu’ils sont traités par le moteur Azure Stream Analytique.

> [!NOTE]
> La conservation de la casse n’est pas encore disponible pour les tâches Stream Analytics hébergés à l’aide de l’environnement Edge. Par conséquent, tous les noms de champs sont convertis en minuscules si votre tâche est hébergée sur Edge.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1.0 niveau :** la commande CREATE TABLE ne filtrait pas les événements avec une valeur NaN (Not-a-Number. Par exemple, Infinity, -Infinity) dans une colonne de type FLOAT, car ils étaient en dehors des limites documentées pour ces numéros.

**niveau 1.1 :** la commande CREATE TABLE vous permet de spécifier un schéma fort. Le moteur Stream Analytics vérifie que les données sont conformes à ce schéma. Avec ce modèle, la commande peut filtrer des événements avec des valeurs NaN.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Désactiver upcast automatique pour les chaînes de date/heure au format JSON

**1.0 niveau :** l’analyseur JSON upcastait automatiquement en type DateTime les valeurs de chaîne contenant des informations de type date/heure/fuseau horaire et les convertissait au format UTC. Ce problème entraînait la perte des informations de fuseau horaire.

**niveau 1.1 :** il n’existe plus aucun upcast automatique des valeurs de chaîne avec des informations de date/heure/fuseau horaire en type DateTime. Les informations relatives au fuseau horaire sont donc conservées.

## <a name="next-steps"></a>Étapes suivantes

* [Résoudre les problèmes liés aux entrées Azure Stream Analytics](stream-analytics-troubleshoot-input.md)
* [Stream Analytique Resource health](stream-analytics-resource-health.md)
