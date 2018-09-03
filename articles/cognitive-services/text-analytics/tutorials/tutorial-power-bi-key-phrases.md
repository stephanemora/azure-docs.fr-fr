---
title: Service Analyse de texte avec Power BI - Azure Cognitive Services | Microsoft Docs
description: Découvrez comment utiliser le service Analyse de texte pour extraire les expressions clés d’un texte stocké dans Power BI.
services: cognitive-services
author: luiscabrer
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: tutorial
ms.date: 3/07/2018
ms.author: luisca
ms.openlocfilehash: 2cdb93d44218627efdcb0360d8cf4a4eeeca177a
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2018
ms.locfileid: "42889269"
---
# <a name="text-analytics-with-power-bi"></a>Analyse de texte avec Power BI

Microsoft Power BI synthétise les données d’organisation dans de remarquables rapports que vous pouvez ensuite diffuser à l’ensemble de votre organisation, qui bénéficie ainsi d’insights approfondis plus rapidement. Le service Analyse de texte, l’un des services de la gamme Cognitive Services dans Microsoft Azure, peut extraire les expressions essentielles d’un texte par le biais de son API Expressions clés. Utilisés conjointement, ces outils peuvent vous aider à identifier rapidement ce dont parlent vos clients, ainsi que leurs sentiments à ce sujet.

Dans le cadre de ce didacticiel, vous allez découvrir comment intégrer Power BI Desktop et l’API Expressions clés pour extraire les expressions fondamentales des commentaires des clients à l’aide d’une fonction Power Query personnalisée. Vous allez également créer un nuage de mots à partir de ces expressions.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez disposer des éléments suivants :

> [!div class="checklist"]
> * Microsoft Power BI Desktop. [Téléchargez-le sans frais](https://powerbi.microsoft.com/get-started/).
> * Un compte Microsoft Azure [Démarrez un essai gratuit](https://azure.microsoft.com/free/) ou [connectez-vous](https://portal.azure.com/).
> * Une clé d’accès pour le service Analyse de texte. [Inscrivez-vous](../../cognitive-services-apis-create-account.md), puis [obtenez votre clé](../how-tos/text-analytics-how-to-access-key.md).
> * Des commentaires de clients. [Obtenez nos exemples de données](https://aka.ms/cogsvc/ta) ou utilisez vos propres données.

## <a name="loading-customer-data"></a>Chargement de données client

Pour commencer, ouvrez Power BI Desktop et chargez le fichier de valeurs séparées par des virgules (CSV) **FabrikamComments.csv**. Ce fichier représente une journée d’activité hypothétique dans le forum de support d’une petite entreprise fictive.

> [!NOTE]
> Power BI peut utiliser les données issues d’une multitude de sources, telles que Facebook ou une base de données SQL. Pour plus d’informations, consultez les pages concernant [l’intégration de Facebook à Power BI](https://powerbi.microsoft.com/integrations/facebook/) et [l’intégration de SQL Server à Power BI](https://powerbi.microsoft.com/integrations/sql-server/).

Dans la fenêtre principale Power BI Desktop, recherchez le groupe Données externes du ruban Accueil. Dans le menu déroulant **Obtenir les données** de ce groupe, choisissez **Texte/CSV**.

![[Bouton Obtenir les données]](../media/tutorials/power-bi/get-data-button.png)

La boîte de dialogue Ouvrir s’affiche. Accédez à votre dossier Téléchargements ou au dossier contenant l’exemple de fichier de données. Cliquez sur `FabrikamComments.csv`, puis sur le bouton **Ouvrir**. La boîte de dialogue d’importation CSV s’affiche.

![[Boîte de dialogue d’importation CSV]](../media/tutorials/power-bi/csv-import.png)

La boîte de dialogue d’importation CSV vous permet de vérifier que Power BI Desktop a correctement détecté le jeu de caractères, le délimiteur, les lignes d’en-tête et les types de colonnes. Ces informations sont toutes correctes. Nous allons donc cliquer sur **Charger**.

Pour visualiser les données chargées, basculez vers la vue Données à l’aide du bouton Vue de données sur la gauche de l’espace de travail Power BI. Cette opération affiche une table contenant nos données, comme dans Microsoft Excel.

![[Vue initiale des données importées]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="preparing-the-data"></a>Préparation des données

Vous pouvez avoir besoin de transformer vos données dans Power BI Desktop afin d’en préparer le traitement par le service Expressions clés.

Par exemple, nos données contiennent à la fois un champ `subject` et un champ `comment`. Pour extraire les expressions clés, nous devons considérer le texte figurant dans ces deux champs, et non uniquement dans le champ `comment`. La fonction Fusionner les colonnes de Power BI Desktop facilite cette tâche.

Ouvrez l’Éditeur de requête à partir de la fenêtre principale de Power BI Desktop en cliquant sur **Modifier les requêtes** dans le groupe Données externes du ruban Accueil. 

![[Groupe Données externes du ruban Accueil]](../media/tutorials/power-bi/edit-queries.png)

Dans la liste Requêtes située sur le côté gauche de la fenêtre, sélectionnez « FabrikamComments » si cette requête n’est pas encore sélectionnée.

À présent, sélectionnez à la fois la colonne `subject` et la colonne `comment` de la table. Vous devrez peut-être faire défiler l’écran horizontalement pour voir ces colonnes. Commencez par cliquer sur l’en-tête de la colonne `subject`, puis maintenez la touche CTRL enfoncée tout en cliquant sur l’en-tête de la colonne `comment`.

![[Sélection des champs à fusionner]](../media/tutorials/power-bi/select-columns.png)

Dans le groupe Text Columns (Colonnes de texte) du ruban Transformation, cliquez sur **Fusionner les colonnes**. La boîte de dialogue Fusionner les colonnes s’affiche.

![[Fusion de champs à l’aide de la boîte de dialogue Fusionner les colonnes]](../media/tutorials/power-bi/merge-columns.png)

Dans la boîte de dialogue Fusionner les colonnes, choisissez le séparateur Tabulation, puis cliquez sur **OK**. Vous avez terminé.

Vous pouvez également envisager de filtrer les messages vides en utilisant le filtre Supprimer les éléments vides ou en supprimant les caractères non imprimables à l’aide de la transformation Nettoyer. Si vos données comportent une colonne comparable à la colonne `spamscore` de notre exemple de fichier, vous pouvez ignorer les commentaires « spam » à l’aide d’un filtre Nombre.

## <a name="understanding-the-api"></a>Compréhension de l’API

L’API Expressions clés peut traiter jusqu’à mille documents texte par requête HTTP. Toutefois, Power BI traite généralement les enregistrements un par un ; par conséquent, nos appels de l’API ne contiennent qu’un seul document. [L’API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) requiert les champs ci-après pour chaque document à traiter.

| | |
| - | - |
| `id`  | Identificateur unique de ce document dans la requête. La réponse contient également ce champ. De cette façon, si vous traitez plusieurs documents, vous pouvez facilement associer les expressions clés extraites au document dont elles sont issues. Étant donné que nous traitons un seul document par requête, nous connaissons déjà le document auquel est associée la réponse, et `id` peut donc être identique dans chaque requête.|
| `text`  | Texte à traiter. Nous obtenons ce texte à partir de la colonne `Merged` que nous avons créée, qui combine le texte de la ligne Objet et du commentaire. L’API Expressions clés exige que ces données ne dépassent pas 5 000 caractères.|
| `language` | Code représentant la langue dans laquelle le document est écrit. Tous nos messages étant en anglais, nous pouvons coder en dur la valeur de langue `en` dans notre requête.|

Nous devons combiner ces champs dans un document JSON (JavaScript Object Notation) pour envoyer ce dernier à l’API Expressions clés. La réponse à cette requête est également fournie sous la forme d’un document JSON, que nous devons analyser et dont nous devons extraire les expressions clés.

## <a name="creating-a-custom-function"></a>Création d’une fonction personnalisée

Nous voici prêts à créer la fonction personnalisée qui intégrera Power BI et la fonctionnalité Analyse de texte. Power BI Desktop appelle la fonction pour chaque ligne de la table et crée une colonne avec les résultats correspondants.

La fonction reçoit le texte à traiter sous la forme d’un paramètre. Elle convertit les données vers et depuis le format JSON requis et adresse la requête HTTP au point de terminaison de l’API Expressions clés. Après avoir analysé la réponse, la fonction renvoie une chaîne contenant une liste d’expressions clés extraites séparées par des virgules.

> [!NOTE]
> Les fonctions Power BI Desktop personnalisées sont écrites en [langage de formule M Power Query](https://msdn.microsoft.com/library/mt211003.aspx), abrégé sous l’appellation « M ». M est un langage de programmation fonctionnel basé sur [F#](https://docs.microsoft.com/dotnet/fsharp/). Toutefois, vous n’avez pas besoin d’être programmeur pour terminer ce didacticiel ; le code requis est inclus ci-après.

Vous devez toujours vous trouver dans la fenêtre Éditeur de requête. Dans le ruban Accueil, cliquez sur **Nouvelle source** (dans le groupe Nouvelle requête), puis choisissez **Requête vide** dans le menu déroulant. 

Une nouvelle requête, initialement nommée Query1, apparaît dans la liste Requêtes. Double-cliquez sur cette entrée et renommez-la `KeyPhrases`.

À présent, ouvrez la fenêtre Éditeur avancé en cliquant sur **Éditeur avancé** dans le groupe Requête du ruban Accueil. Supprimez le code figurant déjà dans cette fenêtre, puis collez le code suivant. 

> [!NOTE]
> Dans les exemples ci-après, nous supposons que le point de terminaison se trouve à l’emplacement https://westus.api.cognitive.microsoft.com.  Le service Analyse de texte prend en charge la création d’un abonnement dans 13 régions différentes. Si vous vous êtes inscrit à ce service dans une autre région, veillez à utiliser le point de terminaison de la région que vous avez sélectionnée. Ce dernier doit figurer dans la page Vue d’ensemble du Portail Azure lorsque vous sélectionnez votre abonnement Analyse de texte.

```fsharp
// Returns key phrases from the text in a comma-separated list
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = Text.Lower(Text.Combine(jsonresp[documents]{0}[keyPhrases], ", "))
in  keyphrases
```

En outre, collez votre clé API Analyse de texte, obtenue à partir du tableau de bord Microsoft Azure, dans la ligne qui commence par `apikey`. (Veillez à conserver les guillemets avant et après la clé.) Puis cliquez sur **Terminé**.

## <a name="using-the-function"></a>Utilisation de la fonction

À présent, nous pouvons utiliser la fonction personnalisée pour obtenir les expressions clés figurant dans chacun des commentaires de nos clients et pour les stocker dans une nouvelle colonne de la table. 

Toujours dans l’Éditeur de requête, rebasculez vers la requête FabrikamComments, réaccédez au ruban Ajouter une colonne, puis cliquez sur le bouton **Appeler une fonction personnalisée** du groupe Général.

![[Bouton Appeler une fonction personnalisée]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

Dans la boîte de dialogue Appeler une fonction personnalisée, entrez `keyphrases` comme nom de la nouvelle colonne. Choisissez notre fonction personnalisée, `KeyPhrases`, comme requête de fonction. 

La boîte de dialogue affiche un nouveau champ vous demandant quel champ transmettre à notre fonction en tant que paramètre `text`. Dans le menu déroulant, choisissez `Merged` (la colonne que nous avons créée précédemment en fusionnant les champs d’objet et de message).

![[Appel d’une fonction personnalisée]](../media/tutorials/power-bi/invoke-custom-function.png)

Pour finir, cliquez sur **OK**.

Si tout est prêt, Power BI appelle notre fonction une fois pour chaque ligne de notre table, en exécutant les requêtes Expressions clés et en ajoutant la nouvelle colonne à la table. Toutefois, avant l’exécution de ce processus, vous pouvez avoir besoin de spécifier les paramètres d’authentification et de confidentialité.

## <a name="authentication-and-privacy"></a>Authentification et confidentialité

Une fois que vous avez fermé la boîte de dialogue Appeler une fonction personnalisée, il est possible qu’une bannière s’affiche pour vous inviter à spécifier le mode de connexion au point de terminaison Expressions clés.

![[Bannière d’informations d’identification]](../media/tutorials/power-bi/credentials-banner.png)

Cliquez sur **Modifier les informations d’identification**, assurez-vous que la valeur Anonyme est sélectionnée dans la boîte de dialogue, puis cliquez sur **Se connecter**. 

> [!NOTE]
> Pourquoi choisir l’authentification anonyme ? Le service Analyse de texte vous authentifie à l’aide de la clé API ; par conséquent, Power BI n’a pas besoin de fournir d’informations d’identification pour la requête HTTP proprement dite.

![[Définition du mode d’accès anonyme]](../media/tutorials/power-bi/access-web-content.png)

Si la bannière Modifier les informations d’identification reste visible une fois que vous avez choisi l’accès anonyme, il est possible que vous ayez oublié de coller votre clé API. Vérifiez la fonction personnalisée `KeyPhrases` dans l’Éditeur avancé pour vous assurer qu’elle est présente.

Ensuite, une bannière peut vous inviter à fournir des informations sur la confidentialité de vos sources de données. 

![[Bannière de confidentialité]](../media/tutorials/power-bi/privacy-banner.png)

Cliquez sur **Continuer**, puis choisissez Public pour chacune des sources de données dans la boîte de dialogue. Ensuite, cliquez sur **Enregistrer**.

![[Définition de la confidentialité des sources de données]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="creating-the-word-cloud"></a>Création du nuage de mots

Une fois que vous avez géré les bannières qui s’affichent, cliquez sur **Fermer et appliquer** dans le ruban Accueil pour fermer l’Éditeur de requête.

Power BI Desktop consacre quelques minutes à l’exécution des requêtes HTTP nécessaires. Pour chaque ligne de la table, la nouvelle colonne `keyphrases` contient les expressions clés détectées dans le texte par l’API Expressions clés. 

Utilisons cette colonne pour générer un nuage de mots. Pour commencer, cliquez sur le bouton Rapport dans la fenêtre principale de Power BI Desktop, à gauche de l’espace de travail.

> [!NOTE]
> Pourquoi utiliser les expressions clés extraites pour générer un nuage de mots, plutôt que le texte complet de chaque commentaire ? Les expressions clés nous précisent les mots *importants* des commentaires de nos clients, et non simplement les *mots les plus courants*. En outre, le dimensionnement des mots dans le nuage résultant n’est pas faussé par l’usage fréquent d’un mot dans un nombre de commentaires relativement faible.

Si vous n’avez pas encore installé l’objet visuel personnalisé Word Cloud, installez-le. Dans le panneau Visualisations à droite de l’espace de travail, cliquez sur les points de suspension (**...** ) et choisissez **Import From Store** (Importer à partir du Store). Recherchez le terme « cloud », puis cliquez sur le bouton **Ajouter** en regard de l’objet visuel Word Cloud. Power BI installe l’objet visuel Word Cloud et vous avertit lorsque l’installation est terminée.

![[Ajout d’un objet visuel personnalisé]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

À présent, créons notre nuage de mots.

![[Icône Word Cloud dans le panneau Visualisations]](../media/tutorials/power-bi/visualizations-panel.png)

Commencez par cliquer sur l’icône Word Cloud dans le panneau Visualisations. Un nouveau rapport s’affiche dans l’espace de travail. Faites glisser le champ `keyphrases` du panneau Champs vers le champ Catégorie du volet Visualisations. Le nuage de mots apparaît à l’intérieur du rapport.

Passez maintenant à la page Format du panneau Visualisations. Dans la catégorie Mots vides, activez l’option **Mots vides par défaut** pour éliminer du nuage les mots courants courts tels que « de ». 

![[Activation des mots vides par défaut]](../media/tutorials/power-bi/default-stop-words.png)

Un peu plus bas dans ce panneau, désactivez les options **Faire pivoter le texte** et **Titre**.

![[Activation du mode Focus]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

Cliquez sur l’outil Mode Focus dans le rapport pour améliorer l’aspect de votre nuage de mots. L’outil développe le nuage de mots afin qu’il remplisse la totalité de l’espace de travail, comme illustré ci-après.

![[Exemple de nuage de mots]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>Autres services Analyse de texte

Le service Analyse de texte, appartenant à la gamme Cognitive Services proposée par Microsoft Azure, offre également des fonctionnalités d’analyse des sentiments et de détection de la langue. La détection de la langue se révèle particulièrement utile si les commentaires de vos clients ne sont pas tous en anglais.

Ces deux autres API sont très semblables à l’API Expressions clés. Vous pouvez donc utiliser des fonctions personnalisées quasiment identiques pour intégrer ces API à Power BI Desktop. Il vous suffit de créer une requête vide et de coller le code approprié ci-après dans l’Éditeur avancé, comme vous l’avez fait précédemment. (N’oubliez pas votre clé d’accès !) Ensuite, comme auparavant, utilisez la fonction pour ajouter une nouvelle colonne à la table.

La fonction Analyse des sentiments ci-après renvoie un score évaluant le caractère positif du sentiment exprimé dans le texte.

```fsharp
// Returns the sentiment score of the text, from 0.0 (least favorable) to 1.0 (most favorable)
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    sentiment   = jsonresp[documents]{0}[score]
in  sentiment
```

Voici deux versions d’une fonction Détection de langue. La première renvoie le code de langue ISO (par exemple, `en` pour l’anglais), alors que la seconde renvoie le nom « convivial » de la langue (par exemple, `English`). Vous pouvez constater que seule la dernière ligne du corps diffère entre les deux versions.

```fsharp
// Returns the two-letter language code (e.g. en for English) of the text
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[iso6391Name]
in  language
```
```fsharp
// Returns the name (e.g. English) of the language in which the text is written
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[name]
in  language
```

Pour finir, voici une variante de la fonction Expressions clés déjà présentée qui renvoie les expressions sous la forme d’un objet de liste, plutôt qu’au moyen d’une chaîne unique d’expressions séparées par des virgules. 

> [!NOTE]
> Le renvoi d’une chaîne unique contribuait à simplifier notre exemple de nuage de mots. En revanche, une liste vous offre un surcroît de souplesse lorsque vous travaillez sur les expressions renvoyées dans Power BI. Vous pouvez manipuler les objets de liste dans Power BI Desktop à l’aide du groupe Colonne structurée dans le ruban Transformation de l’Éditeur de requête.

```fsharp
// Returns key phrases from the text as a list object
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = jsonresp[documents]{0}[keyPhrases]
in  keyphrases
```

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur le service Analyse de texte, sur le langage de formule M Power Query ou sur Power BI.

> [!div class="nextstepaction"]
> [Informations de référence sur l’API Analyse de texte](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

> [!div class="nextstepaction"]
> [Informations de référence sur le langage M Power Query](//msdn.microsoft.com/library/mt211003.aspx)

> [!div class="nextstepaction"]
> [Documentation Power BI](//powerbi.microsoft.com/documentation/powerbi-landing-page/)
