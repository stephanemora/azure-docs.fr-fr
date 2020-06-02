---
title: Comment effectuer des commandes à partir d’un client avec le Kit de développement logiciel (SDK) Speech
titleSuffix: Azure Cognitive Services
description: Dans cet article, nous expliquons comment gérer des activités de commandes personnalisées sur un client avec le Kit de développement logiciel (SDK) Speech.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: trbye
ms.openlocfilehash: f11f5f3c2ad4c9f0241d34edeb664f739f88d15c
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871734"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Effectuer des commandes à partir d’un client avec le Kit de développement logiciel (SDK) Speech (préversion)

Pour effectuer des tâches à l’aide d’une application de commandes personnalisées, vous pouvez envoyer des charges utiles personnalisées à un appareil client connecté.

Dans cet article, vous allez :

- Définir et envoyer une charge utile JSON personnalisée à partir de votre application de commandes personnalisées
- Recevoir et visualiser le contenu de la charge utile JSON personnalisée d’une application cliente du Kit de développement logiciel (SDK) Speech UWP C#

## <a name="prerequisites"></a>Prérequis
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Clé d'abonnement Azure pour le service Speech : [Obtenez-en une gratuitement](get-started.md) ou créez-la sur le [portail Azure](https://portal.azure.com)
> * Application Commandes personnalisées créée précédemment : [Démarrage rapide : Créer une commande personnalisée avec Paramètres (préversion)](./quickstart-custom-speech-commands-create-parameters.md)
> * Application cliente compatible avec le kit de développement logiciel (SDK) Speech : [Démarrage rapide : Se connecter à une application Commandes personnalisées avec le kit de développement logiciel (SDK) Speech (préversion)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Facultatif : Démarrer rapidement

Cet article explique, étape par étape, comment créer une application cliente pour communiquer avec votre application de commandes personnalisées. Si vous préférez vous y plonger dès maintenant, le code source complet et prêt à compiler utilisé dans cet article est disponible dans les [exemples du Kit de développement logiciel (SDK) Speech](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Avec une charge utile JSON

1. Ouvrez l'application Commandes personnalisées que vous avez précédemment créée à partir du [Guide de démarrage rapide : Créer une commande personnalisée avec des paramètres](./quickstart-custom-speech-commands-create-parameters.md)
1. Consulter la section des **règles d’exécution** pour vous assurer que la règle créée précédemment répond à l’utilisateur
1. Pour envoyer une charge utile directement au client, créer une nouvelle règle avec une action Send Activity (Activité d’envoi)

   > [!div class="mx-imgBorder"]
   > ![Règle d’exécution Send Activity (Activité d’envoi)](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Paramètre | Valeur suggérée | Description |
   | ------- | --------------- | ----------- |
   | Nom de la règle | UpdateDeviceState | Nom décrivant l’objectif de la règle |
   | Conditions | Paramètre obligatoire : `OnOff` et `SubjectDevice` | Conditions déterminant le moment où la règle peut s’exécuter |
   | Actions | `SendActivity` (voir ci-dessous) | Action à exécuter lorsque la condition de la règle est remplie |

1. Copiez le fichier JSON ci-dessous dans **Contenu de l'activité**
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
   > [!div class="mx-imgBorder"]
   > ![Charge utile d’activité d’envoi](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

## <a name="create-visuals-for-device-on-or-off-state"></a>Créer des éléments visuels pour l’état activé ou désactivé de l’appareil

Dans [Démarrage rapide : Se connecter à une application Commandes personnalisées avec le kit de développement logiciel (SDK) Speech](./quickstart-custom-speech-commands-speech-sdk.md), vous avez créé une application cliente du kit de développement logiciel (SDK) Speech qui gérait des commandes telles que `turn on the tv`, `turn off the fan`. Avec l'ajout de quelques éléments visuels, vous pouvez voir le résultat de ces commandes.

Ajoutez des zones étiquetées avec du texte qui indique **Activé** ou **Désactivé** à l’aide du code XML suivant ajouté à `MainPage.xaml`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

## <a name="handle-customizable-payload"></a>Gérer la charge utile personnalisable
### <a name="add-reference-libraries"></a>Ajouter des bibliothèques de référence

Comme vous avez créé une charge utile JSON, vous devez ajouter une référence à la bibliothèque [JSON.NET](https://www.newtonsoft.com/json) pour gérer la désérialisation.
- Cliquez avec le bouton droit sur votre solution.
- Sélectionnez **Gérer les packages NuGet de la solution**, puis **Installer**. 
- Recherchez **Newtonsoft.json** dans la liste des mises à jour, puis mettez à jour **Microsoft.NETCore.UniversalWindowsPlatform** vers la dernière version.

> [!div class="mx-imgBorder"]
> ![Charge utile d’activité d’envoi](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

Dans « MainPage.xaml.cs », ajoutez
- `using Newtonsoft.Json;` 
- `using Windows.ApplicationModel.Core;`

### <a name="handle-received-payload"></a>Gérer la charge utile reçue

Dans `InitializeDialogServiceConnector`, remplacez le gestionnaire d'événements `ActivityReceived` par le code suivant. Le gestionnaire d'événements `ActivityReceived` modifié extraira la charge utile de l'activité et modifiera l'état visuel du téléviseur ou du ventilateur en conséquence.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.device != null ? activity.state.ToString() : string.Empty;
        var device = activity?.device != null ? activity.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Faites un essai

1. Lancer l’application
1. Sélectionner Activer le microphone
1. Cliquer sur le bouton Parler
1. Dire `turn on the tv`
1. L’état visuel du téléviseur doit passer à « Activé »
   > [!div class="mx-imgBorder"]
   > ![Charge utile d’activité d’envoi](media/custom-speech-commands/fulfill-sdk-turn-on-tv.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Procédure : ajouter des validations aux paramètres de commande personnalisée (préversion)](./how-to-custom-speech-commands-validations.md)
