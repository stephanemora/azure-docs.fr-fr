---
title: 'Recherche personnalisée Bing : Créer une page web de recherche personnalisée | Microsoft Docs'
description: Décrit la configuration d’une instance de recherche personnalisée et son intégration à une page web
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 10/16/2017
ms.author: v-brapel
ms.openlocfilehash: c1431ec852cab943e00d3933ef4f0500a4fdb151
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368852"
---
# <a name="build-a-custom-search-web-page"></a>Créer une page web Recherche personnalisée
La Recherche personnalisée Bing vous permet de créer des expériences de recherche adaptées pour des sujets qui vous intéressent. Par exemple, si vous avez un site web d’arts martiaux qui fournit une expérience de recherche, vous pouvez spécifier les domaines, sous-sites et pages web dans lesquels Bing effectue les recherches. Vos utilisateurs consultent des résultats de recherche adaptés au contenu qui les intéresse au lieu de compulser des pages de résultats de recherche générale dont le contenu peut être inapproprié. 

Ce tutoriel montre comment configurer une instance de recherche personnalisée et l’intégrer à une nouvelle page web.

Les tâches traitées sont les suivantes :

> [!div class="checklist"]
> - Création d’une instance de recherche personnalisée
> - Ajout d’entrées actives
> - Ajout d’entrées bloquées
> - Ajout d’entrées épinglées
> - Intégration de la recherche personnalisée à une page web

## <a name="prerequisites"></a>Prérequis
- Pour suivre ce tutoriel, vous avez besoin d’une clé d’abonnement pour l’API Recherche personnalisée Bing.  Pour obtenir une clé, consultez [Essayer Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
- Si vous n’avez pas encore installé Visual Studio 2017, vous pouvez télécharger et utiliser la version **gratuite** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).

## <a name="create-a-custom-search-instance"></a>Création d’une instance de recherche personnalisée
Pour créer une instance Recherche personnalisée Bing :

1.  Ouvrez un navigateur Internet.
2.  Accédez au [portail](https://customsearch.ai) de la Recherche personnalisée.
3.  Connectez-vous au portail à l’aide d’un compte Microsoft. Si vous n’en avez pas, cliquez sur **Créer un compte Microsoft**. Si c’est la première fois que vous utilisez le portail, des autorisations d’accès à vos données vous sont demandées. Cliquez sur **Oui**.
4.  Une fois connecté, cliquez sur **New custom search** (Nouvelle recherche personnalisée). Dans la fenêtre **Create a new custom search instance** (Créer une nouvelle instance de recherche personnalisée), entrez un nom explicite et décrivez le type de contenu que la recherche retourne. Vous pouvez modifier le nom à tout moment.
 
    ![Capture d’écran de la boîte de dialogue de création d’une instance de recherche personnalisée](../media/newCustomSrch.png)

5.  Cliquez sur OK, spécifiez une URL et précisez s’il faut inclure les sous-pages de la page de base :

    ![Capture d’écran de la page de définition de l’URL](../media/newCustomSrch1-a.png)

## <a name="add-active-entries"></a>Ajout d’entrées actives
Pour inclure les résultats provenant d’URL ou de sites particuliers, ajoutez-les à l’onglet **Active** (Éléments actifs).

1.  Dans **Definition Editor** (Éditeur de définitions), cliquez sur l’onglet **Active** (Éléments actifs) et entrez l’URL d’un ou plusieurs sites à inclure dans votre recherche.

    ![Capture d’écran de l’onglet des éléments actifs dans l’éditeur de définitions](../media/customSrchEditor.png)

2.  Pour vérifier que votre instance retourne des résultats, entrez une requête dans le volet de visualisation situé à droite. Bing retourne des résultats uniquement pour les sites publics qu’il a indexés.

## <a name="add-blocked-entries"></a>Ajout d’entrées bloquées
Pour exclure les résultats d’URL ou de sites particuliers, ajoutez-les à l’onglet **Blocked** (Éléments bloqués).

1. Dans **Definition Editor** (Éditeur de définitions), cliquez sur l’onglet **Blocked** (Éléments bloqués) et entrez l’URL d’un ou plusieurs sites à exclure de votre recherche.

    ![Capture d’écran de l’onglet des éléments bloqués dans l’éditeur de définitions](../media/blockedCustomSrch.png)


2. Pour vérifier que votre instance ne retourne pas de résultats provenant des sites bloqués, entrez une requête dans le volet de visualisation situé à droite. 

## <a name="add-pinned-entries"></a>Ajout d’entrées épinglées
Pour épingler une page web spécifique au début des résultats de recherche, ajoutez la page web et le terme de requête dans l’onglet **Pinned** (Éléments épinglés).  L’onglet **Pinned** (Éléments épinglés) contient une liste de paires, constituées de termes de requête associés à des pages web, qui spécifient la page web devant s’afficher en tant que meilleur résultat pour une requête particulière. Le terme de requête de l’utilisateur doit correspondre exactement au terme de requête épinglé pour que la page web se trouve épinglée en haut.

1. Dans **Definition Editor** (Éditeur de définitions), cliquez sur l’onglet **Pinned** (Éléments épinglés) et entrez la page web et le terme de requête de la page web qui doit être retournée comme meilleur résultat.

    ![Capture d’écran de l’onglet des éléments épinglés dans l’éditeur de définitions](../media/pinnedCustomSrch.png)

2. Pour vérifier que votre instance retourne la page web spécifiée comme meilleur résultat, entrez le terme de requête que vous avez épinglé dans le volet de visualisation situé à droite.

## <a name="configure-hosted-ui"></a>Configurer l’interface utilisateur hébergée
La Recherche personnalisée fournit une IU hébergée pour restituer la réponse JSON de votre instance de recherche personnalisée.  Pour définir votre expérience d’interface utilisateur :

1. Cliquez sur l’onglet **Hosted UI** (IU hébergée).
2. Sélectionnez une disposition.

    ![Capture d’écran de l’étape de sélection de la disposition de l’interface utilisateur hébergée](./media/custom-search-hosted-ui-select-layout.png)

3. Sélectionnez un thème de couleur.

    ![Capture d’écran de l’étape de sélection de la disposition de l’interface utilisateur hébergée](./media/custom-search-hosted-ui-select-color-theme.png)

4. Spécifiez des options de configuration supplémentaires.

    ![Capture d’écran de l’étape des configurations supplémentaires de l’interface utilisateur hébergée](./media/custom-search-hosted-ui-additional-configurations.png)

5. Collez votre **Subscription key** (Clé d’abonnement). Consultez [Essayer Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).

    ![Capture d’écran de l’étape des configurations supplémentaires de l’interface utilisateur hébergée](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE[publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Consommation de l’interface utilisateur hébergée
Il existe deux façons de consommer l’IU hébergée.  

- Option 1 : intégrer l’extrait de code JavaScript fourni dans votre application.
- Option 2 : utiliser le point de terminaison HTML fourni.

Le reste de ce tutoriel illustre l’**Option 1 : Extrait de code Javascript**.  

## <a name="set-up-your-visual-studio-solution"></a>Configurer votre solution Visual Studio
1. Ouvrez **Visual Studio** sur votre ordinateur.
2. Dans le menu **Fichier**, sélectionnez **Nouveau**, puis choisissez **Projet**.
3. Dans la fenêtre **Nouveau projet**, sélectionnez **Visual C# / Web / Application web ASP.NET Core**, nommez votre projet, puis cliquez sur **OK**.
   
    ![Capture d’écran de la fenêtre Nouveau projet](./media/custom-search-new-project.png)

4. Dans la fenêtre **Nouvelle application ASP.NET Core**, sélectionnez **Application web** et cliquez sur **OK**.
    
    ![Capture d’écran de la fenêtre Nouveau projet](./media/custom-search-new-webapp.png)

## <a name="edit-indexcshtml"></a>Éditer index.cshtml
1. Dans l’**Explorateur de solutions**, développez **Pages** et double-cliquez sur **index.cshtml** pour ouvrir le fichier.

    ![Capture d’écran de l’Explorateur de solutions, où Pages est développé, et le fichier index.cshtml sélectionné](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)

2. Dans index.cshtml, supprimez tout le contenu à partir de la ligne 7 et en dessous.
    
    ``` razor
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }    
    ```

3. Ajoutez un élément de saut de ligne et un élément div pour servir de conteneur.

    ``` html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    <br />
    <div id="customSearch"></div>
    ```

4. À partir de l’onglet **Hosted UI** (IU hébergée), faites défiler jusqu’à la section intitulée **Consuming the UI** (Consommation de l’IU). Copiez l’extrait de code JavaScript.

    ![Capture d’écran de l’onglet de l’interface utilisateur hébergée](./media/custom-search-hosted-ui-consuming-ui.png)

5. Collez l’élément de script dans le conteneur que vous avez ajouté.

    ``` html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    <br />
    <div id="customSearch">
        <script type="text/javascript"
                id="bcs_js_snippet"
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">
        </script>
    </div>
    ```

6. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **wwwroot** et cliquez sur **Afficher dans le navigateur**.

    ![Capture d’écran de l’Explorateur de solutions, avec sélection de l’option Afficher dans le navigateur, à partir du menu contextuel de wwwroot](./media/custom-search-webapp-view-in-browser.png)

Votre nouvelle page web de recherche personnalisée doit ressembler à ceci :

![Capture d’écran d’une page web de recherche personnalisée](./media/custom-search-webapp-browse-index.png)

L’exécution d’une recherche restitue des résultats comme ceux qui suivent.

![Capture d’écran des résultats de la recherche personnalisée](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez :

> [!div class="checklist"]
> - Créé une instance de recherche personnalisée
> - Ajouté des entrées actives
> - Ajouté des entrées bloquées
> - Ajouté des entrées épinglées
> - Intégré la recherche personnalisée à une page web

Vous pouvez à présent passer à l’appel par programmation des points de terminaison de la Recherche personnalisée Bing.

> [!div class="nextstepaction"]
> [Appeler le point de terminaison Recherche personnalisée Bing (C#)](../call-endpoint-csharp.md)