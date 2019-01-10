---
title: Découvrez comment gérer les comptes de base de données dans Azure Cosmos DB
description: Découvrez comment gérer les comptes de base de données dans Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: c27cee4842c0e65e1737f100a215cff82a0fd439
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033092"
---
# <a name="manage-indexing-in-azure-cosmos-db"></a>Gérer l’indexation dans Azure Cosmos DB

Dans Azure Cosmos DB, vous pouvez choisir si vous souhaitez qu’un conteneur indexe automatiquement tous les éléments ou non. Par défaut, tous les éléments d’un conteneur Azure Cosmos sont indexés automatiquement, mais vous pouvez désactiver l’indexation automatique. Quand l’indexation est désactivée, les éléments sont accessibles par le biais de leurs liens réflexifs, ou de requêtes en utilisant l’ID de l’élément, tel que l’ID de document. Vous pouvez demander explicitement de traiter les résultats sans utiliser l’index, en passant l’en-tête **x-ms-documentdb-enable-scan**  dans l’API REST, ou l’option de requête **EnableScanInQuery** avec le kit SDK .NET.

Si l’indexation automatique est désactivée, vous pouvez continuer à ajouter ponctuellement des éléments spécifiques à l’index. À l’inverse, vous pouvez laisser l’indexation automatique activée et choisir d’exclure de façon sélective des éléments spécifiques. Les configurations d’indexation activée/désactivée sont utiles lorsque vous avez un sous-ensemble d’éléments à interroger.  

Le débit d’écriture et les unités de requête sont proportionnels au nombre de valeurs devant être indexées, ce qui est spécifié par l’ensemble inclus dans la stratégie d’indexation. Si vous avez une bonne connaissance des modèles de requête, vous pouvez choisir explicitement le sous-ensemble d’inclusion/exclusion des chemins afin d’améliorer le débit d’écriture.

## <a name="manage-indexing-using-azure-portal"></a>Gérer l’indexation avec le portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

2. Créez un compte Azure Cosmos ou sélectionnez un compte existant.

3. Ouvrez le volet **Explorateur de données**.

4. Sélectionnez un conteneur existant, développez-le et modifiez les valeurs suivantes :

   * Ouvrez la fenêtre **Mise à l’échelle et paramètres**.
   * Passez **indexingMode** de *cohérent* à *aucun* ou incluez/excluez de l’indexation certains chemins.
   * Cliquez sur **OK** pour enregistrer les modifications.

   ![Gérer l’indexation avec le portail Azure](./media/how-to-manage-indexing/how-to-manage-indexing-portal.png)

## <a name="manage-indexing-using-azure-sdks"></a>Gérer l’indexation avec les kits SDK Azure

### <a id="dotnet"></a>Kit SDK .NET

L’exemple suivant montre comment explicitement inclure un élément à l’aide du [kit SDK .NET de l’API SQL](sql-api-sdk-dotnet.md) et de la propriété [RequestOptions.IndexingDirective](/dotnet/api/microsoft.azure.documents.client.requestoptions.indexingdirective).

```csharp
// To override the default behavior to exclude (or include) a document in indexing,
// use the RequestOptions.IndexingDirective property.
client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new { id = "myDocumentId", isRegistered = true },
    new RequestOptions { IndexingDirective = IndexingDirective.Include });
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’indexation, consultez les articles suivants :

* [Vue d’ensemble de l’indexation](index-overview.md)
* [Stratégie d’indexation](index-policy.md)
* [Types d’index](index-types.md)
* [Chemins de l’index](index-paths.md)
