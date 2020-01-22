---
title: Regénérer un index de recherche
titleSuffix: Azure Cognitive Search
description: Ajoutez de nouveaux éléments, mettez à jour des documents ou des éléments existants ou supprimez des documents obsolètes via une régénération complète ou une indexation partielle visant à actualiser un index Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 18cfa3c6fde399ea61e09c5788c72ce20e5570e8
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754393"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Guide pratique pour regénérer un index dans la Recherche cognitive Azure

Cet article explique comment regénerer un index Recherche cognitive Azure et les circonstances dans lesquelles les regénérations sont nécessaires, et il contient des suggestions pour atténuer l’impact des regénérations sur les demandes des requêtes en cours.

Une *regénération* fait référence à la suppression et à la recréation des structures de données physiques associées à un index, notamment tous les index inversés basés sur un champ. Dans Recherche cognitive Azure, vous ne pouvez pas supprimer et recréer des champs un par un. Pour regénerer un index, la totalité du stockage des champs doit être supprimé, recréé sur la base d’un schéma d’index existant ou révisé, puis à nouveau rempli avec les données envoyées à l’index ou extraites de sources externes. Il est courant de regénérer les index pendant le développement, mais il peut également être nécessaire de les regénérer au niveau de la production pour prendre en compte des modifications structurelles, comme l’ajout de types complexes ou l’ajout de champs à des suggesteurs.

Contrairement aux regénérations, qui placent un index hors connexion, *l’actualisation des données* s’exécute comme tâche d’arrière-plan. Vous pouvez ajouter, supprimer et remplacer des documents avec une interruption minimale pour les charges de travail de requêtes, bien que l’exécution des requêtes soit alors généralement plus longue. Pour plus d’informations sur la mise à jour du contenu des index, consultez [Ajouter, mettre à jour ou supprimer des documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="rebuild-conditions"></a>Conditions de la recréation

| Condition | Description |
|-----------|-------------|
| Modifier une définition de champ | La révision d’un nom de champ, d’un type de données ou [d’attributs d’index](https://docs.microsoft.com/rest/api/searchservice/create-index) spécifiques (interrogeable, filtrable, triable, à choix multiples) exige une régénération complète. |
| Affecter un analyseur à un champ | Les [analyseurs](search-analyzers.md) sont définis dans un index, puis affectés à des champs. Vous pouvez à tout moment ajouter une nouvelle définition d’analyseur à un index, mais il n’est possible *d’affecter* l’analyseur qu’à la création du champ. Cette condition s’applique à la fois à la propriété **analyzer** et à la propriété **indexAnalyzer**. La propriété **searchAnalyzer** fait figure d’exception (elle peut être affectée à un champ existant). |
| Mettre à jour ou supprimer une définition d’analyseur dans un index | Il n’est pas possible de supprimer ou de modifier une configuration d’analyseur existante (analyseur, générateur de jetons, filtre de jetons ou filtre de caractères) dans l’index, à moins de regénérer la totalité de l’index. |
| Ajouter un champ à un suggesteur | Pour pouvoir ajouter un champ existant à une construction [Suggesteurs](index-add-suggesters.md), il faut regénérer l’index. |
| Supprimer un champ | Pour supprimer physiquement toutes les traces d’un champ, vous devez regénerer l’index. Quand une régénération immédiate n’est pas réalisable, il est possible de modifier le code de l’application en désactivant l’accès au champ « supprimé ». Physiquement, le contenu et la définition du champ restent dans l’index jusqu’à la régénération suivante, lors de laquelle est appliqué un schéma omettant le champ en question. |
| Changer de niveau | Si vous avez besoin de davantage de capacité, il n’y a pas de mise à niveau sur place dans le portail Azure. Vous devez créer un service et regénérer entièrement les index sur le nouveau service. Pour faciliter l’automatisation de ce processus, vous pouvez utiliser l’exemple de code **index-backup-restore** dans cet [exemple de dépôt .NET Recherche cognitive Azure](https://github.com/Azure-Samples/azure-search-dotnet-samples). Cette application va sauvegarder votre index dans une série de fichiers JSON, puis recréer l’index dans un service de recherche que vous spécifiez.|

Toutes les autres modifications peuvent être effectuées sans impact sur les structures physiques existantes. Plus précisément, les modifications suivantes n’exigent *pas* de régénération d’index :

+ Ajouter un nouveau champ
+ Définir l’attribut **retrievable** sur un champ existant
+ Définir un **searchAnalyzer** sur un champ existant
+ Ajouter une nouvelle définition d’analyseur dans un index
+ Ajouter, mettre à jour ou supprimer des profils de scoring
+ Ajouter, mettre à jour ou supprimer des paramètres CORS
+ Ajouter, mettre à jour ou supprimer des synonymMaps

Quand vous ajoutez un nouveau champ, les documents indexés existants reçoivent une valeur null pour le nouveau champ. Lors de l’actualisation suivante des données, les valeurs provenant des données sources externes remplacent les valeurs null ajoutées par Recherche cognitive Azure. Pour plus d’informations sur la mise à jour du contenu des index, consultez [Ajouter, mettre à jour ou supprimer des documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="partial-indexing"></a>Indexation partielle

Dans Recherche cognitive Azure, vous ne pouvez pas contrôler l’indexation au niveau d’un champ, en choisissant de supprimer ou de recréer des champs spécifiques. De même, il n’existe pas de mécanisme intégré pour [l’indexation de documents en fonction de critères](https://stackoverflow.com/questions/40539019/azure-search-what-is-the-best-way-to-update-a-batch-of-documents). Toutes vos exigences quant à une indexation pilotée par des critères doivent être satisfaites via du code personnalisé.

Une chose facile à faire est cependant d’*actualiser des documents* dans un index. Pour de nombreuses solutions de recherche, les données sources externes sont volatiles, et la synchronisation entre les données sources et un index de recherche est une pratique courante. Dans le code, appelez l’opération [Ajouter, mettre à jour ou supprimer des documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) ou l’[équivalent .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexesoperationsextensions.createorupdate?view=azure-dotnet) pour mettre à jour le contenu de l’index, ou pour ajouter des valeurs pour un nouveau champ.

## <a name="partial-indexing-with-indexers"></a>Indexation partielle avec des indexeurs

Les [indexeurs](search-indexer-overview.md) simplifient la tâche d’actualisation des données. Un indexeur peut indexer seulement une table ou une vue dans la source de données externe. Pour indexer plusieurs tables, l’approche la plus simple est de créer une vue qui joint les tables et projette les colonnes que vous voulez indexer. 

Quand vous utilisez des indexeurs qui analysent des sources de données externes, recherchez une colonne de « limite supérieure » dans la source de données. S’il en existe une, vous pouvez l’utiliser pour la détection des modifications incrémentielles en sélectionnant seulement les lignes avec du contenu nouveau ou modifié. Pour le [stockage Blob Azure](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection), un champ `lastModified` est utilisé. Pour le [stockage Table Azure](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection), `timestamp` remplit le même rôle. De même, [l’indexeur Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) et [l’indexeur Azure Cosmos DB](search-howto-index-cosmosdb.md#indexing-changed-documents) contiennent tous deux des champs pour identifier les mises à jour de ligne. 

Pour plus d’informations sur les indexeurs, consultez [Vue d’ensemble des indexeurs](search-indexer-overview.md) et [Reset Indexer REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="how-to-rebuild-an-index"></a>Comment regénérer un index

Planifiez des régénérations fréquentes et complètes pendant la phase de développement actif, lorsque les schémas d’index sont à l’état de flux. Pour les applications déjà en production, nous recommandons de créer un nouvel index qui s’exécute côte à côte avec un index existant pour éviter des temps d’arrêt dans les requêtes.

Les mises à jour d’index requièrent des autorisations en lecture/écriture au niveau du service. 

Il n’est pas possible de regénérer un index sur le portail. Vous pouvez appeler programmatiquement [l’API REST Mettre à jour l’index](https://docs.microsoft.com/rest/api/searchservice/update-index) ou des [API .NET équivalentes](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexesoperations.createorupdatewithhttpmessagesasync?view=azure-dotnet) pour une régénération complète. Une demande de mise à jour d’index est identique à [l’API REST Créer un index](https://docs.microsoft.com/rest/api/searchservice/create-index), mais avec un contexte différent.

Le workflow suivant est orienté vers l’API REST, mais il s’applique également au kit SDK .NET.

1. En cas de réutilisation du nom d’un index, [supprimez l’index existant](https://docs.microsoft.com/rest/api/searchservice/delete-index). 

   Toutes les requêtes ciblant cet index sont immédiatement supprimées. La suppression d’un index est irréversible, détruisant le stockage physique pour la collection de champs et d’autres constructions. Veillez à bien comprendre les implications de la suppression d’un index avant de le supprimer. 

2. Formulez une demande [Mettre à jour l’index](https://docs.microsoft.com/rest/api/searchservice/update-index) avec votre point de terminaison de service, votre clé API et une [clé d’administration](https://docs.microsoft.com/azure/search/search-security-api-keys). La clé d’administration est requise pour les opérations d’écriture.

3. Dans le corps de la demande, indiquez un schéma d’index avec les définitions des champs modifiés. Le corps de la demande contient le schéma d’index, ainsi que des constructions pour les profils de scoring, les analyseurs, les générateurs de suggestions et les options CORS. Les spécifications des schémas sont documentées dans [Créer un index](https://docs.microsoft.com/rest/api/searchservice/create-index).

4. Envoyez une requête [Mettre à jour l’index](https://docs.microsoft.com/rest/api/searchservice/update-index) pour regénérer l’expression physique de l’index sur Recherche cognitive Azure. 

5. [Chargez l’index avec des documents ](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) d’une source externe.

Quand vous créez l’index, du stockage physique est alloué pour chaque champ dans le schéma d’index, avec un index inversé créé pour chaque champ avec possibilité de recherche. Des champs sans possibilité de recherche peuvent être utilisés dans des filtres ou des expressions, mais ils n’ont pas d’index inversé et n’autorisent pas la recherche approximative ou en texte intégral. Lors d’une regénération d’index, ces index inversés sont supprimés et recréés sur la base du schéma d’index que vous fournissez.

Quand vous chargez l’index, l’index inversé de chaque champ est rempli avec tous les mots uniques tokenisés de chaque document, avec un mappage aux ID des documents correspondants. Par exemple, lors de l’indexation d’un jeu de données avec des hôtels, un index inversé créé pour un champ Ville peut contenir des termes pour Seattle, Portland, etc. L’ID des documents qui incluent « Seattle » ou « Portland » dans le champ Ville figure à côté du terme. Lors d’une opération [Ajouter, mettre à jour ou supprimer](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents), les termes et la liste des ID de document sont mis à jour en conséquence.

> [!NOTE]
> Si vous avez des exigences strictes dans le cadre d’un contrat SLA, vous pouvez envisager de provisionner un nouveau service spécifiquement pour ce travail, le développement et l’indexation se produisant dans une isolation complète d’un index de production. Un service distinct s’exécute sur son propre matériel, éliminant toute possibilité de contention des ressources. Une fois le développement terminé, vous laissez le nouvel index en place et vous redirigez les requêtes vers le nouveau point de terminaison et le nouvel index, ou bien vous exécutez le code terminé pour publier un index revu sur votre service Recherche cognitive Azure d’origine. Il n’existe actuellement aucun mécanisme pour déplacer un index prêt à l’emploi vers un autre service.

## <a name="view-updates"></a>Voir les mises à jour

Vous pouvez commencer à interroger un index dès que le premier document est chargé. Si vous connaissez l’ID d’un document, l’[API REST de recherche de document](https://docs.microsoft.com/rest/api/searchservice/lookup-document) retourne le document spécifique. Pour un test plus large, attendez que l’index soit entièrement chargé, puis utilisez des requêtes pour vérifier le contexte que vous vous attendez à voir.

## <a name="see-also"></a>Voir aussi

+ [Présentation de l’indexeur](search-indexer-overview.md)
+ [Indexer de grands jeux de données à grand échelle](search-howto-large-index.md)
+ [Indexation dans le portail](search-import-data-portal.md)
+ [Indexeur Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexeur Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indexeur Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indexeur Stockage Table Azure](search-howto-indexing-azure-tables.md)
+ [Sécurité dans Recherche cognitive Azure](search-security-overview.md)