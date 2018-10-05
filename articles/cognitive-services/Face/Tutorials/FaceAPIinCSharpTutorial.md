---
title: 'Tutoriel : Détecter et encadrer des visages dans une image - API Visage, C#'
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous créez une application Windows qui utilise l’API Visage pour détecter et encadrer des visages dans une image.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: nolachar
ms.openlocfilehash: 657c471761c36de5095763623210909308f55c2a
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162609"
---
# <a name="tutorial-create-a-wpf-app-to-detect-and-frame-faces-in-an-image"></a>Didacticiel : Créer une application WPF pour détecter et encadrer des visages dans une image

Dans ce didacticiel, vous créez une structure de présentation Windows d’une application (WPF) qui utilise le service de l’API Visage via sa bibliothèque de client .NET. L’application détecte les visages dans une image, trace un cadre autour de chaque visage et affiche une description du visage dans la barre d’état. L’exemple de code complet est disponible sur GitHub à l’adresse [Détecter et encadrer des visages dans une image sur Windows](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample).

![Capture d’écran affichant des visages détectés encadrés par des rectangles](../Images/getting-started-cs-detected.png)

Ce didacticiel vous explique les procédures suivantes :

> [!div class="checklist"]
> - Créer une application WPF
> - Installer la bibliothèque de client de l’API Visage
> - Utiliser la bibliothèque de client pour détecter les visages dans une image
> - Tracer un cadre autour de chaque visage détecté
> - Afficher une description du visage dans la barre d’état

## <a name="prerequisites"></a>Prérequis

- Vous avez besoin d’une clé d’abonnement pour exécuter l’exemple. Vous pouvez obtenir des clés d’abonnement d’essai à partir de la page [Essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
- N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/). Pour Visual Studio 2017, la charge de travail de développement d’une application de bureau .NET est nécessaire. Ce didacticiel utilise Visual Studio 2017 Community Edition.
- Le package NuGet de la bibliothèque de client [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview). Il n’est pas nécessaire de télécharger le package. Vous trouverez ci-dessous des instructions d’installation.

## <a name="create-the-visual-studio-solution"></a>Créer la solution Visual Studio

Suivez ces étapes pour créer un projet Windows d’application WPF.

1. Après avoir ouvert Visual Studio, dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.
   - Dans Visual Studio 2017, développez **Installé**, puis **Autres langages**. Sélectionnez **Visual C#**, puis **Application WPF (.NET Framework)**.
   - Dans Visual Studio 2015, développez **Installé**, puis **Modèles**. Sélectionnez **Visual C#**, puis **Application WPF**.
1. Nommez l’application **FaceTutorial**, puis cliquez sur **OK**.

## <a name="install-the-face-service-client-library"></a>Installer la bibliothèque de client de l’API Visage

Suivez ces instructions pour installer la bibliothèque de client.

1. Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet**, puis **Console du gestionnaire de package**.
1. Dans la **Console du gestionnaire de package**, collez le texte suivant, puis appuyez sur **entrée**.

    `Install-Package Microsoft.Azure.CognitiveServices.Vision.Face -Version 2.2.0-preview`

## <a name="add-the-initial-code"></a>Ajouter le code initial

### <a name="mainwindowxaml"></a>MainWindow.xaml

Ouvrez *MainWindow.xaml* (Conseil : échangez les volets à l’aide de l’**icône représentant une flèche haut/bas**) et remplacez le contenu par le code suivant. Ce code xaml est utilisé pour créer la fenêtre de l’interface utilisateur. Notez les gestionnaires d’événements, `FacePhoto_MouseMove` et `BrowseButton_Click`.

```xml
<Window x:Class="FaceTutorial.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="700" Width="960">
    <Grid x:Name="BackPanel">
        <Image x:Name="FacePhoto" Stretch="Uniform" Margin="0,0,0,50" MouseMove="FacePhoto_MouseMove" />
        <DockPanel DockPanel.Dock="Bottom">
            <Button x:Name="BrowseButton" Width="72" Height="20" VerticalAlignment="Bottom" HorizontalAlignment="Left"
                    Content="Browse..."
                    Click="BrowseButton_Click" />
            <StatusBar VerticalAlignment="Bottom">
                <StatusBarItem>
                    <TextBlock Name="faceDescriptionStatusBar" />
                </StatusBarItem>
            </StatusBar>
        </DockPanel>
    </Grid>
</Window>
```

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Ouvrez *MainWindow.xaml*, puis ouvrez *MainWindow.xaml.cs*, et remplacez le contenu par le code suivant. Ignorez les traits rouges ondulés de soulignement ; ils disparaîtront après la première génération.

Les deux premières lignes importent les espaces de noms de bibliothèque de client. Ensuite, le `FaceClient` est créé, en saisissant la clé d’abonnement, alors que la région Azure est définie le constructeur `MainWindow`. Les deux méthodes, `BrowseButton_Click` et `FacePhoto_MouseMove`, correspondent aux gestionnaires d’événements déclarés dans *MainWindow.xaml*.

`BrowseButton_Click` crée l’élément `OpenFileDialog`, ce qui permet à l’utilisateur de sélectionner une image jpg. L’image est lue et affichée dans la fenêtre principale. Le code restant de `BrowseButton_Click` et le code de `FacePhoto_MouseMove` sont insérés dans les étapes suivantes.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;

namespace FaceTutorial
{
    public partial class MainWindow : Window
    {
        // Replace <SubscriptionKey> with your valid subscription key.
        // For example, subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // Replace or verify the region.
        //
        // You must use the same region as you used to obtain your subscription
        // keys. For example, if you obtained your subscription keys from the
        // westus region, replace "westcentralus" with "westus".
        //
        // NOTE: Free trial subscription keys are generated in the westcentralus
        // region, so if you are using a free trial subscription key, you should
        // not need to change this region.
        private const string faceEndpoint =
            "https://westcentralus.api.cognitive.microsoft.com";

        private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });

        IList<DetectedFace> faceList;   // The list of detected faces.
        String[] faceDescriptions;      // The list of descriptions for the detected faces.
        double resizeFactor;            // The resize factor for the displayed image.

        public MainWindow()
        {
            InitializeComponent();

            if (Uri.IsWellFormedUriString(faceEndpoint, UriKind.Absolute))
            {
                faceClient.Endpoint = faceEndpoint;
            }
            else
            {
                MessageBox.Show(faceEndpoint,
                    "Invalid URI", MessageBoxButton.OK, MessageBoxImage.Error);
                Environment.Exit(0);
            }
        }

        // Displays the image and calls UploadAndDetectFaces.
        private async void BrowseButton_Click(object sender, RoutedEventArgs e)
        {
            // Get the image file to scan from the user.
            var openDlg = new Microsoft.Win32.OpenFileDialog();

            openDlg.Filter = "JPEG Image(*.jpg)|*.jpg";
            bool? result = openDlg.ShowDialog(this);

            // Return if canceled.
            if (!(bool)result)
            {
                return;
            }

            // Display the image file.
            string filePath = openDlg.FileName;

            Uri fileUri = new Uri(filePath);
            BitmapImage bitmapSource = new BitmapImage();

            bitmapSource.BeginInit();
            bitmapSource.CacheOption = BitmapCacheOption.None;
            bitmapSource.UriSource = fileUri;
            bitmapSource.EndInit();

            FacePhoto.Source = bitmapSource;
        }

        // Displays the face description when the mouse is over a face rectangle.
        private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
        {
        }
    }
}
```

### <a name="insert-your-subscription-key-and-verify-or-change-the-region"></a>Insérer votre clé d’abonnement et vérifier ou changer la région

- Recherchez la ligne suivante dans *MainWindow.xaml.cs* et remplacez `<Subscription Key>` par votre clé d’abonnement de l’API visage :

    ```csharp
    private const string subscriptionKey = "<SubscriptionKey>";
    ```

- Recherchez la ligne suivante dans *MainWindow.xaml.cs* et remplacez ou vérifiez la région Azure associée à votre clé d’abonnement :

    ```csharp
    private const string Endpoint =
        "https://westcentralus.api.cognitive.microsoft.com";
    ```

    Vérifiez que l’emplacement est le même que celui où vous avez obtenu votre clé d’abonnement. Par exemple, si vous avez obtenu vos clés d’abonnement à partir de la région **westus** remplacez la région `Westcentralus` par `Westus`.

    Si vous avez reçu vos clés d’abonnement à l’aide de la version d’évaluation gratuite, la région de vos clés est **westcentralus** et aucune modification n’est nécessaire.

### <a name="test-the-app"></a>Test de l'application

Appuyez sur **Démarrer** dans le menu pour tester votre application. Lorsque la fenêtre s’ouvre, cliquez sur **Parcourir** dans l’angle inférieur gauche. Une boîte de dialogue **Fichier ouvert** s’affiche, où vous pouvez parcourir et sélectionner une photo, ensuite affichée dans la fenêtre.

![Capture d’écran montrant l’image non modifiée des visages](../Images/getting-started-cs-ui.png)

## <a name="upload-an-image-to-detect-faces"></a>Charger une image pour détecter les visages

La façon la plus simple de détecter des visages consiste à appeler la méthode `FaceClient.Face.DetectWithStreamAsync` qui inclut la méthode API [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) pour charger l’image locale.

Insérez la méthode suivante dans la classe `MainWindow`, au-dessus de la méthode `FacePhoto_MouseMove`.

Une liste d’attributs de visage à analyser est créée et le fichier image soumis est lu dans un `Stream`. Les deux sont passés à l’appel `DetectWithStreamAsync`.

```csharp
// Uploads the image file and calls DetectWithStreamAsync.
private async Task<IList<DetectedFace>> UploadAndDetectFaces(string imageFilePath)
{
    // The list of Face attributes to return.
    IList<FaceAttributeType> faceAttributes =
        new FaceAttributeType[]
        {
            FaceAttributeType.Gender, FaceAttributeType.Age,
            FaceAttributeType.Smile, FaceAttributeType.Emotion,
            FaceAttributeType.Glasses, FaceAttributeType.Hair
        };

    // Call the Face API.
    try
    {
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            // The second argument specifies to return the faceId, while
            // the third argument specifies not to return face landmarks.
            IList<DetectedFace> faceList =
                await faceClient.Face.DetectWithStreamAsync(
                    imageFileStream, true, false, faceAttributes);
            return faceList;
        }
    }
    // Catch and display Face API errors.
    catch (APIErrorException f)
    {
        MessageBox.Show(f.Message);
        return new List<DetectedFace>();
    }
    // Catch and display all other errors.
    catch (Exception e)
    {
        MessageBox.Show(e.Message, "Error");
        return new List<DetectedFace>();
    }
}
```

## <a name="draw-rectangles-around-each-face"></a>Tracer des rectangles autour de chaque visage

Ajoutez le code pour tracer un rectangle autour de chaque visage détecté dans l’image.

Dans *MainWindow.xaml.cs*, ajoutez le modificateur `async` à la méthode `BrowseButton_Click`.

```csharp
private async void BrowseButton_Click(object sender, RoutedEventArgs e)
```

Ajoutez le code suivant à la fin de la méthode `BrowseButton_Click`, après la ligne `FacePhoto.Source = bitmapSource`.

La liste de visages détectés est remplie par l’appel à `UploadAndDetectFaces`. Ensuite, un rectangle est tracé autour de chaque visage, et l’image modifiée s’affiche dans la fenêtre principale.

```csharp
// Detect any faces in the image.
Title = "Detecting...";
faceList = await UploadAndDetectFaces(filePath);
Title = String.Format(
    "Detection Finished. {0} face(s) detected", faceList.Count);

if (faceList.Count > 0)
{
    // Prepare to draw rectangles around the faces.
    DrawingVisual visual = new DrawingVisual();
    DrawingContext drawingContext = visual.RenderOpen();
    drawingContext.DrawImage(bitmapSource,
        new Rect(0, 0, bitmapSource.Width, bitmapSource.Height));
    double dpi = bitmapSource.DpiX;
    resizeFactor = (dpi > 0) ? 96 / dpi : 1;
    faceDescriptions = new String[faceList.Count];

    for (int i = 0; i < faceList.Count; ++i)
    {
        DetectedFace face = faceList[i];

        // Draw a rectangle on the face.
        drawingContext.DrawRectangle(
            Brushes.Transparent,
            new Pen(Brushes.Red, 2),
            new Rect(
                face.FaceRectangle.Left * resizeFactor,
                face.FaceRectangle.Top * resizeFactor,
                face.FaceRectangle.Width * resizeFactor,
                face.FaceRectangle.Height * resizeFactor
                )
        );

        // Store the face description.
        faceDescriptions[i] = FaceDescription(face);
    }

    drawingContext.Close();

    // Display the image with the rectangle around the face.
    RenderTargetBitmap faceWithRectBitmap = new RenderTargetBitmap(
        (int)(bitmapSource.PixelWidth * resizeFactor),
        (int)(bitmapSource.PixelHeight * resizeFactor),
        96,
        96,
        PixelFormats.Pbgra32);

    faceWithRectBitmap.Render(visual);
    FacePhoto.Source = faceWithRectBitmap;

    // Set the status bar text.
    faceDescriptionStatusBar.Text =
        "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="describe-the-faces-in-the-image"></a>Décrire les visages dans l’image

Ajoutez la méthode suivante dans la classe `MainWindow`, au-dessous de la méthode `UploadAndDetectFaces`.

La méthode convertit les attributs de visage dans une chaîne décrivant le visage. Cette chaîne s’affiche quand le pointeur de la souris est sur le rectangle du visage.

```csharp
// Creates a string out of the attributes describing the face.
private string FaceDescription(DetectedFace face)
{
    StringBuilder sb = new StringBuilder();

    sb.Append("Face: ");

    // Add the gender, age, and smile.
    sb.Append(face.FaceAttributes.Gender);
    sb.Append(", ");
    sb.Append(face.FaceAttributes.Age);
    sb.Append(", ");
    sb.Append(String.Format("smile {0:F1}%, ", face.FaceAttributes.Smile * 100));

    // Add the emotions. Display all emotions over 10%.
    sb.Append("Emotion: ");
    Emotion emotionScores = face.FaceAttributes.Emotion;
    if (emotionScores.Anger >= 0.1f)
        sb.Append(String.Format("anger {0:F1}%, ", emotionScores.Anger * 100));
    if (emotionScores.Contempt >= 0.1f)
        sb.Append(String.Format("contempt {0:F1}%, ", emotionScores.Contempt * 100));
    if (emotionScores.Disgust >= 0.1f)
        sb.Append(String.Format("disgust {0:F1}%, ", emotionScores.Disgust * 100));
    if (emotionScores.Fear >= 0.1f)
        sb.Append(String.Format("fear {0:F1}%, ", emotionScores.Fear * 100));
    if (emotionScores.Happiness >= 0.1f)
        sb.Append(String.Format("happiness {0:F1}%, ", emotionScores.Happiness * 100));
    if (emotionScores.Neutral >= 0.1f)
        sb.Append(String.Format("neutral {0:F1}%, ", emotionScores.Neutral * 100));
    if (emotionScores.Sadness >= 0.1f)
        sb.Append(String.Format("sadness {0:F1}%, ", emotionScores.Sadness * 100));
    if (emotionScores.Surprise >= 0.1f)
        sb.Append(String.Format("surprise {0:F1}%, ", emotionScores.Surprise * 100));

    // Add glasses.
    sb.Append(face.FaceAttributes.Glasses);
    sb.Append(", ");

    // Add hair.
    sb.Append("Hair: ");

    // Display baldness confidence if over 1%.
    if (face.FaceAttributes.Hair.Bald >= 0.01f)
        sb.Append(String.Format("bald {0:F1}% ", face.FaceAttributes.Hair.Bald * 100));

    // Display all hair color attributes over 10%.
    IList<HairColor> hairColors = face.FaceAttributes.Hair.HairColor;
    foreach (HairColor hairColor in hairColors)
    {
        if (hairColor.Confidence >= 0.1f)
        {
            sb.Append(hairColor.Color.ToString());
            sb.Append(String.Format(" {0:F1}% ", hairColor.Confidence * 100));
        }
    }

    // Return the built string.
    return sb.ToString();
}
```

## <a name="display-the-face-description"></a>Afficher la description du visage

Remplacez la méthode `FacePhoto_MouseMove` par le code suivant.

Ce gestionnaire d’événements affiche la chaîne de description du visage lorsque le pointeur de la souris pointe sur le rectangle du visage.

```csharp
private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
{
    // If the REST call has not completed, return.
    if (faceList == null)
        return;

    // Find the mouse position relative to the image.
    Point mouseXY = e.GetPosition(FacePhoto);

    ImageSource imageSource = FacePhoto.Source;
    BitmapSource bitmapSource = (BitmapSource)imageSource;

    // Scale adjustment between the actual size and displayed size.
    var scale = FacePhoto.ActualWidth / (bitmapSource.PixelWidth / resizeFactor);

    // Check if this mouse position is over a face rectangle.
    bool mouseOverFace = false;

    for (int i = 0; i < faceList.Count; ++i)
    {
        FaceRectangle fr = faceList[i].FaceRectangle;
        double left = fr.Left * scale;
        double top = fr.Top * scale;
        double width = fr.Width * scale;
        double height = fr.Height * scale;

        // Display the face description if the mouse is over this face rectangle.
        if (mouseXY.X >= left && mouseXY.X <= left + width &&
            mouseXY.Y >= top  && mouseXY.Y <= top + height)
        {
            faceDescriptionStatusBar.Text = faceDescriptions[i];
            mouseOverFace = true;
            break;
        }
    }

    // String to display when the mouse is not over a face rectangle.
    if (!mouseOverFace)
        faceDescriptionStatusBar.Text =
            "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="run-the-app"></a>Exécution de l'application

Exécutez cette application et recherchez une image contenant un visage. Patientez pendant quelques secondes pour laisser à l’API Visage le temps de répondre. Après cela, vous verrez un rectangle rouge sur les visages dans l’image. Quand vous déplacez la souris sur le rectangle du visage, la description du visage apparaît dans la barre d’état.

![Capture d’écran affichant des visages détectés encadrés par des rectangles](../Images/getting-started-cs-detected.png)

## <a name="summary"></a>Résumé

Dans ce didacticiel, vous avez appris le processus de base pour l’utilisation de la bibliothèque de client de l’API Visage, et créé une application pour afficher des visages encadrés dans une image.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la détection et l’utilisation des points de repère du visage.

> [!div class="nextstepaction"]
> [Comment détecter des visages dans une image](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
