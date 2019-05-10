---
title: Créer une définition OpenAPI pour une fonction avec Gestion des API Azure
description: Créez une définition OpenAPI permettant aux autres applications et services d’appeler votre fonction dans Azure.
services: functions
keywords: OpenAPI, Swagger, applications cloud, services cloud
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: glenga
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 3ad304bc8f038d4009352dae72d70079828c26ba
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141555"
---
# <a name="create-an-openapi-definition-for-a-function-with-azure-api-management"></a>Créer une définition OpenAPI pour une fonction avec Gestion des API Azure

Les API REST sont souvent décrites à l’aide d’une définition OpenAPI. Cette définition contient des informations sur les opérations qui sont disponibles dans une API et sur la façon dont les données de demande et de réponse de l’API doivent être structurées.

Dans ce didacticiel, vous allez créer une fonction qui détermine si la réparation d’urgence d’une éolienne est rentable. Ensuite, vous allez créer une définition OpenAPI pour l’application de fonction en utilisant [Gestion des API Azure](../api-management/api-management-key-concepts.md) afin que la fonction puisse être appelée à partir d’autres applications et services.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une fonction dans Azure
> * Générer une définition OpenAPI à l’aide de Gestion des API Azure
> * Tester la définition en appelant la fonction

## <a name="create-a-function-app"></a>Créer une application de fonction

Vous devez disposer d’une Function App pour héberger l’exécution de vos fonctions. Une application de fonctions vous permet de regrouper des fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage des ressources.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>Création de la fonction

Ce didacticiel utilise une fonction déclenchée via HTTP qui accepte deux paramètres :

* Durée estimée pour réparer une éolienne réparer, exprimée en heures.
* La capacité de l’éolienne, exprimée en kilowatts. 

La fonction calcule ensuite le coût de la réparation et les revenus engendrés par 24 heures de fonctionnement de l’éolienne. Pour créer une fonction déclenchée via HTTP dans le [portail Azure](https://portal.azure.com).

1. Développez votre Function App, puis sélectionnez le bouton **+** en regard de **Fonctions**. Sélectionnez **Dans le portail** > **Continuer**.

1. Sélectionnez **Plus de modèles...**, puis **Terminer et afficher les modèles**.

1. Sélectionnez Déclencheur HTTP, tapez `TurbineRepair` comme **Nom** de fonction, choisissez `Function` comme **[Niveau d’authentification](functions-bindings-http-webhook.md#http-auth)**, puis sélectionnez **Créer**.  

    ![Créer une fonction HTTP pour OpenAPI](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Remplacez le contenu du fichier de script C# run.csx par le code suivant, puis choisissez **Enregistrer** :

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    Le code de cette fonction retourne un message `Yes` ou `No` indiquant si la réparation d’urgence est rentable, en comparant l’opportunité de revenus que représente la réparation de l’éolienne et le coût de la réparation.

1. Pour tester la fonction, cliquez sur **Test** tout à droite pour développer l’onglet de test. Entrez la valeur suivante pour le **corps de la requête**, puis cliquez sur **Exécuter**.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![Testez la fonction dans le portail Azure](media/functions-openapi-definition/test-function.png)

    La valeur suivante est retournée dans le corps de la réponse.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Vous disposez maintenant d’une fonction qui détermine la rentabilité des réparations d’urgence. Ensuite, vous générez une définition OpenAPI pour l’application de fonction.

## <a name="generate-the-openapi-definition"></a>Générer la définition OpenAPI

Vous êtes maintenant prêt à générer la définition OpenAPI.

1. Sélectionnez l’application de fonction, puis **Fonctionnalités de la plateforme**, **Tous les paramètres**.

    ![Tester la fonction dans le portail Azure](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Faites défiler la liste, puis choisissez **Gestion des API** > **Créer** pour créer une instance de Gestion des API.

    ![Lier la fonction](media/functions-openapi-definition/link-apim-openapi.png)

1. Utilisez les paramètres de Gestion des API de la manière spécifiée dans le tableau sous l’image.

    ![Créer un service de Gestion des API](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom** | Nom globalement unique | Un nom est généré sur la base du nom de votre application de fonction. |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel cette nouvelle ressource est créée. |  
    | **[Groupe de ressources](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Même ressource que votre application de fonction, qui doit être définie pour vous. |
    | **Lieu** | USA Ouest | Choisissez l’emplacement USA Ouest. |
    | **Nom de l’organisation** | Contoso | Nom de l’organisation utilisé dans le portail des développeurs et pour les notifications par e-mail. |
    | **E-mail de l’administrateur** | votre e-mail | E-mail ayant reçu les notifications système de la Gestion des API. |
    | **Niveau tarifaire** | Consommation (préversion) | Pour des informations tarifaires complètes, voir la [page de tarification de la Gestion des API](https://azure.microsoft.com/pricing/details/api-management/). |
    | **Application Insights** | Votre instance | Utilisez le service Application Insights qu’utilise votre application de fonction. |

1. Choisissez **Créer** pour créer l’instance de Gestion des API, ce qui peut prendre plusieurs minutes.

1. Sélectionnez **Activer Application Insights** pour envoyer des journaux au même emplacement que l’application de fonction, puis acceptez les valeurs par défaut restantes et sélectionnez **Lier l’API**.

1. La fenêtre **Importer Azure Functions** s’ouvre avec la fonction **TurbineRepair** en surbrillance. Cliquez sur **Sélectionner** pour continuer.

    ![Importer Azure Functions dans Gestion des API](media/functions-openapi-definition/import-function-openapi.png)

1. Dans la page **Créer à partir de Function App**, acceptez les valeurs par défaut, puis sélectionnez **Créer**.

    ![Créer à partir de Function App](media/functions-openapi-definition/create-function-openapi.png)

L’API est maintenant créé pour la fonction.

## <a name="test-the-openapi-definition"></a>Tester la définition OpenAPI

Avant d’utiliser la définition de l’API, vous devez vérifier qu’elle fonctionne.

1. Sous l’onglet **Test** de votre fonction, sélectionnez l’opération **POST**.

1. Entrez des valeurs pour les **heures** et la **capacité**.

```json
{
"hours": "6",
"capacity": "2500"
}
```

1. Cliquez sur **Envoyer**, puis affichez la réponse HTTP.

    ![Tester l’API de fonction](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer une fonction dans Azure
> * Générer une définition OpenAPI à l’aide de Gestion des API Azure
> * Tester la définition en appelant la fonction

Passez à la rubrique suivante pour découvrir la Gestion des API.

> [!div class="nextstepaction"]
> [Gestion des API](../api-management/api-management-key-concepts.md)
