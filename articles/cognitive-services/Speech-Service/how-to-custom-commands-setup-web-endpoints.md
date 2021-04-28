---
title: Configurer des points de terminaison web
titleSuffix: Azure Cognitive Services
description: Configurer des points de terminaison web pour Commandes personnalisées
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 5f1d5318140dd14c5024e8dd3ad0def0afc7f378
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725905"
---
# <a name="set-up-web-endpoints"></a>Configurer des points de terminaison Web

Dans cet article, vous apprendrez à configurer des points de terminaison web dans une application Commandes personnalisées vous permettant d’effectuer des requêtes HTTP à partir d’une application cliente. Vous allez effectuer les étapes suivantes :

- Configurer des points de terminaison Web dans une application Commandes personnalisées
- Appeler des points de terminaison Web dans une application Commandes personnalisées
- Recevoir la réponse des points de terminaison Web
- Intégrer la réponse des points de terminaison Web dans une charge utile JSON personnalisée, l’envoyer, puis la visualiser dans une application cliente du Kit de développement logiciel (SDK) UWP C#

## <a name="prerequisites"></a>Prérequis

> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Clé d'abonnement Azure pour le service Speech : [Obtenez-en une gratuitement](overview.md#try-the-speech-service-for-free) ou créez-la sur le [portail Azure](https://portal.azure.com)
> * L’application Commandes personnalisées (voir [Créer un Assistant vocal à l’aide de Commandes personnalisées](quickstart-custom-commands-application.md))
> * Une application cliente compatible avec le SDK Speech (voir [Intégration à une application cliente à l’aide du SDK Speech](how-to-custom-commands-setup-speech-sdk.md))

## <a name="deploy-an-external-web-endpoint-using-azure-function-app"></a>Déployer un point de terminaison web externe à l’aide de l’application Azure Function

Pour ce tutoriel, vous avez besoin d’un point de terminaison HTTP qui gère les états de tous les appareils que vous configurez dans la commande **TurnOnOff** de votre application Commandes personnalisées.

Si vous disposez déjà d’un point de terminaison web que vous souhaitez appeler, passez à la [section suivante](#setup-web-endpoints-in-custom-commands). La section suivante fournit également des informations sur un point de terminaison web par défaut hébergé que vous pouvez utiliser si vous souhaitez ignorer cette section.

### <a name="input-format-of-azure-function"></a>Format d’entrée d’Azure Function

Ensuite, vous allez déployer un point de terminaison à l’aide d’[Azure Functions](../../azure-functions/index.yml).
Voici le format d’un événement Commandes personnalisées qui est passé à votre fonction Azure. Utilisez ces informations lorsque vous écrivez votre application Azure Function.

```json
{
  "conversationId": "string",
  "currentCommand": {
    "name": "string",
    "parameters": {
      "SomeParameterName": "string",
      "SomeOtherParameterName": "string"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "string",
      "SomeOtherGlobalParameterName": "string"
  }
}
```

    
Le tableau suivant liste les attributs de clé de cette entrée :
        
| Attribut | Explication |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | Identificateur unique de la conversation. Notez que cet ID peut être généré par l’application cliente. |
| **currentCommand** | Commande actuellement active dans la conversation. |
| **name** | Nom de la commande. L’attribut `parameters` est un mappage avec les valeurs actuelles des paramètres. |
| **currentGlobalParameters** | Mappage similaire à `parameters`, mais utilisé pour les paramètres globaux. |


Pour la fonction Azure **DeviceState**, un événement Commandes personnalisées ressemble à ce qui suit. Cela servira d’**entrée** à l’application de fonction.
    
```json
{
  "conversationId": "someConversationId",
  "currentCommand": {
    "name": "TurnOnOff",
    "parameters": {
      "item": "tv",
      "value": "on"
    }
  }
}
```

### <a name="azure-function-output-for-a-custom-command-app"></a>Sortie de la fonction Azure pour une application Commandes personnalisées

Si la sortie de votre fonction Azure est consommée par une application Commandes personnalisées, elle doit avoir le format suivant. Pour plus d’informations, consultez [Mettre à jour une commande à partir d’un point de terminaison web](./how-to-custom-commands-update-command-from-web-endpoint.md).

```json
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

### <a name="azure-function-output-for-a-client-application"></a>Sortie de la fonction Azure pour une application cliente

Si la sortie de votre fonction Azure est consommée par une application cliente, la sortie peut prendre n’importe quelle forme exigée par l’application cliente.

Pour notre point de terminaison **DeviceState**, la sortie de votre fonction Azure est consommée par une application cliente au lieu de l’application Commandes personnalisées. L’exemple de sortie de la fonction Azure doit ressembler à ce qui suit :
    
```json
{
  "TV": "on",
  "Fan": "off"
}
``` 

Cette sortie doit être écrite dans un stockage externe, afin que vous puissiez gérer l’état des appareils. L’état du stockage externe sera utilisé dans la section [Intégrer à l’application cliente](#integrate-with-client-application) ci-dessous.


### <a name="deploy-azure-function"></a>Déployez la fonction Azure.

Nous fournissons un exemple que vous pouvez configurer et déployer en tant qu’application Azure Functions. Si vous souhaitez créer un compte de stockage pour notre exemple, effectuez les étapes suivantes.
 
1. Créez un stockage Table pour enregistrer l’état de l’appareil. Dans le portail Azure, créez une ressource de type **Compte de stockage** avec le nom **devicestate**.
1. Copiez la valeur de la **chaîne de connexion** à partir de **devicestate -> Clés d’accès**. Vous devrez ajouter ce secret de chaîne à l’exemple de code d’application de fonction téléchargé.
1. Téléchargez l’exemple de [code d’application de fonction](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/main/custom-commands/quick-start).
1. Ouvrez la solution téléchargée dans Visual Studio 2019. Dans **Connections.js**, remplacez **STORAGE_ACCOUNT_SECRET_CONNECTION_STRING** par le secret de l’étape 2.
1.  Téléchargez le code de **DeviceStateAzureFunction**.

Pour déployer l’exemple d’application sur Azure Functions, effectuez les étapes suivantes.

1. [Déployez](../../azure-functions/index.yml) l’application Azure Functions.
1. Attendez que le déploiement se termine correctement, puis accédez à la ressource déployée sur le portail Azure. 
1. Sélectionnez **Fonctions** dans le volet de gauche, puis sélectionnez **DeviceState**.
1.  Dans la nouvelle fenêtre, sélectionnez **Code + test**, puis sélectionnez **Obtenir l’URL de la fonction**.
 
## <a name="setup-web-endpoints-in-custom-commands"></a>Configurer des points de terminaison web dans Commandes personnalisées

Nous allons maintenant connecter la fonction Azure à l’application Commandes personnalisées existante.
Dans cette section, vous allez utiliser le point de terminaison **DeviceState** par défaut. Si vous avez créé votre propre point de terminaison web à l’aide d’Azure Functions ou autre, utilisez-le à la place du `https://webendpointexample.azurewebsites.net/api/DeviceState` par défaut.

1. Ouvrez l'application Commandes personnalisées que vous avez précédemment créée.
1. Accédez à **Points de terminaison Web**, puis cliquez sur **Nouveau point de terminaison web**.

   > [!div class="mx-imgBorder"]
   > ![Nouveau point de terminaison Web](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Paramètre | Valeur suggérée | Description |
   | ------- | --------------- | ----------- |
   | Nom | UpdateDeviceState | Nom du point de terminaison Web. |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | URL du point de terminaison avec laquelle votre application Commandes personnalisées doit communiquer. |
   | Méthode | POST | Interactions autorisées (par exemple GET, POST) avec votre point de terminaison.|
   | headers | Clé : application, Valeur : prendre les 8 premiers chiffres de votre applicationId | Paramètres d’en-tête à inclure dans l’en-tête de la demande.|

    > [!NOTE]
    > - Exemple de point de terminaison web créé à l’aide d'[Azure Functions](../../azure-functions/index.yml), qui se connecte à la base de données enregistrant l’état du téléviseur et du ventilateur.
    > - L’en-tête suggéré n’est nécessaire que pour l’exemple de point de terminaison.
    > - Pour vérifier que la valeur de l’en-tête est unique dans l’exemple de point de terminaison, prenez les 8 premiers chiffres de votre **applicationId**.
    > - Dans le monde réel, le point de terminaison web peut être le point de terminaison du [hub IoT](../../iot-hub/about-iot-hub.md) qui gère vos appareils.

1. Cliquez sur **Enregistrer**.

## <a name="call-web-endpoints"></a>Appeler des points de terminaison Web

1. Accédez à la commande **TurnOnOff**, sélectionnez **ConfirmationResponse** dans la règle d’exécution, puis **Add an action**.
1. Sous **New Action-Type**, sélectionnez **Call web endpoint**
1. Dans **Edit Action - Endpoints**, sélectionnez **UpdateDeviceState**, qui correspond au point de terminaison Web que nous avons créé.  
1. Dans **Configuration**, entrez les valeurs suivantes :
   > [!div class="mx-imgBorder"]
   > ![Appeler les paramètres d’action des points de terminaison Web](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Paramètre | Valeur suggérée | Description |
   | ------- | --------------- | ----------- |
   | Points de terminaison | UpdateDeviceState | Point de terminaison Web que vous souhaitez appeler dans cette action. |
   | Paramètres de requête | item={SubjectDevice}&&value={OnOff} | Paramètres de requête à ajouter à l’URL du point de terminaison Web.  |
   | Type de contenu | N/A | Contenu du corps de la demande. |

    > [!NOTE]
    > - Les paramètres de la demande suggérés sont uniquement nécessaires pour l’exemple de point de terminaison

1. Dans **On Success - Action to execute**, sélectionnez **Send speech response**.

    Dans **Éditeur simple**, entrez `{SubjectDevice} is {OnOff}`.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran montrant l’écran « On Success – Action to execute » (« Réussite – Action à effectuer »).](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Paramètre | Valeur suggérée | Description |
   | ------- | --------------- | ----------- |
   | Action à exécuter | Envoyer une réponse vocale | Action à exécuter si la demande envoyée au point de terminaison Web a abouti |

   > [!NOTE]
   > - Vous pouvez également accéder directement aux champs de la réponse http en utilisant `{YourWebEndpointName.FieldName}`. Par exemple : `{UpdateDeviceState.TV}`

1. Dans **On Failure - Action to execute**, sélectionnez **Send speech response**

    Dans **Éditeur simple**, entrez `Sorry, {WebEndpointErrorMessage}`.

   > [!div class="mx-imgBorder"]
   > ![Action Appeler les points de terminaison Web en cas d’échec](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Paramètre | Valeur suggérée | Description |
   | ------- | --------------- | ----------- |
   | Action à exécuter | Envoyer une réponse vocale | Action à exécuter en cas d’échec de la demande sur le point de terminaison Web |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}` est facultatif. Vous êtes libre de supprimer cet élément si vous ne souhaitez pas afficher de message d’erreur.
   > - Dans notre exemple de point de terminaison, nous renvoyons une réponse HTTP avec des messages d’erreur détaillés pour les erreurs courantes telles que des paramètres d’en-tête manquants.

### <a name="try-it-out-in-test-portal"></a>Tester dans le portail test
- En cas de réponse de réussite, enregistrez, entraînez puis testez.
   > [!div class="mx-imgBorder"]
   > ![Capture d’écran montrant l’écran « On Success response » (« Réponse en cas de réussite »).](media/custom-commands/setup-web-endpoint-on-success-response.png)
- En cas de réponse d’échec, supprimez l’un des paramètres de requête, enregistrez, réentraînez puis testez.
   > [!div class="mx-imgBorder"]
   > ![Action Appeler les points de terminaison Web en cas de réussite](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Intégrer à l’application cliente

Dans [Envoyer une activité Commandes personnalisées à l’application cliente](./how-to-custom-commands-send-activity-to-client.md), vous avez ajouté une action **Envoyer l’activité au client**. L’activité est envoyée à l’application cliente, que l’action **Appeler le point de terminaison Web** ait réussi ou non.
Toutefois, en général, vous devez uniquement envoyer l’activité à l’application cliente lorsque l’appel au point de terminaison web réussit. Cet exemple illustre un scénario où l’état de l’appareil est correctement mis à jour.

1. Supprimez l’action **Envoyer l’activité au client** que vous avez ajoutée précédemment.
1. Modifiez le point de terminaison Web de l’appel :
    1. Dans **Configuration**, vérifiez que le paramètre **Query Parameters** affiche `item={SubjectDevice}&&value={OnOff}`
    1. Dans **On Success**, définissez **Action to execute** sur **Send activity to client**
    1. Copiez le fichier JSON ci-dessous dans **Contenu de l'activité**
   ```json
   {
      "type": "event",
      "name": "UpdateDeviceState",
      "value": {
        "state": "{OnOff}",
        "device": "{SubjectDevice}"
      }
    }
   ```
À présent, vous envoyez l’activité au client uniquement lorsque la demande au point de terminaison Web réussit.

### <a name="create-visuals-for-syncing-device-state"></a>Créer des éléments visuels pour la synchronisation de l’état de l’appareil

Ajoutez le code XML suivant à `MainPage.xaml` au-dessus du bloc **EnableMicrophoneButton**.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Synchroniser l’état de l’appareil

Dans `MainPage.xaml.cs`, ajoutez la référence `using Windows.Web.Http;`. Ajoutez le code suivant à la classe `MainPage` . Cette méthode enverra une requête GET à l’exemple de point de terminaison, puis extraira l’état actuel de l’appareil pour votre application. Veillez à modifier `<your_app_name>` selon ce que vous avez utilisé dans l’**en-tête** du point de terminaison web Commandes personnalisées.

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request.
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>Faire un essai

1. Lancez l’application.
1. Cliquez sur Synchroniser l’état de l’appareil. \
Si vous avez testé l’application avec `turn on tv` dans la section précédente, vous verrez que la télévision affiche l’état **on** (allumée).
    > [!div class="mx-imgBorder"]
    > ![Synchroniser l’état de l’appareil](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Sélectionnez **Activer le microphone**.
1. Sélectionnez le bouton **Parler**.
1. Dites `turn on the fan` (allumer le ventilateur). L’état visuel du ventilateur doit passer à **on**.
    > [!div class="mx-imgBorder"]
    > ![Activer le ventilateur](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Exporter une application Commandes personnalisées en tant que compétence distante](./how-to-custom-commands-integrate-remote-skills.md)
