---
title: Service connecté Visual Studio - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Connectez-vous à l’API Vision par ordinateur à partir d’une application web ASP.NET Core à l’aide de la fonctionnalité Service connecté Visual Studio.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: ghogen
ms.custom: seodec18
ms.openlocfilehash: 24d9a5fa1e9c2f44ef32ac1fc05ad09f8a550f12
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827748"
---
# <a name="use-connected-services-in-visual-studio-to-connect-to-the-computer-vision-api"></a>Utilisation des Services connectés dans Visual Studio pour se connecter à l’API Vision par ordinateur

Cet article et ceux qui l’accompagnent donnent des détails sur l’utilisation de la fonctionnalité Service connecté Visual Studio de l’API Vision par ordinateur Cognitive Services. Elle est disponible dans Visual Studio 2017 15.7 et les versions ultérieures, à condition que l’extension Cognitive Services soit installée.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’en possédez pas, vous pouvez vous inscrire pour créer dès aujourd’hui un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2017 version 15.7 ou ultérieure avec la charge de travail **Développement web** installée. [Téléchargez-le maintenant](https://visualstudio.microsoft.com/downloads/).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>Ajouter la prise en charge de l’API Vision par ordinateur Cognitive Services au projet

1. Créez un projet web ASP.NET Core. Utilisez le modèle de projet Vide. 

1. Dans **l’Explorateur de solutions**, choisissez **Ajouter** > **Service connecté**.
   La page Service connecté s’affiche et montre des services que vous pouvez ajouter à votre projet.

   ![Menu contextuel d’un projet Visual Studio : Ajouter > Service connecté](../media/vs-common/Connected-Service-Menu.PNG)

1. Dans le menu des services disponibles, choisissez **API Vision par ordinateur Cognitive Services**.

   ![Menu Services connectés : Analyser les images... est mis en surbrillance](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   Si vous êtes connecté à Visual Studio et si un abonnement Azure est associé à votre compte, une page s’affiche et montre la liste déroulante de vos abonnements.

   ![Fenêtre API Vision par ordinateur dans laquelle est encadrée la liste déroulante Abonnement](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. Sélectionnez l’abonnement que vous souhaitez utiliser. Ensuite, choisissez un nom pour l’API Vision par ordinateur ou cliquez sur le lien Modifier pour modifier le nom généré automatiquement. Choisissez le groupe de ressources et le niveau tarifaire.

   ![Modifier les informations du service connecté](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   Pour plus d’informations sur les niveaux tarifaires, suivez le lien.

1. Cliquez sur Ajouter pour ajouter la prise en charge du Service connecté.
   Visual Studio modifie le projet en ajoutant les packages NuGet, les entrées du fichier de configuration et autres afin de prendre en charge une connexion à l’API Vision par ordinateur. La fenêtre Sortie affiche le journal des événements relatifs au projet. Un résultat tel que celui-ci doit s’afficher :

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 2.1.0.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.0
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>Utiliser l’API Vision par ordinateur pour détecter les attributs d’une image

1. Ajoutez les instructions using suivantes dans Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Ajoutez un champ configuration, puis un constructeur qui l’initialise dans la classe `Startup` pour activer la configuration de votre programme.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Dans le dossier wwwroot de votre projet, ajoutez un dossier images et un fichier image. Par exemple, vous pouvez utiliser l’une des images de cette [page de l’API Vision par ordinateur](https://azure.microsoft.com/services/cognitive-services/computer-vision/). Cliquez avec le bouton droit sur une des images et enregistrez-la sur votre disque dur local. Ensuite, dans l’Explorateur de solutions, cliquez avec le bouton droit sur le dossier des images et choisissez **Ajouter** > **Élément existant** pour l’ajouter à votre projet. Votre projet devrait se présenter ainsi dans l’Explorateur de solutions : 
  
   ![Capture d’écran de la vue Explorateur de solutions dans laquelle un fichier image est sélectionné](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. Cliquez avec le bouton droit sur le fichier image, choisissez Propriétés, puis **Copier si plus récent**. 

   ![Fenêtre de propriétés d’une image ; l’option Copier dans le répertoire de sortie est définie sur Copier si plus récent](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
1. Remplacez la méthode Configurer par le code suivant pour accéder à l’API Vision par ordinateur et tester une image.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        // TODO: Change this to your image's path on your site. 
        string imagePath = @"images/subway.png";

        // Enable static files such as image files. 
        app.UseStaticFiles();

        string visionApiKey = this.configuration["ComputerVisionAPI_ServiceKey"];
        string visionApiEndPoint = this.configuration["ComputerVisionAPI_ServiceEndPoint"];

        HttpClient client = new HttpClient();

        // Request headers.
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", visionApiKey);

        // Request parameters. A third optional parameter is "details".
        string requestParameters = "visualFeatures=Categories,Description,Color&language=en";

        // Assemble the URI for the REST API Call.
        string uri = visionApiEndPoint + "/analyze" + "?" + requestParameters;

        HttpResponseMessage response;

        // Request body. Posts an image you've added to your site's images folder. 
        var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
        byte[] byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

        string contentString = string.Empty;
        using (ByteArrayContent content = new ByteArrayContent(byteData))
        {
            // This example uses content type "application/octet-stream".
            // The other content types you can use are "application/json" and "multipart/form-data".
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

            // Execute the REST API call.
            response = client.PostAsync(uri, content).Result;

            // Get the JSON response.
            contentString = response.Content.ReadAsStringAsync().Result;
        }

        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("<h1>Cognitive Services Demo</h1>");
            await context.Response.WriteAsync($"<p><b>Test Image:</b></p>");
            await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
            await context.Response.WriteAsync($"<p><b>Computer Vision API results:</b></p>");
            await context.Response.WriteAsync("<p>");
            await context.Response.WriteAsync(JsonPrettyPrint(contentString));
            await context.Response.WriteAsync("<p>");
        });
    }
   ```

    Ce code construit une requête HTTP en se servant de l’URI et de l’image comme contenu binaire pour un appel à l’API REST Vision par ordinateur.

1. Ajoutez les fonctions d’assistance GetImageAsByteArray et JsonPrettyPrint.

   ```csharp
    /// <summary>
    /// Returns the contents of the specified file as a byte array.
    /// </summary>
    /// <param name="imageFilePath">The image file to read.</param>
    /// <returns>The byte array of the image data.</returns>
    static byte[] GetImageAsByteArray(string imageFilePath)
    {
        FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
        BinaryReader binaryReader = new BinaryReader(fileStream);
        return binaryReader.ReadBytes((int)fileStream.Length);
    }

    /// <summary>
    /// Formats the given JSON string by adding line breaks and indents.
    /// </summary>
    /// <param name="json">The raw JSON string to format.</param>
    /// <returns>The formatted JSON string.</returns>
    static string JsonPrettyPrint(string json)
    {
        if (string.IsNullOrEmpty(json))
            return string.Empty;

        json = json.Replace(Environment.NewLine, "").Replace("\t", "");

        string INDENT_STRING = "    ";
        var indent = 0;
        var quoted = false;
        var sb = new StringBuilder();
        for (var i = 0; i < json.Length; i++)
        {
            var ch = json[i];
            switch (ch)
            {
                case '{':
                case '[':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case '}':
                case ']':
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    sb.Append(ch);
                    break;
                case '"':
                    sb.Append(ch);
                    bool escaped = false;
                    var index = i;
                    while (index > 0 && json[--index] == '\\')
                        escaped = !escaped;
                    if (!escaped)
                        quoted = !quoted;
                    break;
                case ',':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case ':':
                    sb.Append(ch);
                    if (!quoted)
                        sb.Append(" ");
                    break;
                default:
                    sb.Append(ch);
                    break;
            }
        }
        return sb.ToString();
    }
   ```

1. Exécutez l’application web : l’API Vision par ordinateur indique ce qu’elle a trouvé dans l’image.

   ![Image de l’API Vision par ordinateur et résultats mis en forme](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-4.PNG)  

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsqu’il n’est plus nécessaire, supprimez le groupe de ressources. Cette opération supprime le service cognitif et les ressources qui lui sont associées. Pour supprimer le groupe de ressources à l’aide du portail :

1. Entrez le nom de votre groupe de ressources dans la zone Recherche en haut du portail. Lorsque vous voyez le groupe de ressources utilisé dans ce démarrage rapide dans les résultats de recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Dans le champ **TYPE THE RESOURCE GROUP NAME: (TAPER LE NOM DU GROUPE DE RESSOURCES :)** , tapez le nom du groupe de ressources et sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’API Vision par ordinateur en lisant la [documentation des API Vision par ordinateur](Home.md).
