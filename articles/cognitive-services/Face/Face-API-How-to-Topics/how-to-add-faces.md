---
title: Ajouter des visages avec l’API Visage | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Utilisez l’API Visage de Cognitive Services pour ajouter des visages dans des images.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 3306c13d6c3d231ddbda38cfcbc5419fcdbd30db
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367969"
---
# <a name="how-to-add-faces"></a>Comment ajouter des visages

Ce guide présente la meilleure pratique pour ajouter un nombre important de personnes et de visages à un groupe PersonGroup.
La même stratégie s’applique également à FaceList et LargePersonGroup.
Les exemples sont écrits en C# à l’aide de la bibliothèque de client de l’API Visage.

## <a name="step1"></a> Étape 1 : Initialisation

Plusieurs variables sont déclarées et une fonction d’assistance est implémentée pour planifier les requêtes.

- `PersonCount` correspond au nombre total de personnes.
- `CallLimitPerSecond` correspond au nombre maximal d’appels par seconde en fonction du niveau d’abonnement.
- `_timeStampQueue` est une file d'attente qui permet d’enregistrer les horodatages des requêtes.
- `await WaitCallLimitPerSecondAsync()` attend jusqu'à ce qu’il puisse envoyer la requête suivante.

```CSharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step2"></a> Étape 2 : Autoriser l’appel d’API

Quand vous utilisez une bibliothèque de client, la clé d’abonnement est transmise par le biais du constructeur de la classe FaceServiceClient. Par exemple : 

```CSharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

Vous pouvez obtenir la clé d’abonnement à partir de la page Place de marché de votre Portail Azure. Consultez la page sur les [abonnements](https://www.microsoft.com/cognitive-services/en-us/sign-up).

## <a name="step3"></a> Étape 3 : Créer le groupe PersonGroup

Un groupe PersonGroup nommé « MyPersonGroup » est créé pour enregistrer les personnes.
L’heure de la requête est empilée dans `_timeStampQueue` pour garantir la validation globale.

```CSharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step4"></a> Étape 4 : Créer les personnes dans le groupe PersonGroup

Les personnes sont créées en même temps et `await WaitCallLimitPerSecondAsync()` est également appliqué pour éviter de dépasser la limite de l’appel.

```CSharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step5"></a> Étape 5 : Ajouter des visages aux personnes

L’ajout de visages à différentes personnes est traité simultanément, alors que pour une personne spécifique, le traitement est séquentiel.
Là encore, `await WaitCallLimitPerSecondAsync()` est appelé afin de garantir que la fréquence de requêtes se trouve dans l’étendue de la limite.

```CSharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceServiceClient.AddPersonFaceAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a> Résumé

Dans ce guide, vous avez appris le processus de création d’un groupe PersonGroup avec un nombre important de personnes et de visages. Plusieurs rappels :

- Cette stratégie s’applique également à FaceList et LargePersonGroup.
- L’ajout/la suppression de visages pour différents FaceLists ou Persons dans LargePersonGroup peuvent être traités simultanément.
- Des opérations similaires pour une FaceList ou Person spécifique dans LargePersonGroup doivent être effectuées de façon séquentielle.
- Pour des raisons de simplicité, le traitement des exceptions éventuelles n’est pas abordé dans ce guide. Si vous souhaitez davantage de fiabilité, une stratégie de nouvelles tentatives adéquate doit être appliquée.

Voici un rappel rapide des fonctionnalités précédemment expliquées et montrées :

- Création de groupes PersonGroups à l’aide de l’API [PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
- Création de personnes à l’aide de l’API [PersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
- Ajout de visages aux personnes à l’aide de l’API [PersonGroup Person - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)

## <a name="related"></a> Rubriques connexes
- [How to identify faces in images](HowtoIdentifyFacesinImage.md) (Comment identifier les visages dans l’image)
- [How to Detect Faces in Image](HowtoDetectFacesinImage.md) (Comment détecter des visages dans l’image)
- [How to use the large-scale feature](how-to-use-large-scale.md) (Comment utiliser la fonctionnalité à grande échelle)
