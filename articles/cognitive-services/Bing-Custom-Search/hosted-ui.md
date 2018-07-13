---
title: 'Recherche personnalisée Bing : Recherche sur un site | Microsoft Docs'
description: Explique comment configurer l’IU hébergée
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 593ea4d23f8ddcec8efc4be632afa2aab1a5210f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368840"
---
# <a name="configure-your-hosted-ui-experience"></a>Configurer votre expérience d’interface utilisateur hébergée
Après avoir configuré votre instance de recherche personnalisée, vous pouvez appeler l’API Recherche personnalisée pour obtenir les résultats de la recherche et les afficher dans votre application. Si votre application est une application web, vous pouvez aussi utiliser l’interface utilisateur hébergée qui est fournie par la Recherche personnalisée.   

## <a name="configure-custom-hosted-ui"></a>Configurer l’IU hébergée personnalisée
Utilisez les instructions suivantes pour configurer une interface utilisateur hébergée à inclure dans votre application web.
1.  Connectez-vous au [portail](https://customsearch.ai) de la Recherche personnalisée.
2.  Cliquez sur une instance Recherche personnalisée. Pour créer une instance, consultez [Créer votre première instance Recherche personnalisée Bing](quick-start.md).
3.  Cliquez sur l’onglet **Hosted UI** (IU hébergée).
4.  Sélectionnez une disposition.
    - Search bar and results (default) (Barre de recherche et résultats – Par défaut) &mdash; Affiche une zone de recherche et les résultats de recherche
    - Results only (Résultats uniquement)&mdash; N’affiche pas de zone de recherche, seulement les résultats
    - Pop-over (Pop-over) &mdash; N’affiche pas de zone de recherche, uniquement les résultats en superposition dans une fenêtre contextuelle glissante
    
   > [!IMPORTANT]
   > Veillez à inclure le paramètre de requête customConfig lors de la sélection de la disposition **Results only** (Résultats uniquement) ; consultez [Paramètres de requête](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters).

5.  Sous **Additional Configurations** (Configurations supplémentaires), fournissez des valeurs en fonction de votre application. Ces paramètres sont facultatifs. Pour voir l’effet de leur application ou de leur suppression, regardez le volet de visualisation situé à droite.  Voici les options de configuration disponibles :
    - Configurations de la recherche web :
        - Web results enabled (Résultats Web activés) &mdash; Détermine si les résultats de la recherche Web sont retournés
        - Enable autosuggest (Activer la suggestion automatique) &mdash; Détermine si la suggestion automatique personnalisée est activée
        - Web results per page (Résultats Web par page) &mdash; Nombre de résultats de la recherche Web à afficher à la fois
        - Image caption (Légende de l’image) &mdash; Détermine si les images sont affichées avec les résultats de la recherche
        - Highlight words (Surligner les mots) &mdash; Détermine si les résultats sont affichés avec les termes de la recherche en gras
    - Configurations de la recherche d’images :
        - Image results enabled (Résultats d’images activée) &mdash; Détermine si les résultats de la recherche d’images sont retournés
    - Configurations diverses :
        - Page title (Titre de la page) &mdash; Texte affiché dans la zone de titre de la page
        - Toolbar theme (Thème de la barre d’outils) &mdash; Détermine la couleur d’arrière-plan de la zone de titre de la page
        - Search box text placeholder (Espace réservé au texte de la zone de recherche) &mdash; Texte affiché dans la zone de recherche avant l’entrée de texte
        - Title link url (Url du lien vers le titre) &mdash; Cible du lien vers le titre
        - Logo url (Url du logo) &mdash; Image affichée en regard du titre 
        - Favicon url (Url d’icône favorite) &mdash; Icône affichée dans la barre de titre du navigateur

   > [!IMPORTANT]
   > Au moins une recherche d’images ou une recherche Web doit être activée.

6.  Entrez la clé d’abonnement de la recherche, ou sélectionnez-en une dans la liste déroulante. La liste déroulante est remplie avec les clés des abonnements Azure de votre compte. Consultez [Compte d'API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
7.  Si vous avez activé la suggestion automatique, entrez la clé d’abonnement de la suggestion automatique, ou sélectionnez-en une dans la liste déroulante. La liste déroulante est remplie avec les clés des abonnements Azure de votre compte. La Suggestion automatique personnalisée nécessite un niveau d’abonnement spécifique, consultez les [Tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

> [!NOTE]
> À mesure que vous apportez des modifications à la configuration de l’interface utilisateur hébergée personnalisée, le volet de droite donne une représentation visuelle des modifications effectuées. Les résultats de la recherche affichés ne sont pas les résultats réels de votre instance.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Consommer l’interface utilisateur personnalisée
Pour consommer l’IU hébergée, soit vous : 

- Incluez le script dans votre page web
    ``` html
    <html>
        <body>
            <script type="text/javascript"
                id="bcs_js_snippet"            
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">            
            </script>
        </body>    
    </html>
    ```

- Utilisez l’URL fournie `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`

  > [!IMPORTANT]
  > La page ne peut pas afficher votre déclaration de confidentialité, ou d’autres mentions et conditions. L’adéquation à votre utilisation peut varier.  

Pour obtenir plus d’informations, y compris votre ID de configuration personnalisée, accédez à **Endpoints** (Points de terminaison) sous l’onglet **Production** (Production).

## <a name="next-steps"></a>Étapes suivantes
- [Utiliser des marqueurs d’ornement pour mettre en surbrillance du texte](./hit-highlighting.md)
- [Paginer des pages web](./page-webpages.md)