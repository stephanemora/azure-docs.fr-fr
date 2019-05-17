---
title: 'Didacticiel : Explorer la bibliothèque cliente JavaScript de Azure Time Series Insights | Microsoft Docs'
description: Obtenez des informations sur la bibliothèque cliente JavaScript de Azure Time Series Insights et le modèle de programmation associé.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 8cb1d06872f7eae04bac934220da9d58982d0f4b
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233734"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Didacticiel : Explorer la bibliothèque cliente JavaScript de Azure Time Series Insights

Pour aider les développeurs web à exécuter des requêtes afin de visualiser les données stockées dans Time Series Insights (TSI), une bibliothèque cliente TSI JavaScript basée sur D3 a été développée. Ce tutoriel explore le modèle de programmation et la bibliothèque de client TSI avec un exemple d’application hébergée.

Ce tutoriel explique comment utiliser la bibliothèque, accéder aux données TSI et utiliser les contrôles de graphe pour présenter les données. Vous apprendrez également à essayer différents types de graphes pour visualiser les données. À la fin du tutoriel, vous saurez intégrer des fonctionnalités TSI à votre application web à partir de la bibliothèque de client.

Plus précisément, voici les points abordés :

> [!div class="checklist"]
> * L’exemple d’application TSI.
> * La bibliothèque cliente JavaScript de TSI.
> * La façon dont l’exemple d’application utilise la bibliothèque pour visualiser les données TSI.

> [!NOTE]
> * Ce tutoriel utilise une [démonstration web Time Series Insights](https://insights.timeseries.azure.com/clientsample) gratuite et hébergée.
> * Les fichiers sources de l’exemple d'application Time Series Insights sont fournis dans le [référentiel d’exemples GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Lisez la [documentation de référence du client TSI](https://github.com/microsoft/tsiclient/blob/master/docs/API.md).

## <a name="video"></a>Vidéo

### <a name="in-this-video-we-introduce-the-open-source-time-series-insights-javascript-sdkbr"></a>Dans cette vidéo, nous présentons le SDK JavaScript open source de Time Series Insights.</br>

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>Prérequis

Ce tutoriel utilise la fonctionnalité **Outils de développement** de votre navigateur. Les navigateurs web modernes ([Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [Firefox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/), etc.) donnent généralement accès à la **Vue Inspecteur web** par la touche d’accès rapide `F12`. Elle est sinon accessible en cliquant avec le bouton droit sur une page web et en sélectionnant **Inspecter l’élément**.

## <a name="time-series-insights-sample-application"></a>Exemple d’application Time Series Insights

Ce tutoriel s’appuie sur un exemple d’application Azure Time Series Insight gratuit et hébergé pour explorer le code source qui tourne derrière l’application et la bibliothèque de client JavaScript TSI. Vous allez y apprendre à interagir avec TSI en JavaScript et à visualiser les données sous forme de graphes.

1. Accédez à l’[exemple d’application Time Series Insights](https://insights.timeseries.azure.com/clientsample). L’invite de connexion suivante s’affiche :

   [![Exemple d’invite de connexion du client TSI](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Sélectionnez **Se connecter** et entrez ou sélectionnez vos informations d’identification. Utilisez un compte d’entreprise (Azure Active Directory) ou un compte personnel (compte Microsoft ou compte de service administré).

   [![Exemple d’invite de demande d'informations d’identification du client TSI](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. Après la connexion, une page comportant plusieurs types de graphes de données TSI apparaît. Votre compte d’utilisateur et l’option **Se déconnecter** sont visibles dans le coin supérieur droit :

   [![Exemple de page principale du client TSI après la connexion](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Structure et source de la page

Examinons tout d’abord le [code source HTML et JavaScript](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) de la page web :

1. Ouvrez **Outils de développement** dans votre navigateur. Inspectez les éléments HTML qui composent la page active, également connue sous le nom d’arborescence HTML ou DOM.

1. Développez les éléments `<head>` et `<body>`, et observez les sections suivantes :

   * Sous l’élément `<head>` se trouvent les métadonnées de la page et les dépendances qui permettent à l’application de s’exécuter :
     * Un élément `<script>` qui est utilisé pour faire référence à la bibliothèque d’authentification Azure Active Directory **adal.min.js** (également appelée ADAL). La bibliothèque ADAL est une bibliothèque JavaScript qui fournit l’authentification OAuth 2.0 (connexion) et l’acquisition de jeton pour accéder aux API.
     * Plusieurs éléments `<link>` des feuilles de style (également appelés CSS) comme **sampleStyles.css** et **tsiclient.css**. Les feuilles de style permettent de contrôler les détails du style visuel de la page, comme les couleurs, les polices, l’espacement et ainsi de suite.
     * Un élément `<script>` utilisé pour faire référence à la bibliothèque JavaScript de client TSI **tsiclient.js**. La bibliothèque est utilisée par la page pour appeler des API de service STI et restituer les contrôles de graphique dans la page.

     >[!NOTE]
     > * Le code source pour la bibliothèque JavaScript ADAL est disponible à partir du [référentiel azure-activedirectory-library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > * Le code source pour la bibliothèque JavaScript du client TSI est disponible à partir du [référentiel tsiclient](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

   * Sous l’élément `<body>` se trouvent les éléments `<div>`, qui permettent de définir la disposition des éléments sur la page, et un autre élément `<script>` :
     * Le premier élément `<div>` spécifie la boîte de dialogue de **connexion** (`id="loginModal"`).
     * Le second élément `<div>` agit comme parent pour :
       * Un élément `<div>` d’en-tête, utilisé pour les messages d’état et les informations de connexion en haut de la page (`class="header"`).
       * Un élément `<div>` pour le reste des éléments du corps de la page, y compris tous les graphiques (`class="chartsWrapper"`).
       * Une section `<script>`, qui contient tout le code JavaScript utilisé pour contrôler la page.

   [![Exemple de client TSI avec des outils de développement](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Développez l’élément `<div class="chartsWrapper">` pour trouver d’autres éléments `<div>` enfants. Ces éléments sont utilisés pour placer chaque exemple de contrôle de graphique. Notez qu’il existe plusieurs paires d’éléments `<div>`, une pour chaque exemple de graphique :

   * Le premier élément (`class="rowOfCardsTitle"`) contient un titre descriptif qui résume ce que le ou les graphiques illustrent. Par exemple : `Static Line Charts With Full-Size Legends.`
   * Le second élément (`class="rowOfCards"`) est un parent contenant des éléments `<div>` enfants supplémentaires qui positionnent le ou les contrôles de graphique réels dans une ligne.

   [![Éléments div du corps](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. À présent, développez l’élément `<script type="text/javascript">` qui est directement sous l’élément `<div class="chartsWrapper">`. Remarquez le début de la section JavaScript de niveau page, utilisée pour gérer toute la logique de la page (authentification, appel des API du service TSI, rendu des contrôles de graphes, etc.) :

   [![Script du corps](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="tsi-javascript-client-library-concepts"></a>Concepts de la bibliothèque cliente JavaScript de TSI

La bibliothèque de client TSI (**tsclient.js**) fournit les abstractions de deux fonctionnalités importantes de JavaScript :

* **Méthodes de wrapper pour appeler les API de requête TSI** : API REST qui vous permettent d’exécuter des requêtes pour obtenir des données TSI à l’aide d’expressions d’agrégation. Les méthodes sont organisées sous l’espace de noms `TsiClient.Server` de la bibliothèque.

* **Méthodes pour créer et remplir plusieurs types de contrôles graphiques** : Méthodes utilisées pour rendre les données d’agrégation TSI dans une page web. Les méthodes sont organisées sous l’espace de noms `TsiClient.UX` de la bibliothèque.

Grâce à ces simplifications, les développeurs peuvent plus facilement créer des composants de graphes d’interface utilisateur alimentés par des données TSI.

### <a name="authentication"></a>Authentication

[L’exemple d’application Time Series Insights](https://insights.timeseries.azure.com/clientsample) est une application monopage avec prise en charge de l’authentification utilisateur ADAL OAuth 2.0 :

1. Avec l’authentification ADAL, l’application cliente doit être inscrite dans Azure Active Directory. De fait, l’application monopage est enregistrée de façon à utiliser le [flux d’octroi implicite OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow).
1. Par conséquent, l’application doit spécifier certaines des propriétés d’inscription à l’exécution, notamment le GUID du client (`clientId`) et l’URI de redirection (`postLogoutRedirectUri`).
1. Ensuite, l’application demande un **jeton d’accès** à Azure Active Directory. Ce jeton est émis pour un ensemble fini d’autorisations et un identificateur de service/d’API spécifique (`https://api.timeseries.azure.com`). Les autorisations de jeton sont émises pour le compte de l’utilisateur connecté. L’identificateur du service/de l’API est une autre propriété contenue dans l’inscription Azure Active Directory de l’application.
1. Le jeton d’accès à l’application retourné par la bibliothèque ADAL est transmis sous forme de **jeton du porteur** lors de l’accès aux API du service TSI.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> Pour plus d’informations sur les bibliothèques ADAL prises en charge par Microsoft, voir la [documentation de référence ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries).

### <a name="control-identification"></a>Identification du contrôle

Dans l’exemple fourni, les éléments `<div>` sont organisés dans l’élément `<body>` parent pour offrir une mise en page judicieuse de tous les contrôles de graphes qui s’affichent sur la page.

Chaque élément `<div>` spécifie des propriétés pour les attributs visuels et les attributs de placement des contrôles de graphes. Les propriétés `id` des éléments HTML servent d’identificateurs uniques liés à des contrôles spécifiques pour le rendu et la mise à jour des données visualisées.

### <a name="aggregate-expressions"></a>Expressions d’agrégation

Les API de la bibliothèque de client TSI utilisent des expressions d’agrégation :

* Une expression d’agrégation offre la possibilité de construire un ou plusieurs **critères de recherche**.

* Les API client sont conçues pour fournir des fonctionnalités similaires à celles d’une autre application de démonstration fournie ([l’Explorateur Time Series Insights](https://insights.timeseries.azure.com/demo)) qui utilise la période de recherche, le prédicat where, les mesures et le fractionnement par valeur.

* La plupart des API de la bibliothèque de client prennent un tableau d’expressions d’agrégation, qui sont utilisées par le service pour générer une requête de données TSI.

### <a name="call-pattern"></a>Modèle d’appel

Le remplissage et le rendu des contrôles de graphes suivent un modèle général, visible dans la totalité de l’exemple d’application, qui facilite le recours à la bibliothèque de client :

1. Déclarez un `array` contenant une ou plusieurs expressions d’agrégation TSI :

   ```javascript
   var aes =  [];
   ```

1. Créez *1* à *n* objets d’expression d’agrégation. Ensuite, ajoutez-les au tableau des expressions d’agrégation :

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **Paramètres d’aggregateExpression**

   | Paramètre | Description | Exemples |
   | --------- | ----------- | ------- |
   | `predicateObject` | Expression de filtrage de données. |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | Nom de propriété de la mesure utilisée. | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | Agrégations souhaitées de la propriété de mesure. | `['avg', 'min']` |
   | `searchSpan`      | Durée et taille d’intervalle de l’expression d’agrégation. | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | Propriété de chaîne que vous souhaitez utiliser pour le fractionnement (facultative, peut être null). | `{property: 'Station', type: 'String'}` |
   | `color`         | Couleur des objets que vous souhaitez afficher. | `'pink'` |
   | `alias`           | Nom convivial de l’expression d’agrégation. | `'Factory3Temperature'` |
   | `contextMenuActions` | Tableau d’actions à lier aux objets de série chronologique dans une visualisation (facultatif). | Pour plus d’informations, voir la section [Menus contextuels](#pop-up-context-menus) |

1. Appelez une requête TSI à l’aide d’API `TsiClient.Server` pour demander les données d’agrégation :

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **Paramètres de getAggregates**

   | Paramètre | Description | Exemples |
   | --------- | ----------- | ------- |
   | `token`     | Le jeton d’accès pour l’API TSI. |  `authContext.getTsiToken()` Pour en savoir plus, voir la section [Authentification.](#authentication) |
   | `envFQDN`   | Le nom de domaine complet (FQDN) pour l’environnement TSI. | À partir du portail Azure, par exemple : `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | Tableau d’expressions de requête TSI. | Utilisez la variable `aes` comme décrit précédemment : `aes.map(function(ae){return ae.toTsx()}`. |

1. Transformez le résultat compressé retourné de la requête TSI, en langage JSON pour la visualisation :

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Créez un contrôle de graphique à l’aide des API `TsiClient.UX` et liez-le à l’un des éléments `<div>` dans la page :

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. Remplissez le contrôle de graphique avec le ou les objets de données JSON transformés et affichez-le dans la page :

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Contrôles de rendu

La bibliothèque de client TSI fournit huit contrôles d’analytics uniques et prêts à l’emploi :

* **Graphique en courbes**
* **Graphique à secteurs**
* **Graphique à barres**
* **heatmap**
* **Contrôles hiérarchiques**
* **Grille accessible**
* **Chronologies d’événements discrets**
* **Chronologies de transitions d'état**

### <a name="line-bar-pie-chart-examples"></a>Exemples de graphiques en courbes, à barres et à secteurs

Examinez le code de démonstration permettant de restituer certaines contrôles de graphes standard. Remarquez le modèle de programmation et les modèles de création de ces contrôles. Plus précisément, examinez la section de code HTML sous le commentaire `// Example 3/4/5`, qui affiche les contrôles dont la valeur `id` HTML est `chart3`, `chart4` ou `chart5`.

Comme nous l’avons vu à **l’étape 3** de la section [Structure et source de la page](#page-source-and-structure), les contrôles de graphes sont disposés en lignes sur la page, chacun avec une ligne de titre descriptive. Dans cet exemple, les trois graphes sont remplis sous l’élément `<div>` du titre `"Multiple Chart Types From the Same Data"`, et liés aux trois éléments `<div>` situés au-dessous du titre :

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

La section suivante du code JavaScript utilise les modèles décrits précédemment : générer des expressions d’agrégation TSI, les utiliser pour exécuter des requêtes afin d’obtenir des données TSI, et afficher les trois graphiques. Notez les trois types utilisés à partir de l’espace de noms `tsiClient.ux`, `LineChart`, `BarChart` et `PieChart`, pour créer et afficher les graphiques respectifs. Notez également que les trois graphiques sont en mesure d’utiliser les mêmes données d’expression d’agrégation, `transformedResult` :

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

Ces trois graphiques apparaissent comme suit lors de leur affichage :

[![Plusieurs types de graphiques à partir des mêmes données](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Fonctionnalités avancées

La bibliothèque de client TSI comporte d’autres fonctionnalités permettant d’implémenter des visualisations de données créatives.

### <a name="states-and-events"></a>États et événements

Parmi les fonctionnalités avancées figure la possibilité d’ajouter des transitions d’état et des événements discrets aux graphes, utile pour mettre en évidence les incidents, émettre des alertes et créer des changements d’état (activé/désactivé par exemple).

Examinez le code qui se trouve autour du commentaire `// Example 10`. Il affiche un contrôle de ligne sous le titre `"Line Charts with Multiple Series Types"` et le lie à l’élément `<div>` dont la valeur `id` HTML est `chart10`.

1. Tout d’abord, une structure nommée `events4` est définie ; elle contiendra les éléments de changement d’état à suivre. La structure contient :

   * Une clé de chaîne nommée `Component States`.
   * Un tableau d’objets de valeur qui représentent les états. Chaque objet inclut :
     * Une clé de chaîne contenant un horodateur ISO JavaScript.
     * Un tableau qui contient les caractéristiques de l’état : une couleur et une description.

2. Ensuite, la structure `events5` est définie pour `Incidents` ; elle contient un tableau des éléments d’événements à suivre. La structure de tableau a la forme décrite pour `events4`.

3. Enfin, le graphique en courbes est affiché avec les deux structures et les paramètres des options de graphique : `events:` et `states:`. Notez les autres paramètres d’option pour spécifier un élément `tooltip:`, `theme:` ou `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Visuellement, les fenêtres indépendantes/marqueurs en forme de losange qui sont utilisés pour indiquer les incidents, et les fenêtres indépendantes/barres de couleur le long de l’échelle chronologique indiquent les changements d’état :

[![Graphiques en courbes avec plusieurs types de séries](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Menus contextuels

La possibilité de créer des menus contextuels personnalisés (accessibles par clic droit) constituent une autre fonctionnalité avancée. Les menus contextuels personnalisés sont utiles pour l’activation d’actions et d’étapes logiques dans la portée de votre application.

Examinez le code qui se trouve autour du commentaire `// Example 13/14/15`. Il affiche initialement un graphique en courbes sous le titre `"Line Chart with Context Menu to Create Pie/Bar Chart"` ; le graphique est lié à l’élément `<div>` dont la valeur `id` HTML est `chart13`.

À l’aide de menus contextuels, le graphique en courbes permet de créer dynamiquement un graphique à secteurs et un graphique à barres, liés aux éléments `<div>` avec les ID `chart14` et `chart15`. En outre, les graphiques à secteurs et à barres utilisent également des menus contextuels pour proposer leurs propres fonctionnalités : la capacité de copier des données du graphique à secteurs au graphique à barres, et d’imprimer les données du graphique à barres dans la fenêtre de console du navigateur, respectivement.

1. Tout d’abord, une série d’actions personnalisées sont définies. Chaque action contient un tableau avec un ou plusieurs éléments. Chaque élément définit un élément de menu contextuel :

   * `barChartActions`: cette action définit le menu contextuel pour le graphique à secteurs, qui contient un seul élément pour définir un élément individuel :
     * `name`: texte utilisé pour l’élément de menu : « Paramètres d’impression dans la console. »
     * `action`: action associée à l’élément de menu. L’action associée est toujours une fonction anonyme acceptant trois arguments en fonction de l’expression d’agrégation utilisée pour créer le graphique. Dans ce cas, les arguments sont écrits dans la fenêtre de console du navigateur :
       * `ae`: tableau des expressions d’agrégation.
       * `splitBy`: valeur `splitBy`.
       * `timestamp`: horodateur.

   * `pieChartActions`: cette action définit le menu contextuel pour le graphique à barres, qui contient un seul élément pour définir un élément individuel. La forme et le schéma sont identiques à l’élément `barChartActions` précédent, mais notez que la fonction `action` est radicalement différente, elle instancie et affiche le graphique à barres. Notez également que ’argument `ae` est utilisé pour spécifier le tableau des expressions d’agrégation transmis lors de l’exécution, pendant l’ouverture de l’élément de menu. La fonction définit également la propriété `ae.contextMenu` avec le menu contextuel `barChartActions`.
   * `contextMenuActions`: cette action définit le menu contextuel pour le graphique en courbes, qui contient trois éléments pour définir trois éléments de menu. La forme et le schéma de chaque élément sont identiques aux éléments décrits précédemment. Tout comme l’élément `barChartActions`, le premier élément écrit les trois arguments de fonction dans la fenêtre de console du navigateur. De façon similaire à l’élément `pieChartActions`, les deux seconds éléments instancient et affichent les graphiques à secteurs et à barres, respectivement. Les deux seconds éléments définissent également leurs propriétés `ae.contextMenu` avec les menus contextuels `pieChartActions` et `barChartActions`, respectivement.

2. Ensuite, deux expressions d’agrégation sont envoyées sur le `aes` tableau d’expressions d’agrégation et spécifient le tableau `contextMenuActions` pour chaque élément. Ces expressions sont utilisées avec le contrôle de graphique en courbes.

3. Enfin, seul le graphique en courbes est initialement affiché. Les graphiques à secteurs et à barres peuvent être affichés à partir de celui-ci lors de l’exécution.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

La capture d’écran montre les graphiques avec leurs menus contextuels respectifs. Les graphiques à secteurs et à barres ont été créés dynamiquement à l’aide des options du menu contextuel du graphique en courbes.

[![Graphique en courbes avec menu contextuel pour créer le graphique à secteurs/barres](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pinceaux

Les pinceaux sont utilisés pour délimiter une plage de temps afin de définir des actions telles que le zoom et l’exploration.

Le code utilisé pour illustrer les pinceaux apparaît également dans l’exemple précédent « Graphique en courbes avec menu contextuel pour créer le graphique à secteurs/barres », décrivant les menus contextuels.

1. Les actions de pinceau sont semblables à un menu contextuel dans le sens où elles définissent une série d’actions personnalisées pour le pinceau. Chaque action contient un tableau avec un ou plusieurs éléments. Chaque élément définit un élément de menu contextuel :
   * `name`: texte utilisé pour l’élément de menu : « Paramètres d’impression dans la console. »
   * `action`: action associée à l’élément de menu, toujours une fonction anonyme acceptant deux arguments. Dans ce cas, les arguments sont écrits dans la fenêtre de console du navigateur :
      * `fromTime`: timestamp `from` de la sélection de pinceau.
      * `toTime`: timestamp `to` de la sélection de pinceau.

2. Les actions de pinceau sont ajoutées sous la forme d’une autre propriété d’option de graphique. Remarquez la propriété `brushContextMenuActions: brushActions` transmise à l’appel `linechart.Render`.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![Graphique en courbes avec menu contextuel permettant de créer un graphique à secteurs/barres avec des pinceaux](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Vous connecter et explorer l’exemple d’application TSI et sa source.
> * Utiliser les API dans la bibliothèque cliente JavaScript de TSI.
> * Utiliser JavaScript pour créer et remplir des contrôles de graphique avec des données TSI.

Nous voyons que l’exemple d’application TSI utilise un jeu de données de démonstration. Pour savoir comment créer vos propres environnement et jeu de données TSI, consultez l’article suivant :

> [!div class="nextstepaction"]
> [Didacticiel : Créer un environnement Azure Time Series Insights](tutorial-create-populate-tsi-environment.md)

Ou afficher les fichiers sources de l’exemple d'application TSI :

> [!div class="nextstepaction"]
> [Exemple de référentiel d’application TSI](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

Lisez la documentation de référence de l’API de client TSI :

> [!div class="nextstepaction"]
> [Documentation de référence de l’API TSI](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)
