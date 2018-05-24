---
title: Explorer la bibliothèque cliente JavaScript de Time Series Insights
description: Obtenez des informations sur la bibliothèque cliente JavaScript de Time Series Insights et le modèle de programmation associé.
documentationcenter: ''
services: time-series-insights
author: BryanLa
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: time-series-insights
ms.workload: na
ms.tgt_pltfrm: ''
ms.devlang: na
ms.topic: tutorial
ms.date: 05/16/2018
ms.author: bryanla
ms.openlocfilehash: 3fbd4f54fb511ae737abf28ae7b1b50750ab5d69
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210475"
---
# <a name="tutorial-explore-the-time-series-insights-javascript-client-library"></a>Tutoriel : Explorer la bibliothèque cliente JavaScript de Time Series Insights

Pour aider les développeurs à exécuter des requêtes afin de visualiser les données stockées dans Time Series Insights (TSI), nous avons développé une bibliothèque de contrôles JavaScript basée sur D3 qui facilite cette tâche. Ce tutoriel utilise un exemple d’application web pour vous guider dans une exploration de la bibliothèque cliente JavaScript de TSI et du modèle de programmation associé.

Les rubriques abordées vous donnent l’opportunité de faire des essais, de mieux comprendre comment accéder aux données TSI, et d’utiliser des contrôles de graphique pour afficher et visualiser ces données. L’objectif est de vous fournir suffisamment de détails pour vous permettre d’utiliser cette bibliothèque dans votre propre application web.

Ce tutoriel vous fournira des informations sur :

> [!div class="checklist"]
> * L’exemple d’application TSI
> * La bibliothèque cliente JavaScript de TSI
> * La façon dont l’exemple d’application utilise la bibliothèque pour visualiser les données TSI

## <a name="prerequisites"></a>Prérequis


Ce tutoriel utilise la fonctionnalité « Outils de développement » (également appelée DevTools ou F12), présente dans les navigateurs web les plus récents tels que [Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/), etc. Si elle ne vous est pas encore familière, vous pouvez explorer cette fonctionnalité dans votre navigateur avant de continuer.

## <a name="the-time-series-insights-sample-application"></a>Exemple d’application Time Series Insights

Dans ce didacticiel, l’exemple d’application Azure Time Series Insight est utilisé pour explorer le code source derrière l’application, y compris l’utilisation de la bibliothèque cliente JavaScript de TSI. Cette application est une application web monopage qui illustre l’utilisation de la bibliothèque pour l’exécution de requêtes et la visualisation de données à partir d’un exemple d’environnement TSI.

1. Accédez à l’[exemple d’application Time Series Insights](https://insights.timeseries.azure.com/clientsample). Vous voyez une page semblable à la suivante, qui vous invite à vous connecter : ![Exemple d’invite de connexion au client TSI](media/tutorial-explore-js-client-lib/tcs-sign-in.png)

2. Cliquez sur le bouton « Log in » (Se connecter) et entrez ou sélectionnez vos informations d’identification. Vous pouvez utiliser un compte d’entreprise/organisation (Azure Active Directory) ou un compte personnel (compte Microsoft ou compte de service administré).

   ![Exemple d’invite de saisie d’informations d’identification de client TSI](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)

3. Une fois connecté, vous voyez une page semblable à la suivante, qui contient plusieurs styles de graphique d’exemple, remplis avec des données TSI. Notez également votre compte d’utilisateur et le lien « log out » de déconnexion dans le coin supérieur droit : ![Exemple de page principale de client TSI après connexion](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)

### <a name="page-source-and-structure"></a>Structure et source de la page

Commençons par examiner le code source HTML et JavaScript derrière la page affichée dans votre navigateur. Nous ne présentons pas tous les éléments, mais vous découvrez les sections principales qui vous donnent une idée de la façon dont la page fonctionne :

1. Ouvrez « Outils de développement » dans votre navigateur et inspectez les éléments HTML qui composent la page active, également connue sous le nom d’arborescence HTML ou DOM.

2. Développez les éléments `<head>` et `<body>`, et notez les sections suivantes :
   - Sous `<head>`, vous trouvez des éléments qui extraient des fichiers supplémentaires pour faciliter le fonctionnement de la page :
     - un élément `<script>` pour référencer la bibliothèque d’authentification Azure Active Directory (adal.min.js), également connue sous le nom de bibliothèque ADAL. Il s’agit d’une bibliothèque JavaScript qui fournit l’authentification OAuth 2.0 (connexion) et l’acquisition de jeton pour accéder aux API :
     - des éléments `<link>` pour les feuilles de style (sampleStyles.css, tsiclient.css), également connues sous le nom de feuilles CSS. Elles permettent de contrôler les détails du style visuel de la page, tels que les couleurs, les polices, l’espacement, etc.
     - un élément `<script>` pour référencer la bibliothèque du client TSI (tsiclient.js), une bibliothèque JavaScript utilisée par la page pour appeler les API du service TSI et afficher les contrôles de graphique dans la page.

     >[!NOTE]
     > Le code source pour la bibliothèque JavaScript ADAL est disponible à partir du [référentiel azure-activedirectory-library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > Le code source pour la bibliothèque JavaScript du client TSI est disponible à partir du [référentiel tsiclient](https://github.com/Microsoft/tsiclient).

   - Sous `<body>`, vous trouvez les éléments `<div>`, qui agissent comme des conteneurs pour définir la disposition des éléments dans la page, et un autre élément `<script>` :
     - Le premier `<div>` spécifie la boîte de dialogue de connexion « Log In » (`id="loginModal"`).
     - Le second `<div>` agit comme parent pour :
       - un en-tête `<div>`, utilisé pour les messages d’état et les informations de connexion en haut de la page (`class="header"`).
       - un élément `<div>` pour le reste des éléments du corps de la page, y compris tous les graphiques (`class="chartsWrapper"`).
       - une section `<script>`, qui contient tout le code JavaScript utilisé pour contrôler la page.

   [![Exemple de client TSI avec DevTools](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

3. Développez l’élément `<div class="chartsWrapper">` pour trouver plus d’éléments `<div>` enfants, utilisés pour positionner chaque exemple de contrôle de graphique. Notez qu’il existe plusieurs paires d’éléments `<div>`, une pour chaque exemple de graphique :
   - Le premier élément (`class="rowOfCardsTitle"`) contient un titre descriptif qui résume ce que le ou les graphiques illustrent. Par exemple : « Graphiques en courbes statiques avec légendes pleine taille »
   - Le second élément (`class="rowOfCards"`) est un parent contenant des éléments `<div>` enfants supplémentaires qui positionnent le ou les contrôles de graphique réels dans une ligne.

  ![Affichage des éléments « div » du corps](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)

4. À présent, développez l’élément `<script type="text/javascript">`, directement sous l’élément `<div class="chartsWrapper">`. Vous voyez le début de la section JavaScript de niveau page, utilisée pour gérer toute la logique de la page pour des choses telles que l’authentification, l’appel des API du service TSI, le rendu des contrôles de graphique, etc. :

  ![Affichage du script du corps](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)

## <a name="tsi-client-javascript-library-concepts"></a>Concepts liés à la bibliothèque JavaScript du client TSI

Même si nous ne l’examinons pas en détail, fondamentalement, la bibliothèque du client TSI (tsclient.js) fournit une abstraction pour deux catégories importantes :

- **Méthodes de wrapper pour appeler les API de requête TSI** - API REST qui vous permettent d’exécuter des requêtes pour obtenir des données TSI à l’aide d’expressions d’agrégation et qui sont organisées sous l’espace de noms `TsiClient.Server` de la bibliothèque.
- **Méthodes pour créer et remplir plusieurs types de contrôles de graphique** - Utilisées pour afficher les données d’agrégation TSI dans une page web et organisées sous l’espace de noms `TsiClient.UX` de la bibliothèque.

Les concepts suivants sont universels et applicables aux API de la bibliothèque du client TSI en général.

### <a name="authentication"></a>Authentification

Comme mentionné précédemment, cet exemple est une application monopage qui utilise la prise en charge d’OAuth 2.0 dans la bibliothèque ADAL pour l’authentification utilisateur. Voici quelques points d’intérêt propres à cette section du script :

1. L’utilisation de la bibliothèque ADAL pour l’authentification exige que l’application cliente s’inscrive dans le registre d’application Azure Active Directory (Azure AD). En tant qu’application monopage, cette application est inscrite pour utiliser le flux d’octroi d’autorisation OAuth 2.0 « implicite ». En conséquence, l’application spécifie certaines propriétés d’inscription lors de l’exécution, telles que le GUID d’ID client (`clientId`) et l’URI de redirection (`postLogoutRedirectUri`), pour participer au flux.

2. Ultérieurement, l’application demande un « jeton d’accès » auprès d’Azure AD. Ce jeton d’accès est émis pour un ensemble limité d’autorisations, pour un identificateur de service/d’API spécifique (https://api.timeseries.azure.com), également connu sous le nom d’audience de jeton. Les autorisations de jeton sont émises pour le compte de l’utilisateur connecté. L’identificateur du service/de l’API est encore une autre propriété contenue dans l’inscription de l’application auprès d’Azure AD. Une fois que la bibliothèque ADAL retourne le jeton d’accès à l’application, il est transmis en tant que « jeton du porteur » lors de l’accès aux API du service TSI.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=145-204&highlight=4-9,36-39)]

### <a name="control-identification"></a>Identification du contrôle

Comme indiqué précédemment, les éléments `<div>` inclus dans `<body>` fournissent la disposition de tous les contrôles de graphique illustrés dans la page. Chacun d’eux spécifie des propriétés pour les attributs visuels et les attributs de placement du contrôle de graphique, y compris une propriété `id`. Cette propriété `id` fournit un identificateur unique, qui est utilisé dans le code JavaScript pour identifier et lier chaque contrôle pour le rendu et la mise à jour.

### <a name="aggregate-expressions"></a>Expressions d’agrégation

Les API de la bibliothèque du client TSI font une utilisation intensive d’expressions d’agrégation. Une expression d’agrégation confère la capacité de construire un ou plusieurs « termes de recherche ». Les API sont similaires à la façon dont l’[explorateur Time Series Insights](https://insights.timeseries.azure.com/demo) utilise la période de recherche, le prédicat where, les mesures et le fractionnement par valeur. La plupart des API de la bibliothèque prennent un tableau d’expressions d’agrégation, qui sont utilisées par le service pour générer une requête de données TSI.

### <a name="call-pattern"></a>Modèle d’appel

Le remplissage et le rendu des contrôles de graphique suivent un modèle général. Ce modèle est utilisé dans l’ensemble du code JavaScript de la page, qui instancie et charge les contrôles de l’exemple d’application TSI :

1. Déclarez un tableau contenant une ou plusieurs expressions d’agrégation TSI.

   ```javascript
   var aes =  [];
   ```

2. Générez les objets d’expression d’agrégation 1 à n, et ajoutez-les dans le tableau des expressions d’agrégation.

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```
   **Paramètres d’aggregateExpression**

   | Paramètre | Description | Exemples |
   | --------- | ----------- | ------- |
   | predicateObject | Expression de filtrage de données. |`{predicateString: "Factory = 'Factory3'"}` |
   | measureObject   | Nom de propriété de la mesure utilisée. | `{property: 'Temperature', type: "Double"}` |
   | measureTypes    | Agrégations souhaitées de la propriété de mesure. | `['avg', 'min']` |
   | searchSpan      | Durée et taille d’intervalle de l’expression d’agrégation. | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | splitByObject   | Propriété de chaîne que vous souhaitez utiliser pour le fractionnement (facultative, peut être null). | `{property: 'Station', type: 'String'}` |
   | color           | Couleur des objets que vous souhaitez afficher. | `'pink'` |
   | alias           | Nom convivial de l’expression d’agrégation. | `'Factory3Temperature'` |
   | contextMenuActions | Tableau d’actions à lier aux objets de série chronologique dans une visualisation (facultatif). | Consultez [Menus contextuels de la section Fonctionnalités avancées](#popup-context-menus). |

3. Appelez une requête TSI à l’aide d’API `TsiClient.Server` pour demander les données d’agrégation.

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```
   **Paramètres de getAggregates**

   | Paramètre | Description | Exemples |
   | --------- | ----------- | ------- |
   | token     | Jeton d’accès pour l’API TSI | `authContext.getTsiToken()` Voir la [section d’authentification](#authentication). |
   | envFQDN     | Nom de domaine complet pour l’environnement TSI | À partir du portail Azure, par exemple `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com` |
   | aeTsxArray | Tableau d’expressions de requête TSI | Utilisez la variable `aes` comme décrit précédemment : `aes.map(function(ae){return ae.toTsx()}` |

4. Transformez le résultat compressé retourné de la requête TSI, en langage JSON pour la visualisation.

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

5. Créez un contrôle de graphique à l’aide des API `TsiClient.UX` et liez-le à l’un des éléments `<div>` dans la page.

   ```javascript
   var lineChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

6. Remplissez le contrôle de graphique avec le ou les objets de données JSON transformés et affichez-le dans la page.

   ```javascript
   lineChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Contrôles de rendu

La bibliothèque expose actuellement huit contrôles d’analyse uniques. Ils incluent un graphique en courbes, un graphique à secteurs, un graphique à barres, une carte thermique, des contrôles de hiérarchie, une grille accessible, des chronologies d’événement discrètes et des chronologies de transition d’état.

### <a name="line-bar-pie-chart-examples"></a>Exemples de graphiques en courbes, à barres et à secteurs

Commençons par examiner le code derrière certains contrôles de graphique standard illustrés dans l’application, et le modèle/les motifs de programmation permettant de les créer. Plus précisément, vous examinez la section de code HTML sous le commentaire `// Example 3/4/5`, qui affiche les contrôles avec les valeurs d’ID `chart3`, `chart4` et `chart5`.

Comme nous l’avons vu à l’étape 3 de la [section Structure et source de la page](#page-source-and-structure), les contrôles de graphique sont organisés en lignes dans la page et chacun a une ligne de titre descriptive. Dans cet exemple, les trois graphiques qui sont remplis figurent tous sous l’élément `<div>` du titre « Plusieurs types de graphiques à partir des mêmes données », lié aux trois éléments `<div>` situés en dessous :

[!code-javascript[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

La section suivante du code JavaScript utilise le modèle décrit précédemment pour générer des expressions d’agrégation TSI, les utiliser pour exécuter des requêtes afin d’obtenir des données TSI, et afficher les trois graphiques. Notez les trois types utilisés à partir de l’espace de noms `tsiClient.ux`, `LineChart`, `BarChart`, `PieChart`, pour créer et afficher les graphiques respectifs. Notez également que les trois graphiques sont en mesure d’utiliser les mêmes données d’expression d’agrégation, `transformedResult` :

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

Ces trois graphiques apparaissent comme suit lors de leur affichage :

[![Plusieurs types de graphiques à partir des mêmes données](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Fonctionnalités avancées

La bibliothèque expose également certaines fonctionnalités avancées facultatives dont vous pouvez tirer parti.

### <a name="states-and-events"></a>États et événements

Un exemple des fonctionnalités avancées fournies est la possibilité d’ajouter des transitions d’état et des événements discrets aux graphiques. Cette fonctionnalité est utile pour mettre en évidence les incidents, émettre des alertes et indiquer l’état activé/désactivé des commutateurs.

Examinons ici le code derrière la section de code HTML sous le commentaire `// Example 10`. Ce code affiche un contrôle de ligne sous le titre « Graphiques en courbes avec plusieurs types de séries » qui le lie à l’élément `<div>` avec la valeur d’ID `chart10` :

1. Tout d’abord, une structure nommée `events4` est définie qui contiendra les éléments de changement d’état à suivre. Il contient :
   - Une clé de chaîne nommée `"Component States"`
   - Un tableau d’objets de valeur qui représentent les états, dont chacun inclut :
     - Une clé de chaîne contenant un horodateur ISO JavaScript
     - Un tableau contenant les caractéristiques de l’état
       - une couleur
       - une description

2. Ensuite, la structure `events5` est définie pour `"Incidents"`, qui contient un tableau des éléments d’événement à suivre. La structure de tableau a la forme décrite pour `events4`.

3. Enfin, le graphique en courbes est affiché avec les deux structures et les paramètres des options de graphique : `events:` et `states:`. Notez les autres paramètres d’option pour spécifier un élément `tooltip:`, `theme:` ou `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Visuellement, les menus/marqueurs en forme de losange sont utilisés pour indiquer les incidents, et les menus/barres de couleur le long de l’échelle chronologique indiquent les changements d’état :

[![Graphiques en courbes avec plusieurs types de séries](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="popup-context-menus"></a>Menus contextuels

Un autre exemple de fonctionnalité avancée sont les menus contextuels personnalisés (menus contextuels de clic droit), qui permettent d’activer des actions et des étapes logiques ultérieures dans le cadre de votre application.

Nous étudions ici le code derrière le code HTML sous `// Example 13/14/15`. Ce code affiche initialement un graphique en courbes sous le titre « Graphique en courbes avec menu contextuel pour créer le graphique à secteurs/barres », lié à l’élément `<div>` avec la valeur d’ID `chart13`. À l’aide de menus contextuels, le graphique en courbes permet de créer dynamiquement un graphique à secteurs et un graphique à barres, liés aux éléments `<div>` avec les ID `chart14` et `chart15`. En outre, les graphiques à secteurs et à barres utilisent également des menus contextuels pour proposer leurs propres fonctionnalités : la capacité de copier des données du graphique à secteurs au graphique à barres, et d’imprimer les données du graphique à barres dans la fenêtre de console du navigateur, respectivement.

1. Tout d’abord, une série d’actions personnalisées sont définies. Chacune contient un tableau avec un ou plusieurs éléments, et chaque élément définit un élément de menu contextuel unique :
   - `barChartActions` : définit le menu contextuel pour le graphique à secteurs, qui contient un seul élément pour définir un élément individuel :
     - `name` : le texte utilisé pour l’élément de menu : « Imprimer les paramètres dans la console »
     - `action` : action associée à l’élément de menu, qui est toujours une fonction anonyme acceptant trois arguments en fonction de l’expression d’agrégation utilisée pour créer le graphique. Dans ce cas, ils sont écrits dans la fenêtre de console du navigateur :
       - `ae` : tableau des expressions d’agrégation
       - `splitBy` : valeur splitBy
       - `timestamp` : horodateur
   - `pieChartActions` : définit le menu contextuel pour le graphique à barres, qui contient un seul élément pour définir un élément individuel. La forme et le schéma sont identiques à l’élément `barChartActions`, mais notez que la fonction `action` est radicalement différente, car elle instancie et affiche le graphique à barres. Notez également qu’elle utilise l’argument `ae` pour spécifier le tableau des expressions d’agrégation, transmis lors de l’exécution, pendant l’apparition de l’élément de menu. La fonction définit également la propriété `ae.contextMenu` avec le menu contextuel `barChartActions`.
   - `contextMenuActions` : définit le menu contextuel pour le graphique en courbes, qui contient trois éléments pour définir trois éléments de menu. La forme et le schéma de chaque élément sont identiques aux précédents. Tout comme `barChartActions`, le premier élément écrit les trois arguments de fonction dans la fenêtre de console du navigateur. De façon similaire à `pieChartActions`, les deux seconds éléments instancient et affichent les graphiques à secteurs et à barres, respectivement. Les deux seconds éléments définissent également leurs propriétés `ae.contextMenu` avec les menus contextuels `pieChartActions` et `barChartActions`, respectivement.

2. Ensuite, deux expressions d’agrégation sont envoyées sur le tableau d’expressions d’agrégation `aes`, spécifiant le tableau `contextMenuActions` pour chacune. Elles sont utilisées avec le contrôle de graphique en courbes.

3. Enfin, seul le graphique en courbes est initialement affiché. Les graphiques à secteurs et à barres peuvent être affichés à partir de celui-ci lors de l’exécution.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

La capture d’écran montre les graphiques avec leurs menus contextuels respectifs. Les graphiques à secteurs et à barres ont été créés dynamiquement à l’aide des options du menu contextuel du graphique en courbes :

[![Graphique en courbes avec menu contextuel pour créer le graphique à secteurs/barres](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pinceaux

Les pinceaux peuvent être utilisés pour délimiter une plage de temps afin de définir des actions telles que le zoom et l’exploration.

Le code utilisé pour illustrer les pinceaux apparaît également dans l’exemple précédent « Graphique en courbes avec menu contextuel pour créer le graphique à secteurs/barres », couvrant les [menus contextuels](#popup-context-menus-section).

1. Les actions de pinceau sont très semblables à un menu contextuel et définissent une série d’actions personnalisées pour le pinceau. Chacune contient un tableau avec un ou plusieurs éléments, et chaque élément définit un élément de menu contextuel unique :
   - `name` : le texte utilisé pour l’élément de menu : « Imprimer les paramètres dans la console »
   - `action` : action associée à l’élément de menu, toujours une fonction anonyme acceptant deux arguments. Dans ce cas, ils sont écrits dans la fenêtre de console du navigateur :
      - `fromTime` : horodateur « de » de la sélection de pinceaux
      - `toTime` : horodateur « à » de la sélection de pinceaux

2. Les actions de pinceau sont ajoutées sous la forme d’une autre propriété d’option de graphique. Remarquez la propriété `brushContextMenuActions: brushActions` transmise à l’appel `linechart.Render`.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

![Graphique en courbes avec menu contextuel pour créer le graphique à secteurs/barres à l’aide des pinceaux](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Vous connecter et explorer l’exemple d’application TSI et sa source
> * Utiliser les API dans la bibliothèque cliente JavaScript de TSI
> * Utiliser JavaScript pour créer et remplir des contrôles de graphique avec des données TSI

Comme indiqué, l’exemple d’application TSI utilise un jeu de données de démonstration. Pour en savoir plus sur la façon de créer vos propres environnement et jeu de données TSI, consultez l’article suivant :

> [!div class="nextstepaction"]
> [Planifier votre environnement Azure Time Series Insights](time-series-insights-environment-planning.md)


