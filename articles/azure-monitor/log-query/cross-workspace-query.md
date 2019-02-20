---
title: Interroger des ressources avec Azure Monitor | Microsoft Docs
description: Cet article explique comment interroger les ressources à partir de plusieurs espaces de travail et d’une application App Insights incluse dans votre abonnement.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: magoedte
ms.openlocfilehash: ccc9a74c4e238ebfcab0fc05a3bf825000917843
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998936"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Effectuer des requêtes de journal inter-ressources dans Azure Monitor  

Auparavant, Azure Monitor vous permettait d’analyser les données uniquement dans l’espace de travail actif, ce qui limitait votre capacité à interroger plusieurs espaces de travail définis dans votre abonnement.  De plus, vos recherches ne pouvaient porter que sur les éléments de télémétrie recueillis par votre application web avec Application Insights directement dans Application Insights ou à partir de Visual Studio.  Cela compliquait aussi l’analyse simultanée des données opérationnelles et d’application en mode natif.   

Maintenant, vous pouvez interroger non seulement plusieurs espaces de travail Log Analytics, mais également des données d’une application Application Insights spécifique dans le même groupe de ressources, un autre groupe de ressources ou un autre abonnement. Cela vous donne une vue de vos données à l’échelle du système.  Vous ne pouvez effectuer ces types de requêtes que dans [Log Analytics](portals.md). Le nombre de ressources (espaces de travail Log Analytics et applications Application Insights) que vous pouvez inclure dans une seule requête est limité à 100. 

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Interrogation de plusieurs espaces de travail Log Analytics à partir d’Application Insights
Pour référencer un autre espace de travail dans votre requête, utilisez l’identificateur [*workspace*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression). Pour une application Application Insights, utilisez l’identificateur [*app*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression).  

### <a name="identifying-workspace-resources"></a>Identification des ressources d’espace de travail
Les exemples suivants illustrent des requêtes exécutées sur des espaces de travail Log Analytics dans le but de retourner les décomptes synthétiques des journaux à partir de la table Update d’un espace de travail nommé *contosoretail-it*. 

Il est possible d’identifier un espace de travail de plusieurs manières :

* Nom de la ressource : il s’agit d’un nom convivial de l’espace de travail, parfois appelé *nom du composant*. 

    `workspace("contosoretail-it").Update | count`
 
    >[!NOTE]
    >L’identification d’un espace de travail par nom suppose une unicité entre tous les abonnements accessibles. Si plusieurs applications portent le nom spécifié, la requête échoue en raison de l’ambiguïté. Dans ce cas, vous devez utiliser l’un des autres identificateurs.

* Nom qualifié : il s’agit du « nom complet » de l’espace de travail, composé du nom de l’abonnement, du groupe de ressources et du nom du composant au format suivant : *nomAbonnement/groupeRessources/nomComposant*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count `

    >[!NOTE]
    >Étant donné que les noms des abonnements Azure ne sont pas uniques, cet identificateur peut être ambigu. 
    >

* ID de l’espace de travail : il s’agit de l’identificateur unique et immuable affecté à chaque espace de travail, représenté sous la forme d’un identificateur global unique (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* ID de ressource Azure : c’est l’identité unique de l’espace de travail, définie par Azure. Vous utilisez l’ID de ressource quand le nom de la ressource est ambigu.  Pour les espaces de travail, le format est : */subscriptions/idAbonnement/resourcegroups/groupeRessources/providers/microsoft.OperationalInsights/workspaces/nomComposant*.  

    Par exemple : 
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Identification d’une application
L’exemple suivant retourne un décompte synthétique des demandes adressées à une application nommée *fabrikamapp* dans Application Insights. 

Vous pouvez identifier une application dans Application Insights avec l’expression *app(Identifier)*.  L’argument *Identifier* spécifie l’application à l’aide de l’un éléments suivants :

* Nom de la ressource : il s’agit d’un nom convivial de l’application, parfois appelé *nom du composant*.  

    `app("fabrikamapp")`

* Nom qualifié : il s’agit du « nom complet » de l’application, composé du nom de l’abonnement, du groupe de ressources et du nom du composant au format suivant : *nomAbonnement/groupeRessources/nomComposant*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Étant donné que les noms des abonnements Azure ne sont pas uniques, cet identificateur peut être ambigu. 
    >

* ID : GUID de l’application.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* ID de ressource Azure : identité unique de l’application, définie par Azure. Vous utilisez l’ID de ressource quand le nom de la ressource est ambigu. Le format est le suivant : */subscriptions/ID_abonnement/resourcegroups/Groupe_Ressources/providers/microsoft. OperationalInsights/components/Nom_Composant*.  

    Par exemple : 
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Exécution d’une requête sur plusieurs ressources
Vous pouvez interroger plusieurs ressources à partir d’une de vos instances de ressource. Il peut s’agir d’un mélange d’espaces de travail et d’applications.
    
Exemple de requête sur deux espaces de travail :    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Utilisation d’une requête multiressource sur plusieurs ressources
Quand vous utilisez une requête inter-ressources pour mettre en corrélation des données provenant de plusieurs espaces de travail Log Analytics et ressources Application Insights, la requête peut devenir complexe et difficile à gérer. Vous devez tirer parti des [fonctions des requêtes de journal d’Azure Monitor](functions.md) pour séparer la logique de la requête de l’étendue des ressources de la requête, simplifiant ainsi la structure de la requête. L’exemple suivant montre comment superviser plusieurs ressources Application Insights et visualiser le nombre de demandes ayant échoué par nom d’application. 

Créez une requête semblable à la suivante qui référence l’étendue des ressources Application Insights. La commande `withsource= SourceApp` ajoute une colonne qui désigne le nom de l’application ayant envoyé le journal. [Enregistrez la requête en tant que fonction](functions.md#create-a-function) avec l’alias _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Vous pouvez à présent [utiliser cette fonction](../../azure-monitor/log-query/functions.md#use-a-function) dans une requête multiressource comme suit. L’alias de fonction _applicationsScoping_ retourne l’union de la table requests à partir de toutes les applications définies. Ensuite, la requête filtre les demandes ayant échoué et permet de visualiser les tendances par application. L’opérateur _parse_ est facultatif dans cet exemple. Il extrait le nom de l’application de la propriété _SourceApp_.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```
![Graphique temporel](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble des requêtes de journal et de la manière dont les données de journal d’Azure Monitor sont structurées, voir [Analyser les données de journal dans Azure Monitor](log-query-overview.md).
- Pour afficher toutes les ressources pour les requêtes de journal Azure Monitor, voir [Requêtes de journal Azure Monitor](query-language.md).
