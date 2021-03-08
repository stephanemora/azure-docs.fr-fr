---
title: Vue d’ensemble de Runtime SDK
description: Familiarisez-vous avec le SDK du runtime Object Anchors.
author: craigktreasure
manager: vriveras
services: azure-object-anchors
ms.author: crtreasu
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: azure-object-anchors
ms.openlocfilehash: 020f727674449523a57a608e8930d67e0f239cf6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743266"
---
# <a name="runtime-sdk-overview"></a>Vue d’ensemble de Runtime SDK

Cette section fournit une vue d’ensemble de haut niveau du Kit de développement logiciel (SDK) du runtime Object Anchors, qui permet de détecter des objets à l’aide d’un modèle Object Anchors. Vous aurez une idée de la façon dont un objet est représenté et de la façon dont les différents composants sont utilisés.

Tous les types décrits ci-dessous peuvent être retrouvés dans l’espace de noms **Microsoft.MixedReality.ObjectAnchors**.

## <a name="types"></a>Types

### <a name="objectmodel"></a>ObjectModel

Un [ObjectModel](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectmodel) représente la géométrie d’un objet physique et code les paramètres nécessaires à la détection et à l’estimation de la pose. Il doit être créé à l’aide du [service Object Anchors](../quickstarts/get-started-model-ingestion.md). Ensuite, une application peut charger le fichier de modèle généré à l’aide de l’API Object Anchors et interroger le maillage incorporé dans ce modèle pour la visualisation.

### <a name="objectsearcharea"></a>ObjectSearchArea

Un [ObjectSearchArea](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectsearcharea) spécifie l’espace pour rechercher un ou plusieurs objets. Il est défini par un ID de nœud de graphique spatial et des limites spatiales dans le système de coordonnées représenté par l’ID de nœud de graphique spatial. Le Kit de développement logiciel (SDK) du runtime Object Anchors prend en charge quatre types de limites, à savoir un **champ de vision**, un **cadre englobant**, une **sphère** et un **emplacement**.

### <a name="objectquery"></a>ObjectQuery

Un [ObjectQuery](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery) indique à un **observateur d’objet** comment rechercher des objets d’un modèle donné. Il fournit les paramètres réglables suivants, dont les valeurs par défaut peuvent être récupérées à partir d’un modèle objet.

#### <a name="minsurfacecoverage"></a>MinSurfaceCoverage

La propriété [MinSurfaceCoverage](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.minsurfacecoverage) indique la valeur pour considérer une instance comme détectée.

Pour chaque candidat à un objet, un **observateur** calcule le rapport des surfaces superposées entre le modèle objet transformé et la scène, puis il signale ce candidat à l’application uniquement lorsque le rapport de couverture est supérieur à un seuil donné.

#### <a name="isexpectedtobestandingongroundplane"></a>IsExpectedToBeStandingOnGroundPlane

La propriété [IsExpectedToBeStandingOnGroundPlane](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.isexpectedtobestandingongroundplane) indique si l’objet cible est supposé reposer sur le plan de masse.

Un plan de masse correspond au sol horizontal le plus bas dans la zone de recherche. Il fournit une bonne contrainte sur les poses d’objet possibles. L’activation de cet indicateur guidera l'**observateur** pour estimer la pose dans un espace limité et peut permettre d’améliorer l’exactitude. Ce paramètre est ignoré si le modèle n’est pas supposé reposer sur le plan de masse.

#### <a name="expectedmaxverticalorientationindegrees"></a>ExpectedMaxVerticalOrientationInDegrees

La propriété [ExpectedMaxVerticalOrientationInDegrees](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.expectedmaxverticalorientationindegrees) indique l’angle maximal attendu, en degrés, entre la direction vers le haut d’une instance d’objet et la gravité.

Ce paramètre fournit une autre contrainte sur la direction vers le haut d’une pose estimée. Par exemple, si un objet est vertical, ce paramètre peut avoir la valeur 0. Object Anchors n’est pas censé détecter les objets qui sont différents du modèle. Si un modèle est vertical, il ne détectera pas une instance retournée. Un nouveau modèle devra être utilisé pour la disposition retournée. La même règle s’applique à l’articulation.

#### <a name="maxscalechange"></a>MaxScaleChange

La propriété [MaxScaleChange](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.maxscalechange) indique le changement d’échelle d’objet maximal (0 ~ 1) par rapport au mappage spatial. L’échelle estimée est appliquée aux sommets d’objets transformés centrés à l’origine et alignés sur l’axe. Les échelles estimées peuvent ne pas correspondre à l’échelle réelle entre un modèle CAO et sa représentation physique, mais certaines valeurs permettent à l’application de restituer un modèle objet proche du mappage spatial sur l’objet physique.

#### <a name="searchareas"></a>SearchAreas

La propriété [SearchAreas](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.searchareas) indique un tableau de limites spatiales dans lesquelles rechercher des objets.

L'**observateur** recherche des objets dans l’espace commun de toutes les zones de recherche spécifiées dans une requête. Dans cette version, nous renvoyons un objet tout au plus avec une confiance maximale pour réduire la latence.

### <a name="objectinstance"></a>ObjectInstance

Un [ObjectInstance](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectinstance) représente une position hypothétique dans laquelle une instance d’un modèle donné peut se trouver dans le système de coordonnées HoloLens. Chaque instance est accompagnée d’une propriété `SurfaceCoverage` pour indiquer la qualité de la pose estimée.

Une instance est créée en appelant la méthode `ObjectObserver.DetectAsync`, puis est mise à jour automatiquement en arrière-plan lorsqu’elle est active. Une application peut écouter l’événement à l’état modifié sur une instance particulière ou modifier le mode de suivi pour suspendre/reprendre la mise à jour. Une instance est automatiquement supprimée de l'**observateur** lorsque le suivi est perdu.

### <a name="objectobserver"></a>ObjectObserver

Un [ObjectObserver](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectobserver) charge des modèles objet, détecte leurs instances et signale les poses 6-DoF de chaque instance dans le système de coordonnées HoloLens.

Bien qu’un modèle objet ou une instance soit créé à partir d’un **observateur**, leurs durées de vie sont indépendantes. Une application peut supprimer un observateur et continuer à utiliser le modèle objet ou l’instance.

### <a name="objectdiagnosticssession"></a>ObjectDiagnosticsSession

[ObjectDiagnosticSession](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.diagnostics.objectdiagnosticssession) enregistre les diagnostics et écrit les données dans une archive.

Une archive de diagnostics comprend le nuage de points de la scène, l’état de l’observateur et des informations sur les modèles. Ces informations sont utiles pour identifier d’éventuels problèmes du runtime. Pour plus d’informations, visitez le [FAQ](../faq.md).

## <a name="runtime-sdk-usage-and-details"></a>Utilisation et détails du SDK du runtime

Cette section doit vous fournir les principes fondamentaux de l’utilisation du Kit de développement logiciel (SDK) du runtime. Elle doit vous fournir suffisamment de contexte pour parcourir les exemples d’applications et voir comment Object Anchors est utilisé de manière holistique.

### <a name="initialization"></a>Initialisation

Les applications doivent appeler l'API `ObjectObserver.IsSupported()` pour déterminer si Object Anchors est pris en charge sur l’appareil avant de l’utiliser. Si l'API `ObjectObserver.IsSupported()` retourne `false`, vérifiez que la fonctionnalité **spatialPerception** est activée dans l’application et/ou mettez à niveau vers la dernière version du système d’exploitation HoloLens.

```cs
if (!ObjectObserver.IsSupported())
{
    // Handle the error
}

// This call should grant the access we need.
var status = await ObjectObserver.RequestAccessAsync();
if(status != ObjectObserverStatus.Allowed)
{
    // Handle the error
}
```

L’application crée ensuite un observateur d’objet et charge les modèles nécessaires générés par le [service d’ingestion Object Anchors](../quickstarts/get-started-model-ingestion.md).

```cs
var observer = new ObjectObserver();

byte[] modelAsBytes; // Load a model into a byte array. Model could be a file, an embedded resource, or a network stream.
var model = await observer.LoadObjectModelAsync(modelAsBytes);

// Note that after a model is loaded, its vertices and normals are transformed into a centered coordinate system for the 
// ease of computing the object pose. The rigid transform can be retrieved through the `OriginToCenterTransform` property.
```

L’application crée une requête pour détecter les instances de ce modèle dans un espace.

```cs
var coordinateSystem = default(SpatialGraphCoordinateSystem);
var searchAreaAsBox = ObjectSearchArea.FromOrientedBox(coordinateSystem, default(SpatialOrientedBox));

// Optionally change the parameters, otherwise use the default values embedded in the model.
var query = new ObjectQuery(model);
query.MinSurfaceCoverage = 0.2f;
query.ExpectedMaxVerticalOrientationInDegrees = 1.5f;
query.MaxScaleChange = 0.1f;
query.SearchAreas.Add(searchAreaAsBox);

// Detection could take long, run in a background thread.
var detectedObjects = await observer.DetectAsync(query);
```

Par défaut, chaque instance détectée est automatiquement suivie par l'**observateur**. Nous pouvons éventuellement manipuler ces instances en modifiant leur mode de suivi ou en écoutant leur événement de changement d’état.

```cs
foreach (var instance in detectedObjects)
{
    // Supported modes:
    // "LowLatencyCoarsePosition" - consumes less CPU cycles thus fast to update the state.
    // "HighLatencyAccuratePosition" - (not yet implemented) consumes more CPU cycles thus potentially taking longer time to update the state.
    // "Paused" - stops to update the state until mode changed to low or high.
    instance.Mode = ObjectInstanceTrackingMode.LowLatencyCoarsePosition;

    // Listen to state changed event on this instance.
    instance.Changed += InstanceChangedHandler;

    // Optionally dispose an instance if not interested in it.
    //instance.Dispose();
}
```

Dans l’événement à l’état modifié, nous pouvons interroger l’état le plus récent ou supprimer une instance si le suivi a été perdu.

```cs
var InstanceChangedHandler = new Windows.Foundation.TypedEventHandler<ObjectInstance, ObjectInstanceChangedEventArgs>((sender, args) =>
{
    // Try to query the current instance state.
    var state = sender.TryGetCurrentState();

    if (state.HasValue)
    {
        // Process latest state via state.Value.
        // An object pose includes scale, rotation and translation, applied in the same order
        // to the object model in the centered coordinate system.
    }
    else
    {
        // This object instance is lost for tracking, and will never be recovered.
        // The caller can detach the Changed event handler from this instance and dispose it.
        sender.Dispose();
    }
});
```

En outre, une application peut éventuellement enregistrer une ou plusieurs sessions de diagnostic pour le débogage hors connexion.

```cs
string diagnosticsFolderPath = Windows.Storage.ApplicationData.Current.TemporaryFolder.Path;
const uint maxSessionSizeInMegaBytes = uint.MaxValue;

// Recording starts on the creation of a diagnostics session.
var diagnostics = new ObjectDiagnosticsSession(observer, maxSessionSizeInMegaBytes);

// Wait for the observer to do a job.

// Application can report some **pseudo ground-truth** pose for an instance acquired from other means.
diagnostics.ReportActualInstanceLocation(instance, coordinateSystem, Vector3.Zero, Quaternion.Identity);

// Close a session and write the diagnostics into an archive at specified location.
await diagnostics.CloseAsync(System.IO.Path.Combine(diagnosticsFolderPath, "diagnostics.zip"));
```

Enfin, nous libérons des ressources en supprimant tous les objets.

```cs
foreach(var instance in activeInstances)
{
    instance.Changed -= InstanceChangedHandler;
    instance.Dispose();
}

model.Dispose();
observer.Dispose();
```
