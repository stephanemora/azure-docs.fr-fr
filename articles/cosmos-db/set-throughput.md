---
title: Approvisionner le débit pour Azure Cosmos DB | Microsoft Docs
description: Découvrez comment définir un débit approvisionné pour vos conteneurs, collections, graphes et tables Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: 0a53bb0a23fae386abbe71de944b073cbb93d502
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2018
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers"></a>Définir et obtenir le débit des conteneurs Azure Cosmos DB

Vous pouvez définir le débit de vos conteneurs Azure Cosmos DB dans le portail Azure ou à l’aide des SDK clients. 

Le tableau suivant répertorie les débits disponibles pour les conteneurs :

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Conteneur à partition unique</strong></p></td>
            <td valign="top"><p><strong>Conteneur partitionné</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Débit minimal</p></td>
            <td valign="top"><p>400 unités de demande par seconde</p></td>
            <td valign="top"><p>1000 unités de requête par seconde</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Débit maximal</p></td>
            <td valign="top"><p>10 000 unités de demande par seconde</p></td>
            <td valign="top"><p>Illimité</p></td>
        </tr>
    </tbody>
</table>

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Pour définir le débit à l’aide du portail Azure

1. Dans une nouvelle fenêtre, ouvrez le [portail Azure](https://portal.azure.com).
2. Dans la barre de gauche, cliquez sur **Azure Cosmos DB** ou sur **Tous les services** en bas, accédez à **Bases de données**, puis sélectionnez **Azure Cosmos DB**.
3. Sélectionnez votre compte Azure Cosmos DB.
4. Dans la nouvelle fenêtre, cliquez sur **Explorateur de données** dans le menu de navigation.
5. Dans la nouvelle fenêtre, développez la base de données et le conteneur, puis cliquez sur **Scale & Settings** (Mise à l’échelle & paramètres).
6. Dans la nouvelle fenêtre, tapez la nouvelle valeur de débit dans la zone **Débit**, puis cliquez sur **Enregistrer**.

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-net"></a>Pour définir le débit à l’aide de l’API SQL pour .NET

L’extrait de code suivant récupère le débit actuel et le modifie en 500 UR/s. Pour l’exemple de code complet, voir le projet [CollectionManagement](https://github.com/Azure/azure-documentdb-dotnet/blob/95521ff51ade486bb899d6913880995beaff58ce/samples/code-samples/CollectionManagement/Program.cs#L188-L216) sur GitHub.

```csharp
// Fetch the offer of the collection whose throughput needs to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 500 request units per second
offer = new OfferV2(offer, 500);

// Now persist these changes to the collection by replacing the original offer resource
await client.ReplaceOfferAsync(offer);
```

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Pour définir le débit à l’aide de l’API SQL pour Java

L’extrait de code suivant récupère le débit actuel et le modifie en 500 UR/s. Pour un exemple de code complet, voir le fichier [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) sur GitHub. 

```Java
// find offer associated with this collection
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

Ainsi, une méthode permettant d’estimer la quantité de débit réservé requis par votre application consiste à enregistrer les frais d’unité de requête associés à l’exécution des opérations courantes sur un élément représentatif utilisé par votre application, puis à évaluer le nombre d’opérations que vous prévoyez d’effectuer chaque seconde.

> [!NOTE]
> Si vous avez des types d’éléments qui varient considérablement en termes de taille et de nombre de propriétés indexées, enregistrez les frais d’unités de requête d’opérations applicables associés à chaque *type* d’élément standard.
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Obtenir le débit à l’aide de mesures du portail de l’API MongoDB

La méthode la plus simple pour obtenir une estimation correcte des frais d’unité de requête pour votre base de données de l’API MongoDB consiste à utiliser les mesures du [Portail Azure](https://portal.azure.com). Grâce aux graphiques *Nombre de requêtes* et *Frais de requête*, vous pouvez obtenir une estimation du nombre d’unités de requête consommées par chaque opération et par les opérations les unes par rapport aux autres.

![Mesures du portail de l’API MongoDB][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> Dépassement des limites de débit réservé dans l’API MongoDB
Les applications qui dépassent le débit approvisionné pour un conteneur seront limitées jusqu'à ce que le taux de consommation tombe au-dessous du taux de débit approvisionné. En cas de limitation, le serveur principal interrompra la requête de manière préemptive avec un code d’erreur `16500`-`Too Many Requests`. Par défaut, l’API MongoDB réessaie automatiquement jusqu’à 10 fois avant de renvoyer un code d’erreur `Too Many Requests`. Si vous recevez de nombreux codes d’erreur`Too Many Requests`, vous pouvez considérer l’ajout d’une logique de nouvelle tentative dans vos routines de gestion des erreurs ou [augmenter le débit approvisionné pour le conteneur](set-throughput.md).

## <a name="throughput-faq"></a>Forum Aux Questions sur le débit

**Puis-je définir mon débit sur une valeur inférieure à 400 unités de demande/s ?**

Cette valeur de 400 unités de requête/s correspond au débit minimal disponible sur les conteneurs à partition unique Cosmos DB (une valeur de 1000 unités de requête/s correspond à la valeur minimale pour les conteneurs partitionnés). Les unités de demande sont définies par intervalles de 100 unités de demande/s, mais le débit ne peut pas avoir la valeur 100 unités de demande/s ou toute valeur inférieure à 400 unités de demande/s. Si vous recherchez une méthode économique pour développer et tester Cosmos DB, vous pouvez utiliser gratuitement l’[Émulateur Azure Cosmos DB](local-emulator.md), que vous pouvez déployer localement sans frais. 

**Comment définir le débit à l’aide de l’API MongoDB ?**

Il n’existe aucune extension d’API MongoDB pour définir le débit. Nous vous recommandons d’utiliser l’API SQL, comme indiqué dans [Pour définir le débit à l’aide de l’API SQL pour .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’approvisionnement et la mise à l’échelle avec Cosmos DB, consultez [Partitioning and scaling with Cosmos DB (Partitionnement et mise à l’échelle avec Cosmos DB)](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png