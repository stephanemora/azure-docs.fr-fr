---
title: 'Tutoriel : Générer une application de liaison d’entités - C#'
titlesuffix: Azure Cognitive Services
description: Analysez du texte et liez des entités nommées aux entrées adéquates dans une base de connaissances à l’aide de l’API de liaison d’entités.
services: cognitive-services
author: DavidLiCIG
manager: nitinme
ms.service: cognitive-services
ms.subservice: entity-linking-intelligence
ms.topic: tutorial
ms.date: 07/06/2016
ms.author: davl
ms.openlocfilehash: fc1bdd5c6ad4829e22af9922c6749e60f842abaf
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594030"
---
# <a name="tutorial-build-an-entity-linking-app-with-c"></a>Tutoriel : Générer une application de liaison d’entités en C#

La liaison d'entités de Microsoft est un outil de traitement de langage naturel qui analyse du texte et lie des entités nommées aux entrées adéquates dans une base de connaissances. 

Ce didacticiel explore la liaison d'entités avec la bibliothèque de client de liaison d'entités comme package NuGet. 

### <a name="Prerequisites">Composants requis</a>

- Visual Studio 2015
- Clé API Microsoft Cognitive Services
- Obtenir la bibliothèque cliente et l’exemple
- Package NuGet Entity Linking de Microsoft

Vous pouvez télécharger la bibliothèque de client de l’API Entity Linking Intelligence Service via [SDK](https://www.github.com/microsoft/cognitive-entitylinking-windows). Le fichier zip téléchargé doit être extrait dans le dossier de votre choix ; de nombreux utilisateurs choisissent le dossier de Visual Studio 2015.

### <a name="step-1-subscribe-entity-linking-intelligence-service-and-get-your-own-key">Étape 1 : S’abonner à Entity Linking Intelligence Service et obtenir votre clé</a>
Avant d’utiliser le service Entity Linking Intelligence Service, vous devez vous inscrire pour obtenir une clé API. Consultez la page sur les [abonnements](https://www.microsoft.com/cognitive-services/en-us/sign-up). Les clés primaire et secondaire peuvent toutes deux être utilisées dans ce tutoriel.

### <a name="step-2-create-a-new-project-in-visual-studio"> Étape 2 : Créer un projet dans Visual Studio</a>

Commençons par créer un projet dans Visual Studio. Pour commencer, lancez Visual Studio 2015 depuis le Menu Démarrer. Ensuite, créez un projet en sélectionnant **Installé → Modèles → Visual C# → Windows universel → Application vide** pour le modèle de votre projet :

 ![Créer une application universelle](./Images/CreateUWP.png)

### <a name="step-3-add-the-entity-linking-nuget-package-to-your-project">Étape 3 : Ajouter le package NuGet de liaison d’entités à votre projet</a>

Le service de liaison d'entités de Cognitive Services est publié en tant que package NuGet.org et doit être installé avant de pouvoir l’utiliser.
Pour l’ajouter à votre package, allez dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur votre projet puis sélectionnez **Gérer les packages NuGet**.

D’abord, dans la fenêtre **Gestionnaire de package NuGet**, sélectionnez Nuget.org comme **source de package** dans le coin supérieur droit. Sélectionnez **Parcourir** dans le coin supérieur gauche et dans entrez « ProjectOxford.EntityLinking » dans la zone de recherche. Sélectionnez le package NuGet **Microsoft.ProjectOxford.EntityLinking** et cliquez sur **Installer**.

Cherchez ensuite le fichier Newtonsoft.Json et installez-le. Si vous êtes invité à examiner les modifications, cliquez sur **OK**. Si les termes du contrat de licence EntityLinking s’affichent, cliquez sur **J’accepte**.

La liaison d'entités est maintenant installée dans votre application. Elle a bien été installée si la référence ** Microsoft.ProjectOxford.EntityLinking** est présente dans votre projet dans l’Explorateur de solutions.

 ![Bibliothèque NuGet incluse dans le projet](./Images/NugetLibraryInProject.png)
 
### <a name="step-4-add-an-input-and-output-text-block-to-your-apps-xaml">Étape 4 : Ajouter un bloc de texte d’entrée et de sortie au fichier XAML de votre application</a>
Accédez au fichier **MainPage.xaml** dans l’**Explorateur de solutions**, puis double-cliquez sur le fichier pour l’ouvrir dans une nouvelle fenêtre. Pour des raisons pratiques, vous pouvez double-cliquez sur le bouton **XAML** dans l’onglet **Concepteur**. Ceci masque le **Concepteur visuel** et réserve toute la place disponible à l’affichage du code.

 ![Bibliothèque NuGet incluse dans le projet](./Images/UWPMainPage.png)
 
En tant que service textuel, le meilleur moyen de visualiser la fonctionnalité est de créer un bloc de texte d’entrée et de sortie. Pour ce faire, ajoutez le fichier XAML suivant dans la **Grille**. Ce code ajoute trois composants, une zone de texte d’entrée, une zone de texte de sortie et un bouton de démarrage.
 
 ```XAML
 <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="*"/>
        <RowDefinition Height="*" />
        <RowDefinition Height="50" />
    </Grid.RowDefinitions>
    <TextBox x:Name="inputBox" Grid.Row="0" TextWrapping="Wrap" Text="Enter a paragraph" Margin="10" AcceptsReturn="True" />
    <TextBlock x:Name="outputBlock" Grid.Row="1" TextWrapping="Wrap" Text="Result will be here" Margin="10" />
    <Button x:Name="button" Grid.Row="2" Content="Get Result" />
</Grid>
 ```
 
### <a name="step-5-proceed-to-add-entity-linking-intelligence-service">Étape 5 : Effectuer l’ajout de l’API Entity Linking Intelligence Service</a>
 
L’interface utilisateur est maintenant créée. Avant de pouvoir utiliser le service de liaison d'entités, nous devons ajouter le gestionnaire button-Click. Ouvrez le fichier **MainPage.xaml** depuis l’**Explorateur de solutions**. Ajoutez un gestionnaire button_Click à la fin du bouton.
 
 ```XAML
 <Button x:Name="button" Grid.Row="2" Content="Get Result" Click="button_Click" />
 ```
 
Un gestionnaire button-Click doit être implémenté dans le code. Ouvrez le fichier **MainPage.xaml.cs** depuis l’**Explorateur de solutions** pour implémenter le gestionnaire button-Click. Le fichier EntityLinkingServiceClient sert de wrapper pour récupérer les réponses de liaison d'entités. L’argument du constructeur de EntityLinkingServiceClient est la clé d’abonnement Cognitive Services. Collez la clé d’abonnement que vous avez obtenu à l’**Étape 1** pour appeler le service de liaison d'entités. 

Voici des exemples de code, qui ajoute la valeur « wikipediaId » à la réponse à l’aide du service de liaison d’entité. 
 
 ```csharp
 private async void button_Click(object sender, RoutedEventArgs e)
{
    var text = this.inputBox.Text;
    var client = new EntityLinkingServiceClient("Your subscription key","https://api.labs.cognitive.microsoft.com");
    var linkResponse = await client.LinkAsync(text);
    var result = string.Join(", ", linkResponse.Select(i => i.WikipediaID).ToList());
    this.outputBlock.Text = result;
}
 ```
 
Vous êtes maintenant prêt à exécuter votre première application Entity Linking de traitement de langage naturel. Appuyez sur **F5** pour compiler et lancer l’application. Collez les extraits de texte ou paragraphes dans les zones d’entrée. Appuyez sur le bouton « Obtenir résultat » et les entités identifiées s’affichent dans le bloc de sortie.
 
 ![Exemple de résultat UWP](./Images/DemoCodeResult.png)
 
### <a name="summary">Résumé</a>
 
Dans ce didacticiel, vous avez appris à créer une application pour exploiter la bibliothèque de client Entity Linking Intelligence Service en seulement quelques lignes de code C# et XAML. 

