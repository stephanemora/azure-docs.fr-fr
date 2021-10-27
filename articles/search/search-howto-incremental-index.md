---
title: Activer la mise en cache pour l’enrichissement incrémentiel (préversion)
titleSuffix: Azure Cognitive Search
description: Activez la mise en cache de contenu enrichi pour une réutilisation potentielle lors de la modification de compétences et de projections en aval dans un pipeline d’enrichissement par IA.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: b905517a2558eb6bc01b4ba218f0ecde7e830ebf
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130138603"
---
# <a name="enable-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Activation de la mise en cache pour l’enrichissement incrémentiel dans Recherche cognitive Azure

> [!IMPORTANT] 
> Cette fonctionnalité est en préversion publique sous les [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). L’[API REST en préversion](/rest/api/searchservice/index-preview) prend en charge cette fonctionnalité

Cet article explique comment ajouter la mise en cache à un pipeline d’enrichissement afin de pouvoir modifier les étapes d’enrichissement en aval sans avoir à effectuer une régénération complète à chaque fois. Par défaut, un ensemble de compétences n'a pas d'état, et toute modification de sa composition nécessite une réexécution complète de l'indexeur. Grâce à l’[**enrichissement incrémentiel**](cognitive-search-incremental-indexing-conceptual.md), l’indexeur peut déterminer les parties de l’arborescence de documents qui doivent être actualisées en fonction des modifications détectées dans l’ensemble de compétences ou les définitions de l’indexeur. La sortie traitée existante est conservée et réutilisée dans la mesure du possible. 

Le contenu mis en cache est placé dans le stockage Azure à l’aide des informations de compte que vous fournissez. Le conteneur, nommé `ms-az-search-indexercache-<alpha-numerc-string>`, est créé lors de l’exécution de l’indexeur. Il doit être considéré comme un composant interne géré par votre service de recherche et ne doit pas être modifié.

Si le processus de configuration des indexeurs ne vous est pas familier, commencez par la [Présentation de l'indexeur](search-indexer-overview.md), puis passez aux [Ensembles de compétences](cognitive-search-working-with-skillsets.md) pour en savoir plus sur les pipelines d'enrichissement. Pour plus d’informations sur les concepts clés, consultez [Enrichissement incrémentiel](cognitive-search-incremental-indexing-conceptual.md).

## <a name="prerequisites"></a>Prérequis

Stockage Azure est utilisé pour stocker les enrichissements mis en cache. Le compte de stockage doit être de type [v2 à usage général](../storage/common/storage-account-overview.md#types-of-storage-accounts).

Les API d’aperçu ou les kits de développement logiciel (SDK) Azure bêta sont obligatoires pour activer le cache sur un indexeur. Actuellement, le portail ne fournit pas d’option de mise en cache de l’enrichissement.

## <a name="enable-on-new-indexers"></a>Activer sur de nouveaux indexeurs

Sur les nouveaux indexeurs, ajoutez la propriété « cache » dans la charge utile de la définition de l’indexeur lors de l’appel de l’[indexeur Créer ou Mettre à jour (2021-04-30-Preview)](/rest/api/searchservice/preview-api/create-or-update-indexer). Vous pouvez également utiliser la préversion d’API précédente, 2020-06-30-Preview.

```https
POST https://[service name].search.windows.net/indexers?api-version=2021-04-30-Preview
    {
        "name": "<YOUR-INDEXER-NAME>",
        "targetIndexName": "<YOUR-INDEX-NAME>",
        "dataSourceName": "<YOUR-DATASOURCE-NAME>",
        "skillsetName": "<YOUR-SKILLSET-NAME>",
        "cache" : {
            "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
            "enableReprocessing": true
        },
        "fieldMappings" : [],
        "outputFieldMappings": [],
        "parameters": []
        }
    }
```

## <a name="enable-on-existing-indexers"></a>Activer sur des indexeurs existants

Pour les indexeurs existants qui possèdent déjà un ensemble de compétences, procédez comme suit pour ajouter la mise en cache. Dans le cadre d’une opération unique, réinitialisez et réexécutez l’indexeur entièrement pour charger le cache.

### <a name="step-1-get-the-indexer-definition"></a>Étape 1 : Obtenir la définition de l’indexeur

Démarrez par un indexeur de travail qui contient les composants suivants : source de données, ensemble de compétences, index. À l’aide d’un client d’API, envoyez une requête [GET Indexer](/rest/api/searchservice/get-indexer) pour récupérer l’indexeur. Lorsque vous utilisez la préversion de l’API pour faire une requête GET sur l’indexeur, une propriété « cache » définie sur Null est ajoutée automatiquement aux définitions.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2021-04-30-Preview
    Content-Type: application/json
    api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-2-set-the-cache-property"></a>Étape 2 : Définir la propriété cache

Dans la définition de l’index, modifiez « cache » de façon à inclure les propriétés obligatoires et facultatives suivantes :

+ (Obligatoire) `storageConnectionString` doit être défini sur une chaîne de connexion Stockage Azure.
+ (Facultatif) La propriété booléenne `enableReprocessing` (`true` par défaut) indique que l’enrichissement incrémentiel est activé. Définissez-la sur `false` si vous souhaitez interrompre le traitement incrémentiel pendant que d’autres opérations gourmandes en ressources, telles que l’indexation de nouveaux documents, sont en cours d’exécution, puis la revenir à `true` plus tard.

```http
POST https://[service name].search.windows.net/indexers?api-version=2021-04-30-Preview
    {
        "name": "<YOUR-INDEXER-NAME>",
        "targetIndexName": "<YOUR-INDEX-NAME>",
        "dataSourceName": "<YOUR-DATASOURCE-NAME>",
        "skillsetName": "<YOUR-SKILLSET-NAME>",
        "cache" : {
            "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
            "enableReprocessing": true
        },
        "fieldMappings" : [],
        "outputFieldMappings": [],
        "parameters": []
    }
```

### <a name="step-3-reset-the-indexer"></a>Étape 3 : Réinitialiser l’indexeur

[Réinitialiser l’indexeur](/rest/api/searchservice/reset-indexer) est nécessaire lors de la configuration de l’enrichissement incrémentiel pour que les indexeurs existants puissent garantir que tous les documents sont dans un état cohérent. Vous pouvez utiliser le portail ou un client d’API pour cette tâche.

```https
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2021-04-30-Preview
    Content-Type: application/json
    api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-indexer"></a>Étape 4 : Enregistrer l’indexeur

[Indexeur Mettre à jour (2021-04-30-Preview)](/rest/api/searchservice/preview-api/create-or-update-indexer) avec une requête PUT, où le corps de la requête comprend « cache ».

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2021-04-30-Preview
    Content-Type: application/json
    api-key: [YOUR-ADMIN-KEY]
    {
        "name" : "<YOUR-INDEXER-NAME>",
        ...
        "cache": {
            "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
            "enableReprocessing": true
        }
    }
```

Si vous émettez à présent une autre requête GET sur l’indexeur, la réponse du service inclut une propriété `ID` dans l’objet cache. La chaîne alphanumérique est ajoutée au nom du conteneur contenant tous les résultats mis en cache et l’état intermédiaire de chaque document traité par cet indexeur. L’ID sera utilisé pour nommer le cache de manière unique dans le stockage d’objets blob.

```http
    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }
```

### <a name="step-5-run-the-indexer"></a>Étape 5 : Exécuter l’indexeur

Pour exécuter l’indexeur, vous pouvez utiliser le portail ou l’API. Dans le portail, à partir de la liste des indexeurs, sélectionnez l’indexeur souhaité, puis cliquez sur **Exécuter**. L’un des avantages à utiliser le portail est que vous pouvez surveiller l’état de l’indexeur, noter la durée du travail et connaître le nombre de documents traités. Les pages du portail sont actualisées à des intervalles de quelques minutes.

Vous pouvez également utiliser REST pour [exécuter l’indexeur](/rest/api/searchservice/run-indexer) :

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

> [!NOTE]
> Une réinitialisation et une réexécution de l’indexeur entraînent une régénération complète afin que le contenu puisse être mis en cache. Tous les enrichissements cognitifs sont réexécutés sur tous les documents. La réutilisation du contenu enrichi à partir du cache commence après le chargement du cache.
>

## <a name="check-for-cached-output"></a>Rechercher la sortie mise en cache

Recherchez le cache dans Stockage Azure, sous le conteneur blob. Le nom du conteneur sera `ms-az-search-indexercache-<some-alphanumeric-string>`.

Un cache est créé et utilisé par un indexeur. Son contenu n’est pas lisible par l’homme.

Pour vérifier si le cache est opérationnel, modifiez un ensemble de compétences et exécutez l’indexeur, puis comparez les métriques avant et après pour le nombre de documents et le temps d’exécution. 

Les ensemble de compétences qui incluent l’analyse d’images et la reconnaissance optique de caractères (OCR) des documents numérisés constituent de bons scénarios de test. Si vous modifiez une compétence de texte en aval ou toute compétence qui n’est pas liée à une image, l’indexeur peut récupérer tout le contenu des images et de l’OCR traité précédemment à partir du cache, en mettant à jour et en traitant uniquement les modifications relatives au texte indiquées par vos modifications.  Vous pouvez vous attendre à voir moins de documents dans le nombre de documents d’exécution d’indexeur, des durées d’exécution plus courtes et moins de frais sur votre facture. 

L’[ensemble de fichiers](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/ai-enrichment-mixed-media) utilisé dans les [didacticiels cog-search-demo](cognitive-search-tutorial-blob.md) est un cas de test utile, car il contient 14 fichiers de différents formats JPG, PNG, HTML, DOCX, PPTX et d’autres types. Changez `en` en `es` ou en une autre langue dans la compétence de traduction de texte pour les tests de preuve de concept de l’enrichissement incrémentiel.

## <a name="common-errors"></a>Erreurs courantes

L’erreur suivante se produit si vous oubliez de spécifier une préversion de l’API dans la requête :

`"The request is invalid. Details: indexer : A resource without a type name was found, but no expected type was specified. To allow entries without type information, the expected type must also be specified when the model is specified."`

Une erreur de demande incorrecte 400 se produit également si une condition d’indexeur est manquante. Le message d’erreur spécifie toutes les dépendances manquantes.

## <a name="next-steps"></a>Étapes suivantes

L’enrichissement incrémentiel s’applique aux indexeurs qui contiennent des ensembles de compétences, ce qui fournit du contenu réutilisable pour les index et les bases de connaissances. Pour plus d’informations sur la mise en cache et les ensembles de compétences, consultez les liens suivants.

+ [Enrichissement incrémentiel (cycle de vie et gestion)](cognitive-search-incremental-indexing-conceptual.md)
+ [Concepts et composition des ensembles de compétences](cognitive-search-working-with-skillsets.md)
+ [Créer un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Tutoriel : Utiliser REST et l’IA pour générer du contenu pouvant faire l’objet de recherches à partir d’objets blob Azure](cognitive-search-tutorial-blob.md)