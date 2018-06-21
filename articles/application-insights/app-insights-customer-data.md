---
title: Guide pour les données personnelles stockées dans Azure Application Insights | Microsoft Docs
description: Cet article explique comment gérer les données personnelles stockées dans Azure Application Insights et décrit les méthodes permettant de les identifier et les supprimer.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.reviewer: Evgeny.Ternovsky
ms.author: mbullwin
ms.openlocfilehash: 95e421278b46015e761764792e11dec0351b9785
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294419"
---
# <a name="guidance-for-personal-data-stored-in-application-insights"></a>Guide pour les données personnelles stockées dans Application Insights

Application Insights est une banque de données pouvant contenir des données personnelles. Cet article explique où ces données se trouvent généralement dans Application Insights, et présente les fonctionnalités à votre disposition pour gérer ces données.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Stratégie de gestion des données personnelles

S’il dépend au final de vous et de votre entreprise de déterminer la stratégie de gestion de vos données privées (le cas échéant), voici quelques approches possibles. Elles sont répertoriées par ordre de préférence décroissant d’un point de vue technique :
* Là où c’est possible, arrêtez la collecte des données, masquez, anonymisez ou réduisez les données collectées pour les exclure de ce qui peut être considéré comme _privé_. Il s’agit de la meilleure approche, qui vous évite de devoir créer une stratégie de gestion de données coûteuse et avec un impact fort.
* Quand ce n’est pas possible, essayez de normaliser les données pour réduire l’impact sur la plateforme de données et les performances. Par exemple, au lieu de journaliser un ID utilisateur explicite, créez des données de recherche qui vont établir une corrélation entre d’une part le nom d’utilisateur et ses informations détaillées et d’autre part un ID interne, qui peuvent ensuite être enregistrés ailleurs. De cette façon, si un de vos utilisateurs vous demande de supprimer ses données personnelles, il est possible que la seule suppression de la ligne dans la table de recherche correspondant à l’utilisateur soit suffisante. 
* Enfin, si des données privées doivent être collectées, créez un processus autour du chemin de l’API de vidage et du chemin de l’API de la requête existante pour répondre à vos obligations liées à l’exportation et à la suppression des données privées associées à un utilisateur.

## <a name="where-to-look-for-private-data-in-application-insights"></a>Où rechercher des données privées dans Application Insights ?

Application Insights est un magasin totalement flexible qui, tout en prescrivant un schéma pour vos données, vous permet de remplacer chaque champ par des valeurs personnalisées. Ainsi, il est impossible de dire exactement où les données privées se trouvent dans votre application spécifique. Cependant, les emplacements suivants constituent de bons points de départ pour votre inventaire :

* *Adresses IP* : par défaut, Application Insights définit tous les champs d’adresse IP sur « 0.0.0.0 », mais il est assez courant de remplacer cette valeur par l’adresse IP réelle de l’utilisateur afin de gérer les informations de la session. La requête Analytics ci-dessous permet de rechercher dans la colonne des adresses IP toutes les tables contenant des valeurs autres que « 0.0.0.0 », au cours des 24 heures :
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *ID d’utilisateur* : par défaut, Application Insights utilise les ID générés de manière aléatoire pour effectuer le suivi des utilisateurs et des sessions. Toutefois, il est courant de remplacer ces champs par un ID plus représentatif de l’application. Par exemple : noms d’utilisateurs, GUID AAD, etc. Ces ID sont souvent considérés comme faisant partie de l’étendue car ce sont des données personnelles et, par conséquent, ils doivent être gérés de façon appropriée. Nous vous recommandons de toujours masquer ou anonymiser ces ID. Ces valeurs sont couramment utilisées dans les champs tels que session_Id, user_Id, user_AuthenticatedId, user_AccountId et customDimensions.
* *Données personnalisées* : Application Insights vous permet d’ajouter un ensemble de dimensions personnalisées à n’importe quel type de données. Ces dimensions peuvent représenter *n’importe quelle* donnée. Utilisez la requête suivante pour identifier toutes les dimensions personnalisées collectées au cours des dernières 24 heures :
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Données en mémoire et en transit* : Application Insights effectuera le suivi des exceptions, requêtes, appels de dépendance et traces. Les données privées peuvent souvent être collectées au niveau du code et des appels HTTP. Examinez les tables contenant des exceptions, requêtes, dépendances et traces pour identifier ces données. Utilisez si possible des [initialiseurs de télémétrie](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) afin de brouiller ces données.
* *Captures du débogueur de capture instantanée* : la fonctionnalité [Débogueur de capture instantanée](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) d’Application Insights vous permet de collecter des instantanés de débogage chaque fois qu’une exception est interceptée sur l’instance de production de votre application. Les instantanés exposeront la trace de pile complète conduisant à des exceptions, ainsi que les valeurs des variables locales à chaque étape de la pile. Malheureusement, cette fonctionnalité ne permet pas la suppression sélective de points d’ancrage, ou l’accès par programme aux données de l’instantané. Par conséquent, si le taux de rétention des instantanés par défaut ne répond pas à vos exigences de conformité, il est recommandé de désactiver cette fonctionnalité.

## <a name="how-to-export-and-delete-private-data"></a>Comment exporter et supprimer des données privées

Il est __fortement__ recommandé (si c’est possible) de restructurer votre stratégie de collecte des données pour éviter la collecte de données privées, de les masquer ou de les anonymiser, ou à défaut d’éviter qu’elles soient considérées comme « privées ». Une gestion des données collectées va générer des coûts pour vous et votre équipe, pour définir et automatiser une stratégie et créer une interface destinée à vos clients qui leur permet d’interagir avec leurs données, ainsi que des coûts liés à la maintenance. De plus, elle est coûteuse en termes de ressources informatiques pour Application Insights, et un grand nombre d’appels d’API de requête ou de vidage simultanés peut avoir un impact potentiellement négatif sur toutes les autres interactions avec les fonctionnalités d’Application Insights. Cela étant dit, il existe en effet des scénarios valides où des données privées doivent être collectées. Dans ces cas, les données doivent être gérées comme décrit dans cette section.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Afficher et exporter

Pour les demandes de visualisation et d’exportation de données, vous devez utiliser [l’API de requête](https://dev.applicationinsights.io/quickstart). L’implémentation de la logique pour convertir la forme des données selon un format approprié pour vos utilisateurs dépend de vous. [Azure Functions](https://azure.microsoft.com/services/functions/) est l’endroit idéal pour héberger cette logique.

### <a name="delete"></a>Supprimer

> [!WARNING]
> Les suppressions dans Application Insights sont destructrices et non réversibles ! Soyez très prudents quand vous les réalisez.

Nous avons rendu disponible un chemin d’API « de vidage » dans le cadre d’une gestion de la confidentialité. Ce chemin doit être utilisé avec prudence en raison du risque associé à cette opération, de l’impact potentiel sur les performances, et de la possibilité d’influer sur les agrégations, les mesures et d’autres aspects de vos données Application Insights. Pour d’autres approches de la gestion des données privées, consultez la section [Stratégie de gestion des données personnelles](#strategy-for-personal-data-handling) ci-dessus.

Le vidage est une opération nécessitant des privilèges élevés, qu’aucune application ni utilisateur dans Azure (y compris même le propriétaire de la ressource) n’a l’autorisation d’exécuter sans qu’un rôle lui soit explicitement accordé dans Azure Resource Manager. Ce rôle est _Videur de données_ et il doit être délégué avec prudence en raison du risque de perte de données.

Une fois que le rôle Azure Resource Manager a été affecté, deux nouveaux chemins d’API sont disponibles, la documentation complète du développeur et la forme d’appel liée :

* [POST purge](https://docs.microsoft.com/rest/api/application-insights/components/purge) - prend un objet spécifiant les paramètres des données à supprimer et retourne un GUID de référence
* GET purge status - l’appel de POST purge retourne un en-tête « x-ms-état-location » qui inclut une URL que vous pouvez appeler pour déterminer l’état de votre API de vidage. Par exemple : 
   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

Alors que la grande majorité des opérations de vidage peut être effectuée beaucoup plus rapidement que ce que prévoit le contrat SLA, en raison de leur impact important sur la plateforme de données utilisée par Application Insights, le contrat SLA formel pour la réalisation des opérations de vidage est défini à 30 jours.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la façon dont les données sont collectées, traitées et sécurisées, consultez [Sécurité des données Application Insights](app-insights-data-retention-privacy.md).