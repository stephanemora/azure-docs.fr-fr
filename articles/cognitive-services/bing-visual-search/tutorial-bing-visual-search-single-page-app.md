---
title: Recherche d’images Bing dans une application web à page unique | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Montre comment utiliser l’API Recherche d’images Bing dans une application web à page unique.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-brapel
ms.openlocfilehash: 303d7745167d2ea25fda083ed99881ac4e0a7ec7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369680"
---
# <a name="tutorial-visual-search-single-page-web-app"></a>Tutoriel : application web à page unique Recherche visuelle

L’API Recherche visuelle de Bing fournit une expérience similaire aux détails de l’image illustrés sur Bing.com/images. Avec la Recherche visuelle, vous pouvez spécifier une image et obtenir des insights sur celle-ci, comme des images visuellement similaires, des sources d’achat, des pages web qui incluent l’image, et bien plus encore. 

Ce tutoriel étend l’application web à page unique du tutoriel Recherche d’images Bing (consultez [Application web à page unique](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)). Pour le code source complet permettant de démarrer ce tutoriel, consultez [Application web à page unique (code source)](../Bing-Image-Search/tutorial-bing-image-search-single-page-app-source.md). Pour le code source final de ce tutoriel, consultez [Application web à page unique Recherche visuelle](tutorial-bing-visual-search-single-page-app-source.md).

Les tâches traitées sont les suivantes :

> [!div class="checklist"]
> * Appeler l’API Recherche visuelle Bing avec un jeton d’insights d’image
> * Afficher des images similaires

## <a name="call-bing-visual-search"></a>Appeler la Recherche visuelle Bing
Modifiez le tutoriel Recherche d’images Bing et ajoutez le code suivant à la fin de l’élément de script à la ligne 409. Ce code appelle l’API Recherche visuelle Bing et affiche les résultats.

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}

function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;       
    
    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>Capturer le jeton d’insights
Ajoutez le code suivant à l’objet `searchItemsRenderer` à la ligne 151. Ce code ajoute un lien **Rechercher semblables** qui appelle la fonction `bingVisualSearch` quand vous cliquez dessus. La fonction reçoit imageInsightsToken en tant qu’argument.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Afficher des images similaires
Ajoutez le code HTML suivant à la ligne 601. Ce code de balisage ajoute un élément utilisé pour afficher les résultats de l’appel de l’API Recherche visuelle Bing.

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Avec l’ensemble du nouveau code JavaScript et des nouveaux éléments HTML en place, les résultats de la recherche sont affichés avec un lien **Rechercher semblables**. Cliquez sur le lien pour remplir la section **Semblable** avec des images similaires à celle que vous avez choisie. Vous devrez peut-être développer la section **Semblable** pour afficher les images.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Source d’application web à page unique Recherche visuelle](tutorial-bing-visual-search-single-page-app-source.md)
> [Référence de l’API Recherche visuelle Bing](https://aka.ms/bingvisualsearchreferencedoc)