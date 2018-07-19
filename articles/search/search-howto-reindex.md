---
title: Régénérer un index Recherche Azure ou actualiser du contenu pouvant faire l’objet d’une recherche | Microsoft Docs
description: Ajoutez de nouveaux éléments, mettez à jour des documents ou des éléments existants, ou supprimez des documents obsolètes via une régénération complète ou une indexation incrémentielle partielle visant à actualiser un index Recherche Azure.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: 374e7601169647f0eb7d3a214cf15567b7b11090
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2018
ms.locfileid: "34641422"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Comment régénérer un index Recherche Azure

Lorsque vous régénérez un index, vous modifiez sa structure et son expression physique dans votre service Recherche Azure. À l’inverse, lorsque vous actualisez un index, vous mettez uniquement à jour son contenu pour obtenir les dernières modifications à partir d’une source de données externe de contribution. Cet article explique comment mettre à jour les index de manière structurelle et substantielle.

Les mises à jour d’index requièrent des autorisations en lecture/écriture au niveau du service. Via la programmation, vous pouvez appeler des API REST ou .NET pour effectuer une régénération complète ou une indexation incrémentielle du contenu, avec des paramètres spécifiant les options de mise à jour. 

En règle générale, les mises à jour d’index se font à la demande. Toutefois, pour les index remplis à l’aide [d’indexeurs](search-indexer-overview.md) spécifiques à la source, vous pouvez utiliser un planificateur intégré. Le planificateur prend en charge l’actualisation des documents toutes les 15 minutes minimum, selon l’intervalle et le modèle dont vous avez besoin. Pour actualiser le contenu plus fréquemment, il est nécessaire d’exécuter les mises à jour d’index manuellement, par exemple via une double-écriture au niveau des transactions, afin de mettre à jour simultanément la source de données externe et l’index Recherche Azure.

## <a name="full-rebuilds"></a>Régénérations complètes

De nombreux types de mises à jour nécessitent une régénération complète. Une régénération complète fait référence à la suppression d’un index, à la fois les données et les métadonnées, suivie d’un nouveau remplissage de l’index à partir de sources de données externes. Via la programmation, vous devez [supprimer](https://docs.microsoft.com/rest/api/searchservice/delete-index), [créer](https://docs.microsoft.com/rest/api/searchservice/create-index), puis [recharger](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) l’index pour le régénérer. 

Une fois l’index régénéré, n’oubliez pas que si vous avez testé des modèles de requête et des profils de score, vous pouvez obtenir des résultats différents si le contenu sous-jacent a été modifié.

## <a name="when-to-rebuild"></a>Quand régénérer un index

Planifiez des régénérations fréquentes et complètes pendant la phase de développement actif, lorsque les schémas d’index sont à l’état de flux.

| Modification | État de la régénération|
|--------------|---------------|
| Modifier un type de données,un nom de champ ou ses [attributs d’index](https://docs.microsoft.com/rest/api/searchservice/create-index) | La modification d’une définition de champ entraîne généralement une pénalité de régénération, sauf pour les [attributs d’index](https://docs.microsoft.com/rest/api/searchservice/create-index) suivants : Retrievable, SearchAnalyzer et SynonymMaps. Vous pouvez ajouter les attributs Retrievable, SearchAnalyzer et SynonymMaps à un champ existant sans avoir à régénérer l’index.|
| Ajouter un champ | Aucune obligation de régénération. Les documents indexés existants reçoivent une valeur null pour le nouveau champ. Lors de la prochaine réindexation, les valeurs des données sources remplaceront les valeurs null ajoutées par le service Recherche Azure. |
| Supprimer un champ | Vous ne pouvez pas supprimer directement un champ d’un index Recherche Azure. Au lieu de cela, vous devez configurer votre application pour qu’elle ignore le champ « supprimé » et ne l’utilise pas. Physiquement, le contenu et la définition du champ restent dans l’index jusqu’à ce que vous régénériez votre index à l’aide d’un schéma qui omet le champ en question.|

> [!Note]
> Une régénération est également nécessaire si vous changez de niveau. Si à un moment donné vous décidez d’augmenter la capacité, il n’existe aucune possibilité de mise à niveau sur place. Vous devez créer un nouveau service sur le nouveau point de capacité et régénérer entièrement les index sur le nouveau service. 

## <a name="partial-or-incremental-indexing"></a>Indexation incrémentielle ou partielle

Une fois qu’un index est en production, orientez-vous vers une indexation incrémentielle, qui se fait généralement sans interruption de service notable. L’indexation partielle ou incrémentielle est une charge de travail portant uniquement sur le contenu qui synchronise le contenu d’un index de recherche pour refléter l’état du contenu dans une source de données de contribution. Un document ajouté ou supprimé dans la source est ajouté ou supprimé dans l’index. Dans le code, appelez l’opération [Ajouter, mettre à jour et supprimer des documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) ou l’équivalent .NET.

> [!Note]
> Lorsque vous utilisez des indexeurs qui analysent les sources de données externes, l’indexation incrémentielle fait appel à des mécanismes de suivi des modifications au niveau des systèmes sources. Pour le [stockage Blob Azure](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection), un champ `lastModified` est utilisé. Pour le [stockage Table Azure](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection), `timestamp` remplit le même rôle. De même, [l’indexeur Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) et [l’indexeur Azure Cosmos DB](search-howto-index-cosmosdb.md#indexing-changed-documents) contiennent tous deux des champs pour identifier les mises à jour de ligne. Pour en savoir plus sur les indexeurs, consultez l’article [Présentation de l’indexeur](search-indexer-overview.md).


## <a name="see-also"></a>Voir aussi

+ [Présentation de l’indexeur](search-indexer-overview.md)
+ [Indexer de grands jeux de données à grand échelle](search-howto-large-index.md)
+ [Indexation dans le portail](search-import-data-portal.md)
+ [Indexeur Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexeur Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indexeur Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indexeur Stockage Table Azure](search-howto-indexing-azure-tables.md)
+ [Sécurité dans Recherche Azure](search-security-overview.md)