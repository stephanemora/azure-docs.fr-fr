---
title: Indexation incrémentielle (préversion)
titleSuffix: Azure Cognitive Search
description: Configurez votre pipeline d’enrichissement de l’IA pour rendre vos données éventuellement cohérentes et gérer les mises à jour des compétences, des ensembles de compétences, des indexeurs ou des sources de données. Cette fonctionnalité est actuellement en préversion publique.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c44228d7e1456bce870765935beb011cb24626d5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790933"
---
# <a name="what-is-incremental-indexing-in-azure-cognitive-search"></a>Qu’est-ce que l’indexation incrémentielle dans la Recherche cognitive Azure ?

> [!IMPORTANT] 
> L’indexation incrémentielle est actuellement en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). L’[API REST version 2019-05-06-Preview](search-api-preview.md) fournit cette fonctionnalité. Il n’y a pas de prise en charge de portail ou de SDK .NET pour l’instant.

L’indexation incrémentielle est une nouvelle fonctionnalité de la Recherche cognitive Azure qui ajoute une mise en cache et un état au contenu enrichi d’un ensemble de compétences cognitif, ce qui vous permet de contrôler le traitement et le retraitement d’étapes individuelles dans un pipeline d’enrichissement. Cela permet non seulement de préserver votre investissement financier lors du traitement, mais également d’améliorer l’efficacité d’un système. Quand des structures et du content sont mis en cache, un indexeur peut déterminer les compétences qui ont changé et exécuter uniquement celles qui ont été modifiées, ainsi que toutes les compétences dépendantes en aval. 

Avec l’indexation incrémentielle, la version actuelle du pipeline d’enrichissement effectue le moins de travail possible pour garantir la cohérence de tous les documents de votre index. Pour les scénarios où vous souhaitez un contrôle total, vous pouvez utiliser des contrôles affinés pour remplacer les comportements attendus. Pour plus d’informations sur la configuration, consultez [Configurer l’indexation incrémentielle](search-howto-incremental-index.md).

## <a name="indexer-cache"></a>Cache d’indexeur

L’indexation incrémentielle ajoute un cache d’indexeur au pipeline d’enrichissement. L’indexeur met en cache les résultats du craquage de document ainsi que les sorties de chaque compétence pour chaque document. Quand un ensemble de compétences est mis à jour, seules les compétences ayant changé ou situées en aval sont réexécutées. Les résultats mis à jour sont écrits dans le cache. Le document est mis à jour dans l’index et la base de connaissances.

Du point de vue physique, le cache est un compte de stockage. Tous les index d’un service de recherche peuvent partager le même compte de stockage pour le cache de l’indexeur. Un identificateur de cache unique et non modifiable est affecté à chaque indexeur.

### <a name="cache-configuration"></a>Configuration du cache

Vous devez définir la propriété `cache` sur l’indexeur pour commencer à bénéficier de l’indexation incrémentielle. L’exemple suivant illustre un indexeur pour lequel la mise en cache est activée. Des parties spécifiques de cette configuration sont décrites dans les sections suivantes.

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
    "parameters": {}
}
```

Si vous définissez cette propriété pour la première fois sur un indexeur existant, vous devez également le réinitialiser, ce qui entraînera un nouveau traitement de tous les documents présents dans votre source de données. L’indexation incrémentielle a pour objectif de rendre les documents de votre index cohérents par rapport à votre source de données et à la version actuelle de votre ensemble de compétences. La réinitialisation de l’index est la première étape vers cette cohérence, car elle permet d’éliminer tous les documents enrichis par les versions précédentes de l’ensemble de compétences. Vous devez réinitialiser l’indexeur pour démarrer avec une base de référence cohérente.

### <a name="cache-lifecycle"></a>Cycle de vie du cache

Le cycle de vie du cache est géré par l’indexeur. Si la propriété `cache` définie sur l’indexeur a une valeur null ou si la chaîne de connexion change, le cache existant est supprimé. Le cycle de vie du cache est également lié au cycle de vie de l’indexeur. Si un indexeur est supprimé, le cache associé est également supprimé.

### <a name="indexer-cache-mode"></a>Mode de cache de l’indexeur

Le cache de l’indexeur peut fonctionner dans les modes suivants : les données sont écrites uniquement dans le cache, ou les données sont écrites dans le cache et utilisées pour réenrichir les documents.  Pour interrompre temporairement l’enrichissement incrémentiel, affectez à la propriété `enableReprocessing` du cache la valeur `false`. Vous pouvez ensuite reprendre l’enrichissement incrémentiel pour améliorer la cohérence en affectant à la propriété la valeur `true`. Ce contrôle est particulièrement utile quand vous souhaitez donner la priorité à l’indexation de nouveaux documents plutôt qu’à la cohérence de votre corpus de documents.

## <a name="change-detection-override"></a>Remplacement de la détection des changements

L’indexation incrémentielle vous donne un contrôle précis sur tous les aspects du pipeline d’enrichissement. Ce contrôle vous permet de gérer les situations où un changement peut avoir des conséquences inattendues. Par exemple, si vous modifiez un ensemble de compétences et si vous mettez à jour l’URL d’une compétence personnalisée, l’indexeur invalide les résultats mis en cache pour cette compétence. Si vous déplacez uniquement le point de terminaison vers une autre machine virtuelle ou si vous redéployez vos compétences avec une nouvelle clé d’accès, vous ne souhaitez pas que les documents existants soient retraités.

Pour garantir que l’indexeur effectue uniquement les enrichissements explicitement nécessaires, les mises à jour apportées à l’ensemble de compétences peuvent éventuellement affecter au paramètre de chaîne de requête `disableCacheReprocessingChangeDetection` la valeur `true`. Une fois défini, ce paramètre garantit que seules les mises à jour de l’ensemble de compétences sont validées et que le changement n’est pas évalué en ce qui concerne ses effets sur le corpus existant.

L’exemple suivant illustre l’utilisation d’une chaîne de requête. Elle fait partie de la requête, avec des paires clé-valeur séparées par un signe &. 

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

## <a name="cache-invalidation"></a>Invalidation du cache

L’inverse de ce scénario est celui où vous pouvez déployer une nouvelle version d’une compétence personnalisée sans que cela entraîne le moindre changement dans le pipeline d’enrichissement. Toutefois, vous avez besoin d’une compétence spécifique invalidée et de tous les documents affectés retraités pour refléter les avantages d’un modèle mis à jour. Dans ce genre de cas, vous pouvez appeler l’opération d’invalidation de compétences sur l’ensemble de compétences. L’API de réinitialisation des compétences accepte une requête POST avec la liste des sorties de compétences du cache qui doivent être invalidées. Pour plus d’informations sur l’API de réinitialisation des compétences, consultez [Réinitialiser l’indexeur (API REST de la recherche)](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="bi-directional-change-detection"></a>Détection des changements bidirectionnels

Les indexeurs déplacent et traitent les nouveaux documents, mais ils peuvent désormais améliorer la cohérence de documents déjà traités. Avec cette nouvelle fonctionnalité, il est important de comprendre comment les changements apportés aux composants de votre pipeline d’enrichissement affectent le travail de l’indexeur. L’indexeur met en file d’attente le travail à effectuer quand il identifie un changement invalidant ou incohérent par rapport au contenu mis en cache.

### <a name="invalidating-changes"></a>Changements invalidants

Les changements invalidants sont rares mais ils ont un impact significatif sur l’état de votre pipeline d’enrichissement. Un changement invalidant est un changement qui rend la totalité du cache non valide. La mise à jour de votre source de données est un exemple de changement invalidant. Dans les scénarios où vous savez que le changement ne doit pas invalider le cache, par exemple la rotation de la clé sur le compte de stockage, le paramètre de chaîne de requête `ignoreResetRequirement` doit avoir la valeur `true` pendant l’opération de mise à jour de la ressource spécifique pour garantir le non-rejet de l’opération.

Voici la liste complète des changements qui invalident votre cache :

* Changement de votre type de source de données
* Changement du conteneur de source de données
* Informations d’identification de la source de données
* Stratégie de détection des changements apportés à la source de données
* Stratégie de détection de la suppression des sources de données
* Mappages de champs de l’indexeur
* Paramètres d’indexeur
    * Mode d’analyse
    * Extensions de noms de fichiers exclues
    * Extensions de noms de fichiers indexées
    * Indexer les métadonnées de stockage uniquement pour les documents volumineux
    * En-têtes de texte délimité
    * Délimiteur de texte délimité
    * Racine du document
    * Action d’image (changements apportés au mode d’extraction des images)

### <a name="inconsistent-changes"></a>Changements incohérents

Toute mise à jour de votre ensemble de compétences qui modifie une compétence est un exemple de changement incohérent. Cette modification peut rendre une partie du cache incohérente. L’indexeur va identifier le travail nécessaire au rétablissement de la cohérence.  

Liste complète des changements qui entraînent une incohérence du cache :

* Une compétence de l’ensemble de compétences est d’un type distinct. Mise à jour du type OData de la compétence
* Mise à jour des paramètres spécifiques à une compétence, par exemple l’URL, les paramètres par défaut ou tout autre paramètre
* Changement des sorties d’une compétence, la compétence retourne des sorties supplémentaires ou distinctes
* Mises à jour de compétences entraînant un changement d’ancêtres, par exemple un changement du chaînage des compétences entrées de compétences
* Toute invalidation de compétence amont, si une compétence qui fournit une entrée à cette compétence est mise à jour
* Mises à jour de l’emplacement de projection de la base de connaissances, qui entraînent une reprojection des documents
* Apport de changements aux projections de la base de connaissances, qui entraînent une reprojection des documents
* Changements des mappages de champs de sortie d’un indexeur, qui entraînent une reprojection des documents dans l’index

## <a name="rest-api-reference-for-incremental-indexing"></a>Informations de référence sur les API REST pour l’indexation incrémentielle

REST `api-version=2019-05-06-Preview` fournit les API pour l’indexation incrémentielle, avec des ajouts aux indexeurs, aux ensembles de compétences et aux sources de données. La documentation de référence ne contient actuellement pas ces ajouts. La section suivante décrit les changements d’API.

### <a name="indexers"></a>Indexeurs

[Créer un indexeur](https://docs.microsoft.com/rest/api/searchservice/create-indexer) et [Mettre à jour l’indexeur](https://docs.microsoft.com/rest/api/searchservice/update-indexer) exposent désormais de nouvelles propriétés relatives au cache :

* `StorageAccountConnectionString`: Chaîne de connexion au compte de stockage servant à mettre en cache les résultats intermédiaires.

* `CacheId`: `cacheId` est l’identificateur du conteneur dans le compte de stockage `annotationCache` qui est utilisé en tant que cache pour cet indexeur. Ce cache est unique pour cet indexeur. Si ce dernier est supprimé et recréé avec le même nom, `cacheId` est regénéré. `cacheId` ne peut pas être défini, il est toujours généré par le service.

* `EnableReprocessing`: A la valeur `true` par défaut. Quand il a la valeur `false`, les documents continuent d’être écrits dans le cache, mais aucun document existant n’est retraité en fonction des données du cache.

Certains indexeurs (par l’intermédiaire de [sources de données](https://docs.microsoft.com/rest/api/searchservice/create-data-source)) récupèrent des données par le biais de requêtes. Pour les requêtes qui récupèrent des données, les indexeurs prennent également en charge un nouveau paramètre de chaîne de requête : `ignoreResetRequirement` doit avoir la valeur `true` quand votre action de mise à jour ne doit pas invalider le cache.

### <a name="skillsets"></a>Ensemble de compétences

Les ensembles de compétences ne prennent en charge aucune nouvelle opération, mais ils prennent en charge un nouveau paramètre de chaîne de requête : `disableCacheReprocessingChangeDetection` doit avoir la valeur `true` quand vous ne souhaitez pas mettre à jour des documents existants en fonction de l’action en cours.

### <a name="datasources"></a>Sources de données

Les sources de données ne prennent en charge aucune nouvelle opération, mais elles prennent en charge un nouveau paramètre de chaîne de requête : `ignoreResetRequirement` doit avoir la valeur `true` quand votre action de mise à jour ne doit pas invalider le cache.

## <a name="best-practices"></a>Bonnes pratiques

L’approche recommandée pour l’utilisation de l’indexation incrémentielle consiste à configurer cette dernière en affectant un nouvel indexeur à la propriété de cache, ou à réinitialiser un indexeur existant et à définir la propriété de cache.

Utilisez la méthode `ignoreResetRequirement` avec parcimonie, car cela peut entraîner une incohérence involontaire de vos données, qui n’est pas facile à détecter.

## <a name="takeaways"></a>Éléments importants à retenir

L’indexation incrémentielle est une fonctionnalité puissante qui étend le suivi des changements de la source de données à tous les aspects du pipeline d’enrichissement incluant la source de données, la version actuelle de votre ensemble de compétences et l’indexeur. Au fur et à mesure que vos compétences, vos ensembles de compétences ou vos enrichissements évoluent, le pipeline d’enrichissement vous permet d’effectuer le moins de travail possible tout en maintenant la cohérence de vos documents.

## <a name="next-steps"></a>Étapes suivantes

Pour bien démarrer avec l’indexation incrémentielle, ajoutez un cache à un indexeur existant, ou ajoutez le cache au moment de la définition d’un nouvel indexeur.

> [!div class="nextstepaction"]
> [Configurer l’indexation incrémentielle](search-howto-incremental-index.md)
