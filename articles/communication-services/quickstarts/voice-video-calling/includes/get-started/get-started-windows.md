---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: a85246e9c60af6363ddd59066f55941da04cfe1c
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112535871"
---
Ce démarrage rapide explique comment démarrer un appel à l’aide du Kit de développement logiciel (SDK) Appel d’Azure Communication Services pour Windows.

## <a name="sample-code"></a>Exemple de code

Vous pouvez télécharger l’exemple d’application sur [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/VoiceCalling).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous devez disposer des prérequis suivants :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installez [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) avec la charge de travail de développement pour la plateforme Windows universelle. 
- Une ressource Communication Services déployée. [Créer une ressource Communication Services](../../../create-communication-resource.md)
- Un [jeton d’accès utilisateur](../../../access-tokens.md) pour votre service Azure Communication

## <a name="setting-up"></a>Configuration

### <a name="creating-the-project"></a>Création du projet

Dans Visual Studio, créez un projet avec le modèle **Application vide (Windows universel)** pour configurer une application de plateforme Windows universelle (UWP) monopage.

:::image type="content" source="../../media/windows/create-a-new-project.png" alt-text="Capture d’écran montrant la fenêtre Nouveau projet dans Visual Studio.":::

### <a name="install-the-package"></a>Installer le package

Cliquez avec le bouton droit sur votre projet, puis accédez à `Manage Nuget Packages` pour installer `Azure.Communication.Calling`. 

### <a name="request-access"></a>Demander l'accès

Accédez à `Package.appxmanifest`, puis cliquez sur `Capabilities`.
Cochez la case `Internet (Client & Server)` pour obtenir un accès entrant et sortant à Internet. Cochez la case `Microphone` pour accéder au flux audio du microphone. 

:::image type="content" source="../../media/windows/request-access.png" alt-text="Capture d’écran montrant la demande d’accès à Internet et au microphone dans Visual Studio.":::

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

Nous devons configurer une disposition de base pour attacher notre logique. Afin de passer un appel sortant, nous avons besoin d’une `TextBox` pour fournir l’ID d’utilisateur de l’appelé. Nous avons également besoin d’un bouton `Start Call` et d’un bouton `Hang Up`. Ouvrez le fichier `MainPage.xaml` de votre projet et ajoutez le nœud `StackPanel` à votre `Page` : 

```C#
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
        <TextBox Text="Who would you like to call?" TextWrapping="Wrap" x:Name="CalleeTextBox" Margin="10,10,10,10"></TextBox>
        <Button Content="Start Call" Click="CallButton_ClickAsync" x:Name="CallButton" Margin="10,10,10,10"></Button>
        <Button Content="Hang Up" Click="HangupButton_Click" x:Name="HangupButton" Margin="10,10,10,10"></Button>
    </StackPanel>
</Page>
```

Ouvrez le fichier `MainPage.xaml.cs` et remplacez le contenu par l’implémentation suivante : 
```C#
using System;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

using Azure.Communication;
using Azure.Communication.Calling;

namespace CallingQuickstart
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.InitCallAgent();
        }
        
        private async void InitCallAgent()
        {
            // Create Call Client and initialize Call Agent
        }
        
        private async void CallButton_ClickAsync(object sender, RoutedEventArgs e)
        {
            // Start call
        }

        private async void HangupButton_Click(object sender, RoutedEventArgs e)
        {
            // End the current call
        }

        CallClient call_client_;
        CallAgent call_agent_;
        Call call_;
    }
}
```

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités du SDK Azure Communication Services Calling :

| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient est le point d’entrée principal du SDK Calling.|
| CallAgent | CallAgent sert à démarrer et à gérer les appels. |
| CommunicationTokenCredential | CommunicationTokenCredential est utilisé comme informations d’identification du jeton pour instancier CallAgent.| 
| CommunicationUserIdentifier | CommunicationUserIdentifier sert à représenter l’identité de l’utilisateur qui peut être l’une des suivantes : CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

## <a name="authenticate-the-client"></a>Authentifier le client

Initialisez une instance de `CallAgent` avec un jeton d’accès utilisateur qui nous permettra d’établir et de recevoir des appels. 

Dans le code suivant, remplacez `<USER_ACCESS_TOKEN>` par un jeton d’accès utilisateur. Consultez la documentation sur les [jetons d’accès utilisateur](../../../access-tokens.md) si vous n’avez pas encore de jeton disponible.

Ajoutez le code suivant à la fonction `InitCallAgent`. 

```C#
CommunicationTokenCredential token_credential = new CommunicationTokenCredential("<USER_ACCESS_TOKEN>");
call_client_ = new CallClient();

CallAgentOptions callAgentOptions = new CallAgentOptions()
{
    DisplayName = "<YOUR_DISPLAY_NAME>"
};
call_agent_ = await call_client_.CreateCallAgent(token_credential, callAgentOptions);
```

## <a name="start-a-call"></a>Démarrer un appel

Ajoutez l’implémentation au `CallButton_ClickAsync` pour démarrer un appel avec le `call_agent` que nous avons créé. 

```C#
StartCallOptions startCallOptions = new StartCallOptions();
ICommunicationIdentifier[] callees = new ICommunicationIdentifier[1]
{
    new CommunicationUserIdentifier(CalleeTextBox.Text)
};
call_ = await call_agent_.StartCallAsync(callees, startCallOptions);
```

## <a name="end-a-call"></a>Terminer un appel

Terminez l’appel en cours quand l’utilisateur clique sur le bouton `Hang Up`. 

```C#
private async void HangupButton_Click(object sender, RoutedEventArgs e)
{
    await call_.HangUpAsync(new HangUpOptions());
}
```

## <a name="run-the-code"></a>Exécuter le code

Vous pouvez générer et exécuter le code sur Visual Studio. Notez que, pour les plateformes de solution, nous prenons en charge `ARM64`, `x64` et `x86`. 

Vous pouvez passer un appel sortant en entrant un ID d’utilisateur dans le champ de texte, puis en cliquant sur le bouton `Start Call`. L’appel de `8:echo123` vous connecte à un bot d’écho, ce qui est parfait pour bien démarrer et vérifier que vos périphériques audio fonctionnent.

:::image type="content" source="../../media/windows/run-the-app.png" alt-text="Capture d’écran montrant l’exécution de l’application de démarrage rapide":::
