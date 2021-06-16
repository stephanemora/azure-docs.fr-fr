---
title: Balises de nœud dynamiques Azure Service Fabric
description: Azure Service Fabric vous permet d’ajouter et de supprimer dynamiquement des balises de nœud.
author: yu-supersonic
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: branim
ms.custom: devx-track-csharp
ms.openlocfilehash: b20ab4720f9f172ef9248d3314b25922896eb8bd
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110796927"
---
# <a name="introduction-to-dynamic-node-tags"></a>Présentation des balises de nœud dynamiques
Les balises de nœuds vous permettent d’ajouter et de supprimer dynamiquement des balises à partir de nœuds afin d’influencer le positionnement des services. Le balisage des nœuds est très flexible et permet de modifier le placement des services sans mise à niveau de l’application ou du cluster. Les balises peuvent être ajoutées ou supprimées des nœuds à tout moment, et les services peuvent spécifier des exigences pour certaines balises lors de leur création. Les spécifications d’un service peuvent également être mises à jour de manière dynamique pendant qu’il est en cours d’exécution.

Le balisage des nœuds est similaire aux [contraintes de placement](service-fabric-cluster-resource-manager-configure-services.md) et est généralement utilisé pour contrôler les nœuds sur lesquels un service s’exécute. Chaque service Service Fabric peut être configuré pour exiger le placement d’une balise ou son exécution.

Le balisage de nœud est pris en charge pour tous les types de service hébergés Service Fabric (Reliable Services, exécutables invités et conteneurs). Pour utiliser le balisage de nœud, vous devez exécuter la version 8.0 ou une version ultérieure du runtime Service Fabric.

Le reste de cet article décrit les différentes façons d’activer ou de désactiver le balisage des nœuds et fournit des exemples d’utilisation de cette fonctionnalité.


## <a name="describing-dynamic-node-tags"></a>Description des balises de nœud dynamiques
Les services peuvent spécifier les balises dont ils ont besoin. Il existe deux types d’étiquettes :
* Les **balises requises pour le placement** décrivent un ensemble de balises requises uniquement pour le placement de service. Une fois le réplica placé, ces balises peuvent être supprimées sans interrompre le service. Si l’une de ces balises est supprimée du nœud, le réplica de service continue de fonctionner et Service Fabric ne supprime pas le service

* Les **balises requises pour l’exécution** décrivent un ensemble de balises requises pour le placement et l’exécution du service. Si l’une des balises en cours d’exécution requises est supprimée, Service Fabric déplace le service vers un autre nœud dont les balises sont spécifiées.

Exemple : Des balises requises pour le placement peuvent être utilisées lorsque vous utilisez un type de service d’activateur de conteneur, et que vous avez besoin de ce service pour que votre conteneur soit placé. Dès que le conteneur est activé, vous n’avez plus besoin de l’activateur, et vous pouvez supprimer la balise associée, mais le conteneur doit continuer à s’exécuter.
Des balises requises pour l’exécution peuvent être utilisées lorsque vous avez un service de facturation, qu’il est commode de colocaliser avec le service orienté utilisateur. Lorsque le service de facturation échoue sur le nœud, vous supprimez la balise associée et le service accessible à l’utilisateur est déplacé vers un autre nœud, dont le service de facturation et sa balise sont présents.

Une balise ou un ensemble de balises peut être ajouté, mis à jour ou supprimé d’un nœud unique à l’aide de mécanismes d’interface de Service Fabric standard, tels que les API C#, les API REST ou les commandes PowerShell.

> [!NOTE]
> Service Fabric ne gère pas les distributions UD/FD lors de l’utilisation de balises de nœud. Gérez les balises de nœud de manière appropriée, afin de ne pas subir de violations FD/UD en raison d’une mauvaise distribution des balises sur les domaines.

## <a name="enabling-dynamic-node-tags"></a>Activation des balises de nœud dynamiques
Pour utiliser cette fonctionnalité, vous devez activer la configuration NodeTaggingEnabled dans la section PlacementAndLoadBalancing du manifeste de cluster à l’aide de XML ou de JSON :

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="NodeTaggingEnabled" Value="true" />
</Section>
```

via ClusterConfig.json pour les déploiements autonomes ou Template.json pour les clusters hébergés sur Azure :

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "NodeTaggingEnabled",
          "value": "true"
      }
    ]
  }
]
```

## <a name="setting-dynamic-node-tags"></a>Définition de balises de nœud dynamiques

### <a name="using-powershell"></a>Utilisation de PowerShell

Ajout de balises de nœud au nœud :

```posh
Add-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
Cette commande ajoute les balises « SampleTag1 » et « SampleTag2 » sur le nœud DB.1.

Supprimer des balises de nœud du nœud :

```posh
Remove-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
Cette commande va supprimer les balises « SampleTag1 » et « SampleTag2 » du nœud DB.1.

### <a name="using-c-apis"></a>Utilisation des API C#

Ajout de balises de nœud au nœud :

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.AddNodeTagsAsync("DB.1", nodeTagsList);
```

Supprimer des balises de nœud du nœud :

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.RemoveNodeTagsAsync("DB.1", nodeTagsList);
```

## <a name="setting-required-tags-for-services"></a>Définition des balises requises pour les services

### <a name="using-powershell"></a>Utilisation de PowerShell

Création d’un service :

```posh
New-ServiceFabricService -ApplicationName fabric:/HelloWorld -ServiceName fabric:/HelloWorld/svc1 -ServiceTypeName HelloWorldStateful -Stateful -PartitionSchemeSingleton -TargetReplicaSetSize 5 -MinReplicaSetSize 3 -TagsRequiredToRun @("SampleTag1") - TagsRequiredToPlace @("SampleTag2")
```
Cette commande crée un service qui nécessite que « SampleTag2 » soit présent sur un nœud pour que le service y soit placé, et que « SampleTag1 » soit présent pour que le service continue à s’exécuter sur ce nœud.

Mise à jour d’un service existant :

```posh
Update-ServiceFabricService -Stateful -ServiceName fabric:/myapp/test -TagsRequiredToRun @("SampleTag1") -TagsRequiredToPlace @("SampleTag2")
```
Cette commande met à jour un service qui nécessite que « SampleTag2 » soit présent sur un nœud pour que le service y soit placé, et que « SampleTag1 » soit présent pour que le service continue à s’exécuter sur ce nœud.

### <a name="using-c-apis"></a>Utilisation des API C#

Création d’un service :

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceDescription.ServiceTags = serviceTags;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Mise à jour d’un service existant :

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceUpdate.ServiceTags = serviceTags;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```

Toutes ces commandes s’appliquent également aux services sans état.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur les [contraintes de placement](service-fabric-cluster-resource-manager-configure-services.md)
