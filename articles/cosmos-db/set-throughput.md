---
title: Approvisionner le débit pour Azure Cosmos DB | Microsoft Docs
description: Découvrez comment définir un débit approvisionné pour vos conteneurs, collections, graphes et tables Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: sngun
ms.openlocfilehash: 99cd7fe6f9f46ff4d6dbbf6a6e024b3b32679724
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444259"
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers-and-database"></a>Définir et obtenir le débit des conteneurs Azure Cosmos DB et de base de données

Vous pouvez définir le débit d’un conteneur Azure Cosmos DB ou d’un ensemble de conteneurs à l’aide du portail Azure ou des SDK clients. 

**Approvisionner le débit pour un conteneur individuel :** lorsque vous approvisionnez le débit pour un ensemble de conteneurs, tous ces conteneurs partagent le débit approvisionné. L’approvisionnement de débit pour des conteneurs individuels garantit la réservation du débit pour ce conteneur spécifique. Quand vous affectez le nombre de RU/s au niveau du conteneur, les conteneurs peuvent être créés comme ayant une taille *fixe* ou *illimitée*. Les conteneurs de taille fixe ont une limite maximale de 10 Go et de 10 000 RU/s de débit. Pour créer un conteneur illimité, vous devez spécifier un débit minimal de 1 000 RU/s et une [clé de partition](partition-data.md). Dans la mesure où vos données doivent parfois être réparties sur plusieurs partitions, vous devez choisir une clé de partition ayant une cardinalité élevée (de plusieurs centaines à plusieurs millions de valeurs distinctes). Le fait de sélectionner une clé de partition avec de nombreuses valeurs distinctes permet à Azure Cosmos DB de mettre à l’échelle votre conteneur/table/graphe et vos requêtes de façon uniforme. 

**Approvisionner le débit pour un ensemble de conteneurs ou une base de données :** l’approvisionnement de débit pour une base de données permet de partager le débit entre tous les conteneurs appartenant à cette base de données. Dans une base de données Azure Cosmos DB, vous pouvez avoir un ensemble de conteneurs qui partagent le débit, ainsi que des conteneurs avec un débit dédié. Quand vous affectez le nombre de RU/s sur un ensemble de conteneurs, les conteneurs appartenant à cet ensemble sont traités comme des conteneurs *illimités* et vous devez spécifier une clé de partition.

Azure Cosmos DB allouera des partitions physiques pour héberger vos conteneurs en fonction du débit provisionné et répartira/rééquilibrera les données sur les partitions au fur et à mesure de leur croissance. L’approvisionnement de débit au niveau du conteneur et de la base de données constitue des offres distinctes, et tout changement de l’un vers l’autre nécessite la migration de données de la source vers la destination. Cela signifie donc que vous devez créer une base de données ou une collection, puis migrer les données avec la [bibliothèque de l’exécuteur en bloc](bulk-executor-overview.md) ou [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). L’image suivante illustre l’approvisionnement de débit à différents niveaux :

![Provisionnement d’unités de requête pour des conteneurs individuels et des ensembles de conteneurs](./media/request-units/provisioning_set_containers.png)

Dans les prochaines sections, vous apprendrez à configurer du débit à différents niveaux d’un compte Azure Cosmos DB. 

## <a name="provision-throughput-by-using-azure-portal"></a>Approvisionner le débit à l’aide du portail Azure

### <a name="provision-throughput-for-a-container-collectiongraphtable"></a>Approvisionner le débit pour un conteneur (collection/graphique/table)

1. Connectez-vous au [Portail Azure](https://portal.azure.com).  
2. Dans le volet de navigation de gauche, sélectionnez **Toutes les ressources** et recherchez votre compte Azure Cosmos DB.  
3. Vous pouvez configurer le débit lors de la création d’un conteneur (collection, graphique, table) ou mettre à jour le débit d’un conteneur existant.  
4. Pour affecter le débit lors de la création d’un conteneur, ouvrez le panneau **Explorateur de données** et sélectionnez **Nouvelle collection** (nouveau graphique, nouvelle table pour d’autres API)  
5. Remplissez le formulaire dans le panneau **Ajouter une collection**. Les champs de ce panneau sont décrits dans le tableau suivant :  

   |**Paramètre**  |**Description**  |
   |---------|---------|
   |ID de base de données  |  Fournissez un nom unique pour identifier votre base de données. Une base de données est un conteneur logique d'une ou plusieurs collections. Les noms de base de données doivent comporter entre 1 et 255 caractères, et ne peuvent pas contenir les caractères /, \\, # ou ?, ni d’espace de fin. |
   |ID de la collection  | Fournissez un nom unique pour identifier votre collection. Les ID de collection sont soumis aux mêmes spécifications de caractères que les noms de base de données. |
   |Capacité de stockage   | Cette valeur représente la capacité de stockage de la base de données. Lors de l’approvisionnement du débit d’une collection individuelle, la capacité de stockage peut être **fixe (10 Go)** ou **illimitée**. La capacité de stockage illimitée vous oblige à définir une clé de partition pour vos données.  |
   |Débit   | Chaque collection et chaque base de données peuvent avoir un débit en unités de requête par seconde.  Pour la capacité de stockage fixe, le débit minimal est de 400 unités de requête par seconde (RU/s), pour la capacité de stockage illimitée, le débit minimum est défini à 1000 RU/s.|

6. Après avoir entré des valeurs pour ces champs, sélectionnez **OK** pour enregistrer les paramètres.  

   ![Définir le débit pour une collection](./media/set-throughput/set-throughput-for-container.png)

7. Pour mettre à jour le débit d’un conteneur existant, développez votre base de données et le conteneur, puis cliquez sur **Paramètres**. Dans la nouvelle fenêtre, entrez la nouvelle valeur de débit, puis sélectionnez **Enregistrer**.  

   ![Mettre à jour le débit d’une collection](./media/set-throughput/update-throughput-for-container.png)

### <a name="provision-throughput-for-a-set-of-containers-or-at-the-database-level"></a>Provisionner le débit pour un ensemble de conteneurs ou au niveau de la base de données

1. Connectez-vous au [Portail Azure](https://portal.azure.com).  
2. Dans le volet de navigation de gauche, sélectionnez **Toutes les ressources** et recherchez votre compte Azure Cosmos DB.  
3. Vous pouvez configurer le débit lors de la création d’une base de données ou mettre à jour le débit d’une base de données existante.  
4. Pour affecter un débit lors de la création d’une base de données, ouvrez le panneau **Explorateur de données** et sélectionnez **Nouvelle base de données**  
5. Spécifiez la valeur **ID de base de données**, cochez la case **Provisionner le débit**, puis configurez la valeur de débit. Une base de données peut être provisionnée avec une valeur de débit minimum de 50 000 RU/s.  

   ![Définir le débit avec l’option Nouvelle base de données](./media/set-throughput/set-throughput-with-new-database-option.png)

6. Pour mettre à jour le débit d’une base de données existante, développez votre base de données et le conteneur, puis cliquez sur **Mettre à l'échelle**. Dans la nouvelle fenêtre, entrez la nouvelle valeur de débit, puis sélectionnez **Enregistrer**.  

   ![Mettre à jour le débit d’une base de données](./media/set-throughput/update-throughput-for-database.png)

### <a name="provision-throughput-for-a-set-of-containers-as-well-as-for-an-individual-container-in-a-database"></a>Provisionner le débit d’un ensemble de conteneurs ainsi que d’un conteneur individuel dans une base de données

1. Connectez-vous au [Portail Azure](https://portal.azure.com).  
2. Dans le volet de navigation de gauche, sélectionnez **Toutes les ressources** et recherchez votre compte Azure Cosmos DB.  
3. Créez une base de données et affectez-lui un débit. Ouvrez le panneau **Explorateur de données** et sélectionnez **Nouvelle base de données**  
4. Spécifiez la valeur **ID de base de données**, cochez la case **Provisionner le débit**, puis configurez la valeur de débit. Une base de données peut être provisionnée avec une valeur de débit minimum de 50 000 RU/s.  

   ![Définir le débit avec l’option Nouvelle base de données](./media/set-throughput/set-throughput-with-new-database-option.png)

5. Créez ensuite une collection dans la base de données que vous avez créée à l’étape ci-dessus. Pour créer une collection, cliquez avec le bouton droit sur la base de données puis sélectionnez **Nouvelle collection**.  

6. Dans le panneau **Ajouter une collection**, entrez un nom pour la collection et une clé de partition. Vous pouvez également configurer le débit pour ce conteneur spécifique. Si vous choisissez de ne pas affecter une valeur de débit, le débit affecté à la base de données est partagé avec la collection.  

   ![Définir éventuellement le débit du conteneur](./media/set-throughput/optionally-set-throughput-for-the-container.png)

## <a name="considerations-when-provisioning-throughput"></a>Considérations relatives au provisionnement du débit

Voici quelques éléments qui vous aideront à choisir une stratégie de réservation de débit.

### <a name="considerations-when-provisioning-throughput-at-the-database-level"></a>Considérations relatives au provisionnement du débit au niveau de la base de données

Privilégiez un approvisionnement du débit au niveau de la base de données (pour un ensemble de conteneurs) dans les cas suivants :

* Vous disposez d’une dizaine de conteneurs ou plus et tout ou partie de ces conteneurs peuvent se partager le débit.  

* Vous effectuez une migration à partir d’une base de données à locataire unique conçue pour s’exécuter sur des machines virtuelles hébergées sur IaaS ou localement (par exemple des bases de données NoSQL ou relationnelles) vers Azure Cosmos DB et vous utilisez de nombreux conteneurs.  

* Vous souhaitez prendre en compte les pics non planifiés dans les charges de travail en regroupant le débit au niveau de la base de données.  

* Au lieu de définir le débit dans un conteneur individuel, vous préférez répartir le débit d’agrégat sur un ensemble de conteneurs au sein de la base de données.

### <a name="considerations-when-provisioning-throughput-at-the-container-level"></a>Considérations relatives au provisionnement du débit au niveau du conteneur

Approvisionnez le débit sur un conteneur individuel dans les cas suivants :

* Vous avez moins de conteneurs Azure Cosmos DB.  

* Vous souhaitez obtenir le débit garanti sur un conteneur donné soutenu par un contrat SLA.

## <a name="throughput-ranges"></a>Plages de débit

Le tableau suivant répertorie les débits disponibles pour les conteneurs :

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Conteneur à partition unique</strong></p></td>
            <td valign="top"><p><strong>Conteneur partitionné</strong></p></td>
            <td valign="top"><p><strong>Ensemble de conteneurs</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Débit minimal</p></td>
            <td valign="top"><p>400 unités de demande par seconde</p></td>
            <td valign="top"><p>1 000 unités de demande par seconde</p></td>
            <td valign="top"><p>50 000 unités de demande par seconde</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Débit maximal</p></td>
            <td valign="top"><p>10 000 unités de demande par seconde</p></td>
            <td valign="top"><p>Illimité</p></td>
            <td valign="top"><p>Illimité</p></td>
        </tr>
    </tbody>
</table>

<a id="set-throughput-sdk"></a>

## <a name="set-throughput-by-using-sql-api-for-net"></a>Définir le débit à l’aide de l’API SQL pour .NET

### <a name="set-throughput-at-the-container-level"></a>Définir le débit au niveau du conteneur
L’extrait de code suivant permet de créer un conteneur avec 3 000 unités de requête par seconde à l’aide du SDK .NET de l’API SQL :

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

### <a name="set-throughput-at-the-for-a-set-of-containers-or-at-the-database-level"></a>Définir le débit pour un ensemble de conteneurs ou au niveau de la base de données

L’extrait de code suivant permet de provisionner 100 000 unités de requête par seconde sur un ensemble de conteneurs à l’aide du SDK .NET de l’API SQL :

```csharp
// Provision 100,000 RU/sec at the database level. 
// sharedCollection1 and sharedCollection2 will share the 100,000 RU/sec from the parent database
// dedicatedCollection will have its own dedicated 4,000 RU/sec, independant of the 100,000 RU/sec provisioned from the parent database
Database database = client.CreateDatabaseAsync(new Database { Id = "myDb" }, new RequestOptions { OfferThroughput = 100000 }).Result;

DocumentCollection sharedCollection1 = new DocumentCollection();
sharedCollection1.Id = "sharedCollection1";
sharedCollection1.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection1, new RequestOptions())

DocumentCollection sharedCollection2 = new DocumentCollection();
sharedCollection2.Id = "sharedCollection2";
sharedCollection2.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection2, new RequestOptions())

DocumentCollection dedicatedCollection = new DocumentCollection();
dedicatedCollection.Id = "dedicatedCollection";
dedicatedCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, dedicatedCollection, new RequestOptions { OfferThroughput = 4000 )
```

Azure Cosmos DB fonctionne sur un modèle de réservation de débit. Autrement dit, vous êtes facturé pour la quantité de débit *réservée*, quelle que soit la quantité activement *utilisée*. À mesure que les modèles d’utilisation, de données et de charge de votre application évoluent, vous pouvez facilement augmenter ou réduire le nombre de RU réservées par le biais des SDK ou à l’aide du [portail Azure](https://portal.azure.com).

Chaque conteneur, ou ensemble de conteneurs, est mappé à une ressource `Offer` dans Azure Cosmos DB, qui contient des métadonnées sur le débit provisionné. Vous pouvez modifier le débit alloué pour un conteneur en recherchant la ressource de l’offre correspondante, et en mettant à jour la valeur de débit. L’extrait de code suivant permet de changer le débit d’un conteneur pour passer à 5 000 unités de requête par seconde à l’aide du Kit SDK .NET. Après avoir modifié le débit, vous devez actualiser les fenêtres de portail Azure existants de manière à afficher la modification. 

```csharp
// Fetch the resource to be updated
// For a updating throughput for a set of containers, replace the collection's self link with the database's self link
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

La modification du débit n’a aucun impact sur la disponibilité de votre conteneur ou ensemble de conteneurs. Le nouveau débit réservé est généralement effectif quelques secondes après son application.

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Pour définir le débit à l’aide de l’API SQL pour Java

L’extrait de code suivant récupère le débit actuel et le modifie en 500 UR/s. Pour un exemple de code complet, voir le fichier [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) sur GitHub. 

```Java
// find offer associated with this collection
// To change the throughput for a set of containers, use the database's resource id instead of the collection's resource id
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 500;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a id="GetLastRequestStatistics"></a>Obtenir le débit en utilisant la commande GetLastRequestStatistics des API MongoDB

L’API MongoDB prend en charge une commande personnalisée, *getLastRequestStatistics*, pour récupérer les frais de requête d’une opération donnée.

Par exemple, dans l’interpréteur de commandes Mongo, exécutez l’opération dont vous souhaitez vérifier les frais de demande.
```
> db.sample.find()
```

Ensuite, exécutez la commande *getLastRequestStatistics*.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

Une méthode permettant d’estimer la quantité de débit réservé requis par votre application consiste à enregistrer les frais d’unité de requête associés à l’exécution des opérations courantes sur un élément représentatif utilisé par votre application, puis à évaluer le nombre d’opérations que vous prévoyez d’effectuer chaque seconde.

> [!NOTE]
> Si vous avez des types d’éléments qui varient considérablement en termes de taille et de nombre de propriétés indexées, enregistrez les frais d’unités de requête d’opérations applicables associés à chaque *type* d’élément standard.
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Obtenir le débit à l’aide de mesures du portail de l’API MongoDB

La méthode la plus simple pour obtenir une estimation correcte des frais d’unité de requête pour votre base de données de l’API MongoDB consiste à utiliser les mesures du [Portail Azure](https://portal.azure.com). Grâce aux graphiques *Nombre de requêtes* et *Frais de requête*, vous pouvez obtenir une estimation du nombre d’unités de requête consommées par chaque opération et par les opérations les unes par rapport aux autres.

![Mesures du portail de l’API MongoDB][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> Dépassement des limites de débit réservé dans l’API MongoDB
Les applications qui dépassent le débit provisionné pour un conteneur ou un ensemble de conteneurs sont limitées jusqu’à ce que le taux de consommation tombe au-dessous du taux de débit provisionné. En cas de limitation, le serveur principal interrompra la requête avec un code d’erreur `16500`-`Too Many Requests`. Par défaut, l’API MongoDB réessaie automatiquement jusqu’à 10 fois avant de renvoyer un code d’erreur `Too Many Requests`. Si vous recevez de nombreux codes d’erreur`Too Many Requests`, vous pouvez considérer l’ajout d’une logique de nouvelle tentative dans vos routines de gestion des erreurs ou [augmenter le débit approvisionné pour le conteneur](set-throughput.md).

## <a name="throughput-faq"></a>Forum Aux Questions sur le débit

**Puis-je définir mon débit sur une valeur inférieure à 400 unités de demande/s ?**

Cette valeur de 400 unités de requête/s correspond au débit minimal disponible sur les conteneurs à partition unique Cosmos DB (une valeur de 1000 unités de requête/s correspond à la valeur minimale pour les conteneurs partitionnés). Les unités de demande sont définies par intervalles de 100 unités de demande/s, mais le débit ne peut pas avoir la valeur 100 unités de demande/s ou toute valeur inférieure à 400 unités de demande/s. Si vous recherchez une méthode économique pour développer et tester Cosmos DB, vous pouvez utiliser gratuitement l’[Émulateur Azure Cosmos DB](local-emulator.md), que vous pouvez déployer localement sans frais. 

**Comment définir le débit à l’aide de l’API MongoDB ?**

Il n’existe aucune extension d’API MongoDB pour définir le débit. Nous vous recommandons d’utiliser l’API SQL, comme indiqué dans [Pour définir le débit à l’aide de l’API SQL pour .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur l’estimation du débit et les unités de requête, consultez [Unités de requête et estimation du débit dans Azure Cosmos DB](request-units.md)

* Pour plus d’informations sur l’approvisionnement et la mise à l’échelle avec Cosmos DB, consultez [Partitioning and scaling with Cosmos DB (Partitionnement et mise à l’échelle avec Cosmos DB)](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png
