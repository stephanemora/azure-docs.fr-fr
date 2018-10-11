---
title: Résolution des conflits multimaîtres dans Azure Cosmos DB
description: Cet article décrit les catégories de conflits et les modes de résolution des conflits comme Dernière écriture prioritaire (LWW), Procédure définie par l’utilisateur/personnalisée, Asynchrone/personnalisé dans le multimaître Azure Cosmos DB.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 39fd393e78a2b66749c6aa34a758b185b38effdf
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041185"
---
# <a name="multi-master-conflict-resolution-in-azure-cosmos-db"></a>Résolution des conflits multimaîtres dans Azure Cosmos DB 

Le multimaître Azure Cosmos DB fournit des modes de résolution des conflits globaux pour garantir que les données sont cohérentes dans toutes les régions où elles sont répliquées.

## <a name="conflict-categories"></a>Catégories de conflits

Il existe trois catégories de conflits qui peuvent se produire quand vous utilisez des données Azure Cosmos DB :

* **Conflits d’insertion :** ce type de conflit se produit quand l’application insère deux documents, ou plus, avec le même index unique (par exemple, l’ID) simultanément à partir de deux régions, ou plus. Dans ce cas, toutes les écritures peuvent réussir initialement, mais en fonction de la stratégie de résolution des conflits que vous choisissez, un seul document avec l’ID d’origine est validé.

* **Conflits de substitution :** ce type de conflit se produit quand l’application met à jour un seul et même document simultanément à partir de deux régions, ou plus.

* **Conflits de suppression :** ce type de conflit se produit quand l’application supprime un document à partir d’une région et le met à jour à partir d’une ou de plusieurs régions. 

## <a name="conflict-resolution-modes"></a>Modes de résolution des conflits

Azure Cosmos DB offre trois modes de résolution des conflits. Les modes de résolution des conflits sont définis pour chaque collection.

> [!NOTE]
> Les utilisateurs d’API SQL peuvent choisir parmi trois modes de résolution des conflits différents. Pour tous les autres modèles d’API (MongoDB, Cassandra, Graph et Table), les conflits sont résolus à l’aide du mode Dernière écriture prioritaire.

### <a name="last-writer-wins-lww"></a>Dernière écriture prioritaire (LWW)

Le mode Dernière écriture prioritaire est le mode de résolution des conflits par défaut. Dans ce mode, les conflits sont résolus selon une valeur numérique passée dans une propriété sur le document.

L’extrait de code suivant est un exemple montrant comment configurer la stratégie de résolution des conflits Dernière écriture prioritaire quand vous utilisez le SDK .Net. Le paramètre « ConflictResolutionPath » définit le chemin de la propriété qui est utilisée pour résoudre le conflit. Dans cet exemple, `/userDefinedId` est le chemin de résolution du conflit, et le document ayant la valeur `userDefinedId` la plus élevée gagnera toujours le conflit. Pour inscrire un mode de résolution Dernière écriture prioritaire, provisionnez la collection avec le paramètre ConflictResolutionPolicy, comme indiqué ci-dessous.

```csharp
DocumentCollection lwwCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
   Id = "myCollection", 
   ConflictResolutionPolicy = new ConflictResolutionPolicy
   {
      Mode = ConflictResolutionMode.LastWriterWins,
      ConflictResolutionPath = "/userDefinedId"
   }
});
```

 Le mode de résolution Dernière écriture prioritaire est appliqué à différentes catégories de conflits de données, comme suit :

* **Conflits d’insertion et de mise jour :** si deux documents, ou plus, sont en conflit lors d’opérations d’insertion ou de substitution, le document qui contient la plus grande valeur pour le chemin de résolution du conflit (autrement dit, userDefinedId) l’emporte. Si plusieurs documents ont la même valeur numérique pour le chemin de résolution du conflit, le gagnant sélectionné est non déterministe. Toutefois, toutes les régions convergeront vers un gagnant unique.

* **Conflit de suppression :** si des conflits de suppression sont impliqués, la suppression gagne toujours sur d’autres conflits de substitution, quelle que soit la valeur du chemin de résolution du conflit.

### <a name="custom--user-defined-procedure"></a>Procédure définie par l’utilisateur/personnalisée

Dans ce mode, l’utilisateur contrôle la résolution des conflits en inscrivant une procédure définie par l’utilisateur auprès de la collection. Cette procédure définie par l’utilisateur comporte une signature spécifique. Si vous sélectionnez cette option, mais que vous ne parvenez pas à inscrire une procédure définie par l’utilisateur, ou si la procédure définie par l’utilisateur lève une exception au moment de l’exécution, les conflits sont écrits dans le flux de conflits où ils peuvent être résolus individuellement.

Pour inscrire un mode de résolution des conflits Procédure définie par l’utilisateur/personnalisée, provisionnez la collection avec le paramètre ConflictResolutionPolicy, comme indiqué ci-dessous.

```csharp
DocumentCollection udpCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
  Id = "myCollection",
  ConflictResolutionPolicy = new ConflictResolutionPolicy
  {
     Mode = ConflictResolutionMode.Custom,
     ConflictResolutionProcedure = UriFactory.CreateStoredProcedureUri("myDatabase","myCollection","myUdpStoredProcedure").ToString()
  }
});
```

Ensuite, ajoutez la procédure définie par l’utilisateur à la collection, comme indiqué ci-dessous.

```csharp
StoredProcedure myUdpStoredProc = new StoredProcedure
{
   Id = "myUdpStoredProcedure",
   Body = myUdpStoredProcText
};
await myClient.UpsertStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("myDatabase", "myCollection"), myUdpStoredProc);
```

La procédure stockée inscrite auprès de la collection a une signature spéciale. Dans l’exemple ci-dessous, la procédure définie par l’utilisateur utilise une propriété, `UserDefinedId`, pour résoudre les conflits. Le document ayant la plus grande valeur remporte le conflit.

```javascript
function myUdpStoredProcedure(incomingDocument, existingDocument, isDeleteConflict, conflictingDocuments){
    var collection = getContext().getCollection();

    if (!incomingDocument) {
        if (existingDocument) {

            collection.deleteDocument(existingDocument._self, {}, function(err, responseOptions) {
                if (err) throw err;
            });
        }
    } else if (isDeleteConflict) {
        // delete always wins.
    } else {
        var documentToUse = incomingDocument;

        if (existingDocument) {
            if (documentToUse.userDefinedId < existingDocument.userDefinedId) {
                documentToUse = existingDocument;
            }
        }

        var i;
        for (i = 0; i < conflictingDocuments.length; i++) {
            if (documentToUse.userDefinedId < conflictingDocuments[i].userDefinedId) {
                documentToUse = conflictingDocuments[i];
            }
        }

        tryDelete(conflictingDocuments, incomingDocument, existingDocument, documentToUse);
    }

    function tryDelete(documents, incoming, existing, documentToInsert) {
        if (documents.length > 0) {
            collection.deleteDocument(documents[0]._self, {}, function(err, responseOptions) {
                if (err) throw err;

                documents.shift();
                tryDelete(documents, incoming, existing, documentToInsert);
            });
        } else if (existing) {
            collection.replaceDocument(existing._self, documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        } else {
            collection.createDocument(collection.getSelfLink(), documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        }
    }
}

```

La procédure comprend quatre paramètres :

* **incomingDocument :** spécifie la version en conflit du document. Le conflit peut être un conflit d’insertion, de substitution ou de suppression. Pour un conflit de suppression, il s’agit d’une image de suppression (élément résiduel) sans contenu.

* **existingDocument :** spécifie la version validée du document, qui a la même valeur « rid » qu’incomingDocument. Ce paramètre est défini sur null pour un conflit d’insertion et de suppression.

* **isDeleteConflict :** indicateur booléen spécifiant si le document entrant est en conflit avec un document supprimé précédemment. Quand ce paramètre est défini sur true, existingDocument aura également la valeur null.

* **conflictingDocuments :** spécifie une collection des versions validées de tous les documents de la base de données qui sont en conflit avec incomingDocument sur la colonne d’ID ou n’importe quel autre champ d’index unique. Ces documents ont une valeur « rid » différente d’incomingDocument.

La procédure définie par l’utilisateur a un accès complet à la clé de partition Cosmos DB et peut effectuer n’importe quelle opération de stockage pour résoudre des conflits. Si la procédure définie par l’utilisateur ne valide pas la version en conflit, le système supprime le conflit et existingDocument reste validé. Si la procédure définie par l’utilisateur échoue ou n’existe pas, Azure Cosmos DB ajoute le conflit au flux de conflits en lecture seule du flux où il peut être traité de manière asynchrone, comme indiqué dans [Mode de résolution des conflits asynchrone](#custom--asynchronous). 

### <a name="custom--asynchronous"></a>Asynchrone/personnalisé  

Dans ce mode, Azure Cosmos DB exclut tous les conflits (d’insertion, de substitution et de suppression) de la validation et les inscrit dans le flux de conflits en lecture seule pour la résolution différée par l’application de l’utilisateur. L’application peut effectuer la résolution des conflits de façon asynchrone et utiliser n’importe quelle logique ou faire référence à n’importe quelle source externe, n’importe quelle application ou n’importe quel service pour résoudre le conflit.

Pour inscrire un mode de résolution des conflits asynchrone/personnalisé, provisionnez la collection avec le paramètre ConflictResolutionPolicy, comme indiqué ci-dessous.

```csharp
DocumentCollection manualCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
    Id = "myCollection",
    ConflictResolutionPolicy = new ConflictResolutionPolicy
    {
        Mode = ConflictResolutionMode.Custom
    }
});
```

Pour lire et traiter tout conflit du flux de conflits, implémentez le code indiqué ci-dessous. Les données stockées dans le flux de conflits entraînent des frais de stockage. Par conséquent, il est recommandé de supprimer les données stockées dans le flux de conflits une fois qu’elles sont traitées.

```csharp
FeedResponse<Conflict> response = await myClient.ReadConflictFeedAsync(myCollectionUri);
  foreach (Conflict conflict in response)
  {
      switch(conflict.OperationKind)
      {
         case OperationKind.Create:
         //Process Insert Conflicts.
         …
         case OperationKind.Replace:
         //Process Replace Conflicts
         …
         case OperationKind.Delete:
         //Process Delete Conflicts
         …
      }
  //Optionally delete the conflict after processed
  await myClient.DeleteConflictAsync(conflict.SelfLink);
  }
```

> [!NOTE]
> Le flux de conflits n’inclut pas d’écouteur pour envoyer des notifications concernant le traitement en aval comme le flux de modification dans Cosmos DB. Vous devez implémenter la logique pour interroger le flux de conflits et déterminer si des conflits sont présents.

## <a name="code-samples"></a>Exemples de code

Vous trouverez ci-après des exemples d’applications qui illustrent la résolution des conflits pour les API répertoriées. Chaque exemple génère des conflits dans un conteneur, puis montre comment les conflits sont résolus pour chaque mode de résolution des conflits pris en charge.

|Modèle d’API  | Foundation |Exemple |
|---------|---------|---------|
|API SQL    | .NET    |[azure-cosmos-db-sql-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-sql-dotnet-multi-master)  |
|API SQL    | Nœud    |[azure-cosmos-js/samples/MultiRegionWrite/](https://github.com/Azure/azure-cosmos-js/tree/master/samples/MultiRegionWrite)  |
|API SQL    | Java    |[azure-cosmos-db-sql-java-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-sql-java-multi-master)  |
|MongoDB  | .NET    |[azure-cosmos-db-mongodb-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-multi-master)   |
|API de table  | .NET    |[azure-cosmos-db-table-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-multi-master)       |
|API Gremlin | .NET | [azure-cosmos-db-gremlin-dontnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-gremlin-dontnet-multi-master)|

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert les catégories de conflits et des modes de résolution des conflits pour Azure Cosmos DB. Vous pouvez maintenant examiner les ressources suivantes :

* [Utiliser des clients MongoDB avec le multimaître](multi-master-oss-nosql.md)
