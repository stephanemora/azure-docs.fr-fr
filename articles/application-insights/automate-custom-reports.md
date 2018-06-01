---
title: Automatiser des rapports personnalisés avec des données Azure Application Insights
description: Automatiser des rapports quotidients/hebdomadaires/mensuels personnalisés avec des données Azure Application Insights
services: application-insights
documentationcenter: ''
author: sdash
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: sdash
ms.openlocfilehash: 804e8c7a43d1ab16d11b6075be44599b33b46a3e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072656"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatiser des rapports personnalisés avec des données Azure Application Insights

Les rapports périodiques permettent de tenir une équipe informée sur le fonctionnement des services critiques pour l’entreprise dont elle s’occupe. Les développeurs, les équipes DevOps/SRE et leurs responsables peuvent produire des rapports automatisés qui fournissent des insights fiables, sans demander à quiconque de se connecter au portail. Ces rapports servent également à mieux identifier les augmentations progressives des latences, des taux de chargement ou d’échec susceptibles de ne pas déclencher de règles d’alerte.

Chaque entreprise a des besoins uniques en termes de rapports, notamment : 

* Agrégations en pourcentage spécifiques des métriques ou métriques personnalisées dans un rapport.
* Disponibilité de rapports différents pour les cumuls de données quotidiens, hebdomadaires et mensuels destinés à différents publics.
* Segmentation selon des attributs personnalisés comme la région ou l’environnement. 
* Regroupement de ressources IA dans un seul rapport, même si elles appartiennent à des abonnements ou groupes de ressources différents, etc.
* Rapports distincts contenant des métriques sensibles envoyés à un public sélectionné.
* Rapports destinés à des parties prenantes qui n’ont pas accès aux ressources du portail.

> [!NOTE] 
> L’e-mail de synthèse Application Insights hebdomadaire ne permettait aucune personnalisation et sera supprimé en faveur des options personnalisées répertoriées ci-dessous. Vous recevrez le dernier e-mail de synthèse hebdomadaire le 11 juin 2018. Configurez l’une des options suivantes pour obtenir des rapports personnalisés similaires (utilisez la requête suggérée ci-dessous).

## <a name="to-automate-custom-report-emails"></a>Pour automatiser les e-mails de rapports personnalisés

Vous pouvez [interroger par programmation des données Application Insights](https://dev.applicationinsights.io/) pour générer des rapports personnalisés selon une planification. Les options suivantes peuvent vous aider à démarrer rapidement :

* [Automatiser des rapports avec Microsoft Flow](app-insights-automate-with-flow.md)
* [Automatiser des rapports avec Logic Apps](automate-with-logic-apps.md)
* Utilisez le modèle de [fonction Azure](https://azure.microsoft.com/services/functions/) « synthèse planifiée Application Insights » dans le scénario de surveillance. Cette fonction utilise SendGrid pour remettre l’e-mail. 

    ![Modèle de fonction Azure](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Exemple de requête pour un e-mail de synthèse hebdomadaire
La requête suivante montre la jointure entre plusieurs jeux de données utilisée pour un e-mail de synthèse hebdomadaire comme un rapport. Personnalisez-la selon les besoins et utilisez-la avec les options répertoriées ci-dessus pour automatiser un rapport hebdomadaire.   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

  
## <a name="next-steps"></a>Étapes suivantes

- Découvrez la création de [requêtes Analytics](app-insights-analytics-using.md).
- En savoir plus sur l’[interrogation par programmation de données Application Insights](https://dev.applicationinsights.io/)
- Découvrez plus en détail les [applications logiques](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
- Découvrez [Microsoft Flow](https://ms.flow.microsoft.com).


