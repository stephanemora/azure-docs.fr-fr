---
title: Surveillance d’un site SharePoint avec Application Insights
description: Démarrage de la surveillance d'une nouvelle application avec une nouvelle clé d'instrumentation
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 2bfe5910-d673-4cf6-a5c1-4c115eae1be0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: mbullwin
ms.openlocfilehash: 027d5485b29deb434953470023d8a290cc0af58a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60784553"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Surveillance d’un site SharePoint avec Application Insights
Azure Application Insights surveille la disponibilité, les performances et l’utilisation de vos applications. Vous allez maintenant apprendre à le configurer pour un site SharePoint.

## <a name="create-an-application-insights-resource"></a>Création d’une ressource Application Insights dans Azure
Dans le [portail Azure](https://portal.azure.com), créez une ressource Application Insights. Choisissez le type d'application ASP.NET.

![Cliquez sur Propriétés, sélectionnez la clé et appuyez sur Ctrl + C](./media/sharepoint/001.png)

Dans la fenêtre qui s’ouvre, vous trouverez des données relatives à l’utilisation et aux performances de votre application. Vous devriez trouver une vignette sur l’écran d’accueil pour accéder à ces informations, la prochaine fois que vous vous connecterez à Azure. Sinon, cliquez sur Parcourir.

## <a name="add-the-script-to-your-web-pages"></a>Ajout du script dans vos pages web

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var appInsights=window.appInsights||function(a){
  function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
  }({
      instrumentationKey:"<your instrumentation key>"
  });
  
window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
</script>
```

Insérez-le juste avant la balise &lt;/head&gt; de chaque page que vous voulez suivre. Si votre site Web possède une page maître, vous pouvez y placer le script. Par exemple, dans un projet ASP.NET MVC, vous placeriez le script dans View\Shared\_Layout.cshtml

Le script contient la clé d'instrumentation qui dirige la télémétrie vers votre ressource Application Insights.

### <a name="add-the-code-to-your-site-pages"></a>Ajouter le code aux pages de votre site
#### <a name="on-the-master-page"></a>Sur la page maître
Si vous pouvez modifier la page maître de votre site, vous pourrez surveiller chaque page du site.

Vérifiez la page maître et modifiez-la à l'aide de SharePoint Designer ou de n’importe quel autre éditeur.

![](./media/sharepoint/03-master.png)

Ajoutez le code juste avant la balise </head> . 

![](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>Ou sur des pages individuelles
Pour surveiller un ensemble limité de pages, ajoutez le script à chaque page, séparément. 

Insérez un composant Web et incorporez l'extrait de code qu'il contient.

![](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Affichage des données relatives à votre application
Redéployez votre application.

Revenez au panneau de votre application dans le [portail Azure](https://portal.azure.com).

Les premiers événements s’affichent dans Search. 

![](./media/sharepoint/09-search.png)

Après quelques secondes, cliquez sur Actualiser pour obtenir des données supplémentaires.

## <a name="capturing-user-id"></a>Capture des ID d’utilisateur
L’extrait de code d’une page Web standard ne capture pas l’ID d’utilisateur à partir de SharePoint, mais vous pouvez y parvenir en effectuant une simple modification.

1. Copiez la clé d’instrumentation de votre application de la liste déroulante Bases dans Application Insights. 

    ![](./media/sharepoint/02-props.png)

1. Insérez la clé d’instrumentation à l’emplacement « XXXX » dans l’extrait de code ci-dessous. 
2. Incorporez dans votre application SharePoint au lieu de l’extrait de code que vous obtenez à partir du portail.

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>Étapes suivantes
* [Tests Web](../../azure-monitor/app/monitor-web-app-availability.md) pour surveiller la disponibilité de votre site.
* [Application Insights](../../azure-monitor/app/app-insights-overview.md) pour les autres types d'applications.

<!--Link references-->


