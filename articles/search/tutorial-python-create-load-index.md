---
title: 'Tutoriel Python : Ajouter une fonctionnalité de recherche à des applications web'
titleSuffix: Azure Cognitive Search
description: Créez un index et importez des données CSV dans l’index de recherche en Python, à l’aide du kit SDK azure-search-documents du package PYPI.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 05/25/2021
ms.custom: devx-track-python
ms.devlang: python
ms.openlocfilehash: a9678e542e577c13141f18e59fe2e628ed465321
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110580627"
---
# <a name="2---create-and-load-search-index-with-python"></a>2 - Créer et charger un index de recherche en Python

Continuez à créer votre site web de recherche en procédant comme suit :
* Création d’une ressource de recherche avec l’extension VS Code
* Création d’un index et importation des données en Python à l’aide de l’exemple de script et du kit SDK Azure [azure-search-documents](https://pypi.org/project/azure-search-documents/).

## <a name="create-an-azure-cognitive-search-resource"></a>Créer une ressource Recherche cognitive Azure 

Créez une nouvelle ressource de recherche avec l’extension [Recherche cognitive Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch) pour Visual Studio Code.

1. Dans Visual Studio Code, ouvrez la [barre d’activités](https://code.visualstudio.com/docs/getstarted/userinterface) et sélectionnez l’icône Azure. 

1. Dans la barre latérale, **cliquez avec le bouton droit sur votre abonnement Azure** sous la zone `Azure: Cognitive Search`, puis sélectionnez **Créer un service de recherche**.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-create-search-resource.png" alt-text="Dans la barre latérale, cliquez avec le bouton droit sur votre abonnement Azure sous la zone **Recherche cognitive Azure**, puis sélectionnez **Créer un service de recherche**.":::

1. Suivez les invites pour fournir les informations suivantes :

    |Prompt|Entrez|
    |--|--|
    |Entrez un nom global unique pour le nouveau service de recherche.|**Mémorisez ce nom**. Ce nom de ressource devient partie intégrante de votre point de terminaison de ressource.|
    |Sélectionner un groupe de ressources pour les nouvelles ressources|Utilisez le groupe de ressources que vous avez créé pour ce tutoriel.|
    |Sélectionnez la référence SKU de votre service de recherche.|Pour les besoins de ce tutoriel, sélectionnez **Free (Gratuit)** . Vous ne pouvez pas changer un niveau tarifaire une fois le service créé.|
    |Sélectionnez un emplacement pour les nouvelles ressources.|Sélectionnez une région proche de chez vous.|

1. Une fois que vous avez terminé les invites, votre nouvelle ressource de recherche est créée. 

## <a name="get-your-search-resource-admin-key"></a>Récupérer la clé d’administration de la ressource de recherche

Récupérez la clé d’administration de votre ressource de recherche avec l’extension Visual Studio Code. 

1. Dans Visual Studio Code, dans la barre latérale, cliquez avec le bouton droit sur votre ressource de recherche, puis sélectionnez **Copier la clé d’administration**.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-admin-key.png" alt-text="Dans la barre latérale, cliquez avec le bouton droit sur votre ressource de recherche, puis sélectionnez **Copier la clé d’administration**":::.

1. Conservez cette clé d’administration, vous devrez l’utiliser pour créer des objets dans [une prochaine section](#prepare-the-bulk-import-script-for-search). 

## <a name="prepare-the-bulk-import-script-for-search"></a>Préparer le script d’importation en bloc pour la recherche

Le script utilise le Kit de développement logiciel (SDK) Azure pour Recherche cognitive :

* [Package PYPI : azure-search-documents](https://pypi.org/project/azure-search-documents/)
* [Documentation de référence](/python/api/azure-search-documents)

1. Dans Visual Studio Code, ouvrez le fichier `bulk_upload.py` dans le sous-répertoire `search-website/bulk-upload` et remplacez les variables suivantes par vos propres valeurs pour vous authentifier auprès du Kit de développement logiciel (SDK) Azure Search :

    * VOTRE-NOM-DE-RESSOURCE-DE-RECHERCHE
    * VOTRE CLÉ-ADMINISTRATION-DE-RECHERCHE

    :::code language="python" source="~/azure-search-python-samples/search-website/bulk-upload/bulk-upload.py" highlight="12,13, 117" :::

1. Ouvrez un terminal intégré dans Visual Studio pour le sous-répertoire du répertoire du projet, `search-website/bulk-upload`, puis exécutez la commande suivante pour installer les dépendances. 

    # <a name="macoslinux"></a>[macOS/Linux](#tab/linux-install)
    
    ```bash
    python3 -m pip install -r requirements.txt 
    ```
    
    # <a name="windows"></a>[Windows](#tab/windows-install)

    ```bash
    py -m pip install -r requirements.txt 
    ```

## <a name="run-the-bulk-import-script-for-search"></a>Exécuter le script d’importation en bloc pour la recherche

1. Continuez à utiliser le terminal intégré dans Visual Studio pour le sous-répertoire du répertoire du projet, `search-website/bulk-upload`, pour exécuter la commande bash suivante afin d’exécuter le script `bulk_upload.py` :

    # <a name="macoslinux"></a>[macOS/Linux](#tab/linux-run)
    
    ```bash
    python3 bulk-upload.py
    ```
    
    # <a name="windows"></a>[Windows](#tab/windows-run)

    ```bash
    py bulk-upload.py
    ```


1. À mesure que le code s’exécute, la console affiche la progression. 
1. Une fois le chargement effectué, la dernière instruction affichée sur la console est « Terminé. Appuyez sur une touche pour fermer le terminal ».

## <a name="review-the-new-search-index"></a>Examiner le nouvel index de recherche

Une fois le chargement effectué, l’index de recherche est prêt à être utilisé. Passez en revue le nouvel index.

1. Dans Visual Studio Code, ouvrez l’extension Recherche cognitive Azure et sélectionnez votre ressource de recherche.  

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-resource.png" alt-text="Dans Visual Studio Code, ouvrez l’extension Recherche cognitive Azure et ouvrez votre ressource de recherche.":::

1. Développez Index, puis Documents, puis `good-books`, puis sélectionnez un document pour en afficher toutes les données spécifiques.
 
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" lightbox="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" alt-text="Développez Index, puis « good-books », puis sélectionnez un document.":::

## <a name="copy-your-search-resource-name"></a>Copiez le nom de votre ressource de recherche.

Notez le **nom de votre ressource de recherche**. Vous en aurez besoin pour connecter l’application Azure Function à votre ressource de recherche. 

> [!CAUTION]
> Vous pouvez être tenté d’utiliser votre clé d’administration de recherche dans Azure Function, qui ne suit pas le principe du privilège minimum. Azure Function utilise la clé de requête pour se conformer au privilège minimum. 

## <a name="next-steps"></a>Étapes suivantes

[Déployer votre application web statique](tutorial-python-deploy-static-web-app.md)