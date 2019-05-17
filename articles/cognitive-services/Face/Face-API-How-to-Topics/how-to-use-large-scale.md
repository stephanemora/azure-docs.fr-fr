---
title: 'Exemple : Utiliser la fonctionnalité de grande échelle - API Visage'
titleSuffix: Azure Cognitive Services
description: Utilisez la fonctionnalité de grande échelle dans l’API Visage.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: 35ab2d36a5d6c9977398fdbc16ba22eb1d9656a4
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65229833"
---
# <a name="example-how-to-use-the-large-scale-feature"></a>Exemple : Guide pratique pour utiliser la fonctionnalité de grande échelle

Ce guide est un article avancé sur la façon d’effectuer un scale-up de **PersonGroup** et de **FaceList** vers **LargePersonGroup** et **LargeFaceList**, respectivement. Ce guide explique le processus de migration et suppose des connaissances élémentaires de **PersonGroup**, de **FaceList**, de l’opération [Entraînement](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) et des fonctions de reconnaissance faciale. Pour en savoir plus sur ces éléments, consultez le guide conceptuel [Reconnaissance faciale](../concepts/face-recognition.md).

LargePersonGroup et LargeFaceList sont collectivement appelés « opérations à grande échelle ». LargePersonGroup peut contenir jusqu’à 1 000 000 personnes, avec un maximum de 248 visages chacune, et LargeFaceList peut contenir jusqu’à 1 000 000 visages. Les opérations à grande échelle sont semblables aux PersonGroup et FaceList classiques, mais il existe certaines différences notables en raison de la nouvelle architecture. 

Les exemples sont écrits en C# à l’aide de la bibliothèque de client de l’API Visage.

> [!NOTE]
> Pour activer les performances de recherche de Visage pour Identification et FindSimilar à grande échelle, vous devez introduire une opération d’entraînement afin de prétraiter LargeFaceList et LargePersonGroup. Le temps d’entraînement varie de quelques secondes à environ une demi-heure, en fonction de la capacité réelle. Pendant la période d’entraînement, il est toujours possible d’effectuer Identification et FindSimilar si une formation réussie est exécutée avant. Toutefois, l’inconvénient est que les nouvelles personnes et les nouveaux visages ajoutés n’apparaîtront pas dans le résultat avant qu’une nouvelle publication de la migration vers l’entraînement à grande échelle soit terminée.

## <a name="step-1-initialize-the-client-object"></a>Étape 1 : Initialiser l’objet client

Quand vous utilisez la bibliothèque cliente de l’API Visage, la clé d’abonnement et le point de terminaison d’abonnement sont transmis par le biais du constructeur de la classe FaceServiceClient. Par exemple : 

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionRegion = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/";
FaceServiceClient FaceServiceClient = new FaceServiceClient(SubscriptionKey, SubscriptionRegion);
```

La clé d’abonnement avec le point de terminaison correspondant peut être obtenue à partir de la page Place de Marché du portail Azure.
Consultez la page sur les [abonnements](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Étape 2 : Migration de code

Cette section est axée uniquement sur la migration de l’implémentation de PersonGroup/FaceList vers LargePersonGroup/LargeFaceList. Bien que LargePersonGroup/LargeFaceList diffèrent de PersonGroup/FaceList en termes de conception et d’implémentation interne, les interfaces API sont similaires concernant la compatibilité descendante.

La migration des données n’étant pas prise en charge, vous devez recréer le LargePersonGroup/LargeFaceList.

### <a name="migrate-persongroup-to-largepersongroup"></a>Migrer PersonGroup vers LargePersonGroup

La migration de PersonGroup vers LargePersonGroup est simple car ils partagent exactement les mêmes opérations au niveau du groupe.

Pour l’implémentation liée à PersonGroup/Person, il est uniquement nécessaire de changer les chemins de l’API ou le module/la classe de SDK en veillant à sélectionner LargePersonGroup et LargePersonGroup Person.

Vous devez ajouter tous les visages et toutes les personnes du groupe PersonGroup au nouveau groupe LargePersonGroup. Pour référence, consultez [Comment ajouter des visages](how-to-add-faces.md).

### <a name="migrate-facelist-to-largefacelist"></a>Migrer FaceList vers LargeFaceList

| API FaceList | API LargeFaceList |
|:---:|:---:|
| Créer | Créer |
| Supprimer | Supprimer |
| Obtenir | Obtenir |
| Liste | Liste |
| Mettre à jour | Mettre à jour |
| - | Former |
| - | Obtenir l’état de l’entraînement |

Le tableau précédent est une comparaison des opérations au niveau de la liste entre FaceList et LargeFaceList. Comme nous pouvons l’observer, LargeFaceList comporte de nouvelles opérations, Entraîner et Obtenir l’état de l’entraînement, par rapport à FaceList. La LargeFaceList doit être entraînée avant l’opération [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), alors qu’aucun entraînement n’est nécessaire pour FaceList. L’extrait de code suivant est une fonction d’assistance pour attendre l’entraînement d’une LargeFaceList.

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

Auparavant, une utilisation typique de FaceList avec l’ajout de visages et FindSimilar se présentait ainsi :

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

Quand vous le migrez vers LargeFaceList, le code doit devenir le suivant :

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

Comme on peut le voir ci-dessus, la gestion des données et la partie FindSimilar sont presque identiques. La seule exception est qu’une nouvelle opération d’entraînement de pré-traitement doit se terminer dans la LargeFaceList avant que FindSimilar puisse fonctionne.

## <a name="step-3-train-suggestions"></a>Étape 3 : Suggestions d’entraînement

Bien que l’opération d’entraînement accélère [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) et [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), le temps de formation augmente, en particulier à grande échelle. Le tableau suivant fournit une estimation du temps d’entraînement à différentes échelles :

| Échelle (visages ou personnes) | Estimation du temps d’entraînement |
|:---:|:---:|
| 1 000 | 1-2 s |
| 10 000 | 5-10 s |
| 100 000 | 1 - 2 min |
| 1 000 000 | 10 - 30 min |

Pour mieux utiliser la fonctionnalité de grande échelle, nous vous recommandons d’appliquer certaines stratégies.

## <a name="step-31-customize-time-interval"></a>Étape 3.1 : Personnaliser l’intervalle de temps

Comme indiqué dans `TrainLargeFaceList()`, il existe un `timeIntervalInMilliseconds` afin de retarder le processus de vérification d’état d’entraînement infini. Pour LargeFaceList avec davantage de visages, l’utilisation d’un intervalle plus élevé permet de réduire le nombre et le coût des appels. L’intervalle de temps doit être personnalisé en fonction de la capacité attendue de la LargeFaceList.

La même stratégie s’applique également à LargePersonGroup. Par exemple, lors de l’entraînement d’un groupe LargePersonGroup comprenant 1 000 000 personnes, `timeIntervalInMilliseconds` peut être égal à 60 000 (un intervalle d’une minute).

## <a name="step-32-small-scale-buffer"></a>Étape 3.2 : Mémoire tampon à petite échelle

Il est possible d’effectuer des recherches de personnes/visages dans LargePersonGroup/LargeFaceList uniquement après son entraînement. Dans un scénario dynamique, de nouvelles personnes et nouveaux visages sont ajoutés constamment et doivent être immédiatement interrogeables, mais l’entraînement peut être plus long qu’on ne le souhaiterait. Pour atténuer ce problème, vous pouvez utiliser un LargePersonGroup/LargeFaceList supplémentaire à petite échelle en guise de mémoire tampon uniquement pour les entrées qui viennent d’être ajoutées. Cette mémoire tampon est plus rapide à entraîner en raison de sa plus petite taille, et la recherche immédiate dans cette mémoire tampon temporaire devrait fonctionner. Utilisez cette mémoire tampon en combinaison avec un entraînement sur le LargePersonGroup/LargeFaceList maître en exécutant l’entraînement maître avec un intervalle plus élevé, par exemple au milieu de la nuit, et tous les jours.

Exemple de flux de travail :
1. Créez un LargePersonGroup/LargeFaceList maître (collection principale) et un LargePersonGroup/LargeFaceList mémoire tampon (collection de mémoire tampon). La collection de mémoire tampon est destinée uniquement aux personnes/visages nouvellement ajoutés.
1. Ajoutez de nouvelles personnes et de nouveaux visages à la collection principale et à la collection de mémoire tampon.
1. Entraînez uniquement la collection de mémoire tampon avec un court intervalle, afin de garantir la prise en compte des nouvelles entrées.
1. Appelez Identification/FindSimilar par rapport à la collection principale et à la collection de mémoire tampon, et fusionnez les résultats.
1. Quand la taille de la collection de mémoire tampon augmente jusqu’à un seuil ou un temps d’inactivité système, créez une nouvelle collection de mémoire tampon et déclenchez l’entraînement sur la collection principale.
1. Supprimez l’ancienne collection de mémoire tampon une fois l’entraînement sur la collection principale terminé.

## <a name="step-33-standalone-training"></a>Étape 3.3 : Entraînement autonome

Si une latence relativement longue est acceptable, il n’est pas nécessaire de déclencher l’opération d’entraînement juste après l’ajout de nouvelles données. Au lieu de cela, elle peut être séparée de la logique principale et déclenchée régulièrement. Cette stratégie convient aux scénarios dynamiques avec une latence acceptable, et peut être appliquée aux scénarios statiques afin de réduire encore davantage la fréquence d’entraînement.

Supposez qu’il existe une fonction `TrainLargePersonGroup` similaire à la `TrainLargeFaceList`. Une implémentation standard de l’entraînement autonome sur LargePersonGroup en appelant la classe [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) dans `System.Timers` serait :

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

## <a name="summary"></a>Résumé

Dans ce guide, vous avez découvert comment migrer le code PersonGroup/FaceList existant (pas des données) vers LargePersonGroup/LargeFaceList :

- LargePersonGroup et LargeFaceList fonctionnent comme PersonGroup et FaceList, sauf qu’une opération d’entraînement est exigée par LargeFaceList.
- Vous devez appliquer une stratégie d’entraînement appropriée pour la mise à jour dynamique des données d’un jeu de données à grande échelle.

## <a name="next-steps"></a>Étapes suivantes

Suivez un guide pratique pour savoir comment ajouter des visages à un groupe PersonGroup ou exécuter l’opération d’identification sur un groupe PersonGroup.

- [Comment ajouter des visages](how-to-add-faces.md)
- [How to identify faces in images](HowtoIdentifyFacesinImage.md) (Comment identifier les visages dans l’image)