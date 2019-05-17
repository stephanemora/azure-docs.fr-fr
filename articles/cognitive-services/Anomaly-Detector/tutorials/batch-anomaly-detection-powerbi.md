---
title: Visualiser les anomalies à l’aide de la détection de lot et Power BI
titlesuffix: Azure Cognitive Services
description: Utilisez l’API de détecteur d’anomalies et Power BI pour visualiser les anomalies dans l’ensemble de vos données de série chronologique.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 04/30/2019
ms.author: aahi
ms.openlocfilehash: 48257782bd71f6145adc7107a5fe70b4cc4f8305
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790648"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Didacticiel : Visualiser les anomalies à l’aide de la détection de lot et Power BI

Ce didacticiel permet de rechercher des anomalies au sein d’un jeu de données de série chronologique en tant que lot. À l’aide de Power BI desktop, vous prendre un fichier Excel, préparez les données à l’API de détecteur d’anomalie et visualiser les anomalies statistiques tout.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Utiliser Power BI Desktop pour importer et transformer un jeu de données de série chronologique
> * Intégrer Power BI Desktop à l’API de détecteur d’anomalie pour la détection des anomalies de lot
> * Visualiser les anomalies détectées au sein de vos données, y compris les valeurs attendues et vu et limites de détection des anomalies.

## <a name="prerequisites"></a>Conditions préalables

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/), disponible gratuitement.
* Une données de série chronologique contenant excel (.xlsx) de fichier pointant. Vous trouverez l’exemple de données pour ce démarrage rapide sur [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Charger et mettre en forme les données de série chronologique

Pour commencer, ouvrez Power BI Desktop et charger les données de série chronologique que vous avez téléchargé à partir de la configuration requise. Ce fichier excel contient une série de paires de timestamp et la valeur de temps universel coordonné (UTC).  

> [!NOTE]
> Power BI peut utiliser des données à partir d’un large éventail de sources, telles que des fichiers .csv, SQL bases de données, le stockage blob Azure et bien plus encore.  

Dans la fenêtre principale de Power BI Desktop, cliquez sur le **accueil** ruban. Dans le **données externes** groupe du ruban, ouvrez le **obtenir des données** menu de liste déroulante et cliquez sur **Excel**.

![Une image du bouton « Obtenir des données » dans Power BI](../media/tutorials/power-bi-get-data-button.png)

Une fois la boîte de dialogue s’affiche, accédez au dossier où vous avez téléchargé l’exemple de fichier .xlsx et sélectionnez-le. Après le **Navigator** boîte de dialogue s’affiche, cliquez sur **Sheet1**, puis **modifier**.

![Une image de l’écran de « Navigateur » de source de données dans Power BI](../media/tutorials/navigator-dialog-box.png)

Power BI convertit les horodatages dans la première colonne pour un `Date/Time` type de données. Ces horodatages doivent être converties en texte pour les envoyer à l’API de détecteur d’anomalie. Si l’éditeur Power Query n’ouvre pas automatiquement, cliquez sur **modifier les requêtes** sous l’onglet Accueil. 

Cliquez sur le **transformer** ruban dans l’éditeur de requête d’alimentation. Dans le **n’importe quelle colonne** groupe, ouvrez le **Type de données :** menu déroulant, puis sélectionnez **texte**.

![Une image de l’écran de « Navigateur » de source de données dans Power BI](../media/tutorials/data-type-drop-down.png)

Lorsque vous recevez un avis sur la modification du type de colonne, cliquez sur **remplacer actuel**. Ensuite, cliquez sur **Fermer & appliquer** ou **appliquer** dans le **accueil** ruban. 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Créer une fonction pour envoyer les données et mettre en forme la réponse

Pour mettre en forme et envoyer le fichier de données à l’API de détecteur d’anomalie, vous pouvez appeler une requête sur la table créée ci-dessus. Dans l’éditeur de requête Power, à partir de la **accueil** ruban, ouvrez le **nouvelle Source** menu de liste déroulante et cliquez sur **requête vide**.

Assurez-vous que votre nouvelle requête est sélectionné, puis cliquez sur **éditeur avancé**. 

![Une image du bouton « Éditeur avancé » dans Power BI](../media/tutorials/advanced-editor-screen.png)

Dans l’éditeur avancé, utilisez l’extrait suivant de M Power Query pour extraire les colonnes de la table et l’envoyer à l’API. Par la suite, la requête crée une table à partir de la réponse JSON et renvoyez-le. Remplacez le `apiKey` variable avec votre clé API de détecteur d’anomalies valide, et `endpoint` avec votre point de terminaison. Une fois que vous avez entré la requête dans l’éditeur avancé, cliquez sur **fait**.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect,
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({
                    
                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),
    
    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

Appeler la requête de la feuille de données en sélectionnant `Sheet1` ci-dessous **Entrez le paramètre**, puis cliquez sur **Invoke**. 

![Une image du bouton « Éditeur avancé »](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>L’authentification et la confidentialité des données source

> [!NOTE]
> N’oubliez pas de stratégies de votre organisation pour l’accès et la confidentialité des données. Consultez [les niveaux de confidentialité de Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-privacy-levels) pour plus d’informations.

Vous pouvez obtenir un message d’avertissement lorsque vous essayez d’exécuter la requête, car il utilise une source de données externe. 

![Image illustrant un avertissement créé par Power BI](../media/tutorials/blocked-function.png)

Pour résoudre ce problème, cliquez sur **fichier**, et **Options et paramètres**. Puis cliquez sur **Options**. Ci-dessous **fichier actuel**, sélectionnez **confidentialité**, et **ignorer les niveaux de confidentialité et potentiellement améliorer les performances**. 

En outre, vous pouvez obtenir un message vous demandant de spécifier la façon dont vous souhaitez vous connecter à l’API.

![Image montrant une requête pour spécifier les informations d’identification de l’accès](../media/tutorials/edit-credentials-message.png)

Pour résoudre ce problème, cliquez sur **modifier les informations d’identification** dans le message. Une fois la boîte de dialogue s’affiche, sélectionnez **anonyme** à se connecter anonymement à l’API. Puis, cliquez sur **Se connecter**. 

Ensuite, cliquez sur **Fermer & appliquer** dans le **accueil** ruban pour appliquer les modifications.

## <a name="visualize-the-anomaly-detector-api-response"></a>Visualiser la réponse de l’API de détecteur d’anomalies

Dans l’écran principal de Power BI, commencez à l’aide de requêtes créés ci-dessus pour visualiser les données. Sélectionnez d’abord **graphique en courbes** dans **visualisations**. Puis ajouter l’horodatage de la fonction appelée au graphique en courbes **axe**. Avec le bouton droit dessus, puis sélectionnez **Timestamp**. 

![Clic droit sur la valeur d’horodatage](../media/tutorials/timestamp-right-click.png)

Ajoutez les champs suivants à partir de la **appelé la fonction** pour le graphique **valeurs** champ. Utilisez la capture d’écran pour vous aider à développer votre graphique ci-dessous.

    * Valeur
    * UpperMargins
    * LowerMargins
    * ExpectedValues

![Une image du nouvel écran mesure rapide](../media/tutorials/chart-settings.png)

Après avoir ajouté les champs, cliquez sur le graphique et redimensionnez-le pour afficher tous les points de données. Votre graphique doit ressembler à la capture d’écran ci-dessous :

![Une image du nouvel écran mesure rapide](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Afficher les points de données d’anomalie

Sur le côté droit de la fenêtre Power BI, ci-dessous la **champs** volet, avec le bouton droit sur **valeur** sous le **requête de fonction appelé**, puis cliquez sur **rapide mesure**.

![Une image du nouvel écran mesure rapide](../media/tutorials/new-quick-measure.png)

Dans l’écran qui s’affiche, sélectionnez **filtré valeur** en tant que le calcul. Définissez **valeur de Base** à `Sum of Value`. Puis faites glisser `IsAnomaly` à partir de la **appelé la fonction** champs **filtre**. Sélectionnez `True` à partir de la **filtre** menu déroulant.

![Une image du nouvel écran mesure rapide](../media/tutorials/new-quick-measure-2.png)

Après avoir cliqué sur **Ok**, vous aurez un `Value for True` champ, en bas de la liste de vos champs. Faites un clic droit et renommez-le **des anomalies**. Ajouter à l’organigramme **valeurs**. Puis sélectionnez le **Format** outil et définissez le type de l’axe des x sur **catégorie**.

![Une image du nouvel écran mesure rapide](../media/tutorials/format-x-axis.png)

Appliquer des couleurs à votre graphique en cliquant sur le **Format** outil et **couleurs des données**. Votre graphique doit ressembler à ceci :

![Une image du nouvel écran mesure rapide](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
>[Diffusion en continu de détection d’anomalie avec Azure Databricks](anomaly-detection-streaming-databricks.md)
