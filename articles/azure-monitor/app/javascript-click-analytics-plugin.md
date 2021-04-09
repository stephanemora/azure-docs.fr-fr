---
title: Plug-in Click Analytics Auto-collection pour kit SDK JavaScript Application Insights
description: Comment installer et utiliser le plug-in Click Analytics Auto-collection pour kit SDK JavaScript Application Insights.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: lagayhar
ms.openlocfilehash: e48d669321ad8c58681e8a92e68f2089962bdc17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102429848"
---
# <a name="click-analytics-auto-collection-plugin-for-application-insights-javascript-sdk"></a>Plug-in Click Analytics Auto-collection pour kit SDK JavaScript Application Insights

Ce plug-in suit automatiquement les événements de clic sur les pages web et utilise les attributs data-* sur les éléments HTML pour remplir la télémétrie des événements.

## <a name="getting-started"></a>Prise en main

Les utilisateurs peuvent configurer le plug-in Click Analytics Auto-collection via npm.

### <a name="npm-setup"></a>Configuration npm

Installez le package npm :

```bash
npm install --save @microsoft/applicationinsights-clickanalytics-js @microsoft/applicationinsights-web
```

```js

import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ClickAnalyticsPlugin } from '@microsoft/applicationinsights-clickanalytics-js';

const clickPluginInstance = new ClickAnalyticsPlugin();
// Click Analytics configuration
const clickPluginConfig = {
  autoCapture: true
};
// Application Insights Configuration
const configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: clickPluginConfig
  },
};

const appInsights = new ApplicationInsights({ config: configObj });
appInsights.loadAppInsights();
```

## <a name="how-to-effectively-use-the-plugin"></a>Comment utiliser efficacement le plug-in

1. Les données de télémétrie générées à partir des événements de clic sont stockées en tant que `customEvents` dans la section Application Insights du portail Azure.
2. Le `name` de customEvent est rempli en fonction des règles suivantes :
    1.  Le `id` fourni dans le `data-*-id` sera utilisé comme nom de customEvent. Par exemple, si l’élément HTML cliqué a l’attribut « data-sample-id »=« button1 », alors « button1 » est le nom de customEvent.
    2. Si aucun attribut de ce type n’existe et que `useDefaultContentNameOrId` est défini sur `true` dans la configuration, l’attribut HTML `id` de l’élément cliqué ou le nom de contenu de l’élément est utilisé comme nom de customEvent.
    3. Si `useDefaultContentNameOrId` a la valeur false, le nom de customEvent est « not_specified ».

    > [!TIP]
    > Nous vous recommandons de définir `useDefaultContentNameOrId` sur true pour générer des données significatives.  
3. `parentDataTag` effectue deux opérations :
    1. Si cette balise est présente, le plug-in récupère les attributs et les valeurs `data-*` à partir de tous les éléments HTML parents de l’élément cliqué.
    2. Pour améliorer l’efficacité, le plug-in utilise cette balise en tant qu’indicateur. Quand il la rencontre, il cesse de traiter le DOM (Document Object Model) vers le haut.
    
    > [!CAUTION]
    > Une fois `parentDataTag` utilisé, le Kit de développement logiciel (SDK) commence à rechercher les balises parentes dans toute l’application, et pas seulement dans l’élément HTML où vous l’avez utilisé.
4. `customDataPrefix` fourni par l’utilisateur doit toujours commencer par `data-`, par exemple `data-sample-`. En HTML, les attributs généraux `data-*` forment une classe d’attributs appelée attributs de données personnalisés, qui autorisent l’échange d’informations propriétaires entre le code HTML et sa représentation DOM par des scripts. Les navigateurs plus anciens (Internet Explorer, Safari) suppriment les attributs qu’ils ne comprennent pas, sauf s’ils commencent par `data-`.

    Le `*` dans `data-*` peut être remplacé par n’importe quel nom qui suit la [règle de production des noms XML](https://www.w3.org/TR/REC-xml/#NT-Name) avec les restrictions suivantes :
    - Le nom ne doit pas commencer par « xml », quelle que soit la casse utilisée pour ces lettres.
    - Le nom ne doit pas contenir de point-virgule (U + 003A).
    - Le nom ne doit pas contenir de lettres majuscules.

## <a name="configuration"></a>Configuration

| Name                  | Type                               | Default | Description                                                                                                                              |
| --------------------- | -----------------------------------| --------| ---------------------------------------------------------------------------------------------------------------------------------------- |
| autoCapture           | boolean                            | true    | Configuration de capture automatique.                                                                                                         |
| rappel              | [IValueCallback](#ivaluecallback)  | null    | Configuration des rappels.                                                                                                                 |
| pageTags              | string                             | null    | Balises de page.                                                                                                                               |
| dataTags              | [ICustomDataTags](#icustomdatatags)| null    | Balises de données personnalisées fournies pour remplacer les balises par défaut utilisées pour capturer les données de clic.                                                           |
| urlCollectHash        | boolean                            | false   | Active la journalisation des données après un caractère « # » de l’URL.                                                                          |
| urlCollectQuery       | boolean                            | false   | Active la journalisation de la chaîne de requête de l’URL.                                                                                      |
| behaviorValidator     | Fonction                           | null  | Fonction de rappel à utiliser pour la validation de la valeur `data-*-bhvr`. Pour plus d’informations, consultez la [section behaviorValidator](#behaviorvalidator).|
| defaultRightClickBhvr | Chaîne (ou) nombre                 | ''      | Valeur de comportement par défaut lorsqu’un événement de clic droit s’est produit. Cette valeur sera remplacée si l’élément a l’attribut `data-*-bhvr`. |
| dropInvalidEvents     | boolean                            | false   | Marquer pour supprimer les événements qui n’ont pas de données de clic utiles.                                                                                   |

### <a name="ivaluecallback"></a>IValueCallback

| Nom               | Type     | Default | Description                                                                             |
| ------------------ | -------- | ------- | --------------------------------------------------------------------------------------- |
| pageName           | Fonction | null    | Fonction permettant de remplacer le comportement de capture pageName par défaut.                           |
| pageActionPageTags | Fonction | null    | Fonction de rappel permettant d’augmenter les pageTags par défaut collectées pendant l’événement pageAction.  |
| contentName        | Fonction | null    | Fonction de rappel permettant de remplir un contentName personnalisé.                                 |

### <a name="icustomdatatags"></a>ICustomDataTags

| Nom                      | Type    | Default   | Balise par défaut à utiliser en HTML |   Description                                                                                |
|---------------------------|---------|-----------|-------------|----------------------------------------------------------------------------------------------|
| useDefaultContentNameOrId | boolean | false     | N/A         |Collecte l’attribut HTML standard de contentName lorsqu’un élément spécifique n’est pas balisé avec la valeur par défaut de customDataPrefix ou que customDataPrefix n’est pas fourni par l’utilisateur. |
| customDataPrefix          | string  | `data-`   | `data-*`| Capture automatique du contenu et de la valeur des éléments balisés avec le préfixe fourni. Par exemple, `data-*-id`, `data-<yourcustomattribute>` peut être utilisé dans les balises HTML.   |
| aiBlobAttributeTag        | string  | `ai-blob` |  `data-ai-blob`| Le plug-in prend en charge un attribut blob JSON plutôt que des attributs `data-*` individuels. |
| metaDataPrefix            | string  | null      | N/A  | Capture automatique du contenu et du nom de l’élément méta de l’en-tête HTML avec le préfixe fourni. Par exemple, `custom-` peut être utilisé dans la balise méta HTML. |
| captureAllMetaDataContent | boolean | false     | N/A   | Capture automatique de tous les noms et de tout le contenu de l’élément méta de l’en-tête HTML. La valeur par défaut est false. Si elle est activée, cette option remplace la valeur metaDataPrefix fournie. |
| parentDataTag             | string  | null      |  N/A  | Cesse de parcourir le DOM pour capturer le nom et la valeur du contenu des éléments quand cette balise est rencontrée. Par exemple, `data-<yourparentDataTag>` peut être utilisé dans les balises HTML.|
| dntDataTag                | string  | `ai-dnt`  |  `data-ai-dnt`| Les éléments HTML avec cet attribut seront ignorés par le plug-in pour la capture des données de télémétrie.|

### <a name="behaviorvalidator"></a>behaviorValidator

Les fonctions behaviorValidator vérifient automatiquement que les comportements balisés dans le code respectent une liste prédéfinie. Cela garantit la cohérence des comportements balisés avec la taxonomie établie par votre entreprise. L’utilisation de cette fonction n’est ni obligatoire ni attendue pour les clients d’Azure Monitor mais elle est disponible pour des scénarios avancés. Trois fonctions de rappel behaviorValidator différentes sont exposées dans le cadre de cette extension. Toutefois, les utilisateurs peuvent utiliser leurs propres fonctions de rappel si les fonctions exposées ne correspondent pas à leurs exigences. L’objectif est d’apporter votre propre structure de données de comportements, le plug-in utilise cette fonction de validateur lors de l’extraction des comportements des balises de données.

| Nom                   | Description                                                                        |
| ---------------------- | -----------------------------------------------------------------------------------|
| BehaviorValueValidator | Utilisez cette fonction de rappel si votre structure de données de comportements est un tableau de chaînes.|
| BehaviorMapValidator   | Utilisez cette fonction de rappel si votre structure de données de comportements est un dictionnaire.       |
| BehaviorEnumValidator  | Utilisez cette fonction de rappel si votre structure de données de comportements est une énumération.            |

#### <a name="sample-usage-with-behaviorvalidator"></a>Exemple d’utilisation avec behaviorValidator

```js
var clickPlugin = Microsoft.ApplicationInsights.ClickAnalyticsPlugin;
var clickPluginInstance = new clickPlugin();

// Behavior enum values
var behaviorMap = {
  UNDEFINED: 0, // default, Undefined

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Page Experience [1-19]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  NAVIGATIONBACK: 1, // Advancing to the previous index position within a webpage
  NAVIGATION: 2, // Advancing to a specific index position within a webpage
  NAVIGATIONFORWARD: 3, // Advancing to the next index position within a webpage
  APPLY: 4, // Applying filter(s) or making selections
  REMOVE: 5, // Applying filter(s) or removing selections
  SORT: 6, // Sorting content
  EXPAND: 7, // Expanding content or content container
  REDUCE: 8, // Sorting content
  CONTEXTMENU: 9, // Context Menu
  TAB: 10, // Tab control
  COPY: 11, // Copy the contents of a page
  EXPERIMENTATION: 12, // Used to identify a third party experimentation event
  PRINT: 13, // User printed page
  SHOW: 14, //  Displaying an overlay
  HIDE: 15, //  Hiding an overlay
  MAXIMIZE: 16, //  Maximizing an overlay
  MINIMIZE: 17, // Minimizing an overlay
  BACKBUTTON: 18, //  Clicking the back button

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Scenario Process [20-39]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  STARTPROCESS: 20, // Initiate a web process unique to adopter
  PROCESSCHECKPOINT: 21, // Represents a checkpoint in a web process unique to adopter
  COMPLETEPROCESS: 22, // Page Actions that complete a web process unique to adopter
  SCENARIOCANCEL: 23, // Actions resulting from cancelling a process/scenario

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Download [40-59]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  DOWNLOADCOMMIT: 40, // Initiating an unmeasurable off-network download
  DOWNLOAD: 41, // Initiating a download

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Search [60-79]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SEARCHAUTOCOMPLETE: 60, // Auto-completing a search query during user input
  SEARCH: 61, // Submitting a search query
  SEARCHINITIATE: 62, // Initiating a search query
  TEXTBOXINPUT: 63, // Typing or entering text in the text box

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Commerce [80-99]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIEWCART: 82, // Viewing the cart
  ADDWISHLIST: 83, // Adding a physical or digital good or services to a wishlist
  FINDSTORE: 84, // Finding a physical store
  CHECKOUT: 85, // Before you fill in credit card info
  REMOVEFROMCART: 86, // Remove an item from the cart
  PURCHASECOMPLETE: 87, // Used to track the pageView event that happens when the CongratsPage or Thank You page loads after a successful purchase
  VIEWCHECKOUTPAGE: 88, // View the checkout page
  VIEWCARTPAGE: 89, // View the cart page
  VIEWPDP: 90, // View a PDP
  UPDATEITEMQUANTITY: 91, // Update an item's quantity
  INTENTTOBUY: 92, // User has the intent to buy an item
  PUSHTOINSTALL: 93, // User has selected the push to install option

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Authentication [100-119]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNIN: 100, // User sign-in
  SIGNOUT: 101, // User sign-out

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Social [120-139]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SOCIALSHARE: 120, // "Sharing" content for a specific social channel
  SOCIALLIKE: 121, // "Liking" content for a specific social channel
  SOCIALREPLY: 122, // "Replying" content for a specific social channel
  CALL: 123, // Click on a "call" link
  EMAIL: 124, // Click on an "email" link
  COMMUNITY: 125, // Click on a "community" link

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Feedback [140-159]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VOTE: 140, // Rating content or voting for content
  SURVEYCHECKPOINT: 145, // reaching the survey page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Registration, Contact [160-179]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  REGISTRATIONINITIATE: 161, // Initiating a registration process
  REGISTRATIONCOMPLETE: 162, // Completing a registration process
  CANCELSUBSCRIPTION: 163, // Canceling a subscription
  RENEWSUBSCRIPTION: 164, // Renewing a subscription
  CHANGESUBSCRIPTION: 165, // Changing a subscription
  REGISTRATIONCHECKPOINT: 166, // Reaching the registration page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Chat [180-199]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  CHATINITIATE: 180, // Initiating a chat experience
  CHATEND: 181, // Ending a chat experience

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Trial [200-209]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  TRIALSIGNUP: 200, // Signing-up for a trial
  TRIALINITIATE: 201, // Initiating a trial

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Signup [210-219]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNUP: 210, // Signing-up for a notification or service
  FREESIGNUP: 211, // Signing-up for a free service

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Referals [220-229]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  PARTNERREFERRAL: 220, // Navigating to a partner's web property

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Intents [230-239]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  LEARNLOWFUNNEL: 230, // Engaging in learning behavior on a commerce page (ex. "Learn more click")
  LEARNHIGHFUNNEL: 231, // Engaging in learning behavior on a non-commerce page (ex. "Learn more click")
  SHOPPINGINTENT: 232, // Shopping behavior prior to landing on a commerce page

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Video [240-259]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIDEOSTART: 240, // Initiating a video
  VIDEOPAUSE: 241, // Pausing a video
  VIDEOCONTINUE: 242, // Pausing or resuming a video.
  VIDEOCHECKPOINT: 243, // Capturing predetermined video percentage complete.
  VIDEOJUMP: 244, // Jumping to a new video location.
  VIDEOCOMPLETE: 245, // Completing a video (or % proxy)
  VIDEOBUFFERING: 246, // Capturing a video buffer event
  VIDEOERROR: 247, // Capturing a video error
  VIDEOMUTE: 248, // Muting a video
  VIDEOUNMUTE: 249, // Unmuting a video
  VIDEOFULLSCREEN: 250, // Making a video full screen
  VIDEOUNFULLSCREEN: 251, // Making a video return from full screen to original size
  VIDEOREPLAY: 252, // Making a video replay
  VIDEOPLAYERLOAD: 253, // Loading the video player
  VIDEOPLAYERCLICK: 254, //  Click on a button within the interactive player
  VIDEOVOLUMECONTROL: 255, //  Click on video volume control
  VIDEOAUDIOTRACKCONTROL: 256, // Click on audio control within a video
  VIDEOCLOSEDCAPTIONCONTROL: 257, //  Click on the closed caption control
  VIDEOCLOSEDCAPTIONSTYLE: 258, //  Click to change closed caption style
  VIDEORESOLUTIONCONTROL: 259, //  Click to change resolution

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  //    Advertisement Engagement [280-299]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  ADBUFFERING: 283, // Ad is buffering
  ADERROR: 284, // Ad error
  ADSTART: 285, // Ad start
  ADCOMPLETE: 286, // Ad complete
  ADSKIP: 287, // Ad skipped
  ADTIMEOUT: 288, // Ad timed-out
  OTHER: 300 // Other
};

// Application Insights Configuration
var configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: {
      behaviorValidator: Microsoft.ApplicationInsights.BehaviorMapValidator(behaviorMap),
      defaultRightClickBhvr: 9
    },
  },
};
var appInsights = new Microsoft.ApplicationInsights.ApplicationInsights({
  config: configObj
});
appInsights.loadAppInsights();
```

## <a name="sample-app"></a>Exemple d'application

[Application web simple avec le plug-in Click Analytics Auto-collection activé](https://go.microsoft.com/fwlink/?linkid=2152871).

## <a name="next-steps"></a>Étapes suivantes

- Consultez les [référentiel GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-clickanalytics-js) et [package NPM](https://www.npmjs.com/package/@microsoft/applicationinsights-clickanalytics-js) pour le plug-in Click Analytics Auto-collection.
- Utilisez [l’analyse des événements dans l’expérience d’utilisation](usage-segmentation.md) pour analyser les clics principaux et les segmenter en utilisant des dimensions disponibles.
- Recherchez les données de clic dans le champ de contenu de l’attribut customDimensions de la table CustomEvents dans [Log Analytics](../logs/log-analytics-tutorial.md#write-a-query). Pour obtenir une aide supplémentaire, consultez [Exemple d’application](https://go.microsoft.com/fwlink/?linkid=2152871).
- Créez un [classeur](../visualize/workbooks-overview.md) ou [exportez un classeur vers Power BI](../logs/log-powerbi.md#integrating-queries) pour créer des visualisations personnalisées concernant les données de clic.
