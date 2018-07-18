---
title: Chargement d’images Bing pour obtenir des informations | Microsoft Docs
description: Application console qui utilise l’API Recherche d’images Bing pour charger une image et obtenir des informations à son sujet.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 12/07/2017
ms.author: v-gedod
ms.openlocfilehash: f0bf32a9951527a072fffe464f6b5f50d0f237a2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367893"
---
# <a name="tutorial-bing-image-upload-for-insights"></a>Didacticiel : chargement d’images Bing pour obtenir des informations

L’API Recherche d’images Bing fournit une option `POST` permettant de charger une image et de rechercher des informations pertinentes à son sujet. Cette application console C# charge une image à l’aide du point de terminaison Recherche d’images pour obtenir des informations.
En résumé, les résultats sont des objets JSON semblables aux résultats suivants :

![[Résultats JSON]](media/cognitive-services-bing-images-api/jsonResult.jpg)

Ce didacticiel explique comment :

> [!div class="checklist"]
> * Utiliser le point de terminaison Recherche d’images `/details` dans une requête `POST`
> * Spécifier des en-têtes pour la requête
> * Utiliser les paramètres d’URL pour spécifier les résultats
> * Télécharger les données de l’image et envoyer la requête `POST`
> * Imprimer les résultats JSON vers la console

## <a name="app-components"></a>Composants de l’application

L’application du didacticiel comprend trois parties :

> [!div class="checklist"]
> * Configuration du point de terminaison pour spécifier le point de terminaison Recherche d’images Bing et les en-têtes nécessaires
> * Téléchargement du fichier image pour la requête `POST` vers le point de terminaison
> * Analyse des résultats JSON qui constituent les détails renvoyés par la requête `POST`

## <a name="scenario-overview"></a>Présentation du scénario
Il existe [trois points de terminaison Recherche d’images](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint). Le point de terminaison `/details` peut utiliser une requête `POST` avec les données de l’image dans le corps de la requête.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Le paramètre d’URL `modules` qui suit `/details?` spécifie le type d’informations qui sont intégrées dans les résultats :
* `modules=All`
* `modules=RecognizedEntities` (personnes ou lieux visibles dans l’image)

Spécifiez `modules=All` dans la requête `POST` pour obtenir du texte JSON comprenant la liste suivante :
* `bestRepresentativeQuery` - une requête Bing qui renvoie des images similaire à l’image chargée
* `detectedObjects` - objets détectés tels qu’un rectangle englobant ou une zone réactive dans l’image
* `image` - métadonnées de l’image
* `imageInsightsToken` - jeton en vue d’une requête `GET` ultérieure obtenant `RecognizedEntities` (personnes ou lieux visibles dans l’image) 
* `imageTags`
* `pagesIncluding` - pages Web où l’image est intégrée
* `relatedSearches`
* `visuallySimilarImages`

Spécifiez `modules=RecognizedEntities` dans la requête `POST` pour n’obtenir que le jeton `imageInsightsToken`, qui sera utilisé dans une requête `GET` ultérieure. Les personnes ou lieux visibles dans l’image seront identifiés.

## <a name="webclient-and-headers-for-the-post-request"></a>WebClient et en-têtes pour la requête POST
Créez un objet `WebClient` et définissez les en-têtes. Pour effectuer des requêtes vers l’API Recherche Bing, vous avez besoin d’une clé `Ocp-Apim-Subscription-Key`. La requête `POST` permettant de charger une image doit également spécifier `ContentType: multipart/form-data`.

```
            WebClient client = new WebClient();
            client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            client.Headers["ContentType"] = "multipart/form-data"; 
```

## <a name="upload-the-image-and-get-results"></a>Télécharger l’image et obtenir des résultats

La classe `WebClient`inclut la méthode`UpLoadFile` qui met en forme les données pour la requête `POST`. Elle met en forme `RequestStream` et appelle `HttpWebRequest` afin de simplifier le processus.
Appelez `WebClient.UpLoadFile` avec le point de terminaison `/details` et le fichier image à télécharger. La réponse comporte des données binaires qui sont facilement converties au format JSON. 

Utilisez le texte JSON pour initialiser une instance de la structure `SearchResult` (consultez le [code source de l’application](tutorial-image-post-source.md) pour en savoir plus sur le contexte).
```        
         const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";

        // The image to upload. Replace with your file and path.
        const string imageFile = "ansel-adams-tetons-snake-river.jpg";
            
        byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
        var json = System.Text.Encoding.Default.GetString(resp);

        // Create result object for return
        var searchResult = new SearchResult()
        {
            jsonResult = json,
            relevantHeaders = new Dictionary<String, String>()
        };
```

## <a name="print-the-results"></a>Imprimer les résultats
Le reste du code analyse le résultat JSON et l’imprime dans la console.

```
        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
```
## <a name="get-request-using-the-imageinsightstoken"></a>Requête GET à l’aide du jeton ImageInsightsToken
Pour utiliser le jeton `ImageInsightsToken` renvoyé avec les résultats d’une requête `POST`, créez une requête `GET` de la façon suivante :
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```
Si des personnes ou des lieux peuvent être identifiés dans l’image, cette requête renverra des informations à leur sujet.
Les [démarrages rapides](https://docs.microsoft.com/azure/cognitive-services/bing-image-search) contiennent de nombreux exemples de code.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Informations de référence sur l’API Recherche d’images Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

