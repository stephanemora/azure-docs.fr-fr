---
title: Créer des désérialiseurs .NET personnalisés pour les travaux cloud Azure Stream Analytics à l’aide de Visual Studio Code
description: Ce tutoriel montre comment créer un désérialiseur .NET personnalisé pour un travail cloud Azure Stream Analytics à l’aide de Visual Studio Code.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/22/2020
ms.openlocfilehash: 1813fb222bca74f355fec52252ce3d77fef06e5d
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013921"
---
# <a name="create-custom-net-deserializers-for-azure-stream-analytics-in-visual-studio-code"></a>Créer des désérialiseurs .NET personnalisés pour Azure Stream Analytics dans Visual Studio Code

Azure Stream Analytics dispose d’une [prise en charge intégrée de trois formats de données](stream-analytics-parsing-json.md) : JSON, CSV et Avro. Avec les désérialiseurs .NET personnalisés, vous pouvez lire des données à partir d’autres formats, par exemple le [tampon de protocole](https://developers.google.com/protocol-buffers/), [Bond](https://github.com/Microsoft/bond) et d’autres formats définis par l’utilisateur pour les travaux cloud.

## <a name="custom-net-deserializers-in-visual-studio-code"></a>Désérialiseurs .NET personnalisés dans Visual Studio Code

Vous pouvez créer, tester et déboguer un désérialiseur .NET personnalisé pour un travail cloud Azure Stream Analytics à l’aide de Visual Studio Code.

### <a name="prerequisites"></a>Prérequis

* Installez le [SDK .NET Core](https://dotnet.microsoft.com/download) et redémarrez Visual Studio Code.

* Utilisez ce [démarrage rapide](quick-create-visual-studio-code.md) pour apprendre à créer un travail Stream Analytics à l’aide de Visual Studio Code.

### <a name="create-a-custom-deserializer"></a>Créer un désérialiseur personnalisé

1. Ouvrez un terminal et exécutez la commande suivante pour créer une bibliothèque de classes .NET dans Visual Studio Code pour votre désérialiseur personnalisé appelé **ProtobufDeserializer**.

   ```dotnetcli
   dotnet new classlib -o ProtobufDeserializer
   ```

2. Accédez au répertoire de projet **ProtobufDeserializer** et installez les packages NuGet [Microsoft.Azure.StreamAnalytics](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics/) et [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/).

   ```dotnetcli
   dotnet add package Microsoft.Azure.StreamAnalytics
   ```

   ```dotnetcli
   dotnet add package Google.Protobuf
   ```

3. Ajoutez la [classe MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) et la [classe MessageBodyDeserializer ](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) à votre projet.

4. Générez le projet **ProtobufDeserializer**.

### <a name="add-an-azure-stream-analytics-project"></a>Ajouter un projet Azure Stream Analytics

1. Ouvrez Visual Studio Code et sélectionnez **Ctrl+Maj+P** pour ouvrir la palette de commandes. Entrez ensuite ASA et sélectionnez **ASA : Créer un projet**. Nommez-le **ProtobufCloudDeserializer**.

### <a name="configure-a-stream-analytics-job"></a>Configurer un travail Stream Analytics

1. Double-cliquez sur **JobConfig.json**. Utilisez les configurations par défaut, sauf pour les paramètres suivants :

   |Paramètre|Valeur suggérée|
   |-------|---------------|
   |Ressource des paramètres de stockage généraux|Choisir une source de données du compte actuel|
   |Abonnement des paramètres de stockage généraux| < votre abonnement >|
   |Compte de stockage des paramètres de stockage généraux| < votre compte de stockage >|
   |Compte de stockage des paramètres de CustomCodeStorage|< votre compte de stockage >|
   |Conteneur des paramètres de CustomCodeStorage|< votre conteneur de stockage >|

2. Dans le dossier **Entrées**, ouvrez **input.json**. Sélectionnez **Ajouter des entrées dynamiques**, ajoutez une entrée à partir du stockage Azure Data Lake Storage Gen2/Blob, et sélectionnez **Sélectionner dans votre abonnement Azure**. Utilisez les configurations par défaut, sauf pour les paramètres suivants :

   |Paramètre|Valeur suggérée|
   |-------|---------------|
   |Nom|Entrée|
   |Abonnement|< votre abonnement >|
   |Compte de stockage|< votre compte de stockage >|
   |Conteneur|< votre conteneur de stockage >|
   |Type de sérialisation|Sélectionnez **Personnalisé**|
   |SerializationProjectPath|Sélectionnez l’option **Sélectionner un chemin d’accès de projet de bibliothèque** dans CodeLens et sélectionnez le projet **ProtobufDeserializer** créé dans la section précédente. Sélectionnez **générer le projet** pour générer le projet|
   |SerializationClassName|Sélectionnez l’option **Sélectionner la classe de désérialisation** dans CodeLens pour remplir automatiquement le nom de la classe et le chemin d’accès à la DLL|
   |Nom de la classe|MessageBodyProto.MessageBodyDeserializer|

   :::image type="content" source="./media/custom-deserializer/create-input-vscode.png" alt-text="Ajouter une entrée de désérialiseur personnalisé":::

3. Ajoutez la requête suivante au fichier **ProtobufCloudDeserializer.asaql**.

   ```sql
   SELECT * FROM Input
   ```

4. Téléchargez l’[exemple de fichier d’entrée protobuf](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). Dans le dossier **Entrées**, cliquez avec le bouton droit sur **input.json**, puis sélectionnez **Ajouter une entrée locale**. Double-cliquez sur **local_input1.json** et utilisez les configurations par défaut, sauf pour les paramètres suivants.

   |Paramètre|Valeur suggérée|
   |-------|---------------|
   |Sélectionnez le chemin d’accès de fichier local|Cliquez sur CodeLens pour sélectionner <le chemin de l’exemple de fichier d’entrée protobuf téléchargé>|

### <a name="execute-the-stream-analytics-job"></a>Exécuter le travail Stream Analytics

1. Ouvrez **ProtobufCloudDeserializer.asaql** et sélectionnez **Exécuter localement** dans CodeLens, puis **Utiliser l’entrée locale** dans la liste déroulante.

2. Observez les résultats sous l’onglet **Résultats** dans le diagramme de travail situé à droite. Vous pouvez également cliquer sur les étapes dans le diagramme de travail pour afficher un résultat intermédiaire. Pour en savoir plus, consultez [Débogage local à l’aide du diagramme de travail](debug-locally-using-job-diagram-vs-code.md).

   :::image type="content" source="./media/custom-deserializer/check-local-run-result-vscode.png" alt-text="Vérifier le résultat de l’exécution locale":::

Vous avez réussi à implémenter un désérialiseur personnalisé pour votre travail Stream Analytics. Dans ce tutoriel, vous avez testé le désérialiseur personnalisé localement avec des données locales. Vous pouvez également le tester [à l’aide d’entrées dynamiques dans le cloud](visual-studio-code-local-run-live-input.md). Pour exécuter le travail dans le cloud, vous devez configurer correctement l’entrée et la sortie. Envoyez ensuite votre travail à Azure à partir de Visual Studio Code pour l’exécuter dans le cloud à l’aide du désérialiseur personnalisé que vous venez d’implémenter.

### <a name="debug-your-deserializer"></a>Déboguer votre désérialiseur

Vous pouvez déboguer votre désérialiseur .NET localement de la même façon que vous déboguez du code .NET. 

1. Ajoutez des points d’arrêt à votre fonction .NET.

2. Cliquez sur **Exécuter** dans la barre d’activité de Visual Studio Code et sélectionnez **Créer un fichier launch.json**.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode.png" alt-text="Créer un fichier de démarrage.":::

   Sélectionnez **ProtobufCloudDeserializer**, puis **Azure Stream Analytics** dans la liste déroulante.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode-2.png" alt-text="Créer un fichier de démarrage 2.":::

   Modifiez le fichier **launch.json** en remplaçant <ASAScript>.asaql par ProtobufCloudDeserializer.asaql.
   :::image type="content" source="./media/custom-deserializer/configure-launch-file-vscode.png" alt-text="Configurer le fichier de démarrage.":::

3. Appuyez sur **F5** pour démarrer le débogage. Le programme s’arrête sur vos points d’arrêt comme prévu. Cela fonctionne pour les données d’entrée locale et les données d’entrée dynamiques.

   :::image type="content" source="./media/custom-deserializer/debug-vscode.png" alt-text="Déboguer un désérialiseur personnalisé.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, le travail de streaming et toutes les ressources associées. La suppression du travail évite la facturation des unités de streaming consommées par le travail. Si vous envisagez d’utiliser le travail à l’avenir, vous pouvez l’arrêter et le redémarrer plus tard lorsque vous en avez besoin. Si vous ne pensez pas continuer à utiliser ce travail, supprimez toutes les ressources créées au cours de ce didacticiel en procédant comme suit :

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée.  

2. Sur la page de votre groupe de ressources, sélectionnez **Supprimer**, saisissez le nom de la ressource à supprimer dans la zone de texte, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à implémenter un désérialiseur .NET personnalisé pour la sérialisation des entrées du tampon de protocole. Pour en savoir plus sur la création de désérialiseurs personnalisés, passez à l’article suivant :

> [!div class="nextstepaction"]
> * [Créer différents désérialiseurs .NET pour les travaux Azure Stream Analytics](custom-deserializer-examples.md)
> * [Tester des travaux Azure Stream Analytics localement avec une entrée en direct à l’aide de Visual Studio Code](visual-studio-code-local-run-live-input.md)