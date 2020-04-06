---
title: Comment interroger Recherche cognitive Azure à partir de Power Apps
titleSuffix: Azure Cognitive Search
description: Guide pas à pas sur la façon de créer un connecteur personnalisé pour Recherche cognitive et de le visualiser à partir d’une Power App
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: c246f8652227a5ad2c0798880e530d6039cdeea8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385110"
---
# <a name="how-to-query-a-cognitive-search-index-from-power-apps"></a>Comment interroger un index Recherche cognitive à partir de Power Apps

Ce document montre comment créer un connecteur personnalisé Power Apps afin de pouvoir récupérer les résultats de recherche à partir d’un index de recherche. Il montre également comment émettre une requête de recherche et visualiser les résultats à partir d’une Power App. 

## <a name="prerequisites"></a>Prérequis
*    L’accès au compte Power Apps avec la possibilité de créer des connecteurs personnalisés.
*    Nous supposons que vous avez déjà créé un index Recherche Azure.

## <a name="create-a-custom-connector-to-query-azure-search"></a>Créer un connecteur personnalisé pour interroger Recherche Azure

Il existe deux étapes principales pour avoir une Power App qui montre les résultats de Recherche cognitive Azure. Tout d’abord, nous allons créer un connecteur capable d’interroger l’index de recherche. Dans la [section suivante](#visualize-results-from-the-custom-connector), nous allons mettre à jour votre application Power Apps pour visualiser les résultats retournés par le connecteur.

1. Accédez à [make.powerapps.com](http://make.powerapps.com) et **connectez-vous**.

1. Recherchez **Données** > **Connecteurs personnalisés**.
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Menu du connecteur personnalisé" border="true":::

1. Cliquez sur **+ Nouveau connecteur personnalisé** puis sélectionnez **Créer entièrement**.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Créer entièrement" border="true":::

1. Donnez un nom à votre connecteur personnalisé (autrement dit, *AzureSearchQuery*), puis cliquez sur **Continuer**. Un Assistant s’affiche pour créer votre nouveau connecteur.

1. Entrez les informations dans la page Général.

    - Couleur d’arrière-plan de l’icône (par exemple, #007ee5)
    - Description (par exemple, « Connecteur vers Recherche cognitive Azure »)
    - Dans l’hôte, vous devez entrer l’URL de votre service de recherche (par exemple, `<yourservicename>.search.windows.net`)
    - Pour l’URL de base, entrez simplement « / »
    
    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Boîte de dialogue Informations générales" border="true":::

1. Dans la page Sécurité, définissez *Clé de l’API* comme **Type d’authentification**, définissez les champs Étiquette du paramètre et Nom du paramètre sous *api-key*. Pour **Emplacement du paramètre**, sélectionnez *En-tête* comme indiqué ci-dessous.
 
    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Option Type d’authentification" border="true":::

1. Dans la page Définitions, sélectionnez **+ Nouvelle action** pour créer une action qui interroge l’index. Entrez la valeur « Requête » pour le résumé et le nom de l’ID d’opération. Entrez une description comme *« Interroge l’index de recherche »* .
 
    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Options Nouvelle action" border="true":::


1. Appuyez sur le bouton **+ Importer à partir de l’exemple** pour définir les paramètres et les en-têtes. Ensuite, vous allez définir la demande de requête.  

    * Sélectionnez le verbe `GET`
    * Pour l’URL, entrez un exemple de requête pour votre index de recherche, par exemple :
       
    >https://yoursearchservicename.search.windows.net/indexes/yourindexname/docs?search= *&api-version=2019-05-06-Preview
    

    **Power Apps** utilise la syntaxe pour extraire les paramètres de la requête. Notez que nous avons défini explicitement le champ de recherche. 

    :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Importer à partir de l’exemple" border="false":::

1.  Cliquez sur **Importer** pour préremplir automatiquement la boîte de dialogue Requête.

    :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Boîte de dialogue Importer à partir de l’exemple" border="false":::


1. Terminez la configuration des métadonnées de paramètre en cliquant sur le symbole **…** à côté de chacun des paramètres.

    - Pour *search* : définissez `*` comme **valeur par défaut**, définissez **obligatoire** sur *faux* et définissez la **visibilité** sur *aucune*. 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Métadonnées du paramètre Search" border="true":::

    - Pour *api-version* : définissez `2019-05-06-Preview` comme **valeur par défaut**, définissez la **visibilité** sur Interne et définissez **obligatoire** sur *Vrai*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Métadonnées du paramètre Version" border="true":::

    - De même, pour *api-key*, définissez-le comme **obligatoire**, avec une **visibilité** *interne*. Entrez la clé API de votre service de recherche comme **valeur par défaut**.
    
    Une fois ces modifications effectuées, basculez sur l’affichage **Éditeur Swagger**. Dans la section Paramètres, vous devez voir la configuration suivante :    

    ```
          parameters:
          - {name: search, in: query, required: false, type: string, default: '*'}
          - {name: api-version, in: query, required: true, type: string, default: 2019-05-06-Preview,
            x-ms-visibility: internal}
          - {name: api-key, in: header, required: true, type: string, default: YOURKEYGOESHERE,
            x-ms-visibility: internal}
    ```

1. Dans la section Réponse, cliquez sur **« Ajouter une réponse par défaut »** . Cela est essentiel, car cela permet à **Power Apps** de comprendre le schéma de la réponse. Collez un exemple de réponse.

    > [!TIP] 
    > Il y a une limite de caractères pour la réponse JSON que vous pouvez entrer, vous devrez donc peut-être simplifier le JSON avant de le coller. L’aspect important est le schéma ou format de la réponse. Les valeurs réelles de l’exemple de réponse sont moins importantes et peuvent être simplifiées pour réduire le nombre de caractères.
    

1.    Cliquez sur le bouton **Créer un connecteur** en haut à droite de l’écran avant de pouvoir le tester.

1.  Dans la page Test, cliquez sur **+ Nouvelle connexion**, puis entrez la clé de requête du service de recherche comme valeur pour *api-key*.

    Cette étape peut vous conduire à l’Assistant et à la page Connexions. Vous souhaiterez peut-être revenir à l’éditeur Connexions personnalisées pour tester réellement la connexion. Accédez à **Connecteur personnalisé** > sélectionnez le connecteur que vous venez de créer > *…* > **Affichez les propriétés** > **Modifier** > **4. Tester** pour revenir à la page de test.

1.    Cliquez maintenant sur **Opération de test** pour vous assurer que vous obtenez les résultats de votre index. Si vous avez réussi, vous devriez voir un état 200 et, dans le corps de la réponse, vous devriez voir un JSON décrivant les résultats de votre recherche.




## <a name="visualize-results-from-the-custom-connector"></a>Visualiser les résultats à partir du connecteur personnalisé
L’objectif de ce didacticiel n’est pas de vous montrer comment créer des expériences utilisateur sophistiquées avec Power Apps, la disposition de l’interface utilisateur sera donc minimaliste. Créons une Power App avec une zone de recherche et un bouton de recherche et affichons les résultats dans un contrôle de galerie.  La Power App se connecte à notre connecteur personnalisé récemment créé pour récupérer les données de Recherche Azure.

1. Créez une nouvelle Power App. Accédez à la section **Applications**, cliquez sur **+ Nouvelle application**, puis sélectionnez **Canevas**.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Créer une application canevas" border="true":::

1. Sélectionnez le type d’application que vous souhaitez. Pour ce didacticiel, créez une **application vide** avec la **disposition téléphone**. **Power Apps Studio** s’affiche.

1. Une fois dans le studio, sélectionnez l’onglet **Sources de données**, puis cliquez sur le nouveau connecteur que vous venez de créer. Dans notre cas, il s’appelle *AzureSearchQuery*. Cliquez sur **Ajouter une connexion**.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="Connecter le connecteur" border="true":::

    À présent, *AzureSearchQuery* est une source de données qui peut être utilisée à partir de votre application.
    
1. Accédez à l’**onglet Insérer**, afin de pouvoir ajouter quelques contrôles à notre formulaire.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Insérer des contrôles" border="true":::

1.  Insérez les éléments suivants :
    -   Une étiquette de texte avec la valeur « Requête : »
    -   Un élément d’entrée de texte (appelez-le *txtQuery*, valeur par défaut : «*»)
    -   Un bouton avec le texte « Rechercher » 
    -   Une galerie verticale appelée (appelez-la *galleryResults*)
    
    Votre formulaire doit ressembler à ceci :

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Disposition des contrôles" border="true":::

1. Pour faire en sorte que le **bouton Rechercher** émette une requête, sélectionnez le bouton, puis collez l’action suivante à entreprendre sur **OnSelect** :

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Get({search: txtQuery.Text}).value))
    ```

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="Bouton OnSelect" border="true":::
 
    Cette action fera que le bouton mettra à jour une nouvelle collection appelée *azResult* avec le résultat de la requête de recherche en utilisant le texte de la zone de texte *txtQuery* comme terme de la requête.
    
1.  À l’étape suivante, nous allons lier la galerie verticale que nous avons créée à la collection *azResult*. Sélectionnez le contrôle Galerie, puis effectuez les actions suivantes dans le volet Propriétés.

    -  Définissez **Source de données** sur *azResult*.
    
    -  Sélectionnez une **Disposition** qui vous convienne en fonction du type de données de votre index. Dans ce cas, nous avons utilisé la disposition *Title, subtitle and body* (Titre, sous-titre et corps).
    
    -  **Modifiez les champs**, puis sélectionnez les champs que vous souhaitez visualiser.

    Étant donné que nous avons fourni un exemple de résultat lorsque nous avons défini le connecteur, l’application connaît les champs disponibles dans votre index.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Champs de la galerie" border="true":::   
 
1.  Appuyez sur la touche **F5** pour afficher un aperçu de l’application.  

    N’oubliez pas que les champs peuvent être définis sur des valeurs calculées.      
    Pour l’exemple, le paramètre utilisant la disposition *« Image, Title and Subtitle »* (Image, titre et sous-titres) et la spécification de la fonction *Image* comme concaténation du chemin racine pour les données et le nom de fichier (par exemple `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`) produira le résultat ci-dessous.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Application finale" border="true":::        

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir plus d’informations et recevoir une formation en ligne, consultez [Catalogue de formation Power Apps](https://docs.microsoft.com/powerapps/learning-catalog/get-started).

