---
title: 'Exemple : Ajouter des visages - API Visage'
titleSuffix: Azure Cognitive Services
description: Utilisez l’API Visage pour ajouter des visages à une image.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: f443eb13650483bc3ee63dad59cc40b8042bc35b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55222812"
---
# <a name="example-how-to-add-faces"></a>Exemple : Comment ajouter des visages

Ce guide présente la meilleure pratique pour ajouter un nombre important de personnes et de visages à un groupe PersonGroup.
La même stratégie s’applique également à FaceList et LargePersonGroup.
Les exemples sont écrits en C# à l’aide de la bibliothèque de client de l’API Visage.

## <a name="step-1-initialization"></a>Étape 1 : Initialisation

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

## <a name="step-2-authorize-the-api-call"></a>Étape 2 : Autoriser l’appel d’API

Quand vous utilisez une bibliothèque de client, la clé d’abonnement est transmise par le biais du constructeur de la classe FaceServiceClient. Par exemple : 

```CSharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

Vous pouvez obtenir la clé d’abonnement à partir de la page Place de marché de votre Portail Azure. Consultez la page sur les [abonnements](https://www.microsoft.com/cognitive-services/en-us/sign-up).

## <a name="step-3-create-the-persongroup"></a>Étape 3 : Créer PersonGroup

Un groupe PersonGroup nommé « MyPersonGroup » est créé pour enregistrer les personnes.
L’heure de la requête est empilée dans `_timeStampQueue` pour garantir la validation globale.

```CSharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-to-the-persongroup"></a>Étape 4 : Créer les personnes du PersonGroup

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

## <a name="step-5-add-faces-to-the-persons"></a>Étape 5 : Ajouter des visages aux personnes

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

## <a name="summary"></a>Résumé

Dans ce guide, vous avez appris le processus de création d’un groupe PersonGroup avec un nombre important de personnes et de visages. Plusieurs rappels :

- Cette stratégie s’applique également à FaceList et LargePersonGroup.
- L’ajout/la suppression de visages pour différents FaceLists ou Persons dans LargePersonGroup peuvent être traités simultanément.
- Des opérations similaires pour une FaceList ou Person spécifique dans LargePersonGroup doivent être effectuées de façon séquentielle.
- Pour des raisons de simplicité, le traitement des exceptions éventuelles n’est pas abordé dans ce guide. Si vous souhaitez davantage de fiabilité, une stratégie de nouvelles tentatives adéquate doit être appliquée.

Voici un rappel rapide des fonctionnalités précédemment expliquées et montrées :

- Création de groupes PersonGroups à l’aide de l’API [PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
- Création de personnes à l’aide de l’API [PersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
- Ajout de visages aux personnes à l’aide de l’API [PersonGroup Person - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)

## <a name="related-topics"></a>Rubriques connexes

- [How to identify faces in images](HowtoIdentifyFacesinImage.md) (Comment identifier les visages dans l’image)
- [How to Detect Faces in Image](HowtoDetectFacesinImage.md) (Comment détecter des visages dans l’image)
- [How to use the large-scale feature](how-to-use-large-scale.md) (Comment utiliser la fonctionnalité à grande échelle)
