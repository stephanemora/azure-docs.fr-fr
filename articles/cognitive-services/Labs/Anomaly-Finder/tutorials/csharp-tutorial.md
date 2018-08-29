---
title: Détection d’anomalies dans une application C# - Microsoft Cognitive Services | Microsoft Docs
description: Explorez une application C# qui utilise l’API Détection d’anomalies dans Microsoft Cognitive Services. Envoyez des points de données d’origine à l’API et obtenez la valeur attendue et des points d’anomalies.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 7d4f6a12c94620f447b5d6df4d7715d32eac2d98
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2018
ms.locfileid: "41936734"
---
# <a name="anomaly-detection-c-application"></a>Détection d’anomalies dans une application C#

Explorer une application Windows de base qui utilise une API Détection d’anomalie pour détecter les anomalies à partir de l’entrée. L’exemple envoie les données de série chronologique à l’API Détection d’anomalies avec votre clé d’abonnement, puis obtient tous les points d’anomalies et la valeur attendue pour chaque point de données à partir de l’API.

## <a name="prerequisites"></a>Prérequis

### <a name="platform-requirements"></a>Plateforme requise

L’exemple a été développé pour le .NET Framework à l’aide de [Visual Studio 2017 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs). 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>S’abonner à la Détection d’anomalies et obtenir une clé d’abonnement 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Obtenir et utiliser l’exemple

Vous pouvez cloner l’exemple d’application de Détection d’anomalies sur votre ordinateur à partir de [Github](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git). 
<a name="Step1"></a>
### <a name="install-the-example"></a>Installer l’exemple

Dans votre bureau GitHub, ouvrez Sample\AnomalyDetectionSample.sln.

<a name="Step2"></a>
### <a name="build-the-example"></a>Générer l’exemple

Appuyez sur Ctrl+Maj+B ou cliquez sur Générer dans le menu du ruban, puis sélectionnez Générer la solution.

<a name="Step3"></a>
### <a name="run-the-example"></a>Exécuter l’exemple

1. Une fois la génération terminée, appuyez sur **F5** ou cliquez sur **Démarrer** dans le menu du ruban pour exécuter l’exemple.
2. Localisez la fenêtre d’interface utilisateur Détection d’anomalies avec la zone d’édition de texte « {your_subscription_key} ».
3. Remplacez le fichier request.json, qui contient les exemples de données, par vos propres données, puis cliquez sur « Envoyer ». Microsoft reçoit les données que vous chargez et les utilise pour y détecter des points d’anomalies. Les données que vous chargez ne sont pas rendues persistantes dans le serveur de Microsoft. Pour détecter à nouveau le point d’anomalie, vous devez recharger les données.
4. Si les données sont correctes, vous trouverez le résultat de la détection d’anomalies dans le champ « Réponse ». Si une erreur se produit, les informations sur l’erreur s’afficheront également Réponse.

<a name="Review"></a>
### <a name="read-the-result"></a>Lire le résultat

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Réviser et apprendre

Maintenant que vous avez une application en cours d’exécution, examinons comment l’exemple d’application s’intègre à la technologie Cognitive Services. Cette étape vous permettra soit de poursuivre la création de cette application, soit de développer votre propre application à l’aide de la Détection d’anomalies Microsoft.

Cet exemple d’application utilise le point de terminaison d’API Restful de Détection d’anomalies.

En examinant l’utilisation de l’API Restful dans l’exemple d’application, regardons un extrait de code de **AnomalyDetectionClient.cs**. Le fichier contient des commentaires de code qui indiquent « KEY SAMPLE CODE STARTS HERE » et « KEY SAMPLE CODE ENDS HERE » pour vous aider à localiser les extraits de code reproduits ci-après.

```csharp
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

```
### <a name="request"></a>**Requête**
L’extrait de code ci-dessous montre comment utiliser le HttpClient pour soumettre votre clé d’abonnement et des points de données au point de terminaison de l’API Détection d'anomalies.

```csharp
    public async Task<string> Request(string baseAddress, string endpoint, string subscriptionKey, string requestData)
    {
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(baseAddress) })
        {
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Build the request content
            // ---------------------------------------------------------------------- 
            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Send the request content with POST method.
            // ---------------------------------------------------------------------- 
            var res = await client.PostAsync(endpoint, content);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------
            if (res.IsSuccessStatusCode)
            {
                return await res.Content.ReadAsStringAsync();
            }
            else
            {
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Référence d’API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
