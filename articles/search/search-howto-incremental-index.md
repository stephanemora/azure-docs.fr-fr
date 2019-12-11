---
title: Ajouter l’indexation incrémentielle (préversion)
titleSuffix: Azure Cognitive Search
description: Activez le suivi des modifications et préservez l'état du contenu enrichi pour un traitement contrôlé dans un ensemble de compétences cognitives. Cette fonctionnalité est actuellement disponible en préversion publique.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 92da697c95f2b9ea544bb1f9bfa689c13bd0d2ae
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806760"
---
# <a name="how-to-set-up-incremental-indexing-of-enriched-documents-in-azure-cognitive-search"></a>Configuration de l'indexation incrémentielle de documents enrichis dans la Recherche cognitive Azure

> [!IMPORTANT] 
> L’indexation incrémentielle est actuellement en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). L’[API REST version 2019-05-06-Preview](search-api-preview.md) fournit cette fonctionnalité. Il n’y a pas de prise en charge de portail ou de SDK .NET pour l’instant.

Cet article explique comment ajouter l'état et la mise en cache à des documents enrichis traversant un pipeline d'enrichissement Recherche cognitive Azure afin de procéder à l'indexation incrémentielle de documents issus de n'importe laquelle des sources de données prises en charge. Par défaut, un ensemble de compétences n'a pas d'état, et toute modification de sa composition nécessite une réexécution complète de l'indexeur. L'indexation incrémentielle permet à l'indexeur de déterminer quelles parties du pipeline ont été modifiées, en réutilisant les enrichissements existants pour les parties inchangées et en révisant les enrichissements pour les étapes modifiées. Le contenu mis en cache est placé dans le Stockage Azure.

Si le processus de configuration des indexeurs ne vous est pas familier, commencez par la [Présentation de l'indexeur](search-indexer-overview.md), puis passez aux [Ensembles de compétences](cognitive-search-working-with-skillsets.md) pour en savoir plus sur les pipelines d'enrichissement. Pour plus d'informations sur les concepts clés, reportez-vous à [Indexation incrémentielle](cognitive-search-incremental-indexing-conceptual.md).

## <a name="modify-an-existing-indexer"></a>Modifier un indexeur existant

Si vous disposez d'un indexeur, procédez comme suit pour activer l'indexation incrémentielle.

### <a name="step-1-get-the-indexer"></a>Étape 1 : Obtenir l’indexeur

Commencez avec un indexeur existant valide pour lequel la source de données et l'index requis ont déjà été définis. Votre indexeur doit être exécutable. À l'aide d'un client d'API, créez une [requête GET](https://docs.microsoft.com/rest/api/searchservice/get-indexer) pour obtenir la configuration actuelle de l'indexeur auquel vous souhaitez ajouter l'indexation incrémentielle.

```http
GET https://[service name].search.windows.net/indexers/[your indexer name]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-2-add-the-cache-property"></a>Étape 2 : Ajouter la propriété du cache

Modifiez la réponse de la demande GET pour ajouter la propriété `cache` à l’indexeur. L’objet cache nécessite une seule propriété, `storageConnectionString`, qui est la chaîne de connexion au compte de stockage. 

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true,
        "id" : "Auto generated Id you do not need to set"
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": {
        "configuration": {
            "enableAnnotationCache": true
        }
    }
}
```
#### <a name="enable-reporocessing"></a>Activer le retraitement

Vous pouvez éventuellement définir dans le cache la propriété booléenne `enableReprocessing` dont la valeur par défaut est true. L’indicateur `enableReprocessing` vous permet de contrôler le comportement de votre indexeur. Dans les scénarios où vous voulez que l’indexeur hiérarchise l’ajout de nouveaux documents à l’index, vous devez définir l’indicateur sur false. Une fois que votre indexeur reflète les nouveaux documents, faire passer l’indicateur sur true permet alors à l’indexeur de commencer à mener les documents existants vers une cohérence éventuelle. Pendant la période où il a la valeur false, l’indicateur `enableReprocessing` ne fait qu’écrire dans le cache, mais il ne traite pas les documents existants en fonction des changements identifiés apportés au pipeline d’enrichissement.

### <a name="step-3-reset-the-indexer"></a>Étape 3 : Réinitialiser l’indexeur

> [!NOTE]
> La réinitialisation de l'indexeur entraînera un nouveau traitement de tous les documents de votre source de données afin que le contenu puisse être mis en cache. Tous les enrichissements cognitifs sont réexécutés sur tous les documents.
>

Une réinitialisation de l’indexeur est nécessaire lors de la configuration de l’indexation incrémentielle pour que les indexeurs existants puissent garantir que tous les documents sont dans un état cohérent. Réinitialisez l'indexeur à l'aide de l'[API REST](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

```http
POST https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-4-save-the-updated-definition"></a>Étape 4 : Enregistrer la définition mise à jour

Mettez à jour la définition de l’indexeur avec une demande PUT. Le corps de la demande doit contenir la définition de l’indexeur mis à jour.

```http
PUT https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
{
    "name" : "your indexer name",
    ...
    "cache": {
        "storageConnectionString": "[your storage connection string]",
        "enableReprocessing": true
    }
}
```

Si vous émettez à présent une autre demande GET sur l’indexeur, la réponse du service inclut une propriété `cacheId` dans l’objet de cache. `cacheId` est le nom du conteneur qui contiendra tous les résultats mis en cache et l’état intermédiaire de chaque document traité par cet indexeur.

## <a name="enable-incremental-indexing-on-new-indexers"></a>Activer l'indexation incrémentielle sur les nouveaux indexeurs

Pour configurer l'indexation incrémentielle d'un nouvel indexeur, incluez la propriété `cache` dans la charge utile de la définition de l'indexeur. Veillez à utiliser la version `2019-05-06-Preview` de l’API.

## <a name="overriding-incremental-indexing"></a>Remplacement de l’indexation incrémentielle

Lorsqu’elle est configurée, l’indexation incrémentielle effectue le suivi des modifications dans votre pipeline d’indexation et conduit les documents jusqu’à leur cohérence éventuelle dans vos index et projections. Dans certains cas, vous devez remplacer ce comportement pour vous assurer que l’indexeur n’effectue pas de travail supplémentaire suite à une mise à jour du pipeline d’indexation. Par exemple, la mise à jour de la chaîne de connexion à la source de données nécessite la réinitialisation de l’indexeur et la réindexation de tous les documents, dans la mesure où la source de données a changé. Mais, si vous mettiez seulement à jour la chaîne de connexion avec une nouvelle clé, vous ne voudriez pas que cette modification entraîne la moindre mise à jour des documents existants. Inversement, vous souhaiterez peut-être que l’indexeur invalide le cache et enrichisse les documents même si aucune modification n’est apportée au pipeline d’indexation. Par exemple, vous souhaiterez peut-être invalider l’indexeur si vous deviez redéployer une compétence personnalisée avec un nouveau modèle et souhaitiez que la compétence soit réexécutée sur tous vos documents.

### <a name="override-reset-requirement"></a>Remplacer l’exigence de réinitialisation

Lorsque vous apportez des modifications au pipeline d’indexation, toute modification entraînant une invalidation du cache nécessite une réinitialisation de l’indexeur. Si vous apportez une modification au pipeline de l’indexeur et que vous ne souhaitez pas que le suivi des modifications invalide le cache, vous devez définir le paramètre de chaîne de requête `ignoreResetRequirement` sur `true` pour les opérations sur l’indexeur ou la source de données.

### <a name="override-change-detection"></a>Remplacer la détection des modifications

Lors de mises à jour des compétences qui entraîneraient le marquage de documents comme étant incohérents, comme par exemple la mise à jour de l’URL d’une compétence personnalisée, lorsque la compétence est redéployée, définissez le paramètre de chaîne de requête `disableCacheReprocessingChangeDetection` sur `true` sur les mises à jour des compétences.

### <a name="force-change-detection"></a>Forcer la détection des modifications

Lorsque vous souhaitez que le pipeline d’indexation reconnaisse une modification apportée à une entité externe, comme le déploiement d’une nouvelle version d’une compétence personnalisée, vous devez mettre à jour les compétences et « toucher » la compétence spécifique en modifiant la définition de la compétence, notamment l’URL pour forcer la détection des modifications et invalider le cache pour cette compétence.

## <a name="next-steps"></a>Étapes suivantes

Cet article traite de l'indexation incrémentielle pour les indexeurs qui incluent des ensembles de compétences. Pour approfondir vos connaissances, consultez des articles consacrés à la réindexation en général, applicables à tous les scénarios d'indexation de la Recherche cognitive Azure.

+ [Régénération d'un index Recherche cognitive Azure](search-howto-reindex.md). 
+ [Indexation de grands ensembles de données dans la Recherche cognitive Azure](search-howto-large-index.md). 
