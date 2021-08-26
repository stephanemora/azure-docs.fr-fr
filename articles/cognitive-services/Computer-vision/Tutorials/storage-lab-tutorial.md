---
title: 'Tutoriel : Générer des métadonnées pour les images Azure'
titleSuffix: Azure Cognitive Services
description: Ce tutoriel vous montre comment intégrer le service Azure Vision par ordinateur dans une application web afin de générer des métadonnées pour des images.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 07/06/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 35e98fcbd1be87e471152d0760ca375e46770a81
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747643"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Tutoriel : Utiliser le service Vision par ordinateur pour générer des métadonnées des images dans le stockage Azure

Ce tutoriel vous montre comment intégrer le service Vision par ordinateur d’Azure dans une application web afin de générer ensuite des métadonnées pour les images chargées. Cela est utile pour les scénarios de [gestion des actifs numériques](../overview.md#computer-vision-for-digital-asset-management), par exemple si une entreprise souhaite générer rapidement des sous-titres descriptifs ou des mots clés de recherche pour toutes ses images.

Vous utiliserez Visual Studio pour écrire une application web MVC qui accepte les images chargées par les utilisateurs et stocke les images dans Stockage Blob Azure. Vous apprendrez à lire et à écrire des blobs en C# et à utiliser les métadonnées des blobs pour joindre des informations supplémentaires aux blobs que vous créez. Ensuite, vous soumettrez chaque image chargée par l’utilisateur à l’API Vision par ordinateur pour générer une légende et des métadonnées de recherche pour l’image. Enfin, vous pouvez déployer l’application dans le cloud à l’aide de Visual Studio.

Ce didacticiel vous explique les procédures suivantes :

> [!div class="checklist"]
> * Créer un compte de stockage et des conteneurs de stockage à l’aide du portail Azure
> * Créer une application web dans Visual Studio et la déployer dans Azure
> * Utiliser l’API Vision par ordinateur pour extraire des informations des images
> * Attacher des métadonnées aux images du stockage Azure
> * Vérifier les métadonnées des images à l’aide d’[Explorateur Stockage Azure](http://storageexplorer.com/)

> [!TIP]
> La section [Utiliser Vision par ordinateur pour générer des métadonnées](#Exercise5) est la plus pertinente pour l’analyse d’images. Passez directement à cette section si vous souhaitez simplement voir comment l’analyse d’images est intégrée à une application établie.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services) avant de commencer. 

## <a name="prerequisites"></a>Prérequis

- [Visual Studio 2017 Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) ou ultérieur, avec les charges de travail « ASP.NET et développement web » et « Développement Azure » installées.
- L’outil [Explorateur Stockage Azure](http://storageexplorer.com/) installé.

<a name="Exercise1"></a>
## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Dans cette section, vous allez utiliser le [portail Azure](https://portal.azure.com?WT.mc_id=academiccontent-github-cxa) pour créer un compte de stockage. Vous créerez ensuite deux conteneurs : l’un pour stocker les images chargées par l’utilisateur et l’autre pour stocker les miniatures d’images générées à partir des images téléchargées.

1. Dans votre navigateur, ouvrez le [portail Azure](https://portal.azure.com?WT.mc_id=academiccontent-github-cxa). Si vous êtes invité à vous connecter, faites-le en utilisant votre compte Microsoft.
1. Pour créer un compte de stockage, cliquez sur **+ Créer une ressource** dans le ruban à gauche. Cliquez ensuite sur **Stockage**, puis sur **Compte de stockage**.

    ![Création d’un compte de stockage](Images/new-storage-account.png)
1. Entrez un nom unique pour le compte de stockage dans le champ **Nom** et assurez-vous qu’une coche verte apparaît à côté. Le nom est important, car il constitue une partie de l’URL par le biais de laquelle les blobs créés sous ce compte sont accessibles. Placez le compte de stockage dans un nouveau groupe de ressources nommé « IntellipixResources » et sélectionnez la région la plus proche de vous. Terminez en cliquant sur le bouton **Vérifier + créer** en bas de l’écran pour créer le nouveau compte de stockage.
    > [!NOTE]
    > Les noms des comptes de stockage peuvent comporter de 3 à 24 caractères, uniquement des lettres minuscules et des chiffres. En outre, le nom que vous entrez doit être unique au sein d’Azure. Si quelqu’un d’autre a choisi le même nom, vous êtes informé que le nom n’est pas disponible avec un point d’exclamation rouge dans le champ **Nom**.
   
    ![Spécification des paramètres d’un nouveau compte de stockage](Images/create-storage-account.png)
1. Cliquez sur **Groupes de ressources** dans le ruban à gauche. Cliquez ensuite sur le groupe de ressources « IntellipixResources ».

    ![Ouverture du groupe de ressources](Images/open-resource-group.png)
1. Dans l’onglet qui s’ouvre pour le groupe de ressources, cliquez sur le compte de stockage que vous avez créé. Si le compte de stockage n’est pas encore présent, vous pouvez cliquer sur **Actualiser** en haut de l’onglet jusqu’à ce qu’il apparaisse.

    ![Ouverture du nouveau compte de stockage](Images/open-storage-account.png)
1. Dans l’onglet du compte de stockage, cliquez sur **Blobs** pour afficher la liste des conteneurs associés à ce compte.

    ![Bouton Afficher les blobs](Images/view-containers.png)

1. Le compte de stockage n’a actuellement aucun conteneur. Avant de pouvoir créer un blob, vous devez créer un conteneur dans lequel le stocker. Cliquez sur **+ Conteneur** pour créer un conteneur. Saisissez `photos` dans le champ **Nom** et sélectionnez **Blob** comme **niveau d’accès public**. Cliquez ensuite sur **OK** pour créer un conteneur nommé « photos ».

    > Par défaut, les conteneurs et leur contenu sont privés. La sélection de **Blob** comme niveau d’accès rend les blobs du conteneur « photos » publiquement accessibles, mais ne rend pas public le conteneur proprement dit. C’est ce que vous voulez, car les images stockées dans le conteneur « photos » seront liées à partir d’une application web. 

    ![Création d’un conteneur « photos »](Images/create-photos-container.png)

1. Répétez l’étape précédente pour créer un conteneur nommé « miniatures », en vous assurant une fois de plus que le **niveau d’accès public** du conteneur est défini sur **Blob**.
1. Vérifiez que les deux conteneurs apparaissent dans la liste des conteneurs de ce compte de stockage et que les noms sont correctement orthographiés.

    ![Les nouveaux conteneurs](Images/new-containers.png)

1. Fermez l’écran « Service BLOB ». Cliquez sur **Clés d’accès** dans le menu situé à gauche de l’écran du compte de stockage, puis cliquez sur le bouton **Copier** à côté de **CLÉ** pour **key1**. Collez cette clé d’accès dans votre éditeur de texte favori pour une utilisation ultérieure.

    ![Copie de la clé d’accès](Images/copy-storage-account-access-key.png)

Vous avez maintenant créé un compte de stockage pour contenir les images chargées vers l’application que vous allez créer, ainsi que des conteneurs dans lesquels stocker les images. 

<a name="Exercise2"></a>
## <a name="run-azure-storage-explorer"></a>Exécuter Explorateur Stockage Azure

[Explorateur Stockage Azure](http://storageexplorer.com/) est un outil gratuit qui fournit une interface graphique permettant de travailler avec Stockage Azure sur des PC sous Windows, macOS et Linux. Il offre la plupart des mêmes fonctionnalités que le portail Azure et propose d’autres fonctionnalités comme la possibilité de visualiser les métadonnées des blobs. Dans cette section, vous allez utiliser Explorateur Stockage Microsoft Azure pour afficher les conteneurs que vous avez créés dans la section précédente.

1. Si vous n’avez pas installé Explorateur Stockage ou si vous souhaitez vous assurer que vous exécutez la version la plus récente, accédez à l’adresse http://storageexplorer.com/ et téléchargez et installez-la.
1. Démarrez l’Explorateur Stockage. Si vous êtes invité à vous connecter, utilisez votre compte Microsoft, celui que vous avez utilisé pour vous connecter au portail Azure. Si vous ne voyez pas le compte de stockage dans le volet gauche d’Explorateur Stockage, cliquez sur le bouton **Gérer les comptes** mis en évidence ci-dessous et assurez-vous que votre compte Microsoft et l’abonnement utilisé pour créer le compte de stockage ont été ajoutés à Explorateur Stockage.

    ![Gestion des comptes dans Explorateur Stockage](Images/add-account.png)

1. Cliquez sur la petite flèche à côté du compte de stockage pour afficher son contenu, puis cliquez sur la flèche à côté de **Conteneurs de blobs**. Vérifiez que les conteneurs que vous avez créés apparaissent dans la liste.

    ![Consultation des conteneurs d’objets blob](Images/storage-explorer.png)

Les conteneurs sont actuellement vides, mais cela changera une fois que votre application sera déployée et que vous commencerez à charger des photos. L’installation d’Explorateur Stockage vous permettra de voir facilement ce que votre application écrit dans Stockage Blob.

<a name="Exercise3"></a>
## <a name="create-a-new-web-app-in-visual-studio"></a>Créer une application web dans Visual Studio

Dans cette section, vous allez créer une application web dans Visual Studio et ajouter du code pour implémenter les fonctionnalités de base requises pour charger des images, les écrire dans Stockage Blob et les afficher dans une page web.

1. Démarrez Visual Studio et utilisez la commande **fichier-> New-> Project** pour créer un projet d’**application web ASP.NET** C# Visual nommé « Intellipix » (abréviation de « Intelligent Pictures », images intelligentes).

    ![Création d’un projet d’application web](Images/new-web-app.png)

1. Dans la boîte de dialogue « Nouvelle application web ASP.NET », assurez-vous que **MVC** est sélectionné. Cliquez ensuite sur **OK**.

    ![Création d’un projet MVC ASP.NET](Images/new-mvc-project.png)

1. Prenez un moment pour examiner la structure du projet dans Explorateur de solutions. Vous y trouverez, entre autres, un dossier nommé **Controllers**, qui contient les contrôleurs MVC du projet, et un dossier nommé **Views**, qui contient les affichages du projet. Vous travaillerez avec les ressources de ces dossiers et d’autres dossiers au fur et à mesure de l’implémentation de l’application.

    ![Le projet affiché dans Explorateur de solutions](Images/project-structure.png)

1. Utilisez la commande **Debug -> Start Without Debugging** de Visual Studio (ou appuyez sur **Ctrl+F5**) pour lancer l’application dans votre navigateur. Voici à quoi ressemble l’application dans son état actuel :

    ![L’application initiale](Images/initial-application.png)

1. Fermez le navigateur et revenez à Visual Studio. Dans Explorateur de solutions, cliquez avec le bouton droit sur le projet **Intellipix** et sélectionnez **Gérer les packages NuGet...** Cliquez sur **Parcourir**. Saisissez ensuite `imageresizer` dans la zone de recherche et sélectionnez le package NuGet nommé **ImageResizer**. Enfin, cliquez sur **Installer** pour installer la dernière version stable du package. ImageResizer contient des API que vous allez utiliser pour créer des miniatures d’image à partir des images chargées dans l’application. Validez les modifications et acceptez les licences qui vous sont présentées.

    ![Installation d’ImageResizer](Images/install-image-resizer.png)

1. Répétez ce processus pour ajouter le package NuGet nommé **WindowsAzure.Storage** au projet. Ce package contient des API permettant d’accéder à Stockage Azure à partir d’applications .NET. Validez les modifications et acceptez les licences qui vous sont présentées.

    ![Installation de WindowsAzure.Stockage](Images/install-storage-package.png)

1. Ouvrez _Web.config_ et ajoutez l’instruction suivante à la section ```<appSettings>```, en remplaçant ACCOUNT_NAME par le nom du compte de stockage que vous avez créé dans la première section, et ACCOUNT_KEY par la clé d’accès que vous avez enregistrée.

    ```xml
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=ACCOUNT_NAME;AccountKey=ACCOUNT_KEY" />
    ```

1. Ouvrez le fichier nommé *_Layout.cshtml* dans le dossier **Views/Shared** du projet. Sur la ligne 19, remplacez « Application name » par « Intellipix ». La ligne doit se présenter comme suit :

    ```C#
    @Html.ActionLink("Intellipix", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

    > [!NOTE]
    > Dans un projet MVC ASP.NET, *_Layout.cshtml* est un affichage spécial qui sert de modèle aux autres affichages. Dans ce fichier, vous définissez généralement le contenu de l’en-tête et du pied de page qui est commun à tous les affichages.

1. Cliquez avec le bouton droit sur le dossier **Models** du projet et utilisez la commande **Add -> Class...** pour ajouter un fichier de classe nommé *BlobInfo.cs* au dossier. Remplacez ensuite la classe **BlobInfo** vide par la définition de classe suivante :

    ```C#
    public class BlobInfo
    {
        public string ImageUri { get; set; }
        public string ThumbnailUri { get; set; }
        public string Caption { get; set; }
    }
    ```

1. Ouvrez *HomeController.cs* dans le dossier **Controllers** du projet et ajoutez les instructions `using` suivantes en haut du fichier :

    ```C#
    using ImageResizer;
    using Intellipix.Models;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Configuration;
    using System.Threading.Tasks;
    using System.IO;
    ```

1. Remplacez la méthode **Index** dans *HomeController.cs* par l’implémentation suivante :

    ```C#
    public ActionResult Index()
    {
        // Pass a list of blob URIs in ViewBag
        CloudStorageAccount account = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);
        CloudBlobClient client = account.CreateCloudBlobClient();
        CloudBlobContainer container = client.GetContainerReference("photos");
        List<BlobInfo> blobs = new List<BlobInfo>();
    
        foreach (IListBlobItem item in container.ListBlobs())
        {
            var blob = item as CloudBlockBlob;
    
            if (blob != null)
            {
                blobs.Add(new BlobInfo()
                {
                    ImageUri = blob.Uri.ToString(),
                    ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/")
                });
            }
        }
    
        ViewBag.Blobs = blobs.ToArray();
        return View();
    }
    ```

    La nouvelle méthode **Index** énumère les blobs dans le conteneur `"photos"` et transmet un tableau d’objets **BlobInfo** représentant ces blobs à l’affichage par le biais de la propriété **ViewBag** de MVC ASP.NET. Plus tard, vous modifierez l’affichage pour énumérer ces objets et afficher une collection de miniatures de photos. Les classes que vous allez utiliser pour accéder à votre compte de stockage et énumérer les blobs ( **[CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount?view=azure-dotnet)** , **[CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient?view=azure-dotnet-legacy)** et **[CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer?view=azure-dotnet-legacy)** ) proviennent du package **WindowsAzure.Storage** que vous avez installé via NuGet.

1. Ajoutez la méthode suivante à la classe **HomeController** dans *HomeController.cs* :

    ```C#
    [HttpPost]
    public async Task<ActionResult> Upload(HttpPostedFileBase file)
    {
        if (file != null && file.ContentLength > 0)
        {
            // Make sure the user selected an image file
            if (!file.ContentType.StartsWith("image"))
            {
                TempData["Message"] = "Only image files may be uploaded";
            }
            else
            {
                try
                {
                    // Save the original image in the "photos" container
                    CloudStorageAccount account = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);
                    CloudBlobClient client = account.CreateCloudBlobClient();
                    CloudBlobContainer container = client.GetContainerReference("photos");
                    CloudBlockBlob photo = container.GetBlockBlobReference(Path.GetFileName(file.FileName));
                    await photo.UploadFromStreamAsync(file.InputStream);
    
                    // Generate a thumbnail and save it in the "thumbnails" container
                    using (var outputStream = new MemoryStream())
                    {
                        file.InputStream.Seek(0L, SeekOrigin.Begin);
                        var settings = new ResizeSettings { MaxWidth = 192 };
                        ImageBuilder.Current.Build(file.InputStream, outputStream, settings);
                        outputStream.Seek(0L, SeekOrigin.Begin);
                        container = client.GetContainerReference("thumbnails");
                        CloudBlockBlob thumbnail = container.GetBlockBlobReference(Path.GetFileName(file.FileName));
                        await thumbnail.UploadFromStreamAsync(outputStream);
                    }
                }
                catch (Exception ex)
                {
                    // In case something goes wrong
                    TempData["Message"] = ex.Message;
                }
            }
        }
    
        return RedirectToAction("Index");
    }
    ```

    Il s’agit de la méthode qui est appelée lorsque vous chargez une photo. Elle stocke chaque image chargée sous la forme d’un blob dans le conteneur `"photos"`, crée une image miniature à partir de l’image d’origine à l’aide du package `ImageResizer` et stocke la l’image miniature sous la forme d’un blob dans le conteneur `"thumbnails"`.

1. Ouvrez *Index.cshmtl* dans le dossier **Views/Home** du projet et remplacez son contenu par le code et le balisage suivants :

    ```HTML
    @{
        ViewBag.Title = "Intellipix Home Page";
    }
    
    @using Intellipix.Models
    
    <div class="container" style="padding-top: 24px">
        <div class="row">
            <div class="col-sm-8">
                @using (Html.BeginForm("Upload", "Home", FormMethod.Post, new { enctype = "multipart/form-data" }))
                {
                    <input type="file" name="file" id="upload" style="display: none" onchange="$('#submit').click();" />
                    <input type="button" value="Upload a Photo" class="btn btn-primary btn-lg" onclick="$('#upload').click();" />
                    <input type="submit" id="submit" style="display: none" />
                }
            </div>
            <div class="col-sm-4 pull-right">
            </div>
        </div>
    
        <hr />
    
        <div class="row">
            <div class="col-sm-12">
                @foreach (BlobInfo blob in ViewBag.Blobs)
                {
                    <img src="@blob.ThumbnailUri" width="192" title="@blob.Caption" style="padding-right: 16px; padding-bottom: 16px" />
                }
            </div>
        </div>
    </div>
    
    @section scripts
    {
        <script type="text/javascript" language="javascript">
            if ("@TempData["Message"]" !== "") {
                alert("@TempData["Message"]");
            }
        </script>
    }
    ```

    Le langage utilisé ici est [Razor](http://www.asp.net/web-pages/overview/getting-started/introducing-razor-syntax-c), qui permet d’incorporer du code exécutable dans le balisage HTML. L’instruction ```@foreach``` au milieu du fichier énumère les objets **BlobInfo** transmis par le contrôleur dans **ViewBag** et crée des éléments HTML ```<img>``` à partir de ceux-ci. La propriété ```src``` de chaque élément est initialisée avec l’URI du blob contenant la miniature de l’image.

1. Téléchargez et décompressez le fichier _photos.zip_ provenant de l’[exemple de référentiel de données GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/ComputerVision/storage-lab-tutorial). Il s’agit d’un assortiment de différentes photos que vous pouvez utiliser pour tester l’application.

1. Enregistrez vos modifications, puis appuyez sur **Ctrl+F5** pour lancer l’application dans votre navigateur. Cliquez ensuite sur **Charger une photo** et chargez l’une des images que vous avez téléchargées. Confirmez qu’une version miniature de la photo apparaît sur la page.

    ![Intellipix avec une photo chargée](Images/one-photo-uploaded.png)

1. Chargez quelques images supplémentaires à partir de votre dossier **photos**. Confirmez qu’elles apparaissent également sur la page :

    ![Intellipix avec trois photos chargées](Images/three-photos-uploaded.png)

1. Cliquez avec le bouton droit dans votre navigateur et sélectionnez **Afficher la source de la page** pour afficher le code source de la page. Recherchez les éléments ```<img>``` représentant les miniatures d’image. Notez que les URL attribuées aux images font directement référence aux blobs dans Stockage Blob. Cela s’explique par le fait que vous avez défini le **niveau d’accès public** des conteneurs sur **Blob**, ce qui rend les blobs qu’ils contiennent accessibles au public.

1. Revenez à Explorateur Stockage Azure (ou redémarrez-le si vous ne l’avez pas laissé tourner) et sélectionnez le conteneur `"photos"` sous votre compte de stockage. Le nombre de blobs dans le conteneur doit être égal au nombre de photos que vous avez chargées. Double-cliquez sur l’un des blobs pour le télécharger et voir l’image stockée dans le blob.

    ![Contenu du conteneur « photos »](Images/photos-container.png)

1. Ouvrez le conteneur `"thumbnails"` dans Explorateur Stockage. Ouvrez l’un des blobs pour afficher les images miniatures générées à partir des images chargées.

L’application ne permet pas encore d’afficher les images d’origine que vous avez chargées. Dans l’idéal, un clic sur une miniature d’image doit afficher l’image d’origine. Cette fonctionnalité sera ajoutée prochainement.

<a name="Exercise4"></a>
## <a name="add-a-lightbox-for-viewing-photos"></a>Ajouter une table lumineuse pour l’affichage des photos

Dans cette section, vous allez utiliser une bibliothèque JavaScript gratuite et open source pour ajouter une visionneuse de type « table lumineuse » qui permet aux utilisateurs de voir les images d’origine qu’ils ont chargées (plutôt que les miniatures d’image). Les fichiers sont fournis pour vous. Il vous suffit de les intégrer au projet et d’apporter une modification mineure à *Index.cshtml*.

1. Téléchargez les fichiers _lightbox.css_ et _lightbox.js_ à partir du [référentiel de code GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/storage-lab-tutorial).
1. Dans Explorateur de solutions, cliquez avec le bouton droit sur le dossier **Scripts** de votre projet et utilisez la commande **Add -> New Item...** pour créer un fichier *lightbox.js*. Collez le contenu du fichier d’exemple dans le [référentiel de code GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/storage-lab-tutorial/scripts/lightbox.js).

1. Cliquez avec le bouton droit sur le dossier « Content » du projet et utilisez la commande **Add -> New Item...** pour créer un fichier *lightbox.css*. Collez le contenu du fichier d’exemple dans le [référentiel de code GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/storage-lab-tutorial/css/lightbox.css).
1. Téléchargez et décompressez le fichier _buttons.zip_ provenant du référentiel de fichiers de données de GitHub : https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/ComputerVision/storage-lab-tutorial. Vous devez avoir quatre images de bouton.

1. Cliquez avec le bouton droit sur le projet Intellipix dans Explorateur de solutions et utilisez la commande **Add -> New Folder** pour ajouter un dossier nommé « Images » au projet.

1. Cliquez avec le bouton droit sur le dossier **Images** et utilisez la commande **Add -> Existing Item...** pour importer les quatre images que vous avez téléchargées.

1. Ouvrez *BundleConfig.cs* dans le dossier « App_Start » du projet. Ajoutez l’instruction suivante à la méthode ```RegisterBundles``` dans **BundleConfig.cs** :

    ```C#
    bundles.Add(new ScriptBundle("~/bundles/lightbox").Include(
              "~/Scripts/lightbox.js"));
    ```

1. Dans la même méthode, recherchez l’instruction qui crée un ```StyleBundle``` à partir de « ~/Content/css » et ajoutez *lightbox.css* à la liste des feuilles de style du paquet. Voici l’instruction modifiée :

    ```C#
    bundles.Add(new StyleBundle("~/Content/css").Include(
              "~/Content/bootstrap.css",
              "~/Content/site.css",
              "~/Content/lightbox.css"));
    ```

1. Ouvrez *_Layout.cshtml* dans le dossier **Views/Shared** du projet et ajoutez l’instruction suivante juste avant l’instruction ```@RenderSection```, vers le bas :

    ```C#
    @Scripts.Render("~/bundles/lightbox")
    ```

1. La dernière tâche consiste à incorporer la visionneuse de type « table lumineuse » à la page d’accueil. Pour ce faire, ouvrez *Index.cshtml* (il se trouve dans le dossier **Views/Home** du projet) et remplacez la boucle ```@foreach``` par celle-ci :

    ```HTML
    @foreach (BlobInfo blob in ViewBag.Blobs)
    {
        <a href="@blob.ImageUri" rel="lightbox" title="@blob.Caption">
            <img src="@blob.ThumbnailUri" width="192" title="@blob.Caption" style="padding-right: 16px; padding-bottom: 16px" />
        </a>
    }
    ```

1. Enregistrez vos modifications, puis appuyez sur **Ctrl+F5** pour lancer l’application dans votre navigateur. Cliquez ensuite sur l’une des images que vous avez chargées précédemment. Confirmez qu’une table lumineuse apparaît et affiche une vue agrandie de l’image.

    ![Une image agrandie](Images/lightbox-image.png)

1. Cliquez sur le **X** dans le coin inférieur droit de la table lumineuse pour la faire disparaître.

Vous disposez maintenant d’un moyen d’afficher les images que vous avez chargées. L’étape suivante consiste à faire plus avec ces images.

<a name="Exercise5"></a>
## <a name="use-computer-vision-to-generate-metadata"></a>Utiliser Vision par ordinateur pour générer des métadonnées

### <a name="create-a-computer-vision-resource"></a>Créer une ressource Vision par ordinateur

Vous devez créer une ressource Vision par ordinateur pour votre compte Azure ; cette ressource gère votre accès au service Vision par ordinateur d’Azure. 

1. Pour créer une ressource Vision par ordinateur, suivez les instructions de l'article [Créer une ressource Azure Cognitive Services](../../cognitive-services-apis-create-account.md).

1. Accédez ensuite au menu de votre groupe de ressources et cliquez sur l’abonnement à l’API Vision par ordinateur que vous avez créé. Copiez l’URL indiquée sous **Point de terminaison** à un endroit où vous pourrez facilement la récupérer un peu plus tard. Cliquez ensuite sur **Afficher les clés d’accès**.

    ![Page du portail Azure avec l’URL du point de terminaison et le lien des clés d’accès entourés](Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. Dans la fenêtre suivante, copiez la valeur de **KEY 1** dans le Presse-papiers.

    ![Boîte de dialogue Gérer les clés, avec le bouton de copie entouré](Images/copy-vision-key.png)

### <a name="add-computer-vision-credentials"></a>Ajouter les informations d’identification pour Vision par ordinateur

Vous devez ensuite ajouter les informations d’identification nécessaire à votre application pour pouvoir accéder aux ressources Vision par ordinateur.

Accédez au fichier *Web.config* à la racine du projet. Ajoutez les instructions suivantes dans la section `<appSettings>` du fichier, en remplaçant `VISION_KEY` par la clé que vous aviez copiée à l’étape précédente et `VISION_ENDPOINT` par l’URL que vous avez enregistrée à l’étape précédente.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis utilisez la commande **Gérer les packages NuGet** pour installer le package **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**. Ce package contient les types à utiliser pour appeler l’API Vision par ordinateur.

### <a name="add-metadata-generation-code"></a>Ajouter le code de génération des métadonnées

Ensuite, vous allez ajouter le code qui utilise réellement le service Vision par ordinateur pour créer des métadonnées pour les images.

1. Ouvrez le fichier *HomeController.cs* dans le dossier **Contrôleurs** du projet et ajoutez les instructions `using` suivantes en haut du fichier :

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Accédez ensuite à la méthode **Upload**, qui convertit et charge les images dans un stockage blob. Ajoutez le code suivant immédiatement après le bloc qui commence par `// Generate a thumbnail` (ou à la fin du processus de création de l’objet blob de l’image). Ce code utilise l’objet blob contenant l’image (`photo`) et génère une description de cette image à l’aide de Vision par ordinateur. L’API Vision par ordinateur génère également une liste de mots clés en lien avec l’image. Les mots clés et la description générés sont stockés dans les métadonnées de l’objet blob pour les utiliser plus tard.

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    List<VisualFeatureTypes?> features = new List<VisualFeatureTypes?>() { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. Ensuite, accédez à la méthode **Index** dans le même fichier. Cette méthode énumère les objets blob d’image stockés dans le conteneur d’objets blob ciblé (comme les instances de **IListBlobItem**) et les passe à la vue de l’application. Remplacez le bloc `foreach` dans cette méthode par le code suivant. Ce code appelle **CloudBlockBlob.FetchAttributes** pour obtenir les métadonnées attachées à chaque objet blob. Il extrait la description générée par ordinateur (`caption`) à partir des métadonnées et l’ajoute à l’objet **BlobInfo**, qui est alors passé à la vue.
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

### <a name="test-the-app"></a>Test de l'application

Enregistrez vos modifications dans Visual Studio, puis appuyez sur **Ctrl+F5** pour lancer l’application dans votre navigateur. Utilisez l’application pour charger quelques images supplémentaires, soit à partir du jeu de photos que vous avez téléchargé, soit à partir de votre propre dossier. Lorsque vous passez le curseur sur l’une des nouvelles images dans la vue, une fenêtre d’infobulle doit apparaître et afficher la légende générée par ordinateur pour l’image.

![Légende générée par ordinateur](Images/thumbnail-with-tooltip.png)

Pour voir toutes les métadonnées attachées, affichez le conteneur de stockage utilisé pour les images dans Explorateur Stockage Azure. Cliquez avec le bouton droit sur un objet blob dans le conteneur, puis sélectionnez **Propriétés**. Dans la boîte de dialogue, vous voyez une liste de paires clé-valeur. La description de l’image générée par ordinateur est stockée dans l’élément `Caption` et les différents mots clés de recherche sont stockés dans `Tag0`, `Tag1`, etc. Quand vous avez terminé, cliquez sur **Annuler** pour fermer la boîte de dialogue.

![Fenêtre de dialogue des propriétés de l’image, avec les balises de métadonnées listées](Images/blob-metadata.png)

<a name="Exercise6"></a>
## <a name="add-search-to-the-app"></a>Ajouter la recherche à l’application

Dans cette section, vous allez ajouter une zone de recherche à la page d’accueil, ce qui permet aux utilisateurs d’effectuer des recherches par mots clés sur les images qu’ils ont chargées. Les mots clés sont ceux générés par le API Vision par ordinateur et stockés dans les métadonnées du blob.

1. Ouvrez *Index.cshtml* dans le dossier **Views/Home** du projet et ajoutez les instructions suivantes à l’élément ```<div>``` vide avec l’attribut ```class="col-sm-4 pull-right"``` :

    ```HTML
    @using (Html.BeginForm("Search", "Home", FormMethod.Post, new { enctype = "multipart/form-data", @class = "navbar-form" }))
    {
        <div class="input-group">
            <input type="text" class="form-control" placeholder="Search photos" name="term" value="@ViewBag.Search" style="max-width: 800px">
            <span class="input-group-btn">
                <button class="btn btn-primary" type="submit">
                    <i class="glyphicon glyphicon-search"></i>
                </button>
            </span>
        </div>
    }
    ```

    Ce code et ce balisage ajoutent une zone de recherche et un bouton **Recherche** à la page d’accueil.

1. Ouvrez *HomeController.cs* dans le dossier **Controllers** du projet et ajoutez la méthode suivante à la classe **HomeController** :

    ```C#
    [HttpPost]
    public ActionResult Search(string term)
    {
        return RedirectToAction("Index", new { id = term });
    }
    ```

    Il s’agit de la méthode qui est appelée lorsque l’utilisateur clique sur le bouton **Recherche** ajouté à l’étape précédente. Elle actualise la page et inclut un paramètre de recherche dans l’URL.

1. Remplacez la méthode **Index** par l’implémentation suivante :

    ```C#
    public ActionResult Index(string id)
    {
        // Pass a list of blob URIs and captions in ViewBag
        CloudStorageAccount account = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);
        CloudBlobClient client = account.CreateCloudBlobClient();
        CloudBlobContainer container = client.GetContainerReference("photos");
        List<BlobInfo> blobs = new List<BlobInfo>();

        foreach (IListBlobItem item in container.ListBlobs())
        {
            var blob = item as CloudBlockBlob;

            if (blob != null)
            {
                blob.FetchAttributes(); // Get blob metadata

                if (String.IsNullOrEmpty(id) || HasMatchingMetadata(blob, id))
                {
                    var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;

                    blobs.Add(new BlobInfo()
                    {
                        ImageUri = blob.Uri.ToString(),
                        ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                        Caption = caption
                    });
                }
            }
        }

        ViewBag.Blobs = blobs.ToArray();
        ViewBag.Search = id; // Prevent search box from losing its content
        return View();
    }
    ```

    Notez que la méthode **Index** accepte désormais un paramètre _id_ qui contient la valeur saisie par l’utilisateur dans la zone de recherche. Un paramètre _id_ vide ou manquant indique que toutes les photos doivent être affichées.

1. Ajoutez la méthode d’assistance suivante à la classe **HomeController** :

    ```C#
    private bool HasMatchingMetadata(CloudBlockBlob blob, string term)
    {
        foreach (var item in blob.Metadata)
        {
            if (item.Key.StartsWith("Tag") && item.Value.Equals(term, StringComparison.InvariantCultureIgnoreCase))
                return true;
        }

        return false;
    }
    ```

    Cette méthode est appelée par la méthode **Index** pour déterminer si les mots clés de métadonnées attachés à un blob d’image donné contiennent le terme de recherche entré par l’utilisateur.

1. Relancez l’application et chargez plusieurs photos. N’hésitez pas à utiliser vos propres photos, pas seulement celles fournies avec le tutoriel.

1. Saisissez un mot clé tel que « rivière » dans la zone de recherche. Cliquez ensuite sur le bouton **Recherche**.

    ![Exécution d’une recherche](Images/enter-search-term.png)

1. Les résultats de la recherche varient en fonction de ce que vous avez saisi et des images que vous avez chargées. Toutefois, le résultat doit être une liste filtrée d’images dont les mots clés de métadonnées incluent tout ou partie du mot clé que vous avez saisi.

    ![Résultats de la recherche](Images/search-results.png)

1. Cliquez sur le bouton de retour en arrière du navigateur pour afficher à nouveau toutes les images.

Vous avez presque fini. Il est temps de déployer l’application dans le cloud.

<a name="Exercise7"></a>
## <a name="deploy-the-app-to-azure"></a>Déploiement de l’application dans Azure

Dans cette section, vous allez déployer l’application sur Visual Studio. Vous allez autoriser Visual Studio à créer une application web Azure pour vous, ce qui vous évitera d’avoir à accéder au portail Azure et à la créer séparément.

1. Dans Explorateur de solutions, cliquez avec le bouton droit sur le projet et sélectionnez **Publier…** dans le menu contextuel. Assurez-vous que **Microsoft Azure App Service** et **Créer nouveau** sont sélectionnés, puis cliquez sur le bouton **Publier**.

    ![Publication de l’application](Images/publish-1.png)

1. Dans la boîte de dialogue suivante, sélectionnez le groupe de ressources « IntellipixResources » sous **Groupe de ressources**. Cliquez sur le bouton **Nouveau…** à côté de « Plan App Service » et créez un nouveau plan App Service dans le même emplacement que celui que vous avez sélectionné pour le compte de stockage dans [Créer un compte de stockage](#Exercise1), en acceptant les valeurs par défaut partout ailleurs. Terminez en cliquant sur le bouton **Créer**.

    ![Création d’une application web Azure](Images/publish-2.png)

1. Après quelques instants, l’application s’affiche dans une fenêtre de navigateur. Prenez note de l’URL dans la barre d’adresse. L’application n’est plus exécutée localement ; elle s’exécute sur le web, où elle est accessible publiquement.

    ![Le produit fini !](Images/vs-intellipix.png)

Si vous apportez des modifications à l’application et que vous souhaitez les publier sur le web, recommencez le processus de publication. Vous pouvez toujours tester vos modifications localement avant de les publier sur le web.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez continuer à travailler sur votre application web, passez à la section [Étapes suivantes](#next-steps). Si vous n’avez plus besoin de cette application, supprimez toutes les ressources propres à l’application. Pour supprimer les ressources, vous pouvez supprimer le groupe de ressources qui contient votre abonnement Stockage Azure et la ressource Vision par ordinateur. Cette opération supprime le compte de stockage, les objets blob qui y ont été chargés ainsi que la ressource App Service utilisée pour vous connecter à l’application web ASP.NET. 

Pour supprimer le groupe de ressources, ouvrez l’onglet **Groupes de ressources** dans le portail, accédez au groupe de ressources que vous avez utilisé dans ce projet, puis cliquez sur **Supprimer le groupe de ressources** en haut de la vue. Il vous sera demandé de saisir le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer. Une fois supprimé, un groupe de ressources ne peut pas être récupéré.

## <a name="next-steps"></a>Étapes suivantes

Vous pourriez faire bien d’autres choses pour utiliser Azure et développer votre application Intellipix encore davantage. Par exemple, vous pourriez ajouter la prise en charge de l’authentification des utilisateurs et de la suppression des photos et, plutôt que de forcer l’utilisateur à attendre que Cognitive Services traite une photo après un chargement, vous pourriez utiliser [Azure Functions](https://azure.microsoft.com/services/functions/?WT.mc_id=academiccontent-github-cxa) pour appeler l’API Vision par ordinateur de façon asynchrone chaque fois qu’une image est ajoutée à Stockage Blob. Vous pourriez également effectuer un certain nombre d’autres opérations d’analyse d’image sur l’image, décrites dans la vue d’ensemble.

> [!div class="nextstepaction"]
> [Vue d’ensemble de l’analyse d’image](../overview-image-analysis.md)
