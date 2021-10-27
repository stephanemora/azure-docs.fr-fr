---
title: Concepts d’enrichissement incrémentiel (préversion)
titleSuffix: Azure Cognitive Search
description: Mettez en cache le contenu intermédiaire et les modifications incrémentielles du pipeline d’enrichissement par IA dans le stockage Azure pour protéger les investissements dans les documents traités existants. Cette fonctionnalité est actuellement disponible en préversion publique.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/17/2021
ms.openlocfilehash: cb04bbea71588ea8d9fa1f1c4734b10e3d0b6792
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179200"
---
# <a name="incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Enrichissement incrémentiel et mise en cache dans Recherche cognitive Azure

> [!IMPORTANT] 
> Cette fonctionnalité est en préversion publique dans le cadre de [Conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). L’[API REST en préversion](/rest/api/searchservice/index-preview) prend en charge cette fonctionnalité.

L’*enrichissement incrémentiel* fait référence à l’utilisation d’enrichissements mis en cache lors de [l’exécution des ensembles de compétences](cognitive-search-working-with-skillsets.md) afin que seuls les nouveaux documents et les compétences et modifiées bénéficient du traitement par l’intelligence artificielle. Le cache contient les résultats du [craquage de document](search-indexer-overview.md#document-cracking) ainsi que les sorties de chaque compétence pour chaque document. Étant donné que l’extraction d’images et le traitement IA sont des [événements facturables](search-sku-manage-costs.md#billable-events), l’activation du cache réduit le coût et le temps de traitement de l’enrichissement par l’intelligence artificielle. 

Lorsque vous activez la mise en cache, l’indexeur évalue vos mises à jour pour déterminer si les enrichissements existants peuvent être extraits du cache. Le contenu de l’image et du texte de la phase de craquage de document, ainsi que les sorties de compétences qui sont en amont ou orthogonales de vos modifications, sont susceptibles d’être réutilisables.

Après avoir effectué les enrichissements incrémentiels comme indiqué par la mise à jour de l’ensemble de compétences, les résultats actualisés sont réécrits dans le cache, ainsi que dans l’index de recherche ou la base de connaissances.

## <a name="cache-configuration"></a>Configuration du cache

Physiquement, le cache est stocké dans un conteneur d’objets blob de votre compte de stockage Azure, un par indexeur. Chaque indexeur se voir affecté un identificateur de cache unique et non modifiable qui correspond au conteneur qu’il utilise.

Le cache est créé lorsque vous spécifiez la propriété « cache » et que vous exécutez l’indexeur. Seul le contenu enrichi peut être mis en cache. Si votre indexeur n’a pas d’ensemble de compétences qui lui est associé, la mise en cache ne s’applique pas. 

L’exemple suivant illustre un indexeur pour lequel la mise en cache est activée. Pour obtenir des instructions complètes, consultez [activer la mise en cache d’enrichissement](search-howto-incremental-index.md). Notez que lors de l’ajout de la propriété de cache, utilisez la préversion de l’API, 2020-06-30-Preview ou version ultérieure, sur la demande.

```json
POST https://[search service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    {
        "name": "myIndexerName",
        "targetIndexName": "myIndex",
        "dataSourceName": "myDatasource",
        "skillsetName": "mySkillset",
        "cache" : {
            "storageConnectionString" : "<Your storage account connection string>",
            "enableReprocessing": true
        },
        "fieldMappings" : [],
        "outputFieldMappings": [],
        "parameters": []
    }
```

## <a name="cache-management"></a>Gestion du cache

Le cycle de vie du cache est géré par l’indexeur. Si un indexeur est supprimé, son cache est également supprimé. Si la propriété « cache » définie sur l’indexeur a une valeur null ou si la chaîne de connexion est modifiée, le cache existant est supprimé lors de la prochaine exécution de l’indexeur. 

Alors que l’enrichissement incrémentiel est conçu pour détecter les modifications et y répondre sans aucune intervention de votre part, vous pouvez utiliser certains paramètres pour invoquer des comportements spécifiques :

+ [Classer les nouveaux documents par ordre de priorité](#Prioritize-new-documents)
+ [Ignorer les vérifications des ensembles de compétences](#Bypass-skillset-checks)
+ [Ignorer les vérifications des sources de données](#Bypass-data-source-check)
+ [Forcer l’évaluation des ensembles de compétences](#Force-skillset-evaluation)

<a name="Prioritize-new-documents"></a>

### <a name="prioritize-new-documents"></a>Classer les nouveaux documents par ordre de priorité

La propriété « cache » comprend un paramètre « enableReprocessing ». Elle est utilisée pour contrôler le traitement des documents entrants déjà représentés dans le cache. Lorsque la valeur est vraie (valeur par défaut), les documents qui se trouvent déjà dans le cache sont traités à nouveau lorsque vous réexécutez l’indexeur, en supposant que la mise à jour de vos compétences affecte ce document. 

Lorsque la valeur est fausse, les documents existants ne sont pas retraités, ce qui permet de hiérarchiser efficacement le nouveau contenu entrant par rapport au contenu existant. Vous devez uniquement définir « enableReprocessing » sur « false » de manière temporaire. Le fait d’attribuer la valeur true à « enableReprocessing » la plupart du temps vous êtes assure que tous les documents, qu’ils soient nouveaux ou existants, sont valides conformément à la définition de l’ensemble de compétences actuel.

<a name="Bypass-skillset-checks"></a>

### <a name="bypass-skillset-evaluation"></a>Ignorer l’évaluation des ensembles de compétences

La modification d’une compétence et le retraitement de cette compétence vont généralement de pair. Toutefois, certaines modifications des compétences ne n’entraînent pas de retraitement (par exemple, le déploiement d’une compétence personnalisée vers un nouvel emplacement ou avec une nouvelle clé d’accès). La plupart du temps, il s’agit de modifications périphériques qui n’ont pas d’impact réel sur la sorties des compétences. 

Si vous savez que la modification apportée à la compétence est en effet superficielle, vous pouvez remplacer l’évaluation des compétences en définissant le paramètre « disableCacheReprocessingChangeDetection » sur vrai :

1. Appelez [Mettre à jour l’ensemblle de compétences](/rest/api/searchservice/update-skillset) et modifiez la définition de l’ensemble de compétences.
1. Ajoutez le paramètre « disableCacheReprocessingChangeDetection=true » à la demande.
1. Envoyer la modification.

Ce paramètre garantit que seules les mises à jour de la définition de l’ensemble de compétences sont validées et que le changement n’est pas évalué en ce qui concerne ses effets sur le cache existant. Utilisez la préversion de l’API 2020-06-30-Preview ou version ultérieure.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30-Preview
    {
        "disableCacheReprocessingChangeDetection" : true
    }
```

<a name="Bypass-data-source-check"></a>

### <a name="bypass-data-source-validation-checks"></a>Ignorer les vérifications de validation de la source de données

La plupart des modifications apportées à la définition de source de données invalident le cache. Toutefois, dans les scénarios où vous savez qu’une modification ne doit pas invalider le cache, par exemple, la modification d’une chaîne de connexion ou la rotation de la clé sur le compte de stockage, ajoutez le paramètre « ignoreResetRequirement » lors de la mise à jour de la source de données. La définition de ce paramètre sur vrai permet à la validation se poursuivre sans déclencher de réinitialisation qui entraînerait la reconstruction de tous les objets et leur remplissage complet.

```http
PUT https://[search service].search.windows.net/datasources/[data source name]?api-version=2020-06-30-Preview
    {
        "ignoreResetRequirement" : true
    }
```

<a name="Force-skillset-evaluation"></a>

### <a name="force-skillset-evaluation"></a>Forcer l’évaluation des ensembles de compétences

L’objectif du cache est d’éviter les traitements inutiles, mais supposons que vous apportiez une modification à une compétence que l’indexeur ne détecte pas (par exemple, en modifiant un texte dans du code externe, par exemple une compétence personnalisée).

Dans ce cas, vous pouvez utiliser les [compétences de réinitialisation](/rest/api/searchservice/preview-api/reset-skills) pour forcer le retraitement d’une compétence en particulier, y compris les compétences en aval qui dépendent du résultat de cette compétence. Cette API accepte la requête POST avec une liste de compétences qui doit être invalidée et marquée pour retraitement. Après avoir réinitialisé les compétences, envoyez une demande [Exécuter l’indexeur](/rest/api/searchservice/run-indexer) pour appeler le traitement du pipeline.

## <a name="re-cache-specific-documents"></a>Remettre en cache des documents spécifiques

La [réinitialisation d’un indexeur](/rest/api/searchservice/reset-indexer) entraîne le retraitement de tous les documents du corpus de recherche. Dans les scénarios où seuls quelques documents doivent être retraités, utilisez [Réinitialiser les documents (préversion)](/rest/api/searchservice/preview-api/reset-documents) pour forcer le retraitement de documents spécifiques. Lorsqu’un document est réinitialisé, l’indexeur invalide le cache de ce document, qui est retraité en le lisant à partir de la source de données. Pour plus d'informations, consultez [Exécuter ou réinitialiser des indexeurs, des compétences et des documents](search-howto-run-reset-indexers.md).

Pour réinitialiser des documents spécifiques, la demande contient la liste des clés de document telles qu’elles sont lues à partir de l’index de recherche. Si la clé est mappée à un champ de la source de données externe, la valeur que vous fournissez doit être celle utilisée dans l’index de recherche.

Selon la façon dont l’API est appelée, la demande ajoute, remplace ou met en file d’attente la liste des clés :

+ En appelant l’API plusieurs fois avec des clés différentes, les nouvelles clés sont ajoutées à la liste des clés de document à réinitialiser. 

+ Si vous appelez l’API avec le paramètre de chaîne de requête « overwrite » défini sur true, la liste actuelle des clés de document à réinitialiser est remplacée par la charge utile de la demande.

+ Si vous appelez uniquement l’API, les clés de document sont ajoutées à la file d’attente du travail effectué par l’indexeur. Lorsque l’indexeur est appelé par la suite (de manière planifiée ou à la demande), il donne la priorité au traitement de la réinitialisation des clés de document par rapport à toute autre modification provenant de la source de données.

L’exemple suivant illustre une demande de réinitialisation de document :

```http
POST https://[search service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
    {
        "documentKeys" : [
            "key1",
            "key2",
            "key3"
        ]
    }
```

## <a name="changes-that-invalidate-the-cache"></a>Modifications qui invalident le cache

Une fois que vous avez activé un cache, l’indexeur évalue les modifications apportées à la composition de votre pipeline pour déterminer le contenu qui peut être réutilisé et celui qui nécessite un retraitement. Cette section énumère les modifications qui invalident directement le cache, suivies des modifications qui déclenchent un traitement incrémentiel. 

Un changement invalidant est un changement qui rend la totalité du cache non valide. La mise à jour de votre source de données est un exemple de changement invalidant. Voici la liste complète des modifications apportées aux différentes parties du pipeline de l’indexeur qui pourraient invalider votre cache :

+ Modification du type de source de données
+ Modification du conteneur de source de données
+ Modification des informations d’identification de la source de données
+ Modification de la stratégie de détection de la source de données
+ Modification de la stratégie de suppression de la source de données
+ Modification des mappages des champs de l’indexeur
+ Modification des paramètres de l’indexeur :
  + Mode d’analyse
  + Extensions de noms de fichiers exclues
  + Extensions de noms de fichiers indexées
  + Indexer les métadonnées de stockage uniquement pour les documents volumineux
  + En-têtes de texte délimité
  + Délimiteur de texte délimité
  + Racine du document
  + Action d’image (changements apportés au mode d’extraction des images)

## <a name="changes-that-trigger-incremental-processing"></a>Modifications qui déclenchent un traitement incrémentiel

Le traitement incrémentiel évalue la définition de votre ensemble de compétences et détermine les compétences à réexécuter, en mettant à jour de manière sélective les parties affectées de l’arborescence du document. Voici la liste complète des modifications entraînant un enrichissement incrémentiel :

+ Modification du type de compétence (le type OData de la compétence est mis à jour)
+ Mise à jour des paramètres spécifiques à une compétence, par exemple l’URL, les paramètres par défaut ou tout autre paramètre
+ Changement de sortie d’une compétence, la compétence retourne des sorties supplémentaires ou distinctes
+ Changement d’entrée d’une compétence entraînant un changement d’ancêtres, par exemple un changement du chaînage des compétences
+ Toute invalidation de compétence amont, si une compétence qui fournit une entrée à cette compétence est mise à jour
+ Mises à jour de l’emplacement de la projection de la base de connaissances qui entraînent une reprojection des documents
+ Apport de changements aux projections de la base de connaissances, qui entraînent une reprojection des documents
+ Changements des mappages de champs de sortie d’un indexeur, qui entraînent une reprojection des documents dans l’index

## <a name="apis-used-for-caching"></a>API utilisées pour la mise en cache

L’API REST version `2020-06-30-Preview` ou version ultérieures assure l’enrichissement incrémentiel par le biais de propriétés supplémentaires sur les indexeurs. Des ensembles de compétences et des sources de données peuvent utiliser la version généralement disponible. En plus de la documentation de référence, consultez [Configurer la mise en cache pour l’enrichissement incrémentiel](search-howto-incremental-index.md) pour plus d’informations sur l’ordre des opérations.

+ [Créer ou mettre à jour un indexeur (api-version=2020-06-30-Preview)](/rest/api/searchservice/preview-api/create-or-update-indexer) 

+ [Mettre à jour un ensemble de compétences (api-version=2020-06-30)](/rest/api/searchservice/update-skillset) (nouveau paramètre d’URI sur la requête)

+ [Réinitialiser les compétences (api-version=2020-06-30)](/rest/api/searchservice/preview-api/reset-skills)

+ [Mettre à jour la source de données](/rest/api/searchservice/update-data-source), lorsqu’elle est appelée avec une API en préversion, fournit un nouveau paramètre « ignoreResetRequirement » qui doit être défini sur true lorsque votre action de mise à jour ne doit pas invalider le cache. Utilisez « ignoreResetRequirement » avec modération, car cela peut entraîner une incohérence involontaire de vos données, qui n’est pas facilement détectée.

## <a name="next-steps"></a>Étapes suivantes

L’enrichissement incrémentiel est une fonctionnalité puissante qui étend le suivi aux ensembles de compétences et à l’enrichissement par IA. L'enrichissement incrémentiel permet de réutiliser le contenu traité existant au fur et à mesure que vous itérez au sein de la conception de compétences. Dans l’étape suivante, activez la mise en cache sur vos indexeurs.

> [!div class="nextstepaction"]
> [Activer la mise en cache pour l’enrichissement incrémentiel](search-howto-incremental-index.md)