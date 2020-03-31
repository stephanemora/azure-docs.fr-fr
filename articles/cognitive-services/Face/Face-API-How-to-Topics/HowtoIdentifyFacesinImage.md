---
title: 'Exemple : Identifier les visages dans une image - Visage'
titleSuffix: Azure Cognitive Services
description: Ce guide montre comment identifier des visages inconnus à l'aide d'objets PersonGroup créés à l'avance à partir de personnes connues.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 0b1cf99fe6e2aa4d7fcb12c3fb96b10b42c7c0b7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169911"
---
# <a name="example-identify-faces-in-images"></a>Exemple : Identifier des visages sur des images

Ce guide montre comment identifier des visages inconnus à l'aide d'objets PersonGroup créés à l'avance à partir de personnes connues. Les exemples sont écrits en C# à l’aide de la bibliothèque de client Visage d’Azure Cognitive Services.

## <a name="preparation"></a>Préparation

Cet exemple montre :

- comment créer un objet PersonGroup ; cet objet PersonGroup contient une liste de personnes connues.
- comment attribuer un visage à chaque personne ; ces visages servent de base pour identifier les personnes. Nous vous recommandons d'utiliser une vue de face bien nette des visages. Par exemple, une photo d'identité. Un bon jeu de photos contient des prises de vue sur lesquelles une même personne est représentée différemment (poses, tenues vestimentaires et coiffures différentes).

Pour cette démonstration, préparez :

- Quelques photos montrant le visage de la personne. [Téléchargez des exemples de photos](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) pour Anna, Bill et Clare.
- Une série de photos test. Les photos peuvent contenir ou non le visage d'Anna, de Bill ou de Clare. Elles sont utilisées pour tester l'identification. Sélectionnez également des exemples d'images à partir du lien précédent.

## <a name="step-1-authorize-the-api-call"></a>Étape 1 : Autoriser l’appel d’API

Chaque appel de l’API Visage nécessite une clé d’abonnement. Cette clé peut être transmise via un paramètre de chaîne de requête, ou spécifiée dans l'en-tête de la requête. Pour transmettre la clé d'abonnement par le biais d'une chaîne de requête, reportez-vous à l'URL de la requête de [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) en guise d'exemple :
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Vous pouvez également spécifier la clé d'abonnement dans l'en-tête de la requête HTTP **ocp-apim-subscription-key : &lt;Clé d'abonnement&gt;** .
Lorsque vous utilisez une bibliothèque de client, la clé d'abonnement est transmise par le biais du constructeur de la classe FaceClient. Par exemple :
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });
```
 
Pour obtenir la clé d'abonnement, accédez à la Place de marché Azure à partir du portail Azure. Pour plus d'informations, consultez [Abonnements](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>Étape 2 : Créer PersonGroup

Ici, un objet PersonGroup intitulé « MesAmis » contient Anna, Bill et Clare. Plusieurs visages sont enregistrés pour chaque personne. Les visages doivent être détectés à partir des images. À l’issue de toutes ces étapes, vous disposez d’un PersonGroup tel qu’illustré ci-dessous :

![MesAmis](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Étape 2.1 : Définir les personnes pour l'objet PersonGroup
Une personne est une unité d’identification de base. Une personne peut avoir un ou plusieurs visages enregistrés. Un objet PersonGroup est une collection de personnes. Chaque personne est définie dans un objet PersonGroup particulier. L'identification s'effectue par rapport à un objet PersonGroup. La tâche consiste à créer un objet PersonGroup, puis à créer des personnes dans celui-ci, comme Anna, Bill et Clare.

Commencez par créer un objet PersonGroup à l'aide de l'API [PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244). L’API de bibliothèque de client correspondant est la méthode CreatePersonGroupAsync pour la classe FaceClient. L'ID de groupe spécifié pour créer le groupe est propre à chaque abonnement. Vous pouvez également obtenir, mettre à jour ou supprimer des objets PersonGroup à l'aide d'autres API PersonGroup. 

Après avoir défini un groupe, vous pouvez y définir des personnes à l'aide de l'API [PersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c). La méthode de la bibliothèque de client est CreatePersonAsync. Vous pouvez ajouter un visage à chaque personne créée.

```csharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceClient.PersonGroupPerson.CreateAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step-22-detect-faces-and-register-them-to-the-correct-person"></a><a name="step2-2"></a> Étape 2.2 : Détecter les visages et les enregistrer pour la personne appropriée
La détection est effectuée en envoyant une requête web « POST » à l’API [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) avec le fichier image dans le corps de la requête HTTP. Lorsque vous utilisez la bibliothèque de client, la détection des visages s’effectue à l’aide d’une des méthodes DetectAsync de la classe FaceClient.

Pour chaque visage détecté, appelez l'API [PersonGroup Person - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) pour l'ajouter à la personne appropriée.

Le code suivant illustre le processus de détection d’un visage à partir d’une photo et d’ajout à une personne :

```csharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Si l'image contient plusieurs visages, seul le visage le plus grand est ajouté. Vous pouvez attribuer d'autres visages à la personne. Passez une chaîne au format « targetFace = left, top, width, height » au paramètre de requête targetFace de l'API [PersonGroup Person - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b). Vous pouvez également utiliser le paramètre facultatif targetFace de la méthode AddPersonFaceAsync pour ajouter d'autres visages. Chaque visage ajouté à la personne reçoit un ID de visage unique et persistant. Vous pouvez utiliser cet ID dans [PersonGroup Person - Supprimer un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) et [Visage - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Étape 3 : Entraîner le PersonGroup

Avant d'utiliser l'objet PersonGroup à des fins d'identification, vous devez le former. L'objet PersonGroup doit suivre une nouvelle formation après l'ajout ou la suppression d'une personne ou après la modification du visage enregistré d'une personne. La formation est effectuée par l’API [PersonGroup - Former](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). Lorsque vous utilisez la bibliothèque de client, il s'agit d'un appel à la méthode TrainPersonGroupAsync :
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
La formation est un processus asynchrone. Elle peut ne pas être terminée, même après le retour de la méthode TrainPersonGroupAsync. Vous serez peut-être amené à interroger l'état de la formation. Utilisez l'API [PersonGroup - Accéder à l'état de la formation](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) ou la méthode GetPersonGroupTrainingStatusAsync de la bibliothèque de client. Le code suivant illustre une logique simple consistant à attendre la fin de la formation de l'objet PersonGroup :
 
```csharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceClient.PersonGroup.GetTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != TrainingStatusType.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>Étape 4 : Identifier un visage par rapport à un PersonGroup défini

Lorsque le service Visage procède à des identifications, il calcule les similitudes entre un visage test et tous les visages d’un groupe. Elle renvoie les personnes les plus proches du visage test. Ce processus s'effectue à l'aide de l'API [Visage - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) ou de la méthode IdentifyAsync de la bibliothèque de client.

Le visage test doit être détecté en suivant les étapes précédentes. L'ID du visage est ensuite transmis à l'API d'identification comme deuxième argument. Plusieurs ID de visage peuvent être identifiés simultanément. Le résultat contient tous les résultats identifiés. Par défaut, le processus d'identification renvoie la personne la plus proche du visage test. Si vous préférez, spécifiez le paramètre facultatif maxNumOfCandidatesReturned pour permettre au processus d'identification de renvoyer davantage de candidats.

Le code suivant illustre le processus d'identification :

```csharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceClient.Face.DetectWithStreamAsync(s);
    var faceIds = faces.Select(face => face.FaceId.Value).ToArray();
 
    var results = await faceClient.Face.IdentifyAsync(faceIds, personGroupId);
    foreach (var identifyResult in results)
    {
        Console.WriteLine("Result of face: {0}", identifyResult.FaceId);
        if (identifyResult.Candidates.Length == 0)
        {
            Console.WriteLine("No one identified");
        }
        else
        {
            // Get top 1 among all candidates returned
            var candidateId = identifyResult.Candidates[0].PersonId;
            var person = await faceClient.PersonGroupPerson.GetAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

Au terme de cette procédure, essayez d'identifier différents visages. Voyez si les visages d'Anna, de Bill ou de Clare peuvent être correctement identifiés d'après les images chargées pour la détection des visages. Regardez les exemples suivants :

![Identifier différents visages](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Étape 5 : Demande à grande échelle

Un objet PersonGroup peut contenir jusqu'à 10 000 personnes en fonction de la limite de conception précédente.
Pour plus d’informations sur les scénarios à l’échelle de plusieurs millions, voir [Comment utiliser la fonctionnalité à grande échelle](how-to-use-large-scale.md).

## <a name="summary"></a>Résumé

Dans ce guide, vous avez appris à créer un objet PersonGroup et à identifier une personne. Les fonctionnalités suivantes ont été décrites et illustrées :

- Détection de visages à l'aide de l'API [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d)
- Création d'objets PersonGroups à l'aide de l'API [PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
- Création de personnes à l'aide de l'API [PersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
- Formation d'un objet PersonGroup à l'aide de l'API [PersonGroup - Former](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)
- Identification de visages inconnus par rapport à l'objet PersonGroup à l'aide de l'API [Visage - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)

## <a name="related-topics"></a>Rubriques connexes

- [Concepts de reconnaissance faciale](../concepts/face-recognition.md)
- [Détecter des visages sur une image](HowtoDetectFacesinImage.md)
- [Ajouter des visages](how-to-add-faces.md)
- [Utiliser la fonctionnalité à grande échelle](how-to-use-large-scale.md)
