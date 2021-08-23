---
title: 'Exemple : Utiliser la structure PersonDirectory - Visage'
titleSuffix: Azure Cognitive Services
description: Apprenez à utiliser la structure de données PersonDirectory pour stocker les données relatives aux visages et aux personnes en bénéficiant d'une plus grande capacité et de nouvelles fonctionnalités.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/22/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 70bbf20570c39fa59da9af7f7883aeef2e107df4
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633785"
---
# <a name="use-the-persondirectory-structure"></a>Utiliser la structure PersonDirectory

Pour effectuer des opérations de reconnaissance faciale, comme Identifier et Rechercher semblables, les clients de l'API Visage doivent créer une liste assortie d'objets **Person**. La nouvelle structure de données **PersonDirectory** contient des ID uniques, des chaînes de nom facultatives et des chaînes de métadonnées utilisateur facultatives pour chaque identité **Person** ajoutée à l'annuaire.

Actuellement, l'API Visage offre la structure **LargePersonGroup** qui comporte des fonctionnalités similaires, mais est limitée à 1 million d'identités. La structure **PersonDirectory** peut faire l'objet d'un scale-up pour atteindre 75 millions d'identités.

Une autre différence majeure entre **PersonDirectory** et les structures de données précédentes est que vous n'avez plus à passer d'appels d'apprentissage une fois que vous avez ajouté des visages à un objet **Person** &mdash; le processus de mise à jour est automatique.

## <a name="prerequisites"></a>Prérequis
* Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/cognitive-services/).
* Une fois que vous avez votre abonnement Azure, [créez une ressource Visage](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) dans le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
  * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Visage. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous.
  * Vous pouvez utiliser le niveau tarifaire Gratuit (F0) pour tester le service, puis passer par la suite à un niveau payant pour la production.

## <a name="add-persons-to-the-persondirectory"></a>Ajouter des objets Person à la structure PersonDirectory
L'objet **Person** est l'unité d'inscription de base dans la structure **PersonDirectory**. Une fois que vous avez ajouté un objet **Person** à l'annuaire, vous pouvez y ajouter un maximum de 248 images de visages par modèle de reconnaissance. Vous pouvez ensuite identifier des visages en les comparant à celles-ci à l'aide de différentes étendues.

### <a name="create-the-person"></a>Créer un objet Person
Pour créer un objet **Person**, vous devez appeler l'API **CreatePerson** et fournir un nom ou la valeur d'une propriété userData.

```csharp
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var addPersonUri = "https:// {endpoint}/face/v1.0-preview/persons";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example Person");
body.Add("userData", "User defined data");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(addPersonUri, content); 
}
```

L'API CreatePerson renvoie l'ID généré pour l'objet **Person** ainsi qu'un emplacement d'opération. Les données **Person** sont traitées de façon asynchrone. Vous devez donc utiliser l'emplacement d'opération pour extraire les résultats. 

### <a name="wait-for-asynchronous-operation-completion"></a>Attendre la fin de l'opération asynchrone
Vous devez interroger l'état de l'opération asynchrone à l'aide de la chaîne d'emplacement d'opération renvoyée pour vérifier la progression. 

Tout d'abord, vous devez définir un modèle de données comme celui présenté ci-dessous pour gérer la réponse d'état.

```csharp
[Serializable]
public class AsyncStatus
{
    [DataMember(Name = "status")]
    public string Status { get; set; }

    [DataMember(Name = "createdTime")]
    public DateTime CreatedTime { get; set; }

    [DataMember(Name = "lastActionTime")]
    public DateTime? LastActionTime { get; set; }

    [DataMember(Name = "finishedTime", EmitDefaultValue = false)]
    public DateTime? FinishedTime { get; set; }

    [DataMember(Name = "resourceLocation", EmitDefaultValue = false)]
    public string ResourceLocation { get; set; }

    [DataMember(Name = "message", EmitDefaultValue = false)]
    public string Message { get; set; }
}
```

À l'aide du message HttpResponseMessage ci-dessus, vous pouvez ensuite interroger l'URL et attendre les résultats.

```csharp
string operationLocation = response.Headers.GetValues("Operation-Location").FirstOrDefault();

Stopwatch s = Stopwatch.StartNew();
string status = "notstarted";
do
{
    if (status == "succeeded")
    {
        await Task.Delay(500);
    }

    var operationResponseMessage = await client.GetAsync(operationLocation);

    var asyncOperationObj = JsonConvert.DeserializeObject<AsyncStatus>(await operationResponseMessage.Content.ReadAsStringAsync());
    status = asyncOperationObj.Status;

} while ((status == "running" || status == "notstarted") && s.Elapsed < TimeSpan.FromSeconds(30));
```


Dès que l'état « succeeded » est renvoyé, l'objet **Person** est considéré comme ajouté à l'annuaire.

> [!NOTE]
> L'opération asynchrone de l'appel **CreatePerson** n'a pas besoin que l'état « succeeded » soit affiché pour que des visages y soient ajoutés, mais elle doit être terminée pour que l'objet **Person** soit ajouté à un objet **DynamicPersonGroup** (voir ci-dessous Créer et mettre à jour un objet **DynamicPersonGroup**) ou comparé lors d'un appel d'identification. Les appels de vérification fonctionneront immédiatement après l'ajout des visages à l'objet **Person**.


### <a name="add-faces-to-persons"></a>Ajouter des visages à des objets Person

Une fois que disposez de l'ID de **Person** transmis par l'appel de création d'une personne, vous pouvez ajouter un maximum de 248 images de visages à un objet **Person**, par modèle de reconnaissance. Spécifiez le modèle de reconnaissance (et éventuellement le modèle de détection) à utiliser dans l'appel, car les données de chaque modèle de reconnaissance seront traitées séparément dans la structure **PersonDirectory**.

Les modèles de reconnaissance actuellement pris en charge sont les suivants :
* `Recognition_02`
* `Recognition_03`
* `Recognition_04`

En outre, si l'image contient plusieurs visages, vous devez spécifier le rectangle englobant pour le visage correspondant à la cible prévue. Le code suivant ajoute des visages à un objet **Person**.

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

// Optional query strings for more fine grained face control
var queryString = "userData={userDefinedData}&targetFace={left,top,width,height}&detectionModel={detectionModel}";
var uri = "https://{endpoint}/face/v1.0-preview/persons/{personId}/recognitionModels/{recognitionModel}/persistedFaces?" + queryString;

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("url", "{image url}");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

Après l'appel d'ajout de visages, les données des visages sont traitées de façon asynchrone et vous devez attendre le succès de l'opération de la même manière que précédemment.

Une fois l'opération d'ajout des visages terminée, les données sont prêtes pour les appels d'identification.

## <a name="create-and-update-a-dynamicpersongroup"></a>Créer et mettre à jour un objet **DynamicPersonGroup**

Les objets **DynamicPersonGroup** sont des collections de références aux objets **Person** au sein d'une structure **PersonDirectory** ; ils permettent de créer des sous-ensembles de l'annuaire. Ils sont souvent utilisés pour réduire le nombre de faux positifs et accroître la précision d'une opération d'identification en limitant l'étendue aux seuls objets **Person** attendus. Parmi les cas d'usage pratiques figurent les annuaires permettant d'accéder à des bâtiments spécifiques au sein d'un campus ou d'une organisation plus vaste. L'annuaire de l'organisation peut contenir 5 millions d'individus, mais comme la recherche que vous souhaitez effectuer ne porte que sur les 800 personnes d'un bâtiment particulier, vous devez donc créer un objet **DynamicPersonGroup** contenant ces individus spécifiques. 

Si vous avez déjà utilisé un objet **PersonGroup**, notez qu'il existe deux différences majeures : 
* Chacun des objets **Person** contenus dans un objet **DynamicPersonGroup** fait référence à l'objet **Person** réel de la structure **PersonDirectory**, ce qui signifie qu'il n'est pas nécessaire de recréer un objet **Person** dans chaque groupe.
* Comme mentionné dans les sections précédentes, il n'est pas nécessaire de passer des appels d'apprentissage car les données des visages sont traitées automatiquement au niveau de l'annuaire.

### <a name="create-the-group"></a>Créer le groupe

Pour créer un objet **DynamicPersonGroup**, vous devez fournir un ID de groupe avec des caractères alphanumériques ou des tirets. Cet ID servira d'identificateur unique pour toutes les utilisations du groupe.

Il existe deux façons d'initialiser une collection de groupes. Vous pouvez créer un groupe vide et le remplir ultérieurement :

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/dynamicpersongroups/{dynamicPersonGroupId}";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example DynamicPersonGroup");
body.Add("userData", "User defined data");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PutAsync(uri, content);
}
```

Ce processus est immédiat et il n'est pas nécessaire d'attendre le succès des opérations asynchrones.

Vous pouvez également le créer avec un ensemble d'ID de **Person**, afin que ces références s'y trouvent dès le départ, en fournissant l'ensemble dans l'argument _AddPersonIds_ :

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/dynamicpersongroups/{dynamicPersonGroupId}";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example DynamicPersonGroup");
body.Add("userData", "User defined data");
body.Add("addPersonIds", new List<string>{"{guid1}", "{guid2}", …});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PutAsync(uri, content);

    // Async operation location to query the completion status from
    var operationLocation = response.Headers.Get("Operation-Location");
}
```

> [!NOTE]
> Dès que l'appel est traité, l'objet **DynamicPersonGroup** créé est prêt à être utilisé pour un appel d'identification, avec les références **Person** fournies dans le processus. L'état d'achèvement de l'ID d'opération renvoyé, en revanche, indique l'état de mise à jour de la relation personne-groupe.

### <a name="update-the-dynamicpersongroup"></a>Mettre à jour l'objet DynamicPersonGroup

Après la création initiale, vous pouvez ajouter et supprimer des références **Person** de l'objet **DynamicPersonGroup** à l'aide de l'API de mise à jour du groupe de personnes dynamique. Pour ajouter des objets **Person** au groupe, répertoriez les ID de **Person** dans l'argument _addPersonsIds_. Pour supprimer des objets **Person**, répertoriez-les dans l'argument _removePersonIds_. L'ajout et la suppression peuvent être effectués en un seul appel :

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/dynamicpersongroups/{dynamicPersonGroupId}";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example Dynamic Person Group updated");
body.Add("userData", "User defined data updated");
body.Add("addPersonIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("removePersonIds", new List<string>{"{guid1}", "{guid2}", …});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PatchAsync(uri, content);

    // Async operation location to query the completion status from
    var operationLocation = response.Headers.Get("Operation-Location");
}
```

Une fois l'appel traité, les mises à jour de la collection sont reflétées lors de l'interrogation du groupe. Comme avec l'API de création, l'opération renvoyée indique l'état de mise à jour de la relation personne-groupe pour tout objet **Person** impliqué dans la mise à jour. Vous n'avez pas besoin d'attendre la fin de l'opération pour passer d'autres appels de mise à jour au groupe.

## <a name="identify-faces-in-a-persondirectory"></a>Identifier des visages dans une structure PersonDirectory

Les utilisations les plus courantes des données des visages dans une structure **PersonDirectory** sont la comparaison des objets **Person** inscrits avec un visage donné et l'identification du candidat le plus probable auquel ce visage appartient. Plusieurs visages peuvent être fournis dans la demande, et chacun recevra son propre ensemble de résultats de comparaison dans la réponse.

Dans la structure **PersonDirectory**, chaque visage peut être identifié à partir de trois types d'étendues :

### <a name="scenario-1-identify-against-a-dynamicpersongroup"></a>Scénario 1 : Identification par rapport à un objet DynamicPersonGroup
 
La spécification de la propriété _dynamicPersonGroupId_ dans la demande compare le visage avec tous les objets **Person** référencés dans le groupe. Un seul objet **DynamicPersonGroup** peut être identifié dans un appel. 

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

// Optional query strings for more fine grained face control
var uri = "https://{endpoint}/face/v1.0-preview/identify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("dynamicPersonGroupId", "{dynamicPersonGroupIdToIdentifyIn}");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

### <a name="scenario-2-identify-against-a-specific-list-of-persons"></a>Scénario 2 : Identification par rapport à une liste spécifique de personnes

Vous pouvez également spécifier une liste d'ID de **Person** dans la propriété _personIds_ pour comparer le visage avec chacun d'entre eux.

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");
            
var uri = "https://{endpoint}/face/v1.0-preview/identify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("personIds", new List<string>{"{guid1}", "{guid2}", …});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

### <a name="scenario-3-identify-against-the-entire-persondirectory"></a>Scénario 3 : Identification par rapport à l'ensemble de la structure **PersonDirectory**

Le fait de fournir un seul astérisque dans la propriété _personIds_ de la demande permet de comparer le visage avec tous les objets **Person** inscrits dans la structure **PersonDirectory**. 
 

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");
            
var uri = "https://{endpoint}/face/v1.0-preview/identify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("personIds", new List<string>{"*"});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```
Pour les trois scénarios, l'identification ne compare le visage entrant qu'avec les visages pour lesquels l'appel AddPersonFace a renvoyé une réponse « succeeded ».

## <a name="verify-faces-against-persons-in-the-persondirectory"></a>Vérifier que des visages appartiennent à des personnes figurant dans la structure **PersonDirectory**

Avec un ID de visage renvoyé par un appel de détection, vous pouvez vérifier si le visage correspond à un objet **Person** inscrit dans la structure **PersonDirectory**. Spécifiez l'objet **Person** à l'aide de la propriété _personId_.

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/verify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceId", "{guid1}");
body.Add("personId", "{guid1}");
var jsSerializer = new JavaScriptSerializer();
byte[] byteData = Encoding.UTF8.GetBytes(jsSerializer.Serialize(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

La réponse contient une valeur booléenne indiquant si le service considère que le nouveau visage correspond au même objet **Person**, avec un score de confiance pour la prédiction.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide, vous avez appris à utiliser la structure **PersonDirectory** afin de stocker les données relatives aux visages et aux personnes de votre application Visage. Découvrez ensuite les meilleures pratiques à adopter pour ajouter les données de visage de vos utilisateurs.

* [Bonnes pratiques pour ajouter des utilisateurs](../enrollment-overview.md)
