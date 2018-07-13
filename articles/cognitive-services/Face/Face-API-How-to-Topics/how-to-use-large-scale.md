---
title: Utiliser la fonctionnalité de grande échelle dans l’API Visage | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Utilisez la fonctionnalité de grande échelle dans l’API Visage de Cognitive Services.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: f7b3ac57cf6b24c8a90b4ea59757d3a2cfafd781
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368161"
---
# <a name="how-to-use-the-large-scale-feature"></a>Guide pratique pour utiliser la fonctionnalité de grande échelle

Ce guide est un article avancé sur la migration du code en vue d’une mise à l’échelle de PersonGroup et FaceList vers LargePersonGroup et LargeFaceList, respectivement.
Il explique le processus de migration en partant du principe que vous savez comment utiliser PersonGroup et FaceList.
Pour vous familiariser avec les opérations de base, consultez d’autres tutoriels tels que [Guide pratique pour identifier des visages dans une image](HowtoIdentifyFacesinImage.md).

L’API Visage de Microsoft a récemment publié deux fonctionnalités pour activer des scénarios à grande échelle, LargePersonGroup et LargeFaceList, collectivement appelés opérations à grande échelle.
LargePersonGroup peut contenir jusqu’à 1 000 000 de personnes avec un maximum de 248 visages, et LargeFaceList peut contenir jusqu’à 1 000 000 de visages.

Les opérations à grande échelle sont semblables aux PersonGroup et FaceList classiques, mais il existe certaines différences notables en raison de la nouvelle architecture.
Ce guide explique le processus de migration en partant du principe que vous savez comment utiliser PersonGroup et FaceList.
Les exemples sont écrits en C# à l’aide de la bibliothèque cliente de l’API Visage.

Pour activer les performances de recherche de Visage pour Identification et FindSimilar à grande échelle, vous devez introduire une opération d’entraînement afin de prétraiter LargeFaceList et LargePersonGroup.
Le temps d’entraînement varie de quelques secondes à environ une demi-heure, en fonction de la capacité réelle.
Pendant la période d’entraînement, il est toujours possible d’effectuer Identification et FindSimilar si une formation réussie est exécutée avant.
Toutefois, l’inconvénient est que les nouvelles personnes et les nouveaux visages ajoutés n’apparaîtront pas dans le résultat avant qu’une nouvelle publication de la migration vers l’entraînement à grande échelle soit terminée.

## <a name="concepts"></a> Concepts

Si vous ne connaissez pas bien les concepts suivants utilisés dans ce guide, les définitions sont accessibles dans le [glossaire](../Glossary.md) :

- LargePersonGroup : Collection de personnes avec une capacité allant jusqu’à 1 000 000.
- LargeFaceList : Collection de visages avec une capacité allant jusqu’à 1 000 000.
- Entraîner : Processus préliminaire destiné à garantir les performances d’Identification/FindSimilar.
- Identification : Identifier un ou plusieurs visages d’un PersonGroup ou LargePersonGroup.
- FindSimilar : Rechercher des visages similaires à partir d’une FaceList ou LargeFaceList.

## <a name="initialization"></a> Étape 1 : Autoriser l’appel d’API

Quand vous utilisez la bibliothèque cliente de l’API Visage, la clé d’abonnement et le point de terminaison d’abonnement sont transmis par le biais du constructeur de la classe FaceServiceClient. Par exemple : 

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionRegion = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/";
FaceServiceClient FaceServiceClient = new FaceServiceClient(SubscriptionKey, SubscriptionRegion);
```

La clé d’abonnement avec le point de terminaison correspondant peut être obtenue à partir de la page Place de Marché du portail Azure.
Voir [Abonnements](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="migrate"></a> Étape 2 : Migration de code en action

Cette section est axée uniquement sur la migration de l’implémentation de PersonGroup/FaceList vers LargePersonGroup/LargeFaceList.
Bien que LargePersonGroup/LargeFaceList diffèrent de PersonGroup/FaceList en termes de conception et d’implémentation interne, les interfaces d’API sont similaires pour des raisons de compatibilité descendante.

La migration des données n’étant pas prise en charge, vous devez recréer le LargePersonGroup/LargeFaceList.

## <a name="largepersongroup"></a> Étape 2.1 : Migrer PersonGroup vers LargePersonGroup

La migration de PersonGroup vers LargePersonGroup est fluide car ils partagent exactement les mêmes opérations au niveau du groupe.

Pour l’implémentation liée à PersonGroup/Person, il est uniquement nécessaire de changer les chemins de l’API ou le module/la classe de SDK en veillant à sélectionner LargePersonGroup et LargePersonGroup Person.

Pour obtenir des informations de référence en termes de migration des données, consultez [Guide pratique pour ajouter des visages](how-to-add-faces.md).

## <a name="largefacelist"></a> Étape 2.2 : Migrer FaceList vers LargeFaceList

| API FaceList | API LargeFaceList |
|:---:|:---:|
| Créer | Créer |
| Supprimer | Supprimer |
| Obtenir | Obtenir |
| Liste | Liste |
| Mettre à jour | Mettre à jour |
| - | Entraîner |
| - | Obtenir l’état de l’entraînement |

Le tableau précédent est une comparaison des opérations au niveau de la liste entre FaceList et LargeFaceList.
Comme nous pouvons l’observer, LargeFaceList comporte de nouvelles opérations, Entraîner et Obtenir l’état de l’entraînement, par rapport à FaceList.
La LargeFaceList doit être entraînée avant l’opération [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), alors qu’aucun entraînement n’est nécessaire pour FaceList.
L’extrait de code suivant est une fonction d’assistance pour attendre l’entraînement d’une LargeFaceList.

```CSharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceServiceClient.TrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await FaceServiceClient.GetLargeFaceListTrainingStatusAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

Avant, une utilisation typique de FaceList avec ajout de visages et FindSimilar aurait été :

```CSharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateFaceListAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToFaceListAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

Lors de sa migration vers LargeFaceList, le code doit être le suivant :

```CSharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateLargeFaceListAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToLargeFaceListAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Comme on peut le voir ci-dessus, la gestion des données et la partie FindSimilar sont presque identiques.
La seule exception est qu’une nouvelle opération d’entraînement de pré-traitement doit se terminer dans la LargeFaceList avant que FindSimilar puisse fonctionne.

## <a name="train"></a>Étape 3 : Suggestions d’entraînement

Bien que l’opération d’entraînement accélère [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) et [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), le temps de formation augmente, en particulier à grande échelle.
Le tableau suivant fournit une estimation du temps d’entraînement à différentes échelles :

| Échelle (visages ou personnes) | Estimation du temps d’entraînement |
|:---:|:---:|
| 1 000 | 1-2 s |
| 10 000 | 5-10 s |
| 100 000 | 1 - 2 min |
| 1 000 000 | 10 - 30 min |

Pour mieux utiliser la fonctionnalité de grande échelle, nous vous recommandons d’appliquer certaines stratégies.

## <a name="interval"></a>Étape 3.1 : Personnaliser l’intervalle de temps

Comme indiqué dans `TrainLargeFaceList()`, il existe un `timeIntervalInMilliseconds` afin de retarder le processus de vérification d’état d’entraînement infini.
Pour LargeFaceList avec davantage de visages, l’utilisation d’un intervalle plus élevé permet de réduire le nombre et le coût des appels.
L’intervalle de temps doit être personnalisé en fonction de la capacité attendue de la LargeFaceList.

La même stratégie s’applique également à LargePersonGroup.
Par exemple, lors de l’entraînement d’un LargePersonGroup avec 1 000 000 de personnes, `timeIntervalInMilliseconds` peut être égal à 60 000 (autrement dit, un intervalle d’une minute).

## <a name="buffer"></a>Étape 3.2 : Mémoire tampon à petite échelle

Il est possible d’effectuer des recherches de personnes/visages dans LargePersonGroup/LargeFaceList uniquement après son entraînement.
Dans un scénario dynamique, de nouvelles personnes et nouveaux visages sont ajoutés constamment et doivent être immédiatement interrogeables, mais l’entraînement peut être plus long qu’on ne le souhaiterait.
Pour atténuer ce problème, vous pouvez utiliser un LargePersonGroup/LargeFaceList supplémentaire à petite échelle en guise de mémoire tampon uniquement pour les entrées qui viennent d’être ajoutées.
Cette mémoire tampon est plus rapide à entraîner en raison de sa plus petite taille, et la recherche immédiate dans cette mémoire tampon temporaire devrait fonctionner.
Utilisez cette mémoire tampon en combinaison avec un entraînement sur le LargePersonGroup/LargeFaceList maître en exécutant l’entraînement maître avec un intervalle plus élevé, par exemple au milieu de la nuit, et tous les jours.

Exemple de flux de travail :
1. Créez un LargePersonGroup/LargeFaceList maître (collection principale) et un LargePersonGroup/LargeFaceList mémoire tampon (collection de mémoire tampon). La collection de mémoire tampon est destinée uniquement aux personnes/visages nouvellement ajoutés.
1. Ajoutez de nouvelles personnes et de nouveaux visages à la collection principale et à la collection de mémoire tampon.
1. Entraînez uniquement la collection de mémoire tampon avec un court intervalle, afin de garantir la prise en compte des nouvelles entrées.
1. Appelez Identification/FindSimilar par rapport à la collection principale et à la collection de mémoire tampon, et fusionnez les résultats.
1. Quand la taille de la collection de mémoire tampon augmente jusqu’à un seuil ou un temps d’inactivité système, créez une nouvelle collection de mémoire tampon et déclenchez l’entraînement sur la collection principale.
1. Supprimez l’ancienne collection de mémoire tampon une fois l’entraînement sur la collection principale terminé.

## <a name="standalone"></a>Étape 3.3 : Entraînement autonome

Si une latence relativement longue est acceptable, il n’est pas nécessaire de déclencher l’opération d’entraînement juste après l’ajout de nouvelles données.
Au lieu de cela, elle peut être séparée de la logique principale et déclenchée régulièrement.
Cette stratégie convient aux scénarios dynamiques avec une latence acceptable, et peut être appliquée aux scénarios statiques afin de réduire encore davantage la fréquence d’entraînement.

Supposez qu’il existe une fonction `TrainLargePersonGroup` similaire à la `TrainLargeFaceList`.
Une implémentation standard de l’entraînement autonome sur LargePersonGroup en appelant la classe [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) dans `System.Timers` serait :

```CSharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    FaceServiceClient.CreateLargePersonGroupAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Setup a standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1 minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1 hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces and Identification except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Pour plus d’informations sur la gestion des données et les implémentations relatives à l’identification, consultez [Guide pratique pour ajouter des visages](how-to-add-faces.md) et [Guide pratique pour identifier des visages dans une image](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a> Synthèse

Dans ce guide, vous avez découvert comment migrer le code PersonGroup/FaceList existant (pas des données) vers LargePersonGroup/LargeFaceList :

- LargePersonGroup et LargeFaceList fonctionnent comme PersonGroup et FaceList, sauf qu’une opération d’entraînement est exigée par LargeFaceList.
- Vous devez appliquer une stratégie d’entraînement appropriée pour la mise à jour dynamique des données d’un jeu de données à grande échelle.

## <a name="related"></a> Rubriques connexes

- [Guide pratique pour identifier des visages dans une image](HowtoIdentifyFacesinImage.md)
- [Guide pratique pour ajouter des visages](how-to-add-faces.md)
