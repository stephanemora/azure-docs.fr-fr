---
title: 'Exemple : Identifier les visages d’une image - API Visage'
titleSuffix: Azure Cognitive Services
description: Utilisez l’API Visage pour identifier les visages d’une image.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: a26f7d6057f92fd3ab92405ecca6965dbd6e37ad
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129069"
---
# <a name="example-how-to-identify-faces-in-images"></a>Exemple : Comment identifier des visages dans des images

Ce guide montre comment identifier des visages inconnus à l’aide de PersonGroups, groupes créés à partir de personnes connues à l’avance. Les exemples sont écrits en C# à l’aide de la bibliothèque de client de l’API Visage.

## <a name="concepts"></a>Concepts

Si vous n’êtes pas familiarisé avec les concepts suivants utilisés dans ce guide, recherchez les définitions dans notre [glossaire](../Glossary.md) à tout moment :

- Visage - Détecter
- Visage - Identifier
- PersonGroup

## <a name="preparation"></a>Préparation

Dans cet exemple, nous montrons ce qui suit :

- Comment créer un PersonGroup : un PersonGroup contient une liste de personnes connues.
- Comment attribuer un visage à chaque personne : ces visages servent de base pour identifier les personnes. Il est recommandé d’utiliser des visages de face clairs, comme sur une photo d’identité. Un bon jeu de photos doit contenir des visages d’une même personne dans diverses poses, avec des vêtements de couleurs différentes et des coiffures variées.

Pour effectuer la démonstration de cet exemple, vous devez préparer une série de photos :

- Quelques photos montrant le visage de la personne. [Cliquez ici pour télécharger des exemples de photos](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) pour Anna, Bill et Clare.
- Il s’agit d’une série de photos test, qui peuvent contenir ou non les visages d’Anna, de Bill ou de Clare utilisés pour tester l’identification. Vous pouvez également sélectionner des exemples d’images à partir du lien précédent.

## <a name="step-1-authorize-the-api-call"></a>Étape 1 : Autoriser l’appel d’API

Chaque appel de l’API Visage nécessite une clé d’abonnement. Cette clé peut être transmise via un paramètre de chaîne de requête, ou spécifiée dans l’en-tête de demande. Pour transmettre la clé d’abonnement dans une chaîne de requête, reportez-vous à l’URL de demande pour [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) en guise d’exemple :
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Une alternative consiste à spécifier la clé d’abonnement dans l’en-tête de demande HTTP : **ocp-apim-subscription-key: &lt;clé d’abonnement&gt;**. En cas d’utilisation d’une bibliothèque cliente, la clé d’abonnement est transmise dans le constructeur de la classe FaceServiceClient. Par exemple : 
 
```CSharp 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
Vous pouvez obtenir la clé d’abonnement à partir de la page Place de marché de votre Portail Azure. Consultez la page sur les [abonnements](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>Étape 2 : Créer le groupe PersonGroup

Dans cette étape, nous avons créé un groupe de personnes (PersonGroup) nommé « MyFriends » (MesAmis) contenant trois personnes : Anna, Bill et Clare. Plusieurs visages sont enregistrés pour chaque personne. Les visages doivent être détectés à partir des photos. À l’issue de toutes ces étapes, vous disposez d’un PersonGroup tel qu’illustré ci-dessous :

![HowToIdentify1](../Images/group.image.1.jpg)

### <a name="21-define-people-for-the-persongroup"></a>2.1 Définir les personnes du PersonGroup
Une personne est une unité d’identification de base. Une personne peut avoir un ou plusieurs visages enregistrés. Cependant, un groupe de personnes (PersonGroup) est une collection de personnes, chacune étant définie dans un PersonGroup particulier. L’identification est effectuée par rapport à un PersonGroup. Par conséquent, la tâche consiste à créer un PersonGroup, puis à créer des personnes dans celui-ci, telles que Anna, Bill et Clare.

Tout d’abord, vous devez créer un PersonGroup. Pour ce faire, utilisez l’API [PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244). L’API de bibliothèque de client correspondant est la méthode CreatePersonGroupAsync pour la classe FaceServiceClient. L’ID de groupe spécifié pour créer le groupe est unique pour chaque abonnement. Vous pouvez également obtenir, mettre à jour ou supprimer des PersonGroups à l’aide d’autres API PersonGroup. Une fois un groupe défini, vous pouvez définir des personnes dans celui-ci à l’aide de l’API [Personne de PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c). La méthode de la bibliothèque de client est CreatePersonAsync. Vous pouvez ajouter un visage à chaque personne créée.

```CSharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceServiceClient.CreatePersonGroupAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceServiceClient.CreatePersonAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step2-2"></a> 2.2 Détecter les visages et les enregistrer pour la personne appropriée
La détection est effectuée en envoyant une requête web « POST » à l’API [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) avec le fichier image dans le corps de la requête HTTP. Lorsque vous utilisez la bibliothèque de client, la détection des visages est exécutée via la méthode DetectAsync pour la classe FaceServiceClient.

Pour chaque visage détecté, vous pouvez appeler l’API [Personne PersonGroup - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) pour l’ajouter à la personne appropriée.

Le code suivant illustre le processus de détection d’un visage à partir d’une photo et d’ajout à une personne :
```CSharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceServiceClient.AddPersonFaceAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Notez que, si l’image contient plusieurs visages, seul le visage le plus grand est ajouté. Vous pouvez ajouter d’autres visages à la personne en passant une chaîne au format « targetFace = left, top, width, height » au paramètre de requête targetFace de l’API [Personne PersonGroup - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b), ou en utilisant le paramètre facultatif targetFace pour la méthode AddPersonFaceAsync afin d’ajouter d’autres visages. Chaque visage ajouté à la personne reçoit un Face ID unique persistant qui peut être utilisé dans les API [Personne PersonGroup - Supprimer le visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) et [Face - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Étape 3 : Entraîner PersonGroup

Avant d’utiliser le PersonGroup pour une identification, celui-ci doit être formé. Par ailleurs, il doit être formé à nouveau après l’ajout ou la suppression de toute personne, ou la modification du visage enregistré d’une personne. La formation est effectuée par l’API [PersonGroup - Former](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). Lorsque vous utilisez la bibliothèque de client, il s’agit simplement d’un appel à la méthode TrainPersonGroupAsync :
 
```CSharp 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
La formation est un processus asynchrone. Il n’est pas possible d’y mettre fin, même après que la méthode TrainPersonGroupAsync a renvoyé. Il se peut que vous deviez interroger le statut de formation à l’aide de l’API [PersonGroup - Obtenir le statut de formation](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) ou de la méthode GetPersonGroupTrainingStatusAsync de la bibliothèque de client. Le code suivant montre une logique simple d’attente d’achèvement de formation de PersonGroup :
 
```CSharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceServiceClient.GetPersonGroupTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != Status.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>Étape 4 : Identifier un visage par rapport à un PersonGroup défini

Lors de l’exécution d’identifications, l’API Visage calcule la similarité d’un visage test avec tous les visages d’un groupe, et renvoie la ou les personnes ressemblant le plus à ce visage test. Cette opération est effectuée à l’aide de l’API [Visage - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) ou de la méthode IdentifyAsync de la bibliothèque de client.

Le visage test doit être détecté dans le cadre des étapes précédentes. Le Face ID est ensuite transmis à l’API d’identification en tant que deuxième argument. Il est possible d’identifier plusieurs Face ID à la fois, et le résultat contient tous les résultats de l’identification. Par défaut, l’identification renvoie la personne correspondant le mieux au visage test. Si vous préférez, vous pouvez spécifier le paramètre facultatif maxNumOfCandidatesReturned pour permettre à l’identification de renvoyer davantage de candidats.

Le code suivant illustre le processus d’identification :

```CSharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceServiceClient.DetectAsync(s);
    var faceIds = faces.Select(face => face.FaceId).ToArray();
 
    var results = await faceServiceClient.IdentifyAsync(personGroupId, faceIds);
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
            var person = await faceServiceClient.GetPersonAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

Une fois ces étapes accomplies, vous pouvez essayer d’identifier d’autres visages et voir si les visages d’Anna, de Bill ou de Clare peuvent être correctement identifiés, en fonction des images chargées pour la détection des visages. Regardez les exemples suivants :

![HowToIdentify2](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Étape 5 : Faire une demande de grande échelle

Comme on le sait, un PersonGroup peut contenir jusqu’à 10 000 personnes en raison de la limitation de la conception précédente.
Pour plus d’informations sur les scénarios à l’échelle de plusieurs millions, voir [Comment utiliser la fonctionnalité à grande échelle](how-to-use-large-scale.md).

## <a name="summary"></a>Résumé

Dans ce guide, vous avez appris le processus de création d’un PersonGroup et d’identification d’une personne. Voici un rappel rapide des fonctionnalités précédemment expliquées et montrées :

- Détection de visages à l’aide de l’API [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d)
- Création de PersonGroups à l’aide de l’API [PersonGroup - créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
- Création de personnes à l’aide de l’API [Personne de PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
- Formation d’un PersonGroup à l’aide de l’API [PersonGroup - Former](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)
- Identification de visages inconnus par rapport au PersonGroup à l’aide de l’API [Visage - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)

## <a name="related-topics"></a>Rubriques connexes

- [How to Detect Faces in Image](HowtoDetectFacesinImage.md) (Comment détecter des visages dans l’image)
- [Comment ajouter des visages](how-to-add-faces.md)
- [Comment utiliser la fonctionnalité à grande échelle](how-to-use-large-scale.md)
