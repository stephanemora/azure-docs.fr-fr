---
title: Modifier, effectuer l’apprentissage d’une application, C#
titleSuffix: Language Understanding - Azure Cognitive Services
description: Dans ce démarrage rapide de C#, vous allez ajouter des exemples d’énoncés à une application de domotique et effectuer l’apprentissage de l’application.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 12/17/2018
ms.author: diberry
ms.openlocfilehash: fb976842f3ea6f5df81c795b3be775ed57798b90
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870943"
---
# <a name="quickstart-change-model-using-c"></a>Démarrage rapide : Modifier un modèle à l’aide de C#

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* La dernière [**édition de Visual Studio Community**](https://www.visualstudio.com/downloads/).
* Le langage de programmation C# doit être installé.
* Packages NuGet [JsonFormatterPlus](https://www.nuget.org/packages/JsonFormatterPlus) et [CommandLine](https://www.nuget.org/packages/CommandLineParser/)

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Exemples d’énoncés de fichier JSON

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Créer un code de démarrage rapide 

Dans Visual Studio, créez une application **Console de bureau Windows classique** à l’aide de .Net Framework. 

![Type de projet Visual Studio](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

### <a name="add-the-systemweb-dependency"></a>Ajouter la dépendance System.Web

Le projet Visual Studio a besoin d’une référence **System.Web**. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **Références**, puis sélectionnez **Ajouter une référence**.

![Ajouter une référence System.web](./media/luis-quickstart-cs-add-utterance/system.web.png)

### <a name="add-other-dependencies"></a>Ajouter d’autres dépendances

Le projet Visual Studio a besoin des packages **JsonFormatterPlus** et **CommandLineParser**. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **Références**, puis sélectionnez **Gérer les packages NuGet**. Recherchez et ajoutez les deux packages. 

![Ajouter des dépendances tierces](./media/luis-quickstart-cs-add-utterance/add-dependencies.png)


### <a name="write-the-c-code"></a>Écrire le code C#
Le fichier **Program.cs** doit être comme suit :

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp3
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Ajoutez les dépendances.

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=1-11 "Add the dependencies")]


Ajoutez les chaînes et ID LUIS à la classe **Program**.

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=19-30&dedent=8 "Add the LUIS IDs and strings")]

Ajoutez une classe de gestion des paramètres de ligne de commande à la classe **Program**.

   [!code-csharp[Add class to manage command line parameters.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=32-46 "Add class to manage command-line parameters.")]

Ajoutez la méthode de requête GET à la classe **Program**.

   [!code-csharp[Add the GET request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=49-59 "Add the GET request.")]


Ajoutez la méthode de requête POST à la classe **Program**. 

   [!code-csharp[Add the POST request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=60-76 "Add the POST request.")]

Ajoutez des exemples d’énoncés à partir de la méthode fichier à la classe **Program**.

   [!code-csharp[Add example utterances from file.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=77-86 "Add example utterances from file.")]

Une fois que les modifications ont été appliquées au modèle, effectuez l’apprentissage du modèle. Ajoutez la méthode à la classe **Program**.

   [!code-csharp[After the changes are applied to the model, train the model.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=87-96 "After the changes are applied to the model, train the model.")]

L’apprentissage peut prendre un certain temps. Vérifiez l’état pour vous assurer que le processus d’apprentissage est terminé. Ajoutez la méthode à la classe **Program**.

   [!code-csharp[Training may not complete immediately, check status to verify training is complete.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=97-103 "Training may not complete immediately, check status to verify training is complete.")]

Pour gérer les arguments de ligne de commande, ajoutez le code principal. Ajoutez la méthode à la classe **Program**.

   [!code-csharp[To manage command line arguments, add the main code.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=104-137 "To manage command-line arguments, add the main code.")]

### <a name="copy-utterancesjson-to-output-directory"></a>Copier le fichier utterances.json dans le répertoire de sortie

Dans l’Explorateur de solutions, cliquez avec le bouton droit sur `utterances.json` et sélectionnez **Propriétés**. Dans la fenêtre Propriétés, marquez l’élément **Action de génération** de `Content` et l’élément **Copier dans le répertoire de sortie** de `Copy Always`.  

![Marquer le fichier JSON en tant que contenu](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="build-code"></a>Générer le code

Générez le code dans Visual Studio. 

## <a name="run-code"></a>Exécuter le code

Exécutez l’application à partir d’une ligne de commande dans le répertoire /bin/Debug du projet. 

```console
ConsoleApp\bin\Debug> ConsoleApp1.exe --add utterances.json --train --status
```

Cette ligne de commande affiche les résultats de l’appel de l’API d’ajout d’énoncés. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources
Une fois le démarrage rapide terminé, supprimez tous les fichiers créés pour ce démarrage rapide. 

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Créer une application LUIS par programme](luis-tutorial-node-import-utterances-csv.md) 
