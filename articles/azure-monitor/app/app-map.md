---
title: Mise en correspondance d’applications dans Azure Application Insights | Microsoft Docs
description: Surveiller des topologies d’applications complexes avec la mise en correspondance d’applications
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 89aa5006882680205816e7e5d1e7e55b9c4b2ab0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60692918"
---
# <a name="application-map-triage-distributed-applications"></a>Cartographie d’application : trier des applications distribuées

La mise en correspondance d’applications vous permet d’identifier les baisses de performances ou les défaillances sur l’ensemble des composants de votre application distribuée. Chaque nœud de cette mise en correspondance représente un composant d’application ou ses dépendances, avec des indicateurs de performance clés d’intégrité et l’état des alertes. Vous pouvez cliquer sur n’importe quel composant pour obtenir des diagnostics plus détaillés, par exemple des événements Application Insights. Si votre application utilise les services Azure, vous pouvez également accéder aux diagnostics Azure, et notamment aux recommandations de SQL Database Advisor.

## <a name="what-is-a-component"></a>Qu’est un composant ?

Les composants sont des parties pouvant être déployées de manière indépendante de votre application distribuée/de microservices. Les développeurs et équipes d’opérations disposent d’une visibilité au niveau du code ou d’un accès à la télémétrie générée par ces composants d’application. 

* Les composants sont différents des dépendances externes « observées » telles que SQL, EventHub, etc., auxquelles votre équipe/organisation peut ne pas avoir accès (code ou télémétrie).
* Les composants s’exécutent sur un nombre quelconque d’instances de serveur/rôle/conteneur.
* Les composants peuvent être des clés d’instrumentation Application Insights distinctes (même si les abonnements sont différents) ou des rôles différents rapportant à une clé d’instrumentation Application Insights unique. L’aperçu de mise en correspondance montre les composants, quelle que soit leur configuration.

## <a name="composite-application-map"></a>Cartographie d’application composite

Vous pouvez afficher la topologie complète des applications sur plusieurs niveaux des composants d’application associés. Les composants peuvent représenter différentes ressources Application Insights, ou différents rôles d’une seule ressource. La mise en correspondance d’applications trouve les composants en suivant les appels de dépendance HTTP effectués entre les serveurs sur lesquels le kit SDK Application Insights est installé. 

Cette expérience démarre la découverte progressive des composants. Lorsque vous chargez tout d’abord le mappage d’application, un ensemble de requêtes est déclenché pour découvrir les composants liés à ce composant. Un bouton dans le coin supérieur gauche permet de mettre à jour le nombre de composants de votre application dès qu’ils sont détectés. 

Lorsque vous cliquez sur « Update map components » (Mettre à jour les composants de cartographie), la carte est actualisée avec tous les composants détectés. Selon la complexité de votre application, le chargement peut prendre une minute.

Si tous les composants sont des rôles au sein d’une seule ressource Application Insights, cette étape de découverte n’est pas requise. La charge initiale pour une telle application aura tous ses composants.

![Capture d’écran de la cartographie d’application](media/app-map/app-map-001.png)

L’un des principaux objectifs de cette expérience est de permettre de visualiser des topologies complexes incluant des centaines de composants.

Cliquez sur n’importe quel composant pour afficher des informations connexes ainsi que les performances et l’expérience de triage de défaillance de ce composant.

![Menu volant](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Examiner les échecs

Pour lancer le panneau Échecs, sélectionnez **Examiner les échecs**.

![Capture d’écran du bouton Examiner les échecs](media/app-map/investigate-failures.png)

![Capture d’écran du panneau Échecs](media/app-map/failures.png)

### <a name="investigate-performance"></a>Examiner les performances

Pour résoudre les problèmes de performances, sélectionnez **Examiner les performances**.

![Capture d’écran du bouton Examiner les performances](media/app-map/investigate-performance.png)

![Capture d’écran du panneau Performances](media/app-map/performance.png)

### <a name="go-to-details"></a>Accéder aux détails

Pour explorer l’expérience de transaction de bout en bout pouvant présenter des vues générées au niveau de la pile des appels, sélectionnez **Accéder aux détails**.

![Capture d’écran du bouton Accéder aux détails](media/app-map/go-to-details.png)

![Capture d’écran des détails de transaction de bout en bout](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Afficher dans Analytics

Pour interroger et analyser vos données d’application de manière plus approfondie, cliquez sur **Afficher dans Analytics**.

![Capture d’écran du bouton Afficher dans Analytics](media/app-map/view-in-analytics.png)

![Capture d’écran de l’expérience d’analyse](media/app-map/analytics.png)

### <a name="alerts"></a>Alertes

Pour visualiser les alertes actives et les règles sous-jacentes qui entraînent le déclenchement d’alertes, sélectionnez **Alertes**.

![Capture d’écran du bouton Alertes](media/app-map/alerts.png)

![Capture d’écran de l’expérience d’analyse](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Nom de rôle de cloud de jeu

Cartographie d’application utilise le **nom_rôle cloud** propriété pour identifier les composants sur la carte. Le SDK Application Insights ajoute automatiquement la propriété de nom de rôle de cloud pour les données de télémétrie émises par les composants. Par exemple, le Kit de développement logiciel ajouterez un nom de site web ou le nom de rôle de service à la propriété de nom de rôle de cloud. Toutefois, vous pouvez être amené à remplacer la valeur par défaut. Pour remplacer le nom de rôle de cloud et modifier ce qui s’affiche sur la carte de l’Application :

### <a name="net"></a>.NET

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance"
            }
        }
    }
}
```

**Charger votre initialiseur**

Dans ApplicationInsights.config :

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Une autre méthode consiste à instancier l’initialiseur dans le code, par exemple dans Global.aspx.cs :

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Autre méthode pour Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

### <a name="java"></a>Java

Si vous utilisez Spring Boot avec le starter SpringBoot Application Insights, le seul changement nécessaire consiste à définir votre nom personnalisé pour l’application dans le fichier application.properties.

`spring.application.name=<name-of-app>`

La Spring Boot starter attribuera automatiquement le nom de rôle de cloud à la valeur d’entrée pour la propriété spring.application.name.

Pour plus d’informations sur Java corrélation et comment configurer le rôle de cloud nom pour la récupération des applications non SpringBoot ce [section](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) sur la corrélation.

### <a name="clientbrowser-side-javascript"></a>JavaScript côté client/navigateur

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Nom de rôle de cloud de compréhension dans le contexte de la cartographie d’Application

En ce qui concerne la façon de réfléchir à **nom_rôle cloud**, il peut être utile d’examiner une cartographie d’Application qui a plusieurs noms de rôle cloud présents :

![Capture d’écran de la cartographie d’application](media/app-map/cloud-rolename.png)

Dans le mappage d’Application ci-dessus chacun de ces noms dans les zones vertes est cloud valeurs de nom de rôle pour différents aspects de cette application distribuée. Donc pour cette application, ses rôles se composent de : `Authentication`, `acmefrontend`, `Inventory Management`, un `Payment Processing Worker Role`. 

Dans le cas de cette application, chacun de ces noms de rôle cloud représente également une autre ressource Application Insights unique avec leurs propres clés d’instrumentation. Étant donné que le propriétaire de cette application a accès à chacune de ces quatre ressources d’Application Insights disparates, cartographie d’Application est en mesure d’assembler une représentation des relations sous-jacent.

Pour le [définitions officielles](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Vous pouvez également **instance de rôle cloud** peut être utile pour les scénarios où **nom_rôle cloud** vous indique le problème est quelque part dans votre serveur web frontal, mais vous pouvez exécuter votre serveur web frontal sur plusieurs serveurs à équilibrage donc être en mesure d’Explorer dans une couche plus approfondie via des requêtes de Kusto et de savoir si le problème affecte toutes les frontaux serveurs/instances web ou un seul peut être extrêmement important.

Un scénario où vous pouvez souhaiter remplacer la valeur de l’instance de rôle de cloud peut être si votre application est en cours d’exécution dans un environnement en conteneur dans lequel seulement à savoir le serveur peut ne pas être suffisamment d’informations pour localiser un problème donné.

Pour plus d’informations sur la substitution de la propriété de nom de rôle de cloud avec des initialiseurs de télémétrie, consultez [ajouter des propriétés : ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Résolution de problèmes

Si la cartographie d’application ne fonctionne pas comme prévu, essayez ces étapes :

### <a name="general"></a>Généralités

1. Veillez à utiliser un SDK officiellement pris en charge. Les SDK non pris en charge ou de la communauté ne prennent pas forcément en charge la corrélation.

    Reportez-vous à cet [article](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) pour obtenir la liste des SDK pris en charge.

2. Mettez à niveau tous les composants avec la dernière version du SDK.

3. Si vous utilisez Azure Functions avec C#, effectuez une mise à niveau vers [Functions V2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Confirmer [nom_rôle cloud](#set-cloud-role-name) est correctement configuré.

5. S’il vous manque une dépendance, vérifiez qu’elle figure dans la liste des [dépendances collectées automatiquement](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Sinon, vous pouvez toujours la suivre manuellement avec un [suivi d’appel de dépendance](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Trop grand nombre de nœuds sur la carte

Cartographie d’application construit un nœud d’application pour chaque nom de rôle de cloud unique présent dans vos données de télémétrie de demande et un nœud de dépendance pour chaque combinaison unique de type et nom de rôle de cloud dans vos données de télémétrie de dépendance cible. S’il existe plus de 10 000 nœuds dans vos données de télémétrie, cartographie d’Application ne sera pas en mesure d’extraire tous les nœuds et liens, ainsi, votre carte sera incomplète. Si cela se produit, un message d’avertissement s’affiche lors de l’affichage de la carte.

En outre, cartographie d’Application prend uniquement en charge des nœuds non groupés distincts jusqu'à 1000 rendus à la fois. Cartographie d’application réduit la complexité visuelle en regroupant les dépendances qui ont le même type et les appelants, mais si vos données de télémétrie a trop de noms de rôle de cloud unique ou trop de types de dépendances, ce regroupement ne suffire pas suffisants et que la carte ne pourront pas être rendu.

Pour résoudre ce problème, vous devrez modifier votre instrumentation pour définir correctement le nom de rôle de cloud, type de dépendance et les champs de dépendance cible.

* Cible de dépendance doit représenter le nom logique d’une dépendance. Dans de nombreux cas, il est équivalent au serveur ou au nom de la ressource de la dépendance. Par exemple, dans le cas des dépendances HTTP, il est défini pour le nom d’hôte. Il ne doit pas contenir des ID uniques ou des paramètres qui changent d’une requête à un autre.

* Type de dépendance doit représenter le type de logique d’une dépendance. Par exemple, HTTP, SQL ou Azure Blob sont des types de dépendances classique. Il ne doit pas contenir un ID unique.

* L’objectif du nom de rôle de cloud est décrit dans la [au-dessus de section](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name).

## <a name="portal-feedback"></a>Commentaires du portail

Pour envoyer des commentaires, utilisez l’option de commentaires.

![Image MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Étapes suivantes

* [Présentation de la corrélation](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)