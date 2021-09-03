---
title: Bibliothèque de client C#/.NET ou Python de Traduction de documentation
titleSuffix: Azure Cognitive Services
description: Utiliser la bibliothèque de client C#/.NET ou Python Translator (kit de développement logiciel) pour le processus et le service de traduction de documentation en lot basés sur le cloud
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 07/06/2021
ms.author: lajanuar
ms.openlocfilehash: b7bcf5339f6bff6a0f055e2016bcd3e12bfd5f45
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562182"
---
# <a name="document-translation-client-library-sdks"></a>SDK de la bibliothèque de client de traduction de documentation
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD001 -->
La [Traduction de documentation](overview.md) est une fonctionnalité cloud du service [Azure Translator](../translator-overview.md). Vous pouvez traduire des documents entiers ou traiter des traductions de documentation par lot dans différents formats de fichier tout en conservant le format et la structure de document d’origine. Dans cet article, vous allez apprendre à utiliser les bibliothèques de client C#/.NET et Python du service de traduction de documentation. Pour les API REST, consultez notre guide [Démarrage rapide](get-started-with-document-translation.md).

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

* Un [**compte Azure**](https://azure.microsoft.com/free/cognitive-services/) actif.  Si vous n’en avez pas, vous pouvez [**créer un compte gratuit**](https://azure.microsoft.com/free/).

* Une [**ressource Translator de service unique**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) (et **pas** une ressource Cognitive Services multiservice).

* Un [**compte de stockage Blob Azure**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Vous devez [**créer des conteneurs**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) dans votre compte de stockage blob Azure pour les fichiers sources et cibles :

  * **Conteneur source**. Ce conteneur est l’emplacement où vous chargez vos fichiers pour la traduction (obligatoire).
  * **Conteneur cible**. Ce conteneur est l’emplacement où vos fichiers traduits seront stockés (obligatoire).

* Vous devez également créer des jetons de signature d’accès partagé (SAS) pour vos conteneurs source et cible. `sourceUrl` et `targetUrl` doivent inclure un jeton de signature d’accès partagé (SAS), ajouté comme chaîne de requête. Le jeton peut être attribué à votre conteneur ou à des blobs spécifiques. *Consultez* [ **Créer des jetons SAS pour le processus de Traduction de documentation**](create-sas-tokens.md).

  * Votre blob ou conteneur **source** doit disposer d’un accès **lecture**  et d’un accès **liste** désignés.
  * Votre blob ou conteneur **cible** doit disposer d’un accès **écriture** et d’un accès **liste** désignés.

Pour plus d’informations, *consultez* [Créer des jetons SAS](create-sas-tokens.md).

## <a name="client-libraries"></a>Bibliothèques clientes

### <a name="cnet"></a>[C#/.NET](#tab/csharp)

| [Package (NuGet)][documenttranslation_nuget_package] | [Bibliothèque de client][documenttranslation_client_library_docs] |  [API REST][documenttranslation_rest_api] | [Documentation produit][documenttranslation_docs] | [Exemples][documenttranslation_samples] |

> [!IMPORTANT]
> Il s’agit d’une version préliminaire du kit de développement logiciel (SDK) de traduction de documentation. Elle est disponible sous forme d’introduction afin que les clients puissent y accéder en avant-première et donner leur feedback. Les versions préliminaires sont toujours en cours de développement, sont sujettes à modification et certaines fonctionnalités peuvent ne pas être prises en charge ou peuvent avoir des capacités restreintes ; ne les utilisez pas dans les applications de production.

### <a name="set-up-your-project"></a>Configuration de votre projet

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `batch-document-translation`. Cette commande crée un projet C# simple nommé « Hello World » avec un seul fichier source : *program.cs*.

```console
dotnet new console -n batch-document-translation
```

Déplacez vos répertoires vers le dossier d’application nouvellement créé. Générez votre application à l’aide de la commande suivante :

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

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Dans le répertoire de l’application, installez la bibliothèque de client de traduction de documentation pour .NET à l’aide de l’une des méthodes suivantes :

#### <a name="net-cli"></a>**CLI .NET**

```console
dotnet add package Azure.AI.Translation.Document --version 1.0.0-beta.2
```

#### <a name="nuget-package-manager"></a>**Gestionnaire de package NuGet**

```console
Install-Package Azure.AI.Translation.Document -Version 1.0.0-beta.2
```

#### <a name="nuget-packagereference"></a>**NuGet PackageReference**

```xml
<ItemGroup>
    <!-- ... -->
<PackageReference Include="Azure.AI.Translation.Document" Version="1.0.0-beta.2" />
    <!-- ... -->
</ItemGroup>
```

À partir du répertoire de projet, ouvrez le fichier Program.cs dans votre éditeur ou votre IDE favori. Ajoutez les directives using suivantes :

```csharp
using Azure;
using Azure.AI.Translation.Document;

using System;
using System.Threading;
```

Dans la classe **Program** de l’application, créez une variable pour votre clé d’abonnement et votre point de terminaison du client. Pour plus de détail, *voir* [Nom de domaine personnalisé et clé d’abonnement](get-started-with-document-translation.md#custom-domain-name-and-subscription-key)

```csharp
private static readonly string endpoint = "<your custom endpoint>";
private static readonly string subscriptionKey = "<your subscription key>";
```

### <a name="translate-a-document-or-batch-files"></a>Traduire un document ou des fichiers par lot

* Pour démarrer une opération de traduction pour un ou plusieurs documents dans un seul conteneur blob, vous devez appeler la méthode `StartTranslationAsync`.

* Pour appeler `StartTranslationAsync` vous devez initialiser un objet `DocumentTranslationInput` qui contient les paramètres suivants :

* **sourceUri**. L’URI SAS du conteneur source contenant les documents à traduire.
* **targetUri** L’URI SAS du conteneur cible dans lequel les documents traduits seront écrits.
* **targetLanguageCode**. Le code de langue pour les documents traduits. Vous pouvez trouver les codes de langue sur notre page [Support multilingue](../language-support.md).

```csharp

public void StartTranslation() {
  Uri sourceUri = new Uri("<sourceUrl>");
  Uri targetUri = new Uri("<targetUrl>");

  DocumentTranslationClient client = new DocumentTranslationClient(new Uri(endpoint), new AzureKeyCredential(subscriptionKey));

  DocumentTranslationInput input = new DocumentTranslationInput(sourceUri, targetUri, "es")

  DocumentTranslationOperation operation = await client.StartTranslationAsync(input);

  await operation.WaitForCompletionAsync();

  Console.WriteLine($ "  Status: {operation.Status}");
  Console.WriteLine($ "  Created on: {operation.CreatedOn}");
  Console.WriteLine($ "  Last modified: {operation.LastModified}");
  Console.WriteLine($ "  Total documents: {operation.DocumentsTotal}");
  Console.WriteLine($ "    Succeeded: {operation.DocumentsSucceeded}");
  Console.WriteLine($ "    Failed: {operation.DocumentsFailed}");
  Console.WriteLine($ "    In Progress: {operation.DocumentsInProgress}");
  Console.WriteLine($ "    Not started: {operation.DocumentsNotStarted}");

  await foreach(DocumentStatusResult document in operation.Value) {
    Console.WriteLine($ "Document with Id: {document.DocumentId}");
    Console.WriteLine($ "  Status:{document.Status}");
    if (document.Status == TranslationStatus.Succeeded) {
      Console.WriteLine($ "  Translated Document Uri: {document.TranslatedDocumentUri}");
      Console.WriteLine($ "  Translated to language: {document.TranslatedTo}.");
      Console.WriteLine($ "  Document source Uri: {document.SourceDocumentUri}");
    }
    else {
      Console.WriteLine($ "  Error Code: {document.Error.ErrorCode}");
      Console.WriteLine($ "  Message: {document.Error.Message}");
    }
  }
}
```

Et voilà ! Vous avez créé un programme pour traduire des documents dans un conteneur blob à l’aide de la bibliothèque de client .NET.

### <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
 > [**Découvrir d’autres exemples de code C#** ](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.Translation.Document_1.0.0-beta.2/sdk/translation/Azure.AI.Translation.Document/samples)

<!-- LINKS -->

[documenttranslation_nuget_package]: https://www.nuget.org/packages/Azure.AI.Translation.Document/1.0.0-beta.2
[documenttranslation_client_library_docs]: /dotnet/api/azure.ai.translation.document
[documenttranslation_docs]: overview.md
[documenttranslation_rest_api]: reference/rest-api-guide.md
[documenttranslation_samples]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/translation/Azure.AI.Translation.Document/samples

### <a name="python"></a>[Python](#tab/python)

| [Package (PyPI)][python-dt-pypi] |  [Bibliothèque de client][python-dt-client-library] |  [API REST][python-rest-api] | [Documentation produit][python-dt-product-docs] | [Exemples][python-dt-samples] |

> [!IMPORTANT]
> Il s’agit d’une version préliminaire du kit de développement logiciel (SDK) de traduction de documentation. Elle est disponible sous forme d’introduction afin que les clients puissent y accéder en avant-première et donner leur feedback. Les versions préliminaires sont toujours en cours de développement, sont sujettes à modification et certaines fonctionnalités peuvent ne pas être prises en charge ou peuvent avoir des capacités restreintes ; ne les utilisez pas dans les applications de production.

### <a name="set-up-your-project"></a>Configuration de votre projet

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Si cela n’est pas le cas, installez [Python](https://www.python.org/downloads/), puis installez la dernière version de la bibliothèque de client Translator :

```console
pip install azure-ai-translation-document
```

### <a name="create-your-application"></a>Créer votre application

Créez une application Python dans votre éditeur ou IDE favori. Importez ensuite les bibliothèques suivantes.

```python
    import os
    from azure.core.credentials import AzureKeyCredential
    from azure.ai.translation.document import DocumentTranslationClient
```

Créer des variables pour votre clé d’abonnement aux ressources, point de terminaison personnalisé, sourceUrl et targetUrl. Pour plus d’informations, *voir* [Nom de domaine personnalisé et clé d’abonnement](get-started-with-document-translation.md#custom-domain-name-and-subscription-key)

```python
 subscriptionKey = "<your-subscription-key>"
 endpoint = "<your-custom-endpoint>"
 sourceUrl = "<your-container-sourceUrl>"
 targetUrl = "<your-container-targetUrl>"
```

### <a name="translate-a-document-or-batch-files"></a>Traduire un document ou des fichiers par lot

```python
client = DocumentTranslationClient(endpoint, AzureKeyCredential(subscriptionKey))

    poller = client.begin_translation(sourceUrl, targetUrl, "fr")
    result = poller.result()

    print("Status: {}".format(poller.status()))
    print("Created on: {}".format(poller.details.created_on))
    print("Last updated on: {}".format(poller.details.last_updated_on))
    print("Total number of translations on documents: {}".format(poller.details.documents_total_count))

    print("\nOf total documents...")
    print("{} failed".format(poller.details.documents_failed_count))
    print("{} succeeded".format(poller.details.documents_succeeded_count))

    for document in result:
        print("Document ID: {}".format(document.id))
        print("Document status: {}".format(document.status))
        if document.status == "Succeeded":
            print("Source document location: {}".format(document.source_document_url))
            print("Translated document location: {}".format(document.translated_document_url))
            print("Translated to language: {}\n".format(document.translated_to))
        else:
            print("Error Code: {}, Message: {}\n".format(document.error.code, document.error.message))
```

Et voilà ! Vous avez créé un programme pour traduire des documents dans un conteneur blob en utilisant la bibliothèque de client Python.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrir plus d’exemples de code Python](https://github.com/Azure/azure-sdk-for-python/tree/azure-ai-translation-document_1.0.0b1/sdk/translation/azure-ai-translation-document/samples)

<!-- LINKS -->
[python-dt-pypi]: https://aka.ms/azsdk/python/texttranslation/pypi
[python-dt-client-library]: https://aka.ms/azsdk/python/documenttranslation/docs
[python-rest-api]: reference/rest-api-guide.md
[python-dt-product-docs]: overview.md
[python-dt-samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/translation/azure-ai-translation-document/samples

---