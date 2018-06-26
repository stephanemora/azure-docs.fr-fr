---
title: Explorer la bibliothèque cliente JavaScript de Azure Time Series Insights
description: Obtenez des informations sur la bibliothèque cliente JavaScript de Azure Time Series Insights et le modèle de programmation associé.
author: ashannon7
manager: timlt
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 06/05/2018
ms.author: bryanla
ms.openlocfilehash: 70e29b1a6b8a4443ae6545ec7960f1d2370218e3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36295387"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Tutoriel : Explorer la bibliothèque cliente JavaScript de Azure Time Series Insights

Pour aider les développeurs web à exécuter des requêtes afin de visualiser les données stockées dans Time Series Insights (TSI), une bibliothèque cliente TSI JavaScript basée sur D3 a été développée.  Ce tutoriel utilise un exemple d’application web pour vous guider dans une exploration de la bibliothèque cliente de TSI et du modèle de programmation associé.

Les rubriques dans ce didacticiel vous donnent l’opportunité de faire des essais avec la bibliothèque, afin de comprendre comment accéder aux données TSI, et d’utiliser des contrôles de graphique pour afficher et visualiser ces données. L’objectif est de vous fournir suffisamment de détails pour vous permettre d’utiliser cette bibliothèque dans votre propre application web.

Ce tutoriel vous fournira des informations sur :

> [!div class="checklist"]
> * L’exemple d’application TSI.
> * La bibliothèque cliente JavaScript de TSI.
> * La façon dont l’exemple d’application utilise la bibliothèque pour visualiser les données TSI.

## <a name="prerequisites"></a>Prérequis

Ce tutoriel utilise la fonctionnalité « Outils de développement » (également appelée DevTools ou F12), présente dans les navigateurs web les plus récents tels que [Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/), etc. Si vous n’êtes pas encore familiarisé avec cette fonctionnalité, vous pouvez l’explorer dans votre navigateur avant de continuer.

## <a name="time-series-insights-sample-application"></a>Exemple d’application Time Series Insights

Dans ce didacticiel, l’exemple d’application Azure Time Series Insight est utilisé pour explorer le code source derrière l’application, y compris l’utilisation de la bibliothèque cliente JavaScript de TSI. L’exemple est une application web de page unique (SPA) qui montre comment utiliser la bibliothèque. L’exemple montre comment interroger et visualiser des données à partir d’un exemple d’environnement STI.

1. Accédez à l’[exemple d’application Time Series Insights](https://insights.timeseries.azure.com/clientsample). Vous consultez une page similaire à l’image suivante avec une invite pour se connecter :

   ![Invite d’authentification de l’exemple de client STI](media/tutorial-explore-js-client-lib/tcs-sign-in.png)

2. Sélectionnez **Se connecter** et entrez ou sélectionnez vos informations d’identification. Utilisez un compte d’entreprise ou d’organisation (Azure Active Directory) ou un compte personnel (compte Microsoft ou compte de service administré).

   ![Exemple d’invite de saisie d’informations d’identification de client TSI](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)

3. Une fois connecté, vous consultez une page similaire à l’image suivante. La page affiche plusieurs styles de graphiques exemples remplis avec des données STI. Votre compte d’utilisateur et l’option **Se déconnecter** sont visibles dans le coin supérieur droit :

   ![Exemple de page principale de client STI après la connexion](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)

### <a name="page-source-and-structure"></a>Structure et source de la page

Commençons par examiner le code source HTML et JavaScript derrière la page affichée dans votre navigateur. Nous ne présentons pas tous les éléments, mais vous découvrez les sections principales et vous obtenez une idée de la façon dont la page fonctionne :

1. Ouvrez **Outils de développement** dans votre navigateur. Inspectez les éléments HTML qui composent la page active, également connue sous le nom d’arborescence HTML ou DOM.

2. Développez les éléments `<head>` et `<body>`, et notez les sections suivantes :

   - Sous l’élément `<head>`, vous trouvez des éléments qui extraient des fichiers supplémentaires pour faciliter le fonctionnement de la page :
     - Un élément `<script>` qui est utilisé pour faire référence à la bibliothèque d’authentification Azure Active Directory **adal.min.js** (également appelée ADAL). La bibliothèque ADAL est une bibliothèque JavaScript qui fournit l’authentification OAuth 2.0 (connexion) et l’acquisition de jeton pour accéder aux API.
     - Plusieurs éléments `<link>` des feuilles de style (également appelés CSS) comme **sampleStyles.css** et **tsiclient.css**. Les feuilles de style permettent de contrôler les détails du style visuel de la page, comme les couleurs, les polices, l’espacement et ainsi de suite.
     - Un élément `<script>` utilisé pour faire référence à la bibliothèque JavaScript de client TSI **tsiclient.js**. La bibliothèque est utilisée par la page pour appeler des API de service STI et restituer les contrôles de graphique dans la page.

     >[!NOTE]
     > Le code source pour la bibliothèque JavaScript ADAL est disponible à partir du [référentiel azure-activedirectory-library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > Le code source pour la bibliothèque JavaScript du client TSI est disponible à partir du [référentiel tsiclient](https://github.com/Microsoft/tsiclient).

   - Sous l’élément `<body>`, vous trouvez les éléments `<div>`, qui agissent comme des conteneurs pour définir la disposition des éléments dans la page, et un autre élément `<script>` :
     - Le premier élément `<div>` spécifie la boîte de dialogue de **connexion** (`id="loginModal"`).
     - Le second élément `<div>` agit comme parent pour :
       - Un élément `<div>` d’en-tête, utilisé pour les messages d’état et les informations de connexion en haut de la page (`class="header"`).
       - Un élément `<div>` pour le reste des éléments du corps de la page, y compris tous les graphiques (`class="chartsWrapper"`).
       - Une section `<script>`, qui contient tout le code JavaScript utilisé pour contrôler la page.

   [![Exemple de client TSI avec des outils de développement](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

3. Développez l’élément `<div class="chartsWrapper">` et vous trouvez plus d’élément `<div>` enfants. Ces éléments sont utilisés pour placer chaque exemple de contrôle de graphique. Notez qu’il existe plusieurs paires d’éléments `<div>`, une pour chaque exemple de graphique :

   - Le premier élément (`class="rowOfCardsTitle"`) contient un titre descriptif qui résume ce que le ou les graphiques illustrent. Par exemple : « Graphiques en courbes statiques avec légendes pleine taille »
   - Le second élément (`class="rowOfCards"`) est un parent contenant des éléments `<div>` enfants supplémentaires qui positionnent le ou les contrôles de graphique réels dans une ligne.

   ![Éléments div du corps](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)

4. À présent, développez l’élément `<script type="text/javascript">` qui est directement sous l’élément `<div class="chartsWrapper">`. Remarquez que le début de la section JavaScript de niveau page, utilisée pour gérer toute la logique de la page : l’authentification, l’appel des API du service TSI, le rendu des contrôles de graphique, etc. :

   ![Script du corps](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)

## <a name="tsi-javascript-client-library-concepts"></a>Concepts de la bibliothèque cliente JavaScript de TSI

Même si nous ne l’examinons pas en détail, fondamentalement, la bibliothèque du client TSI **tsclient.js** fournit une abstraction pour deux catégories importantes :

- **Méthodes de wrapper pour appeler les API de requête TSI** : API REST qui vous permettent d’exécuter des requêtes pour obtenir des données TSI à l’aide d’expressions d’agrégation. Les méthodes sont organisées sous l’espace de noms `TsiClient.Server` de la bibliothèque.
- **Méthodes pour créer et remplir plusieurs types de contrôles de graphique** : Méthodes utilisées pour afficher les données d’agrégation TSI dans une page web. Les méthodes sont organisées sous l’espace de noms `TsiClient.UX` de la bibliothèque.

Les concepts suivants sont universels et applicables aux API de la bibliothèque du client TSI en général.

### <a name="authentication"></a>Authentification

Comme mentionné précédemment, cet exemple est une SPA qui utilise la prise en charge d’OAuth 2.0 dans la bibliothèque ADAL pour l’authentification utilisateur. Voici quelques points d’intérêt propres à cette section du script :

1. En cas d’utilisation de la bibliothèque ADAL pour l’authentification, l’application cliente doit s’inscrire dans le registre d’application Azure Active Directory (Azure AD). En tant qu’application monopage, cette application est inscrite pour utiliser le flux d’octroi d’autorisation OAuth 2.0 « implicite ». En conséquence, l’application spécifie certaines propriétés d’inscription lors de l’exécution, telles que le GUID d’ID client (`clientId`) et l’URI de redirection (`postLogoutRedirectUri`), pour participer au flux.

2. Ultérieurement, l’application demande un « jeton d’accès » auprès d’Azure AD. Ce jeton d’accès est émis pour un ensemble limité d’autorisations, pour un identificateur de service/d’API spécifique https://api.timeseries.azure.com. L’identificateur d’API/de services est également connu comme « l’audience » du jeton. Les autorisations de jeton sont émises pour le compte de l’utilisateur connecté. L’identificateur du service/de l’API est encore une autre propriété contenue dans l’inscription de l’application auprès d’Azure AD. Une fois que la bibliothèque ADAL retourne le jeton d’accès à l’application, il est transmis en tant que « jeton du porteur » lors de l’accès aux API du service TSI.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=145-204&highlight=4-9,36-39)]

### <a name="control-identification"></a>Identification du contrôle

Comme indiqué précédemment, les éléments `<div>` inclus dans l’élément `<body>` fournissent la disposition de tous les contrôles de graphique illustrés dans la page. Chaque élément `<div>` spécifie des propriétés pour les attributs visuels et les attributs de placement du contrôle de graphique, y compris une propriété `id`. Cette propriété `id` fournit un identificateur unique, qui est utilisé dans le code JavaScript pour identifier et lier chaque contrôle pour le rendu et la mise à jour.

### <a name="aggregate-expressions"></a>Expressions d’agrégation

Les API de la bibliothèque du client TSI font une utilisation intensive d’expressions d’agrégation. Une expression d’agrégation confère la capacité de construire un ou plusieurs « termes de recherche ». Les API sont conçues d’une façon similaire à l’[explorateur Time Series Insights](https://insights.timeseries.azure.com/demo) qui utilise la période de recherche, le prédicat where, les mesures et le fractionnement par valeur. La plupart des API de la bibliothèque prennent un tableau d’expressions d’agrégation, qui sont utilisées par le service pour générer une requête de données TSI.

### <a name="call-pattern"></a>Modèle d’appel

Le remplissage et le rendu des contrôles de graphique suivent un modèle général. Ce modèle est utilisé dans l’ensemble du code JavaScript de la page, qui instancie et charge les contrôles de l’exemple d’application TSI :

1. Déclarez un `array` contenant une ou plusieurs expressions d’agrégation TSI :

   ```javascript
   var aes =  [];
   ```

2. Générez les objets d’expression d’agrégation 1 à n, et ajoutez-les dans le tableau des expressions d’agrégation :

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
   | `contextMenuActions` | Tableau d’actions à lier aux objets de série chronologique dans une visualisation (facultatif). | Pour plus d’informations, consultez [Menus contextuels de la section Fonctionnalités avancées.](#popup-context-menus) |

3. Appelez une requête TSI à l’aide d’API `TsiClient.Server` pour demander les données d’agrégation :

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **Paramètres de getAggregates**

   | Paramètre | Description | Exemples |
   | --------- | ----------- | ------- |
   | `token`     | Le jeton d’accès pour l’API TSI. |  `authContext.getTsiToken()` Pour en savoir plus, voir la section [Authentification.](#authentication) |
   | `envFQDN`   | Le nom de domaine complet (FQDN) pour l’environnement TSI. | À partir du portail Azure, par exemple : `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | Tableau d’expressions de requête TSI. | Utilisez la variable `aes` comme décrit précédemment : `aes.map(function(ae){return ae.toTsx()}`. |

4. Transformez le résultat compressé retourné de la requête TSI, en langage JSON pour la visualisation :

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

5. Créez un contrôle de graphique à l’aide des API `TsiClient.UX` et liez-le à l’un des éléments `<div>` dans la page :

   ```javascript
   var lineChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

6. Remplissez le contrôle de graphique avec le ou les objets de données JSON transformés et affichez-le dans la page :

   ```javascript
   lineChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Contrôles de rendu

La bibliothèque de client TSI expose huit contrôles d’analytique uniques : un graphique en courbes, un graphique à secteurs, un graphique à barres, une carte thermique, des contrôles de hiérarchie, une grille accessible, des chronologies d’événement discrètes et des chronologies de transition d’état.

### <a name="line-bar-pie-chart-examples"></a>Exemples de graphiques en courbes, à barres et à secteurs

Examinez le code derrière certains contrôles de graphique standard illustrés dans l’application, et le modèle/les motifs de programmation permettant de créer les contrôles. Plus précisément, examinez la section de code HTML sous le commentaire `// Example 3/4/5`, qui affiche les contrôles avec les valeurs d’ID `chart3`, `chart4` et `chart5`.

Comme nous l’avons vu à l’étape 3 de la [section Structure et source de la page](#page-source-and-structure), les contrôles de graphique sont organisés en lignes dans la page et chacun a une ligne de titre descriptive. Dans cet exemple, les trois graphiques sont remplis sous l’élément `<div>` du titre « Plusieurs types de graphiques à partir des mêmes données », et ils sont liés aux trois éléments `<div>` situés en dessous :

[!code-javascript[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

La section suivante du code JavaScript utilise les modèles décrits précédemment : générer des expressions d’agrégation TSI, les utiliser pour exécuter des requêtes afin d’obtenir des données TSI, et afficher les trois graphiques. Notez les trois types utilisés à partir de l’espace de noms `tsiClient.ux`, `LineChart`, `BarChart` et `PieChart`, pour créer et afficher les graphiques respectifs. Notez également que les trois graphiques sont en mesure d’utiliser les mêmes données d’expression d’agrégation, `transformedResult` :

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

Ces trois graphiques apparaissent comme suit lors de leur affichage :

[![Plusieurs types de graphiques à partir des mêmes données](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Fonctionnalités avancées

La bibliothèque de client TSI expose également certaines fonctionnalités avancées facultatives dont vous pouvez tirer parti.

### <a name="states-and-events"></a>États et événements

Un exemple des fonctionnalités avancées fournies est la possibilité d’ajouter des transitions d’état et des événements discrets aux graphiques. Cette fonctionnalité est utile pour mettre en évidence les incidents, émettre des alertes et indiquer l’état activé/désactivé des commutateurs.

Examinez le code derrière la section de code HTML sous le commentaire `// Example 10`. Ce code affiche un contrôle de ligne sous le titre « Graphiques en courbes avec plusieurs types de séries » et le lie à l’élément `<div>` avec la valeur d’ID `chart10`.

1. Tout d’abord, une structure nommée `events4` est définie qui contiendra les éléments de changement d’état à suivre. La structure contient :

   - Une clé de chaîne nommée `Component States`.
   - Un tableau d’objets de valeur qui représentent les états. Chaque objet inclut :
     - Une clé de chaîne contenant un horodateur ISO JavaScript.
     - Un tableau qui contient les caractéristiques de l’état : une couleur et une description.

2. Ensuite, la structure `events5` est définie pour « Incidents », qui contient un tableau des éléments d’événement à suivre. La structure de tableau a la forme décrite pour `events4`.

3. Enfin, le graphique en courbes est affiché avec les deux structures et les paramètres des options de graphique : `events:` et `states:`. Notez les autres paramètres d’option pour spécifier un élément `tooltip:`, `theme:` ou `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Visuellement, les fenêtres indépendantes/marqueurs en forme de losange qui sont utilisés pour indiquer les incidents, et les fenêtres indépendantes/barres de couleur le long de l’échelle chronologique indiquent les changements d’état :

[![Graphiques en courbes avec plusieurs types de séries](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Menus contextuels

Les menus contextuels personnalisés constituent un autre exemple de fonctionnalité avancée (menus contextuels lors d’un clic droit). Les menus contextuels personnalisés sont utiles pour l’activation d’actions et d’étapes logiques dans la portée de votre application.

Examinez le code derrière la section de code HTML sous le commentaire `// Example 13/14/15`. Ce code affiche initialement un graphique en courbes sous le titre « Graphique en courbes avec menu contextuel pour créer le graphique à secteurs/barres », et le graphique est lié à l’élément `<div>` avec la valeur d’ID `chart13`. À l’aide de menus contextuels, le graphique en courbes permet de créer dynamiquement un graphique à secteurs et un graphique à barres, liés aux éléments `<div>` avec les ID `chart14` et `chart15`. En outre, les graphiques à secteurs et à barres utilisent également des menus contextuels pour proposer leurs propres fonctionnalités : la capacité de copier des données du graphique à secteurs au graphique à barres, et d’imprimer les données du graphique à barres dans la fenêtre de console du navigateur, respectivement.

1. Tout d’abord, une série d’actions personnalisées sont définies. Chaque action contient un tableau avec un ou plusieurs éléments. Chaque élément définit un élément de menu contextuel :

   - `barChartActions` : cette action définit le menu contextuel pour le graphique à secteurs, qui contient un seul élément pour définir un élément individuel :
     - `name` : le texte qui est utilisé pour l’élément de menu : « Imprimer les paramètres dans la console »
     - `action` : l’action qui est associée à l’élément de menu. L’action associée est toujours une fonction anonyme acceptant trois arguments en fonction de l’expression d’agrégation utilisée pour créer le graphique. Dans ce cas, les arguments sont écrits dans la fenêtre de console du navigateur :
       - `ae` : le tableau des expressions d’agrégation.
       - `splitBy` : la valeur splitBy.
       - `timestamp` : l’horodateur.

   - `pieChartActions` : cette action définit le menu contextuel pour le graphique à barres, qui contient un seul élément pour définir un élément individuel. La forme et le schéma sont identiques à l’élément `barChartActions` précédent, mais notez que la fonction `action` est radicalement différente, elle instancie et affiche le graphique à barres. Notez également que ’argument `ae` est utilisé pour spécifier le tableau des expressions d’agrégation transmis lors de l’exécution, pendant l’ouverture de l’élément de menu. La fonction définit également la propriété `ae.contextMenu` avec le menu contextuel `barChartActions`.
   - `contextMenuActions` : cette action définit le menu contextuel pour le graphique en courbes, qui contient trois éléments pour définir trois éléments de menu. La forme et le schéma de chaque élément sont identiques aux éléments décrits précédemment. Tout comme l’élément `barChartActions`, le premier élément écrit les trois arguments de fonction dans la fenêtre de console du navigateur. De façon similaire à l’élément `pieChartActions`, les deux seconds éléments instancient et affichent les graphiques à secteurs et à barres, respectivement. Les deux seconds éléments définissent également leurs propriétés `ae.contextMenu` avec les menus contextuels `pieChartActions` et `barChartActions`, respectivement.

2. Ensuite, deux expressions d’agrégation sont envoyées sur le `aes` tableau d’expressions d’agrégation et spécifient le tableau `contextMenuActions` pour chaque élément. Ces expressions sont utilisées avec le contrôle de graphique en courbes.

3. Enfin, seul le graphique en courbes est initialement affiché. Les graphiques à secteurs et à barres peuvent être affichés à partir de celui-ci lors de l’exécution.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

La capture d’écran montre les graphiques avec leurs menus contextuels respectifs. Les graphiques à secteurs et à barres ont été créés dynamiquement à l’aide des options du menu contextuel du graphique en courbes.

[![Graphique en courbes avec menu contextuel pour créer le graphique à secteurs/barres](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pinceaux

Les pinceaux sont utilisés pour délimiter une plage de temps afin de définir des actions telles que le zoom et l’exploration.

Le code utilisé pour illustrer les pinceaux apparaît également dans l’exemple précédent « Graphique en courbes avec menu contextuel pour créer le graphique à secteurs/barres », décrivant les [menus contextuels](#popup-context-menus-section).

1. Les actions de pinceau sont semblables à un menu contextuel dans le sens où elles définissent une série d’actions personnalisées pour le pinceau. Chaque action contient un tableau avec un ou plusieurs éléments. Chaque élément définit un élément de menu contextuel :
   - `name` : le texte qui est utilisé pour l’élément de menu : « Imprimer les paramètres dans la console »
   - `action` : l’action associée à l’élément de menu, toujours une fonction anonyme acceptant deux arguments. Dans ce cas, les arguments sont écrits dans la fenêtre de console du navigateur :
      - `fromTime` : l’horodateur « de » de la sélection de pinceaux.
      - `toTime` : l’horodateur « à » de la sélection de pinceaux.

2. Les actions de pinceau sont ajoutées sous la forme d’une autre propriété d’option de graphique. Remarquez la propriété `brushContextMenuActions: brushActions` transmise à l’appel `linechart.Render`.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

![Graphique en courbes avec menu contextuel pour créer le graphique à secteurs/barres avec des pinceaux](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Vous connecter et explorer l’exemple d’application TSI et sa source.
> * Utiliser les API dans la bibliothèque cliente JavaScript de TSI.
> * Utiliser JavaScript pour créer et remplir des contrôles de graphique avec des données TSI.

Comme indiqué, l’exemple d’application TSI utilise un jeu de données de démonstration. Pour savoir comment créer vos propres environnement et jeu de données TSI, consultez l’article suivant :

> [!div class="nextstepaction"]
> [Didacticiel : Créer un environnement Azure Time Series Insights](tutorial-create-populate-tsi-environment.md)


