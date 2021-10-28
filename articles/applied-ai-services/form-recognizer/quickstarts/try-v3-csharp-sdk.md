---
title: 'Démarrage rapide : Form Recognizer v3.0 avec kit SDK C# | Préversion'
titleSuffix: Azure Applied AI Services
description: Traitement, extraction des données et analyse des formulaires et des documents à l’aide de Form Recognizer v3.0 avec les kits SDK de la bibliothèque de client C# (préversion)
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 50c1cfbcfc79212f03fd67f783afaff110ce1e09
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130216729"
---
# <a name="quickstart-form-recognizer-c-client-library-sdks-v30--preview"></a>Démarrage rapide : Form Recognizer v3.0 avec les kits SDK de la bibliothèque de client C# | Préversion

Bien démarrer avec Azure Form Recognizer en utilisant le langage de programmation C#. Azure Form Recognizer est un service cloud [Azure Applied AI Services](../../../applied-ai-services/index.yml) qui vous permet de générer des logiciels de traitement de données automatisés à l’aide de la technologie du machine learning. Vous pouvez utiliser Form Recognizer via l’API REST ou un kit SDK. Nous vous recommandons d’utiliser le service gratuit pendant que vous apprenez la technologie. N’oubliez pas que le nombre de pages gratuites est limité à 500 par mois.

>[!NOTE]
> Form Recognizer v3.0 est actuellement en préversion publique. Certaines fonctionnalités risquent de ne pas être prises en charge ou d’avoir des capacités limitées. 

[Documentation de référence](/dotnet/api/overview/azure/ai.formrecognizer-readme?view=azure-dotnet&preserve-view=true ) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [Package (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [Exemples](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

Azure Cognitive Services Form Recognizer est un service cloud qui utilise le machine learning pour extraire et analyser les champs de formulaire, le texte et les tableaux de vos documents. Vous pouvez facilement appeler des modèles Form Recognizer en intégrant les kits SDK de notre bibliothèque de client dans vos workflows et applications.

### <a name="form-recognizer-models"></a>Modèles Form Recognizer

Le kit SDK C# prend en charge les modèles et les fonctionnalités suivants :

* 🆕Document général – Analysez et extrayez le texte, les tableaux, la structure, les paires clé-valeur et les entités nommées.|
* Disposition – Analysez et extrayez les tableaux, les lignes, les mots et les marques de sélection telles que les cases d’option et les cases à cocher dans des formulaires, sans avoir besoin d’entraîner un modèle.
* Personnalisé – Analysez et extrayez les champs de formulaire et d’autres contenus de vos formulaires personnalisés en utilisant des modèles que vous avez entraînés avec vos propres types de formulaires.
* Factures – Analysez et extrayez les champs courants des factures en utilisant un modèle de facture préentraîné.
* Reçus – Analysez et extrayez les champs courants des reçus en utilisant un modèle de reçu préentraîné.
* Documents d’identité – Analysez et extrayez les champs courants des documents d’identité, tels que les passeports ou les permis de conduire, en utilisant un modèle de pièce d’identité préentraîné.
* Cartes de visite – Analysez et extrayez les champs courants des cartes de visite en utilisant un modèle de carte de visite préentraîné.

Dans ce guide de démarrage rapide, vous allez utiliser les fonctionnalités suivantes pour analyser et extraire les données et les valeurs de formulaires et de documents :

* [**Document général**](#try-it-general-document-model)

* [**Layout**](#try-it-layout-model)

* [**Facture prédéfinie**](#try-it-prebuilt-invoice-model)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)

* La version actuelle de l’[IDE Visual Studio](https://visualstudio.microsoft.com/vs/) ou [.NET Core](https://dotnet.microsoft.com/download).

* Une ressource Cognitive Services ou Form Recognizer. Une fois que vous avez votre abonnement Azure, créez une ressource Form Recognizer [monoservice](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) ou [multiservice](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) dans le portail Azure pour obtenir votre clé et votre point de terminaison. Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

> [!TIP] 
> Créez une ressource Cognitive Services si vous envisagez d’accéder à plusieurs services Cognitive Services sous un seul point de terminaison/clé. Pour l’accès à Form Recognizer uniquement, créez une ressource Form Recognizer. Notez que vous avez besoin d’une ressource monoservice si vous avez l’intention d’utiliser l’[authentification Azure Active Directory](../../../active-directory/authentication/overview-authentication.md).

* Une fois que votre ressource est déployée, cliquez sur **Accéder à la ressource**. Vous avez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Form Recognizer. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans ce guide de démarrage rapide :

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : clés et emplacement du point de terminaison dans le portail Azure":::

## <a name="set-up"></a>Configurer

### <a name="option-1-net-command-line-interface-cli"></a>[Option 1 : interface de ligne de commande (CLI) .NET](#tab/cli)

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `formrecognizer-quickstart`. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : *Program.cs*.

```console
dotnet new console -n formrecognizer-quickstart
```

Ouvrez une ligne de commande et accédez au répertoire contenant votre fichier projet. Générez l’application avec :

```console
dotnet build
```

La sortie de génération ne doit contenir aucun avertissement ni erreur.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library-with-nuget"></a>Installer la bibliothèque de client avec NuGet

Dans le répertoire qui contient votre projet, installez la bibliothèque de client Form Recognizer pour .NET avec la commande suivante :

```console
dotnet add package Azure.AI.FormRecognizer
```

Cette version de la bibliothèque de client correspond par défaut à la version 2021-09-30-preview du service.

### <a name="option-2-visual-studio"></a>[Option 2 : Visual Studio](#tab/vs)

1. Démarrez Visual Studio 2019.

1. Dans la page de démarrage, choisissez Créer un projet.

    :::image type="content" source="../media/quickstarts/start-window.png" alt-text="Capture d’écran : fenêtre de démarrage Visual Studio":::

1. Dans la page **Créer un nouveau projet**, entrez **console** dans la zone de recherche. Choisissez le modèle **Application console**, puis choisissez **Suivant**.

    :::image type="content" source="../media/quickstarts/create-new-project.png" alt-text="Capture d’écran : page Créer un nouveau projet dans Visual Studio":::

1. Dans la fenêtre de dialogue **Configurer votre nouveau projet**, entrez `formRecognizer_quickstart` dans la zone Nom de projet. Ensuite, choisissez Suivant.

    :::image type="content" source="../media/quickstarts/configure-new-project.png" alt-text="Capture d’écran : fenêtre de dialogue Configurer un nouveau projet dans Visual Studio":::

1. Dans la fenêtre de dialogue **Informations supplémentaires**, sélectionnez **.NET 5.0 (Actuel)** , puis sélectionnez **Créer**.

    :::image type="content" source="../media/quickstarts/additional-information.png" alt-text="Capture d’écran : fenêtre de dialogue Informations supplémentaires dans Visual Studio":::

### <a name="install-the-client-library-with-nuget"></a>Installer la bibliothèque de client avec NuGet

 1. Cliquez avec le bouton droit sur votre projet **formRecognizer_quickstart** et sélectionnez **Gérer les packages NuGet**.

    :::image type="content" source="../media/quickstarts/select-nuget-package.png" alt-text="Capture d’écran : select-nuget-package.png":::

 1. Sélectionnez l’onglet Parcourir et tapez Azure.AI.FormRecognizer.

     :::image type="content" source="../media/quickstarts/azure-nuget-package.png" alt-text="Capture d’écran : select-form-recognizer-package.png":::

 1. Sélectionnez la version souhaitée dans le menu déroulant et sélectionnez **Installer**.

     Cette version de la bibliothèque de client correspond par défaut à la version 2021-09-30-preview du service.

---

## <a name="build-your-application"></a>Générer votre application

Pour interagir avec le service Form Recognizer, vous devez créer une instance de la classe `DocumentAnalysisClient`. Pour ce faire, vous allez créer un `AzureKeyCredential` avec votre apiKey et une instance `DocumentAnalysisClient` avec le `AzureKeyCredential` et votre `endpoint` de Form Recognizer.

1. Ouvrez le fichier **Program.cs**.

1. Insérez les directives using suivantes :

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.AI.FormRecognizer.DocumentAnalysis;
    ```

1. Définissez vos variables d’environnement `endpoint` et `apiKey`, et créez vos instances `AzureKeyCredential` et `DocumentAnalysisClient` :

    ```csharp
    string endpoint = "<your-endpoint>";
    string apiKey = "<your-apiKey>";
    var credential = new AzureKeyCredential(apiKey);
    var client = new DocumentAnalysisClient(new Uri(endpoint), credential);
    ```

1. Supprimez la ligne `Console.Writeline("Hello World!");` et ajoutez le code **Essayez** dans la méthode **Main** du fichier **Program.cs** :

    :::image type="content" source="../media/quickstarts/add-code-here.png" alt-text="Capture d’écran : ajout de l’exemple de code à la méthode Main":::

## <a name="try-it-general-document-model"></a>**Essayez** : modèle de document général

> [!div class="checklist"]
>
> * Pour cet exemple, vous aurez besoin d’un **fichier de formulaire au niveau d’un URI**. Vous pouvez utiliser notre [exemple de formulaire](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) pour ce guide de démarrage rapide.
> * Ajoutez la valeur de l’URI du fichier à la variable `string fileUri` en haut de la méthode Main.
> * Pour analyser un fichier donné au niveau d’un URI, vous utiliserez la méthode `StartAnalyzeDocumentFromUri`. La valeur retournée est un objet `AnalyzeResult` contenant des données relatives au document envoyé.

### <a name="add-the-following-code-to-your-general-document-application-main-method"></a>Ajouter le code suivant à la méthode **Main** de votre application de document général

```csharp
// sample form document
string fileUri = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";

AnalyzeDocumentOperation operation = await client.StartAnalyzeDocumentFromUriAsync("prebuilt-document", fileUri);

await operation.WaitForCompletionAsync();

AnalyzeResult result = operation.Value;

Console.WriteLine("Detected entities:");

foreach (DocumentEntity entity in result.Entities)
{
    if (entity.SubCategory == null)
    {
        Console.WriteLine($"  Found entity '{entity.Content}' with category '{entity.Category}'.");
    }
    else
    {
        Console.WriteLine($"  Found entity '{entity.Content}' with category '{entity.Category}' and sub-category '{entity.SubCategory}'.");
    }
}

Console.WriteLine("Detected key-value pairs:");

foreach (DocumentKeyValuePair kvp in result.KeyValuePairs)
{
    if (kvp.Value.Content == null)
    {
        Console.WriteLine($"  Found key with no value: '{kvp.Key.Content}'");
    }
    else
    {
        Console.WriteLine($"  Found key-value pair: '{kvp.Key.Content}' and '{kvp.Value.Content}'");
    }
}

foreach (DocumentPage page in result.Pages)
{
    Console.WriteLine($"Document Page {page.PageNumber} has {page.Lines.Count} line(s), {page.Words.Count} word(s),");
    Console.WriteLine($"and {page.SelectionMarks.Count} selection mark(s).");

    for (int i = 0; i < page.Lines.Count; i++)
    {
        DocumentLine line = page.Lines[i];
        Console.WriteLine($"  Line {i} has content: '{line.Content}'.");

        Console.WriteLine($"    Its bounding box is:");
        Console.WriteLine($"      Upper left => X: {line.BoundingBox[0].X}, Y= {line.BoundingBox[0].Y}");
        Console.WriteLine($"      Upper right => X: {line.BoundingBox[1].X}, Y= {line.BoundingBox[1].Y}");
        Console.WriteLine($"      Lower right => X: {line.BoundingBox[2].X}, Y= {line.BoundingBox[2].Y}");
        Console.WriteLine($"      Lower left => X: {line.BoundingBox[3].X}, Y= {line.BoundingBox[3].Y}");
    }

    for (int i = 0; i < page.SelectionMarks.Count; i++)
    {
        DocumentSelectionMark selectionMark = page.SelectionMarks[i];

        Console.WriteLine($"  Selection Mark {i} is {selectionMark.State}.");
        Console.WriteLine($"    Its bounding box is:");
        Console.WriteLine($"      Upper left => X: {selectionMark.BoundingBox[0].X}, Y= {selectionMark.BoundingBox[0].Y}");
        Console.WriteLine($"      Upper right => X: {selectionMark.BoundingBox[1].X}, Y= {selectionMark.BoundingBox[1].Y}");
        Console.WriteLine($"      Lower right => X: {selectionMark.BoundingBox[2].X}, Y= {selectionMark.BoundingBox[2].Y}");
        Console.WriteLine($"      Lower left => X: {selectionMark.BoundingBox[3].X}, Y= {selectionMark.BoundingBox[3].Y}");
    }
}

foreach (DocumentStyle style in result.Styles)
{
    // Check the style and style confidence to see if text is handwritten.
    // Note that value '0.8' is used as an example.

    bool isHandwritten = style.IsHandwritten.HasValue && style.IsHandwritten == true;

    if (isHandwritten && style.Confidence > 0.8)
    {
        Console.WriteLine($"Handwritten content found:");

        foreach (DocumentSpan span in style.Spans)
        {
            Console.WriteLine($"  Content: {result.Content.Substring(span.Offset, span.Length)}");
        }
    }
}

Console.WriteLine("The following tables were extracted:");

for (int i = 0; i < result.Tables.Count; i++)
{
    DocumentTable table = result.Tables[i];
    Console.WriteLine($"  Table {i} has {table.RowCount} rows and {table.ColumnCount} columns.");

    foreach (DocumentTableCell cell in table.Cells)
    {
        Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex}) has kind '{cell.Kind}' and content: '{cell.Content}'.");
    }
}

```

## <a name="try-it-layout-model"></a>**Essayez** : modèle de disposition

Extrayez le texte, les marques de sélection, les styles de texte et les structures de tableaux, ainsi que les coordonnées de leurs cadres de limitation, à partir de documents.

> [!div class="checklist"]
>
> * Pour cet exemple, vous aurez besoin d’un **fichier de formulaire au niveau d’un URI**. Vous pouvez utiliser notre [exemple de formulaire](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) pour ce guide de démarrage rapide.
> * Ajoutez la valeur de l’URI du fichier à la variable `string fileUri` en haut de la méthode Main.
> * Pour extraire la disposition d’un fichier donné au niveau d’un URI, utilisez la méthode `StartAnalyzeDocumentFromUri` et transmettez `prebuilt-layout` comme ID de modèle. La valeur retournée est un objet `AnalyzeResult` contenant des données provenant du document envoyé.

### <a name="add-the-following-code-to-your-layout-application-main-method"></a>Ajouter le code suivant à la méthode **Main** de votre application de disposition

```csharp
// sample form document
string fileUri = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";

AnalyzeDocumentOperation operation = await client.StartAnalyzeDocumentFromUriAsync("prebuilt-layout", fileUri);

await operation.WaitForCompletionAsync();

AnalyzeResult result = operation.Value;

foreach (DocumentPage page in result.Pages)
{
    Console.WriteLine($"Document Page {page.PageNumber} has {page.Lines.Count} line(s), {page.Words.Count} word(s),");
    Console.WriteLine($"and {page.SelectionMarks.Count} selection mark(s).");

    for (int i = 0; i < page.Lines.Count; i++)
    {
        DocumentLine line = page.Lines[i];
        Console.WriteLine($"  Line {i} has content: '{line.Content}'.");

        Console.WriteLine($"    Its bounding box is:");
        Console.WriteLine($"      Upper left => X: {line.BoundingBox[0].X}, Y= {line.BoundingBox[0].Y}");
        Console.WriteLine($"      Upper right => X: {line.BoundingBox[1].X}, Y= {line.BoundingBox[1].Y}");
        Console.WriteLine($"      Lower right => X: {line.BoundingBox[2].X}, Y= {line.BoundingBox[2].Y}");
        Console.WriteLine($"      Lower left => X: {line.BoundingBox[3].X}, Y= {line.BoundingBox[3].Y}");
    }

    for (int i = 0; i < page.SelectionMarks.Count; i++)
    {
        DocumentSelectionMark selectionMark = page.SelectionMarks[i];

        Console.WriteLine($"  Selection Mark {i} is {selectionMark.State}.");
        Console.WriteLine($"    Its bounding box is:");
        Console.WriteLine($"      Upper left => X: {selectionMark.BoundingBox[0].X}, Y= {selectionMark.BoundingBox[0].Y}");
        Console.WriteLine($"      Upper right => X: {selectionMark.BoundingBox[1].X}, Y= {selectionMark.BoundingBox[1].Y}");
        Console.WriteLine($"      Lower right => X: {selectionMark.BoundingBox[2].X}, Y= {selectionMark.BoundingBox[2].Y}");
        Console.WriteLine($"      Lower left => X: {selectionMark.BoundingBox[3].X}, Y= {selectionMark.BoundingBox[3].Y}");
    }
}

foreach (DocumentStyle style in result.Styles)
{
    // Check the style and style confidence to see if text is handwritten.
    // Note that value '0.8' is used as an example.

    bool isHandwritten = style.IsHandwritten.HasValue && style.IsHandwritten == true;

    if (isHandwritten && style.Confidence > 0.8)
    {
        Console.WriteLine($"Handwritten content found:");

        foreach (DocumentSpan span in style.Spans)
        {
            Console.WriteLine($"  Content: {result.Content.Substring(span.Offset, span.Length)}");
        }
    }
}

Console.WriteLine("The following tables were extracted:");

for (int i = 0; i < result.Tables.Count; i++)
{
    DocumentTable table = result.Tables[i];
    Console.WriteLine($"  Table {i} has {table.RowCount} rows and {table.ColumnCount} columns.");

    foreach (DocumentTableCell cell in table.Cells)
    {
        Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex}) has kind '{cell.Kind}' and content: '{cell.Content}'.");
    }
}

```

## <a name="try-it-prebuilt-invoice-model"></a>**Essayez** : modèle de facture prédéfinie

Cet exemple montre comment analyser les données de certains types de documents courants avec des modèles préentraînés, à l’aide d’une facture à titre d’exemple.

> [!div class="checklist"]
>
> * Pour cet exemple, vous aurez besoin d’un **fichier de facture au niveau d’un URI**. Vous pouvez utiliser notre [exemple de facture](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) pour ce guide de démarrage rapide.
> * Ajoutez la valeur de l’URI du fichier à la variable `string fileUri` en haut de la méthode Main.
> * Pour analyser un fichier donné au niveau d’un URI, utilisez la méthode `StartAnalyzeDocumentFromUri` et transmettez-lui `prebuilt-invoice` comme ID de modèle. La valeur retournée est un objet `AnalyzeResult` contenant des données provenant du document envoyé.

### <a name="choose-the-invoice-prebuilt-model-id"></a>Choisir l’ID de modèle de facture prédéfinie

Vous n’êtes pas limité aux factures – Vous avez le choix entre plusieurs modèles prédéfinis, chacun ayant son propre ensemble de champs pris en charge. Le modèle à utiliser pour l’opération d’analyse dépend du type de document à analyser. Voici les ID de modèle pour les modèles prédéfinis actuellement pris en charge par le service Form Recognizer :

* **prebuilt-invoice** : extrait le texte, les marques de sélection, les tableaux, les paires clé-valeur et les informations clés des factures.
* **prebuilt-businessCard** : extrait le texte et les informations clés des cartes de visite.
* **prebuilt-idDocument** : extrait le texte et les informations clés des permis de conduire et des passeports internationaux.
* **prebuilt-receipt** : extrait le texte et les informations clés des reçus.

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-main-method"></a>Ajouter le code suivant à la méthode **Main** de votre application de facture prédéfinie

```csharp
// sample invoice document
string filePath = "(https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

using var stream = new FileStream(filePath, FileMode.Open);

AnalyzeDocumentOperation operation = await client.StartAnalyzeDocumentAsync("prebuilt-invoice", stream);

await operation.WaitForCompletionAsync();

AnalyzeResult result = operation.Value;

for (int i = 0; i < result.Documents.Count; i++)
{
    Console.WriteLine($"Document {i}:");

    AnalyzedDocument document = result.Documents[i];

    if (document.Fields.TryGetValue("VendorName", out DocumentField vendorNameField))
    {
        if (vendorNameField.ValueType == DocumentFieldType.String)
        {
            string vendorName = vendorNameField.AsString();
            Console.WriteLine($"Vendor Name: '{vendorName}', with confidence {vendorNameField.Confidence}");
        }
    }

    if (document.Fields.TryGetValue("CustomerName", out DocumentField customerNameField))
    {
        if (customerNameField.ValueType == DocumentFieldType.String)
        {
            string customerName = customerNameField.AsString();
            Console.WriteLine($"Customer Name: '{customerName}', with confidence {customerNameField.Confidence}");
        }
    }

    if (document.Fields.TryGetValue("Items", out DocumentField itemsField))
    {
        if (itemsField.ValueType == DocumentFieldType.List)
        {
            foreach (DocumentField itemField in itemsField.AsList())
            {
                Console.WriteLine("Item:");

                if (itemField.ValueType == DocumentFieldType.Dictionary)
                {
                    IReadOnlyDictionary<string, DocumentField> itemFields = itemField.AsDictionary();

                    if (itemFields.TryGetValue("Description", out DocumentField itemDescriptionField))
                    {
                        if (itemDescriptionField.ValueType == DocumentFieldType.String)
                        {
                            string itemDescription = itemDescriptionField.AsString();

                            Console.WriteLine($"  Description: '{itemDescription}', with confidence {itemDescriptionField.Confidence}");
                        }
                    }

                    if (itemFields.TryGetValue("Amount", out DocumentField itemAmountField))
                    {
                        if (itemAmountField.ValueType == DocumentFieldType.Double)
                        {
                            double itemAmount = itemAmountField.AsDouble();

                            Console.WriteLine($"  Amount: '{itemAmount}', with confidence {itemAmountField.Confidence}");
                        }
                    }
                }
            }
        }
    }

    if (document.Fields.TryGetValue("SubTotal", out DocumentField subTotalField))
    {
        if (subTotalField.ValueType == DocumentFieldType.Double)
        {
            double subTotal = subTotalField.AsDouble();
            Console.WriteLine($"Sub Total: '{subTotal}', with confidence {subTotalField.Confidence}");
        }
    }

    if (document.Fields.TryGetValue("TotalTax", out DocumentField totalTaxField))
    {
        if (totalTaxField.ValueType == DocumentFieldType.Double)
        {
            double totalTax = totalTaxField.AsDouble();
            Console.WriteLine($"Total Tax: '{totalTax}', with confidence {totalTaxField.Confidence}");
        }
    }

    if (document.Fields.TryGetValue("InvoiceTotal", out DocumentField invoiceTotalField))
    {
        if (invoiceTotalField.ValueType == DocumentFieldType.Double)
        {
            double invoiceTotal = invoiceTotalField.AsDouble();
            Console.WriteLine($"Invoice Total: '{invoiceTotal}', with confidence {invoiceTotalField.Confidence}");
        }
    }
}

```

## <a name="run-your-application"></a>Exécuter votre application

### <a name="net-command-line-interface-cli"></a>[Interface de ligne de commande (CLI) .NET](#tab/cli)

Ouvrez votre invite de commandes et accédez au répertoire qui contient votre projet, et tapez ce qui suit :

```console
dotnet run formrecognizer-quickstart.dll
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

Choisissez le bouton vert **Démarrer** en regard de formRecognizer_quickstart pour générer et exécuter votre programme, ou appuyez sur **F5**.

  :::image type="content" source="../media/quickstarts/run-visual-studio.png" alt-text="Capture d’écran : exécutez votre programme Visual Studio":::

---

Félicitations ! Dans ce guide de démarrage rapide, vous avez utilisé Form Recognizer avec le kit SDK C# pour analyser divers formulaires et documents de différentes manières. Explorez à présent la documentation de référence pour découvrir plus en détail l’API Form Recognizer.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence de l’API REST v3.0](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)