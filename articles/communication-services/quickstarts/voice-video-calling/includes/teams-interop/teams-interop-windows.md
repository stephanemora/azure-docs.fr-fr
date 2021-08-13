---
title: Démarrage rapide – Rejoindre une réunion Teams à partir d’une application Windows
description: Ce tutoriel explique comment rejoindre une réunion Teams à l’aide du Kit de développement logiciel (SDK) Appel Azure Communication Services Calling pour Windows
author: aurighet
ms.author: aurighet
ms.date: 05/13/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 42735d13eb4a251a702bd486e21f56981e63326f
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112536123"
---
Ce guide de démarrage rapide explique comment rejoindre une réunion Teams à l’aide du Kit de développement logiciel (SDK) Appel Azure Communication Services pour Windows.

## <a name="prerequisites"></a>Prérequis

- Une [application Windows opérationnelle appelant Communication Services](../../getting-started-with-calling.md).
- Un [déploiement de Teams](/deployoffice/teams-install)

## <a name="add-the-teams-ui-controls-and-enable-the-teams-ui-controls"></a>Ajouter les contrôles d’interface utilisateur Teams et les activer

Remplacez le code dans MainPage.xaml par l’extrait de code suivant. La zone de texte servira à entrer le contexte de la réunion Teams, tandis que le bouton permettra de rejoindre la réunion spécifiée :

```xml
<Page
    x:Class="CallingQuickstart.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:CallingQuickstart"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <StackPanel>
        <TextBox PlaceholderText="Please enter the Teams meeting link." TextWrapping="Wrap" x:Name="TeamsLinkTextBox" Margin="10,10,10,10" />
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Center">
            <Button Content="Join Teams Meeting" Click="JoinButton_ClickAsync" x:Name="JoinButton" Margin="10,10,10,10" />
            <Button Content="Leave Meeting" Click="LeaveButton_ClickAsync" x:Name="LeaveButton" Margin="10,10,10,10" />
        </StackPanel>
        <TextBlock TextWrapping="Wrap" x:Name="CallStatusTextBlock" Margin="10,10,10,10" />
        <TextBlock TextWrapping="Wrap" x:Name="RecordingStatusTextBlock" Margin="10,10,10,10" />
    </StackPanel>
</Page>
```

## <a name="enable-the-teams-ui-controls"></a>Activer les contrôles d’interface utilisateur de Teams

Remplacez le contenu de `MainPage.xaml.cs` par l’extrait de code suivant :

```csharp
using System;
using System.Threading.Tasks;

using Windows.UI.Core;
using Windows.UI.Popups;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

using Azure.Communication;
using Azure.Communication.Calling;

namespace CallingQuickstart
{
    public sealed partial class MainPage : Page
    {
        string user_token_ = "<User_Access_Token>";

        Call call_;

        public MainPage()
        {
            this.InitializeComponent();
        }

        private async void JoinButton_ClickAsync(object sender, RoutedEventArgs e)
        {
            if (!await ValidateInput())
            {
                return;
            }

            //
            //  Create CallClient
            //
            CallClient call_client = new CallClient();

            //
            //  Create CallAgent
            //
            CommunicationTokenCredential token_credential;
            CallAgent call_agent;

            try
            {
                token_credential = new CommunicationTokenCredential(user_token_);

                CallAgentOptions call_agent_options = new CallAgentOptions();
                call_agent = await call_client.CreateCallAgent(token_credential, call_agent_options);
            }
            catch
            {
                await new MessageDialog("It was not possible to create call agent. Please check if token is valid.").ShowAsync();
                return;
            }

            //
            //  Join a Teams meeting
            //
            try
            {
                JoinCallOptions joinCallOptions = new JoinCallOptions();
                TeamsMeetingLinkLocator teamsMeetingLinkLocator = new TeamsMeetingLinkLocator(TeamsLinkTextBox.Text);
                call_ = await call_agent.JoinAsync(teamsMeetingLinkLocator, joinCallOptions);
            }
            catch
            {
                await new MessageDialog("It was not possible to join the Teams meeting. Please check if Teams Link is valid.").ShowAsync();
                return;
            }

            //
            //  Set up call callbacks
            //
            call_.OnStateChanged += Call_OnStateChangedAsync;
            call_.OnIsRecordingActiveChanged += Call_OnIsRecordingActiveChangedAsync;
        }

        private async void Call_OnStateChangedAsync(object sender, PropertyChangedEventArgs args)
        {
            await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                CallStatusTextBlock.Text = call_.State.ToString();
            });
        }
        
        private async void Call_OnIsRecordingActiveChangedAsync(object sender, PropertyChangedEventArgs args)
        {
            await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                RecordingStatusTextBlock.Text = call_.IsRecordingActive ? "Recording is active." : "Recording is inactive.";
            });
        }
        
        private async void LeaveButton_ClickAsync(object sender, RoutedEventArgs e)
        {
            try
            {
                await call_.HangUp(new HangUpOptions());
            }
            catch
            {
                await new MessageDialog("It was not possible to leave the Teams meeting.").ShowAsync();
            }
        }
        
        private async Task<bool> ValidateInput()
        {
            if (user_token_.StartsWith("<"))
            {
                await new MessageDialog("Please enter token in source code.").ShowAsync();
                return false;
            }
        
            if (TeamsLinkTextBox.Text.Trim().Length == 0 || !TeamsLinkTextBox.Text.StartsWith("http"))
            {
                await new MessageDialog("Please enter Teams meeting link.").ShowAsync();
                return false;
            }
        
            return true;
        }
    }
}
```

## <a name="get-the-teams-meeting-link"></a>Obtenir le lien de réunion Teams

Le lien de réunion Teams peut être récupéré par le biais des API Graph. Cette procédure est détaillée dans la [documentation de Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
Le kit SDK Communication Services Calling accepte un lien de réunion Teams complet. Ce lien est retourné dans le cadre de la ressource `onlineMeeting`, accessible sous la [propriété `joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). Vous pouvez également récupérer les informations de réunion requises à partir de l’URL **Rejoindre la réunion** disponible dans l’invitation à la réunion Teams.

## <a name="launch-the-app-and-join-teams-meeting"></a>Lancer l’application et rejoindre la réunion Teams

Vous pouvez générer et exécuter votre application sur Visual Studio en sélectionnant **Déboguer** > **Démarrer le débogage** ou à l’aide du raccourci clavier (F5).

Insérez le contexte Teams dans la zone de texte et appuyez sur *Join Teams Meeting* pour rejoindre la réunion Teams à partir de votre application Communication Services.
