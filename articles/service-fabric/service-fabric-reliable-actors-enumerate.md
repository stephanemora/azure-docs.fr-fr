---
title: Énumérer des acteurs sur Azure Service Fabric
description: En savoir plus sur l’énumération des Reliable Actors et leurs métadonnées dans une application Azure Service Fabric à l’aide d’exemples.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: dd3a61db32fb8e442beb42bd45c88da8559a29dd
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89016646"
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Avantages des Reliable Actors dans Service Fabric
Le service Reliable Actors permet à un client d’énumérer des métadonnées concernant les acteurs hébergés par le service. Le service d’acteur est un service avec état partitionné. L’énumération est donc effectuée par partition. Étant donné que chaque partition peut contenir de nombreux acteurs, l’énumération est renvoyée sous la forme d’un ensemble de résultats paginés. Les pages sont traitées en boucle jusqu’à ce qu’elles aient toutes été lues. L’exemple suivant montre comment créer une liste de tous les acteurs actifs dans une partition d’un service d’acteur :

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```



## <a name="next-steps"></a>Étapes suivantes
* [Gestion des états d’acteur](service-fabric-reliable-actors-state-management.md)
* [Cycle de vie des acteurs et Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
* [Documentation de référence de l’API Actors](/previous-versions/azure/dn971626(v=azure.100))
* [Exemple de code .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exemple de code Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
