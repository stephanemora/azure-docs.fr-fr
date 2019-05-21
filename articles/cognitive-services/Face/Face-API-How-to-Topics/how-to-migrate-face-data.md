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
ms.openlocfilehash: 702aed12860c090e83b997e6b56d56e06b416568
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65913789"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migrer vos données de visage vers un autre abonnement API Visage

Ce guide vous montre comment déplacer des données de face, telles qu’un objet de groupe de personnes enregistré avec les visages, vers un autre abonnement API visage Azure Cognitive Services. Pour déplacer les données, vous utilisez la fonctionnalité de capture instantanée. De cette façon vous évitez d’avoir à créer et former un objet groupe de personnes ou FaceList lorsque vous déplacez ou que vous développez vos opérations à plusieurs reprises. Par exemple, peut-être vous créé un objet de groupe de personnes à l’aide d’un abonnement d’essai gratuit et que vous souhaitez maintenant faire migrer vers votre abonnement payant. Ou bien, vous devrez peut-être synchroniser les données de visage dans différentes régions pour une opération de grande entreprise.

Cette même stratégie de migration s’applique également aux objets LargePersonGroup et LargeFaceList. Si vous n’êtes pas familiarisé avec les concepts de ce guide, consultez leurs définitions dans le [doivent faire Face les concepts de reconnaissance](../concepts/face-recognition.md) guide. Ce guide utilise la bibliothèque cliente .NET de l’API Visage en C#.

## <a name="prerequisites"></a>Conditions préalables

Vous avez besoin des éléments suivants :

- Deux API visage clés d’abonnement, l’autre avec les données existantes et à migrer vers. Pour vous abonner au service de l’API visage et obtenez votre clé, suivez les instructions de [créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- L’API visage abonnement chaîne d’ID qui correspond à l’abonnement cible. Pour le rechercher, sélectionnez **vue d’ensemble** dans le portail Azure. 
- N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Créer le projet Visual Studio

Ce guide utilise une application de console simple pour exécuter la migration des données face. Pour une implémentation complète, consultez le [exemple de capture instantanée API visage](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) sur GitHub.

1. Dans Visual Studio, créez un nouveau projet de .NET Framework d’application Console. Nommez-le **FaceApiSnapshotSample**.
1. Récupérez les packages NuGet requis. Cliquez sur votre projet dans l’Explorateur de solutions, puis sélectionnez **gérer les Packages NuGet**. Sélectionnez le **Parcourir** et sélectionnez **inclure la version préliminaire**. Recherchez et installez le package suivant :
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Créer des instances FaceClient

Dans le **Main** méthode dans *Program.cs*, créez deux [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) instances pour vos abonnements source et cible. Cet exemple utilise un abonnement de visage dans la région Asie comme source et un abonnement de l’ouest des États-Unis comme cible. Cet exemple montre comment migrer des données à partir d’une région Azure vers un autre. Si vos abonnements sont dans différentes régions, modifiez le `Endpoint` chaînes.

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

Renseignez les valeurs de clé d’abonnement et les URL de point de terminaison pour vos abonnements source et cible.


## <a name="prepare-a-persongroup-for-migration"></a>Préparer un PersonGroup en vue de sa migration

Vous avez besoin de l’ID du groupe de personnes dans votre abonnement source à migrer vers l’abonnement cible. Utilisez le [PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) méthode pour récupérer une liste de vos objets de groupe de personnes. Puis obtenir le [PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) propriété. Ce processus est différent selon le groupe de personnes d’objets présents. Dans ce guide, l’ID de groupe de personnes de la source est stockée dans `personGroupId`.

> [!NOTE]
> Le [exemple de code](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) crée et effectue l’apprentissage d’un nouveau groupe de personnes à migrer. Dans la plupart des cas, vous devez déjà disposer d’un groupe de personnes à utiliser.

## <a name="take-a-snapshot-of-a-persongroup"></a>Prendre un instantané d’un groupe de personnes

Un instantané est temporaire à distance pour certains types de données de Face. Il fonctionne comme une sorte de Presse-papiers pour copier des données d’un abonnement à un autre. Tout d’abord, vous prenez un instantané des données dans l’abonnement source. Puis vous l’appliquer à un nouvel objet de données dans l’abonnement cible.

Instance de l’abonnement source FaceClient permet de prendre un instantané du groupe de personnes. Utilisez [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) avec l’ID de groupe de personnes et les ID de. l’abonnement cible Si vous avez plusieurs abonnements cibles, ajoutez-les en tant qu’entrées de tableau dans le troisième paramètre.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Le processus consistant à prendre et application des captures instantanées n’interrompre tous les appels régulières à la source ou cible PersonGroups ou liste de visages. N’effectuez pas les appels simultanés qui modifient l’objet source, tel que [appels de gestion FaceList](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) ou [Train de groupe de personnes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) appeler, par exemple. L’opération de capture instantanée peut s’exécuter avant ou après ces opérations, ou peut rencontrer des erreurs.

## <a name="retrieve-the-snapshot-id"></a>Récupérer l’ID d’instantané

La méthode utilisée pour prendre des instantanés étant asynchrone, vous devez attendre son achèvement. Opérations sur les instantanés ne peut pas être annulées. Dans ce code, le `WaitForOperation` méthode surveille l’appel asynchrone. Il vérifie l’état de toutes les 100 ms. Une fois l’opération terminée, extraire un ID d’opération en analysant le `OperationLocation` champ. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Classique `OperationLocation` valeur ressemble à ceci :

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

Après l’état de l’opération affiche `Succeeded`, obtenez l’ID d’instantané en analysant le `ResourceLocation` champ de l’instance OperationStatus retournée.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Classique `resourceLocation` valeur ressemble à ceci :

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Appliquer un instantané à un abonnement cible

Ensuite, créez le nouveau groupe de personnes dans l’abonnement cible à l’aide d’un ID généré de manière aléatoire. Puis utilisez instance FaceClient de l’abonnement cible pour appliquer l’instantané à ce groupe de personnes. Passez dans l’instantané ID et le nouvel ID de groupe de personnes.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Un objet de l’instantané n’est valide que pendant 48 heures. Uniquement prendre un instantané si vous voulez l’utiliser pour la migration de données peu après.

Une demande d’appliquer instantané retourne un autre ID d’opération. Pour obtenir cet ID, analyser le `OperationLocation` champ de l’instance applySnapshotResult retournée. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Le processus d’application instantané est également asynchrone, donc utilisez `WaitForOperation` pour attendre qu’elle se termine.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Tester la migration des données

Après avoir appliqué l’instantané, le nouveau groupe de personnes dans l’abonnement cible remplit avec les données d’origine de la face. Par défaut, les résultats de formation sont également copiés. Le nouveau groupe de personnes est prêt pour les appels d’identification face sans avoir besoin de REFORMATION.

Pour tester la migration des données, exécutez les opérations suivantes et comparez les résultats qu’ils impriment sur la console :

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

Vous pouvez maintenant utiliser le nouveau groupe de personnes dans l’abonnement cible. 

Pour mettre à jour la cible du groupe de personnes à l’avenir, créez un nouveau groupe de personnes pour recevoir l’instantané. Pour ce faire, suivez les étapes décrites dans ce guide. Un objet de groupe de personnes unique peut avoir une capture instantanée n'appliquée qu’une seule fois.

## <a name="clean-up-resources"></a>Supprimer des ressources

Après avoir terminé la migration doivent faire face les données, supprimez manuellement l’objet instantané.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Étapes suivantes

Ensuite, consultez la documentation de référence API pertinentes, Explorer un exemple d’application qui utilise la fonctionnalité de capture instantanée, ou suivez le guide pratique pour commencer à utiliser les autres opérations d’API mentionnées ici :

- [Documentation de référence sur les instantanés (SDK .NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Exemple de capture instantanée de l’API visage](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Ajouter des faces](how-to-add-faces.md)
- [Détectez les visages dans une image](HowtoDetectFacesinImage.md)
- [Identifiez les visages dans une image](HowtoIdentifyFacesinImage.md)
