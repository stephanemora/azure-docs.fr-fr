---
title: 'Didacticiel : API Visage en C#'
titleSuffix: Azure Cognitive Services
description: Créez une application Windows qui utilise l’API Visage Cognitive Services pour détecter les traits de visages dans une image.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: ghogen
ms.openlocfilehash: 4b204b9895a2afea4c78d1d92f2cca68f77ae708
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970293"
---
# <a name="connecting-to-cognitive-services-face-api-by-using-connected-services-in-visual-studio"></a>Se connecter à l’API Visage Cognitive Services avec les Services connectés dans Visual Studio

L’API Visage Cognitive Services permet de détecter, d’analyser, d’organiser et d’identifier des visages sur des photos.

Cet article et ceux qui l’accompagnent donnent des détails sur l’utilisation de la fonctionnalité Service connecté Visual Studio de l’API Visage Cognitive Services. Elle est disponible dans Visual Studio 2017 15.7 et les versions ultérieures, à condition que l’extension Cognitive Services soit installée.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’en possédez pas, vous pouvez vous inscrire pour créer dès aujourd’hui un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2017 version 15.7 ou ultérieure avec la charge de travail **Développement web** installée. [Téléchargez-le maintenant](https://www.visualstudio.com/downloads/).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="create-a-project-and-add-support-for-cognitive-services-face-api"></a>Créer un projet et ajouter la prise en charge de l’API Visage Cognitive Services

1. Créez un projet web ASP.NET Core. Utilisez le modèle de projet Vide. 

1. Dans **l’Explorateur de solutions**, choisissez **Ajouter** > **Service connecté**.
   La page Service connecté s’affiche et montre des services que vous pouvez ajouter à votre projet.

   ![Ajouter un élément de menu Service connecté](./media/vs-face-connected-service/Connected-Service-Menu.PNG)

1. Dans le menu des services disponibles, choisissez **API Visage Cognitive Services**.

   ![Choisir le service](./media/vs-face-connected-service/Cog-Face-Connected-Service-0.PNG)

   Si vous êtes connecté à Visual Studio et si un abonnement Azure est associé à votre compte, une page s’affiche et montre la liste déroulante de vos abonnements.

   ![Sélectionnez votre abonnement](media/vs-face-connected-service/Cog-Face-Connected-Service-1.PNG)

1. Sélectionnez l’abonnement que vous souhaitez utiliser. Ensuite, choisissez un nom pour l’API Visage ou cliquez sur le lien Modifier pour modifier le nom généré automatiquement. Choisissez le groupe de ressources et le niveau tarifaire.

   ![Modifier les informations du service connecté](media/vs-face-connected-service/Cog-Face-Connected-Service-2.PNG)

   Pour plus d’informations sur les niveaux tarifaires, suivez le lien.

1. Cliquez sur Ajouter pour ajouter la prise en charge du Service connecté.
   Visual Studio modifie votre projet en ajoutant les packages NuGet, les entrées du fichier de configuration et autres afin de prendre en charge une connexion à l’API Visage.

## <a name="use-the-face-api-to-detect-attributes-of-faces-in-an-image"></a>Utiliser l’API Visage pour détecter des attributs de visages dans une image

1. Ajoutez les instructions using suivantes dans Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Ajoutez un champ configuration, puis un constructeur qui l’initialise dans la classe Startup pour activer la configuration de votre programme.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Dans le dossier wwwroot de votre projet, ajoutez un dossier images et un fichier image. Par exemple, vous pouvez utiliser l’une des images de cette [page de l’API Visage](https://azure.microsoft.com/services/cognitive-services/face/). Cliquez avec le bouton droit sur une des images et enregistrez-la sur votre disque dur local. Ensuite, dans l’Explorateur de solutions, cliquez avec le bouton droit sur le dossier des images et choisissez **Ajouter** > **Élément existant** pour l’ajouter à votre projet. Votre projet devrait se présenter ainsi dans l’Explorateur de solutions :
 
   ![Dossier images avec fichier image](media/vs-face-connected-service/Cog-Face-Connected-Service-6.PNG)

1. Cliquez avec le bouton droit sur le fichier image, choisissez Propriétés, puis **Copier si plus récent**.

   ![Copier si plus récent](media/vs-face-connected-service/Cog-Face-Connected-Service-5.PNG)
 
1. Remplacez la méthode Configurer par le code suivant pour accéder à l’API Visage et tester une image. Remplacez la chaîne imagePath par le chemin qui convient et le nom de fichier de votre image de visage.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site.
            string imagePath = @"images/face1.png";

            // Enable static files such as image files.
            app.UseStaticFiles();

            string faceApiKey = this.configuration["FaceAPI_ServiceKey"];
            string faceApiEndPoint = this.configuration["FaceAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", faceApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

            // Assemble the URI for the REST API Call.
            string uri = faceApiEndPoint + "/detect?" + requestParameters;

            // Request body. Posts an image you've added to your site's images folder.
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            var byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentStringFace = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                var response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentStringFace = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync($"<p><b>Face Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Face detection API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentStringFace));
                await context.Response.WriteAsync("<p>");
            });
        }
   ```
    Le code de cette étape construit une requête HTTP avec un appel à l’API REST Visage, à l’aide de la clé que vous avez ajoutée quand vous avez intégré le service connecté.

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

1. Exécutez l’application web : l’API Visage indique ce qu’elle a trouvé dans l’image.
 
   ![Image de l’API Visage et résultats mis en forme](media/vs-face-connected-service/Cog-Face-Connected-Service-4.PNG)

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsqu’il n’est plus nécessaire, supprimez le groupe de ressources. Cette opération supprime le service cognitif et les ressources qui lui sont associées. Pour supprimer le groupe de ressources à l’aide du portail :

1. Entrez le nom de votre groupe de ressources dans la zone Recherche en haut du portail. Lorsque vous voyez le groupe de ressources utilisé dans ce démarrage rapide dans les résultats de recherche, sélectionnez-le.
1. Sélectionnez **Supprimer le groupe de ressources**.
1. Dans le champ **TYPE THE RESOURCE GROUP NAME: (TAPER LE NOM DU GROUPE DE RESSOURCES :)** , tapez le nom du groupe de ressources et sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’API Visage, lisez la [Documentation de l’API Visage](Overview.md).
