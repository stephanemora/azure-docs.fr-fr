---
title: Se connecter à du contenu Azure MySQL et l’indexer à l’aide d’un indexeur Recherche cognitive Azure (préversion)
titleSuffix: Azure Cognitive Search
description: Importez des données à partir d’Azure MySQL dans un index pouvant faire l’objet d’une recherche dans Recherche cognitive Azure. Les indexeurs automatisent l’ingestion des données pour les sources de données sélectionnées telles que MySQL.
author: markheff
manager: luisca
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/17/2021
ms.openlocfilehash: 4dee2250d49d437d47148b873cfe8c7ce1e8f5ea
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111754436"
---
# <a name="connect-to-and-index-azure-mysql-content-using-an-azure-cognitive-search-indexer-preview"></a>Se connecter à du contenu Azure MySQL et l’indexer à l’aide d’un indexeur Recherche cognitive Azure (préversion)

> [!IMPORTANT] 
> La prise en charge de MySQL est actuellement disponible en préversion publique. Les fonctionnalités d’évaluation sont fournies sans contrat de niveau de service et ne sont pas recommandées pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vous pouvez demander l’accès aux préversions en remplissant [ce formulaire](https://aka.ms/azure-cognitive-search/indexer-preview). L’[API REST version 2020-06-30-Preview](search-api-preview.md) fournit cette fonctionnalité. Il n’existe actuellement aucune prise en charge du Kit de développement logiciel (SDK) ni aucune prise en charge du portail.

L’indexeur Recherche cognitive Azure pour MySQL analyse votre base de données MySQL sur Azure, extrait les données pouvant faire l’objet d’une recherche et les indexe dans Recherche cognitive Azure. L’indexeur prend en compte toutes les modifications, tous les chargements et toutes les suppressions de votre base de données MySQL et reflète ces modifications dans Recherche cognitive Azure.

## <a name="create-an-azure-mysql-indexer"></a>Créer un indexeur Azure MySQL

Pour indexer MySQL sur Azure, suivez les étapes ci-dessous.

### <a name="step-1-create-a-data-source"></a>Étape 1 : Création d'une source de données

Pour créer la source de données, envoyez la requête suivante :

```http

    POST https://[search service name].search.windows.net/datasources?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {   
        "name" : "[Data source name]"
        "description" : "[Description of MySQL data source]",
        "type" : "mysql",
        "credentials" : { 
            "connectionString" : 
                "Server=[MySQLServerName].MySQL.database.azure.com; Port=3306; Database=[DatabaseName]; Uid=[UserName]; Pwd=[Password]; SslMode=Preferred;" 
        },
        "container" : { 
            "name" : "[TableName]" 
        },
        "dataChangeDetectionPolicy" : { 
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "[HighWaterMarkColumn]"
        }
    }

```

### <a name="step-2-create-an-index"></a>Étape 2 : Création d'un index

Créez l’index Recherche cognitive Azure cible si vous n’en avez pas encore.

```http

    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
       "name": "[Index name]",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "searchable": true,
         "sortable": false,
         "facetable": false
       }]
    }

```

### <a name="step-3-create-the-indexer"></a>Étape 3 : Création de l’indexeur

Une fois l’index et la source de données créés, vous êtes prêt à créer l’indexeur.

```http

    POST https://[search service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "[Indexer name]"
        "description" : "[Description of MySQL indexer]",
        "dataSourceName" : "[Data source name]",
        "targetIndexName" : "[Index name]"
    }

```

## <a name="run-indexers-on-a-schedule"></a>Exécuter des indexeurs selon une planification
Vous pouvez également configurer l'indexeur pour qu’il s’exécute à intervalles périodiques. Pour ce faire, ajoutez la propriété **schedule** lors de la création ou de la mise à jour de l’indexeur. L'exemple ci-dessous montre une requête PUT mettant à jour l'indexeur :

```http
    PUT https://[search service name].search.windows.net/indexers/[Indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin-key]

    {
        "dataSourceName" : "[Data source name]",
        "targetIndexName" : "[Index name]",
        "schedule" : { 
            "interval" : "PT10M", 
            "startTime" : "2021-01-01T00:00:00Z"
        }
    }
```

Le paramètre **interval** est obligatoire. Il correspond à la durée entre le début de deux exécutions consécutives de l’indexeur. L'intervalle minimal autorisé est de 5 minutes, l'intervalle maximal autorisé est d'une journée. Il doit être formaté en tant que valeur « dayTimeDuration » XSD (un sous-ensemble limité d'une valeur de [durée ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Le modèle est le suivant : `P(nD)(T(nH)(nM))`. Exemples : `PT15M` toutes les 15 minutes, `PT2H` toutes les deux heures.

Pour plus d’informations sur la définition des planifications de l’indexeur, consultez [Comment planifier des indexeurs pour la Recherche cognitive Azure](search-howto-schedule-indexers.md).

## <a name="capture-new-changed-and-deleted-rows"></a>Capturer des lignes nouvelles, modifiées et supprimées

La Recherche cognitive Azure utilise **l’indexation incrémentielle** pour éviter d’avoir à réindexer toute la table ou toute la vue à chaque exécution d’un indexeur.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Stratégie de détection des modifications de limite supérieure

Cette stratégie de détection des modifications s’appuie sur une colonne « Limite supérieure » qui capture la version ou l’heure pour laquelle une ligne a été mise à jour. Si vous utilisez une vue, vous devez vous servir d’une stratégie de limite supérieure. La colonne de limite supérieure doit remplir les conditions suivantes.

#### <a name="requirements"></a>Spécifications 

* Toutes les insertions spécifient une valeur pour la colonne.
* Toutes les mises à jour d'un élément modifient également la valeur de la colonne.
* La valeur de cette colonne augmente à chaque insertion ou mise à jour.
* Les requêtes utilisant les clauses WHERE et ORDER BY suivantes peuvent être exécutées efficacement : `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

#### <a name="usage"></a>Usage

Pour utiliser une stratégie de limite supérieure, créez ou mettez à jour votre source de données comme suit :

```http
    {
        "name" : "[Data source name]",
        "type" : "mysql",
        "credentials" : { "connectionString" : "[connection string]" },
        "container" : { "name" : "[table or view name]" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[last_updated column name]"
      }
    }
```

> [!WARNING]
> Si la table source n’a pas d’index sur la colonne de limite supérieure, le délai d’attente des requêtes utilisées par l’indexeur MySQL peut expirer. En particulier, la clause `ORDER BY [High Water Mark Column]` requiert qu’un index s’exécute efficacement lorsque la table contient de nombreuses lignes.

### <a name="soft-delete-column-deletion-detection-policy"></a>Stratégie de détection des colonnes à suppression réversible
Lorsque des lignes sont supprimées de la table source, vous devez également supprimer ces lignes de l'index de recherche. Si des lignes sont physiquement supprimées de la table, Recherche cognitive Azure n’a aucun moyen de déduire la présence d’enregistrements qui n’existent plus.  Toutefois, vous pouvez utiliser la technique de la « suppression réversible » pour supprimer des lignes logiquement sans les supprimer de la table. Ajoutez une colonne à votre table ou votre vue et marquez les lignes comme supprimées à l’aide de cette colonne.

Lorsque vous utilisez la technique de suppression réversible, vous pouvez spécifier cette stratégie réversible comme suit lors de la création ou de la mise à jour de la source de données :

```http
    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }
```

**softDeleteMarkerValue** doit être une chaîne. Utilisez la représentation au format chaîne de votre valeur. Par exemple, si vous avez une colonne d’entiers dans laquelle les lignes supprimées sont marquées avec la valeur 1, utilisez `"1"`. Si vous avez une colonne BIT dans laquelle les lignes supprimées sont marquées avec la valeur booléenne True, utilisez le littéral de chaîne `True` ou `true`, la casse ne comptant pas.

<a name="TypeMapping"></a>

## <a name="mapping-between-mysql-and-azure-cognitive-search-data-types"></a>Mappage entre les types de données MySQL et Recherche cognitive Azure

> [!NOTE]
> La préversion ne prend pas en charge les types de géométrie ni les blobs.

| Type de données MySQL | Types de champs d'index cible autorisés |
| --- | --- |
| bool, boolean | Edm.Boolean, Edm.String |
| tinyint, smallint, mediumint, int, integer, year | Edm.Int32, Edm.Int64, Edm.String |
| bigint | Edm.Int64, Edm.String |
| float, double, real | Edm.Double, Edm.String |
| date, datetime, timestamp | Edm.DateTimeOffset, Edm.String |
| char, varchar, tinytext, mediumtext, text, longtext, enum, set, time | Edm.String |
| données numériques non signées, serial, decimal, dec, bit, blob, binary, geometry | N/A |


## <a name="next-steps"></a>Étapes suivantes

Félicitations ! Vous avez appris à intégrer MySQL à Recherche cognitive Azure à l’aide d’un indexeur.

+ Pour en savoir plus sur les indexeurs, consultez [Création d’indexeurs dans Recherche cognitive Azure](search-howto-create-indexers.md).
