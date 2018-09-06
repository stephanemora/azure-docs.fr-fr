---
title: Démarrage rapide JavaScript pour l’aperçu d’URL de projet - Microsoft Cognitive Services | Microsoft Docs
description: Exemple de script pour la prise en main rapide avec l’API d’aperçu d’URL Bing dans Microsoft Cognitive Services sur Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: dda6f7c105dfbadc3c22f0c008aa8759fe12fa03
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301350"
---
# <a name="url-preview-in-javascript"></a>Aperçu d’URL en JavaScript 

L’application suivante sur une seule page utilise JavaScript pour créer un aperçu d’URL pour le site SwiftKey : https://swiftkey.com/en. 

## <a name="prerequisites"></a>Prérequis

Obtenir une clé d’accès pour l’essai gratuit [Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-url-preview)

## <a name="code-scenario"></a>Scénario de code
L’exemple javascript suivant inclut un objet d’entrée de zone de texte où l’utilisateur entre l’URL dont l’aperçu doit être affiché.  Lorsque l’utilisateur clique sur le bouton **Aperçu**, la méthode sur clic est acheminée jusqu’à `getPreview` où le code génère une requête web vers le point de terminaison **UrlPreview**.

Le code crée une *XMLHttpRequest*, ajoute l’en-tête *Ocp-Apim-abonnement-Key* et envoie la requête.  Il ajoute un gestionnaire d’événements asynchrones pour traiter la réponse.

Si la réponse est retournée avec succès, le gestionnaire affecte le texte JSON de la réponse au paragraphe `demo` de la page. D’autres éléments de réponse sont définis dans les paragraphes suivants pour affichage.

**Réponse brute JSON**

````
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https://swiftkey.com/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

````

**La démonstration en cours d’exécution**

![Exemple d’aperçu d’Url JavaScript](./media/java-script-demo.png)

## <a name="running-the-application"></a>Exécution de l'application

Pour exécuter l’application :

1. Remplacez la valeur `YOUR-SUBSCRIPTION-KEY` par une clé d’accès valide pour votre abonnement.
2. Enregistrez le code HTML et le script dans un fichier avec l’extension .html.
3. Exécutez la page web dans un navigateur.
4. Utilisez l’URL existante, ou entrez en une autre dans la zone de texte.
5. Cliquez sur le bouton **Aperçu**.

**Code source :**

```
<!DOCTYPE html>

<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="utf-8" />
    <title>urlPreview Demo</title>
</head>
<body>
    <h3>URL Preview Demo</h3>

    Page to preview: <input type="url" id="myURL" value="https://swiftkey.com/en">
    <button onclick="getPreview()">Preview</button>

    <p id="demo"></p>
    <br />
    <p id="jsonDesc"></p>
    <p><a id="familyFriendly"></a></p>
    <a id="contentUrl"></a>
    <p />
    <img id="jsonImage" />

    <script>

        var BING_ENDPOINT = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search"; 
        var key = "YOUR-SUBSCRIPTION-KEY";
        var xhr;

        function getPreview() {
            xhr = new XMLHttpRequest();

            var queryUrl = BING_ENDPOINT + "?q=" +
                encodeURIComponent(document.getElementById("myURL").value);
            xhr.open('GET', queryUrl, true);
            xhr.setRequestHeader("Ocp-Apim-Subscription-Key", key);

            xhr.send();

            xhr.addEventListener("readystatechange", processRequest, false);
        }

        function processRequest(e) {

            if (xhr.readyState == 4 && xhr.status == 200) {
                document.getElementById("demo").innerHTML = xhr.responseText;
                var obj = JSON.parse(xhr.responseText);
                document.getElementById("jsonDesc").innerHTML = obj.description;
                document.getElementById("familyFriendly").innerHTML = "Family Friendly: " + obj.isFamilyFriendly;
                document.getElementById("contentUrl").innerHTML = obj.url;
                document.getElementById("contentUrl").href = obj.url;
                document.getElementById("jsonImage").width = 350;
                document.getElementById("jsonImage").src = obj.primaryImageOfPage.contentUrl;

            }

        }

    </script>

</body>
</html>

```

## <a name="next-steps"></a>Étapes suivantes
- [Démarrage rapide C#](csharp.md)
- [Démarrage rapide Java](java-quickstart.md)
- [Démarrage rapide Node](node-quickstart.md)
- [Démarrage rapide Python](python-quickstart.md)
