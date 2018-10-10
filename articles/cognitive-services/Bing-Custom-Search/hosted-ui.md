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
ms.openlocfilehash: 2aec8ba969fb639f2d785a429441f6ed4bbf7dfb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987677"
---
# <a name="configure-your-hosted-ui-experience"></a>Configurer votre expérience d’interface utilisateur hébergée

Après avoir configuré votre instance de recherche personnalisée, vous pouvez appeler l’API Recherche personnalisée pour obtenir les résultats de la recherche et les afficher dans votre application. Si votre application est une application web, vous pouvez aussi utiliser l’interface utilisateur hébergée qui est fournie par la Recherche personnalisée.   

## <a name="configure-custom-hosted-ui"></a>Configurer l’IU hébergée personnalisée

Si vous souhaitez configurer une interface utilisateur hébergée pour votre application web, effectuez les étapes suivantes :

1. Connectez-vous au [portail](https://customsearch.ai) de la Recherche personnalisée.  
  
2. Cliquez sur une instance Recherche personnalisée. Pour créer une instance, consultez [Créer votre première instance Recherche personnalisée Bing](quick-start.md).  

3. Cliquez sur l’onglet **Hosted UI** (IU hébergée).  
  
4. Sélectionnez une disposition.
  
  - Search bar and results (Barre de recherche et de résultats) - par défaut &mdash; Cette disposition correspond à la page de recherche traditionnelle, avec une zone de recherche et des résultats de la recherche.
  - Results only (Résultats uniquement) &mdash; Cette disposition affiche uniquement les résultats de recherche. Cette mise en page n’affiche pas de zone de recherche. Vous devez fournir la requête de recherche en ajoutant le paramètre de requête (&q=\<chaîne de requête>) à l’URL de la requête dans l’extrait de code JavaScript ou dans le lien de point de terminaison HTML.
  - Pop-over (Fenêtre superposée glissante) &mdash; Cette disposition fournit une zone de recherche et affiche les résultats de recherche dans une fenêtre glissante qui vient se superposer à la fenêtre actuelle.
      
5. Sélectionnez un thème de couleur. Les thèmes possibles sont les suivants : 
  
  - Classique
  - Foncé
  - Skyline Blue (Bleu horizon)

  Cliquez sur chacun de ces thèmes pour voir lequel convient le mieux à votre application web. Si vous avez besoin de régler le thème de couleur pour mieux l’intégrer à votre application web, cliquez sur **Customize theme** (Personnaliser le thème). Certaines configurations de couleur ne s’appliquent pas à tous les thèmes de disposition. Pour changer une couleur, entrez la valeur HEX RVB de la couleur (par exemple, #366eb8) dans la zone de texte correspondante. Sinon, cliquez sur le bouton de couleur, puis sur la nuance qui vous convient. 
  
  Après avoir modifié une couleur, regardez l’aperçu qui s’affiche sur la droite. Vous pouvez toujours cliquer sur **Rétablir les valeurs par défaut** pour rétablir les couleurs par défaut du thème sélectionné.

  > [!NOTE]
  > Lorsque vous modifiez le thème d’une couleur, prenez en compte l’accessibilité.

5. Sous **Additional Configurations** (Configurations supplémentaires), fournissez des valeurs en fonction de votre application. Ces paramètres sont facultatifs. Pour voir l’effet de leur application ou de leur suppression, regardez le volet de visualisation situé à droite. Voici les options de configuration disponibles :  
  
  - Configurations de la recherche web :
    - Web results enabled (Résultats web activés) &mdash; Détermine si la recherche sur le Web est activée (si c’est le cas, vous voyez l’onglet Web en haut de la page).
    - Enable autosuggest (Activer la suggestion automatique) &mdash; Détermine si la suggestion automatique personnalisée est activée (voir les [Tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) pour connaître les frais supplémentaires).
    - Web results per page (Résultats web par page) &mdash; Nombre de résultats à afficher pour une recherche sur le Web (le maximum est de 50 résultats par page).
    - Image caption (Légende de l’image) &mdash; Détermine si des images sont affichées avec les résultats de la recherche
  
    Les configurations suivantes sont affichées si vous cliquez sur **Show advanced configurations** (Afficher les configurations avancées).  
  
    - Highlight words (Mettre les mots en gras) &mdash; Détermine si les termes de recherche apparaissent en gras dans les résultats de recherche. 
    - Link target (Cible du lien) &mdash; Détermine si la page web s’ouvre dans un nouvel onglet de navigateur (vide) ou dans le même onglet de navigateur lorsque l’utilisateur clique sur un résultat de recherche. 

  - Configurations de la recherche d’images :
    - Image results enabled (Résultats d’images activés) &mdash; Détermine si la recherche d’images est activée (si c’est le cas, vous voyez l’onglet Images en haut de la page).   
    - Image results per page (Résultats d’images par page) &mdash; Nombre de résultats à afficher pour une recherche d’images (le maximum est de 150 résultats par page).  
  
    La configuration suivante est affichée si vous cliquez sur **Show advanced configurations** (Afficher les configurations avancées).  
  
    - Enable filters (Activer les filtres) &mdash; Ajoute des filtres dont l’utilisateur peut se servir pour filtrer les images retournées par Bing. Par exemple, l’utilisateur peut filtrer les résultats pour n’afficher que des fichiers GIF animés.

  - Configurations pour la recherche de vidéos:
    - Video results enabled (Résultats vidéos activés) &mdash; Détermine si la recherche de vidéos est activée (si c’est le cas, vous voyez l’onglet Vidéos en haut de la page).  
    - Video results per page (Résultats vidéos par page) &mdash; Nombre de résultats à afficher pour une recherche de vidéo (le maximum est de 150 résultats par page).
  
    La configuration suivante est affichée si vous cliquez sur **Show advanced configurations** (Afficher les configurations avancées).  
  
    - Enable filters (Activer les filtres) &mdash; Ajoute des filtres dont l’utilisateur peut se servir pour filtrer les vidéos retournées par Bing. Par exemple, l’utilisateur peut filtrer les résultats pour n’afficher que les vidéos ayant une résolution spécifique ou les vidéos découvertes au cours des dernières 24 heures.

  - Configurations diverses :
    - Page title (Titre de la page) &mdash; Texte affiché dans la zone de titre de la page des résultats de recherche (ne s’applique pas à la disposition de fenêtre superposée glissante).
    - Toolbar theme (Thème de la barre d’outils) &mdash; Détermine la couleur d’arrière-plan de la zone de titre de la page des résultats de recherche.  
  
    Les configurations suivantes sont affichées si vous cliquez sur **Show advanced configurations** (Afficher les configurations avancées).  
  
    - Search box text placeholder (Espace réservé au texte de la zone de recherche) &mdash; Texte affiché dans la zone de recherche avant l’entrée de texte.
    - Title link URL (URL du lien vers le titre) &mdash; Cible du lien vers le titre.
    - Logo URL (URL du logo) &mdash; Image affichée à côté du titre. 
    - Favicon URL (URL d’icône favorite) &mdash; Icône affichée dans la barre de titre du navigateur.  

    Les configurations suivantes s’appliquent uniquement si vous utilisez l’interface utilisateur hébergée via le point de terminaison HTML (elles ne s’appliquent pas si vous utilisez l’extrait de code JavaScript).
    
    - Titre de la page
    - Thème de la barre d’outils
    - URL du lien de titre
    - URL du logo
    - URL d’icône favorite  
  
6. Entrez la clé d’abonnement de la recherche, ou sélectionnez-en une dans la liste déroulante. La liste déroulante est remplie avec les clés des abonnements de votre compte Azure. Consultez [Compte d'API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

7. Si vous avez activé la suggestion automatique, entrez la clé d’abonnement de la suggestion automatique, ou sélectionnez-en une dans la liste déroulante. La liste déroulante est remplie avec les clés des abonnements de votre compte Azure. La Suggestion automatique personnalisée nécessite un niveau d’abonnement spécifique. Pour plus d’informations, consultez les [Tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

> [!NOTE]
> À mesure que vous apportez des modifications à la configuration de l’interface utilisateur hébergée personnalisée, le volet de droite donne une représentation visuelle des modifications effectuées. Les résultats de recherche qui sont affichés ne sont pas les résultats réels de votre instance.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Consommer l’interface utilisateur personnalisée

Pour consommer l’IU hébergée, soit vous : 

- Incluez le script dans votre page web  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- Vous pouvez également utiliser l’URL suivante dans un navigateur web.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Ajoutez les paramètres de requête suivants à l’URL selon vos besoins. Pour plus d’informations sur ces paramètres, consultez la référence [API Recherche personnalisée](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters).
  >
  > - q
  > - mkt
  > - safesearch
  > - setlang

  > [!IMPORTANT]
  > La page ne peut pas afficher votre déclaration de confidentialité, ou d’autres mentions et conditions. L’adéquation à votre utilisation peut varier.  

Pour obtenir plus d’informations, y compris votre ID de configuration personnalisée, accédez à **Endpoints** (Points de terminaison) sous l’onglet **Production** (Production).

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser des marqueurs d’ornement pour mettre en surbrillance du texte](./hit-highlighting.md)
- [Paginer des pages web](./page-webpages.md)