---
title: Migrer vos données de visage d’un abonnement à un autre - API Visage
titleSuffix: Azure Cognitive Services
description: Ce guide vous montre comment migrer vos données de visage stockées d’un abonnement API Visage à un autre.
services: cognitive-services
author: lewlu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: lewlu
ms.openlocfilehash: 95b339e8d7f2c5c63c30e002411152b50cece2a5
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448779"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migrer vos données de visage vers un autre abonnement API Visage

Ce guide vous montre comment déplacer des données de visage (comme un objet **PersonGroup** de visages enregistré) vers un autre abonnement API Visage à l’aide de la fonctionnalité d’instantané. Cela vous évite d’avoir à créer et entraîner un objet **PersonGroup** ou **FaceList** à plusieurs reprises quand vous déplacez ou développez vos opérations. Par exemple, vous avez créé un objet **PersonGroup** à l’aide d’un abonnement d’essai gratuit et vous souhaitez à présent migrer cet objet vers votre abonnement payant, ou vous avez besoin de synchroniser les données de visage entre plusieurs régions en vue d’une opération à grande échelle.

Cette même stratégie de migration s’applique également aux objets **LargePersonGroup** et **LargeFaceList**. Si vous ne connaissez pas bien les concepts utilisés dans ce guide, lisez les définitions correspondantes dans ce [glossaire](../Glossary.md). Ce guide utilise la bibliothèque cliente .NET de l’API Visage en C#.

## <a name="prerequisites"></a>Conditions préalables

- Deux clés d’abonnement API Visage (une avec les données existantes et une pour les données migrées). Suivez les instructions dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner au service API Visage et obtenir votre clé.
- La chaîne de l’ID d’abonnement API Visage correspondant à l’abonnement cible (disponible dans le panneau **Vue d’ensemble** dans le portail Azure). 
- N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).


## <a name="create-the-visual-studio-project"></a>Créer le projet Visual Studio

Ce guide utilise une application console simple pour effectuer la migration des données de visage. Pour obtenir une implémentation complète, consultez l’[exemple d’instantané de l’API Visage](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) sur GitHub.

1. Dans Visual Studio, créez un projet **Application console (.NET Framework)** et nommez-le **FaceApiSnapshotSample**. 
1. Récupérez les packages NuGet requis. Cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions, puis sélectionnez **Gérer les packages NuGet**. Cliquez sur l’onglet **Parcourir**, sélectionnez **Inclure la préversion**, puis recherchez et installez le package suivant :
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)


## <a name="create-face-clients"></a>Créer des instances FaceClient

Dans la méthode **Main** dans *Program.cs*, créez deux instances **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** pour vos abonnements source et cible. Cet exemple utilise un abonnement API Visage dans la région Asie Est comme source et un autre abonnement dans la région USA Ouest comme cible. Vous verrez ainsi comment migrer des données d’une région Azure vers une autre. Si vos abonnements se trouvent dans des régions différentes, vous devez changer les chaînes `Endpoint` en conséquence.

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

Vous devez aussi indiquer les valeurs des clés d’abonnement et les URL de point de terminaison pour vos abonnements source et cible.


## <a name="prepare-a-persongroup-for-migration"></a>Préparer un PersonGroup en vue de sa migration

Vous avez besoin de l’ID de l’objet **PersonGroup** dans votre abonnement source que vous voulez migrer vers l’abonnement cible. Utilisez la méthode **[PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet)** pour récupérer une liste de vos objets **PersonGroup**, puis obtenez la propriété **[PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId)**. Ce processus pourra différer en fonction des objets **PersonGroup** présents. Dans ce guide, l’ID de l’objet **PersonGroup** source est stocké dans `personGroupId`.

> [!NOTE]
> L’[exemple de code](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) crée et entraîne un objet **PersonGroup** à migrer. Toutefois, dans la plupart des cas, vous aurez déjà un objet **PersonGroup** prêt à être utilisé.

## <a name="take-snapshot-of-persongroup"></a>Prendre un instantané du PersonGroup

Un instantané est un stockage à distance temporaire pour certains types de données de visage. Il fonctionne comme une sorte de Presse-papiers pour copier des données d’un abonnement à un autre. L’utilisateur « prend » d’abord un instantané des données de l’abonnement source, puis les « applique » à un nouvel objet de données dans l’abonnement cible.

Utilisez l’instance **FaceClient** de l’abonnement source pour prendre un instantané du **PersonGroup**, en utilisant **[TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet)** avec l’ID du **PersonGroup** et l’ID de l’abonnement cible. Si vous avez plusieurs abonnements cibles, vous pouvez les ajouter en tant qu’entrées de tableau dans le troisième paramètre.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Le processus de prise et d’application d’instantanés n’interrompt pas les appels réguliers aux objets **PersonGroup** (ou **FaceList**) source et cible. Toutefois, nous vous déconseillons d’effectuer des appels simultanés qui changent l’objet source ([appels de gestion de FaceList](https://docs.microsoft.com/rest/api/cognitiveservices/face/facelist) ou appel d’[entraînement de PersonGroup](https://docs.microsoft.com/rest/api/cognitiveservices/face/persongroup/train), par exemple), car l’opération d’instantané est susceptible de s’effectuer avant ou après ces opérations, ou de rencontrer des erreurs. 

## <a name="retrieve-the-snapshot-id"></a>Récupérer l’ID de l’instantané

La capture instantanée en prenant la méthode est asynchrone,. vous devrez donc attendre son achèvement (instantané opérations ne peut pas être annulées). Dans ce code, la méthode `WaitForOperation` supervise l’appel asynchrone, en vérifiant l’état toutes les 100 ms. À la fin de l’opération, vous serez en mesure de récupérer l’ID de l’opération. Vous pouvez le trouver dans le champ `OperationLocation`. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Voici un exemple de valeur `OperationLocation` standard :

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

La méthode d’assistance `WaitForOperation` est ici :

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

Quand l’état de l’opération est marqué comme `Succeeded`, vous pouvez trouver l’ID de l’instantané dans le champ `ResourceLocation` de l’instance **OperationStatus** retournée.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Voici un exemple de valeur `resourceLocation` standard :

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-snapshot-to-target-subscription"></a>Appliquer l’instantané à l’abonnement cible

Ensuite, créez l’objet **PersonGroup** dans l’abonnement cible, en utilisant un ID généré de manière aléatoire. Ensuite, avec l’instance **FaceClient** de l’abonnement cible, appliquez l’instantané à ce PersonGroup, en passant l’ID de l’instantané et l’ID du nouveau **PersonGroup**. 

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Un objet instantané est valide pendant 48 heures uniquement. Prenez un instantané uniquement si vous êtes prêt à l’utiliser peu de temps après pour la migration des données.

Une requête d’application d’un instantané retourne un autre ID d’opération. Vous pouvez trouver cet ID dans le champ `OperationLocation` de l’instance **applySnapshotResult** retournée. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Le processus d’application de l’instantané est également asynchrone. Là encore, vous devez utiliser `WaitForOperation` pour attendre la fin de son exécution.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Tester la migration des données

Une fois que vous avez appliqué l’instantané, le nouveau **PersonGroup** dans l’abonnement cible doit être rempli avec les données de visage sources. Par défaut, les résultats de l’entraînement sont également copiés. Le nouvel objet **PersonGroup** peut donc directement être utilisé dans les appels d’identification des visages sans avoir besoin d’être réentraîné.

Pour tester la migration des données, vous pouvez exécuter les opérations suivantes et comparer les résultats affichés dans la console.

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Utilisez les méthodes d’assistance suivantes :

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

Maintenant, vous pouvez commencer à utiliser le nouveau **PersonGroup** dans l’abonnement cible. 

Si vous avez besoin ultérieurement de mettre à jour l’objet **PersonGroup**, vous devrez recréer un **PersonGroup** (en suivant les étapes de ce guide) pour recevoir l’instantané. Un objet **PersonGroup** peut avoir un seul instantané appliqué à la fois.

## <a name="clean-up-resources"></a>Supprimer des ressources

Une fois que vous avez terminé la migration des données de visage, nous vous recommandons de supprimer manuellement l’objet instantané.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="related-topics"></a>Rubriques connexes

- [Documentation de référence sur les instantanés (SDK .NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Exemple d’instantané de l’API Visage](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Comment ajouter des visages](how-to-add-faces.md)
- [How to Detect Faces in Image](HowtoDetectFacesinImage.md) (Comment détecter des visages dans l’image)
- [Comment identifier des visages dans une image](HowtoIdentifyFacesinImage.md)
