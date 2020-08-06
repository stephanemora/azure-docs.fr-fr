---
title: Présentation de l’analyseur de modèle de jumeaux numériques | Microsoft Docs
description: En tant que développeur, apprenez à utiliser l’analyseur DTDL pour valider des modèles
author: rido-min
ms.author: rmpablos
ms.date: 04/29/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: peterpr
ms.openlocfilehash: 20c4452a32c791f33e08c883d8cec89a345ab188
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351971"
---
# <a name="understand-the-digital-twins-model-parser"></a>Présentation de l’analyseur de modèle de jumeaux numériques

Le langage DTDL (Digital représentations Definition Language) est décrit dans la [spécification DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Les utilisateurs peuvent utiliser le package NuGet _Digital Twins Model Parser_ pour valider et interroger un modèle défini dans plusieurs fichiers.

## <a name="install-the-dtdl-model-parser"></a>Installer l’analyseur de modèle DTDL

L’analyseur est accessible à l’adresse NuGet.org avec l’ID : [Microsoft.Azure.DigitalTwins.Parser](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser). Pour installer l’analyseur, utilisez un gestionnaire de package NuGet compatible, tel que celui de Visual Studio ou de l’interface de ligne de commande `dotnet`.

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

## <a name="use-the-parser-to-validate-a-model"></a>Utiliser l’analyseur pour valider un modèle

Le modèle que vous souhaitez valider peut être composé d’une ou de plusieurs interfaces décrites dans des fichiers JSON. Vous pouvez utiliser l’analyseur pour charger tous les fichiers d’un dossier donné et pour valider tous les fichiers dans leur ensemble, y compris les références entre les fichiers :

1. Créez un `IEnumerable<string>` avec une liste de tous les contenus du modèle :

    ```csharp
    using System.IO;

    string folder = @"c:\myModels\";
    string filespec = "*.json";

    List<string> modelJson = new List<string>();
    foreach (string filename in Directory.GetFiles(folder, filespec))
    {
        using StreamReader modelReader = new StreamReader(filename);
        modelJson.Add(modelReader.ReadToEnd());
    }
    ```

1. Instanciez le `ModelParser` et appelez `ParseAsync` :

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. Vérifiez s’il y a des erreurs de validation. Si l’analyseur détecte des erreurs, il lève une exception `AggregateException` avec une liste de messages d’erreur détaillés :

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (AggregateException ae)
    {
        foreach (var e in ae.InnerExceptions)
        {
            Console.WriteLine(e.Message);
        }
    }
    ```

1. Examinez le `Model`. Si la validation est réussie, vous pouvez utiliser l’API de l’analyseur de modèle pour examiner le modèle. L’extrait de code suivant montre comment itérer sur tous les modèles analysés et affiche les propriétés existantes :

    ```csharp
    foreach (var m in parseResult)
    {
        Console.WriteLine(m.Key);
        foreach (var item in m.Value.AsEnumerable<DTEntityInfo>())
        {
            var p = item as DTInterfaceInfo;
            if (p!=null)
            {
                Console.WriteLine($"\t{p.Id}");
                Console.WriteLine($"\t{p.Description.FirstOrDefault()}");
            }
            Console.WriteLine("--------------");
        }
    }
    ```

## <a name="next-steps"></a>Étapes suivantes

L’API de l’analyseur de modèle examinée dans cet article permet à de nombreux scénarios d’automatiser ou de valider des tâches qui dépendent de modèles DTDL. Par exemple, vous pouvez créer dynamiquement une interface utilisateur à partir des informations du modèle.
