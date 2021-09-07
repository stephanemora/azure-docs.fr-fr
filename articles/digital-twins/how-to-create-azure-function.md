---
title: Configurer une fonction dans Azure pour traiter des données
titleSuffix: Azure Digital Twins
description: Découvrez comment créer une fonction dans Azure qui peut accéder à des jumeaux numériques et être déclenchée par ceux-ci.
author: baanders
ms.author: baanders
ms.date: 7/14/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: eaa26b4682dc7e984c0c84575d9e87b5255f6c99
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114386970"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Connecter des applications de fonction dans Azure pour le traitement des données

Les jumeaux numériques peuvent être mis à jour en fonction des données à l’aide de [routes d’événements](concepts-route-events.md) via des ressources de calcul. Par exemple, une fonction créée à l’aide d’[Azure Functions](../azure-functions/functions-overview.md) peut mettre à jour un jumeau numérique en réponse à :
* Des données de télémétrie d’appareil à partir d’Azure IoT Hub.
* La modification d’une propriété ou autres données provenant d’un autre jumeau numérique au sein du graphe jumeau.

Cet article explique comment créer une fonction dans Azure en vue de l’utiliser avec Azure Digital Twins. Pour créer une fonction, vous devez suivre les étapes de base suivantes :

1. Créer un projet Azure Functions dans Visual Studio
2. Écrire une fonction qui a un déclencheur [Azure Event Grid](../event-grid/overview.md) .
3. Ajouter du code d’authentification à la fonction pour pouvoir accéder à Azure Digital Twins.
4. Publier l’application de fonction dans Azure
5. Configurer la [sécurité](concepts-security.md) pour l’application de fonction

## <a name="prerequisite-set-up-azure-digital-twins"></a>Condition préalable : Configurer Azure Digital Twins

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Créer une application de fonction dans Visual Studio

Pour obtenir des instructions sur la façon de créer une application de fonction à l’aide de Visual Studio, consultez [Développer des fonctions Azure Functions à l’aide de Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

## <a name="write-a-function-that-has-an-event-grid-trigger"></a>Écrire une fonction qui a un déclencheur Event Grid

Vous pouvez écrire une fonction en ajoutant le SDK à votre application de fonction. L’application de fonction interagit avec Azure Digital Twins à l’aide du [Kit de développement logiciel (SDK) Azure Digital Twins pour .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 

Pour utiliser le SDK, vous devez inclure les packages suivants dans votre projet. Installez les packages à l’aide du gestionnaire de package NuGet de Visual Studio. Ou ajoutez les packages à l’aide de `dotnet` dans un outil en ligne de commande.

* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure.Core](https://www.nuget.org/packages/Azure.Core/)

Ensuite, dans l’Explorateur de solutions Visual Studio, ouvrez le fichier  _.cs_ qui contient votre exemple de code. Ajoutez les instructions `using` suivantes pour les packages.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Ajouter du code d’authentification à la fonction

À présent, déclarez les variables au niveau de la classe, et ajoutez le code d’authentification qui permettra à la fonction d’accéder à Azure Digital Twins. Ajoutez les variables et le code à votre fonction.

* **Code permettant de lire l’URL du service Azure Digital Twins en tant que variable d’environnement.** Il est recommandé de lire l’URL du service à partir d’une variable d’environnement, plutôt que de la coder en dur dans la fonction. Vous définirez la valeur de cette variable d’environnement [plus loin dans cet article](#set-up-security-access-for-the-function-app). Pour plus d’informations sur les variables d’environnement, consultez [Gérer votre application de fonction](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal).

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* **Une variable statique destinée à contenir une instance HttpClient.** HttpClient est relativement coûteux à créer. Nous voulons donc éviter d’avoir à le créer pour chaque appel de fonction.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* **Informations d’identification de l’identité managée.**
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* **Une variable locale _DigitalTwinsClient_.** Ajoutez la variable à l'intérieur de votre fonction pour contenir votre instance de client Azure Digital Twins. Vous *ne devez pas* rendre cette variable statique dans votre classe.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* **Un contrôle de valeur Null pour _adtInstanceUrl_.** Ajoutez le contrôle de valeur Null, puis wrappez la logique de votre fonction dans un bloc try/catch pour intercepter toutes les exceptions.

Une fois ces modifications effectuées, le code de votre fonction doit ressembler à l’exemple suivant.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Maintenant que votre application est écrite, vous pouvez la publier sur Azure.

## <a name="publish-the-function-app-to-azure"></a>Publier l’application de fonction dans Azure

Pour obtenir des instructions sur la façon de publier une application de fonction, consultez [Développer des fonctions Azure Functions à l’aide de Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

### <a name="verify-the-publication-of-your-function"></a>Vérifier la publication de votre fonction

1. Connectez-vous avec vos informations d’identification dans le [portail Azure](https://portal.azure.com/).
2. Dans la zone de recherche en haut de la fenêtre, recherchez le nom de votre application de fonction, puis sélectionnez-la.

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Capture d’écran montrant le portail Azure. Dans le champ de recherche, entrez le nom de l’application de fonction." lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. Dans la page **Application de fonction** qui s’ouvre, choisissez **Fonctions** dans le menu sur la gauche. Si votre fonction a bien été publiée, son nom apparaît dans la liste.

    > [!Note] 
    > Vous devrez peut-être attendre quelques minutes ou actualiser la page avant que votre fonction s’affiche dans la liste des fonctions publiées.

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Capture d’écran montrant les fonctions publiées dans le portail Azure" lightbox="media/how-to-create-azure-function/view-published-functions.png":::

Pour accéder à Azure Digital Twins, votre application de fonction devra disposer d’une identité managée par le système avec les autorisations nécessaires pour accéder à votre instance d’Azure Digital Twins. Vous allez maintenant configurer cela.

## <a name="set-up-security-access-for-the-function-app"></a>Configurer l’accès de sécurité pour l’application de fonction

[!INCLUDE [digital-twins-configure-function-app.md](../../includes/digital-twins-configure-function-app.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez configuré une application de fonction dans Azure en vue d’une utilisation avec Azure Digital Twins. Dans la suite, vous allez voir comment créer une fonction de base pour [ingérer des données IoT Hub dans Azure Digital Twins](how-to-ingest-iot-hub-data.md).
