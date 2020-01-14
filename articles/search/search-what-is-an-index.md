---
title: Créer une définition et des concepts d’index
titleSuffix: Azure Cognitive Search
description: Introduction aux termes et aux concepts des index dans la Recherche cognitive Azure, y compris les composants et la structure physique.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d2b8b2fecbf85e6590294f1fbd7ff2a4453b9e87
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460750"
---
# <a name="create-a-basic-index-in-azure-cognitive-search"></a>Créer un index de base dans la Recherche cognitive Azure

Dans la Recherche cognitive Azure, un *index* est une banque permanente de *documents* et d'autres éléments utilisés pour la recherche filtrée et en texte intégral sur un service Recherche cognitive Azure. Conceptuellement, un document correspond à une unité de données pouvant faire l’objet d’une recherche dans un index. Par exemple, un détaillant de commerce électronique peut posséder un document pour chaque article qu’il vend, un organisme d’information peut posséder un document par article, et ainsi de suite. Pour comparer avec des éléments de base de données plus familiers, d’un point de vue conceptuel, un *index* est similaire à une *table*, et les *documents* équivalent plus ou moins aux *lignes* d’une table.

Lorsque vous ajoutez ou chargez un index, la Recherche cognitive Azure crée des structures physiques basées sur le schéma fourni. Par exemple, si l’un des champs de votre index est marqué comme utilisable dans une requête, un index inversé est créé pour ce champ. Lorsque vous ajouterez ou chargerez des documents, ou que vous soumettrez des requêtes de recherche à la Recherche cognitive Azure, vous enverrez les demandes à un index spécifique de votre service de recherche. Le processus consistant à charger des champs ayant valeur de documents se nomme *indexation* ou ingestion des données.

Vous pouvez créer un index avec le portail, [l’API REST](search-create-index-rest-api.md) ou le [Kit SDK .NET](search-create-index-dotnet.md).

## <a name="recommended-workflow"></a>Workflow recommandé

Avant de parvenir à une conception d’index satisfaisante, il convient d’effectuer plusieurs itérations. En utilisant une combinaison d’outils et d’API, vous pouvez finaliser rapidement votre conception.

1. Déterminez si vous pouvez utiliser un [indexeur](search-indexer-overview.md#supported-data-sources). Si vos données externes représentent l’une des sources de données prises en charge, vous pouvez créer un prototype d’index et le charger à l’aide de l’Assistant [**Importation de données**](search-import-data-portal.md).

2. Si vous ne pouvez pas utiliser **Importation de données**, vous pouvez toujours [créer un index initial sur le portail](search-create-index-portal.md), ajouter des champs, des types de données, puis affecter des attributs à l’aide des contrôles de la page **Ajouter un index**. Le portail présente les attributs disponibles en fonction des types de données. Si vous débutez dans la conception d’index, cela vous sera utile.

   ![Ajouter une page d’index avec les attributs par type de données](media/search-create-index-portal/field-attributes.png "Ajouter une page d’index avec les attributs par type de données")
  
   Quand vous cliquez sur **Créer**, toutes les structures physiques supportant votre index sont créées dans votre service de recherche.

3. Téléchargez le schéma d’index à l’aide de l’[API REST d’obtention d’index](https://docs.microsoft.com/rest/api/searchservice/get-index) et d’un outil de test web comme [Postman](search-get-started-postman.md). Vous disposez maintenant d’une représentation JSON de l’index que vous avez créé sur le portail. 

   À ce stade, vous passez à une approche basée sur le code. Le portail ne se prête pas bien à l’itération dans le sens où vous ne pouvez pas modifier un index qui a déjà été créé. En revanche, vous pouvez utiliser Postman et REST pour les tâches restantes.

4. [Chargez votre index avec des données](search-what-is-data-import.md). La Recherche cognitive Azure accepte les documents JSON. Pour charger vos données par programmation, vous pouvez utiliser Postman avec des documents JSON dans la charge utile de demande. S’il n’est pas facilement d’exprimer vos données au format JSON, cette étape sera la plus fastidieuse.

5. Interrogez votre index, examinez les résultats et continuez d’itérer sur le schéma d’index jusqu’à ce que vous commenciez à obtenir les résultats attendus. Pour interroger votre index, vous pouvez utiliser l’[**Explorateur de recherche**](search-explorer.md) ou Postman.

6. Continuez d’utiliser le code pour itérer sur votre conception.  

Comme les structures physiques sont créées dans le service, il est nécessaire de [supprimer et de recréer les index](search-howto-reindex.md) chaque fois que vous apportez des modifications importantes à une définition de champ existante. Cela signifie que pendant le développement, vous devez prévoir des regénérations fréquentes. Vous pouvez envisager de travailler sur une partie de vos données pour regénérer plus rapidement. 

Pour une conception itérative, il est recommandé de privilégier une approche basée sur le code plutôt que sur le portail. Si vous utilisez le portail pour définir un index, vous devrez remplir la définition de l’index à chaque regénération. Sinon, les outils tels que [Postman et l’API REST](search-get-started-postman.md) s’avèrent utiles pour tester la preuve de concept aux phases initiales d’un projet de développement. Vous pouvez apporter des modifications incrémentielles à une définition d’index dans un corps de demande, puis envoyer la demande à votre service pour recréer un index en utilisant un schéma mis à jour.

## <a name="components-of-an-index"></a>Composants d’un index

Schématiquement, un index Recherche cognitive Azure se compose des éléments suivants. 

La [*collection de champs*](#fields-collection) correspond généralement à la majeure partie de l’index, dans laquelle chaque champ est nommé, tapé et pourvu de comportements autorisés qui déterminent son utilisation. Il existe d’autres éléments, comme les [suggesteurs](#suggesters), les [profils de score](#scoring-profiles), les [analyseurs](#analyzers) avec les composants pour prendre en charge la personnalisation et les options [CORS](#cors) et [clé de chiffrement](#encryption-key).

```json
{
  "name": (optional on PUT; required on POST) "name_of_index",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [
    {
      "name": "name of suggester",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["field1", "field2", ...]
    }
  ],
  "scoringProfiles": [
    {
      "name": "name of scoring profile",
      "text": (optional, only applies to searchable fields) {
        "weights": {
          "searchable_field_name": relative_weight_value (positive #'s),
          ...
        }
      },
      "functions": (optional) [
        {
          "type": "magnitude | freshness | distance | tag",
          "boost": # (positive number used as multiplier for raw score != 1),
          "fieldName": "...",
          "interpolation": "constant | linear (default) | quadratic | logarithmic",
          "magnitude": {
            "boostingRangeStart": #,
            "boostingRangeEnd": #,
            "constantBoostBeyondRange": true | false (default)
          },
          "freshness": {
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
          },
          "distance": {
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
          },
          "tag": {
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)
          }
        }
      ],
      "functionAggregation": (optional, applies only when functions are specified) 
        "sum (default) | average | minimum | maximum | firstMatching"
    }
  ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) {
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>Collection et attributs de champs

Lorsque vous définissez votre schéma, vous devez spécifier le nom, le type et les attributs de chaque champ de votre index. Le type de champ classifie les données stockées dans ce champ. Les attributs sont définis sur des champs individuels pour spécifier la façon dont le champ est utilisé. Les tableaux ci-après énumèrent les types et les attributs que vous pouvez spécifier.

### <a name="data-types"></a>Types de données
| Type | Description |
| --- | --- |
| *Edm.String* |Texte pour lequel un jeton peut éventuellement être généré pour la recherche en texte intégral (césure de mots, recherche de radical, etc). |
| *Collection(Edm.String)* |Liste de chaînes pouvant être éventuellement tokenisées pour la recherche en texte intégral. En théorie, il n’existe pas de limite supérieure quant au nombre d’éléments d’une collection, mais la limite supérieure de 16 Mo sur la taille de charge utile s’applique aux collections. |
| *Edm.Boolean* |Contient des valeurs true/false. |
| *Edm.Int32* |Valeurs entières 32 bits. |
| *Edm.Int64* |Valeurs entières 64 bits. |
| *Edm.Double* |Données numériques à double précision. |
| *Edm.DateTimeOffset* |Valeurs de date et heure représentées au format OData V4 (par exemple, `yyyy-MM-ddTHH:mm:ss.fffZ` ou `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |Point représentant un emplacement géographique de la planète. |

Pour plus d’informations sur les types de données pris en charge par le service Recherche cognitive Azure, consultez [cet article](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types).

### <a name="index-attributes"></a>Attributs d’index

Dans votre index, un seul champ doit être désigné en tant que champ de **clé** qui identifie de façon unique chaque document.

Les autres attributs déterminent la façon dont un champ est utilisé dans une application. Par exemple, l’attribut **Interrogeable** est affecté à tous les champs qui doivent être inclus dans une recherche en texte intégral. 

Les API que vous utilisez pour créer un index ont différents comportements par défaut. Pour les [API REST](https://docs.microsoft.com/rest/api/searchservice/Create-Index), la plupart des attributs sont activés par défaut (par exemple, **Interrogeable** et **Récupérable** sont actifs pour les champs de type chaîne) et vous n’avez généralement besoin de les définir que si vous voulez les désactiver. Pour le Kit de développement logiciel (SDK) .NET, l’inverse est vrai. Pour les propriétés que vous ne définissez pas explicitement, l’option par défaut désactive le comportement de recherche correspondante, sauf si vous l’activez de façon spécifique.

| Attribut | Description |
| --- | --- |
| `key` |Chaîne fournissant un ID unique à chaque document utilisé pour rechercher des documents. Chaque index doit avoir une clé. Un seul champ peut être la clé, et son type doit être défini sur Edm.String. |
| `retrievable` |Définit si un champ peut être retourné dans un résultat de recherche. |
| `filterable` |Permet d’utiliser le champ dans des requêtes de filtre. |
| `Sortable` |Permet à une requête de trier les résultats de recherche à l’aide de ce champ. |
| `facetable` |Permet d’utiliser un champ pour le filtrage autonome dans une structure de [navigation par facettes](search-faceted-navigation.md) par un utilisateur. En général, les champs contenant des valeurs répétitives que vous pouvez utiliser pour regrouper plusieurs documents (par exemple, plusieurs documents appartenant à une seule marque ou catégorie de service) sont les mieux adaptés en tant que facettes. |
| `searchable` |Indique que le champ peut faire l’objet d’une recherche en texte intégral. |

## <a name="index-size"></a>Taille d'index

La taille d’un index est déterminée par la taille des documents que vous chargez, plus la configuration de l’index, par exemple si vous incluez des suggesteurs, et la façon dont vous définissez des attributs sur des champs individuels. La capture d’écran suivante illustre les caractéristiques du stockage d’index résultant des différentes combinaisons d’attributs.

L’index est basé sur la source de données de l’[l’exemple intégré real estate](search-get-started-portal.md), que vous pouvez indexer et interroger sur le portail. Bien que les schémas de l’index ne soient pas montrés, vous pouvez en déduire les attributs d’après le nom de l’index. Par exemple, pour l’index *realestate-searchable*, seul l’attribut **searchable** est sélectionné ; pour l’index *realestate-retrievable*, seul l’index **retrievable** est sélectionné, et ainsi de suite.

![Taille de l’index en fonction de la sélection de l’attribut](./media/search-what-is-an-index/realestate-index-size.png "Taille de l’index en fonction de la sélection de l’attribut")

Bien que ces variantes d’index soient artificielles, nous pouvons nous y reporter pour nous faire une idée de la façon dont les attributs affectent le stockage. Le paramètre **retrievable** fait-il croître l’index ? Non. L’ajout de champs à un **suggesteur** fait-il croître l’index ? Oui.

Les index qui prennent en charge le filtrage et le tri sont en proportion plus volumineux que ceux qui prennent en charge uniquement la recherche en texte intégral. Les opérations de filtre et de tri recherchent les correspondances exactes, ce qui nécessite la présence de documents intacts. En revanche, les champs pouvant faire l’objet d’une recherche en texte intégral ou approximative utilisent des index inversés, qui sont remplis avec des termes assortis de jetons qui occupent moins d’espace que les documents entiers. 

> [!Note]
> L’architecture de stockage est considérée comme un détail d’implémentation de Recherche cognitive Azure et est susceptible d’évoluer sans préavis. Il n’est pas garanti que le comportement actuel persistera dans l’avenir.

## <a name="suggesters"></a>Générateurs de suggestions
Un suggesteur est une section du schéma qui définit quels champs d’un index sont utilisés pour prendre en charge l’autocomplétion et les requêtes prédictives dans les recherches. En général, les chaînes de recherche partielle sont envoyées à l’[API REST Suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions) pendant que l’utilisateur tape une requête de recherche, et l’API retourne un ensemble de documents ou d’expressions suggérés. 

Les champs ajoutés à un suggesteur sont utilisés pour générer des termes de recherche à saisie semi-automatique (« type-ahead »). Tous les termes de recherche sont créés pendant l’indexation et stockés séparément. Pour plus d’informations sur la création d’une structure de suggesteur, consultez [Ajouter des suggesteurs](index-add-suggesters.md).

## <a name="scoring-profiles"></a>Profils de score

Un [profil de score](index-add-scoring-profiles.md) est une section du schéma qui définit des comportements de score personnalisés permettant de faire apparaître certains éléments plus haut dans les résultats de la recherche. Les profils de calcul de score sont constitués de pondérations et de fonctions de champ. Pour les utiliser, vous spécifiez un profil par nom dans la chaîne de requête.

Un profil de score par défaut fonctionne en arrière-plan pour calculer un score de recherche pour chaque élément d’un jeu de résultats. Vous pouvez utiliser le profil de score interne et sans nom. Vous pouvez aussi définir **defaultScoringProfile** pour utiliser par défaut un profil personnalisé, appelé chaque fois qu’aucun profil personnalisé n’est spécifié dans la chaîne de requête.

## <a name="analyzers"></a>Analyseurs

L’élément analyseurs définit le nom de l’analyseur linguistique à utiliser pour le champ. Pour plus d’informations sur les différents analyseurs disponibles, consultez [Ajout d’analyseurs à un index Recherche cognitive Azure](search-analyzers.md). Les analyseurs peuvent être utilisés uniquement avec les champs pouvant faire l’objet d’une recherche. Une fois que l’analyseur est affecté à un champ, il ne peut plus être modifié, à moins de regénérer l’index.

## <a name="cors"></a>CORS

Le code JavaScript côté client ne peut pas appeler d’API par défaut, car le navigateur empêche toutes les requêtes cross-origin. Pour autoriser les requêtes cross-origin dans l'index, activez CORS (partage des ressources cross-origin) en définissant l'attribut **corsOptions**. Pour des raisons de sécurité, seules les API de requête prennent en charge CORS. 

Les options suivantes peuvent être définies pour CORS :

+ **allowedOrigins** (obligatoire) : il s'agit de la liste des origines pouvant accéder à l’index. Cela signifie que le code JavaScript distribué à partir de ces origines est autorisé à interroger l’index (s'il fournit la bonne clé API). Chaque origine se présente généralement sous la forme `protocol://<fully-qualified-domain-name>:<port>`, bien que `<port>` soit souvent omis. Pour plus d'informations, voir [Partage des ressources cross-origin (Wikipédia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing).

  Si vous voulez autoriser l'accès à toutes les origines, incluez uniquement l’élément `*` dans le tableau **allowedOrigins**. Si *cette pratique est déconseillée pour les services de recherche de production*, elle est souvent utile pour le développement et le débogage.

+ **maxAgeInSeconds** (facultatif) : les navigateurs utilisent cette valeur pour déterminer la durée (en secondes) de mise en cache des réponses CORS préliminaires. Il doit s'agir d'un entier non négatif. Plus cette valeur est importante, meilleures sont les performances, mais plus il faut de temps pour que les modifications apportées à la stratégie CORS prennent effet. Si la valeur n'est pas définie, une durée par défaut de 5 minutes est utilisée.

## <a name="encryption-key"></a>Clé de chiffrement

Si tous les index de Recherche cognitive Azure sont chiffrés par défaut à l’aide de clés gérées par Microsoft, il est possible de configurer les index pour qu’ils soient chiffrés avec **clés gérées par le client** dans Key Vault. Pour en savoir plus, consultez [Gérer les clés de chiffrement dans Recherche cognitive Azure](search-security-manage-encryption-keys.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous comprenez la composition de l’index, vous pouvez créer votre premier index sur le portail.

> [!div class="nextstepaction"]
> [Ajouter un index (portail)](search-create-index-portal.md)
